# XBOOT_PI035 Command Line Reference

XBOOT是一款TI C2000平台的bootloader软件，配合USBTTL，USBCAN等硬件，可以实现
C2000系列DSP固件IAP功能。

本文档适用于PI035开发板上的XBOOT_PI035，DSP型号：TMS320F28035.

串口参数：`波特率115200、8位数据、1位停止、无校验、无流控`。

本文档基于XBOOT_PI035固件v19.6.20，其余固件版本仅供参考。

## eeprom

查看、设置内部模拟EEPROM内容。

命令格式：`eeprom [load|save|read|write] [addr] [data] Operate Int. EEPROM.`

### eeprom load

加载eeprom：`eeprom load`，数据从FLASH加载到内部缓存。

### eeprom save

存储eeprom：`eeprom save`，eeprom数据从内部缓存写入FLASH，掉电保存。

### eeprom read

读eeprom：`eeprom read [地址] [长度]`，其中地址为必要参数，长度可不填，默认128字，从内部缓存读取数据。

读取EEPROM前64个字内容:

```
eeprom read 0 40

0x0000  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0008  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0010  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0018  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0020  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0028  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0030  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0038  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
```

### eeprom write

写eeprom：`eeprom write [地址] [数据]`，其中地址和数据均为必要参数，数据写入内部缓存。

将0xABCD写入EEPROM地址0x20:

```
eeprom write 20 abcd
 write 0xABCD to eeprom address 0x0020 ...
eeprom read 0 40

0x0000  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0008  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0010  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0018  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0020  ABCD FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0028  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0030  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
0x0038  FFFF FFFF FFFF FFFF FFFF FFFF FFFF FFFF
```
## empty

FLASH查空命令，无参数，用来检测用户FLASH是否为空。

```
empty
 FLASHA is NOT empty @ 0x3F6000.
 FLASHB is NOT empty @ 0x3F4000.
 FLASHC is empty @ 0x3F2000.
 FLASHD is empty @ 0x3F0000.
 FLASHE is empty @ 0x3EE000.
 FLASHF is empty @ 0x3EC000.
 FLASHG is empty @ 0x3EA000.
 FLASHH is empty @ 0x3E8000.
```

**注意**：C2000系列DSP的FLASH最小擦除单位为一个扇区，写入之前必须确保为空，否则需要先擦除。

## erase 

FLASH擦除命令。

命令格式：`erase -> erase [abcdefgh] Erase flash sector.`。

`erase`命令接受一个字符串参数：`abcdefgh`，分别对应8个FLASH扇区。可以同时指定多个FLASH扇区，
如`erase bcd`命令将会擦除`b c d`三个flash扇区。

```
empty
 FLASHA is NOT empty @ 0x3F6000.
 FLASHB is NOT empty @ 0x3F4000.
 FLASHC is NOT empty @ 0x3F2000.
 FLASHD is empty @ 0x3F0000.
 FLASHE is empty @ 0x3EE000.
 FLASHF is empty @ 0x3EC000.
 FLASHG is empty @ 0x3EA000.
 FLASHH is empty @ 0x3E8000.
erase c
 erase flash sector 0x04 OK.
empty
 FLASHA is NOT empty @ 0x3F6000.
 FLASHB is NOT empty @ 0x3F4000.
 FLASHC is empty @ 0x3F2000.
 FLASHD is empty @ 0x3F0000.
 FLASHE is empty @ 0x3EE000.
 FLASHF is empty @ 0x3EC000.
 FLASHG is empty @ 0x3EA000.
 FLASHH is empty @ 0x3E8000.
```

XBOOT自身占据a扇区，b扇区为模拟EEPROM。`erase a`命令将擦除包括XBOOT在内所有FLASH内容。
擦除所有FLASH内容以后掉电之前，XBOOT仍然可以运行并且更新程序，如果擦除以后掉电，只能使其它方式写入XBOOT。

**注意**：将用户代码写入未擦除的FLASH运行结果将是不确定的。

## ymodem

使用ymodem协议更新用户固件。

执行ymodem命令，超级终端显示字符`C`以后，选择超级中断菜单`发送->发送文件`，文件名选择
用户固件的hex文件，协议选择Ymodem，点击发送即可。

```
ymodem
 ymodem update firmware, press A to abort ...
 CCCCCCCCCCCC
  Update firmware OK!
  File Name: F28035APP.hex
  File Size: 34555
  End Addr:  0x3F7FF8
```

执行ymodem命令以后，超级终端显示`C`时，可以按字母`a`取消命令。

用户发送的APP固件仅支持intel hex格式，**不能使用扇区AB**，否则将会损坏XBOOT固件。

## memrd

读取DSP内存。内存地址包括SRAM、FLASH、地址。

命令格式：`memrd [hex addr] [hex len] Show Memory.`。

memrd命令接受两个参数，第一个为要读取的内存地址，第二个为要读取的数据长度，
默认128。全部为十六进制。
 
```
memrd 3f6000

0x3F6000 0000 0020 0020 0020 0020 0020 0020 0020
0x3F6008 0020 0020 0028 0028 0028 0028 0028 0020
0x3F6010 0020 0020 0020 0020 0020 0020 0020 0020
0x3F6018 0020 0020 0020 0020 0020 0020 0020 0020
0x3F6020 0020 0088 0010 0010 0010 0010 0010 0010
0x3F6028 0010 0010 0010 0010 0010 0010 0010 0010
0x3F6030 0010 0044 0044 0044 0044 0044 0044 0044
0x3F6038 0044 0044 0044 0010 0010 0010 0010 0010
0x3F6040 0010 0010 0041 0041 0041 0041 0041 0041
0x3F6048 0001 0001 0001 0001 0001 0001 0001 0001
0x3F6050 0001 0001 0001 0001 0001 0001 0001 0001
0x3F6058 0001 0001 0001 0001 0010 0010 0010 0010
0x3F6060 0010 0010 0042 0042 0042 0042 0042 0042
0x3F6068 0002 0002 0002 0002 0002 0002 0002 0002
0x3F6070 0002 0002 0002 0002 0002 0002 0002 0002
0x3F6078 0002 0002 0002 0002 0010 0010 0010 0010
```

## entry

设置、查看用户程序入口点。

默认无参数查看当前用户程序入口点，带一个参数为设置用户程序入口点。
 
```
entry
 user app entry is 0x3F2000.
entry 3f0000
 set user app entry to 0x3F0000 .
entry
 user app entry is 0x3F0000.
```

## goto

跳转到新地址执行。

接受一个复位向量作为参数。
 
```
goto 3f6000
 goto address 0x3F6000 to execute...
 No user App found ...
 Run xboot shell ...
 XBOOT_28035 v19.06.20 SN:DD843CA0DD094FD9AC6AFE870D8DBFBD
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
```

## reboot

重启系统。

可以带一个延时参数，单位ms，如`reboot 900`延时900ms以后重启。

命令输出如下：
```
reboot
 rebooting ...
 No user App found ...
 Run xboot shell ...
 XBOOT_28035 v19.06.20 SN:DD843CA0DD094FD9AC6AFE870D8DBFBD
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
reboot 900
 rebooting ...
 No user App found ...
 Run xboot shell ...
 XBOOT_28035 v19.06.20 SN:DD843CA0DD094FD9AC6AFE870D8DBFBD
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
```

## help

获取在线帮助。

命令输出如下：

```
 eeprom -> eeprom [load|save|read|write] [addr] [data] Operate Int. EEPROM.
 empty -> empty Check flash sector is empty or not.
 erase -> erase [abcdefgh] Erase flash sector.
 ymodem -> update user APPs.
 memrd -> memrd [hex addr] [hex len] Show Memory.
 entry -> entry {hex addr} Get or Set user app entry.
 goto -> goto [hex addr] to execute.
 reboot -> reboot [delay ms] Restart xboot.
 help -> help Info.
 version -> version display bootloader Info.
```

## version

获取固件和设备序列号等信息。

命令输出如下：
```
version
 XBOOT_28035 v19.06.20 SN:DD843CA0DD094FD9AC6AFE870D8DBFBD
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
```
