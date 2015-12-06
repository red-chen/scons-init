# simple_scons
基于scons 2.4封装的C/C++项目构建工具

## 安装scons
* 打开bashrc
	vim ~/.bashrc
* 在scons的目录加载到bashrc末尾
	alias scons={abs_path}/tools/scons-2.4.1/script/scons
* 使命令生效
	source ~/.bashrc

## 编译
* 在顶级目录全量编译
	scons
	scons -c

* 在子目录编译
	scons -U
	scons -U -c
