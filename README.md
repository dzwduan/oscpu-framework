# oscpu-framework

这是一个基于`verilator`的`RISC-V`CPU开发仿真框架。  

开发前请在`myinfo.txt`文件中填写报名`一生一芯`时的学号和自己的姓名。例如：

```
ID=202100001
Name=张三
```

# 开发环境

操作系统：[Linux Ubuntu v20.04](https://ubuntu.com/download/desktop)  

开发软件：[verilator](https://verilator.org/guide/latest/)、[gtkwave](http://gtkwave.sourceforge.net/)、[mill](https://github.com/com-lihaoyi/mil)

可以使用下面的命令一键安装搭建开发环境。

```shell
# 选择使用verilog语言开发
wget https://gitee.com/oscpu/oscpu-env-setup/raw/master/oscpu-env-setup.sh && chmod +x oscpu-env-setup.sh && ./oscpu-env-setup.sh -g && rm oscpu-env-setup.sh
# 选择使用chisel语言开发
wget https://gitee.com/oscpu/oscpu-env-setup/raw/master/oscpu-env-setup.sh && chmod +x oscpu-env-setup.sh && ./oscpu-env-setup.sh -g -c && rm oscpu-env-setup.sh
```

# 获取代码


# 从github上克隆代码
```shell
git clone --recursive -b 2022 https://github.com/dzwduan/oscpu-framework.git oscpu
```

如果子仓库克隆失败，可在`oscpu`目录下使用下面的命令重新克隆子仓库。

```shell
git submodule update --init --recursive
```

参与`一生一芯`还需要设置git信息。

```shell
# 使用你的编号和姓名拼音代替双引号中内容
git config --global user.name "2021000001-Zhang San"
# 使用你的邮箱代替双引号中内容
git config --global user.email "zhangsan@foo.com"
```

# 例程

`projects`目录用于存放工程文件夹，`projects`目录下的几个例程可用于了解如何基于`verilator`来开发仿真CPU。你可以在该目录下创建自己的工程。工程目录结构如下：

```shell
.
├── build.sc		# 存放chisel编译信息的文件，选择chisel语言时需要该文件
├── csrc			# 存放仿真c++源码的文件夹，接入香山difftest框架时不需要该文件夹
├── src				# 存放chisel源码的文件夹，选择chisel语言时需要该文件夹
└── vsrc			# 存放verilog源码的文件夹，选择verilog语言时需要该文件夹
```

我们提供了脚本`build.sh`用于自动化编译、仿真和查看波形。下面是`build.sh`的参数说明，也可在oscpu目录下使用`./build.sh -h`命令查看帮助。

```shell
-e 指定一个例程作为工程目录，如果不指定，将使用"cpu"目录作为工程目录
-b 编译工程，编译后会在工程目录下生成"build"子目录，里面存放编译后生成的文件
-t 指定verilog顶层文件名，如果不指定，将使用"top.v"作为顶层文件名
-s 运行仿真程序，即"build/emu"程序，运行时工作目录为"build"子目录
-a 传递给仿真程序的参数，比如：-a "1 2 3 ......"，多个参数需要使用双引号
-f 传递给c++编译器的参数，比如：-f "-DGLOBAL_DEFINE=1 -ggdb3"，多个参数需要使用双引号
-l 传递给c++链接器的参数，比如：-l "-ldl -lm"，多个参数需要使用双引号
-g 使用gdb调试仿真程序
-w 使用gtkwave打开工作目录下修改时间最新的.vcd波形文件
-c 删除工程目录下编译生成的"build"文件夹
-r 使用给定的测试用例集合进行回归测试，比如：-r "case1 case2"
-v 传递给verilator的参数，比如：-v "-Wall"，多个参数需要使用双引号
```

## 编译和仿真 

### cpu_diff

`projects/cpu_diff`目录下存放了`verilog`版本单周期`RISC-V` CPU例程源码，源码实现了`RV64I`指令`addi`。可以使用下面的命令编译和仿真。

```shell
# 编译仿真
./build.sh -e cpu_diff -b -s -a "-i inst_diff.bin"
```

### chisel_cpu

`projects/chisel_cpu`目录下存放了`chisel`版本单周期`RISC-V` CPU例程源码，源码实现了`RV64I`指令`addi`。可以使用下面的命令编译和仿真。

```shell
./build.sh -e chisel_cpu -s -a "-i inst_diff.bin" -b
```

## 查看波形

在`oscpu`目录下使用命令可以通过`gtkwave`查看输出的波形，其中`xxx`表示例程名。

```shell
./build.sh -e xxx -w
```

# 测试用例

`bin`目录下存放了`一生一芯`[基础任务](https://oscpu.github.io/ysyx/wiki/tasks/basic.html)需要使用的测试用例，具体说明详见[一生一芯基础任务测试用例说明](./bin/README.md)。

# 回归测试

一键回归测试用于自动化测试给定的测试用例集合，可以通过以下命令对CPU进行一键回归测试。该命令会将`bin`目录下指定子目录中所有`.bin`文件作为参数来调用仿真程序，其中`xxx`表示例程名。

```shell
# 使用"bin/cpu-tests"和"bin/riscv-tests"目录下的bin进行回归测试
./build.sh -e xxx -b -r "cpu-tests riscv-tests"
```

通过测试的用例，将打印`PASS`。测试失败的用例，打印`FAIL`并生成对应的log文件，可以查看log文件来调试，也可以另外开启波形输出来调试。

# 扩展

[RISC-V Unprivileged Spec](https://github.com/riscv/riscv-isa-manual/releases/download/Ratified-IMAFDQC/riscv-spec-20191213.pdf)

[RISC-V Privileged Spec](https://github.com/riscv/riscv-isa-manual/releases/download/Ratified-IMFDQC-and-Priv-v1.11/riscv-privileged-20190608.pdf)

[cpu-tests](https://github.com/NJU-ProjectN/am-kernels)

[riscv-tests](https://github.com/NJU-ProjectN/riscv-tests)

[AXI4 specification](http://www.gstitt.ece.ufl.edu/courses/fall15/eel4720_5721/labs/refs/AXI4_specification.pdf)
