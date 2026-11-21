# sing-box 一键安装 / 管理脚本

多配置并存的 sing-box 服务端部署与管理脚本，基于 `233boy/sing-box` 修改维护。

本仓库为独立 fork，安装与更新均从本仓库获取，不依赖上游发布。

## 本 Fork 的改动

- 节点信息同时输出 IPv4 与 IPv6 分享链接，覆盖 VLESS、VMess、Trojan、Shadowsocks、Hysteria2、TUIC、AnyTLS、Socks
- 移除主菜单的群组入口，以及安装完成后的关注 / 文档 / 推广区块
- 安装脚本与脚本自更新指向本仓库
- 当前脚本版本：`v1.24`

## 支持环境

- Root 权限
- 64 位 Linux：Debian / Ubuntu / CentOS / SUSE / Alpine
- systemd 或 OpenRC
- 使用自动 TLS 的协议需要域名已解析到本机，且 80 / 443 可访问

## 安装

在线安装：

```bash
bash <(wget -qO- https://raw.githubusercontent.com/Felix666-ship-It/sing-box/main/install.sh)
```

```bash
bash <(curl -Ls https://raw.githubusercontent.com/Felix666-ship-It/sing-box/main/install.sh)
```

本地安装：

```bash
git clone https://github.com/Felix666-ship-It/sing-box.git
cd sing-box
bash install.sh -l
```

安装参数：

| 参数 | 说明 |
| --- | --- |
| `-f, --core-file <path>` | 使用本地 sing-box 核心压缩包 |
| `-l, --local-install` | 使用当前目录的脚本源码安装 |
| `-p, --proxy <addr>` | 下载时使用代理，如 `http://127.0.0.1:2333` |
| `-v, --core-version <ver>` | 指定 sing-box 核心版本，如 `v1.12.0` |
| `-h, --help` | 显示安装帮助 |

安装完成后可直接使用 `sing-box` 或 `sb` 命令。

主菜单第 9 项「更新脚本」会直接从本仓库 Release 检查并更新脚本。

交互菜单中：输入 `0` 返回主菜单，输入 `00` 退出脚本。

安装只部署脚本、核心与基础 `config.json`，不会自动创建任何节点；使用 `sing-box add [协议]` 手动添加第一个节点。

## 快速开始

```bash
sing-box                     # 交互式菜单
sing-box add reality         # VLESS-REALITY
sing-box add hy2             # Hysteria2
sing-box add trojan          # Trojan
sing-box add tuic            # TUIC v5
sing-box add anytls          # AnyTLS（需 sing-box >= 1.12）
sing-box add ss              # Shadowsocks 2022
sing-box add vws             # VLESS-WS-TLS
sing-box add vh2             # VLESS-H2-TLS
sing-box add vhu             # VLESS-HTTPUpgrade-TLS
sing-box add ws              # VMess-WS
sing-box add wss             # VMess-WS-TLS
sing-box add h2              # VMess-H2-TLS
sing-box add tws             # Trojan-WS-TLS
sing-box add socks           # Socks5
sing-box info                # 查看节点与分享链接
```

协议别名：

| 协议 | `add` 参数 |
| --- | --- |
| VLESS-REALITY | `reality` / `r` |
| VLESS-HTTP2-REALITY | `rh2` |
| VLESS-WS / H2 / HTTPUpgrade + TLS | `vws` / `vh2` / `vhu` |
| VMess-TCP / HTTP / QUIC | `tcp` / `http` / `quic` |
| VMess-WS / H2 / HTTPUpgrade + TLS | `wss` / `h2` / `hu` |
| Trojan / Trojan-WS / H2 / HTTPUpgrade + TLS | `trojan` / `tws` / `th2` / `thu` |
| Hysteria2 | `hy2` / `hy` |
| TUIC v5 | `tuic` |
| AnyTLS | `anytls` |
| Shadowsocks | `ss` |
| Socks5 | `socks` |
| 直接转发 | `direct` / `door` |

## IPv4 / IPv6 双栈

添加或查看节点时：

1. 先输出 IPv4 分享链接。
2. 检测到公网 IPv6 时，紧接着输出 `链接 (IPv6)` 区块。
3. 纯 IPv4 机器只输出 IPv4 链接，行为与之前一致。

IPv6 地址获取顺序：

1. `wget -6 https://one.one.one.one/cdn-cgi/trace` 读取真实出口 IPv6。
2. 失败时回退 `ip -6 addr show scope global`。

服务端 inbound 默认 `listen: "::"`，同时监听 IPv4 与 IPv6；无需额外开启双栈。云厂商安全组或系统防火墙需放行对应端口，Hysteria2 / TUIC 为 UDP，其余为 TCP。

## 常用命令

| 命令 | 说明 |
| --- | --- |
| `sing-box add [协议]` | 添加配置 |
| `sing-box change [名称] [项] [值]` | 修改配置 |
| `sing-box del [名称]` | 删除配置（无二次确认） |
| `sing-box info [名称]` | 查看配置与链接 |
| `sing-box url [名称]` | 只显示分享链接 |
| `sing-box qr [名称]` | 显示二维码 |
| `sing-box log` | 查看日志 |
| `sing-box status` | 查看运行状态 |
| `sing-box start / stop / restart` | 管理服务 |
| `sing-box test` | 测试运行 |
| `sing-box update core` | 更新 sing-box 核心 |
| `sing-box update.sh` | 更新脚本 |
| `sing-box bbr` | 启用 BBR |
| `sing-box dns` | 设置 DNS |
| `sing-box import` | 导入 sing-box / v2ray 脚本配置 |
| `sing-box uninstall` | 卸载 |
| `sing-box help` | 查看完整帮助 |

## 文件位置

```text
/etc/sing-box/bin/sing-box       sing-box 核心
/etc/sing-box/conf/*.json        每个节点的配置
/etc/sing-box/config.json        汇总配置
/etc/sing-box/sh/                脚本源码
/usr/local/bin/sing-box          脚本入口
/usr/local/bin/sb                脚本入口（简写）
/var/log/sing-box/               日志
```

服务名：`sing-box.service`；启用自动 TLS 时另有 `caddy.service`。

## 更新与卸载

```bash
sing-box update core      # 更新核心
sing-box update.sh        # 更新脚本（从本仓库 Release 获取）
sing-box uninstall        # 卸载
```

`del` / `ddel` 会直接删除配置，没有确认步骤，执行前确认名称。
