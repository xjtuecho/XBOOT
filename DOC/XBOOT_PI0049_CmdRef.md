# XBOOT_PI0049 Command Line Reference

XBOOT是一款TI C2000平台的bootloader软件，配合USBTTL，USBCAN等硬件，可以实现
C2000系列DSP固件IAP功能。

本文档适用于PI0049开发板上的XBOOT_PI0049，DSP型号：TMS320F280049.

串口参数：`波特率115200、8位数据、1位停止、无校验、无流控`。

本文档基于XBOOT_PI0049固件v19.6.20，其余固件版本仅供参考。

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

FLASH查空命令。

命令格式：`empty [0|1] Check flash sector is empty or not.`。

带一个参数，0表示Bank0，1表示Bank1，不带参数默认Bank0.

```
empty
 Check Flash Bank0 ...
 Sector 0x00 @ 0x080000 is not empty, addr=0x080000 word=0x0048
 Sector 0x01 @ 0x081000 is not empty, addr=0x081000 word=0xA2A9
 Sector 0x02 @ 0x082000 is not empty, addr=0x082000 word=0x0020
 Sector 0x03 @ 0x083000 is not empty, addr=0x0831F0 word=0x4000
 Sector 0x04 @ 0x084000 is empty.
 Sector 0x05 @ 0x085000 is empty.
 Sector 0x06 @ 0x086000 is empty.
 Sector 0x07 @ 0x087000 is empty.
 Sector 0x08 @ 0x088000 is empty.
 Sector 0x09 @ 0x089000 is empty.
 Sector 0x09 @ 0x089000 is empty.
 Sector 0x0B @ 0x08B000 is empty.
 Sector 0x0C @ 0x08C000 is empty.
 Sector 0x0D @ 0x08D000 is empty.
 Sector 0x0E @ 0x08E000 is empty.
 Sector 0x0F @ 0x08F000 is empty.
empty 1
 Check Flash Bank1 ...
 Sector 0x10 @ 0x090000 is empty.
 Sector 0x11 @ 0x091000 is empty.
 Sector 0x12 @ 0x092000 is empty.
 Sector 0x13 @ 0x093000 is empty.
 Sector 0x14 @ 0x094000 is empty.
 Sector 0x15 @ 0x095000 is empty.
 Sector 0x16 @ 0x096000 is empty.
 Sector 0x17 @ 0x097000 is empty.
 Sector 0x18 @ 0x098000 is empty.
 Sector 0x19 @ 0x099000 is empty.
 Sector 0x1A @ 0x09A000 is empty.
 Sector 0x1B @ 0x09B000 is empty.
 Sector 0x1C @ 0x09C000 is empty.
 Sector 0x1D @ 0x09D000 is empty.
 Sector 0x1E @ 0x09E000 is empty.
 Sector 0x1F @ 0x09F000 is empty.
```

**注意**：C2000系列DSP的FLASH最小擦除单位是一个扇区，写入之前必须确保为空，否则需要先擦除。


## erase 

FLASH擦除命令。

命令格式：`erase [start] [stop] Erase flash sectors`。

两个参数分别为起始扇区和结束扇区，可以使用`empty`命令检查扇区编号。

如果只擦除一个可不包括结束扇区。

扇区编号为16进制，Bank0编号0x00-0x0F，Bank1编号0x10-0x1F，命令不需要0x前缀。

XBOOT自身占据0-3扇区，擦除0-3扇区以后，XBOOT仍然可以运行并且更新程序，如果掉电则器件变为空器件，需要使用其它方式写入XBOOT

举例如下：

- 擦除第5、6两个扇区：`erase 5 6`。
- 擦除第7个扇区：`erase 7`。
- 擦除Bank1的第一个扇区：`erase 10`
- 擦除XBOOT自身：`erase 0 3`

**注意**：FLASH编程之前需要确保为空，将用户代码写入非空的FLASH运行结果将是不确定的。

## ymodem

使用ymodem协议更新用户固件。

执行ymodem命令，超级终端显示字符`C`以后，选择超级中断菜单`发送->发送文件`，文件名选择
用户固件的hex文件，协议选择Ymodem，点击发送即可。

```
ymodem
 ymodem update firmware, press A to abort ...
CCCCC
 Update firmware OK!
 File Name: XBOOT_280049.hex
 File Size: 45669
  End Addr:  0x082500
```

执行ymodem命令以后，超级终端显示`C`时，可以按字母`a`取消命令。

用户发送的APP固件仅支持intel hex格式，**不能使用扇区0-3**，否则将会损坏XBOOT固件。

## memrd

读取DSP内存。内存地址包括SRAM、FLASH、地址。

命令格式：`memrd [hex addr] [hex len] Show Memory.`。

memrd命令接受两个参数，第一个为要读取的内存地址，第二个为要读取的数据长度，
默认128。全部为十六进制。
 
```
memrd 80000

0x080000 0048 24B6 FFFF FFFF 0000 0020 0020 0020
0x080008 0020 0020 0020 0020 0020 0020 0028 0028
0x080010 0028 0028 0028 0020 0020 0020 0020 0020
0x080018 0020 0020 0020 0020 0020 0020 0020 0020
0x080020 0020 0020 0020 0020 0020 0088 0010 0010
0x080028 0010 0010 0010 0010 0010 0010 0010 0010
0x080030 0010 0010 0010 0010 0010 0044 0044 0044
0x080038 0044 0044 0044 0044 0044 0044 0044 0010
0x080040 0010 0010 0010 0010 0010 0010 0041 0041
0x080048 0041 0041 0041 0041 0001 0001 0001 0001
0x080050 0001 0001 0001 0001 0001 0001 0001 0001
0x080058 0001 0001 0001 0001 0001 0001 0001 0001
0x080060 0010 0010 0010 0010 0010 0010 0042 0042
0x080068 0042 0042 0042 0042 0002 0002 0002 0002
0x080070 0002 0002 0002 0002 0002 0002 0002 0002
0x080078 0002 0002 0002 0002 0002 0002 0002 0002
```

## entry

设置、查看用户程序入口点。

默认无参数查看当前用户程序入口点，带一个参数为设置用户程序入口点。
 
```
entry
 user app entry is 0x084000.
entry 86000
 set user app entry to 0x086000 .
entry
 user app entry is 0x086000.
```

## goto

跳转到新地址执行。

接受一个复位向量作为参数。
 
```
goto 80000
 goto address 0x080000 to execute...
 No user App found ...
 Run xboot shell ...
 XBOOT v19.06.20 for TMS320F280049 Rev:B SN:0x000A2C10
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
 XBOOT v19.06.20 for TMS320F280049 Rev:B SN:0x000A2C10
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
reboot 900
 rebooting ...
 No user App found ...
 Run xboot shell ...
 XBOOT v19.06.20 for TMS320F280049 Rev:B SN:0x000A2C10
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
```

## help

获取在线帮助。

命令输出如下：
```
help
 eeprom -> eeprom [load|save|read|write] [addr] [data] Operate Int. EEPROM.
 empty -> empty [0|1] Check flash sector is empty or not.
 erase -> erase [start] [stop] Erase flash sectors.
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
 XBOOT v19.06.20 for TMS320F280049 Rev:B SN:0x000A2C10
 ECHO Studio <echo.xjtu@gmail.com>. All Rights Reserved.
```
