# Simplessd-MMIO-BAR配置

## 初步探索BAR


pcidevice.py 对BAR初始化，写入BARsize的初始值

    pcidevice.py：line74

<center>

<img src="./images/屏幕截图 2021-02-08 182301.png" width="100%" height="65%" />

PciDevice.py 的初始值（可修改）

</center
device.cc通过构造函数初始化

    device.cc：line102


<center>

<img src="./images/屏幕截图 2021-02-08 183045.png" width="100%" height="65%" />

为BAR初始值添加打印
</center

device.cc中的PciDevice狗欧早函数一共被调用三次，部分BAR的初始值受到PciDevice.py的影响。

通过gdb给    device.cc：line102打断点，获得三次输出结果和调用

**第一次**

    The Size of BAR0 is 8192 
    The Size of BAR1 is 32 
    The Size of BAR2 is 128 
    The Size of BAR3 is 128 
    The Size of BAR4 is 8192 
    The Size of BAR5 is 4096 
    BAR0，BAR4-5与初始值不匹配

- backtrace结果：



<center>

<img src="./images/屏幕截图 2021-02-08 183218.png" width="100%" height="100%" />

第一次调用device.cc中的PciDevice构造函数，仅显示调用栈前五行
</center>

**第二次**

    The Size of BAR0 is 8 
    The Size of BAR1 is 3 
    The Size of BAR2 is 8 
    The Size of BAR3 is 3 
    The Size of BAR4 is 16 
    The Size of BAR5 is 128
    BAR0-5均与初始值不同

- backtrace结果：

<center>
<img src="./images/屏幕截图 2021-02-08 183704.png" width="100%" height="100%" />

第二次调用device.cc中的PciDevice构造函数，仅显示调用栈前五行
</center>

**第三次**

    The Size of BAR0 is 128 
    The Size of BAR1 is 32 
    The Size of BAR2 is 128 
    The Size of BAR3 is 128 
    The Size of BAR4 is 128 
    The Size of BAR5 is 1024 
    BAR5与初始值不同
- backtrace结果：


<center>

<img src="./images/屏幕截图 2021-02-08 184024.png" width="100%" height="100%" />

第三次调用device.cc中的PciDevice构造函数，仅显示调用栈前五行
</center>

### 定位到初始化文件
NVME接口的BAR初始化文件在：src/dev/storage/NVMe.py

### 64-bit MMIO配置探索

- 32-bit prefetchable memory和I/O address space BAR仅适用于Legacy Endpoint[链接](https://www.intel.cn/content/www/cn/zh/programmable/documentation/lbl1415138844137.html)，  
因此，legacyIO=0

- <img src="./images/屏幕截图 2021-02-11 102814.png" width="100%" height="100%" />

- <img src="./images/屏幕截图 2021-02-12 095210.png" width="100%" height="100%" />



