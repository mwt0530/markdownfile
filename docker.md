##docker使用记录
###目录
* [docker安装](#install)
* [docker启动](#start)
* [docker使用](#use)
* [dockerfile](#dockerfile)
* [docker存储](#dockerstorage)

<a name="install"/>
####docker安装

系统centos7-1406版本最高1.3，1.5及以下版本不支持docker hub的push和pull，安装centos7-1503源中的docker **1.8.2**。

```
# yum install docker
```
<a name="start"/>
####docker启动

```
# systemctl start docker.service
# docker info
# docker run hello-world //first docker app
```
<a name="use"/>
####docker使用

- 从docker hub下载一个image
```
# docker pull docker/whalesay
# docker images docker.io/docker/whalesay
REPOSITORY    TAG      IMAGE ID       CREATED    VIRTUAL SIZE
docker.io/docker/whalesay   latest  fb434121fc77  10 months ago   247 MB
```

- 运行下载的image
```
# docker run docker.io/docker/whalesay cowsay boo
```

- An interactive container交互式运行模式
```
# docker run -i -t docker.io/docker/whalesay /bin/bash
//进入系统，可以看到image的文件系统
```
>The `-i` flag starts an interactive container. The `-t` flag creates a pseudo-TTY that attaches stdin and stdout.
>To detach the tty without exiting the shell, use the escape sequence **Ctrl-p** + **Ctrl-q**. ==The container will continue to exist in a stopped state once exited.== To list all containers, stopped and running, use the `docker ps -a` command.

- A daemonized Hello world后台运行
```
docker run -d docker/whalesay /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
查看容器输出
```
# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
d31b4a53c361        docker/whalesay     "/bin/sh -c 'while tr"   5 seconds ago       Up 3 seconds                            nostalgic_newton
# docker logs d31b4a53c361 
# docker logs -f d31b4a53c361    //类似与tail -f
```

- 端口映射
```
# docker run -d -P training/webapp python app.py
```
>`-P` flag is a shortcut for `-p 5000` that maps port 5000 inside the container to a high port (from ephemeral port range which typically ranges from **32768 to 61000**) on the local Docker host. We can also bind Docker containers to specific ports using the `-p` flag, for example:
```
# docker run -d -p 80:5000 training/webapp python app.py
```

- Updating and committing an image
修改image，然后commit。
```
docker commit -m "add mawentao in mywhale" -a "mwt0530" eacb986ea36e mwt0530/mywhale:v2
```
再运行
```
docker run -it mwt0530/mywhale:v2 /bin/bash
```

- Data volumes
数据卷不依赖于docker的文件系统类型Union file systems, or UnionFS，目的就是在各容器之间保存和分享数据，即使容器被删除，数据卷会被保留。
	1. 	添加单个新数据卷
	```
    # docker run -d -P --name web -v /webapp training/webapp python app.py
    ```
    此时在容器中会生成一个/webapp目录，通过
    ```
    # docker inspect web
    ```
	Mounts部分对应路径就是本地对应保存地点`/var/lib/docker/volumes`
	2.	本地目录作为数据卷
	```
    # docker run -d -P --name web -v /opt/mnt:/opt/webapp training/webapp python app.py
    ```
    将本地`/opt/mnt`内容mount到容器的`/opt/webapp`上,容器路径必须绝对路径，本地路径可以使用绝对或者相对路径，区别在于：
    	- /opt/mnt:/opt/webapp ---直接mount
    	- opt/mnt:/opt/webapp---Docker creates a named volume by that name,`/var/lib/docker/volumes/opt/mnt/`
	3.	mount一个本地文件到容器
	```
    # docker run --rm -it -P --name web -v ~/.bash_history:/root/.bash_history docker.io/training/webapp /bin/bash
    ```
    4.	Creating and mounting a data volume container数据卷容器
	```
    # docker create XXXXXXXX
    # docker run --volumes-from XXXXX
    # docker rm -v XXXXX
    ```

<a name="dockerfile"/>
####dockerfile
```
# docker build -f dockerfilename PATH
```
- FROM 
格式：
```
FROM <image>
FROM <image>:<tag>
FROM <image>@<digest>
```
FROM must be the **first non-comment** instruction in the Dockerfile

- CMD
三种格式
```
CMD ["executable","param1","param2"] (exec form, this is the preferred form)
CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
CMD command param1 param2 (shell form)
```
There can only be **one** CMD instruction in a Dockerfile. If you list more than one CMD then only the last CMD will take effect.
- WORKDIR
RUN,CMD,ENTRYPOINT,COPY,ADD的容器的工作目录。
```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
结果是/a/b/c

- ENTRYPOINT
格式：
```
ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
ENTRYPOINT command param1 param2 (shell form)
```
CMD  vs ENTRYPOINT
1.	Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

2.	ENTRYPOINT should be defined when using the container as an executable.

3.	CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.

4.	CMD will be overridden when running the container with alternative arguments.


<a name="dockerstorage"/>
####docker存储
devicemapper，在centos虚拟机中不能启动docker，使用自己创建的lvm

```
# lvs
# vgs
# lvcreate -L 10G -n dockerpool centos
# lvcreate -L 4G -n dockermetadata centos
# docker daemon --storage-driver=devicemapper --storage-opt dm.datadev=/dev/centos/dockerpool --storage-opt dm.metadatadev=/dev/centos/dockermetadata &

```
