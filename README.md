# 音频输出通道分配 · AudioRouteAssign

为 Windows 上的每个应用单独指定音频输出与输入设备。单文件 exe，双击即用，常驻托盘。

![Windows](https://img.shields.io/badge/Windows-10%20%7C%2011-0078D6?logo=windows&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)
![Size](https://img.shields.io/badge/exe-4.0%20MB-blue)

![主界面](src/01-overview.png)

下载：[Releases](../../releases/latest)

## 项目能力

- **会话枚举**：列出已建立音频会话的进程，含未播放的「待机」会话，播放与录音分开显示
- **独立设备**：每个进程分别设置输出设备与输入设备，互不影响
- **设备识别**：枚举系统声卡与虚拟声卡，下拉显示系统设备名
- **两种模式**：进程固定绑定按 exe 长期生效；临时会话只作用于当前会话，退出时还原系统路由
- **分组**：多个进程归入一组，组卡统一设置设备与音量，支持置顶、折叠、展开
- **条目常驻**：进程退出后卡片保留，可标记为「无用」隐藏
- **会话置顶**：☆/★ 置顶并优先排序
- **音量**：每应用独立滑块、10% 档位下拉、一键静音，勾选「默认」把音量写入配置
- **测试音**：会话与分组可向目标输出设备播放 440 Hz 短音
- **应用图标**：从进程 exe 提取真实图标
- **托盘常驻**：关闭窗口收进托盘，左键打开主窗口，右键打开窗口 / 应用配置 / 仅音量模式 / 退出
- **严格单实例**：重复双击只唤出已有窗口，不新开进程
- **开机自启**：界面开关控制，写入 `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- **权限模式**：默认为完整模式；托盘勾选「仅音量模式」后重启不再提权
- **日志面板**：按总 / 信息 / 警告 / 错误分流
- **配置落盘**：`%LOCALAPPDATA%\AudioRouteAssign\config.json`

## 已知限制

- 使用 Windows 未公开接口，系统大版本更新后可能失效；失效时仍保留规则，并在日志提示策略接口不可用
- 策略在会话建立时被读取，已在运行的应用通常需要重启才会切到目标设备
- 仅支持 Windows 10 / 11 x64
- 修改音频路由需要管理员权限

## 工作原理

1. 通过 WASAPI 枚举音频端点，读取 `IAudioSessionManager2` 会话，取 `IAudioSessionControl2::GetProcessId`，再用 `QueryFullProcessImageNameW` 获取 exe 路径
2. 通过 `Windows.Media.Internal.AudioPolicyConfig` 把「进程 → 端点」写入系统音频策略
3. WASAPI 端点 ID 与设备接口路径之间做格式转换
4. 后台线程轮询会话变化并自动下发规则

## 免责声明

本软件按「现状」（AS IS）提供，不附带任何明示或暗示的担保。它不是驱动程序，不安装内核驱动、不注入进程、不拦截音频流，只写入「进程 → 设备」的音频策略偏好；不联网、不上传数据、无遥测。

使用本软件可能导致应用音频被路由到非预期设备、应用无声或系统音频策略被修改，这些影响通常可通过「清除」或系统声音设置恢复默认设备消除。在任何情况下，作者与贡献者均不对因使用或无法使用本软件产生的任何直接、间接、偶然、特殊或后果性损害承担责任，包括但不限于数据丢失、业务中断、设备损坏或利润损失。生产、直播、录音等关键场景请先自行验证。

本软件以管理员权限运行，请仅从本仓库 Releases 页面下载。Windows 是 Microsoft Corporation 的注册商标，本项目与 Microsoft 无任何关联，未获其背书或认证。

## 版权

[MIT License](LICENSE) © 2026 lindagao
