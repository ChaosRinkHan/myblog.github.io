---
title: cx_Freeze发布Python程序的一些经验
date: 2018-03-31 22:44:37
tags:
  - Python
  - PyQt
---

﻿前言
==

最近用Python写了一个化工方面的辅助计算软件，使用PyQt实现界面，同时用了很多库来实现作图和科学计算、求解等功能。使用界面与逻辑分离的方法，画好UI再编码具体功能。这次规模比第一次写的PyQt+SQL的单一界面复杂很多，在打包发布时遇到的问题也是非常多，一方面代码量非常大，另一方面涉及到的库非常多。

 <!-- more -->

pyinstaller和cx_Freeze的对比
==
之前用pyinstaller打包，遇到的问题也就是decimal的问题，属于隐藏的库的没有导入，这次由于库太多，用pyinstaller打包的时候单个程序出来大概60M，而且打开极慢。同时根据报错来看，pyinstaller并非支持所有库，有的库无法打包发布，比如这次程序使用的scipy。
于是采用了cx_Freeze来打包。虽然理论上来讲是可以成功打包的，但是由于这个工具的简单性导致了一系列error。

matplotlib，numpy，scipy
==
matplotlib库在导入时会出现报错，这是因为它的作图依赖于tkinter，cx_Freeze打包不会将它直接包括；

```python
os.environ['TCL_LIBRARY'] = '*\\Python36-32\\tcl\\tcl8.6'
os.environ['TK_LIBRARY'] = '*\\Python36-32\\tcl\\tk8.6'
# *处为Python目录位置，反斜杠依然两个
```

----------


numpy.core._methods无法找到的报错：options里面includes：

```python
'includes': ['numpy.core._methods']
```

----------

程序中使用from ... import ... ，cx_Freeze打包可能出现库导入不完全的情况，即是看报错信息，没有什么module，在程序中import all或者在setup中include：

```python
'packages':['scipy', 'numpy', 'matplotlib', 'tkinter']
```

----------


目前Python 3.6里面包括的tk导入会出现dll错误：ImportError: DLL load failed，这个和cx_Freeze自身有关，需要include：

```python
'include_files': ['*\\Python36-32\\DLLs\\tk86t.dll'],
# *处为Python目录位置，反斜杠依然两个
```

----------
使用绝对位置的资源文件：

```pthyon
include_files=['1.png', '2.png', 'bg.png', 'bg1.png']
```

build
==
build文件夹包含了所有的要用到的编译好的库文件，文件结构决定了程序运行非常快，基本能秒开了。

setup.py
==
仅供参考。比较麻烦的几个库都用了，没用这些应该会简单很多。

```python
from cx_Freeze import setup, Executable
import sys
import os
os.environ['TCL_LIBRARY'] = 'C:\\Users\\Chaolin Han\\AppData\\Local\\Programs\\Python\\Python36-32\\tcl\\tcl8.6'
os.environ['TK_LIBRARY'] = 'C:\\Users\\Chaolin Han\\AppData\\Local\\Programs\\Python\\Python36-32\\tcl\\tk8.6'

base = 'WIN32GUI' if sys.platform == "win32" else None


executables = [Executable("Main_new.py", base=base)]

packages = []
include_files=['1.png', '2.png', 'bg.png', 'bg1.png']
options = {
    'build_exe': {
        'packages':['scipy', 'numpy', 'matplotlib', 'tkinter'],
        'include_files': ['C:\\Users\\Chaolin Han\\AppData\\Local\\Programs\\Python\\Python36-32\\DLLs\\tcl86t.dll', 'C:\\Users\\Chaolin Han\\AppData\\Local\\Programs\\Python\\Python36-32\\DLLs\\tk86t.dll'],
        'includes': ['numpy.core._methods']
    },

}

setup(
    name = "Prog",
    options = options,
    version = "1.0",
    description = 'desc of program',
    executables = executables
)
```