# 官方资料索引与版本范围

[返回目录](README.md)

核对日期：2026-11-19。正文为针对装机流程的整理，链接保留原始来源，读者应选与实际板卡、模块及固件版本相符的页面。未进行具体飞机的实机验证。

## 微空硬件与模块

| 官方资料 | 用于核对 |
| --- | --- |
| [MicoAir743v2 手册](https://micoair.com/zh/docs/flight-controller/micoair743/micoair743v2-flight-controller-manual) | 固件目标、UART 映射、供电、安装和输出约束 |
| [NxtPX4v2 手册](https://micoair.com/zh/docs/flight-controller/nxtpx4/nxtpx4v2-flight-controller-manual) | 另一板卡的供电、磁力计、串口和孔距差异 |
| [MTF-01 手册](https://micoair.com/zh/docs/sensors/sensors/mtf-01-sensors) | 电源、LVTTL 接线、协议、分版本 PX4 设置 |
| [MTF-02/02P 手册](https://micoair.com/zh/docs/sensors/sensors/mtf-02-02p-sensors) | 两种模块量程与环境差异，不能照搬 MTF-01 全部规格 |
| [四合一蓝鸟电调手册](https://micoair.com/zh/docs/esc/esc) | 接口信号和具体型号 Pinout |
| [35A AIO 手册](https://micoair.com/zh/docs/flight-controller/micoair743-aio-series/micoair743v2-aio-35a-manual) | Bluejay 与协议支持的具体型号例子 |
| [微空助手说明](https://micoair.com/zh/docs/software-tutorial/micoassistant-guide) | 传感器版本、协议、数据检查 |
| [厂商 PX4 装机教程](https://micoair.com/zh/docs/flight-controller/flight-controller-guide/px4-flight-controller-configuration-tutorial) | 装配工序与历史示例；示例配置不代表任意飞机 |

## PX4 与地面站

| 官方资料 | 用于核对 |
| --- | --- |
| [QGroundControl 稳定版下载说明](https://docs.qgroundcontrol.com/Stable_V5.1/en/qgc-user-guide/getting_started/download_and_install.html) | 获取地面站及安装说明，核对 PX4 兼容性 |
| [PX4 1.17 机型选择](https://docs.px4.io/v1.17/en/config/airframe) | 机架预设与应用流程 |
| [PX4 1.17 飞控/传感器方向](https://docs.px4.io/v1.17/en/config/flight_controller_orientation) | 安装方向与校准前提 |
| [PX4 1.17 加速度计校准](https://docs.px4.io/v1.17/en/config/accelerometer) | 校准操作 |
| [PX4 1.17 罗盘校准](https://docs.px4.io/v1.17/en/config/compass) | 磁干扰、校准和外置罗盘 |
| [PX4 1.17 遥控设置](https://docs.px4.io/v1.17/en/config/radio) | 遥控校准与通道 |
| [PX4 1.17 模式设置](https://docs.px4.io/v1.17/en/config/flight_mode) | 模式开关 |
| [PX4 1.16 执行器设置](https://docs.px4.io/v1.16/en/config/actuators) | 电机映射、转向、拆桨测试与 DShot/PWM 差异 |
| [PX4 1.17 电池设置](https://docs.px4.io/v1.17/en/config/battery) | 电压/电流和电量估计 |
| [PX4 1.16 光流](https://docs.px4.io/v1.16/en/sensor/optical_flow) | 光流与测距的关系 |
| [PX4 1.17 光流](https://docs.px4.io/v1.17/en/sensor/optical_flow) | MAVLink 消息与积分光流方向 |
| [PX4 1.16 参数表](https://docs.px4.io/v1.16/en/advanced_config/parameter_reference) | 主示例融合参数的数值与适用条件 |
| [PX4 1.17 参数表](https://docs.px4.io/v1.17/en/advanced_config/parameter_reference) | 新版协议和估计器参数 |
| [PX4 1.17 EKF2](https://docs.px4.io/v1.17/en/advanced_config/tuning_the_ecl_ekf#optical-flow) | 质量、测距与光流融合前提 |
| [PX4 1.17 Position 模式](https://docs.px4.io/v1.17/en/flight_modes_mc/position) | 定位模式的使用条件 |
| [PX4 1.17 失效保护](https://docs.px4.io/v1.17/en/config/safety) | 遥控/定位/低电量等失效动作 |
| [PX4 首飞指南 main](https://docs.px4.io/main/en/flying/first_flight_guidelines) | 首次试飞规划；该页面会持续变化 |

## 版本解释

- 主示例采用 1.16.x，以明确板卡目标与参数基线，而非宣称它对所有飞机都是最佳固件。
- 截至核对时，官方文档列有 1.17 稳定版；本文在需要时引用其通用流程和参数解释，并单列 MTF 的协议差异。
- 厂商手册包含更早版本示例。遇到命名冲突，先看实际固件参数元数据与对应版本官方说明，不能混合 ArduPilot 和 PX4 的参数。
- 本目录不包含预编译飞控固件、可直接导入的参数包、接线实测照片或试飞合格证明。

## 维护

报告问题时提供板卡/模块型号、PX4 完整版本、对应文档段落、实际观测和原始来源；避免只写“飞不稳”。连接图纸与软件版本更新后，应重新核对受影响段落与清单。
