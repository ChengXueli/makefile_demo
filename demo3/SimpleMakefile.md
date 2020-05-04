# makefile学习 (三)

---

此篇，单独讲`makefile`中的几种简单的变量。包括基本变量、多行变量、自动变量。

## 1. 基本变量

+ 使用 `=`、`:=`、`::=`、`?=`，他们区别如下：
    + `=` 直接赋值，可递归引用，可引用之后赋值的变量
    + `:=`与`::=`等价，均表示一次性展开变量、不会递归引用。(老版本的make可能不支持`::=`） 
    + `?=` 如果变量没有赋值，才会进行赋值
    + `+=` 追加赋值，并且中间会加入一个空格

+ 引用方式为 `${name}` 或 `$(name)`，如果变量名是单个字符，也可省略花括号或者括号。
+ 变量名可以使用除了 ":"、 "#"、 "=" 和空白字符之外的任何字符。(个人觉得用字母就够了。另外，在未来的版本中，以"."和大写字母开头的变量可能会被赋予特殊的含义)
+ 变量名是大小写敏感的。
+ 变量在读取`makefile`的时候就会被展开。

```makefile
# 最基础的赋值
hello = hello makefile
# 数字变量名，不推荐
123 = 123
# 引用之后赋值的变量
a = $x
# 无限递归自引用，会报错
# b = $b $x
# 使用一次性展开赋值，此时等号右边的$b和$x的值都是空，赋值完后，等号左边的b表示的值也是空
b := $b$x

c = first
# 覆盖之前的赋值
c = second
# 已经赋过值，此时不再赋值
c ?= third

d = abc
d += def

x = x
all:
	@echo \"${hello}, $(123) $a $b $c $d\"


# 输出结果 "hello makefile, 123 x second abc def"

```

## 2. 多行变量
+ 形如 
```makefile
define name 
line1
line2
...
linen
endef
``` 

+ 等价于`name = line1; line2; ...; linen`

```makefile
# 多行变量
define multi-line
123
echo abc
echo 一二三
endef

all:
	@echo ${multi-line}

#输出三行，每行分别是"123"、"abc"、"一二三"

```

## 3. [自动变量](https://www.gnu.org/software/make/manual/make.html#Automatic-Variables)

只介绍几个常用的，不常用(如，`$%`、`$|`、`$*`等)的请参考[官网](https://www.gnu.org/software/make/manual/make.html#Automatic-Variables)。

+ $@: 表示target名字
+ $<: 表示第一个依赖文件名字
+ $^：表示所有依赖文件(会去掉重复的依赖)
+ $+：表示所有依赖文件(不会去重)
+ $?：表示比目标更新的依赖目标

当前文件夹结构
```
./
├── 1
├── SimpleMakefile.md
├── main
├── main.c
└── makefile

0 directories, 5 files
```
`makefile` 执行如下：
```makefile
# 特殊变量
main:main.c 1 1 main.c
	@gcc -o $@ main.c
	@echo \"[$<] [$^] [$+] [$?]\"

# 第一次执行make时，输出"[main.c] [main.c 1] [main.c 1 1 main.c] [main.c 1]"
# 修改"1"后再make, 输出"[main.c] [main.c 1] [main.c 1 1 main.c] [1]"
# 可以看到$?对应的输出只有"1"了，因为只修改了它，它比目标新
```
## 4. 其他

用例子吧
```makefile
x = y
y = z
a = ${$x}
# => a = $(y) = z

all:
	@echo \"$a\"

# 输出"z"
```


> 参考
>
> [GNU make](https://www.gnu.org/software/make/manual/make.html)