# 将 $ 设置为recipe的引导符号
.RECIPEPREFIX = $

main: main.c
$ gcc main.c -o main
# 演示一下这些命令在同一行吧,其实就是linux shell 命令的连接&
$ cat main.c & wc main.c
# 也可以用分号
$ cat main.c ; wc main.c

# 恢复成默认的tab
.RECIPEPREFIX =

clean:
# rm -rf main
# 命令前加上@符号，则不会在终端将该命令显示出来
	@rm -rf main