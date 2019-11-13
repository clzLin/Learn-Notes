## 1.第一个Shell脚本 ##
```
1. 新建一个文件，文件名test.sh
2. 写入内容：
   #!/bin/bash
   echo "hello world"
//#! 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。
3.1 作为可执行程序运行：
    chmod +x ./test.sh
    ./test.sh
3.2 作为解释器参数
    /bin/sh test.sh
    //这样就不需要在内容里写第一行指定解释器了。
```

## 2.Shell变量 ##
```
1. 定义变量
   name="jack"
   - 等号两边不能有空格
   - 变量名只能使用字母、数字、下划线，且首字符不能够以数字开头
   - 不能有空格
   - 不能使用bash保留关键字

   循环赋值：
   for file in `ls /etc`  或  for file in $(ls /etc)
   将/etc目录下的文件名循环赋值给file变量

2. 使用变量
   echo ${name}	//{}可不加 但一般都加
  
3. 只读变量
   name="jack"
   readonly name
   name="tom"
   //运行则会报错

4. 删除变量
   unset name
   不能删除只读变量
```

## 3.Shell字符串 ##
### 3.1单引号字符串 ###
```
str='this is a string'
单引号字符串的限制：
- 任何字符都会原样输出，字符串中的变量是无效的；
- 不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。
```

### 3.2双引号字符串 ###
```
your_name='runoob'
str="Hello, I know you are \"$your_name\"! \n"
echo -e $str

输出结果:
Hello, I know you are "runoob"! 

双引号的优点：

- 可以有变量
- 可以出现转义字符
```

### 3.3拼接字符串 ###
```
your_name="runoob"
# 使用双引号拼接
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting  $greeting_1
# 使用单引号拼接
greeting_2='hello, '$your_name' !'
greeting_3='hello, ${your_name} !'
echo $greeting_2  $greeting_3

输出：
hello, runoob ! hello, runoob !
hello, runoob ! hello, ${your_name} !
```

### 3.4获取字符串长度 ###
```
str="abcd"
echo ${#str}   #输出4
```

### 3.5提取子字符串 ###
```
str="this is a string"
echo ${str:1:6}  #输出his is
```

### 3.6查找子字符串 ###
```
str="this is a string"
echo `expr index "$str" ia`  #输出3
#哪个字母先出现就计算哪个，i先出现，且在第三个(并不是下标 是真实的第几个)
```

## 4.Shell数组 ##
bash只支持一维数组。

### 4.1定义数组 ###
```
array_name=(value0 value1 value2 value3)
```

### 4.2读取数组 ###
```
${数组名[下标]}

echo ${array_name[@]}
# @获取所有下标
```

### 4.3获取数组长度 ###
```
# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}
```

## 5.注释 ##
```
# 单行注释

多行注释
:<<EOF
注释内容...
注释内容...
EOF
```

## 6.Shell传递参数 ##
```
test.sh下：
#!/bin/bash

echo "执行文件名:$0";
echo "第一个参数:$1";
echo "第二个参数:$2";
echo "第三个参数:$3";

输出：
./test.sh 1 2 3

执行的文件名：./test.sh
第一个参数：1
第二个参数：2
第三个参数：3
```

```
$#	传递到脚本的参数个数
$*	以一个单字符串显示所有向脚本传递的参数。
$$	脚本运行的当前进程ID号
$!	后台运行的最后一个进程的ID号
$@	与$*相同，但是使用时加引号，并在引号中返回每个参数。
$-	显示Shell使用的当前选项，与set命令功能相同。
$?	显示最后命令的退出状态。0表示没有错误，其他任何
```

### 6.1$* 与 $@ 区别：###
```
echo "-- \$* 演示 ---"
for i in "$*"; do
    echo $i
done

echo "-- \$@ 演示 ---"
for i in "$@"; do
    echo $i
done
```
输出结果：
```
$ chmod +x test.sh 
$ ./test.sh 1 2 3
-- $* 演示 ---
1 2 3
-- $@ 演示 ---
1
2
3
```

## 7.Shell基本运算符 ##
### 7.1算术运算符 ###
```
原生bash不支持简单的数学运算，可以用命令awk或expr实现
val=`expr 2 + 2`
# expr和数字之间必须要有空格，数字和运算符之间也必须要有空格

val=`expr 20 - 10`
val=`expr 20 \* 10`
val=`expr 20 / 10`
val=`expr $a % $b`
if [ $a == $b ]
then
  echo "a变量等于b变量"
fi
if [ $a != $b ]
then
  echo "a不等于b"
fi

#注意空格的位置
```

### 7.2关系运算符 ###
关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
```
a=10
b=20
1. -eq 相等返回true                
[ $a -eq $b ] 返回 false。

2. -ne 不相等返回true              
[ $a -ne $b ] 返回 true。

3. -gt 左边的数大于右边 返回true     
[ $a -gt $b ] 返回 false。

4. -lt 左边的数小于右边 返回true     
[ $a -lt $b ] 返回 true。

5. -ge 左边的数大于等于右边 返回true  
[ $a -ge $b ] 返回 false。

6. -le 左边的数小于等于右边 返回true  
[ $a -le $b ] 返回 true。

# 注意空格位置
```

### 7.3布尔运算符 ###
```
a=10
b=20
! 非运算  [ $a != $b ]                返回true
-o 或运算 [ $a -lt 20 -o $b -gt 100 ] 返回 true。
-a 与运算 [ $a -lt 20 -a $b -gt 100 ] 返回 false。
```

### 7.4逻辑运算符 ###
```
&& 逻辑的 AND [[ $a -lt 100 && $b -gt 100 ]] 返回 false
|| 逻辑的 OR  [[ $a -lt 100 || $b -gt 100 ]] 返回 true

逻辑运算符的&& 和 || 和布尔运算符的 -o -a一个意思。
```

### 7.5字符串运算符 ###
```
a="abc"
b="efg"
=  检测两个字符串相等返回true    [ $a = $b ]  返回 false
!= 检测两个字符串不相等返回true  [ $a != $b ] 返回 true
-z 检测字符串长度为0返回true     [ -z $a ]    返回 false
-n 检测字符串长度不为0返回true   [ -n "$a" ]  返回 true
$  检测字符串不为空返回 true     [ $a ]       返回 true

```

### 7.6文件测试运算符 ###
文件测试运算符用于检测 Unix 文件的各种属性。
```
-b file 检测文件是否是块设备文件[ -b file ]
-c file 检测文件是否是字符设备文件
-d file 检测文件是否是目录
-f file 检测文件是否是普通文件（既不是目录，也不是设备文件）
-g file 检测文件是否设置了 SGID 位
-k file 检测文件是否设置了粘着位(Sticky Bit)
-p file 检测文件是否是有名管道
-u file 检测文件是否设置了SUID位
-r file 检测文件是否可读
-w file 检测文件是否可写
-x file 检测文件是否可执行	
-s file 检测文件是否不为空（文件大小是否大于0）
-e file 检测文件（包括目录）是否存在
-S file 判断某文件是否 socket
-L file 检测文件是否存在并且是一个符号链接
```

**使用运算符都要注空格的位置.**



## 8.echo命令 ##
### 8.1显示换行 ###
```
echo -e "OK! \n"
echo "It is a test"

输出：
OK!

It is a test
```

### 8.2显示不换行 ###
```
echo -e "OK! \c" # -e 开启转义 \c 不换行
echo "It is a test"

输出：
OK! It is a test
```

### 8.3显示结果定向至文件 ###
```
echo "It is a test" > myfile
```

### 8.4显示命令执行结果 ###
```
echo `date`

输出：
Thu Jul 24 10:08:46 CST 2014
```

## 9.if-elif-else ##
```
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi

实例：
a=10
b=20
if [ $a == $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
elif [ $a -lt $b ]
then
   echo "a 小于 b"
else
   echo "没有符合的条件"
fi
```

## 10.for循环 ##
```
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

实例：
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done

顺序输出字符串中的各个字符
for str in 'This is a string'
do
    echo $str
done

格式2：
for((assignment;condition:next));do
    command_1;
    command_2;
    commond_..;
done;

实例：
for((i=1;i<=5;i++));do
    echo "这是第 $i 次调用";
done;
```

## 11.while循环 ##
```
while condition
do
    command
done

实例：
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

## 12.case语句 ##
```
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac

实例：
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac

```

## 13.Shell函数 ##
```
[ function ] funname [()]		#带[] 代表可选
{
    action;
    [return int;]
}


```

```
定义一个无返回值的函数：
demoFun(){
	echo "this is my first shell function"
}
echo "start to call a function"
demoFun
```

```
定义一个有返回值的函数：
demoFun(){
	a=10
	b=20
	return $(($a+$b))
}
demoFun()
echo "function return value:$?"

注意：$?用来获取函数的返回值，调用完函数后，要马上取值，否则会被别的语句的执行返回值覆盖
```

```
函数参数
在Shell中，调用函数时可以向其传递参数。 和运行可执行文件时，输入参数的用法相同

funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73

输出结果：
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !

注意，$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

```

## 14.文件包含 ##
```
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename

实例：
test.sh 代码如下：
#!/bin/bash
# author:菜鸟教程
# url:www.runoob.com

url="http://www.runoob.com"

test2.sh 代码如下：
#!/bin/bash

#使用 . 号来引用test1.sh 文件
. ./test1.sh

# 或者使用以下包含文件代码
# source ./test1.sh

echo "菜鸟教程官网地址：$url"
```


