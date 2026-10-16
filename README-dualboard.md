# 双板联通包（A 发 B 收）使用说明

> 内容：修好的 PL（`sdr_ai_z7_lvds.bit`，含 AXI 时钟复位补线）＋ 裸机 elf（初始化＋RX DMA 抓数＋64 点打印）＋ `ps7_init.tcl` ＋ `src/` 参考源码。两块板跑**同一个**包，不用区分版本。

## 一、硬件准备（两块板都要）

1. 跳线 `J4/J5` 拨到 `0-0`（靠板边，JTAG 档，断电拨）；
2. 四个 SMA 口全插上天线（重点：A 板 `Tx1A`、B 板 `Rx1A`），两板拉开 1~2 米；
3. 接 JTAG（J2）＋串口（J10）到电脑，上电，记下串口号。两块板默认都是 2.4GHz／30.72MSPS／2R2T，直接对得上。

## 二、跑起来（每块板都一样，XSDB 命令行，不用建工程）

确认装了 Xilinx SDK/Vivado 2019.1（任意一台装了的电脑就行），把本包四个文件放同一目录，逐行敲（等每行跑完再敲下一行）：

```
xsdb
connect
source ps7_init.tcl
ps7_init
fpga -file sdr_ai_z7_lvds.bit
ps7_post_config
targets 2
dow ad9361_noos.elf
con
```

DONE 灯亮＋串口（115200）看到 `MAIN: start` → 跑完出现 `DMA: samp[0..63]` 64 行＋`Done.` 即正常。
A 板跑起来后不用管（一直在发 3MHz 单音），去跑 B 板，把 B 板的 64 行贴回来对比。

## 三、怎么判通没通

- B 板单跑（A 断电）：64 点是满量程乱跳噪声＝本底，正常；
- A、B 双开再跑 B：若出现约 10 点一周期的平滑正弦爬坡＝链路通了；
- 若两次都是噪声：检查天线/距离/频段干扰（2.4GHz 有 WiFi），或联系发包人。
