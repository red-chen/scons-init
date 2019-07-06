# -*- mode: python -*-
# -*- coding: utf-8 -*-

# User guid : http://scons.org/doc/production/HTML/scons-user.html
# Scons保留关键字
# LINKFLAGS 连接的Flag

import sys
import os
import os.path
import SCons.Util

env = Environment() 
env.Decider('MD5-timestamp')

# =============================================================================
## Variables
## 输入变量设置
# =============================================================================

vars = Variables()
vars.Add(EnumVariable('mode', '编译选项', 'debug', allowed_values=('debug', 'release')))
vars.Update(env)
Help(vars.GenerateHelpText(env))

# =============================================================================
## AddMethod
## 公共方法定义
# =============================================================================

# 添加子目录
def aSubs(self, *dirs):
    return self.SConscript(dirs=Flatten(dirs), exports={'env':self})
env.AddMethod(aSubs)

# 编译目标程序
def aProgram(self, target=None, source=None, **kwargs):
    target = self.Program(target, source, CFLAGS=self['CFLAGS'], CPPPATH=self['CPPPATH'], **kwargs)
    install = self.Install(self['BIN_DIR'], target)
    self.Default(install)
    return target
env.AddMethod(aProgram)

# 生成静态库
def aStaticLibrary(self, target=None, source=None, **kwargs):
    target = self.StaticLibrary(target, source, **kwargs)
    self.Default(env.Install(self['LIB_DIR'], target))
    return target
env.AddMethod(aStaticLibrary)

# 生成动态库
def aSharedLibrary(self, *args, **kwargs):
    shlink_flags = SCons.Util.CLVar(self.subst('$SHLINKFLAGS'))
    target = self.SharedLibrary(SHLINKFLAGS=shlink_flags, *args, **kwargs)
    self.Default(self.Install(self['LIB_DIR'], target))
    return target
env.AddMethod(aSharedLibrary)

# 打包发布包
env.Append(BUILDERS = {"_Md5sum": Builder(
                                  action = "md5sum $SOURCE > $TARGET", 
                                  src_suffix=".tar.gz",
                                  suffix = ".tar.gz.md5"
                                  )})
env.Append(BUILDERS = {"_Package": Builder(
                                  action = "tar czvf $TARGET -C $new_source `ls $new_source`", 
                                  suffix = ".tar.gz")})

def aPackage(self, target, files):
    path = "%s/package/temp"%(env['PROJECT_DIR'])
    if not os.path.isdir(path):
        os.makedirs(path)

    pg = self._Package(
        target = "#package/%s"%(target),
        source = [],
        # 不用source，target和source会用于判断依赖 
        new_source='package/temp/%s'%(target)
    )

    for t, s in files:
        install = env.Install('#package/temp/%s/%s' % (target, t), s)
        self.Depends(pg, install)

    md5 = self._Md5sum("#package/%s"%(target))
    self.Default(pg)
    self.Depends(md5, pg)
    self.Default(md5)
env.AddMethod(aPackage)

# =============================================================================
# 预置公共使用的环境变量
# '#' 表示根目录
# =============================================================================
env['PROJECT_DIR'] = os.getcwd()
env['PROJECT_ROOT'] = '#'
env['BUILD_DIR'] = env['PROJECT_ROOT'] + '/build/' + env['mode']
env['HEADER_DIR']= env['BUILD_DIR'] + '/include/'
env['LIB_DIR']   = env['BUILD_DIR'] + '/lib/'
env['BIN_DIR']   = env['BUILD_DIR'] + '/bin/'

if env['mode'] in ['debug']:
    env.MergeFlags('-O0')
else:
    env.MergeFlags('-O2')

# 警告也报错
env.MergeFlags('-Wall')

# 开启可调试
env.MergeFlags('-g')

# 支持C99规范
env.MergeFlags('-D_GNU_SOURCE')
env.MergeFlags('-D_XOPEN_SOURCE=500')
env.MergeFlags('-std=c99')

env.Append(CPPPATH=[])

# =============================================================================
## 初始化工作空间
## 将当期的所有文件link到BUILD_DIR下
# =============================================================================
def _makeBuildDir():
    for d in [env['BUILD_DIR'], env['HEADER_DIR'], env['LIB_DIR'], env['BIN_DIR']]:
        d = env.Dir(d).abspath
        if not os.path.exists(d):
            os.makedirs(d)
        assert os.path.isdir(d)

def _cleanBuildDir():
    buildDir = env.Dir(env['BUILD_DIR']).abspath
    for rt, dirs, files in os.walk(buildDir):
        try:
            dirs.remove('.git')
        except:
            pass
        for f in files:
            f = os.path.join(rt, f)
            if os.path.islink(f) or f.endswith('.gcno') or f.endswith('.gcda'):
                os.remove(f)

def _firstDirName(p):
    x = p
    y = os.path.dirname(p)
    while len(y) > 0:
        x = y
        y = os.path.dirname(x)
    return x

def _cloneFile(rt, fn):
    d = os.path.join(env.Dir(env['BUILD_DIR']).path, rt)
    if not os.path.exists(d):
        os.makedirs(d)
    os.symlink(os.path.abspath(os.path.join(rt, fn)), os.path.join(d, fn))

def _cloneWorkspace():
    buildDir = _firstDirName(env.Dir(env['BUILD_DIR']).path)
    paths = os.listdir('.')
    for x in [buildDir, '.git', '.gitignore', '.sconsign.dblite', 'SConstruct']:
        try:
            paths.remove(x)
        except:
            pass
    for x in paths:
        if os.path.isfile(x):
            _cloneFile('', x)
        if os.path.isdir(x):
            for rt, _, files in os.walk(x):
                for f in files:
                    _cloneFile(rt, f)

_makeBuildDir()
_cleanBuildDir()
_cloneWorkspace()

# 入口
env.SConscript('$BUILD_DIR/SConscript', exports='env')
