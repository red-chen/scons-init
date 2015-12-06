# simple_scons
基于scons 2.4封装的C/C++项目构建工具

## 安装scons
* 打开bashrc
  * > vim ~/.bashrc
* 在scons的目录加载到bashrc末尾
  * > alias scons={abs_path}/tools/scons-2.4.1/script/scons
* 使命令生效
  * > source ~/.bashrc

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
    * 根目录下的include文件夹：env.Append(CPPPATH=['#/include'])
    * 当前目录下的include文件夹：env.Append(CPPPATH=['include'])
    * 系统目录目录下的include文件夹：env.Append(CPPPATH=['/usr/include'])
* 怎么导入依赖的库的路径？
  * 在编译之前调用env的Append函数，向LIBPATH中添加包所在的路径
    * 根目录下的include文件夹：env.Append(LIBPATH=['#/lib'])
    * 当前目录下的include文件夹：env.Append(LIBPATH=['lib'])
    * 系统目录目录下的include文件夹：env.Append(LIBPATH=['/usr/lib'])

