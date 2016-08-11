###gogs安装配置--centos7

####文档
https://gogs.io/docs/installation/install_from_binary

####mysql配置
创建gogs数据库
```
mysql -u root -p
mysql> SET GLOBAL storage_engine = 'InnoDB';
mysql> CREATE DATABASE gogs CHARACTER SET utf8 COLLATE utf8_bin;
mysql> GRANT ALL PRIVILEGES ON gogs.* TO ‘root’@‘localhost’;
mysql> FLUSH PRIVILEGES;
```

####运行
```
./gogs web

nohup gogs/gogs web > log/gogs_web.log 2>&1 &
```

##docker-gogs
```
# docker run  --name=mysql -v /opt/gogs/mysql/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d daocloud.io/library/mysql
# docker run --name=gogs -v /opt/gogs/gogs/:/data --link mysql:gogs-mysql -p 10022:22 -p 10080:3000 -d docker.io/gogs/gogs
```


参考：
http://www.tuicool.com/articles/Uviam2u
https://www.digitalocean.com/community/tutorials/how-to-set-up-gogs-on-ubuntu-14-04
https://github.com/gogits/gogs/issues/1306
>ssh clone参考github issue,需要添加sshkey
