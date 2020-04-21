# makefile学习 (二)

---

前一篇[makefile学习 (一)](../demo1/SimpleMakefile.md)入门了，这一篇慢慢开始加深难度咯。

## 项目结构

项目文件相变多了丢丢(3个)~~不再是单个文件，而是文件之间有包含关系，以下的`makefile`均基于此项目结构进行说明。目录结构如下：
```text
.
├── hello.c
├── hello.h
├── main.c
└── makefile
```
 其中，main.c调用了头文件hello.h中声明的`sayHello()`这个方法，而hello.c是`sayHello()`的实现源文件。(项目结构很简单了，不用present源码了吧)

## 暴力的makefile

 以你的暴脾气，是不是一行就搞定了呢？如下。
 ```makefile
main: main.c hello.c hello.h
	gcc main.c hello.c -o main
 ```
固然可以，但是不推荐，这样就体现不出来`makefile`的优势了。那`makefile`都有些什么优势呢?

+ 自动跟踪文件的改动情况，生成对应的目标文件。也就是文件不改动的话，不用再重新编译或链接~~要知道在大项目中，这可以节约很多很多的时间。

+ 其他 (这个`其他`是不是很精髓？\坏笑)

如果这样写的话，只改动main.c，hello.c也会重新被编译一遍，进而连接生成目标文件"main".

## 完整的makefile及make命令执行流程

比较好的做法是，将每个实现的源文件单独编译，生成`.o`文件(在编译过程中，这个`.o`文件也叫目标文件(object file)，为了防止和makefile的目标文件(target file)混淆，以后都将其称为`.o`文件，其实二者的英文名称是不一样的)，如下。

```makefile
main: main.o hello.o
	gcc main.o hello.o -o main

main.o:main.c hello.h
	gcc -c main.c -o main.o

hello.o:hello.c hello.h
	gcc -c hello.c -o hello.o
```

这样，当改动了main.c，只需重新编译main.c了，进而再链接新的main.o和老的hell.o生成目标文件"main". 是不是速度会快一丢丢了呢？

嗯，这个有那么一点点复杂了，我们先搞清楚`make`命令是怎样一个执行的流程。

0. `make`命令寻找当前目录下的`makefile`，未找到的话就报错~~("make: *** No targets specified and no makefile found.  Stop.")

1. 默认情况下，`make`命令会寻找`makefile`中第一个不是以 `.` 开头目标，称之为"默认目标"(这种默认行为是可以通过设置特殊变量 `.DEFAULT_GOAL` 来更改的)。此例中，"默认目标"是"main"。我们之所以把"main"这条规则放在开头，是因为我们的预期就是生成可执行文件。

2. 找到了"main", `make`会优先依次处理它的依赖文件"main.o"和"hello.o"。这些`.o`文件是通过编译源文件得到的，只有对应规则依赖的源文件或头文件有改动或对应的`.o`文件不存在时，才会重新编译生成。

3. `make`遍历处理完所有的依赖后(其实，`make`并不知道`.c`，`.h`是什么东西，只会把它们当作`target`去搜寻，结果发现没有对应的`target`，`make`就傻傻地什么都不执行，但，此时，`make`会根据它们自己的规则，自动更新生成的C程序，比如Bison或Yacc生成的程序)。重编译这些`.o`文件后，`make`再决定是否需要链接生成"main"。只有"main"不存在，或者任一`.o`文件比"main"新的时候，才会链接生成"main"。

4. 最后，没有被依赖的规则，则不会执行。如果想要单独执行，就需要使用`make rule_name`的形式指定要执行的规则。如`make main.o`

## 具有普通变量的makefile

嗯。每次都要写重复写依赖文件名字，那么长一串，好难写啊！一不小心就写错了！没错，又该介绍makefile的新特征了~

+ makefile中可以设置普通变量，特殊变量~

+ 其他

这样，有了变量，makefile又可以精简一丢丢了，拿"main"这条规则举例吧~
```makefile
objects = main.o hello.o

main: $(objects)
	gcc $(objects) -o main
```
我们设置了一个叫"objects"的变量，是不是看起来简洁很多了。官方标准推荐，所有`.o`文件组成的一个列表命名应该为"objects", "OBJECTS", "objs", "OBJS", "obj" 或者 "OBJ "。


## 瘦身的makefile

啧啧~，文件还是不够简洁，我们继续利用makefile的特性精简吧。

+ 不必写出编译单个源文件的命令(`recipe`)，因为`make`有隐式规则采用"cc -c"命令从对应的源文件生成`.o`文件，此时，对应的`.c`源文件也会隐式的加入到依赖文件列表中。

+ 其他

我们的makefile又可以精简了(省略在依赖列表中声明`.c`和生成`.o`的编译命令)

```makefile
objects = main.o hello.o

main: $(objects)
	gcc $(objects) -o main

main.o: hello.h

hello.o: hello.h

```

## 再瘦身的makefile

额~~什么？还不够精简吗？是的，所有`.o`文件都仅依赖同样的依赖文件"hello.h"。召唤神龙，makefile特性又出来了。

+ 当所有`.o`文件通过隐式规则创建的时候，它们可以根据`prerequisites`分组，而不是根据`target`分组。

+ 其他

```makefile
objects = main.o hello.o

main: $(objects)
	gcc $(objects) -o main

$(objects): hello.h
```

他们都依赖于"hello.h"，所以根据"hello.h"分组，这儿比较巧合，刚好所有的`.o`文件都仅依赖"hello.h"。

## 瘦不动了的makefile

你以为这就完了吗？No！它说它还能更简洁。这儿直接给出栗子吧， `$@` 表示目标文件，`$^`或`$+`表示所有依赖文件，这样也能避免把文件名写错了,写多个`rule`的时候，只需copy，然后修改`target`名和依赖文件名即可。

```makefile
objects = main.o hello.o

main: $(objects)
	gcc $^ -o $@

$(objects): hello.h
```

## 其他小知识

+ `makefile`的名字可以是"GNUmakefile"(GNU make特有)、"makefile"、"Makefile"，`make`命令会按照这个顺序搜索。官方推荐取名为"Makefile"，因为它会显示在目录列表的前面(用`ls -l`看看效果哦)。当然了，也可以用`-f filename`或`--file=filename`指定`makefile`的文件名。

+ 如果`make`没有搜索到这些文件名，那么就必须指定一个目标，`make`会尝试使用隐式规则去生成。假如当前文件夹中没有"makefile"，也可以使用`make main.o`来生成。

+ `makefile`可以使用反斜杠`\`换行。

``` 
xx \   # \前有一个空格
xx
# 等价于 xx xx


xx$\   # \前有一个$
xx
# 等价于 xxxx, 其推导规则是`xx$ xx`, 而 `$ `，是一个不存在的变量，会被替换成空字符，所以得到的结果是`xxxx`


xx\    # \前没有空格
xx
# 等价于 xx\xx 此时会有
```

+ 其他

> 参考
>
> [GNU make](https://www.gnu.org/software/make/manual/make.html)

