#haroopad

![haroopad](http://pad.haroopress.com/assets/images/logo-small.png "点击图片进入")

##下载
```
# wget https://bitbucket.org/rhiokim/haroopad-download/downloads/haroopad-v0.13.1-x64.tar.gz
```

##解压
```
# mkdir haroopad
# tar xvf haroopad-v0.13.1-x64.tar.gz -C haroopad
# tar xvf data.tar.gz -C /
```
>制作rpm包时需要用到ibudev.so.0出现错误,参考rpm.md解决。

## 修改libudev
centos7中没有libudev.so.0，可以创建软连接实现。
`# ln -s libudev.so.1 libudev.so.0`

##运行
CLI运行命令
`# haroopad`
>可以给程序加快捷键

