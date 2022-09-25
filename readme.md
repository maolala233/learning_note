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
