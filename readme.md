# 云渲染学习笔记
## Gaming Anywhere云游戏
_GamingAnywhere是一个开源的实现云游戏的引擎，并且高效、跨平台、易扩展、可调配。_  
  
官方链接：https://gaminganywhere.org/index.html  
github链接：https://github.com/chunying/gaminganywhere  
###  一  运行原理
![image](https://github.com/maolala233/learning_note/blob/main/image/image%201.jpg)  
Gaming Anywhere在体系结构中定义了两种类型的网络流：**数据流** 和 **控制流**。  
数据流用于将音频和视频帧从服务器流到客户端，而控制流则以相反的方向运行，用于将用户的操作从客户端发送到服务器。  
###  二  环境配置
* **虚拟机: VMware 16 Pro(Ubuntu16.0.4)** 相关镜像可自行从网上下载  **推荐设置：**硬盘20G；内存：1G；网络：桥接
* 从官网 (https://github.com/chunying/gaminganywhere) 下载源代码（gaminganywhere-master.zip格式）并通过 VMware tools将文件拖入Home文件夹并解压（可以通过右键解压也可通过终端运行**unzip gaminganywhere-master.zip**解压）
* 解压缩后，进入目录并使用ls命令，目录结构如图所示：
![image](https://github.com/maolala233/learning_note/blob/main/image/image2.png)  
**其中因为我已经安装好 Gaming Anywhere 了，所以多了一个bin的文件夹**  
* 安装GamingAnywhere编译所需要的依赖项  
更新软件源：  ```sudo apt-get update```  
安装依赖项：  ```sudo apt-get install -y patch make cmake g++ pkg-config libx11-dev libxext-dev libxtst-dev libfreetype6-dev libgl1-mesa-dev libglu1-mesa-dev libpulse-dev libasound2-dev lib32z1```  
编辑~/gaminanywhere-master/env-setup文件：  ```vi env-setup```  
修改其中的GADEPS变量值如下：```export GADEPS=/home/myk/myprog/gaminganywhere/deps.posix```其中myk为创建虚拟机时设置的账户名(**不熟悉vi操作的可以直接打开改完后save就行**)  
编辑完成后，在当前目录运行是的编辑的变量立即生效```source env-setup```  
进入~/gaminganywhere-master/deps.src并执行make命令  
```
cd ./deps.src
sudo make
```  
**ERROR: x265 not found using pkg-config的错误**：解决方案：输入命令行即可```sudo apt-get install libx265-dev```  

