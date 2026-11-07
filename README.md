# AQUA Secure

AQUA 安全版：面向 Windows 10/11 与 Android 平板的本地优先音乐播放器。基于 AQUA 1.8.1 构建，重点处理了隐私、远程访问与代码保护问题。

## 下载

安装包只通过 GitHub Releases 提供：

- [AQUA Secure v1.8.1](https://github.com/luhugh/AQUA-Secure/releases/tag/v1.8.1-secure)
- Windows 安装版：`AQUA-Secure-Setup-1.8.1.exe`
- Windows 便携版：`AQUA-Secure-Portable-1.8.1.exe`
- Android：`AQUA-Secure-Android-1.8.1.apk`
- 校验文件：`SHA256SUMS.txt`

## 安全版改动

### 1. 移除 LX Music 内核接口

- 删除 `electron/lx.cjs`；
- 移除主进程 LX IPC；
- 移除 preload 的 `window.aquaDesktop.lx`；
- 安装包内不再暴露 LX 状态、控制或 Scheme 播放接口；
- 网易云页的 LX 内核切换条不会出现。

### 2. AQUA LINK 收紧

- 同步服务只监听 `127.0.0.1:47832`，不再监听局域网；
- 安全版通过 **USB + adb reverse** 使用同步；
- 每次启动生成随机 48 位配对令牌；
- 除 `/api/ping` 外，所有状态、曲库、音频、上传、控制接口都要求令牌；
- CORS 只允许 AQUA / Capacitor 的本机来源；
- 平板连接时需在电脑端复制配对码，再填入平板。

### 3. Android 隐私加固

- `android:allowBackup="false"`，避免网易云登录态进入系统备份；
- 网易云 Cookie 使用 Android Keystore 的 AES-GCM 加密存储；
- 旧版明文 Cookie 会在首次读取时迁移并覆盖；
- Release 构建启用 R8 代码压缩/混淆与资源收缩；
- Release 构建不会开启 WebView 调试；
- 明文流量仅允许 `localhost` / `127.0.0.1` 的 USB 回环；
- 移除 FileProvider 的宽泛外部路径暴露。

### 4. Windows 代码保护

- 渲染层与 Electron 主进程/preload 均使用 `javascript-obfuscator` 处理；
- 安装包基于混淆后的 `app.asar` 重新打包；
- 不包含源码、构建缓存或调试映射。

### 5. 隐私边界

- 没有接入分析、广告、遥测或崩溃上报 SDK；
- 网易云请求仅发往官方 `music.163.com` / CDN；
- 本地曲库、歌单、收藏、搜索历史保存在本机；
- 安全版 AQUA LINK 不监听局域网，不会把曲库暴露给同一 Wi-Fi 的其他设备。

## 功能

- 本地曲库扫描、内嵌封面/歌词、自建歌单、收藏、队列；
- 网易云官方登录、歌单、每日推荐、搜索；
- 歌手页、专辑页、相似歌曲、搜索历史/热搜/联想；
- 8 段均衡器、音量标准化、输出限幅、模拟空间音频；
- 实时频谱、沉浸频谱、Aero 2.0、5 套主题；
- Windows 透明桌面歌词；
- 播放接力、USB 本机曲库/歌单同步；
- 音质回落、媒体错误重试、缓冲恢复。

## 已知限制

- 安全版 **不支持局域网 AQUA LINK**，只支持 USB 回环；这是为了避免无 TLS 的局域网暴露。
- 安全版 **不包含 LX 内核**，也不提供第三方音源脚本接口。
- Windows 安装包未做商业代码签名，首次运行可能触发 SmartScreen。
- Android 包为正式签名的 release 构建；签名密钥不出售、不上传。
- 网易云版权/VIP 限制以官方账号为准，软件不会绕过。
- 空间音频为卷积混响模拟，不含 Dolby 解码。

## 校验

`SHA256SUMS.txt`：

```text
c2c702aba4b9cbc41d28d2eaeb45c2a792a7f6a9226617649de5336c0c792fba  AQUA-Secure-Android-1.8.1.apk
001bdf4c69af2d2a3c2b46f545a81caab035b65d0229213a09b22ec3cf18e2d3  AQUA-Secure-Portable-1.8.1.exe
fbe17b6bf270c5e1088dbaab5cd16cfff1300669fef3a216948d4ad479445b92  AQUA-Secure-Setup-1.8.1.exe
```

校验命令：

```powershell
Get-FileHash .\AQUA-Secure-Setup-1.8.1.exe -Algorithm SHA256
```

## 安装

### Windows

1. 运行安装版或便携版；
2. 首次启动时选择本地音乐目录；
3. 如需 USB 联动：电脑端点“设置 USB 转发”，平板连接 `127.0.0.1:47832`；
4. 把电脑端显示的配对码填入平板。

### Android

1. 安装 APK；
2. 授予音乐/通知权限；
3. 扫描媒体，或在设备联动中填写 USB 地址和配对码。

## 说明

这是一个**安全加固版**，不是普通版的功能替代。它主动关闭了局域网同步与 LX 扩展接口，换取更小的远程攻击面和更少的凭据暴露。
