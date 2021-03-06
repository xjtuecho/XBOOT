# UIDiskC2000用户手册

## 简介

UIDisk是一款DSP固件离线烧写工具，它内置一个小容量(4~16MB)优盘，用户可先将要烧写的固件存入内置优盘，然后用优盘(UIDisk)来离线更新DSP固件，更新过程无需使用电脑。

## 将DSP固件存入UIDisk

- **按住UIDisk上的按键**将UIDisk插入电脑，电脑识别USB设备以后松开按键，进入优盘模式。
- 将要烧写的固件hex文件拷贝到内置优盘，重命名为`C2000.HEX`。
- 在电脑上删除并拔下优盘(UIDisk)。

## 使用UIDisk更新DSP固件

- 主控板断电，连接UIDisk与主控板的TTL接口。
- VCC和GND直接连接，TXD和RXD需交叉，UIDisk通过TTL接口从主控板供电，无需单独电源。
- 主控板上电，等待约1分钟，自动完成烧写，主控板断电，拔下UIDisk。

## 指示灯状态

- 烧写过程中UIDisk的LED快速闪烁，主控板的LED常亮或者常灭。
- 烧写完成后主控板的LED开始闪烁(周期约1秒)，UIDisk的LED慢速闪烁(周期约2秒)。

## 配置文件

- 配置文件在内置优盘名称：`CONFIG.INI`，用户可编辑。
- 第一行内容为：`DSP_TYPE=28335`，指明DSP型号，目前支持`28335`和`28035`，默认`28335`。
- 第二行内容为：`HEX_FILE=C2000.HEX`，指明要烧写的固件名，默认`C2000.HEX`，只支持英文`8.3`格式，扩展名为`.HEX`，文件名不超过8个字符。
