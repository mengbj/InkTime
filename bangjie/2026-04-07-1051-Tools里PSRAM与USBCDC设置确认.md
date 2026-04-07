# Tools里PSRAM与USBCDC设置确认

记录时间：2026-04-07 10:51（Asia/Shanghai）

## 本轮要确认的两个设置

本轮用户要求重点检查：
- `PSRAM = OPI PSRAM`
- `USB CDC On Boot = Enabled`

## 结合截图看到的当前实际状态

从用户截图里，我能明确读到：
- 顶部当前显示的是：`ESP32 Dev Module`
- 端口是：`/dev/cu.usbserial-110`
- `Flash Size = 16MB (128Mb)`
- `PSRAM = Enabled`
- 当前截图中 **没有看到** `USB CDC On Boot` 这一项

这意味着当前状态还**不是我们要的最终设置**。

## 为什么现在还不对

### 1. 选板不对
当前截图显示的是：
- `ESP32 Dev Module`

但我们这块新板实际应选择的是：
- `ESP32S3 Dev Module`

只要板型还是 `ESP32 Dev Module`，就说明当前 IDE 还在按普通 ESP32 的配置菜单显示，而不是按 `ESP32-S3` 的菜单显示。

### 2. PSRAM 当前是 `Enabled`，不是 `OPI PSRAM`
截图里现在看到的是：
- `PSRAM = Enabled`

这不等于我们前面要求的：
- `PSRAM = OPI PSRAM`

对 `ESP32-S3` 来说，Arduino Core 的官方板卡定义里，`PSRAM` 选项应当包含：
- `Disabled`
- `QSPI PSRAM`
- `OPI PSRAM`

所以：
- 如果你当前菜单里只有 `Enabled`，通常意味着你还没切到正确的 `ESP32S3 Dev Module`。

### 3. `USB CDC On Boot` 没出现，也说明当前不是 S3 正确菜单
对于 `ESP32S3 Dev Module`，官方板卡定义里有：
- `USB CDC On Boot`
  - `Disabled`
  - `Enabled`

而当前截图里没有这一项，也再次说明：
- **现在的 Tools 菜单还不是我们要的 S3 配置上下文**。

## 官方板卡定义核对结果

本机用 `arduino-cli` 核对 `ESP32S3 Dev Module` 的官方选项后，确认：
- `USB CDC On Boot` 确实存在
- `PSRAM` 选项里确实有：
  - `QSPI PSRAM`
  - `OPI PSRAM`

因此本轮判断可以写死为：

> 只要你在 Tools 里看到的是 `PSRAM = Enabled` 而不是 `OPI PSRAM`，并且没有 `USB CDC On Boot`，那就说明当前还没有真正切到 `ESP32S3 Dev Module`。

## 现在正确的操作顺序

### 第一步：先把 Board 改正确
在 Arduino IDE 里：
- `Tools -> Board -> ESP32 Arduino -> ESP32S3 Dev Module`

注意是：
- `ESP32S3 Dev Module`

不是：
- `ESP32 Dev Module`

### 第二步：重新打开 Tools 菜单
切换到 `ESP32S3 Dev Module` 之后，再重新看 `Tools` 菜单。

这时应当能看到：
- `USB CDC On Boot`
- `PSRAM`

### 第三步：把这两个设置改成目标值
最终要的是：
- `PSRAM = OPI PSRAM`
- `USB CDC On Boot = Enabled`

## 这一步为什么重要

因为当前我们的目标是：
- 用这块新的 `ESP32-S3-WROOM N16R8` 板稳定烧录；
- 然后继续跑 `ink-display-7C-photo.ino`；
- 后面还要驱动 7.3 寸四色墨水屏。

对于这条链路来说：
- `ESP32S3 Dev Module` 是基础前提；
- `OPI PSRAM` 更符合当前这类 `N16R8` 板子的预期；
- `USB CDC On Boot = Enabled` 有利于串口日志查看与后续排障。

## 本轮结论

当前截图对应的判断是：
- `USB CDC On Boot = Enabled`：**当前还没法确认，因为截图里没出现这项**
- `PSRAM = OPI PSRAM`：**当前不满足，截图里是 `PSRAM = Enabled`**
- 根因：**当前板型选成了 `ESP32 Dev Module`，而不是 `ESP32S3 Dev Module`**

## 当前项目状态更新

当前准确进度：
- 新板串口已识别：`/dev/cu.usbserial-110`
- 但 Arduino IDE 的板型设置当前仍不正确
- 所以下一步不是马上上传，而是：
  - 先切板到 `ESP32S3 Dev Module`
  - 再确认 `PSRAM = OPI PSRAM`
  - 再确认 `USB CDC On Boot = Enabled`
  - 然后再做最小程序烧录验证

## 本轮截图/状态保留说明

以文字方式保留本轮截图要点：
- 当前顶部显示：`ESP32 Dev Module`
- 当前端口：`/dev/cu.usbserial-110`
- 当前 `PSRAM` 显示为：`Enabled`
- 当前截图中未出现：`USB CDC On Boot`
- 当前结论：板型未切到 `ESP32S3 Dev Module`，因此两个目标设置还未真正到位

