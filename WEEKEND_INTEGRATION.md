# 周末真机联调清单

目标链路：玉成手表通过 BLE 连接 Android 手机；手机通过同一家庭 Wi-Fi 调用 RK3576 Android 端侧服务；端侧结果回到手机 APP 显示。

## 1. 环境

- 手机：Android 14，开启蓝牙、附近设备和录音权限。
- 开发板：RK3576、Android 14、`arm64-v8a`，开启 ADB。
- 电脑：Flutter 3.41.7、Dart 3.11.5、Java 21、Android SDK 35/36、NDK 28.2.13676358、Git LFS。
- 网络：手机与开发板连接同一个 Wi-Fi；先记录开发板 IP。

## 2. 手机端

```powershell
cd phone-app
flutter pub get
flutter analyze --fatal-infos
flutter test
flutter build apk --debug --split-per-abi
```

当前 `lib/core/watch/` 已有 `WatchGateway` 边界、字段规范化和失败关闭实现，但缺真实玉成 SDK 适配器。按 `VENDOR_SDK_SETUP.md` 接入后，业务层只调用 `WatchGateway`，不要把厂家类直接散落到 Provider 或页面中。

## 3. RK3576 端侧

```powershell
cd edge-runtime/android_app
flutter pub get
flutter analyze --no-pub
flutter test --no-pub
flutter build apk --debug --target-platform android-arm64 --no-pub
```

开发板侧还必须实现并启动一个前台 Android Service，监听冻结的 `/v1` HTTPS/WSS 协议。正式模式禁止明文 HTTP；工程联调若临时允许明文，必须只在隔离局域网使用，并明确标记为工程模式。参考契约：

- `edge-runtime/configs/openapi.yaml`
- `edge-runtime/configs/phone_edge_contract.json`
- `edge-runtime/device_service/application.py`
- `edge-runtime/device_service/server.py`

## 4. 最小验收顺序

1. `adb devices -l` 只出现一台状态为 `device` 的 RK3576。
2. 核对 `ro.board.platform`、`ro.product.cpu.abi`、Android 版本和安全补丁。
3. 手机能真实扫描、连接手表并读取至少一条带时间戳的心率记录；未支持字段保持“不可用”。
4. 手机能访问开发板 `/v1/health`，再以成员级令牌访问 `/v1/version`。
5. 一条经用户确认的心率记录调用 `/v1/health/evaluate`，结果回到同一成员页面。
6. OCR/ASR 任务未经确认不得写入档案；任一模型失败不得拖垮其他模块。
7. 断开 Wi-Fi、杀进程、拒绝权限、重复提交和切换成员后，应用必须失败关闭且不能串数据。

## 5. 必须回传

- 手表型号、固件、SDK 版本和支持字段。
- RK3576 板卡 SKU、Android 指纹、RKNPU 驱动、RKNN Runtime。
- APK 哈希、ADB 安装/启动结果、手机和开发板 IP。
- 每个接口的状态码与固定错误码；日志不得包含原始健康值、图片或语音。

没有以上真机证据时，只能写“工程构建/仿真通过”，不能写“产品联调或上线完成”。

