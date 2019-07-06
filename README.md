# Simple Scons
基于scons 2.4封装的C/C++项目构建工具，支持编译二进制程序、静态库、动态库

## 安装scons

## 编译
* 在顶级目录全量编译
  * > scons
  * > scons -c

* 在子目录编译
  * > scons -U
  * > scons -U -c

## FAQ
* 怎么导入依赖的头文件？
  * 在编译之前调用env的Append函数，向CPPPATH中添加头文件的路径
    * 导入根目录下的include文件夹：env.Append(CPPPATH=['#/include'])
    * 导入当前目录下的include文件夹：env.Append(CPPPATH=['include'])
    * 导入系统目录目录下的include文件夹：env.Append(CPPPATH=['/usr/include'])
* 怎么导入依赖的库的路径？
  * 在编译之前调用env的Append函数，向LIBPATH中添加包所在的路径
    * 导入根目录下的lib文件夹：env.Append(LIBPATH=['#/lib'])
    * 导入当前目录下的lib文件夹：env.Append(LIBPATH=['lib'])
    * 导入系统目录目录下的lib文件夹：env.Append(LIBPATH=['/usr/lib'])

