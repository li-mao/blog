---
title: sed的两个缓冲区
date: 2021-04-08 15:58:07
tags:
---
["Sed" 高级功能](https://juejin.cn/post/6844903859396050957)   
sed中命令当前处理的行，存放在`pattern space`
还有一个全程无感的`hold space`

| NextLine | operate | PatternSpace | operate | HoldSpace | |
| -------- | ---- | ------- | ---- | ----- | ---- |
| | | $ \bigotimes $ | $\Leftrightarrow $ | $\bigodot  $ | x: 交换
| | | $ \bigotimes $ | $ \Rightarrow $ | $ \bigodot $ | h: 复制
| | | $ \bigotimes $ | $ \Rightarrow $ | $ \bigodot Tail $ | H: 追加
| | | $ \bigotimes $ | $ \Leftarrow $ | $ \bigodot $ | g: 复制
| | | $ \bigotimes Tail $ | $ \Leftarrow $ | $ \bigodot $ | G: 追加
| $ \bigoplus $ | $ \Rightarrow $ | $ \bigotimes  $ | | | n: 复制
| $ \bigoplus $ | $ \Rightarrow $ | $ \bigotimes Tail  $ | | | N: 追加
| | $ Clear $ | $ \bigotimes $ | | | d: 清空
| | $ Clear $ | $ \bigotimes Head  $ | | | D: 删除
| | $ Print $ | $ \bigotimes  $ | | | p: 打印
| | $ Print $ | $ \bigotimes Head  $ | | | P: 打印

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
