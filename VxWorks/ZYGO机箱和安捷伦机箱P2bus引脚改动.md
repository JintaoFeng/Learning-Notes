# ZYGO机箱和安捷伦机箱P2bus引脚改动

A

| Pin  | ZYGO     | 安捷伦   |
| ---- | -------- | -------- |
| 2    | GND      | A5       |
| 5    | P2_A02   | __2      |
| 11   | GND      | AxisX_P2 |
| 23   | P2_D00   | GND      |
| 31   | GND      | A2       |
| 32   | P2_SCLK0 | GND      |

C

| Pin  | ZYGO     | 安捷伦     |
| ---- | -------- | ---------- |
| 1    | P2_SCLK2 | GND        |
| 3    | P2_A08   | GND        |
| 10   | reserved | GND        |
| 11   | GND      | AxisX_P1 3 |
| 14   | GND      | AxisX_P7   |
| 19   | P2_D28   | GND        |
| 28   | P2_D13   | GND        |
| 31   | GND      | A1         |
|      |          |            |

机箱背板中的拨码开关，ON为GND，OFF为引脚功能。