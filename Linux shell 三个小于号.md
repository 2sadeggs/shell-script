# Linux Shell <<<

### 关键字

linux shell  <<<

shell三个小于号

### 简介

"<<<" 在bash脚本文档中称之为"Here Strings"。

 Here Strings是Here Documents的一种变种。

它由操作符"<<<"和作为标准输入的字符串构成，here-strings是一个用于输入重定向的<mark>普通字符串</mark>。

### 引用

[【shell】怎么理解shell中的 "<" 、 "<<" 、 "< <" 、"<<<" 的含义？ - 简书 (jianshu.com)](https://www.jianshu.com/p/70136d731ca0)

### 语法

#command <<< "hello world"

*注意：单个单词不需要引号引用，中间如果有空格的字符串，则需要引号引用起来。*

```
root@xingnengceshi:~# cat <<< "hello world ~"
hello world ~
root@xingnengceshi:~# cat <<< hello world ~
cat: world: No such file or directory
cat: /root: Is a directory
```

### 背景

有个脚本需要用awk操作变量，首先想到的自然是用echo把变量管道输出给awk，但无意中发现了一种“新奇”的写法[【Linux】awk处理变量_卜塔的博客-CSDN博客](https://blog.csdn.net/NextAction/article/details/90401155)

```
root@heimdall:~# cat demo.sh
#!/bin/bash
a='abc,123'
a1=`echo "$a" | awk -F "," '{print $1}'`
a2=`echo "$a" | awk -F "," '{print $2}'`
echo '拆分后第一部分:'$a1
echo '拆分后第二部分:'$a2
```

###### 第一反应写法

new_var=`echo $var | awk -F "," '{print $1}'`

<mark>注意：-F分割符号后是双引号，但是后边的大括号外边是单引号。</mark>

###### 实际上还有一种方式--也就是Here-Strings

```
root@heimdall:~# cat demo2.sh 
#!/bin/bash
a='abc,123'
a1=`awk -F "," '{print $1}'<<<"$a"`
a2=`awk -F "," '{print $2}'<<<"$a"`
echo '拆分后第一部分:'$a1
echo '拆分后第二部分:'$a2
```

###### echo变量管道输出给命令和Here-Strings的区别

 引用：

[bash - What does &lt;&lt;&lt; mean? - Unix &amp; Linux Stack Exchange](https://unix.stackexchange.com/questions/80362/what-does-mean)

[shell - What does the Bash operator &lt;&lt;&lt; (i.e. triple less than sign) mean? - Stack Overflow](https://stackoverflow.com/questions/7950268/what-does-the-bash-operator-i-e-triple-less-than-sign-mean)

小结：

管道的方式会将管道左右的命令都放在子shell里执行，而子shell无法改变父shell里的变量。

Here-Strings的方式并不会出现子shell，这样在一定情况下能避免脚本意外。

验证：

[管道与子shell | 毛帅的博客](https://imshuai.com/pipeline-subshell)

```
name="shuai"
name="jack" | echo $name
echo $name
```

查看shell的man手册

```
man shell
```

###### 管道到底做了什么手脚

[我们天天都在使用的管道命令，Shell 在里面到底动了什么手脚？-腾讯云开发者社区-腾讯云](https://cloud.tencent.com/developer/article/1361656)


