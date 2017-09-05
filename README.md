# zh-pywinauto-doc

[TOC]

[Pywinauto](https://pywinauto.github.io/)的中文翻译。

`Pywinauto`是一个GUI自动化库，它是用`python`写的，而且可以很好的支持Windows GUI。

因为我在做Windows Desktop Application GUI Automation Test用到了这个库，因此顺便翻译了文档。



## 安装

- 直接使用`pip install --upgrade pywinauto` (Py2.7+, Py3.3+)

或者也可以手动安装：

- 安装 [pyWin32 extensions](http://sourceforge.net/projects/pywin32/files/pywin32/) 
- 下载 [six](https://pypi.python.org/pypi/six)并执行 `python setup.py install`来安装
- 下载 [comtypes](https://github.com/enthought/comtypes/releases) 并执行 `python setup.py install`来安装
- 下载 [the latest pywinauto](https://github.com/pywinauto/pywinauto/zipball/master/) 并执行 `python setup.py install`来安装

或者在Linux下安装：

- [six](https://pypi.python.org/pypi/six)
- [python-xlib](https://github.com/python-xlib/python-xlib/releases)
- 执行 `python setup.py install` 对每一个依赖`pywinauto`的包



## 支持的控件

- 标准Win32控件：MFC, WTL, VB6和其他一些使用WinForms的老应用
- 所有基于MS UI Automation的标准部件：WPF, Qt, 所有浏览器, Windows文件资源管理器和其他



## 详细文档

[入门](https://github.com/fangchaooo/zh-pywinauto-doc/blob/master/What%20is%20pywinauto.md)

[怎样使用](https://github.com/fangchaooo/zh-pywinauto-doc/blob/b83093cf125240ceff2fde5f7fe5ef26d0d4fcfc/How%20To's.md)

[等待操作](https://github.com/fangchaooo/zh-pywinauto-doc/blob/b83093cf125240ceff2fde5f7fe5ef26d0d4fcfc/Waiting%20for%20Long%20Operations.md)

[各种不同控件类型可用模块](https://github.com/fangchaooo/zh-pywinauto-doc/blob/b83093cf125240ceff2fde5f7fe5ef26d0d4fcfc/Methods%20available%20to%20each%20different%20control%20type.md)



