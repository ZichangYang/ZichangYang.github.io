---
title: 第一次去驻场，Windows 电脑怎么进内网？从接线到登录的实操指南
published: 2026-11-24
description: 第一次驻场的 Windows 内网接入实操指南，涵盖网卡、路由、DNS、代理、证书和登录跳转，逐步验证内外网访问与远程联调。
tags:
  - 驻场
  - Windows
  - 网络配置
draft: false
toc: true
abbrlink: on-site-presence
---

# 第一次去驻场，Windows 电脑怎么进内网？从接线到登录的实操指南

到了现场，先要解决一个很具体的问题：笔记本要能打开内网系统，有时还要同时访问外网查文档。接上网线或连上 VPN 之后，可能出现网页打不开、首页能开但登录失败，或者内网一通外网就断。这些现象分别涉及网卡、路由、DNS、代理、证书和登录跳转。按下面的顺序配置，每一步都能当场验证。

本文以 Windows 11 为例。示例域名与地址用于说明填法，执行前换成现场 IT 提供的值。双网并用、远程接入和代理例外均按现场网络规定办理。

## 1. 到现场先问清楚这几项

| 你要做的事 | 向现场 IT 确认什么 | 通常在哪里配置 |
| --- | --- | --- |
| 让电脑接入内网 | 网线、办公 Wi-Fi 或 VPN？是否需要设备登记、客户端或账号？ | 网口、Windows 网络设置或指定 VPN 客户端 |
| 拿到可用的 IP | 自动分配还是固定地址？手工配置时需要 IP、前缀长度或掩码、网关和 DNS | 对应网卡的 IP 设置 |
| 打开业务页面 | 完整 URL、端口、登录跳转域名，是否需要组织证书 | 浏览器、DNS 和证书信任设置 |
| 同时使用内外网 | 是否允许双网？哪些网段走内网？普通外网走哪条链路？ | 路由表、VPN 策略和代理规则 |
| 使用组织代理 | 用自动配置脚本还是手动代理？哪些域名应直连？ | Windows「代理」设置或组织管理的代理客户端 |

至少拿到一个预期能访问的业务 URL 和一个获准访问的外网 URL。后面每次改完配置，用这两个地址复测。改网卡、路由或代理前，先记下当前值和对应的网卡名称，离场时可以按记录恢复。

## 2. 先把内网接入配通

### 网线、Wi-Fi 或 VPN，按现场给的方式选

- **网线。** 插到指定办公网口。打开「设置 → 网络和 Internet → 以太网」，确认已连接。网口亮只表示链路建立，接着还要确认终端拿到了地址。
- **办公 Wi-Fi。** 打开「设置 → 网络和 Internet → Wi-Fi」，连指定网络，按 IT 要求完成设备或账号认证。
- **VPN。** 使用现场提供的客户端和配置。若使用 Windows 自带客户端，入口是「设置 → 网络和 Internet → VPN」。填写管理员提供的服务器和认证信息，连接后再测业务端口。后续分流由管理员确认。

如果现场使用 DHCP，在对应网络的「IP 分配 → 编辑」选「自动 (DHCP)」。只有 IT 明确分配了固定 IP，才选择「手动」并开启相应地址族，按分配值填 IP、前缀长度或掩码、网关和 DNS。Windows 界面也可能把 DNS 单独列为「DNS 服务器分配」。IPv4 的 `/24` 对应 `255.255.255.0`，填写哪种看输入框提示。参考 [微软的 Windows 网络设置说明](https://support.microsoft.com/zh-CN/Windows/Experience/Connectivity-Networking/essential-network-settings-and-tasks-in-windows)。

打开 PowerShell，先运行三条只读命令：

```powershell
Get-NetAdapter | Format-Table ifIndex, Name, Status, LinkSpeed
Get-NetIPConfiguration
Get-DnsClientServerAddress
```

对照现场参数看哪块网卡 `Up`、拿到了什么地址、有哪些 DNS 服务器，以及默认网关从哪块网卡进来。`ifIndex` 是接口编号，后面验证路由会用到。使用 DHCP 时，如果现场要求有正常 IPv4、却只拿到 `169.254.x.x`，先查网口、准入和 DHCP。

## 3. 配内外网同时使用，分别处理路由、DNS 和代理

下面是一种常见且经现场允许的安排。内网走有线或 VPN，普通外网走 Wi-Fi。企业也可能要求所有流量经 VPN 或指定代理，按现场给的方案选择。

```text
                       输入业务网址
                             │
                   浏览器选直连还是代理
                             │
          ┌──────────────────┴──────────────────┐
          │                                     │
         直连                             交给指定代理
          │                                     │
    电脑解析目标地址                     电脑先连代理地址
          │                                     │
    路由表选择出网接口                   代理转发到目标服务
          │
    ┌─────┴───────────┐
    │                 │
  内网目标           普通外网目标
  有线 / VPN         Wi-Fi 默认路由
```

### 3.1 路由决定数据从哪块网卡出去

在 PowerShell 查看默认路由和到目标地址会选哪条路：

```powershell
Get-NetRoute -DestinationPrefix '0.0.0.0/0' |
    Format-Table InterfaceAlias, InterfaceIndex, NextHop, RouteMetric

Find-NetRoute -RemoteIPAddress '10.42.18.25'
route print -4
```

如果内网目标和电脑的有线地址在同一子网，通常已有直连路由。如果目标在另一个内网网段，则需要经一个可达的内网下一跳。双网并用时，可以由 IT 下发目标网段路由，让这些流量走内网，同时让普通外网沿用默认出口。更具体的目标前缀优先匹配。多条路由前缀相同时，再看路由和接口的度量。参考 [Windows route 命令](https://learn.microsoft.com/zh-cn/windows-server/administration/windows-commands/route_ws2008)和 [Find-NetRoute](https://learn.microsoft.com/powershell/module/nettcpip/find-netroute)。

**只有 IT 确认需要手工补目标路由时**，在管理员 PowerShell 执行。假设有线网卡编号是 `12`，内网网关是 `10.42.10.1`，获准访问的业务网段是 `10.42.18.0/24`：

```powershell
# 添加临时路由，以下均为示例值。
route.exe add 10.42.18.0 mask 255.255.255.0 10.42.10.1 if 12
Find-NetRoute -RemoteIPAddress '10.42.18.25'
Test-NetConnection '10.42.18.25' -Port 443 -InformationLevel Detailed
```

如果已有同目标路由，先看它的来源和下一跳，再决定是否需要改。验证通过且现场确实需要重启后保留时，由 IT 下发，或按批准方式用 `route.exe -p add` 持久化。临时测试结束后，只删除本次新增的那条，再重新查看路由：

```powershell
route.exe delete 10.42.18.0 mask 255.255.255.0 10.42.10.1 if 12
route print -4
```

### 3.2 DNS 决定域名解析成哪个地址

普通网站能打开，内网域名却报找不到，先查内网 DNS。在 PowerShell 对比本机默认查询和指定内网 DNS 查询：

```powershell
$site = 'portal.example.com'
$internalDns = '10.42.10.53'
Resolve-DnsName -Name $site -Type A -DnsOnly
Resolve-DnsName -Name $site -Type AAAA -DnsOnly
Resolve-DnsName -Name $site -Server $internalDns -Type A -DnsOnly
```

如果只有指定内网 DNS 能返回预期地址，检查对应网卡的 DNS 设置，或请 IT 为 VPN 配置按域名后缀分流解析。内网 DNS 自身也需要可达。如果 `A` 和 `AAAA` 都返回记录，但访问表现不同，结合下一节的 TCP 探测确认浏览器报错时实际使用的地址族。参考 [Resolve-DnsName 的参数说明](https://learn.microsoft.com/en-us/powershell/module/dnsclient/Resolve-DnsName?view=windowsserver2022-ps)。

需要临时验证某个已知 IPv4 地址是否能正确提供 HTTPS 时，用 `curl.exe --resolve`，只影响这一次请求，同时保留原域名和证书校验。确认现场允许直连后执行：

```powershell
curl.exe --noproxy '*' --resolve 'portal.example.com:443:10.42.18.25' `
    --connect-timeout 5 --max-time 15 -sS -D - -o NUL `
    'https://portal.example.com/'
```

参考 [curl 命令文档](https://curl.se/docs/manpage.html)中的 `--resolve` 与 `--noproxy`。

### 3.3 代理决定请求由谁发出去

Windows 的入口是「设置 → 网络和 Internet → 代理」。根据现场规则填写自动配置脚本或手动代理。若明确要求内网站点直连，且使用手动代理，在「手动代理设置 → 使用代理服务器 → 编辑」的例外列表加入业务和登录域名。多项用分号隔开，例如 `portal.example.com;login.example.com`。如果代理由 PAC、组织策略或代理客户端控制，在实际管理规则的地方修改。修改后按现场要求重新打开浏览器和代理客户端复测。参考 [微软的 Windows 代理说明](https://support.microsoft.com/zh-cn/windows/experience/connectivity-networking/use-a-proxy-server-in-windows)。

如果现场允许对同一个 URL 做直连和代理对照，并且 IT 已提供手动 HTTP 代理地址，可以在 PowerShell 分别测一次：

```powershell
$url = 'https://portal.example.com/'
$proxy = 'http://proxy.example.com:8080'
curl.exe --noproxy '*' --connect-timeout 5 --max-time 15 -sS -D - -o NUL $url
curl.exe --proxy $proxy --noproxy 'never-match.invalid' --connect-timeout 5 --max-time 15 -sS -D - -o NUL $url
```

第二条显式指定代理，并用一个与目标不匹配的例外覆盖终端里可能已有的代理例外。使用 PAC 或组织登录代理时，直接在使用该策略的浏览器中复测。参考 [curl 的代理选项](https://curl.se/docs/manpage.html)。

如果走代理打不开而直连能打开，优先核对该域名是否命中正确的代理规则。反过来，现场明确要求走代理的站点，应核对代理入口是否在线、代理是否接受该目标。`curl.exe` 的命令行代理设置与浏览器的组织策略可能不同，因此最后要用实际浏览器验证。

## 4. 网页打不开，用这个顺序定位

```text
输入 URL
   │
   ├─ 域名解析失败 ──── 看内网 DNS、VPN 分流、解析后缀
   │
   ├─ 域名能解，TCP 失败 ─ 看目标端口、出口网卡、路由和访问权限
   │
   ├─ TCP 通，HTTPS 报错 ─ 看原域名、证书有效期和证书链
   │
   ├─ 首页能开，登录失败 ─ 看跳转域名、认证回跳和会话
   │
   └─ 登录成功 ────── 实际打开一个已授权功能
```

按以下命令检查业务站点，登录跳转如果涉及另一个域名，对那个域名也做一遍。示例为允许直连的 HTTPS 入口，端口以现场提供的为准。

```powershell
$site = 'portal.example.com'
$port = 443
Test-NetConnection -ComputerName $site -Port $port -InformationLevel Detailed

# 查看头部和状态，保留 TLS 校验，先观察第一跳响应。
curl.exe --noproxy '*' --connect-timeout 5 --max-time 15 `
    -sS -D - -o NUL "https://${site}:$port/"
```

| 你看到的结果 | 接下来查哪里 |
| --- | --- |
| `TcpTestSucceeded : False` | 看 `RemoteAddress`、`InterfaceAlias`、`SourceAddress`，核对解析结果、路由和真实端口。经代理访问时，还要单独测代理入口 |
| TCP 通，提示证书不可信 | 核对 URL 是否使用原域名、电脑时间是否正确，记录证书签发方与错误。请 IT 确认服务器是否缺中间证书，或通过组织渠道下发可信 CA |
| HTTP `302`，打开登录页失败 | 看响应的 `Location` 或浏览器地址栏，对跳转后的域名重新查 DNS、TCP、代理和证书 |
| 登录后反复跳回登录页 | 在浏览器开发者工具「网络」中开启「保留日志」，重试一次，核对回跳请求、状态码、Cookie 阻止原因和权限。交接时遮盖会话与令牌 |
| HTTP `401` 或 `403` | 先确认响应来自预期服务，再查凭据、会话、账号权限或访问策略。测试 API 时可以结合错误正文判断请求走到了哪里 |
| 内网可用，外网打不开或变慢 | 用同一个外网地址对比实际出口、代理节点和 DNS。记录相同时间窗口下的连接、首字节和总耗时，换一个授权目标复测 |

证书信任以 IT 提供的方式为准。例如 Windows 管理设备可以由组策略下发，IT 指定手工安装时再打开 `certlm.msc` 查看「本地计算机」证书存储，按对方确认的证书类型和存储位置操作。参考 [微软的证书下发说明](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/distribute-certificates-group-policy)。

如果用 `curl` 比较外网慢在哪一段，重复几次同一个目标，并记录当时的直连或代理条件。下面各时间值是从请求开始累计计算，单位为秒。

```powershell
curl.exe --connect-timeout 5 --max-time 20 -sS -o NUL `
    -w 'dns=%{time_namelookup} connect=%{time_connect} first_byte=%{time_starttransfer} total=%{time_total}\n' `
    'https://www.example.com/'
```

## 5. 离开现场后还要访问内网：先试 UU，再试 Tailscale

现场电脑插着办公网线就能进入内网。人离开现场后，外部电脑却没有去内网服务器的路由。起初只需要从外部电脑联调几个已知的 TCP 服务，手边又有一台获准使用、能够访问内网的接入机，于是先尝试用 **网易 UU 端口映射**。后来场景变成在自己的浏览器里用原网址打开多个站点、完成单点登录，还要让第二台终端方便接入，于是接着尝试 **Tailscale 子网路由和分流 DNS**。

```text
第一种：按端口转发
外部电脑的本机端口 ── UU ──> 内网接入机 ──> 某一台服务器的某个 TCP 端口

第二种：按网段路由
外部电脑用原网址 ── Tailscale ──> 内网接入机 ──> 获准的业务网段
                     └─ 分流 DNS ─────────────────> 内网 DNS
```

这里的接入机须能访问目标服务并保持在线。企业已有批准的 VPN 或远程接入服务时，按 IT 提供的方式办理。下面的地址、端口、域名和网段都是示例。

### 5.1 先用 UU 验证几个固定端口

**为什么先试它。** 初始问题是「外部电脑能否请求某个内网 API」。只要接入机能连目标，就可以先配一条映射，快速区分通道连通性与接口鉴权。下面的菜单和字段来自一次 Windows 客户端实操，入口名称以所用客户端为准。

1. **在内网接入机上确认目标。** 用 PowerShell 执行 `Test-NetConnection 10.42.18.25 -Port 443`，将地址和端口换成 IT 提供的值。先确保由这台接入机访问目标时 `TcpTestSucceeded` 为 `True`。
2. **在外部电脑上配置 UU。** 从设备列表选中实际能到内网的接入机，打开该设备的「端口映射」。新增并启用规则。名称自定，本地端口找一个未占用的值，目标地址填接入机能访问的主机地址，目标端口填真实服务端口。目标地址只填主机，浏览器路径仍留在 URL 里。
3. **按真实依赖登记。** 先测一个入口。后续若验证网页，再核对登录跳转、API 和独立静态资源服务是否也有对应通道。两个请求访问同一主机的同一端口时，通常可以共用同一条映射。

| 示例用途 | 外部电脑的本地端口 | 由接入机连接的目标 |
| --- | --- | --- |
| HTTPS 业务网页 | `18443` | `10.42.18.25:443` |
| HTTPS 单点登录 | `19443` | `10.42.19.26:443` |
| 独立 HTTPS API | `18080` | `10.42.18.30:8443` |

**在外部电脑 PowerShell 验证。** 第一条只看本机映射入口。第二条请求业务站点的原 URL，将底层连接改到本机映射端口，原域名、Host、TLS 证书验证都保留。这个探测只测当前 URL 的第一跳。参考 [curl 的 `--connect-to` 参数说明](https://curl.se/docs/manpage.html)。

```powershell
Test-NetConnection 127.0.0.1 -Port 18443
curl.exe --noproxy '*' --connect-to 'portal.example.com:443:127.0.0.1:18443' `
    --connect-timeout 5 --max-time 15 -sS -D - -o NUL 'https://portal.example.com/'
```

看返回头和响应内容是否属于预期服务。比如收到预期的鉴权错误，可以继续排查账号和应用权限。直接把原网址换成 `localhost` 访问时，HTTP Host 可能命中错误站点，HTTPS 也可能出现域名与证书不匹配。如果需要完整浏览器体验，可由 IT 配置一个仅监听本机、仅接受已批准目标的专用代理，按原域名和端口转到对应映射。然后在专用浏览器配置里指定这个代理，让 HTTP 保留 Host，并让 HTTPS 使用端到端 TLS。浏览器报代理连接失败时，分别检查 UU 映射端口和本地代理自己的监听端口。

### 5.2 浏览器要走完整登录链路，后来试 Tailscale

**为什么再试它。** 单个端口探测成功后，浏览器一点击登录便可能跳往另一台认证服务器。一次实测中，保留原 Host 才拿到预期的 API 鉴权响应，点击登录还要覆盖独立的认证入口。继续沿用映射，需要逐个补目标，还要维护原域名到本地端口的对应关系。当有多个站点和多台终端要按原地址工作时，可以由管理员统一提供获准网段的路由与内网域名解析，再让每台终端用自己的身份接入。这正是子网路由和分流 DNS 分别要解决的问题。

| 在哪里 | 怎么配 | 为什么 |
| --- | --- | --- |
| 内网接入机 | 安装 Tailscale，加入组织批准的同一个 tailnet。从接入机验证业务、认证和内网 DNS 均可达。启用 IP 转发后，通告批准的目标网段或主机路由 | 接入机替远程终端转发流量，目标业务主机可以沿用原地址 |
| 管理台 → **Machines** | 找到通告子网的接入机，批准需要的子网路由 | 路由批准后，客户端才可获得对应路由 |
| 管理台 → **Access controls** | 让获准身份访问所需的业务、认证和 DNS 目标及端口。DNS 通常需要 `53/UDP`，也可能需要 `53/TCP` | 路由批准和访问权限分别配置 |
| 管理台 → **DNS** | 添加内网 DNS 为受限名称服务器，例如只解析 `corp.example.com`。确认客户端有去该 DNS 地址的路由与权限 | 内网域名交给内网 DNS，普通域名保留原来的解析安排 |
| 外部 Windows 终端 → 通知区域 Tailscale 图标 → **Preferences** | 用本人身份登录同一 tailnet，核对 **Use Tailscale subnets** 和 **Use Tailscale DNS settings**，若由组织策略锁定则让管理员核对。仅访问内网网段时，**Exit Node** 保持 **None** | 客户端采用受管的子网路由与 DNS，普通外网保留原有出口 |

参考 [Tailscale 的 Windows 子网路由说明](https://tailscale.com/docs/features/subnet-routers?tab=windows)、[客户端偏好设置](https://tailscale.com/docs/features/client/manage-preferences?tab=windows)和[分流 DNS 设置](https://tailscale.com/docs/reference/dns-in-tailscale)。

如果内网接入机也是 Windows，管理员可以先按官方 Windows 指引启用 IP 转发，再从接入机的管理员 PowerShell 通告已批准范围。下例只通告业务、认证和 DNS 三台主机。`--advertise-routes` 设置的是完整列表，已有接入机需先核对当前通告，保留仍在使用的条目。

```powershell
tailscale set --advertise-routes=10.42.18.25/32,10.42.19.26/32,10.42.10.53/32
```

**在外部终端验收。** 关闭用来测 UU 的专用浏览器窗口，按组织代理规则在普通浏览器使用原网址。依次看接入机状态、两个域名的解析和 TCP、证书、登录回跳。终端上允许直连这些站点时，确认业务和认证域名都匹配相应的代理例外。

```powershell
tailscale status
Resolve-DnsName 'portal.corp.example.com' -Type A -DnsOnly -NoHostsFile
Resolve-DnsName 'login.corp.example.com' -Type A -DnsOnly -NoHostsFile
Test-NetConnection 'portal.corp.example.com' -Port 443 -InformationLevel Detailed
Test-NetConnection 'login.corp.example.com' -Port 443 -InformationLevel Detailed
```

如果解析或端口失败，依次检查 DNS 服务器本身是否可达、路由是否获批、访问规则是否允许、终端设置是否生效。如果两边 TCP 都通但证书报错，回到第 4 节核对证书信任。有一台新终端已解析到预期地址，TCP 也通，实际访问却在证书链校验处失败。最后用两台已获准的终端各自登录，并同时打开一项实际授权功能。把「端口打通」「登录回跳成功」「两台终端可同时使用」记成三个独立的验收结果。

## 6. 网络通了，跑一遍现场工作需要的完整路径

| 现场要做什么 | 还需要配什么 | 怎么验收 |
| --- | --- | --- |
| 使用网页系统 | 业务账号、角色和登录入口 | 从首页完成登录与回跳，打开一个实际会用到的授权页面 |
| 联调 API | 环境地址、端口、认证方式、授权测试凭据和样例 | 从实际发请求的电脑或服务端测连通性，再用测试凭据跑一份小请求，核对响应和预期数据 |
| 拉代码或交付改动 | 仓库地址、账号或 SSH 配置、分支及合并流程 | `git status` 看本地改动，`git fetch` 获取远端信息。提交前用 `git diff --cached` 检查将交付的内容。参考 [Git 的 diff 文档](https://git-scm.com/docs/git-diff) |
| 上测试环境 | 配置变量、镜像或应用制品、数据库与存储、服务域名和日志入口 | 先验证启动与健康检查，再从浏览器或客户端走登录、功能与数据保存，记录构建版本和复测结果 |
| 离场后继续远程联调 | 已获准的 VPN 或远程接入、分流范围、DNS、证书和账号 | 从另一条网络重新连接，复测业务入口、登录和实际服务。按交接约定恢复临时路由、网卡和代理设置 |

最后留一条可复核的现场记录，方便同事在另一台电脑上重现。下面的内容可直接复制到工单。

```text
发生时间与接入方式：
目标域名与端口：
预期结果与实际现象：
解析结果、实际网卡和 TCP 测试：
代理模式、HTTP 状态或浏览器错误：
调整了什么，调整后哪个步骤通过：
临时设置的恢复方式：
```
