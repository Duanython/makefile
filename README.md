##### Makefile Memo

> ###### Overview of `make`
>
> > The `make` utility automatically determines which pieces of a large program need to be recompiled, and issues commands to recompile them
>
> - our examples show `C` programs, since they are most common
> - use `make` with any programming language whose compiler can be run with a shell command.
> - or use it to describe any task where some files must be updated automatically from others whenever the others change, such as `git`
>
> > The `make` program uses the `makefile` database and the last-modification times of the files to decide which of the files need to be updated. For each of those files, it issues the shell scripts recorded in the `makefile`.
##### 基本格式

```shell
make
make $(targets)
```

You can provide command line arguments to make to control which files should be recompiled

```makefile
targets : prerequisties
[tab键]command
```
- target：目标文件，可以是 ObjectFile，也可以是执行文件，还可以是一个标签（Label），对于标签这种特性，在后续的“伪目标”章节中会有叙述。
- prerequisite：要生成那个 target 所需要的文件或是目标。
- command：是 make 需要执行的命令。
- 只要有这个声明，不管是否有 "clean" 文件/文件夹，要运行 "clean" 这个目标，只有"make clean" 这个命令
##### Makefile 规则

- `make` 会在当前目录下找到一个名字叫 `Makefile` 或 `makefile` 的文件
- 如果找到，它会找文件中第一个目标文件（`target`），并把这个文件作为最终的目标文件
- 如果 `target` 文件不存在，或是 `target` 文件依赖的 .o 文件(`prerequities`)的文件修改时间要比 `target`这个文件新，就会执行后面所定义的命令 `command` 来生成 `target` 这个文件      
- 如果 `target` 依赖的 .o 文件（`prerequisties`）也存在，`make`会在当前文件中找到 target 为 .o 文件的依赖性，如果找到，再根据那个规则生成 .o 文件

> 综上所述：
>
> `targets`就像树中的非叶结点
>
> `prerequisties`像`targets`的孩子结点
>
> `command`是`targets`的值，必要时执行它

```makefile
# $() is variables , function and arguments in it 
cpp_srcs := $(shell find src -name *.cpp)
pp_files := $(patsubst src/%.cpp,src/%.i,$(cpp_srcs))
# % 是通配符	@ 不打印该命令
src/%.i : src/%.cpp
	@g++ -E $^ -o $@

preprocess : $(pp_files)

clean :
	@rm -f src/*.i

debug :
	@echo $(pp_files)
# pesudo-targets declaration
# otherwise it will find the target file firstly
# if the target file same as pesudo-targets already up-to-date,
# the shell command under the pesudo-targets won't run.
.PHONY : debug preprocess clean
```

