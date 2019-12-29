
# RISCV Program Test

## riscv模拟环境的搭建

### openocd的环境搭建

由于openocd默认只给了jtag的调试程序，因此，建议直接下载官方的源码然后配置，编译，安装


    git clone https://github.com/ntfreak/openocd.git
    git submodule init
    git submodule update
    ./bootstrap
    mkdir build
    cd build
    ../configure --prefix=$RISCV --enable-remote-bitbang
    make
    make install

我这儿直接装到了我的riscv工具链的位置

而对于其他的东西，按照其中的README.md中的内容即可(我这边没有出任何问题)

### spike 和 riscvGDB的使用

此处的原理是这样的，spike作为模拟器，pk(proxy kernel)作为代理的核，在它们上面可以跑riscv的程序。同时对外提供TCP/IP的连接服务，openOCD(这是一个包含了各种调试驱动的软件)再通过remote-bitbang组件去连接spike。最后riscvGDB去远程连接openOCD，从而达到调试的目的。这一过程为:

编译:

    riscv64-unknown-elf-gcc -g -Og -o rot13-64.o -c rot13.c
    riscv64-unknown-elf-gcc -g -Og -T spike.lds -nostartfiles -o rot13-64 rot13-64.o

spike运行，指定端口:

    spike --rbb-port=9824 -m0x10000000:0x20000 rot13-64


启动openocd去连接spike

    openocd -f spike.cfg

启动gdb，连接openocd，开始调试

    riscv64-unknown-elf-gdb rot13-64
    target remote localhost:3333
    ...

这里spike.lds指定了程序段处于的位置，spike.cfg配置了openocd该如何去连接spike



