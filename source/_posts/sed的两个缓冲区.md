---
title: sed的两个缓冲区
date: 2021-04-08 15:58:07
tags:
---
["Sed" 高级功能](https://juejin.cn/post/6844903859396050957)   
[sed命令n，N，d，D，p，P，h，H，g，G，x解析](https://www.cnblogs.com/sqbk/p/8335047.html)

sed中命令当前处理的行，存放在`pattern space`
还有一个全程无感的寄存器`hold space`     
sed有默认输出，即处理到某一行时，先原样输出改行内容，可通过`-n`取消

| NextLine | operate | PatternSpace | operate | HoldSpace | cmd|解释|
| -------- | ---- | ------- | ---- | ----- | ---- | ---- |
| | | $ \bigotimes $ | $\Leftrightarrow $ | $\bigodot  $ | `x`| 交换当前和寄存器的数据
| | | $ \bigotimes $ | $ \Rightarrow $ | $ \bigodot $ | `h`old| 持有当前数据到寄存器
| | | $ \bigotimes $ | $ \Rightarrow $ | $ \bigodot Tail $ | `H`old| 持有当前数据追加到寄存器
| | | $ \bigotimes $ | $ \Leftarrow $ | $ \bigodot $ | `g`et| 获取寄存器数据到当前
| | | $ \bigotimes Tail $ | $ \Leftarrow $ | $ \bigodot $ | `G`et| 获取寄存器数追加据到当前
| $ \bigoplus $ | $ \Rightarrow $ | $ \bigotimes  $ | | | `n`ext| 下一条数据到当前,相当于跳过当前步
| $ \bigoplus $ | $ \Rightarrow $ | $ \bigotimes Tail  $ | | | `N`ext| 下一条数据追加到当前,相当于两步作一步
| | $ Clear $ | $ \bigotimes $ | | | `d`elete| 删除当前数据,不被默认输出,立即进入下一步
| | $ Clear $ | $ \bigotimes Head  $ | | | `D`elete| 删除当前头部数据,不被默认输出,其他当前数据保留到下一步
| | $ Print $ | $ \bigotimes  $ | | | `p`rint| 打印当前数据,追加到默认输出后
| | $ Print $ | $ \bigotimes Head  $ | | | `P`rint| 打印当前头部数据,追加到默认输出前

test.txt
```txet
zero
one
two
three
four
five
six
seven
eight
nine
ten
```

```shell
% gsed 'p' test.txt
zero
zero
one
one
two
two
three
three
four
four
five
five
six
six
seven
seven
eight
eight
nine
nine
ten
ten
```

```shell
% gsed -n 'p' test.txt
zero
one
two
three
four
five
six
seven
eight
nine
ten
```

```shell
% gsed 'n;p' test.txt 
zero  # 当前行被默认输出
one   # 下一行也被默认输出
one   # 这个才是p命令输出的
two
three
three
four
five
five
six
seven
seven
eight
nine
nine
ten
```

```shell
% gsed -n 'n;p' test.txt
one   # 仅有p命令的输出
three
five
seven
nine
```

```shell
#输出最后一行
sed 'N;D' file
```

```shell
#输出文件中最后两行
sed '$!N; $!D' file
```

```shell
#删除文件中最后两行
sed 'N; $!P;$!D;$d' file
```

```shell
#打印偶数行的另一种写法
sed –n 'n;p' file
```

```shell
#每隔5行加入一个空行
sed 'n;n;n;n;G' file
```

```shell
#输出含AAA和BBB和CCC（任意顺序）的段落
sed -e '/./{H; $!d;}' -e 'x;/AAA/!d; /BBB/!d; /CCC/!d' file
```

```shell
#颠倒行序（使末行变首行，首行变末行）
sed -n '1!G; h; $p' file
```
