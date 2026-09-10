---
name: "Grok · 更新 Clash Verge 订阅"
description: >-
  【Grok Bot 专用】当用户要更新 Clash Verge / Clash Verge Rev 的远程订阅节点、刷新 VPN
  配置、检查订阅是否已生效，或调整系统代理/TUN 以稳定 Grok Bot 本机自动化通道时使用。
  不适用于 OpenClaw / Codex 等其他助手运行时。
---
# Grok · 更新 Clash Verge 订阅

> **适用范围：仅 Grok Bot。** 本技能依赖 Grok Bot 的本机执行通道、辅助功能自动化与 `machineId` 工作流；OpenClaw、Codex 等请勿直接套用。

在用户本机把 Clash Verge（含 Clash Verge Rev）里的 **remote 订阅**拉最新并确认核心已加载。不要只改网站账号页；以本地客户端配置为准。

## 前置

- 在用户已注册且可执行的电脑上操作（`machineId`）。通道偶发不通时：短等重试，不要误判成「未授权」。
- **本机自动化通道稳定偏好（macOS）**：优先 `enable_system_proxy: true` 且 `enable_tun_mode: false`。TUN（`utun0` / `198.18.0.1` 自动路由）容易导致 Grok Bot「新开执行通道」频繁 `unreachable`；系统代理相对友好。改 `verge.yaml` 须在 **Clash Verge 已完全退出后**写入，再启动，否则运行中/退出时会被内存状态盖回。
- macOS 常见目录：
  - `~/Library/Application Support/io.github.clash-verge-rev.clash-verge-rev/`
  - 若无 Rev，再查带 `clash-verge` / `Clash` 的 Application Support 目录
- 关键文件：`profiles.yaml`、`profiles/<uid>.yaml`、运行时 `clash-verge.yaml`；核心常为 `verge-mihomo`，控制口多为 unix socket `/tmp/verge/verge-mihomo.sock`（以 `config.yaml` 为准）。
- 无公开 `update_profile` HTTP/CLI；GUI 内为 Tauri 命令。本机自动化用辅助功能点菜单。

## 步骤

1. **定位配置**
   - 确认 Clash Verge 相关进程是否在跑。
   - 读 `profiles.yaml`：找出 `type: remote` 的项（`uid`、`name`、`file`、`url`、`updated`、`extra`）。
   - 若有多条 remote，优先当前 `current` 指向的那条；多条且不明确时再问用户选哪条。

2. **优先：GUI 触发更新（推荐）**
   - 激活 Clash Verge；侧栏点「订阅」（按钮名可能是带空格的 `订 阅`，用「名称包含 订」匹配）。
   - 对目标订阅卡片（如名称 `CroLAX`）打开上下文菜单：`AXShowMenu` / 右键；菜单项含 `更新`、`更新（代理）` 等。
   - 点击 **`更新`**（不要点删除）。若 `menus` 计数为 0，改从 `entire contents` 里找 `menu item` 名为 `更新` 再 `click`（菜单已打开时整窗可能只剩约二十来个元素，几乎都是菜单项）。
   - 等数秒让下载与 enhance 完成。控制键点击等操作可能卡住 `osascript`，卡住就结束进程后重试；菜单若仍留着，直接点 `更新` 即可。

3. **备选：拉取并写盘（仍需第 2 步或等价重载）**
   - 用 remote 的 `url`：`curl -sS -L -A 'clash-verge/v2' -D <headers> -o <new.yaml> --max-time 60 '<url>'`。
   - 校验含 `proxies:` 且体积合理；备份后写入 `profiles/<file>`，更新 `profiles.yaml` 的 `updated` / `extra`。
   - 仅写盘通常不够；还须 GUI「更新」或能完整跑 enhance 并重载核心的等价手段。

4. **验收**
   - `profiles.yaml` 的 `updated`、`profiles/<file>` 与 `clash-verge.yaml` 的 mtime 接近当前时间。
   - unix socket：`/version`、`/proxies` 中能看到订阅名与节点；可抽查流量提示项。
   - 向用户回报：订阅名、更新时间、节点是否在线、流量/到期（若有）。

## 注意

- 不要把某个用户的订阅 URL、uid、机器路径写死进其它流程；每次从本机 `profiles.yaml` 读取。
- 连接不稳时分段执行，成功一步记一步。
- 用户若还要「定期自动更新」，另建 **routine** 调用本技能，间隔由用户指定；技能本身不含日程。
