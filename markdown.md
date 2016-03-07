###some special markdown methods
##目录

* [注释](#note)
* [链接](#links)
* [图片](#images)


#####<a name="note"/>注释部分
> ## This is a header.
> 
> 1. This is the first list item.
> 2. This is the second list item.
> 
> Here's some example code:
> 
>     return shell_exec("echo $input | $markdown_script");

---

This is [an example](http://example.com/ "Title") inline link.
[This link](http://example.net/) has no title attribute.
>title部分是鼠标放置上显示的内容
>链接部分也可以是本地内容

---
<a name="links"\>
####链接部分
This is [ 百度 ] [1] 
This is [ google ] [2] 
[1]: www.baidu.com "baidu"
[2]: www.google.com "谷歌"

>格式：
>
>* [foo]: http://example.com/  "Optional Title Here"
>* [foo]: http://example.com/  'Optional Title Here'
>* [foo]: http://example.com/  (Optional Title Here)


#####<a name="images"\>图片部分
参考 [链接部分](#links)类似


[google][]
[google]: https
