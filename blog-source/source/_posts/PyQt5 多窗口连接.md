---
title: PyQt5 多窗口连接
date: 2017-12-22 22:00:33
tags:
  - Python
  - PyQt
---

前言
==

之前做过pyqt的一个简单界面，在一个窗口（MainWindow）中实现一些操作；之前嫌麻烦没有去做多窗口和它们的切换功能。最近研究了下窗口的调用和切换。
pyqt4和5有很多不同，在参考别人案例的时候走了很多弯路，最后在pyqt5下实现了简洁的多窗口切换为大家参考。

 <!-- more -->

思路
==
多窗口的切换和显示连接到按钮的信号上再进行show（）就行，格式上有别于pyqt4。
**一个逻辑比较清晰的解决方案是，使用一个主程序，在其中实例化各个窗口，然后定义它们的显示逻辑即是按钮的槽函数。**
格式上稍有不对就会报错，也是让我重新审视了一下python类和继承的语法= =
同时窗口的类型也和pyqt4有所不同，pyqt5下QWidgets为主，与4中QDialog显示方式也有所不同。
具体方式见代码。

代码
==

主程序——实例化各个窗口
------------

```Python
from PyQt5 import QtCore, QtGui, QtWidgets
from test import Ui_Dialog
from Login import Ui_MainWindow_Login


class Ui_Dialog(QtWidgets.QWidget,Ui_Dialog):
    def __init__(self):
        super(Ui_Dialog,self).__init__()
        self.setupUi(self)
	#定义登出按钮的功能
    def logoutEvent(self):
        self.hide()           #隐藏此窗口
        self.log = loginWindow() 
        self.log.show()       #显示登录窗口
                              #必须加上self

class loginWindow(QtWidgets.QMainWindow,Ui_MainWindow_Login):
    def __init__(self):
        super(loginWindow,self).__init__()
        self.setupUi(self)
	#定义登录按钮的功能
    def loginEvent(self):
        self.hide()
        self.dia = Ui_Dialog()
        self.dia.show()
        #self.dia.exec_()
        #pyqt5下show()方法有所改变，不再使用exec_()方法。

#运行窗口Login
if __name__=="__main__":
    import sys
    app=QtWidgets.QApplication(sys.argv)
    login_show=loginWindow()
    login_show.show()
    sys.exit(app.exec_())

```

多窗口
---

需要用到的多个窗口可以用Designer直接生成，主窗口实例化。在其中定义好信号连接即可

```Python
from PyQt5 import QtCore, QtGui, QtWidgets

class Ui_MainWindow_Login(object):
    def setupUi(self, MainWindow_Login):
        #略
    def retranslateUi(self, MainWindow_Login):
		#略
		
if __name__=="__main__":
    import sys
    app=QtWidgets.QApplication(sys.argv)
    widget = QtWidgets.QMainWindow()
    login = Ui_MainWindow_Login()
    login.setupUi(widget)
    widget.show()
    sys.exit(app.exec_())
```

效果
==
![这里写图片描述](http://img.blog.csdn.net/20171222223034659?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
点击登陆：
![这里写图片描述](http://img.blog.csdn.net/20171222223112535?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRVhFQ1VURVJf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
退出登录则返回到第一个窗口。

切换
==
理解好这个实例后，添加更多窗口都是线性复杂度的操作，在Designer中设计好窗口，直接转码后就可在主程序中实例化该窗口并设计它们的显示逻辑。

