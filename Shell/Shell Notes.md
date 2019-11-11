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




