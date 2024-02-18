# ST-Link V2.1

# 0 简介

ST-Link V2.1使用STM32F103C8T6/CBT6作为主控，二者可以Pin to Pin替换，CBT6的容量更大，可以满足后续固件升级需要，同时该调试器板载虚拟串口。

ST-Link V2.1可以通过HDR 2.54 2x5P接口进行SWD或者JTAG调试。同时附带一个8P的FPC0.5mm接口，可以在板面积紧张时使用该接口进行调试，但该接口仅支持SWD调试。

![image-20240112112412949](https://raw.githubusercontent.com/MaxDYi/PicGo/main/202401121124046.png)

调试器下方的HDR 1.27 4P接口为烧录固件的接口，无需焊接。

调试器中板载一颗红色ST-LINK COM LED，该LED状态指示如下：

| LED状态                 | ST-LINK运行状态                         |
| ----------------------- | --------------------------------------- |
| LED呈红色闪烁（第一次） | 正在与PC进行第一个USB枚举               |
| LED为红色               | PC与ST-LINK之间的通信已建立（枚举结束） |
| LED呈红色闪烁           | 正在目标和PC之间交换数据                |

调试器所有接口均为3.3V电平，USB输入5V使用6V100mA的自恢复保险丝进行保护。板载3.3V使用XC6206，也带有6V100mA的自恢复保险丝，共可以输出100mA。

# 1 原理图

![ST-Link-V2.1-SCH_page-0001](https://raw.githubusercontent.com/MaxDYi/PicGo/main/202401121103423.jpg)

# 2 PCB

![image-20240112112532149](https://raw.githubusercontent.com/MaxDYi/PicGo/main/202401121125205.png)

![image-20240112112536970](https://raw.githubusercontent.com/MaxDYi/PicGo/main/202401121125024.png)

PCB使用双层板设计，面积4.0x1.5cm，仅正面布有元器件。

PCB使用1.6板厚，若使用2.0板厚时HDR 2x5P接口卡合会更紧，但工程费会更贵。

# 3 物料清单

| Comment          | Description                 | Designator                  | Footprint             | LibRef              | Quantity |
| ---------------- | --------------------------- | --------------------------- | --------------------- | ------------------- | -------- |
| 20pF             | 贴片电容/MLCC电容           | C1, C2                      | C0402_M               | C                   | 2        |
| 100nF            | 贴片电容/MLCC电容           | C3, C4, C5, C6, C7,  C8, C9 | C0402_M               | C                   | 7        |
| 1uF              | 贴片电容/MLCC电容           | C10, C11                    | C0402_M               | C                   | 2        |
| 1N5819           | 肖特基二极管                | D1                          | SOD-323               | 1N5819              | 1        |
| 6V 100mA         | 方形保险丝                  | F1, F2                      | F0805                 | FUSE-8.5*4.0*8.0    | 2        |
| HDR-1X5          | 5P接插件                    | JP1, JP2                    | HDR2.54-WS-5P         | Header 5            | 2        |
| HDR-1X4          | 4P接插件                    | JP3                         | HDR1.27-LI-4P         | Header 4            | 1        |
| CON-8P           | 8P连接器                    | JP4                         | FPC0.5-WT-8P-双侧触点 | Header 8            | 1        |
| RED              | 贴片LED                     | LED1, LED5                  | LED0603_R             | LED                 | 2        |
| GREEN            | 贴片LED                     | LED3                        | LED0603_G             | LED                 | 1        |
| BLUE             | 贴片LED                     | LED4                        | LED0603_B             | LED                 | 1        |
| S9013            | 低频放大-NPN型              | Q1                          | SOT23-3N              | 9013-SMD            | 1        |
| 10K              | 贴片电阻                    | R1, R2, R3, R12, R16        | R0402_M               | R                   | 5        |
| 4K7              | 贴片电阻                    | R4, R10, R11                | R0402_M               | R                   | 3        |
| 100R             | 贴片电阻                    | R5, R13                     | R0402_M               | R                   | 2        |
| 1K               | 贴片电阻                    | R6, R18, R19, R20           | R0402_M               | R                   | 4        |
| 100K             | 贴片电阻                    | R9                          | R0402_M               | R                   | 1        |
| 10R              | 贴片电阻                    | R14, R15                    | R0402_M               | R                   | 2        |
| 1K5              | 贴片电阻                    | R17                         | R0402_M               | R                   | 1        |
| 5.1K             | 贴片电阻                    | R21, R22                    | R0402_M               | R                   | 2        |
| STM32F103CBT6    | STM32 ARM-based  32-bit MCU | U1                          | LQFP48_N              | STM32F103CBT6       | 1        |
| XC6206P332MR     | LDO 3.3V/0.25A              | U2                          | SOT23-3N              | XC6206P332MR        | 1        |
| TYPE-C 2.0  -16P | USB2.0_Type-C_16Pin母座     | USBC1                       | USB-C-SMD_16P         | USB2.0_Type-C_16Pin | 1        |
| 8MHz             | 2脚无源晶振(谐振器）        | X1                          | OSC-5032-2P           | XTAL-2P             | 1        |

# 4 固件烧录

固件烧录时需要先使用一个ST-LINK进行固件烧录，固件有**STLinkV2.J16.S4.bin**与**STLinkV2.J28.M18.bin**（推荐）两版。

使用[STM32CubeProgrammer](https://www.st.com/zh/development-tools/stm32cubeprog.html)进行烧录，如提示**Failed to read memory**，表示目前芯片有写保护，在配置寄存器中解除保护后再烧写固件即可。

烧录完成后使用USB连接调试器，使用[STSW-LINK007](https://www.st.com/zh/development-tools/stsw-link007.html)工具进行固件升级，升级类型可以选择。

