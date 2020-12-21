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

### Mind Studio setup
Install dependencies
```
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install -y gcc g++ make cmake unzip zlib1g zlib1g-dev libsqlite3-dev openssl libssl-dev libffi-dev pciutils net-tools g++-5-aarch64-linux-gnu libblas-dev gfortran libblas3 libopenblas-dev software-properties-common
```
Update default python version
```sh
wget https://www.python.org/ftp/python/3.7.5/Python-3.7.5.tgz
tar -zxvf Python-3.7.5.tgz
cd Python-3.7.5
./configure --prefix=/usr/local/python3.7.5 --enable-shared
make -j8
sudo make install
# link new python
sudo cp /usr/local/python3.7.5/lib/libpython3.7m.so.1.0 /usr/lib
sudo ln -s /usr/local/python3.7.5/bin/python3 /usr/bin/python3.7
sudo ln -s /usr/local/python3.7.5/bin/pip3 /usr/bin/pip3.7
sudo ln -s /usr/local/python3.7.5/bin/python3 /usr/bin/python3.7.5
sudo ln -s /usr/local/python3.7.5/bin/pip3 /usr/bin/pip3.7.5
# Install dependencies
pip3.7.5 install attrs psutil decorator numpy protobuf==3.11.3 scipy sympy cffi grpcio grpcio-tools requests --user
```
Get required files (you might need to sign up for an account), it is recommended to use wget to download these files.
File | URL
---- | ---
Ascend-cann-toolkit_20.1.rc1_linux-aarch64.run | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/cann/V100R020C10/Ascend-cann-toolkit_20.1.rc1_linux-aarch64.run
Ascend-cann-toolkit_20.1.rc1_linux-x86_64.run | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/cann/V100R020C10/Ascend-cann-toolkit_20.1.rc1_linux-x86_64.run
A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/atlas200dk/20.1/A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz
MindStudio_2.0.0-beta2_ubuntu18.04-x86_64.tar.gz | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/mindstudio/2.0.0-beta2/MindStudio_2.0.0-beta2_ubuntu18.04-x86_64.tar.gz

Give required permissions and execute file
```sh
chmod 755 *.run
./Ascend-cann-toolkit_20.1.rc1_linux-aarch64.run --install
./Ascend-cann-toolkit_20.1.rc1_linux-x86_64.run --install
```
Move the file `A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz` into `$HOME/Ascend` directory. Install the media driver.
```
cd $HOME/Ascend
tar -zxvf A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz
```
Install MindStudio
``` sh
cd $HOME
tar -zxvf MindStudio_2.0.0-beta2_ubuntu18.04-x86_64.tar.gz
cd MindStudio-ubuntu/bin
./MindStudio.sh
```
There might be instructions for you to install some more dependencies, install them with the command provided and rerun the line.

After this, a window will pop out, choose do not import settings.

At toolkit version settings, choose the path toyour toolkit installed, which is `$HOME/Ascend/ascend-toolkit/20.1.rc1`.

Congratulations, you have successfully set up your environment for development.

### SD Card preparation
Get tools from [here](https://github.com/Huawei-Ascend/tools).
#### Files Required
File | URL
---- | ---
make_sd_card.py | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/atlas200dk/20.1/make_sd_card.py
make_ubuntu_sd.sh | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/atlas200dk/20.1/make_ubuntu_sd.sh
ubuntu-18.04.5-server-arm64.iso | https://cdimage.ubuntu.com/releases/18.04/release/ubuntu-18.04.5-server-arm64.iso
A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/atlas200dk/20.1/A200dk-npu-driver-20.1.0-ubuntu18.04-aarch64-minirc.tar.gz
Ascend-cann-minirc_20.1.rc1_ubuntu18.04-aarch64.zip | https://obs-9be7.obs.cn-east-2.myhuaweicloud.com/turing/resource/cann/V100R020C10/Ascend-cann-minirc_20.1.rc1_ubuntu18.04-aarch64.zip
#### Make SD Card
Download files listed above and put them in a directory.
A SD Card reader is required for this process. If you do not have one, follow instructions [here](https://support.huaweicloud.com/intl/en-us/usermanual-A200dk_3000/atlas200dk_02_0012.html) to place a jumper on the atlas200dk to make it into a SD Card reader.
```sh
pip3 install pyyaml
sudo apt-get install -y qemu-user-static binfmt-support python3-yaml gcc-aarch64-linux-gnu g++-aarch64-linux-gnu # Install dependencies
sudo chmod 755 make_sd_card.py make_ubuntu_sd.sh ubuntu-18.04.5-server-arm64.iso # give required previliges to execute file
sudo python3 make_sd_card.py local /dev/sdb # change /dev/sdb to your sd card location, you can check with fdisk -l
```
In this process, the script will help you install other dependencies, it might prompt you to answer 'y'.

Wait about 10 minutes, `Make SD Card successfully!` means you successfully flashed the ubuntu server OS into the SD card. Plug it into the atlas200dk and you are ready to go.

### Connect to Atlas200dk
There are two main ways to connect, through the usb type-c and through RJ45 LAN cable.

#### Connecting through USB
Connect the board to your pc with a usb type-c cable. If you are using a VM, you may need to manually connect the device (Huawei Ethernet Gadget) to the VM. Once connected, run
```sh
ifconfig
```
You will get an output similar to this
```
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.142  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::2882:37a5:4e8d:c700  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:dc:2c:b2  txqueuelen 1000  (Ethernet)
        RX packets 3966648  bytes 5866216386 (5.8 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1305635  bytes 111797852 (111.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens160u1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::f07d:6458:cc20:c4df  prefixlen 64  scopeid 0x20<link>
        ether fe:99:ea:f0:aa:22  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 12  bytes 2050 (2.0 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 19188  bytes 1750969 (1.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19188  bytes 1750969 (1.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
The first one(ens33) is your internet connection, the last one(lo) is local network, the centre one(ens160u1) is the atlas200dk. As you can see, it haven't got an IP address assigned to it, so we will assign one to it. To do this, we have to modify netplan. 

Modify `/etc/netplan/01-network-manager-all.yaml` as below with your favourite text editor. Note that .yaml files are indent-sensitive.
```yaml
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
     ens160u1: # replace with your network adapter name
       dhcp4: no # close dchp4
       addresses: [192.168.1.223/8] # Setup IP address and mask for device
       gateway4: 255.255.255.0 #设置网关
       nameservers:
         addresses: [114.114.114.114]
```
and then execute
```
sudo netplan apply
```
Run `ifconfig` again and you will see an IP address assigned to the atlas200dk.
```
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.142  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::2882:37a5:4e8d:c700  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:dc:2c:b2  txqueuelen 1000  (Ethernet)
        RX packets 3967650  bytes 5866333120 (5.8 GB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1306169  bytes 111864657 (111.8 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens160u1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.223  netmask 255.0.0.0  broadcast 192.255.255.255
        inet6 fe80::fc99:eaff:fef0:aa22  prefixlen 64  scopeid 0x20<link>
        ether fe:99:ea:f0:aa:22  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19  bytes 3910 (3.9 KB)
        TX errors 174  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 20731  bytes 1907482 (1.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 20731  bytes 1907482 (1.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
Now, you have successfully established a connection to the atlas200dk. Next, we need to ssh into the board to do some initial setup. Default password is `Mind@123`
```sh
ssh HwHiAiUser@192.168.1.2
```
Here, we need to add the path of `libascendcl.so` into `/etc/ld.so.conf.d/mind_so.conf`.
```sh
su root
echo "/home/HwHiAiUser/Ascend/acllib/lib64/libascendcl.so" >> /etc/ld.so.conf.d/mind_so.conf
ldconfig
exit
```
Then, we need to restart the `ada` service. Run
```sh
ps -ef | grep ada
```
and run (replace `<process id>` with the id of the first process in output of above command.)
```sh
kill <process id>
```
Then, restart the process with
```
/var/ada
```
Congratulations, you have completed hardware setup. Now, let's try a demo project.

### Demo project 1 (ResNet50 Classification)
To start MindStudio, run
```sh
sh ./MindStudio-ubuntu/bin/MindStudio.sh
```
Select `Create New Project` or `File -> New -> Project` if you have opened other projects before.

Select Ascend App on the left menu. Here, enter details of your project or leave it as it is. Click `Next`.

Select `Sample ResNet-50` and click `Finish`. Lets see what is in the sample project.
```s
MyApp
├── build # Here are the cmake build files. 
│   └── cmake
│       ├── CMakeCache.txt
│       ├── CMakeFiles
│       │   ├── 3.10.2
│       │   │   ├── CMakeCCompiler.cmake
│       │   │   ├── CMakeCXXCompiler.cmake
│       │   │   ├── CMakeDetermineCompilerABI_C.bin
│       │   │   ├── CMakeDetermineCompilerABI_CXX.bin
│       │   │   ├── CMakeSystem.cmake
│       │   │   ├── CompilerIdC
│       │   │   │   ├── a.out
│       │   │   │   ├── CMakeCCompilerId.c
│       │   │   │   └── tmp
│       │   │   └── CompilerIdCXX
│       │   │       ├── a.out
│       │   │       ├── CMakeCXXCompilerId.cpp
│       │   │       └── tmp
│       │   ├── cmake.check_cache
│       │   ├── CMakeDirectoryInformation.cmake
│       │   ├── CMakeOutput.log
│       │   ├── CMakeTmp
│       │   ├── feature_tests.bin
│       │   ├── feature_tests.c
│       │   ├── feature_tests.cxx
│       │   ├── Makefile2
│       │   ├── Makefile.cmake
│       │   ├── progress.marks
│       │   └── TargetDirectories.txt
│       ├── cmake_install.cmake
│       ├── compile_commands.json
│       ├── Makefile
│       └── src
│           ├── CMakeFiles
│           │   ├── CMakeDirectoryInformation.cmake
│           │   ├── main.dir
│           │   │   ├── build.make
│           │   │   ├── cmake_clean.cmake
│           │   │   ├── DependInfo.cmake
│           │   │   ├── depend.make
│           │   │   ├── flags.make
│           │   │   ├── link.txt
│           │   │   └── progress.make
│           │   └── progress.marks
│           ├── cmake_install.cmake
│           └── Makefile
├── CMakeLists.txt
├── data # Here is our input data, two images
│   ├── dog1_1024_683.jpg
│   └── dog2_1024_683.jpg
├── inc # Here are the model processing, init and deinit header files.
│   ├── model_process.h
│   ├── sample_process.h
│   └── utils.h
├── MyApp.iml
├── out
├── script 
│   └── transferPic.py # This is a script to covert .jpg images to .bin binary files, and perform resize..
└── src
    ├── acl.json # System init configs
    ├── CMakeLists.txt
    ├── main.cpp # Main function file where the classification task (model inference) is defined
    ├── model_process.cpp # Model processing functions
    ├── sample_process.cpp # Data init and deinit
    └── utils.cpp # common utils
```
Now, we need to download the model definitions and weights.
```sh
mkdir caffe_model && cd caffe_model
wget https://obs-model-ascend.obs.cn-east-2.myhuaweicloud.com/resnet50/resnet50.caffemodel
wget https://obs-model-ascend.obs.cn-east-2.myhuaweicloud.com/resnet50/resnet50.prototxt
```
Now, we need to convert the caffe models to `.om` format for the atlas200dk. On the top menu bar, select `Ascend -> Model Converter` and enter the file path. Other parameters will be loaded automatically based on the model file.

Choose `FP16` for data type and click `Next`. Data preprocessing is not required in this demo, so you can disable it be switching the slider. For the advanced options, we will leave it as it is for this demo. Click `Finish`. The output tab below will print out some logs. 
```sh
2020-12-21 00:20:17  Model converted successfully.
``` 
Something similar to this means a successful conversion. Now, we need to add the model into our project.

Right click your project name, `MyApp` in my case on the left menu and select `Add Model`. The converted model can be found in `$HOME/modelzoo`, which is `$HOME/modelzoo/resnet50/device/resnet50.om` for my case. Select the model file and click `Ok`. You can see the model file in the `model` directory in your project directory now.

Next, we need to convert the two input images located in the `data` directory into .bin format and resize it. The script `transferPic.py` in the `script` directory will do this for us.

Open a terminal in the `data` directory and run
```sh
python3.7.5 ../script/transferPic.py
```
After this, you will see two `.bin` files created in the `data` directory.

Now, its time to build the project. Go to `Build -> Build Configurations` in the top menu. Change the Target Architecture to `aarch64`. Then, click `Build`.

After building, you will see a `main` file generated in the `out` directory.

Next, connect the atlas200dk to MindStudio. Please make sure you have already setup Netplan and can successfully ssh into the board.

In the top menu, select `Ascend -> Device Manager`. Click the plus button on the top-right side of the popup window. Key in the IP address of the atlas200dk, which is `192.168.1.2` by default and click `Ok`. Now, you should see your device connected.

Click `Ok` to exit device manager. Then, select `Run -> Run 'MyApp'` to execute the project on the atlas200dk. A window will popup to confirm the configurations, click `Ok`. You can also run by clicking the green play button on the top menu.

You will get an output similar to this
```sh
[INFO]  acl init success
[INFO]  open device 0 success
[INFO]  create context success
[INFO]  create stream success
[INFO]  get run mode success
[INFO]  load model ../model/resnet50.om success
[INFO]  create model description success
[INFO]  create model output success
[INFO]  start to process file:../data/dog1_1024_683.bin
[INFO]  model execute success
[INFO]  top 1: index[161] value[0.767578] #Here are the results
[INFO]  top 2: index[162] value[0.154785] #Here are the results
[INFO]  top 3: index[167] value[0.038513] #Here are the results
[INFO]  top 4: index[163] value[0.021606] #Here are the results
[INFO]  top 5: index[166] value[0.011658] #Here are the results
[INFO]  output data success
[INFO]  start to process file:../data/dog2_1024_683.bin
[INFO]  model execute success
[INFO]  top 1: index[267] value[0.936035] #Here are the results
[INFO]  top 2: index[266] value[0.041138] #Here are the results
[INFO]  top 3: index[265] value[0.018845] #Here are the results
[INFO]  top 4: index[219] value[0.002609] #Here are the results
[INFO]  top 5: index[160] value[0.000295] #Here are the results
[INFO]  output data success
[INFO]  unload model success, modelId is 1
[INFO]  execute sample success
[INFO]  end to destroy stream
[INFO]  end to destroy context
[INFO]  end to reset device is 0
[INFO]  end to finalize acl
```
Congratulations, you have completed your first project.