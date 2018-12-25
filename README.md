clion怎么调试postgresql呢？
clion使用cmake去编译项目的，但是大家编译postgresql用的是make。虽然项目中也有CMakeLists.txt文件，但是cmake会报错，因此我们要修改使用cmake调用make去做编译，就好像在java中用maven调用ant编译一样。

安装步骤
把源码从git上clone下来
安装依赖库：readline、bison和flex等
运行
sh configure --prefix=$HOME/project --enable-depend --enable-cassert --enable-debug
把其中的$HOME/project改成你想安装postgresql的目录

运行make
运行make install
使用initdb初始化数据库，指定数据目录
使用Clion导入postgresql源码
修改CMakeLists.txt,内容如下
cmake_minimum_required(VERSION 3.6)
project(postgres)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
add_custom_target(postgres COMMAND make -C ${postgres_SOURCE_DIR})


打开debug configuration，增加一个Application，Target选postgres，Executable选择到源码目录的src/backend/postgres，程序参数写 -D 数据目录

然后就可以愉快的调试代码了，主函数在src/backend/main/main.c

