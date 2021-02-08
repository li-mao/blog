---
title: perl的语境和默认值
date: 2021-02-08 11:02:38
tags:
---

#### Modern Perl https://www.jianshu.com/p/98173e7af633

## 语境

### 空语境，标量语境，列表语境

当你调用一个函数，并且不使用它的返回值时，你就使用了空语境。例如调用一个叫`find_chores()` 的函数：
```perl
find_chores()；
```
如果将返回值赋值给一个标量，这就指定了标量语境，函数将表现为标量语境下的行为：
```perl
my $single_result = find_chores();
```
如果将函数的返回值赋值给一个数组、列表，或者在列表中使用，那么函数就处在列表语境中：
```perl
my @all_results = find_chores();
my ($single_element, @rest) = find_chores();
```
单一元素的列表，还是列表，所以也是列表语境：
```perl
my ($single_element) = find_chores();
```
函数在不同的语境下可能产生不同的行为，如果语境不对，结果可能与预期的不一致：
```perl
my %results =(
     cheap_operation => $cheap_results,
     expensive_operation => find_chores(),  # 列表语境下的行为，可能不是你所期望的
);
```
这时可以使用关键词明确指定为标量语境：
```perl
my %results =
(
     cheap_operation => $cheap_results,
     expensive_operation => scalar find_chores(),
);
```

### 操作符语境: 数值语境，字符串语境，布尔语境
在前面的例子中我们看到，使用“+”号时，Perl会变量解析为数值，其实就是加号操作符提供了数值语境。

使用字符串操作符时，就会提供字符串语境。如操作符“eq”：
```perl
say "Catastrophic crypto fail!" if $alice eq $bob;
```
注意和下面这个的区别，数值操作“==”提供的是数值语境：
```perl
say "Catastrophic crypto fail!" if $alice == 'Bob';
```
布尔语境，出现在条件语句里面。比如上面的 if语句后面的表达式就是布尔语境。

在某些罕见的情况下，可能需要更为明确的指明语境：
```perl
my $numeric_x = 0 + $x;     # 加 0   强制用数值语境 
my $stringy_x = '' . $x;    # 加 空串 强制用字符串语境
my $boolean_x = !!$x;       # 加 双反 强制用布尔语境
```

## 默认值

### 默认标量标量： 
#### `$_` 
打印类 print，say;
标量操作类 chr,ord,lc,length,reverse, 正则等等。

### 默认的数组变量: 
#### 函数内`@_`
在调用函数时，传递的参数就存在默认数组变量 `@_` 里面。在函数内部，数组操作符默认就是操作这个数组变量
下面2段代码是等价的：
```perl
sub foo
{
    my $arg = shift;
    ...
}

sub foo_explicit_args
{
    my $arg = shift @_;
    ...
}
```
如果`$_`对应的意思是“那个”，那么`@_`对应的意思就是“那些”。
数组变量`@_`是每个函数内部私有的，不同函数之间互不影响。

#### 函数外`@ARGV`
如果在所有函数的外面，默认的数组变量就是`@ARGV`，里面包含了该程序的命令行参数。

`@ARGV`有自己的个性。如果你是使用钻石操作符`<>`读取空的文件句柄，Perl就会自动以`@ARGV`里面的元素为名字，读取对应的文件。
如果`@ARGV`为空，Perl就从标准输入STDIN里读取。我们可以利用这个特性写个短小精悍的程序，如将输入的字符串逆序输出：
```perl
while (<>)
{
    chomp;
    say scalar reverse;
}
```
为什么要加上scalar呢？如果在列表语境下，reverse并不默认操作`$_` 。
