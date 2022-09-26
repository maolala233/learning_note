# 云渲染学习笔记
## ● Gaming Anywhere云游戏
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
* Windows云游戏客户端下载地址：https://gaminganywhere.org/download.html  
选择gaminganywhere-0.8.0-bin.win32.zip (released on 2015-01-29)下载到windows中解压即可  
###  四  启动云游戏  
* 准备一款Linux游戏并启动  
linux终端运行
```
sudo apt-get install supertux
supertux2
```  
如果无法下载则可点击界面右上角设置更换镜像源为清华镜像源：  
![image](https://github.com/maolala233/learning_note/blob/main/image/image%207.jpg)  
**切记！启动之后不要关闭游戏窗口**  
* 编辑配置文件，开启GamingAnywhere服务端  
在GamingAnywhere的路径中，有一个文件夹：~/gaminganywhere-master/bin/conf/，这个文件夹针对每一款云游戏进行了一定的配置。  
在该文件夹下新建配置文件命名为server.supertux2.linux.conf，并在里面添加内容，首先编辑文件在其中添加如下代码：  
```
# configuration for the openttd game
# work with ga-server-periodic

[video]
video-fps = 50

[core]
include = common/server-common.conf
include = common/controller.conf
include = common/video-x264.conf
include = common/video-x264-param.conf
include = common/audio-lame.conf

[ga-server-periodic]

display = :0
find-window-name = supertux2 v0.4.0

enable-audio = true
```  
其中``` find-window-name``` 项值为当前游戏右上角的窗口名，从该配置中我们可以看出gaminganywhere主要通过窗口抓取方式实现视频流传输：  
![image](https://github.com/maolala233/learning_note/blob/main/image/image%208.jpg)  
* 编辑完成后保存，随后进入bin目录：**gaminganywhere-master/bin**
* 运行云游戏服务端：```./ga-server-periodic config/server.supertux2.linux.conf```  
运行成功，云游戏的Server端将不断刷新如下图：
![image](https://github.com/maolala233/learning_note/blob/main/image/image9.png)  
* 开启客户端连接  
在Windows记住刚才解压的二进制版GamingAnywhere的路径，例如我的地址为```D:\gaminganywhere-0.8.0-bin.win32\gaminganywhere-0.8.0\bin```  
Win + R键打开cmd，输入如下，进入GamingAnywhere客户端目录：
```
d:
cd gaminganywhere-0.8.0-bin.win32\gaminganywhere-0.8.0\bin
```  
由于需要使用Server端的IP地址作为启动客户端的参数，因此在Ubuntu中输入ifconfig，查看IP地址:  
![image](https://github.com/maolala233/learning_note/blob/main/image/image10.png)  
我的服务器端的IP地址为:192.168.158.131  
在windows命令提示栏中输入命令格式如下：ga-client {config} rtsp://server-address:server-port/desktop  
其中```server-address:server-port```为IP地址和端口  
输入开启云端连接```ga-client.exe config\client.abs.conf rtsp://192.168.158.131:8000/desktop```  
云游戏连接成功，效果如下图所示  
![image](https://github.com/maolala233/learning_note/blob/main/image/image11.png)  
## ● moonlight云游戏  
github链接：https://github.com/moonlight-stream  
###  一  运行原理  
* 通过游戏串流的方式以视频的方式发送到其他设备。
* 缺点：1.基于英伟达的技术实现,所以作为运行游戏的服务器,必须使用的是英伟达显卡  
2.打开客户端游戏需手动点击进入
###  二  环境配置  
* 将移动端和服务端放置于同一网络环境下  
* 打开英伟达显卡驱动并启动显卡提供的串流服务：  
![image](https://github.com/maolala233/learning_note/blob/main/image/moonlight%202.png)  
* 移动端下载好moonlight并启动，下载地址：https://github.com/moonlight-stream/moonlight-android/releases  
* 查看电脑所连wifi的IP地址：  
![image](https://github.com/maolala233/learning_note/blob/main/image/moonlight%201.png)   
###  三  启动云游戏  
* 在移动端添加输入ip地址即可连接
* 连接成功：  
![image](https://github.com/maolala233/learning_note/blob/main/image/moonlight%204.jpg)   
_上述云游戏的操作方式都是通过桌面抓取的传输方式进行云渲染，以下是我们对于unity和unreal开发引擎中的云渲染插件进行的尝试_
## ● unreal pixel streaming  
###  一  系统结构  
虚幻引擎将使用该电脑可用的资源（CPU、GPU、内存等）来运行游戏逻辑并渲染每一帧。它会不断将此渲染输出编码到一个媒体流送中，再通过一个轻量级的网页服务堆栈进行传递。用户即可在其他电脑和移动设备上运行的标准网页浏览器中查看直播流送。  
![image](https://github.com/maolala233/learning_note/blob/main/image/unreal%201.jpg)  
![image](https://github.com/maolala233/learning_note/blob/main/image/unreal%202.jpg)  
###  二  生成工程 



## ● unity render streaming  
###  一  系统结构  
渲染流式处理系统由以下 3 个组件组成。
* 信令服务器
* 端 1（发送方）
* 端 2（接收方）  
在渲染流式处理中，在两个端之间创建一个 P2P 网络，该网络发送视频/音频/二进制数据。Web 服务器支持两个端之间的通信，如下图所示： 
![image](https://github.com/maolala233/learning_note/blob/main/image/unity%201.png)  
###  二  生成工程 
* 选择菜单栏中的“窗口>程序包管理器  
![image](https://github.com/maolala233/learning_note/blob/main/image/unity%202.png)  
* 检查“程序包管理器”窗口，单击按钮并选择 。+Add package from git URL...  
![image](https://github.com/maolala233/learning_note/blob/main/image/unity%203.png)  
输入```com.unity.renderstreaming@3.1.0-exp.3```并确定  
* 添加完成  
![image](https://github.com/maolala233/learning_note/blob/main/image/unity%204.png)  
* 下载网络应用程序  
单击 Unity 编辑器上的“编辑>渲染流式处理>下载 Web 应用程序菜单项以下载应用程序:  

下载完成后，打开一个或窗口，并使用选项运行。
