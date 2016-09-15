###docker gui app

目前Unix/Linux比较主流的图形界面服务是X11，而X11服务的图形显示方式实际上是一种Client/Server模式，在服务端和客户端之间，X11通过『DISPLAY』环境变量来指定将图形显示到何处。如下面的流程所示，请注意服务端与客户端的位置，服务端是用于提供显示信息的。

[应用程序]->[X11客户端]->[X11服务端]->[显示屏幕]

DISPLAY的格式是『unix:端口』或『主机名:端口』，前一种格式表示使用本地的unix套接字，后一种表示使用tcp套接字。

默认情况下，X11的服务端会监听本地的『unix:0』端口，而DISPLAY的默认值为『:0』，这实际上是『unit:0』的简写。因此如果在Linux的控制台启动一个图形程序，它就会出现在当前主机的显示屏幕中。

基于这个原理，将Docker中的GUI程序显示到外面，就是通过某种方式把X11的客户端的内容从容器里面传递出来。基本的思路无非有两种：

1. SH连接或远程控制软件，最终通过tcp套接字将数据发送出来
2. 和主机共享X11的unix套接字，直接将数据发送出来


#####本地firefox示例：
```
docker run -d --name firefox -e DISPLAY=unix$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix kennethkl/firefox
```
>其中的『-v /tmp/.X11-unix:/tmp/.X11-unix』参数就是将主机上X11的unix套接字共享到了容器里面。因为每个unix套接字实际上就是系统/tmp/.X11-unix目录下面依据套接字编号命名的一个特殊文件。  

出现错误：
```
No protocol specified
```
执行
```
xhost +
```
####远程ssh启动


参考：
http://www.csdn.net/article/2015-07-30/2825340
https://blog.jessfraz.com/post/docker-containers-on-the-desktop/