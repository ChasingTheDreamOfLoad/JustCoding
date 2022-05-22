# shell base

## "dash" or "bash"

运行这条命令 <mark style="background-color:blue;">ls /bin|grep sh</mark>

```
ls /bin|grep sh
########
-rwxr-xr-x  1 root root 1113504 4月  18 23:08 bash*
-rwxr-xr-x  1 root root  121432 1月  25  2018 dash*
lrwxrwxrwx  1 root root       4 4月  18 23:08 rbash -> bash*
lrwxrwxrwx  1 root root       4 3月   1 11:50 sh -> dash*
lrwxrwxrwx  1 root root       4 3月   1 11:50 sh.distrib -> dash*
lrwxrwxrwx  1 root root       7 11月 25 03:05 static-sh -> busybox*
```

通过上述命令 我们看到 sh 软链接到了 dash

因此 当我们使用 <mark style="color:blue;">**sh xxxxx.sh**</mark> 时，其实用的是dash ,就算 你在脚本文件中通过 <mark style="color:blue;">#/bin/bash</mark> 去指定bash运行也没用。

最简单的办法可以试试 <mark style="color:blue;">bash XXX.sh</mark>&#x20;

<mark style="color:blue;"></mark>

<mark style="color:blue;">在dash中，颜色的输出不需要 在echo后面加 -e</mark>

在bash中，是需要的

```
# dash
echo "\e[31mRed Text\e[0m"
Red Text

# bash
echo -e "\e[31mRed Text\e[0m"
Red Text

```

使用 && 或 || 时需要双层括号

```bash
if [[ $a -lt 100 && $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi
```

<mark style="color:blue;"></mark>

<mark style="color:blue;"></mark>

<mark style="color:blue;"></mark>

<mark style="color:blue;"></mark>

&#x20;
