# Huawei Atlas200 Development Kit tutorials

## Introduction
The atlas200dk is ARM based, it runs a copy of ubuntu server in it. It is mainly used to accelerate AI tasks, so it is recommended to separate other backend tasks such as administration, database, etc to a separate backend server. Similar to other single board computers (SBC), the atlas200dk boots from a sd card.

The host machine needs Ubuntu1804 because [Mind Studio](https://www.huaweicloud.com/intl/en-us/ascend/resources/tools/mindstudio/detail) only works on ubuntu at the time of writing this.

The atlas200dk does not have a wifi or bluetooth interface, so only possible connection is through LAN and type-c USB. Main input power is from the 12v barrel jack. An at least 36W power supply is required.

For more hardware specs, refer [here](https://e.huawei.com/en/products/cloud-computing-dc/atlas/atlas-200/).

## Peripherals
### Gigabit Ethernet
The Atlas 200 DK provides an external 10/100/1000M Base-T interface, using the RJ45 connector and connecting to a network with a common network cable.

### USB Type-C
The Atlas 200 DK provides a Type-C USB port that is compatible with USB 3.0 (SuperSpeed), USB 2.0 (HighSpeed), and USB 1.1 (FullSpeed) communication protocols. This port can be used only in device mode and does not support the master mode. It is used to connect to the debugging host for loading and debugging.

### MicroSD card slot
The Atlas 200 DK provides a MicroSD card port that supports SD 3.0 and is backward compatible with SD 2.0. You are advised to use a standard MicroSD card of SD 3.0. The minimum capacity is 8 GB and the maximum capacity is 2 TB.

### MIPI-CSI
The Atlas 200 DK has two MIPI-CSI interfaces, and the definitions of the two interfaces are the same.

### 40 pin GPIO
Pin | Name | Level | Pin | Name | Level
--- | ---- | ----- | --- | ---- | -----
1 | +3.3V | 3.3V | 2 | +5V | 5V
3 | I2C_2-SDA | 3.3V | 4 | +5V | 5V
5 | I2C_2-SCL | 3.3V | 6 | GND | -
7 | GPIO0 | 3.3V | 8 | UART_0-TX | 3.3V
9 | GND | - | 10 | UART_0-RX | 3.3V
11 | GPIO1 | 3.3V | 12 | NC | -
13 | NC | - | 14 | GND | -
15 | GPIO2 | 3.3V | 16 | UART_1-TX | 3.3V
17 | +3.3V | 3.3V | 18 | UART_1-RX | 3.3V
19 | SPI-MOSI | 3.3V | 20 | GND | -
21 | SPI-MISO | 3.3V | 22 | NC | -
23 | SPI-CLK | 3.3V | 24 | SPI-CS | 3.3V
25 | GND | - | 26 | GPIO10 | 3.3V
27 | GPIO8 | 3.3V | 28 | GPIO9 | 3.3V
29 | GPIO3 | 3.3V | 30 | GND | -
31 | GPIO4 | 3.3V | 32 | NC | -
33 | GPIO5 | 3.3V | 34 | GND | -
35 | GPIO6 | 3.3V | 36 | +1.8V | 1.8V
37 | GPIO7 | 3.3V | 38 | UART_Hi35559-TXD | 3.3V
39 | GND | - | 40 | UART_Hi35559-RXD | 3.3V
#### UART
UART 0 is an 8-pin or 10-pin interface used for the default debugging serial port (console) of the Ascend 310. The baud rate is 115200 bit/s.
UART 1 is a 16-pin or 18-pin interface used for expansion and communication with other modules.
UART-Hi3559 has 38 TX pins and 40 RX pins, which are used for the MIPI-CSI interface to access the Hi3559 chip for debugging. The baud rate is 115200 bit/s.

#### SPI
This 4-wire SPI interface only support master mode.

#### I2C
Only I2C_2 can be used, I2C_1 is intra-board interface. Maximum rate is 400kHz.

#### GPIO
As output pins, GPIO0, GPIO1, and GPIO2 must be connected to external pull- up resistors to increase driving capabilities. It is recommended that the value of pull-up resistance is 1–10 kilohm.

## Setup
Latest SDK version at the time writing this is 20.1.0

### Prerequisite
A computer/vm with Ubuntu18.04.
SD card (>=32GB recommended).
Huawei Atlas200 development kit.

### Ubuntu environment preparation
Ubuntu18.04.04 or Ubuntu18.04.05 is required. Install it in a VM (recommended) or dual boot your computer if you do not have it.

### SD Card preparation
Get tools from [here](https://github.com/Huawei-Ascend/tools).
#### Files Required
File | URL
---- | ---
make_sd_card.py | https://github.com/Huawei-Ascend/tools/blob/master/makesd/for_20.1/make_sd_card.py
make_ubuntu_sd.sh | https://github.com/Huawei-Ascend/tools/blob/master/makesd/for_20.1/make_ubuntu_sd.sh
ubuntu-18.04.5-server-arm64.iso | https://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.5-server-arm64.iso
A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/atlas200dk/20.1/A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz
Ascend-cann-minirc_20.1.rc1_ubuntu18.04-aarch64.zip | https://www.huaweicloud.com/ascend/cann-download

#### Make SD Card
Download files listed above and put them in a directory
```
sudo chmod 755 make_sd_card.py make_ubuntu_sd.sh ubuntu-18.04.4-server-arm64.iso # give required previliges to execute file
sudo python3 make_sd_card.py local /dev/sdb # change /dev/sdb to your sd card location, you can check with fdisk -l
```
In this process, the script will help you install other dependencies, it might prompt you to answer 'y'.

Wait about 10 minutes, `Make SD Card successfully!` means you successfully flashed the ubuntu server OS into the SD card. Plug it into the atlas200dk and you are ready to go.

### Mind Studio setup



