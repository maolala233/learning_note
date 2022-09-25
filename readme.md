# 云渲染学习笔记
## Gaming Anywhere云游戏
_GamingAnywhere是一个开源的实现云游戏的引擎，并且高效、跨平台、易扩展、可调配。_  
  
官方链接：https://gaminganywhere.org/index.html  
github链接：https://github.com/chunying/gaminganywhere  
###  一  运行原理
![image](https://github.com/maolala233/learning_note/blob/main/image/image%201.jpg)  
Gaming Anywhere在体系结构中定义了两种类型的网络流：**数据流** 和 **控制流**。  
数据流用于将音频和视频帧从服务器流到客户端，而控制流则以相反的方向运行，用于将用户的操作从客户端发送到服务器。  
###  二  Linux环境配置
* **虚拟机: VMware 16 Pro(Ubuntu16.0.4)** 相关镜像可自行从网上下载  **推荐设置：**硬盘20G；内存：1G；网络：桥接
* 从官网 (https://github.com/chunying/gaminganywhere) 下载源代码（gaminganywhere-master.zip格式）并通过 VMware tools将文件拖入Home文件夹并解压（可以通过右键解压也可通过终端运行**unzip gaminganywhere-master.zip**解压）
* 解压缩后，进入目录并使用ls命令，目录结构如图所示：
![image](https://github.com/maolala233/learning_note/blob/main/image/image2.png)  
**其中因为我已经安装好 Gaming Anywhere 了，所以多了一个bin的文件夹**  
* 安装GamingAnywhere编译所需要的依赖项  
更新软件源：  ```sudo apt-get update```  
安装依赖项：  ```sudo apt-get install -y patch make cmake g++ pkg-config libx11-dev libxext-dev libxtst-dev libfreetype6-dev libgl1-mesa-dev libglu1-mesa-dev libpulse-dev libasound2-dev lib32z1```  
* 编辑~/gaminanywhere-master/env-setup文件：  ```vi env-setup```  
修改其中的GADEPS变量值如下：```export GADEPS=/home/myk/myprog/gaminganywhere/deps.posix```其中myk为创建虚拟机时设置的账户名(**不熟悉vi操作的可以直接打开改完后save就行**)  
编辑完成后，在当前目录运行是的编辑的变量立即生效```source env-setup```  
* 进入~/gaminganywhere-master/deps.src并执行make命令  
```
cd ./deps.src
sudo make
```  
* **ERROR: x265 not found using pkg-config的错误**：解决方案：输入命令行即可```sudo apt-get install libx265-dev```  
* 编译通过后home中会出现一个gaminganywhere的文件夹：  
![image](https://github.com/maolala233/learning_note/blob/main/image/image3.png)  
* 将**gaminganywhere/deps.posix/lib/pkgconfig**路径下的文件复制进**usr/share/pkgconfig**文件夹中(提示无权限可通过终端进入root权限进行复制操作)：  
![image](https://github.com/maolala233/learning_note/blob/main/image/image4.png)  
* 完成后回到ga文件夹依次执行如下命令：  
```
make all 
make install
```  
* 运行完后退出到gaminganywhere-master目录，查看文件夹中发现出现了bin目录（如果没有则说明未安装成功），查看一下文件：  
```
cd ../bin
ls
```  
![image](https://github.com/maolala233/learning_note/blob/main/image/image5.png)  
其中**ga-client**:用于执行云游戏的客户端命令工具；**ga-server-periodic**是运行云游戏的服务端工具，用于开启周期录屏的方式运行云游戏服务端。 
* 将gaminanywhere包含的动态依赖库文件装载入系统变量  
编辑文件gaminganywhere.conf(路径如图所示):  
![image](https://github.com/maolala233/learning_note/blob/main/image/image6.png)  
文本中的内容为：```/home/myk/gaminganywhere-master/deps.posix/lib```   
编辑完成保存退出即可,完成之后一定要再运行```ldconfig```使得该目录下的库重新被导入系统变量，云游戏服务端已经搭建完毕。  
###  三  windows环境配置
