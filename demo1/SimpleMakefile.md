# makefile学习 (一)

---

## makefile的组成

`makefile` 由一系列`规则(rule)`组成，这些`规则`的基本格式如下：
```makefile
target … : prerequisites …
           recipe
           …
           …
```
+ `target`: 通常是生成的文件的名字也可以是将要执行的动作的名字，比如`clean`. (如果你执意要随便给 `target` 取名也没毛病，看个人喜好咯)

+ `prerequisites`: 是用于生成`target`的依赖文件，一个`target`可以依赖于多个`prerequisites`，也可没有依赖文件。即使有依赖，很多情况下也可以不写(极其不推荐这种不规范的操作).

+ `recipe`: 是`make`执行的动作，可以包含多个命令语句，这些命令可以在同一行，也可以在不同行上(简单理解`recipe`就是linux的shell命令)。 note: `recipe`命令前默认必须有一个tab符(不系统地学习，很容易吃这个亏~~懂的小朋友都流下了眼泪~~)。当然，也可以通过设置`.RECIPEPREFIX`变量来更改。

`规则`解释了如何以及何时根据`prerequisites`生成或更新`target`。


除了`规则`之外，`makefile`也会包含一些其他文本，比如，变量(前文所述的`.RECIPEPREFIX`就是一个特殊的变量)等。


## 举个栗子

讲完了，最最最基本的`makefile`的组成，我们举个栗子吧。我们只有一个基本的main.c的文件, 并且`makefile`与main.c在同一个目录下。
```makefile
# 将 $ 设置为recipe的引导符号
.RECIPEPREFIX = $

main: main.c
$ gcc main.c -o main
# 演示一下这些命令在同一行吧,其实就是linux shell 命令的连接&
$ cat main.c & wc main.c

# 恢复成默认的tab
.RECIPEPREFIX =

clean:
# rm -rf main
# 命令前加上@符号，则不会在终端将该命令显示出来
	@rm -rf main

```

## 打个比喻

我们再打个比喻吧，把`makefile`理解成做菜的清单，比如：做"回锅肉"的`makefile`应该长成这样，当然了，菜单种可能还有"红烧肉"、"水煮肉"... 哇，我饿了！
```makefile
回锅肉: 五花肉 其他佐料 燃气灶
    洗 五花肉 其他佐料
    切 五花肉 其他佐料
    开 燃气灶
    炒 10分钟
#( 此时, 香喷喷的"红烧肉"就出锅了)
```
`make`命令是一个勤劳的厨师，给他菜单他就会帮你做出来对应的菜，比如：`make 回锅肉`，厨师就会帮你做好回锅肉啦！

> 参考
>
> [GNU make](https://www.gnu.org/software/make/manual/make.html)
