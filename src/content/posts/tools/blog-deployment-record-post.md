---
title: 从零折腾一个自己的博客
published: 2026-07-01
description: 记录一次从想拥有个人博客，到选模板、部署 GitHub Pages、买域名、配置 DNS 和等待 HTTPS 生效的完整过程。
tags:
  - Blog
  - GitHub Pages
  - DNS
  - Cloudflare
draft: false
toc: true
abbrlink: blog-deployment-record-post
---

第一次接触博客是大学入学前的那个暑假吧。我哥给我看的他的博客, 我寻思不就是记录东西的吗. 我也可以开个公众号哈哈哈哈哈.现在打脸了

师兄实习后, 经常刷博客, 也会给我分享一些技术型博客, 哎呀我也想有一个。

不是那种很复杂、很商业化的网站，也不是为了立刻写出什么很厉害的技术文章。更像是想给自己留一个地方。以后读论文、配环境、写项目、踩坑、突然想明白一个东西，都可以放进去。它不一定每天更新，但它应该在那里。

这件事刚开始的时候，我其实有点懵。组里有服务器，所以我的第一反应是：是不是应该把博客部署到服务器上？后来折腾了一圈才发现，如果只是个人博客，而且文章基本都是 Markdown，那一开始完全没必要把事情做重。静态博客加 GitHub Pages 已经够用了。(其实是师兄带着我走的)

现在回头看，这次真正有价值的地方不只是“我有了一个博客”，而是我终于把域名、DNS、GitHub Pages、HTTPS 这些以前听起来混在一起的东西，拆开看了一遍。(PS: 买域名的时候好纠结哈哈哈, 因为乳名叫乐乐, 想有个有纪念意义的. 师兄还调侃我"怎么不用你那个lelestarlight了? 因为中二时期给自己的邮箱名字是lelestarlight")

## 先让博客本体跑起来

我一开始没有先买域名，而是先把博客本身跑起来。

这个顺序现在看是对的。因为域名只是一个入口，它不会让一个不存在的网站突然出现。真正最先要确定的是：我到底要把什么内容发布出去。

博客模板我最后换成了 Retypeset，底层是 Astro。对我来说，最重要的不是框架有多酷，而是它让我可以继续用 Markdown 写文章。平时写一篇博客，不需要手写 HTML，也不需要自己写后端。

日常写文章大概就是这样：

```powershell
cd D:\xxx\ZichangYang.github.io
pnpm new-post my-new-post
pnpm dev
```

本地预览没问题之后，再提交：

```powershell
git add .
git commit -m "Add new post"
git push
```

push 到 GitHub 之后，GitHub Pages 会负责把网站发布出去。

这一点让我觉得比较舒服, 就是以后写博客的时候，我面对的主要还是文章本身，而不是每次都要重新部署一遍网站。

## 为什么没有直接用服务器

一开始我想过用组里的服务器。

服务器当然可以部署博客，而且还能顺便练 Nginx、证书、反向代理这些东西。但想了一下，我这次真正想解决的问题不是“学习服务器运维”，而是“先有一个能长期写东西的地方”。

如果一上来就用服务器，我要额外关心很多事：

- 服务器环境要怎么配
- Nginx 怎么写
- HTTPS 证书怎么申请和续期
- 服务挂了怎么办
- 以后换服务器怎么办

这些都不是不能做，只是它们会把注意力从“写博客”拉走。

所以我最后接受了一个更简单的方案：静态博客放在 GitHub Pages。它免费、稳定，和 GitHub 仓库天然绑定。对现在的我来说这已经ok了。

这次我也有一个很明显的感受：简单不是不专业。很多时候简单是先把主要问题解决掉。等以后真的需要更多能力，再去加服务器也不迟。

## 买域名

博客用 GitHub Pages 跑起来之后，默认地址其实已经能访问：

```text
https://ZichangYang.github.io
```

但是还是想有一个属于自己的域名. 所以! 我去 Spaceship 买了一个域名：

```text
lele.best
```

买域名的时候我主要看两个价格：首年价格和续费价格。

很多域名第一年很便宜，但续费可能贵不少。个人博客不是什么商业项目，我也不想为了一个域名每年有太大负担。所以最后选域名的时候，我没有只看“第一年多少钱”，也看了后面长期续费能不能接受。

买完域名以后，我才意识到一件很好笑但很重要的事：买域名本身不会发生什么。我只是拥有了这个名字，但互联网还不知道这个名字应该指向哪里。那么!!域名和ip如何联系起来呢~

这就轮到 DNS 了。

## DNS

以前我看到 DNS 会觉得它很玄学。

这次配完之后，我先把它理解成一个地址簿。

别人访问：

```text
lele.best
```

浏览器并不知道这个名字背后是哪台服务器，也不知道它应该去哪儿拿网页。DNS 要做的事情就是告诉它：

```text
lele.best 应该去这里找
```

我的博客实际托管在 GitHub Pages，所以 DNS 最终要把 `lele.best` 指到 GitHub Pages。

这里有两个平台：

- Spaceship：我在哪里买的域名，负责域名注册和续费
- Cloudflare：我用它管理 DNS，负责告诉别人这个域名应该指向哪里

师兄说“把域名转到 Cloudflare”，我一开始还以为是要把域名从 Spaceship 搬走。后来才明白，这里更准确地说，是把 DNS 管理交给 Cloudflare。

域名本身还是在 Spaceship 续费，但解析记录以后在 Cloudflare 改。

## 把 nameserver 改到 Cloudflare

我在 Cloudflare 里添加了 `lele.best`，选择的是 Connect a domain。Cloudflare 给了我两个 nameserver：

```text
gabe.ns.cloudflare.com
yolanda.ns.cloudflare.com
```

然后我回到 Spaceship，把原来的 nameserver 改成 Cloudflare 给的这两个。

这一步的意思是：

```text
以后别人问 lele.best 应该去哪儿，不再问 Spaceship 默认 DNS，而是去问 Cloudflare
```

这一步刚改完不会立刻全世界生效。DNS 有传播时间，所以中间会有一段“我明明改了，但怎么还没好”的等待期。(所以中间我刷新了好几次, 我很急但是不知道在急什么嘻嘻嘻)

我当时也反复看页面，生怕自己填错。现在回想起来，这种等待本身就是搭网站的一部分。不是所有东西都像本地代码一样，改完一保存就立刻生效。

## 在 Cloudflare 里填 DNS 记录

Cloudflare 接管 DNS 之后，还要告诉它具体怎么指路。

根域名 `lele.best` 用 A 记录指向 GitHub Pages 的 IP：

```text
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

`www.lele.best` 用 CNAME 指向：

```text
ZichangYang.github.io
```

我后来是这样理解 A 记录和 CNAME 的：

- A 记录：直接指向一个 IP 地址
- CNAME：把一个域名当作另一个域名的别名

所以这里的关系大概是：

```text
lele.best -> GitHub Pages 的 IP
www.lele.best -> ZichangYang.github.io
```

当时还有一个细节：Cloudflare 里的小云朵先保持 DNS only，也就是灰色云朵。因为我想先让 GitHub Pages 正常检测到 DNS，然后让 GitHub 自己签发 HTTPS 证书。等基础流程跑通之后，再考虑要不要开 Cloudflare 的代理。

## 在 GitHub Pages 里绑定自定义域名

DNS 指过去以后，还要去 GitHub 仓库里告诉 GitHub：

```text
lele.best 是这个博客要用的域名
```

位置是：

```text
Settings -> Pages -> Custom domain
```

填入：

```text
lele.best
```

保存之后，GitHub 会做 DNS 检查。

我看到绿色的：

```text
DNS check successful
```

呀呼~ok!!!!!!!!!!!!!!(感谢师兄and我的伙伴codex)

这一步让我又想明白一层：DNS 只是把请求带到 GitHub Pages 门口，但 GitHub Pages 也得知道这个请求应该交给哪个仓库。否则它收到了 `lele.best` 的访问，也不知道该把哪个网站返回出去。

## HTTPS：最后只能等

域名绑好之后，还有 HTTPS 证书。

GitHub Pages 页面里显示过类似这样的状态：

```text
TLS certificate is being provisioned
```

意思是证书还在签发。这个阶段里，HTTP 可能已经能访问了，但 HTTPS 还没完全好，`Enforce HTTPS` 也可能暂时不能勾。

这一步很容易让人怀疑自己是不是哪里配错了。

但其实如果前面已经出现了：

```text
DNS check successful
```

那大概率不是 DNS 错了，而是证书还在处理。等一会儿，刷新页面，等 `Enforce HTTPS` 可以勾选之后，再把它打开。

## 一个差点吓到我的小插曲

中间我查 DNS 的时候，看到过类似这样的 IP：

```text
198.18.0.x
```

我当时第一反应是：坏了，是不是 DNS 配错了。

后来才发现，这很可能是本机代理或者网络环境产生的 fake-ip，并不代表公网 DNS 真的是这个结果。

所以查 DNS 的时候，不能只看自己电脑某一次查询。更靠谱的办法是查公共 DNS，比如 Google DNS 或 Cloudflare DNS。只要公网结果里 `lele.best` 指向 GitHub Pages 的那几个 IP，说明大方向就是对的。

这个小插曲对我挺有用的。它提醒我，排查问题的时候要先判断“我看到的现象来自哪里”。有时候不是配置错了，而是观察的位置不对。

## 我最后真正理解了什么

这次之前，我把“部署一个博客”想成一整块东西。

做完之后，我发现它其实可以拆成几层：

- GitHub 仓库：放博客源码
- Astro / Retypeset：把 Markdown 变成网页
- GitHub Pages：托管静态网页
- 域名：给网站一个好记的名字
- DNS：告诉别人这个名字应该去哪儿
- HTTPS：让访问过程变成安全连接

这些东西以前听起来都挤在一起，像一团线。真正做一遍之后，它们就各自站到了自己的位置上。

我也不是一开始就懂。很多步骤其实是跟着做的，做到某一步卡住了，再回头问“这一步到底在干嘛”。但这种理解反而比较真实。不是先把所有概念背完再开始，而是在动手过程中慢慢把它们串起来。

这大概也是我这次最喜欢的地方。

它不是一次很高级的工程实践，但它把一个原本很模糊的东西变成了一个我能摸到的流程。

## 后来又开始纠结文件放哪

博客弄好之后，我准备把这篇文章塞进项目里，结果又卡住了。不是报错，也不是部署失败，就是一个很日常的问题：

我以后每写一篇，都直接扔到 `src/content/posts` 下面吗？

刚开始我觉得应该是这样。毕竟它就叫 `posts`，文章不放这里还能放哪里。但看了一眼目录，我又有点不舒服。现在只有几篇文章当然没事，后面如果写多了，Markdown 文件、图片文件夹、各种临时名字全堆在一起，迟早会乱。毕竟刚开学的时候师兄给我重装系统, 教会我怎么分类文件. 

而且这种乱不是那种马上会炸的乱，是更烦人的那种：每次想找一篇旧文章，都要在一堆文件名里扒拉半天。想到这里我就觉得，算了，还是趁现在还没几篇先理一下。

后来确认了一下，这个项目不是要求所有文章都平铺在 `posts` 下面。只要还在 `src/content/posts` 里面，下面再分文件夹也可以。

于是我就先分了三个：

```text
notes
research
tools
```

也没有什么很严肃的分类体系。`research` 放论文、科研这些，`tools` 放博客部署、环境配置、工具折腾，`notes` 就放一些还没那么明确的小记录。够用就行，别一上来把分类做得比文章还复杂。

但是刚分完我又想起另一个问题：文件移动了，网址会不会跟着变？

这个问题确实会(我很厉害吧..)。因为这个博客默认会用文章路径当网址的一部分。也就是说，如果我把：

```text
posts/first-paper-experience.md
```

挪到：

```text
posts/research/first-paper-experience.md
```

那网址也可能从：

```text
/posts/first-paper-experience/
```

变成：

```text
/posts/research/first-paper-experience/
```

这就有点尴尬了。我只是想收拾一下房间，不是想给门牌号也换了。尤其博客这种东西，后面万一哪篇文章被自己引用过、发给别人过，地址老变就很烦。

最后的办法是给文章写一个固定的 `abbrlink`：

```yaml
abbrlink: first-paper-experience
```

这样文件爱放哪放哪，网址还是认这个短名字。比如文章已经被我放到：

```text
src/content/posts/research/first-paper-experience.md
```

但访问地址还是：

```text
/posts/first-paper-experience/
```

这下就舒服多了。文件夹是给我自己看的，网址是给外面访问的，两边不要互相拖着跑。

我顺便也把分享图那块改了一下，让它也按 `abbrlink` 来找文章。不然正文地址稳住了，结果分享图地址又因为文件移动变了，也挺怪的。

现在目录大概变成这样：

```text
posts
  notes
    hello.md
  research
    first-paper-experience.md
    first-paper-experience.assets
  tools
    blog-deployment-record-post.md
```

以后有图片的话，就跟着文章放一个同名的 `.assets` 文件夹。文章在哪，图就在哪旁边。这个规则很土，但好记。

这一段折腾其实挺小的，但我还挺在意。因为博客这种东西，如果一开始就让自己写得不顺手，后面很容易懒得写。目录不用多高级，至少别让我每次打开项目都先皱眉。

## 后面怎么写

博客搭好之后，真正的问题反而变成了：我要写什么。

现在我的写作流程很简单：

```powershell
cd D:\Learning\blog\ZichangYang.github.io
pnpm new-post tools/post-name
pnpm dev
```

如果这篇是在折腾工具，就放 `tools`；如果是论文相关，就放 `research`；如果只是随便记点东西，就先丢 `notes`。不用想太神圣，先让自己愿意写。

然后记得在文章开头给它一个固定的 `abbrlink`。这个名字以后就是它的网址，别随便改：

```yaml
abbrlink: post-name
```

写完之后本地看一眼，没问题就：

```powershell
git add .
git commit -m "Add new post"
git push
```

以后文章会发到：

```text
https://lele.best
```

我现在对这个博客的期待也不算很大。它不需要立刻变成一个内容很多的网站，也不需要每篇都写得特别完整。它先是一个记录自己的地方。

有些东西当时不记下来，过几天就会忘。尤其是那些配环境、改配置、查报错、突然想通的瞬间。它们单独看都很小，但积累起来，可能就是自己走过的路。

所以这篇文章也算是第一块砖。(Oh不对, 是第二块, 因为我太想先写一下我的论文了哈哈哈哈哈)

博客的门牌号已经挂上了，后面就慢慢往里面放东西~
