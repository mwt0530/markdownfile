##目录
* [shell数组](#shell_array)
* [i3wm class获取](#xprop)
* [i3wm vnc鼠标不同步](#mouse)

<a name="shell_array"/>
####shell脚本数组的使用
- 声明一个数组：
```
declare -a var
```

- 初始化
```
var=( element1 element2 element3 . . . elementN )
array=( [XX]=<value> [XX]=<value> . . . )
read -a array
```
- 访问
```
for i in “${array[@]}”
do
	#access each element as $i. . .
done
```


####访问示例
```
#!/bin/bash

array=( apple bat cat dog elephant frog )

#print first element
echo ${array[0]}
echo ${array:0}

#display all elements
echo ${array[@]}
echo ${array[@]:0}

#display all elements except first one
echo ${array[@]:1}

#display elements in a range
echo ${array[@]:1:4}

#length of first element
echo ${#array[0]}
echo ${#array}

#number of elements
echo ${#array[*]}
echo ${#array[@]}

#replacing substring
echo ${array[@]//a/A}

exit 0
```

<a name="xprop"/>
####i3wm floating enable
配置i3wm时的class值获取，使用xprop命令。
```
xprop WM_CLASS
```
鼠标变成了十字形，然后在窗口点一下，得到结果:
```
WM_CLASS(STRING) = "vncviewer", "Vncviewer"
```
代表instance为vncviewer，class为Vncviewer。
**参考**
https://i3wm.org/docs/userguide.html#command_criteria
http://www.blogbus.com/songyueju-logs/229287654.html

<a name="mouse"/>
####i3wm vnc鼠标不同步
修改input为：
```
<input type=’tablet’ bus=’usb’/>
```
