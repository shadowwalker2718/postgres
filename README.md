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

----
✔ ~/database/postgres [henrywu {origin/henrywu}|✔] 
mkdir -p install/data

./configure --prefix=/home/henry/database/postgres/install/ --enable-depend --enable-cassert --enable-debug

make -j 8 && make install




✔ ~/database/postgres/install [henrywu {origin/henrywu}|✔] 
06:26 $ pwd
/home/henry/database/postgres/install
✔ ~/database/postgres/install [henrywu {origin/henrywu}|✔] 
06:26 $ ./bin/initdb /home/henry/database/postgres/install/data/
The files belonging to this database system will be owned by user "henry".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /home/henry/database/postgres/install/data ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default timezone ... America/Los_Angeles
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    ./bin/pg_ctl -D /home/henry/database/postgres/install/data/ -l logfile start


06:26 $ ./bin/pg_ctl -D /home/henry/database/postgres/install/data/ -l logfile start
waiting for server to start.... done
server started

✔ ~/database/postgres [henrywu {origin/henrywu}|✚ 1] 
06:34 $ ./install/bin/pg_ctl -D /home/henry/database/postgres/install/data/ -l logfile stop
waiting for server to shut down.... done
server stopped
