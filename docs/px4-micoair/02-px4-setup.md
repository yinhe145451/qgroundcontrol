# PX4 与 QGroundControl 设置

[返回目录](README.md)

全程先拆桨，固定飞机；接线或换电池时先退出测试并断电。QGC 不同版本的中文菜单名称可能不同，以下同时给出常见英文名称。

## 1. 固件与备份

1. 根据 [QGroundControl 官方稳定版下载说明](https://docs.qgroundcontrol.com/Stable_V5.1/en/qgc-user-guide/getting_started/download_and_install.html)安装与本机 PX4 版本兼容的地面站，用能传数据的 USB 线连接；不要误装下载页面中的 daily 测试版。
2. 记录飞控标签、硬件版本、现有固件和 QGC 版本；已有配置先导出参数、保留日志。
3. 选择与板卡完全匹配的 PX4 固件。MicoAir743v2 的 PX4 目标为 `micoair_h743-v2`；NxtPX4v2 的目标按其手册核对，不要用 743/AIO/Lite 固件替代。
4. 优先采用厂商已说明支持该硬件的稳定版本。QGC 未自动识别目标时，使用厂商说明的本地固件或 MicoConfigurator；不能把刷入成功等同于型号正确。
5. 更新后断电重启，验证传感器、端口和参数均能正常读取；跨版本升级重新核对配置，旧参数不能整包盲目恢复。

MicoAir743v2 自 PX4 1.16.0 起进入官方支持列表；来源见 [厂商固件说明](https://micoair.com/zh/docs/flight-controller/micoair743/micoair743v2-flight-controller-manual#px4)。这不表示所有微空板卡都从同一版本开始支持。

## 2. 选择机型与安装朝向

1. 在 Vehicle Setup → Airframe 选择与实际构型相符的四旋翼机架，如 Quadrotor X，并应用重启。
2. 设置飞控相对机头的安装方向。不要为了让显示看起来水平而改动电机编号或外设方向。
3. 手动俯仰、横滚和转动机身，确认 QGC 姿态显示与实际动作一致，机头方向定义清楚。

机架预设只是起点，不能保证动力系统和 PID 已适合你的飞机。参见 [机型选择](https://docs.px4.io/v1.17/en/config/airframe)与 [飞控安装方向](https://docs.px4.io/v1.17/en/config/flight_controller_orientation)。

## 3. 传感器校准

在 Sensors 页面按地面站引导完成：

- 陀螺仪：飞机静止，避免台面振动。
- 加速度计：依次完成界面要求的各个朝向，每个朝向放稳；完成后按要求校准水平基准。
- 磁力计：使用实际机载安装状态，在远离钢桌、电源大电流线和强磁物体的环境下完成校准；异常先排查干扰和朝向。
- 检查校准状态与告警；有不一致告警时先处理来源，不能直接放宽阈值。

NxtPX4v2 无板载磁力计，需要合适的外部航向方案。光流本身不能提供可靠绝对航向。来源：[加速度校准](https://docs.px4.io/v1.17/en/config/accelerometer)、[罗盘校准](https://docs.px4.io/v1.17/en/config/compass)、[NxtPX4v2 硬件说明](https://micoair.com/zh/docs/flight-controller/nxtpx4/nxtpx4v2-flight-controller-manual)。

## 4. 遥控与飞行模式

1. 按接收机协议接线、对频，在 Radio 页面校准 Roll、Pitch、Yaw、Throttle 和模式通道。
2. 逐个拨动开关，检查通道方向、中心和端点；确认低油门、解锁开关与模式显示符合预期。
3. 至少规划 Position、Altitude 等适合本机能力和操作者水平的模式；Altitude 不提供水平位置保持，仍需人工控住漂移。
4. 在拆桨、禁止电机意外启动的条件下测试遥控丢失时的提示和配置动作。
5. 根据有无可靠全局位置、室内空间和障碍物选择失效策略。没有有效全局位置时，不把 RTL 当作室内光流飞机的默认退路。

参考 [遥控设置](https://docs.px4.io/v1.17/en/config/radio)、[模式设置](https://docs.px4.io/v1.17/en/config/flight_mode)与 [失效保护](https://docs.px4.io/v1.17/en/config/safety)。

## 5. 电调协议、电机顺序与旋向

1. 在 Actuators 检查构型与四个逻辑 Motor 的位置、旋向示意图。
2. 在正确的输出组选择电调、飞控均支持的协议。兼容 DShot 的组合可从 DShot300 开始；同一硬件定时器组要满足协议约束。
3. 将实际输出通道分配给 Motor 1–4；不要照抄其他装机案例的“3-2-1-4”。
4. 接入电池，确认无桨，按提示启用测试，一次只测试一路，用短时间低输出确认实际位置。
5. 记录每个电机的实际转向，并与界面要求一致。支持 DShot 转向命令的电调可在界面保存方向；否则断电后按电调手册处理，或调换电机三相线中的两根。
6. 重启，再测四路，确认映射与转向确已保留。

**DShot 不需要油门行程校准；PWM/OneShot 是否需要校准按协议和电调手册判断。** 不为调整方向就随意刷写电调固件，也不要假设硬件支持双向 DShot 就代表当前 PX4 固件支持。

参考 [PX4 执行器设置](https://docs.px4.io/v1.16/en/config/actuators)；微空 [35A AIO 手册](https://micoair.com/zh/docs/flight-controller/micoair743-aio-series/micoair743v2-aio-35a-manual)提供 Bluejay/DShot 的具体型号示例，不能据此推断其他电调也支持相同协议。

## 6. 电池监测

在 Power 页面核对电池类型、节数、容量与监测来源。飞控 ADC 电压采样、CURR 模拟采样和电调数字遥测必须区分。

- 用万用表测实际电池总电压，与 QGC 读数对照，按界面校准 Voltage Divider。
- 电流系数从对应电调/电源模块的 PX4 说明开始核对，再用可靠电流测量或充放电数据校验。
- 不跨抄 ArduPilot、Betaflight、INAV 的数值，因为参数定义和单位可能不同。
- 按电池体系及实际压降设置低电量、严重低电量处理；不要把单一电量百分比当作唯一依据。

参考 [PX4 电池估计与校准](https://docs.px4.io/v1.17/en/config/battery)。

完成本页后，导出“基础设置完成”的参数备份，填写 [装机记录](BUILD-RECORD.md)，再配置光流。
