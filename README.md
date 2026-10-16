# 家庭健康终端：周末真机联调仓库

本仓库只保留“手表 → Android 手机 → RK3576 Android 开发板 → 手机显示”联调所需的源码、协议、测试和离线 ASR 资产。它是私有工程交接仓库，不是正式上线版本。

> 当前 GitHub 交付为 `family-health-edge-integration-source-lite.zip`：包含联调源码与配置，但不包含 ONNX/RKNN/TFLite 等大模型权重、字体和历史文档。周末可先完成手表—手机—开发板协议与网络链路；模型权重需线下或对象存储单独交付后再做完整推理验收。

## 目录

- `phone-app/`：手机 Flutter APP。已接入健康、OCR、ASR 的端侧客户端与页面。
- `edge-runtime/android_app/`：RK3576 Android 端侧 Flutter 工程和离线 ASR 资产。
- `edge-runtime/device_service/`：当前 Python 参考服务与 `/v1` 协议实现，用于电脑仿真和契约验证。
- `edge-runtime/configs/phone_edge_contract.json`：手机—端侧冻结协议和真实完成状态。
- `WEEKEND_INTEGRATION.md`：周末联调的最短步骤、验收点和已知阻断。

## 克隆

为兼容普通 GitHub 克隆，超过 100 MiB 的 ASR 编码器被拆成四个校验分片。克隆后先恢复模型：

```powershell
git clone <本仓库地址>
cd family-health-edge-integration
powershell -ExecutionPolicy Bypass -File .\tools\restore_asr_assets.ps1
```

脚本会逐片校验、重组并验证最终 SHA-256。恢复成功前不要构建端侧 Android APP。

## 当前真实状态

- 手机端 HTTPS/WSS 客户端、健康/OCR/ASR 页面和失败关闭逻辑已实现；Flutter 测试 155/155、静态分析 0 问题。
- 手机端真实玉成 SDK `WatchGateway` 适配器尚未实现，默认不会伪造已连接手表。
- RK3576 Android 的局域网 HTTPS/WSS 服务尚未实现；现有 Python 服务只用于电脑 loopback 仿真。
- OCR Android 产品运行时、正式设备配对、公司签名、许可和 RK3576 真机性能均未验收。
- 健康模块只能给非诊断提示，不能输出疾病诊断或权威医疗建议。

因此，本仓库能让同事继续开发和完成真机联调，但不能宣称“拉下来即可完成整条产品链路”。

## 不在仓库中的内容

虚拟环境、Gradle/Flutter 缓存、构建目录、训练数据、历史对比模型、重复 ZIP/APK、真实健康数据及厂家 SDK 二进制未上传。厂家 SDK 请按 `VENDOR_SDK_SETUP.md` 从原始仓库获取并核验版本。


