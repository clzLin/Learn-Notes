**读陈皓的《跟我一起写Makefile》，学习makefile语法**
GNU的make，环境 是RedHat Linux 8.0，make的版本是3.80
## 1. makefile核心规则 ##
```
target ... : prerequisites ...
    command	//前面必须要有个Tab键
    ...
    ...
```
- target
  可以是一个object file（目标文件），也可以是一个执行文件，还可以是一个标签（label）
- prerequisites
  生成该target所依赖的文件
- command
  该target要执行的命令（任意的shell命令），命令前面必须要敲1个Tab


**makefile的核心规则就是上面的这条，体现的是一个文件的依赖关系，**
prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。

```
实例1:
edit : main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o

edit是最终的可执行文件，依赖8个.o文件，下面是8个.o文件各自的依赖关系和生成时的执行命令。
clean不是文件，所以:后面没有依赖文件，更像一个标签(label)，所以执行make时不会去找
clean的依赖文件，也不会执行后面的命令，那么我们想要执行后面的命令就得显示地写出标签。
make clean就会执行clean下的命令。
除了clean，还可以自定义一些别的和编译无关的命令，如打包、备份等
zip :
	tar -czvf code.tar.gz
```

## 2. make是如何工作的 ##
```
在写好makefile文件后，
1. 在同一目录下，输入make命令
2. make在当前目录下找到名字为"Makefile"或"makefile"的文件
3. 锁定文件中第一个目标文件为最终文件，以上例子就是edit这个文件
4. 若edit文件不存在或其后面依赖的.o文件修改日期比edit文件新，就会执行edit下的命令
5. 若edit依赖的.o文件不存在，或该.o文件下的依赖文件(.c .h)的修改日期比.o文件新，就会
   执行该.o文件下的命令。
6. 最终每个.o文件生成后，执行了edit下的命令后，生成了edit文件。

所以如果改变了main.c，main.o下的命令就会执行，重新生成新的main.o，然后因为main.o有
所更新，所以edit下的命令也会执行，从而生成新的edit文件。
```

## 3. makefile中使用变量 ##
根据上面的例子，
```main.o kbd.o command.o display.o insert.o search.o files.o utils.o```被写了多次，不利于维护，那就用一个变量来代替他，或者更像宏定义。
```
objects = main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit $(objects)
```

## 4. make自动推导 ##
GNU的make很强大，它可以自动推导文件以及文件依赖关系后面的命令，于是我们就没必要去在每一个 .o 文件后都写上类似的命令，因为，我们的make会自动识别，并自己推导命令。
```
main.o会自动依赖main.c(根据文件名)，且会根据除了main.c的自己写的别的依赖文件，自动推
导执行命令。
例：
objects = main.o kbd.o command.o display.o \
    insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)

main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h

.PHONY : clean
clean :
    rm edit $(objects)
```
这就是make的隐晦规则。

## 5. Makefile五大项 ##
Makefile里主要包含了五个东西：显式规则、隐晦规则、变量定义、文件指示和注释。
前三个上面都讲了，
```
4. 文件指示其包括了三个部分，
   - 一个是在一个Makefile中引用另一个Makefile，就像C语言中的include一样；
   - 另一个是指根据某些情况指定Makefile中的有效部分，就像C语言中的预编译#if一 样；
   - 还有就是定义一个多行的命令。有关这一部分的内容，我会在后续的部分中讲述。

5. 注释： Makefile中只有单行注释，和UNIX的Shell脚本一样，用#字符

```

## 6. 引用其它的Makefile ##
使用 include 关键字可以把别的Makefile包含进来，这很像C语言的 #include ，被包含的文件会原模原样的放在当前文件的包含位置。

```
include <filename>
filename 可以是当前操作系统Shell的文件模式（可以包含路径和通配符）。
include 前面不能有Tab键

```
```
举个例子，你有这样几个Makefile： a.mk 、 b.mk 、 c.mk ，还有一个文件叫 foo.make ，
以及一个变量 $(bar) ，其包含 了 e.mk 和 f.mk ，那么，下面的语句：

include foo.make *.mk $(bar)
等价于：
include foo.make a.mk b.mk c.mk e.mk f.mk

```

## 7.书写规则 ##
最前面就列出了基本的书写格式
```
targets : prerequisites
    command
    ...

或者可以是：
targets : prerequisites ; command
    command
    ...

不与 “targets : prerequisites” 同一行的command要在前面加Tab键

make会以UNIX的标准Shell，也就是 /bin/sh 来执行命令。
```

## 8.支持的三个通配符(* ? ~) ##
这是和Unix的B-Shell是相同的。
波浪号（ ~ ）字符在文件名中也有比较特殊的用途。如果是 ~/test ，这就表示当前用户 的 $HOME 目录下的test目录。而 ~hchen/test 则表示用户hchen的宿主目录下的test 目录。（这些都是Unix下的小知识了，make也支持）而在Windows或是 MS-DOS下，用户没有宿主目录， 那么波浪号所指的目录则根据环境变量“HOME”而定。

星号（ * ）字符： *.c表示所有后缀为.c的文件。
```
clean:
	rm -f *.o

objects = *.o (注意make中的变量相当于C语言的宏定义，只是替换，不会将所有.o文件展开)
```

## 9.文件搜寻 ##
因为makefile中依赖关系中存在许多.c和.h文件，而我们项目中都会把这些文件进行分类，放在不同的文件夹内，那makefile的依赖中就需要指定文件的路径，那会很麻烦，windows下的IDE可以设置头文件路径，那么Makefile中使用特殊变量**VPATH**来完成这一功能
```
VPATH = src:../headers
当make在当前目录下找不到想要的文件时，就会去VPATH指定的路径下再去查找。 
先去src目录下，再去上一层目录的headers目录下
目录用:隔开。
```

另一个设置文件搜索路径的方法是使用make的“vpath”关键字（注意，它是全小写的），这不是变量，这是 一个make的关键字，这和上面提到的那个VPATH变量很类似，但是它更为灵活。它可以指定不同的文件在不 同的搜索目录中。这是一个很灵活的功能。它的使用方法有三种：
```
vpath <pattern> <directories>
为符合模式<pattern>的文件指定搜索目录<directories>。

vpath <pattern>
清除符合模式<pattern>的文件的搜索目录。

vpath
清除所有已被设置好了的文件搜索目录。
```

<pattern>需要包含 % 字符。 % 的意思是匹配零或若干字符，（需引用 % ，使用 \ ）例如， %.h 表示所有以 .h 结尾的文件。<pattern>指定了要搜索 的文件集，而<directories>则指定了< pattern>的文件集的搜索的目录。例如：
```
vpath %.h ../headers
该语句表示，要求make在“../headers”目录下搜索所有以 .h 结尾的文件。（如果某文件在当前
目录没有找到的话）
```

我们可以连续地使用vpath语句，以指定不同搜索策略。如果连续的vpath语句中出现了相同的<pattern> ，或是被重复了的<pattern>，那么，make会按照vpath语句的先后顺序来执行搜索。
```
vpath %.c foo
vpath %.c blish
vpath %.c bar
其表示 .c 结尾的文件，先在“foo”目录，然后是“blish”，最后是“bar”目录。

vpath %.c foo:bar
vpath %.c blish
而上面的语句则表示 .c 结尾的文件，先在“foo”目录，然后是“bar”目录，最后才是“blish”目录。
```

## 10.伪目标 ##
```
clean:
	rm -f *.o
```
clean就是一个伪目标

有个规定，伪目标名字不能和文件夹内的文件重名，所以可以使用".PHONY"来显式地指明一个目标是伪目标。所以完整的写法如下
```
.PHONY : clean
clean :
	rn -f *.o
```
伪目标一般没有依赖的文件。但是，我们也可以为伪目标指定所依赖的文件。伪目标同样可以作为“默认目 标”，只要将其放在第一个。一个示例就是，如果你的Makefile需要一口气生成若干个可执行文件，但你只 想简单地敲一个make完事，并且，所有的目标文件都写在一个Makefile中，那么你可以使用“伪目标”这个特性：
```
all : prog1 prog2 prog3
.PHONY : all

prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o

prog2 : prog2.o
    cc -o prog2 prog2.o

prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```
我们知道，Makefile中的第一个目标会被作为其默认目标。我们声明了一个“all”的伪目标，其依赖于其它 三个目标。由于默认目标的特性是，总是被执行的，但由于“all”又是一个伪目标，伪目标只是一个标签不 会生成文件，所以不会有“all”文件产生。于是，其它三个目标的规则总是会被决议。也就达到了我们一口 气生成多个目标的目的。 .PHONY : all 声明了“all”这个目标为“伪目标”。（注：这里的显式 “.PHONY : all” 不写的话一般情况也可以正确的执行，这样make可通过隐式规则推导出， “all” 是一 个伪目标，执行make不会生成“all”文件，而执行后面的多个目标。建议：显式写出是一个好习惯。）

从上面的例子我们可以看出，目标也可以成为依赖。所以，伪目标同样也可成为依赖。看下面 的例子：
```
.PHONY : cleanall cleanobj cleandiff

cleanall : cleanobj cleandiff
    rm program

cleanobj :
    rm *.o

cleandiff :
    rm *.diff
```
“make cleanall”将清除所有要被清除的文件。“cleanobj”和“cleandiff”这两个伪目标有点像“子程序”的意思。我们可以输入“make cleanall”和“make cleanobj”和“make cleandiff”命令来达到清除不同种类文件的目的。

## 11.多目标 ##
有可能我们的多个目标同时依赖于一个文件，并且其生成的命令大体类似，使用自动化变量$@，这个变量表示着目前规则中所有的目标的集合，看下例：
```
bigoutput littleoutput : text.g
	generate text.g -$(subst output,,$@) > $@

这里的 "$@" 就等价于 [bigoutput,littleout],
subst是makefile函数，作用是字符串替换，参数1是被替换字符串，参数2是替换字符串，参数3
是被替换操作的字符串。
第一次 $@ 取出bigoutput，执行subst函数后，bigoutput中的output被‘空’替换了(参数3是空)
所以上面的命令整个展开如下：
bigoutput : text.g
    generate text.g -big > bigoutput
littleoutput : text.g
    generate text.g -little > littleoutput
```

## 11.静态模式 ##
更加容易定义多目标的规则，让我们的规则变得更加的有弹性和灵活。
```
<targets ...> : <target-pattern> : <prereq-patterns ...>
    <commands>
    ...

targets定义了一系列的目标文件，可以有通配符。是目标的一个集合。

target-parrtern是指明了targets的模式，也就是的目标集模式。

prereq-parrterns是目标的依赖模式，它对target-parrtern形成的模式再进行一次依赖目标
的定义。

举个例子来说明一下吧。如果我们的<target-parrtern>定义成 %.o ，意思是我们的<target>;
集合中都是以 .o 结尾的，而如果我们的<prereq-parrterns>定义成 %.c ，意思是对
<target-parrtern>所形成的目标集进行二次定义，其计算方法是，取<target-parrtern>模式
中的 % （也就是去掉了 .o 这个结 尾），并为其加上 .c 这个结尾，形成的新集合。
例：
objects = foo.o bar.o
$(objects) : %.o : %.c
等价于：
foo.o : foo.c
bar.o : bar.c

main.s fun.s : %.s : %.c
等价于：
main.s : main.c
fun.s  : fun.c
```
### 例子1：###
```
objects = foo.o bar.o

all: $(objects)

$(objects): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
$< 代表第一个依赖文件。
$@ 在这里是foo.o bar.o的集合

所以等价于

foo.o : foo.c
    $(CC) -c $(CFLAGS) foo.c -o foo.o
bar.o : bar.c
    $(CC) -c $(CFLAGS) bar.c -o bar.o
```
如果有很多类似的.o文件需要生成，都是一个.o由同名的一个.c编译生成，那么只要上面4行就可以了，提高效率。

### 例子2：###
```
files = foo.elc bar.o lose.o

$(filter %.o,$(files)): %.o: %.c
    $(CC) -c $(CFLAGS) $< -o $@
$(filter %.elc,$(files)): %.elc: %.el
    emacs -f batch-byte-compile $<

等价于

bar.o : bar.c
	$(CC) -c $(CFLAGS) bar.c -o bar.o
lose.o : lose.c
	$(CC) -c $(CFLAGS) lose.c -o lose.o
foo.elc : foo.el
	emacs -f batch-byte-compile foo.el

$(filter %.o,$(files))表示调用Makefile的filter函数，过滤“$files”集，只要其中模式 
为“%.o”的内容。这个例子展示了Makefile中更大的弹性。
```




