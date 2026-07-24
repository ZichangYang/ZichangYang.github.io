---
title: 记一次崩坏服务器的全部经过[苦涩]
published: 2026-07-24T06:47:20.396Z
description: ''
updated: ''
tags:
  - Tag
draft: false
pin: 0
toc: true
lang: ''
abbrlink: 'vllm'
---

先来说一下...这次完全是因为要跑上次第一篇论文的后续实验......小心翼翼地闯了很多祸..BG: 4090服务器是化工院合作的老师买的, 大家都在用, 我需要用到两个卡, 但是师兄给我说可以设置KVCache, 于是! 我就像小狗看到骨头(没错)一样去跑实验了. (这个时候我根本不知道后面会发生什么事情..)

## 一、结论

日志能够直接证明的故障点是：采用 TP=2 的 vLLM V1 引擎在执行 JSON Schema 结构化生成时，EngineCore 调用 `sample_tokens(grammar_output)`，并通过 `collective_rpc` 等待 TP Worker 返回采样结果，最终发生 `RPC call to sample_tokens timed out`，随后 EngineCore 异常退出。这个 RPC timeout 表示至少有一个 TP Worker 在规定时间内没有响应，但它更像“Worker 已经失联或卡住”的外部表现，不等同于已经找到了最底层原因。

根据时间线(真的很像侦探...一层一层查到底是哪里出问题了😢)，实验端将并发从 `workers=1` 调整为 `workers=2`(workers=1真的很慢啊 好吧也可能是我太急了😒)、JSON Schema 的 grammar sampling、TP=2 多进程通信、32K 长上下文带来的更高执行压力，以及重启后可能残留的不干净状态，都可能共同提高故障概率。目前更合理的故障链条是：重启后的 TP 多进程环境可能存在残留或不稳定状态，两个结构化生成请求又并发进入模型，其中一个 TP Worker 因 CUDA/NCCL 通信、进程间资源、vLLM V1 并发结构化解码问题或驱动异常而失去响应，EngineCore 最终以 RPC timeout 退出。

服务异常退出后，TP 子进程没有被系统正常回收。最终可以观测到 PID `372858` 处于 `Zl` 状态、父进程为 PID 1、命令为 `[VLLM::Worker_TP] <defunct>`。与此同时，两张 GPU 各残留约 30GB 显存；GPU 0 仍被 NVIDIA 驱动关联到 PID `372858`，GPU 1 则在单卡 PyTorch 测试中报 `No CUDA GPUs are available`。普通 `kill` 无法处理僵尸进程，`nvidia-smi --gpu-reset -i 0,1` 又分别返回 GPU 0 正被其他 client 使用、GPU 1 不支持 reset，因此当前只能切换管理员重载 NVIDIA 驱动或重启整台服务器恢复。

所以很可能是重启清理不完整与并发结构化解码共同构成了触发条件。而且不规范的 screen 和重启流程会增加重复启动、进入错误会话、继承错误环境变量以及未确认旧进程是否退出的风险。

## 二、梳理

Qwen3-14B 最初使用两张约 48GB 的 RTX 4090，通过 vLLM TP=2 成功启动。为了避免服务默认占用约 90% 显存，启动时设置了 `--gpu-memory-utilization 0.60`。初始上下文上限为 8192，服务启动后每张卡实际占用约 29GB，两张卡合计约 58～60GB，符合最初的预期。

在 FinQA 的 Never-RAG 测试中，20 条请求全部成功。随后运行 Full-RAG 时，20 条中有 13 条返回 HTTP 400。进一步读取服务器响应正文后确认，某条请求实际包含 9921 个输入 token，而服务最大上下文只有 8192：(这是跑实验第一次卡壳的地方)

```text
This model's maximum context length is 8192 tokens.
However, your request has 9921 input tokens.
```

因此将服务的 `--max-model-len` 从 8192 调整为 32768。调整后 `/v1/models` 返回 `max_model_len: 32768`，说明这一轮 vLLM 至少完成了启动并成功提供了接口。这只能证明 32K 配置可以启动，不能证明重启过程已经把旧 TP Worker、共享内存、信号量和 CUDA 上下文全部清理干净，也不能反过来证明重启与后续故障完全无关。32K 本身不是当前僵尸进程问题的直接证据，但更长上下文可能增加单次请求的执行时间和 KV Cache 压力，从而放大原本存在的多进程或并发问题。

为了加快实验，在确认暂时无人使用服务器后，实验端把 Full-RAG/TriviaQA 请求从 `workers=1` 调整为 `workers=2`。随后 vLLM 服务端出现以下关键错误：(就是这里)

```text
TimeoutError: RPC call to sample_tokens timed out.
vllm.v1.engine.exceptions.EngineDeadError: EngineCore encountered an issue.
```

堆栈中的关键路径是：

```text
EngineCore.run_busy_loop
  -> _process_engine_step
  -> step
  -> model_executor.sample_tokens(grammar_output)
  -> multiproc_executor.collective_rpc
  -> TimeoutError
```

错误发生后，Worker 日志显示 parent process exited，API Server 随后关闭，实验端收到：

```text
urllib.error.URLError: <urlopen error [Errno 111] Connection refused>
```

这说明 8000 端口已经没有 vLLM 服务监听。此后进行的多次启动只能称为“重启尝试”，并没有真正恢复服务。这些尝试又先后暴露出失效代理、`CUDA_VISIBLE_DEVICES` 为空、旧 Worker 未释放显存等次生问题。

最终检查结果如下：

```text
PID      PPID  STAT  CMD
372858   1     Zl    [VLLM::Worker_TP] <defunct>
```

GPU 状态为：

```text
GPU 0：约 30053 MiB / 49140 MiB
GPU 1：约 30051 MiB / 49140 MiB
```

分别限制到单张卡测试时，GPU 0 可以创建一个很小的 CUDA Tensor，GPU 1 则返回：

```text
RuntimeError('No CUDA GPUs are available')
```

执行 GPU reset 后返回：

```text
GPU 00000000:AF:00.0: In use by another client
GPU 00000000:D8:00.0: Not Supported
```

因此故障已经从普通的 vLLM 进程问题演变为需要管理员处理的 GPU 驱动状态问题。

## 三、原因

初始服务崩溃的直接证据是 vLLM TP Worker 在结构化采样阶段发生 RPC timeout，EngineCore 随后死亡。实验端 `workers=2` 与故障发生时间直接相邻，并且当时请求使用 JSON Schema，服务堆栈又明确停在 `sample_tokens(grammar_output)`，因此并发结构化解码是最值得怀疑的触发条件之一。RPC timeout 是故障检测点，而不是完整根因：EngineCore 只能确认某个 Worker 没有按时返回，却不能仅凭这条异常说明 Worker 究竟是卡在 CUDA Kernel、NCCL/TP 通信、grammar sampling、进程间共享资源，还是已经因为别的异常退出。

TP=2 时，一次生成不是由单个 Python 进程独立完成。API Server 把任务交给 EngineCore，EngineCore 再协调两个 TP Worker；JSON Schema 请求还要为每一步生成 `grammar_output`，限制当前允许采样的 token。两个 Worker 必须在采样和通信阶段保持同步。只要其中一个 Worker 因 CUDA/NCCL 卡住、进程间共享内存或信号量异常、vLLM V1 并发结构化解码缺陷、GPU 驱动状态不稳定，或者执行时间超过内部 RPC 等待阈值，另一个 Worker 和 EngineCore 就会一直等待，最终表现为 `sample_tokens` RPC timeout。

重启与本次故障的关系不能被排除。一次接口验证成功，只能证明新的 API Server 和 EngineCore 当时能够接受请求，不能证明上一次服务的全部多进程和驱动资源已经被彻底回收。尤其是本次退出日志出现了 leaked semaphore 和 leaked shared memory，用户此前重启后又发生过相似现象，因此“重启时清理不完整，给后续 Worker 失联留下了不稳定状态”是有现实依据的推断。

`workers=2` 会让两个 Schema 请求同时进入 TP 执行路径，增加 grammar 状态、KV Cache、GPU 执行和进程间同步的压力，因而很可能提高复现概率；但如果底层多进程和驱动状态完全健康，并发 2 理论上不应必然使服务崩溃。后续在当前服务器上仍应先回退到 `workers=1` 和 `--max-num-seqs 1`，这是风险控制措施!!

服务无法再次启动的直接原因是旧 TP Worker/驱动上下文没有正常清理。PID `372858` 已是 PPID=1 的僵尸进程，普通信号无法让一个已经死亡的进程“再退出一次”；同时 NVIDIA 驱动仍报告相关显存占用。这种状态不能通过修改 vLLM 参数解决。

## 四、父子进程与僵尸进程💀

vLLM 并不是只有一个 Python 进程。当前 V1 Engine 在启动后会形成 API Server、EngineCore 和一个或多个 Worker。使用 `--tensor-parallel-size 2` 时，模型会跨两张 GPU 运行，Worker 之间需要执行集体通信和 RPC。任意一个 rank 长时间无响应，都可能影响整个 EngineCore。

正常停止时，应由父进程通知 Worker 退出，等待子进程释放 CUDA 上下文和共享内存，然后父进程再结束。异常停止时，如果父进程先退出、没有正确 `wait` 子进程，或者 Worker/驱动处于异常状态，系统就可能留下 `<defunct>` 条目。

僵尸进程：进程主体已经结束，只剩下退出状态等待父进程回收。它通常不再执行代码，所以对它执行普通 `kill` 或 `kill -9` 没有实际意义。本次 PID `372858` 的 PPID 已经变成 1，说明原父进程不存在，它被系统的 PID 1 接管；但它长时间保持 `Zl`，并且 NVIDIA 驱动仍报告对应的显存使用，说明用户态进程表和 GPU 驱动状态都没有恢复到干净状态。

这也是为什么“再执行一遍 vllm serve”无效：新的 vLLM 需要重新在两张 GPU 上分配权重与 KV Cache，但旧状态已经各占约 30GB，GPU 1 又不能完成 CUDA 初始化，新服务自然无法建立 TP=2 的 World Size。

## 五、screen 相关知识

`screen` 是 Linux 上的==终端会话管理器==。它的核心作用不是加速训练或推理，而是在服务器上创建一个“不会因为本地 SSH 断开就一起死掉”的独立终端。可以把长时间运行的 `vllm serve`、下载任务或实验脚本放进 screen 里；之后即使本地 Cursor、VS Code 远程连接断开，电脑休眠，或者网络抖动导致 SSH 掉线，服务器上的任务仍然继续运行。之后重新登录，再用 `screen -r` 回到原来的会话，就能看到当时的输出和当前状态。

如果不用 screen，直接在普通 SSH 终端里前台启动 vLLM，那么一旦终端关闭或连接中断，前台进程通常会收到挂断信号并退出。对课题一这种需要连续跑 Never-RAG、Full-RAG、prefix margin 的流程来说，服务中途消失会直接导致实验端 `Connection refused`，并且可能留下未清理干净的 TP Worker。因此，在这台共享 4090 服务器上，screen 是跑长任务的基本工具，不是可选项。

screen 还承担==隔离职责==。模型服务、模型下载和实验脚本不应挤在同一个会话里。服务 screen 只负责 vLLM，实验 screen 只负责调用 API 和跑评测；二者的用户、环境变量和日志都分开。尤其是实验端常常设置 `CUDA_VISIBLE_DEVICES=""` 让 BGE-M3 走 CPU，而服务端必须看到 GPU 0 和 1。如果这两套环境混在同一个 shell 中来回切换，就很容易把空的 GPU 可见性带到 vLLM 启动命令里，最终报出 `World size (2) is larger than the number of available GPUs (0)`。

screen 本身不会监督、重启或回收 vLLM 的子进程。它只是保存一个终端会话。服务是否健康，仍然要看端口、进程树、`nvidia-smi` 和实际请求是否成功。本次故障里，screen 不是 `sample_tokens` RPC timeout 的底层技术根因，但多个重名或用途不清的 screen、频繁进出不同会话、以及在未确认旧进程退出时又开新服务，都会放大误操作风险。

## 六、screen 的名字、状态和基本用法

查看当前有哪些会话时使用：

```bash
screen -ls
```

常见输出类似：

```text
91783.vllm
368558.qwen14-vllm
```

这里的格式是“创建 screen 时的进程 ID + 人为设置的会话名称”。前面的数字是该 screen 会话自己的 PID。即使两个人都把名字起成 `vllm`，不同时间创建的会话仍然会有不同数字。所以当名称可能重复时，不要只写 `screen -r vllm`，而应使用完整标识，例如 `screen -r 91783.vllm`。

`Attached` 表示当前已经有终端附着在这个会话上；`Detached` 表示会话在后台运行，暂时没有人查看。这两种状态都不等于服务健康。一个 Detached 的 screen 可能正在正常提供 14B 接口，也可能只是停在 shell 提示符；一个 Attached 的 screen 也可能已经打满了崩溃堆栈。判断 vLLM 是否可用，仍然要看 8000 端口、模型列表和 GPU 占用，而不是只看 screen 状态。

新建一个带明确用途的会话：

```bash
screen -S topic1-qwen14-32k
```

重新进入一个已经存在且处于 Detached 的会话：

```bash
screen -r topic1-qwen14-32k
```

如果提示名称歧义，改用完整 ID：

```bash
screen -r 368558.topic1-qwen14-32k
```

`screen -d -r <完整ID>` 的含义更强：它会先强制把当前附着者踢开，再把会话接到你这里。只有在确认这是自己的会话，或者已经和当前使用者协调后，才能使用。日常进入 Detached 会话，优先用普通的 `screen -r`。

命名时尽量一眼看出用途和配置，避免所有东西都叫 `vllm`。下载、服务和实验可以分别命名为：

```text
qwen14-download
topic1-qwen14-32k
topic1-finqa-smoke
topic1-trivia-pilot
```

一个最小正确流程是：先在 manager 用户下创建 `topic1-qwen14-32k` 并启动 vLLM；启动成功后用 `Ctrl+A` 再按 `D` 离开，让服务继续跑；然后在 yangzichang 用户下另开 `topic1-finqa-smoke` 跑实验。之后任何时候都可以 `screen -ls` 查看，再用 `screen -r` 分别回到服务端或实验端。

## 七、离开、停止和结束 screen 的正确方式

如果只是暂时离开界面、让里面的任务继续运行，在 screen 中先按 `Ctrl+A`，松开后再按 `D`。这叫 detach。它不会向 vLLM 发送终止信号，服务会继续占用 GPU 并监听端口。这是日常离开服务会话的标准动作。

如果要正常停止 vLLM，必须先进入正确的那个服务 screen，确认眼前就是当前这次 `vllm serve` 的输出，然后按一次 `Ctrl+C`。接着等待它打印 shutdown complete 并回到 shell 提示符。此时不要假设“看起来停了就一定干净”，应立刻在另一个终端检查 8000 端口是否已释放、是否还有 `EngineCore` 或 `Worker_TP` 进程、两张卡显存是否降下来。确认干净后，再在该 screen 内输入 `exit` 结束会话。

`Ctrl+A D` 只是离开，服务继续；`Ctrl+C` 是向前台程序发送中断，用来停止 vLLM；`exit` 是退出当前 shell，只有 vLLM 已经停掉后才应该用它结束服务 screen。很多问题就是本想离开却按成了 `Ctrl+C`，或者服务还没停干净就急着 `exit`，再在另一个 screen 里重新 `vllm serve`，最终叠出旧 Worker 和新服务争用 GPU 的状态。

如果 vLLM 已经自己崩溃并回到了 shell 提示符，`exit` 可以结束这个空的 screen，但它并不能保证异常 Worker 一定被系统回收。崩溃后仍然必须用 `ps` 和 `nvidia-smi` 验证。只要还存在 `Zl` 状态的 `[VLLM::Worker_TP] <defunct>`，或者两张卡仍各占约 30GB，就不能再启动新的 TP=2 服务，而应进入僵尸进程和管理员处理流程。

## 八、vLLM 服务与实验隔离方式

模型服务和实验客户端必须放在不同的用户、screen 和 shell 环境中。推荐约定如下：

```text
manager / topic1-qwen14-32k：只运行 vLLM，看到 GPU 0、1
yangzichang / topic1-trivia-pilot：只运行实验，BGE-M3 强制走 CPU
```

在 vLLM shell 中明确设置：

```bash
export CUDA_DEVICE_ORDER=PCI_BUS_ID
export CUDA_VISIBLE_DEVICES=0,1
```

在实验 shell 中，为了不让 BGE-M3 额外占 GPU，可以设置：

```bash
export CUDA_VISIBLE_DEVICES=""
export OMP_NUM_THREADS=4
export MKL_NUM_THREADS=4
```

这两个设置绝对不能在同一个 shell 中来回混用。尤其不能在设置了 `CUDA_VISIBLE_DEVICES=""` 的实验 shell 中直接切换用户后启动 vLLM；环境变量可能继续存在，导致 vLLM 报：

```text
World size (2) is larger than the number of available GPUs (0)
```

每次启动 vLLM 前都必须先检查：

```bash
whoami
pwd
echo "CUDA_VISIBLE_DEVICES=${CUDA_VISIBLE_DEVICES-unset}"
/home/manager/vllm/.venv/bin/python - <<'PY'
import torch
print("CUDA available =", torch.cuda.is_available())
print("GPU count =", torch.cuda.device_count())
for i in range(torch.cuda.device_count()):
    print(i, torch.cuda.get_device_name(i))
PY
```

只有确认当前用户为 `manager`、目录正确、PyTorch 能正常初始化两张卡后，才允许执行 `vllm serve`。

## 九、后续启动配置

在新的唯一 screen 中启动：

```bash
screen -S topic1-qwen14-32k

cd /home/manager/vllm
source .venv/bin/activate

unset http_proxy https_proxy all_proxy
unset HTTP_PROXY HTTPS_PROXY ALL_PROXY
unset HF_ENDPOINT

export HF_HUB_OFFLINE=1
export TRANSFORMERS_OFFLINE=1
export CUDA_DEVICE_ORDER=PCI_BUS_ID
export CUDA_VISIBLE_DEVICES=0,1

/home/manager/vllm/.venv/bin/vllm serve Qwen/Qwen3-14B \
  --tensor-parallel-size 2 \
  --gpu-memory-utilization 0.60 \
  --max-model-len 32768 \
  --max-num-seqs 1 \
  --max-logprobs 20 \
  --host 0.0.0.0 \
  --port 8000 \
  --api-key '<VLLM_API_KEY>'
```

使用离线模式是因为模型已经完整存在于 `/home/manager/.cache/huggingface`。这样可以避免启动时对 `hf-mirror.com` 发送 HEAD 请求，也能避开已失效的 `127.0.0.1:7897` 代理。

`--gpu-memory-utilization 0.60` 控制当前 vLLM 实例的显存预算。`--max-model-len 32768` 是因为 FinQA Top-10 RAG 已经实测出现 9921 token 输入，8192 不足。`--max-num-seqs 1` 与实验端 `workers=1` 配合，避免当前 vLLM V1 + TP=2 + JSON Schema 组合再次并发进入结构化采样。

## 十、服务启动前后的完整检查

启动前先确认没有别人的任务，没有旧 vLLM 进程，也没有端口占用：

```bash
screen -ls

nvidia-smi --query-compute-apps=pid,gpu_uuid,process_name,used_memory \
  --format=csv,noheader

ss -ltnp | grep ':8000' || echo "8000 port is free"

ps -u manager -o pid,ppid,stat,etime,cmd \
  | grep -E 'vllm|EngineCore|Worker_TP|api_server' \
  | grep -v grep
```

服务启动后，在另一个终端验证模型名称和上下文：

```bash
curl -s \
  -H "Authorization: Bearer <VLLM_API_KEY>" \
  http://127.0.0.1:8000/v1/models \
  | python -m json.tool \
  | grep -E '"id"|"max_model_len"'
```

预期结果包含：

```text
"id": "Qwen/Qwen3-14B"
"max_model_len": 32768
```

然后检查两张 GPU 占用是否对称，并且仍在 60% 预算附近：

```bash
nvidia-smi --query-gpu=index,memory.used,memory.total,utilization.gpu \
  --format=csv,noheader
```

最后分别做普通生成、JSON Schema 生成和 logprobs 测试。只有这三项都通过，才允许恢复实验。测试顺序必须从单请求开始，不直接恢复并发。

## 十一、实验端的推荐运行原则

当前所有调用 Qwen3-14B 的实验统一使用：

```text
--workers 1
```

Never-RAG、Full-RAG 和 prefix margin 应分别输出到独立日志。失败结果不要直接删除，而是重命名保存，例如：

```text
finqa_rag_failed_ctx8192.csv
trivia_qa_never_rag_failed_rpc_workers2.log
```

这样可以在论文实验记录中说明为何调整上下文和并发参数，也能避免把失败样本误纳入正式统计。

## 十二、本次其他错误及其解决方法

### 1. Hugging Face 代理失效

错误表现为：

```text
Unable to connect to proxy 127.0.0.1:7897
Connection refused
```

原因是 shell 继承了本地代理变量，但服务器上对应代理进程没有运行。下载阶段可以只对当前命令清除代理；模型已经下载后，启动阶段应直接使用离线模式。

```bash
unset http_proxy https_proxy all_proxy
unset HTTP_PROXY HTTPS_PROXY ALL_PROXY
export HF_HUB_OFFLINE=1
export TRANSFORMERS_OFFLINE=1
```

### 2. BGE-M3 下载被 `.DS_Store` 阻塞

整仓下载 BGE-M3 时，镜像对 `.DS_Store` 返回 403，导致 snapshot 下载失败。解决方法是保留已经下载的 `pytorch_model.bin`、`colbert_linear.pt` 和 `sparse_linear.pt`，再单独下载 tokenizer 与配置文件，不删除 2.2GB 权重。

### 3. BGE-M3 tokenizer 文件缺失

最初加载 BGE-M3 报 `Can't load tokenizer`。补充 `sentencepiece.bpe.model`、`tokenizer.json`、`tokenizer_config.json`、`special_tokens_map.json` 等文件后，错误转变为版本兼容问题，说明文件缺失已经解决。

### 4. FlagEmbedding 与 Transformers 不兼容

环境最初是：

```text
FlagEmbedding 1.4.0
transformers 4.46.3
```

`FlagEmbedding 1.4.0` 向旧版 Transformers 的 `XLMRobertaModel` 传递 `dtype`，引发：

```text
TypeError: XLMRobertaModel.__init__() got an unexpected keyword argument 'dtype'
```

将 FlagEmbedding 降级到 1.3.5 后解决。由于 `uv run` 可能根据 `uv.lock` 自动恢复 1.4.0，当前命令使用 `uv run --no-sync`：

```bash
uv pip install --reinstall --no-deps \
  --index-url https://pypi.tuna.tsinghua.edu.cn/simple \
  "FlagEmbedding==1.3.5"

uv run --no-sync python ...
```

最终 BGE-M3 CPU 测试返回：

```text
vector shape = (1, 1024)
BGE-M3 load test passed
```

后续应把 `pyproject.toml` 和 `uv.lock` 正式固定到兼容版本，而不是长期依赖 `--no-sync`。

### 5. Never-RAG 出现 `{}`、`}` 和截断答案

旧协议使用 `json_object`，它只要求输出合法 JSON，并不要求必须包含 `answer`，因此模型可以返回 `{}`。旧流程还用第二次 14B 调用做 postprocess，且只给 64 tokens，Qwen thinking 容易把输出截断。

解决方案是改为 JSON Schema，强制 `answer` 字段；把正式 `max_tokens` 调整为 512；不再传 `--postprocess_model`，只调用一次模型，再由本地 `extract_final_answer` 解析。

```json
{
  "type": "object",
  "properties": {
    "answer": {"type": "string"}
  },
  "required": ["answer"],
  "additionalProperties": false
}
```

### 6. FinQA Full-RAG HTTP 400

HTTP 400 不是 BGE 检索失败，而是 Top-10 片段组成的提示词超过 8192 token。将服务上下文调整为 32768 后解决。失败的 8K 结果已经单独保留，不能与正式 RAG 结果混合。

### 7. Connection refused

实验端出现 `127.0.0.1:8000 Connection refused` 时，应先判断端口是否监听

```bash
curl -sS -H "Authorization: Bearer <VLLM_API_KEY>" \
  http://127.0.0.1:8000/v1/models

ss -ltnp | grep ':8000'
```

本次 Connection refused 是因为 EngineCore 已经退出，属于服务端故障的结果。

## 十三、僵尸进程的处理边界

如果进程状态不是 `Z`，并且已经确认它就是本次 vLLM 的具体 PID，可以先正常终止，再在必要时对这个明确 PID 使用更强信号。禁止使用宽泛的 `pkill -f python`、`killall python` 或按字符串清理所有 vLLM，因为服务器上可能存在其他用户的任务。

如果进程已经是 `Z` 或 `Zl`，它本身已经死亡，`kill -9` 没有意义。应检查 PPID；若父进程仍存在，应由父进程回收；若 PPID 已变为 1 且长时间不消失，同时 GPU 显存也没有释放，就进入管理员处理范围。

管理员在确认无人使用 GPU 后可以尝试：

```bash
sudo nvidia-smi --gpu-reset -i 0,1
```

本次 GPU 0 因 stale client 无法 reset，GPU 1 报 Not Supported，所以最终恢复方法是重载 NVIDIA 驱动或重启服务器。不能为了实验进度擅自执行 `sudo reboot`，必须先和师兄、管理员以及其他用户协调。

## 十四、服务器恢复后的验收清单

服务器恢复后，先确认两张卡基础显存已经从约 30GB 降到很低水平：

```bash
nvidia-smi --query-gpu=index,memory.used,memory.total \
  --format=csv,noheader
```

再分别验证两张物理卡都能实际创建 CUDA Tensor：

```bash
for GPU in 0 1; do
  CUDA_VISIBLE_DEVICES=$GPU /home/manager/vllm/.venv/bin/python - <<'PY'
import torch
x = torch.zeros(1, device="cuda:0")
print(torch.cuda.get_device_name(0), x)
PY
done
```

只有两张卡均通过，才启动 TP=2 服务。服务启动后按“普通请求一条、Schema 请求一条、logprobs 请求一条、20 条 workers=1 smoke”的顺序恢复。任何阶段出现 EngineCore、Worker_TP、RPC timeout 或显存不释放，都应立即停止扩大实验规模。

## 十五、怎样尽量不再重启

vLLM TP=2 是多进程系统，重启本身就有清理成本；在当前已经出现过 leaked semaphore、leaked shared memory 和僵尸 Worker 的前提下，任何不必要的重启都在增加下一次残留风险。

因此，服务参数一旦按 32K + `--max-num-seqs 1` 启动并通过验收，就固定使用这一组参数完成今天的全部 smoke。不要中途改 `max-model-len`，不要中途改 `gpu-memory-utilization`，不要为了“快一点”临时提高 `--max-num-seqs`。实验侧所有调参，优先改脚本参数和输出目录，而不是改服务。

如果实验请求失败，先判断失败位置。若是 HTTP 400，先读响应正文，确认是不是上下文、参数或 Schema 问题；若是超时，先看服务 screen 是否仍在正常生成；若是 Connection refused，再去检查 8000 端口和 vLLM 日志。只有确认 EngineCore 已死、端口已无监听时，才停止实验并处理服务。处理服务时也不是立刻重启，而是先记录日志、检查进程树和显存，再决定是正常 `Ctrl+C` 收尾，还是需要管理员介入。

`workers=2` 的验证不要放进正式实验。如果后面确实要区分“重启残留”和“并发结构化解码”问题，应单独安排一个很短的诊断窗口：服务已经稳定运行一段时间后，用两条极短 Schema 请求做并发探测，同时盯着 vLLM 日志和 `nvidia-smi`。即便要做，也只做诊断，不做生产流量。在课题一的正式数据产出阶段，默认始终保持 `workers=1` 和 `--max-num-seqs 1`。
