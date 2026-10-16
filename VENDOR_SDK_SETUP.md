# 玉成 Android SDK 接入

原始来源：<https://gitee.com/yucheng_3/android-sdk-2.0>

项目锁定记录位于：

- `edge-runtime/configs/yucheng_android_sdk_source.json`
- `edge-runtime/configs/wearable_sdk_source.json`

本仓库不重新分发厂家 SDK 二进制，因为商业再分发许可尚未取得具名结论。请从原始仓库获取并核对锁定版本和 SHA-256，再实现 `phone-app/lib/core/watch/watch_gateway.dart` 的正式适配器。

接入约束：

- BLE 扫描、连接、重连和历史同步只在手机端执行。
- 厂家对象先转换为项目字段信封；必须保留原始时间、单位、来源和质量状态。
- 手表未声明支持的字段保持 `unavailable`，不得补零或生成正常值。
- 每条记录使用稳定幂等 ID；成员绑定后才能进入待发送队列。
- 原始厂家回调和健康数据不得写入普通日志。

