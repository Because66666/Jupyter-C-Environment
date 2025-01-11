众所周知，jupyter主要用于写python代码，但是我们可以通过更换核心的方式来写C语言代码。
效果呈现：
![[{26FC477F-6F7D-43AD-A1EB-589A2D644468}.png]]
其中的警告不清楚如何去除，但是能用。
### 一、环境介绍
jupyter版本：
![[Pasted image 20250111141528.png]]
gcc版本（C编译器）：
![[Pasted image 20250111141554.png]]
这里使用的是MinGW（Minimalist GNU for Windows）来下载构建windows平台上的GCC
### 二、指引
注意：本指引并不介绍jupyter的安装说明，也不清楚如何在linux上构建这个环境。
#### **安装MinGW**
下载[cygwin版的MinGW](https://cygwin.com/install.html)
在确认好安装路径之后进行安装，安装完之后进入到MinGW\bin路径下，找到
![[{C3A3138F-CCFB-4E47-A4BA-132AE5A3E348}.png]]
这个exe可执行文件。
#### **安装GCC和MSYS**
在==当前目录==下右箭打开终端/命令提示符，执行
```
mingw-get.exe install gcc
```
![[Pasted image 20250111142637.png]]
以及
```
mingw-get.exe install msys
```
![[{2BF7986A-581C-4E83-9E94-C71B6CB87B69}.png]]
#### **构建libdl.a**
下载好 [dlfcn-win32 for MinGW](https://link.zhihu.com/?target=https%3A//github.com/dlfcn-win32/dlfcn-win32) ,并在该目录下创建 config.mak 文件,内容为
```text
PREFIX=D:\software\mingw 
BUILD_STATIC=yes 
CC=gcc 
AR=ar 
RANLIB=ranlib
```
其中`PREFIX`为你的MinGW安装根目录。
现在将整个克隆下来的文件夹，复制到路径：
```
D:\software\mingw\msys\1.0\home\你的用户名
```
注意，上面的`D:\software\mingw`是MinGW的安装路径，`msys`则是上一步中安装的msys，如果没有则往回看。下面同理。
回到`D:\software\mingw\msys\1.0`路径下，双击运行`msys.bat`，打开类unix终端，依次运行：
```bash
cd dlfcn-win32-1.4.1
make clean
make libdl.a
```
![[{CEE419EF-D63E-4BC9-BBF6-846BA67F8050}.png]]
![[{B5EBEC89-D40F-4F7A-9904-479177D371B5}.png]]
![[{802DA990-4876-48DB-A3F0-2782AF1113AE}.png]]
然后你可以在
`D:\software\mingw\msys\1.0\home\你的用户名\dlfcn-win32-1.4.1`路径下找到刚才编译出来的libdl.a，
将`libdl.a` 复制到 `D:\software\mingw\lib` 目录。
![[{23A8FC5E-CADD-42DA-BF1D-EA529D029D22}.png]]
#### **安装适用于windows的C语言核心**
（实际上只起到一个中转站的作用，编译器还是用的gcc）
打开普通的终端，输入：
```
pip install jupyter_c_kernel
```

将定位到 `Python311\Lib\site-packages\jupyter_c_kernel\` 文件夹 下的kernel.py里面所有的 `-rdynamic` 全部换成 `-export-dynamic` 。
将定位到 `Python311\Lib\site-packages\jupyter_c_kernel\` 文件夹 下的 `install_c_kernel` 修改为 install_c_kernel.py，并运行命令

```text
python install_c_kernel.py
```
![[{087FB66F-9B68-4FA5-AE35-60356274900A}.png]]
最后
```
jupyter lab
```
即可。

### 三、参考文献指路
https://zhuanlan.zhihu.com/p/648326046
因为知乎的这篇文章写于23年8月份，笔者在25年1月份按图索骥的时候发现其中有一些不甚详细的内容（比如make命令是怎么来的），以及一些其他小的问题（比如`dlfcn.c`文件不需要改，`dlfcn.h`文件不需要复制过去），因此重新整理了一份教程。
