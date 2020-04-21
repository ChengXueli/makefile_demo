# 分割符与分割符之间的规则都是可以单独执行的
# --------------------------------------
# 我是一条分割符
# --------------------------------------

# main: main.c hello.c hello.h
# 	gcc main.c hello.c -o main

# clean:
# 	@rm -rf main main.o hello.o

# --------------------------------------
# 我是一条分割符
# --------------------------------------

# main: main.o hello.o
# 	gcc main.o hello.o -o main

# main.o:main.c hello.h
# 	gcc -c main.c -o main.o

# hello.o:hello.c hello.h
# 	gcc -c hello.c -o hello.o

# clean:
# 	@rm -rf main main.o hello.o

# --------------------------------------
# 我是一条分割符
# --------------------------------------

# objects = main.o hello.o

# main: $(objects)
# 	gcc $(objects) -o main

# main.o:main.c hello.h
# 	gcc -c main.c -o main.o

# hello.o:hello.c hello.h
# 	gcc -c hello.c -o hello.o

# clean:
# 	@rm -rf main $(objects)

# --------------------------------------
# 我是一条分割符
# --------------------------------------
# objects = main.o hello.o

# main: $(objects)
# 	gcc $(objects) -o main

# main.o: hello.h

# hello.o: hello.h

# clean:
# 	@rm -rf main $(objects)

# --------------------------------------
# 我是一条分割符
# --------------------------------------
# objects = main.o hello.o

# main: $(objects)
# 	gcc $(objects) -o main

# $(objects): hello.h

# clean:
# 	@rm -rf main $(objects)


# --------------------------------------
# 我是一条分割符
# --------------------------------------

objects = main.o hello.o

main: $(objects)
	gcc $^ -o $@

$(objects): hello.h

clean:
	rm -rf main \
	$(objects)
# --------------------------------------
# 我是一条分割符
# --------------------------------------

