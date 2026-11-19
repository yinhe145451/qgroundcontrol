# 光流与测距配置

[返回目录](README.md)

本页的接线示例限定为 **MicoAir743v2＋MTF-01＋PX4 1.16.x**。安装其他型号时，先核对端口映射与固件支持，再使用同样的验证顺序。

## 1. 模块设置与安装方向

在厂商 [微空助手](https://micoair.com/zh/docs/software-tutorial/micoassistant-guide) 中核对模块型号、版本、通讯波特率和输出协议。选择支持 PX4 的协议；若当前模块提供 Auto，也应确认自动识别后的数据确实被飞控接收。排查协议问题时，可按手册明确选择 `Mavlink_px4`，不要选择 ArduPilot 的协议。

MTF 镜头向下，按模块原厂方向图安装。模块内部旋转设置与 PX4 的 `SENS_FLOW_ROT` 应共同实现正确的传感器到机体坐标变换，不能重复旋转两次。来源：[MTF-01 手册](https://micoair.com/zh/docs/sensors/sensors/mtf-01-sensors)。

## 2. 先分清三个编号

| 名称 | 含义 | 主示例 |
| --- | --- | --- |
| UART4 | 板上物理串口/焊盘 | 光流实际连接的 TX4/RX4 |
| TELEM2 | 该板在 PX4 中的串口名称 | 对应 UART4 |
| MAV_1 | 某一个 MAVLink 软件实例 | 可分配给空闲的 TELEM2；并非“物理串口 1” |

上表来自 [743v2 的 PX4 串口映射](https://micoair.com/zh/docs/flight-controller/micoair743/micoair743v2-flight-controller-manual)。例如该板 UART3 对应 GPS1，而 NxtPX4v2 的 UART1 才对应 GPS1，因此绝不能把“UART 编号”等同于“GPS/TELEM 编号”。

确认 UART4 没有连接数传、图传、伴随计算机等设备，也没有被另一协议驱动占用。一个物理 UART 只能按其配置服务于相应链路。

## 3. 主示例的配置顺序

1. 按 [接线表](01-hardware-wiring.md) 接到 UART4，使用正确的 5V、GND 和交叉 TX/RX。
2. 用 USB 连接 QGC，等参数完整读出；核实光流模块已获得外设电源。
3. 确认 MAVLink 实例 `MAV_1` 可用，将 `MAV_1_CONFIG` 设为 **TELEM2**，重启飞控。
4. 设置 `MAV_1_MODE` 为 **Normal**，`SER_TEL2_BAUD` 为 **115200**，与模块保持一致；重启后检查参数保留。
5. 若实际使用的是其他空闲实例或其他 UART，必须同步替换 `MAV_1_*` 和对应串口波特率参数；不要与现有 MAVLink 实例重复占用同一端口。
6. 先验证数据，再选择估计器融合方式。通讯通了与可以定位是两个不同的验收阶段。

厂家串口配置依据：[MTF 系列的 PX4 配置段落](https://micoair.com/zh/docs/sensors/sensors/mtf-01-sensors#px4)。本例默认普通无硬件流控串口接线；若当前固件要求 CTS/RTS，应查该板和串口参数说明处理，不额外猜接信号线。

## 4. PX4 1.16 的估计器设置

| 参数 | 设置或核对内容 | 使用条件 |
| --- | --- | --- |
| `EKF2_OF_CTRL` | Enabled，启用光流融合 | 有有效测距、光流质量及方向验证 |
| `SENS_FLOW_ROT` | 与实际安装方向匹配 | No rotation 仅适用于默认安装与模块设置 |
| `EKF2_RNG_CTRL` | 0 禁用；1 条件融合；2 持续启用 | 选择符合实际场地、量程和高度源设计的模式 |
| `EKF2_HGT_REF` | 0 气压；1 GPS；2 测距；3 视觉 | 改变高度参考源必须有对应有效观测 |
| `EKF2_OF_POS_X/Y/Z` | 测量光流焦点相对机体参考中心的偏移，单位米 | FRD：前为正 X，右为正 Y，下为正 Z |

对于**平坦地面、持续处于有效测距范围内的室内低空示例**，厂商流程使用测距辅助和测距高度参考；对应 PX4 1.16 可核对 `EKF2_RNG_CTRL=2`、`EKF2_HGT_REF=2`。这组设置不是通用室外方案：飞越台阶、桌面或量程边界会改变地面参考或丢失数据。存在不同工况时，应重新设计高度来源与失效策略。

`EKF2_RNG_CTRL=1` 是带速度/高度条件的融合，不能当作 `2` 的同义写法。光流融合也不意味着必须始终把测距作为高度参考。数值与条件以 [PX4 1.16 参数定义](https://docs.px4.io/v1.16/en/advanced_config/parameter_reference#EKF2_RNG_CTRL) 和 [高度参考说明](https://docs.px4.io/v1.16/en/advanced_config/parameter_reference#EKF2_HGT_REF) 为准。

不要为让光流生效而一并关闭 GNSS、罗盘、位置或解锁检查。没有定位时应排查实际观测与融合条件；混合 GNSS/光流另行验证切换行为。

## 5. 版本差异

| PX4 版本 | 应核对的配置体系 |
| --- | --- |
| 1.12–1.13 | 历史教程使用 `EKF2_AID_MASK`、`EKF2_RNG_AID`、`EKF2_HGT_MODE`；按对应旧版本文档设置位掩码，不能把整个掩码覆盖成猜测值 |
| 1.14–1.16 | 使用 `EKF2_OF_CTRL`、`EKF2_RNG_CTRL`、`EKF2_HGT_REF`；先完成通讯与识别，必要时重启/刷新后才能找到方向参数 |
| 1.17 | 继续核对新版参数；MTF 厂商手册额外要求检查 `MAV_PROTO_VER=1`，应用前评估其他 MAVLink 链路兼容性 |

关于最后一项：在 PX4 1.17 中，`MAV_PROTO_VER=1` 的含义是“以 MAVLink 1 起始，检测到 v2 可升级”，不是永久强制 v1。它是全局协议设置，可能影响同机的其他链路；仅在对应 MTF 型号/模块版本及固件组合需要时，按厂商说明处理并复测各链路。来源：[MTF 版本说明](https://micoair.com/zh/docs/sensors/sensors/mtf-01-sensors#px4)、[PX4 1.17 协议参数](https://docs.px4.io/v1.17/en/advanced_config/parameter_reference#MAV_PROTO_VER)。

本文不提供自动导入的整套 `.params`，以免覆盖另一型号的端口、电源和安全设置。修改后应从本机重新导出参数。

## 6. 从通讯验证到融合验证

### 通讯层

优先 USB 连接 QGC，在 Analyze Tools → MAVLink Inspector 观察 `DISTANCE_SENSOR` 和 `OPTICAL_FLOW_RAD`。若低带宽数传中看不到消息，不直接认定模块故障；检查 USB 连接下的数据及机载状态。

上下移动无桨飞机，测距应在有效范围内连续跟随实测高度；按字段定义核对单位。保持机体姿态基本不变并平移，光流数据应有对应变化；角速度积分不是直接的米每秒速度。

该层判定标准是消息有更新且物理趋势正确，**不是某个固定的地面站消息频率**。传感器数据率和飞控对地面站的转发率不能混为一谈。

### 坐标与质量层

在有纹理、亮度足够的平整地面上进行，避开反光、透明、纯色或移动地面。按当前版本 [PX4 光流坐标约定](https://docs.px4.io/v1.17/en/sensor/optical_flow) 检查方向：机体前移对应积分光流 +Y，右移对应 -X；这些是旋转约定一致后的积分光流轴，不是局部位置或速度轴。反向与左右混淆时先核查安装与坐标变换，不能凭漂移方向随意改符号。

### 估计器层

确认测距有效、光流质量满足阈值、时间信息连续、EKF 实际采用相关观测，位置/速度估计有效且能进入所需 Position 模式。必要时查看机载 ULog 中的估计状态、创新和融合标志，而不仅是消息是否出现。参见 [EKF2 光流使用条件](https://docs.px4.io/v1.17/en/advanced_config/tuning_the_ecl_ekf#optical-flow)。

仅看到光流消息、仅有高度，或地面站能切换到某个模式，都不能代替飞行前的完整验证。全部通过后再执行 [首飞步骤](04-validation-troubleshooting.md)。
