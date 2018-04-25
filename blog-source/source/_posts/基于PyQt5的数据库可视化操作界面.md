---
title: PyQt5+python+数据库可视化操作界面
date: 2017-12-10 22:51:46
tags:
  - Python
  - PyQt
  - Database
---

﻿前言
==

这是我python入门写的第一个项目，从前天晚上开始构思到今天做了第一个Release。这两天时间做了读了Qt库和pymssql的不少文档，边学边做写了一个可视化窗口实现数据库的操作。对PyQt的理解也就限于槽和信号沟通环节，pymssql主要是连接部分，并未过多涉及。内容为**医院挂号系统**，基本实现和练习了增删查改操作。介绍环境和配置上的操作以及搭建经验。

 <!-- more -->

PyQt5环境
==
参考[PyQt5+python3+pycharm开发环境配置](http://blog.csdn.net/a359680405/article/details/45074761%20PyQt5+python3+pycharm%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE)
集成了PyUIC和QtDeisgner。其中Designer跨语言，可以画一些简单的窗口和控件，输出.ui格式的文件，利用PyUIC转码为.py进行编辑。
PyQt库非常庞大，但我实际操作主要聚焦于控件的方法和定义。Qt的参考文档为C++环境，结合Pycharm自动补全基本可以避开转换的问题。
可以看到MainWindow代码很长，但大部分都是ui文件转码自动生成的（retranslateUi、setupUi），实际需要编写的就是逻辑和实现部分。

数据库连接
==

由于要求和环境的限制，我使用了SQL server 2012。MySQL,SQLite等连接更为简洁方便。选用的库为pymssql，参考官方文档[pymssql](http://www.pymssql.org/en/stable/pymssql_examples.html)
连接方面，需要首先登陆SQL Management Studio以管理员身份，然后设置中添加用于该项目连接的用户并给它授权。授权选项中我是全部勾选。
![这里写图片描述](http://img.blog.csdn.net/20171210214927091?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
然后在其中建好设计好的两个表Doctor（用于检索医生）和Reg（用于前端操作）
注意设计时，将`varchar()`数据类型更改为`nvarcahar`,便于中文输入输出，同时连接时采用utf8字集：

```python
server = "DESKTOP-9RUT87E"
user = "Hospital"
password = "123456"
#print('Connecting to MSSQL...')
conn = pymssql.connect(server, user, password, database="Hospital", charset='utf8')
```
之后阅读学习python中获取光标进行简单的数据库操作，即可实现在程序中实现数据库操作了，可以参考pymaasql的example。

Python编辑
==
本项目的意义在于简单的图像化操作代替数据库语言来实现数据的增删查改。考虑到绘制的问题，采用的简单的单一界面交互。对于多界面交互则需要绘制多个ui文件并在主程序中调用。

- QtDesigner绘界面
---------------

 ![这里写图片描述](http://img.blog.csdn.net/20171210220436814?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 非常方便地从左侧选取想要加入的元素，右侧属性栏也可以很好地自定义。对应生成了**retranslateUi、setupUi中的代码，参考意义不大故移入附录。**


- 转码为.py文件进行编辑
--------------
若搭建好开发环境，可直接从Pycharm中选择外部工具对ui文件转码，或是直接文件夹下shift+右键打开cmd调用pyuic转码。
 由于 QtDesigner提供的信号和槽连接非常简单而且多数需要实现的功能为SQL连接和操作，故需要自定信号和槽函数来实现这些按钮，所以转码后在IDE中进行编写。
 熟悉了PyQT空间的语法和事件命名方式后，就可以轻松写出自定槽函数来接受按钮信号，同时获取和改变它们的各种状态。有空会写PyQt常见类的常见操作供交流。接下来就是由设计的界面、数据库结构设计交互逻辑并实现。


交互设计
==
窗口的交互分了7个按钮，交互逻辑如下

 - **查询在岗医生**
 根据科室和是否专家查询可用的医生

 - **挂号**
 挂号需要提供个人信息，同时根据需要挂的科室，可执行上一步操作，选择合适的医生，填入医生号码进行挂号

 ![这里写图片描述](http://img.blog.csdn.net/20171212134439142?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 截图：此时点击*挂号*即可完成挂号
 ![这里写图片描述](http://img.blog.csdn.net/20171212134700558?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 其中1、2两步顺序随意。
 - **查、删、改**
 根据提供的信息构建操作来实现，要注意到这些信息并不是全部需要，例如可以查询急症科的所有病人，也可以查询具体的某一个人

 ![这里写图片描述](http://img.blog.csdn.net/20171212135141464?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)![这里写图片描述](http://img.blog.csdn.net/20171212142559598?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)![这里写图片描述](http://img.blog.csdn.net/20171212142610709?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
例：查询**急症科**的**男**病人
![这里写图片描述](http://img.blog.csdn.net/20171212135307129?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


 - **记录日志**
 简单的文件写入操作

 保存的log：
![这里写图片描述](http://img.blog.csdn.net/20171212135433968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
实现
==

 - 按钮槽函数的编写
 DB_开头的函数为自定槽函数，用于响应按钮点击的事件。同时需要一些自定辅助函数和全局变量用于系统状态的维护。
编写思路见代码注释。
 - 引用并执行
 编写HospitalRegSys.py作为主程序来调用编写好的ui转码而来的文件，实现主程序和UI界面代码的分离，也方便添加多个界面。


```Python
# HospitalRegSys0045.py
from PyQt5 import QtCore, QtGui, QtWidgets
from MainWindow import Ui_MainWindow
import decimal

class mwindow(QtWidgets.QMainWindow,Ui_MainWindow):
    def __init__(self):
        super(mwindow,self).__init__()
        self.setupUi(self)

if __name__=="__main__":
    import sys
    app=QtWidgets.QApplication(sys.argv)
    mshow=mwindow()
    mshow.show()
    sys.exit(app.exec_())
```
主窗口代码

```Python
# 不再提供
# No avilable now
```

pyinstaller生成可执行文件
==
生成exe的时候遇到了很多困难，和python3.6环境以及pymssql、pyinstaller设置的参数有关。经过多次测试和参考官方文档，在cmd中执行程序，根据报错“没有找到模块XXX”，直接在主程序中引入该模块即可，即是

```python
import decimal
# import的具体模块和报错缺少的模块有关
```
这个模块和_mssql有关，可见库和环境的不兼容性。

附录
==

数据库结构
----
![这里写图片描述](http://img.blog.csdn.net/20171210224641546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20171210224654534?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

retranslateUi、setupUi代码
---------------------
只需编辑setupUi最后连接部分的信号，以实现按钮的功能。
```Python
# 不再提供
# No avilable now
```

Ref
--
[PyQt5 Reference Guide](http://pyqt.sourceforge.net/Docs/PyQt5/)
[pymssql](http://www.pymssql.org/en/stable/intro.html)