# PYWINAUTO入门指南

> windows上支持的辅助技术列表
>
> - Win32 API(`backend = "win32"`)(现在默认)
>   - MFC VB6 VCL
> - MS UI Automation(`backend = "uia"`)
>   - WinForms, WPF, Store app, QT, Browsers
>
> GUI观察
>
> - spy++
>
> - Inspect.exe
>
>   路径在`C:\Program Files (x86)\Windows Kits\<winver>\bin\x64`
>
>   ​
>
>   如果GUI不能满足你的要求，那使用鼠标和键盘模块也是另一种选择，使用[pyautogui](https://github.com/asweigart/pyautogui)吧，它可以满足你任何要求。







###  自动化入口

在`pywinauto`中启动程序使用一个`Application`对象来调用它。

```python
from pywinauto.application import Application
# 对于Windows中自带应用程序，直接执行，对于外部应用应输入完整路径
app = Application(backend="uia").start('notepad.exe') 

#描述Notepad.exe进程中的窗口
dlg_spec = app.UntitledNotepad
#等待窗口真正打开
actionable_dlg = dlg_spec.wait('visible')
```

如果你想跨进程进到另一个程序中，你可以使用`Desktop`对象

```python
from subprocess import Popen
from pywinauto import Desktop

Popen('calc.exe', shell=True)
dlg = Desktop(backend="uia").Calculator
dlg.wait('visible')
```

应用程序和桌面对象都是`backend`特定的，因此无需再使用后台名称了。



### 窗口规格

这高级`pywinauto GUI`的核心概念，你可以使用它来模数窗口或者控件的更多细节，无论它是否存在或者已经关闭。窗口规范还保留着有关匹配算法、搜索算法，这些信息将用于获取真实的窗口或者控件。

我们开始创建一个窗口规范：

```python
>>> dlg_spec = app.window(title='Untitled - Notepad')
# 在中文环境下
# dlg_spec = app.window(title='无标题 - 记事本')

>>> dlg_spec
<pywinauto.application.WindowSpecification object at 0x0568B790>

>>> dlg_spec.wrapper_object()
<pywinauto.controls.win32_controls.DialogWrapper object at 0x05639B70>
# 中文 <uiawrapper.UIAWrapper - '无标题 - 记事本', Dialog, 3713039978638135481>
```

通过`warpper_object()`方法可以执行实际窗口的查找，它返回真实的窗口或控件的包装器`ElementNotFoundError`，这个包装器可以通过发送动作或者检索数据来处理窗口控件。

但`python`可以隐藏`wrapper_object()`调用，简化代码，例如：

```python
dlg_spec.wrapper_object().minimize() # while debugging
dlg_spec.minimize() # in production
# 两行代码完全等价
```

创建窗口还有更多的标准，下面是几个示例：

```python
# can be multi-level
app.window(title_re='.* - Notepad$').window(class_name='Edit')

# can combine criteria
dlg = Desktop(backend="uia").Calculator
dlg.window(auto_id='num8Button', control_type='Button')
```

### 

###魔法解析属性

`Python`通过动态解析对象属性来简化窗口规范，但是属性名和变量名一样有相同规范：无空格，逗号和其他特殊符号。幸运的是,`pywinauto`使用”最佳匹配“算法来查看抵消错别字和小的变化。

```python
app.UntitledNotepad
# is equivalent to
app.window(best_match='UntitledNotepad')
```

`Unicode`字符和特殊符号的使用可以通过字典中的项目进行访问（尤其对于中文来说，找不到相应的控件就对其进行字典访问）

```python
app['Untitled - Notepad']
# is the same as
app.window(best_match='Untitled - Notepad')
```



### 如何得到魔法属性名称

有几个原则，如何将“最佳匹配“到的金光闪闪的名称附加到控件上。所以如果窗口规范接近于其中一个名称，那你就能成功匹配。

1. 根据标题（窗口文字，名称）:`app.Properties.OK.click()`
2. 按标题和控制类型：`app.Properties.OKButton.click()`
3. 通过控制类型和数量：`app.Properties.Button3.click()`(Button和Button1匹配相同按钮，button2匹配下一个按钮)
4. 通过左上角的标签和控件类型：`app.OpenDialog.FileNameEdit.set_text("")`
5. 按控件类型和项目文本：`app.Properties.TabControlSharing.select("General")`

通常这些所有匹配的名称并非都可以同时使用。要检查指定对话框的这些名称，可以使用`print_control_identifiers()`方法。可用的最佳匹配名显示为树中每个控件的Python列表。更详细的窗口规范也可以从方法输出中复制。

例如：

`app.Properties.child_window(title="Contains:", auto_id="13087", control_type="Edit")`

```python
from pywinauto.application import Application
app = Application(backend="uia").start('notepad.exe')
# 查到这个记事本的控件树
dlg_spec = app['无标题 - 记事本']
dlg_spec.print_control_identifiers()




Dialog - '无标题 - 记事本'    (L403, T241, R1203, B609)
['Dialog', '无标题 - 记事本Dialog', '无标题 - 记事本']
child_window(title="无标题 - 记事本", control_type="Window")
   | 
   | Edit - '文本编辑器'    (L411, T292, R1195, B601)
   | ['', 'Edit', '0', '1']
   | child_window(title="文本编辑器", auto_id="15", control_type="Edit")
   |    | 
   |    | ScrollBar - '垂直滚动条'    (L1178, T292, R1195, B601)
   |    | ['垂直滚动条', '垂直滚动条ScrollBar', 'ScrollBar']
   |    | child_window(title="垂直滚动条", auto_id="NonClientVerticalScrollBar", control_type="ScrollBar")
   |    |    | 
   |    |    | Button - '上一行'    (L1178, T292, R1195, B309)
   |    |    | ['Button', '上一行', '上一行Button', 'Button0', 'Button1']
   |    |    | child_window(title="上一行", auto_id="UpButton", control_type="Button")
   |    |    | 
   |    |    | Button - '下一行'    (L1178, T584, R1195, B601)
   |    |    | ['Button2', '下一行Button', '下一行']
   |    |    | child_window(title="下一行", auto_id="DownButton", control_type="Button")
   | 
   | TitleBar - 'None'    (L427, T244, R1195, B272)
   | ['2', 'TitleBar']
   |    | 
   |    | Menu - '系统'    (L411, T249, R433, B271)
   |    | ['Menu', '系统', '系统Menu', '系统0', '系统1', 'Menu0', 'Menu1']
   |    | child_window(title="系统", auto_id="MenuBar", control_type="MenuBar")
   |    |    | 
   |    |    | MenuItem - '系统'    (L411, T249, R433, B271)
   |    |    | ['系统2', '系统MenuItem', 'MenuItem', 'MenuItem0', 'MenuItem1']
   |    |    | child_window(title="系统", control_type="MenuItem")
   |    | 
   |    | Button - '最小化'    (L1056, T242, R1103, B272)
   |    | ['Button3', '最小化', '最小化Button']
   |    | child_window(title="最小化", control_type="Button")
   |    | 
   |    | Button - '最大化'    (L1103, T242, R1149, B272)
   |    | ['Button4', '最大化Button', '最大化']
   |    | child_window(title="最大化", control_type="Button")
   |    | 
   |    | Button - '关闭'    (L1149, T242, R1196, B272)
   |    | ['Button5', '关闭Button', '关闭']
   |    | child_window(title="关闭", control_type="Button")
   | 
   | Menu - '应用程序'    (L411, T272, R1195, B291)
   | ['应用程序', 'Menu2', '应用程序Menu']
   | child_window(title="应用程序", auto_id="MenuBar", control_type="MenuBar")
   |    | 
   |    | MenuItem - '文件(F)'    (L411, T272, R463, B291)
   |    | ['文件(F)MenuItem', '文件(F)', 'MenuItem2']
   |    | child_window(title="文件(F)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '编辑(E)'    (L463, T272, R516, B291)
   |    | ['编辑(E)MenuItem', '编辑(E)', 'MenuItem3']
   |    | child_window(title="编辑(E)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '格式(O)'    (L516, T272, R572, B291)
   |    | ['格式(O)MenuItem', '格式(O)', 'MenuItem4']
   |    | child_window(title="格式(O)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '查看(V)'    (L572, T272, R626, B291)
   |    | ['查看(V)', '查看(V)MenuItem', 'MenuItem5']
   |    | child_window(title="查看(V)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '帮助(H)'    (L626, T272, R681, B291)
   |    | ['帮助(H)', '帮助(H)MenuItem', 'MenuItem6']
   |    | child_window(title="帮助(H)", control_type="MenuItem")

Process finished with exit code 0

```



###  一些例子

下面的这些例子所包括：注意：示例是依赖于语言的，他们仅适用于所coding的产品语言，如下所示例的均为英文环境。

- `mspaint.py` 控制`MSPaint`
- `notepad_fast.py` 使用快速时间设置来控制笔记本
- `notepad_slow.py` 使用慢时间设置来控制笔记本
- `notepad_item.py` 使用项目，然后属性访问控件记事本。
- `misc_examples.py` 显示一些异常以及如何获取控制标识符。
- `save_from_internet_explorer.py` 从Internet Explorer保存网页。
- `save_from_firefox.py` 从Firefox保存网页。
- `get_winrar_info.py` 如何做多语言自动化的例子。这不是一个理想的例子（适用于法语，捷克语和德语WinRar）
- `forte_agent_sample.py` 处理复杂的应用程序的例子是非常动态的，并且在启动时经常给出不同的对话框。
- `windowmediaplayer.py` 另一个例子 - 处理ListView中的复选框。
- `test_sakura.py`，`test_sakura2.py` 自动化一个Japanase产品的两个例子。



### 在命令行自动化记事本

请按照如下示例运行

```python
from pywinauto.application import Application
import time

app = Application().start('notepad.exe')
time.sleep(1)
app[' 无标题 - 记事本 '].menu_select("编辑(&E) -> 替换(&R)..")
time.sleep(1)
app['替换'].取消.click()

# 没有with_spaces 参数空格将不会被键入。请参阅SendKeys的这个方法的文档，因为它是SendKeys周围的薄包装。
app[' 无标题 - 记事本 '].Edit.type_keys("Hi from Python interactive prompt %s" % str(dir()), with_spaces = True)

app[' 无标题 - 记事本 '].menu_select('文件(&F) -> 退出(&X)')

# 在这时候不清楚“不保存”的按钮名就对app['记事本'] 使用print_control_identifiers()
app['记事本'].Button2.click()
```

