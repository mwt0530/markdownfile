##makefile

makefile的缺省目标时第一个目标。
```
CC = gcc

.SUFFIXES:.c .o

.c.o:
    $(CC) -o $@ $^

all: mawentao
    @echo "in all"
.PHONY:all

mawentao: 1.o，2.o
    @echo "making lixiangcun"
    $(CC) -o $@ $^

clean:
    -rm mawentao 1.o 2.o
.PHONY:clean
```
---
####特殊变量$@,$^,$<的含义

- `$@`：表示目标文件，上面makefile里代表mawentao这个值
- `$^`：表示依赖的文件，代表1.o,2.o
- `$<`：表示第一个依赖的文件，代表1.o

####命令前的@,-
- makefile在编译时会打印出执行的命令，`@`代表在执行时不打印执行的命令，类似于shell中的在设置`set -x`时再执行`set +x`的效果。
- 减号`-`代表忽略执行命令时出现的错误

####Suffix rules缺省规则.c.o
- 表示目标`.o`文件都依赖于相对应的`.c`文件，在编写时`.c`放`.o`前面

####.PHONY
- 在执行`make clean`时，目标时执行rm操作，并不会产生clean这个文件，因为clean不依赖于任何文件，如果存在clean这个文件rm操作就不会被执行，`.PHONY`的作用就会在`make clean`时不再检查clean文件是否存在。

####= := ?= +=
见参考链接




####参考
http://archive.oreilly.com/pub/a/linux/excerpts/9780596100292/gnu-make-utility.html
http://www.ruanyifeng.com/blog/2015/02/make.html
http://www.cnblogs.com/wanqieddy/archive/2011/09/21/2184257.html
