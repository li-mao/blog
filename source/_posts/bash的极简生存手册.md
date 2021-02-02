---
title: 'bash的极简生存手册'
date: 2021-01-14 11:56:04
tags:
---

## 无处不在的"文件"
unix中一切皆文件。
```shell
pwd   #查看当前目录
ls    #查看当前目录下的文件
cd    #进入指定目录

/     #根目录
.     #当前目录简写
..    #上级目录简写

mkdir #创建目录
touch #创建文件
ln    #创建文件链接,"-s"可以创建目录链接
rm    #删除目录或文件
mv    #移动目录或文件
cp    #复制目录或文件

tree  #查看目录内的结构,"-L 2"控制查看深度
du    #查看目录下的各文件目录磁盘占用,"-d1"控制查看深度

find  #搜索目录或文件
grep  #搜索文件内容
which #查看命令的位置,命令是执行文件,本质也是文件
chmod #修改文件权限,"+x"变成可执行文件,"-x"变成普通文件

vim   #编辑文件内容
ed    #行编辑器
sed   #流编辑器,它的装机率极高
```
文本处理的骚操作
```shell
less    #分页输出文件内容
wc      #统计文件的文本数据

sort    #排序,"-n"数字排序
uniq    #去重
diff    #比较

tac     #各行从下到上展示
rev     #行内从后到前展示

echo    {1..5}  #生成水平序列
seq     1 5     #生成垂直序列

cat     t1 t2   #水平拼接
paste   t1 t2   #垂直拼接
join    t1 t2   #交集拼接

head    -n 5    #水平截取头部
tail    -n 5    #水平截取尾部
cut     -b 3,5  #垂直截取
```
## "输入"、"输出"和"管道"
在shell命令行中首先输入的单词全部被当成"命令"来处理,后面的单词被该"命令"内的"$*"获取。  
如果该"命令"有交互逻辑,"命令"内的"read"会获取输入的数据。  
"命令"会将产生的数据传给shell,shell通过"标准输出"展示到屏幕上。  
如果"命令B"想接着处理"命令A"输出的数据,可以用管道来实现 "A|B"。  
"命令B"能成功接收数据的前提是因为有交互逻辑，除此以外也可以借助xargs命令来实现。  
```text
shell会识别命令后的 "<"  字符(通常之后还有一个文件名),将文本中的内容当做和命令交互的"标准输入",只需要命令支持交互。
shell会识别命令后的 "<<" 字符(通常之后还有HereDoc),将HereDoc的内容当做和命令交互的"标准输入",只需要命令支持交互。
shell会识别命令后的 ">"  字符(通常之后还有一个文件名),将原本输出到屏幕上的数据重定向到文本中(覆盖文本)。
shell会识别命令后的 ">>" 字符(通常之后还有一个文件名),将原本输出到屏幕上的数据重定向到文本中(追加文本)。
```
大致原型:
```shell
"cmdA"
"cmdA" < "fileA"
"cmdA" > "fileC"
"cmdA" < "fileA" > "fileC"
"cmdA" | "cmdC"
"cmdA" | "cmdC" > "fileC"
"cmdA" < "fileA" | "cmdB" | "tee" "fileB" | "cmdC" > "fileC"
```
注意: 不存在 "cmdA" < "fileA" > "cmdC",因为cmd必须在第一位。即 "fileA" > "cmdA" 是非法格式

测试shell
```shell
#!/usr/bin/env bash

# shell脚本 a.sh
echo "args: ",$*
while read str;do
  echo "stdin: ",$str
done
```
输出结果
```shell
./a.sh 111 <<EOF
heredoc> 222
heredoc> 333
heredoc> EOF
args: ,111
stdin: ,222
stdin: ,333

cat test.txt
One
twO
three

./a.sh 111 < test.txt
args: ,111
stdin: ,One
stdin: ,twO
stdin: ,three
```

## Shell语法
### 多重宇宙
```shell
set     #是独享的
env     #是可以遗传给子shell
export  #将某个set变量提升为export

. ./a.sh    #本shell执行脚本,"."与"source"同义,用本shell的环境变量
$(ls)       #子shell执行一组命令,用子shell的环境变量
./a.sh      #子shell执行shell脚本,用子shell的环境变量
```
### 字符
`#` 是去掉 左边（键盘上`#`在`$` 的左边）  
`%` 是去掉 右边（键盘上`%`在`$` 的右边）   
单一符号是最小匹配; 两个符号是最大匹配

```shell
% var=000111222
% echo ${var#*1}
11222
% echo ${var##*1}
222
% echo ${var%1*}
00011
% echo ${var%%1*}
000
```
```shell
${var:N:N }   #提取变量字符串的一段：

% var=000111222
% ${var:0:3}  #提取最左边的 3 个字节：000
${file:3:3}   #提取第 3 个字节右边的连续3个字节：111
```
```shell
${var/a/b}    
${var//a/b}
#对变量值里的字符串作替换

${var/1/3}  #将第一个1 替换为3 : 000311222
${var//1/3} #将全部1 替换为3 : 000333222
```
### 数组和循环
```shell
for i in a b c d;do echo $i;done

while true;do echo $i;done
```
```shell
arr=(1 2)   #创建新数组
arr2=({1..2}) #自动生成数组
arr1=$(ls)  #利用其他命令生成数组
arr+=(3)    #追加元素
arr[0]=666
unset ${arr[1]} #删除元素
${arr[@]}   #展开数组给for用
${!arr[@]}  #只展开它的索引
${#arr[@]}  #获取长度
${arr_name[@]:1:2} #切片
merge_array=( ${a[@] ${b[@]} ... ) #合并数组
```
```shell
% arr=(1 2 3)
% for i in ${arr[@]};do echo $i;done
1
2
3
% arr+=4
% for i in ${arr[@]};do echo $i;done
14
2
3
```
### 判断
$? 命令正常退出返回0,其他情况返回整数

bash中如何实现条件判断？
条件测试类型：
```text
整数测试
字符测试
文件测试
```
一、条件测试的表达式：
```shell
[ expression ]  #括号两端必须要有空格
[[ expression ]] #括号两端必须要有空格
test expression

#组合测试条件
-a: #and
-o: #or
!:  #非
```
二、整数比较：
```shell
-eq #测试两个整数是否相等
-ne #测试两个整数是否不等
-gt #测试一个数是否大于另一个数
-lt #测试一个数是否小于另一个数
-ge #大于或等于
-le #小于或等于
```
```shell
#命令间的逻辑关系
&&  #逻辑与
||  #逻辑或
```
三、字符串比较
字符串比较：
```shell
==   #等于  两边要有空格
!=   #不等
>    #大于
<    #小于
```
四、文件测试
```shell
-z string #测试指定字符是否为空，空着真，非空为假
-n string #测试指定字符串是否为不空，空为假 非空为真
-e FILE   #测试文件是否存在
-f file   #测试文件是否为普通文件
-d file   #测试指定路径是否为目录
-r file   #测试文件对当前用户是否可读
-w file   #测试文件对当前用户是否可写
-x file   #测试文件对当前用户是都可执行
-z        #是否为空  为空则为真
-a        #是否不空
```
五、if语法   
if 判断条件 0为真 其他都为假
```shell
if 判断条件;then statement1;else statement2;fi
```
Note:
```shell
#if语句进行判断是否为空
[ "$name” = "" ]

#等同于
[ ! "$name" ]
[ -z "$name" ]
```
Note:
```text
使用if语句的时候进行判断如果是进行数值类的 ，建议使用 let(())进行判断
对于字符串等使用test[ ] or [[ ]] 进行判断
(()) 中变量是可以不使用$来引用的
```

写脚本的时候很多时候需要用到回传命令，$?如果上一个命令执行成功，回传值为0，否则为1~255之间的任何一个
```text
0为真
非0为假
```
条件测试的写法：
```shell
#  执行一个命令的结果
if grep -q "rm" fs.sh;then

#传回一个命令执行结果的相反值
if !grep -q "rm" fs.sh;then

#使用复合命令（（算式））
if ((a>b));then

#使用bash关键字 [[判断式]]
if [[ str > xyz ]];then

#使用内置命令：test 判断式
if test "str" \> "xyz";then

#使用内置命令：[判断式]  类似test
if [ "str" \> "xyz" ];then

#使用-a -o进行逻辑组合
[ -r filename -a -x filename ]

#命令&&命令
if grep -q "rm" fn.sh && [ $a -lt 100 ];then

#命令||命令
if grep -q "rm" fn.sh || [ $a -lt 100 ];then
```
## 文字处理工具
### grep 全文查找  
```shell
-e #更强的扩展
-i #忽略大小写
-v #取反
```
### sed 流编辑，行处理 
linux系统默认都是`GUN sed`,但MacOS自带的是UNIX原版的`sed`,这两个会有差异,最大不同在于`-i`选项   
MacOS下直接下载`gsed`使用。`brew install gnu-sed`
```shell
-i #更新源文件
-n #参数后只显示处理过的行

sed -i 's/1/2/g' a.txt

s #替换指定字符
a #在当前行下面插入文本。
i #在当前行上面插入文本。
c #把选定的行改为新的文本。
d #删除，删除选择的行。
r #将一个独立文件的数据插入到当前数据流的指定位置。 sed '3r from.txt' to.txt
w #命令用来将文本中指定行的内容写入文件中。 sed '1,2w to.txt' from.txt
```
### awk 处理字段,全面的文本处理  
```shell
awk 'BEGIN{ FS = "," }{print $2,$1,$3}' a.txt
```
```awk
FS  #字段分隔符
OFS #字段输出分隔符
RS  #行分隔符
ORS #行输出分隔符
```
### 其他工具
```shell
cut #字符切片
tr  #字符替换
```
