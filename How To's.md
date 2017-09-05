# How To‘s

[TOC]

### 指定可用的Application实例

一个`Application`实例是所有使用这个你正在自动化操作的应用程序的联系者。因此这个应用程序实例需要连接到进程中，有下面两种方法实现：

`start （self ， cmd_line ， timeout = app_start_timeout ） `

`connect （self ， ** kwargs ）  `

`start()`被用来在这个程序没有运行但你需要启动它的时候

`app = Application().start(r"c:\path\to\your\application -a -n -y --arguments")`

其中超时参数是可选的，如果应用程序需要很长时间来启动，则只需要使用该参数。

`connect()`是当自动化程序已经启动时来使用，要指定以运行的应用程序你需要指定以下选项之一：

- 进程： 应用的过程ID

  `app = Application().connect(process=2341)`

- 句柄：应用程序的窗口句柄

  `app = Application().connect(handle=0x010f0c)`

- 路径：进程中可执行文件路径（`GetModuleFileNameEx`)用于查找每个进程的路径并将其传入的值进行比较）

  `app  =  Application().connect （path = r “c：\ windows \ system32 \ notepad.exe” ）`

或任何窗口参数的组合，都会传递给[`pywinauto.findwindows.find_elements()`](https://pywinauto.readthedocs.io/en/latest/code/pywinauto.findwindows.html#pywinauto.findwindows.find_elements)函数，例如：

`app  =  Application().connect （title_re = “。* Notepad” ， class_name = “Notepad” ）`

注意：应用程序在你使用`connect()`之前必须准备好。在`start()`执行之后寻找应用程序，它是没有超时或重试的。因此，如果你在`pywinauto`之外启动程序，你需要睡眠或者编写一个循环等待来等待应用程序完全启动。



### 如何制定应用程序的对话框

一旦应用程序实例知道了被连接的窗口在工作，那么就需要指定这个窗口。

例如：

`dlg = app.Notepad`

`dlg = app['Notepad']`

接下来是一个最简单的方法，去询问`top_window()`函数

`dlg = app.top_window()`

它将返回这个应用程序最高层级的窗口

**注意：这是目前尚未测试的，所以我们并不清楚它是否会正确的返回**

如果上述还不能进行有效控制，那么你可以使用项目参数传递给`findwindows.find.window()`

`dlg = app.window(title_re="Page Setup", class_name="#32770")`

最后介绍一个你可以进行多控制的方法

`dialogs = app.windows()`

这将返回应用程序中所有可见，启用的顶层窗口列表，然后你就可以使用`handleprops`模块中的某些方法所选用的对话框，一旦你拥有其句柄，就可以使用

`app.window(handle=win)`

**注意：如果对话框的标题很长，那么访问属性可能会是很长的类型，在此情况下，通常使用**

`app.window(title_re=".*Part of Title.*")`



### 如何在对话框上指定控件

有很多方法，最简单的就是

```python
app.dlg.control
app['dlg']['control']
```

对非英文的环境来说，需要传递`unicode`字符，则

`app[u'your dlg title'][u'your ctrl title']`

代码依据如下内容来构建多个标识符：

- 标题
- 相关类
- 标题 + 相关类

如果标签的文本为空（或者删除不能使用的字符后为），那么文本就不能被使用。相反，我们会寻找上面和最右边的控制，并附加其相关类，所以列表就是：

- 相关类
- 联系最紧密的文字+ 相关类

一旦对话框中所有控件创建了一组标识符，我们就将它们消除歧义

使用`WindowSpecification.print_control_identifiers() `

例如

```python
dlg_spec = app['无标题 - 记事本']
dlg_spec.print_control_identifiers()
>>>
Control Identifiers:

Dialog - '无标题 - 记事本'    (L481, T434, R1281, B802)
['无标题 - 记事本Dialog', 'Dialog', '无标题 - 记事本']
child_window(title="无标题 - 记事本", control_type="Window")
   | 
   | Edit - '文本编辑器'    (L489, T485, R1273, B794)
   | ['', 'Edit', '0', '1']
   | child_window(title="文本编辑器", auto_id="15", control_type="Edit")
   |    | 
   |    | ScrollBar - '垂直滚动条'    (L1256, T485, R1273, B794)
   |    | ['垂直滚动条ScrollBar', '垂直滚动条', 'ScrollBar']
   |    | child_window(title="垂直滚动条", auto_id="NonClientVerticalScrollBar", control_type="ScrollBar")
   |    |    | 
   |    |    | Button - '上一行'    (L1256, T485, R1273, B502)
   |    |    | ['上一行', '上一行Button', 'Button', 'Button0', 'Button1']
   |    |    | child_window(title="上一行", auto_id="UpButton", control_type="Button")
   |    |    | 
   |    |    | Button - '下一行'    (L1256, T777, R1273, B794)
   |    |    | ['下一行', '下一行Button', 'Button2']
   |    |    | child_window(title="下一行", auto_id="DownButton", control_type="Button")
   | 
   | TitleBar - 'None'    (L505, T437, R1273, B465)
   | ['2', 'TitleBar']
   |    | 
   |    | Menu - '系统'    (L489, T442, R511, B464)
   |    | ['系统Menu', '系统', 'Menu', '系统0', '系统1', 'Menu0', 'Menu1']
   |    | child_window(title="系统", auto_id="MenuBar", control_type="MenuBar")
   |    |    | 
   |    |    | MenuItem - '系统'    (L489, T442, R511, B464)
   |    |    | ['系统2', 'MenuItem', '系统MenuItem', 'MenuItem0', 'MenuItem1']
   |    |    | child_window(title="系统", control_type="MenuItem")
   |    | 
   |    | Button - '最小化'    (L1134, T435, R1181, B465)
   |    | ['最小化Button', '最小化', 'Button3']
   |    | child_window(title="最小化", control_type="Button")
   |    | 
   |    | Button - '最大化'    (L1181, T435, R1227, B465)
   |    | ['最大化Button', '最大化', 'Button4']
   |    | child_window(title="最大化", control_type="Button")
   |    | 
   |    | Button - '关闭'    (L1227, T435, R1274, B465)
   |    | ['关闭', '关闭Button', 'Button5']
   |    | child_window(title="关闭", control_type="Button")
   | 
   | Menu - '应用程序'    (L489, T465, R1273, B484)
   | ['应用程序', 'Menu2', '应用程序Menu']
   | child_window(title="应用程序", auto_id="MenuBar", control_type="MenuBar")
   |    | 
   |    | MenuItem - '文件(F)'    (L489, T465, R541, B484)
   |    | ['文件(F)MenuItem', 'MenuItem2', '文件(F)']
   |    | child_window(title="文件(F)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '编辑(E)'    (L541, T465, R594, B484)
   |    | ['MenuItem3', '编辑(E)', '编辑(E)MenuItem']
   |    | child_window(title="编辑(E)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '格式(O)'    (L594, T465, R650, B484)
   |    | ['格式(O)', '格式(O)MenuItem', 'MenuItem4']
   |    | child_window(title="格式(O)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '查看(V)'    (L650, T465, R704, B484)
   |    | ['查看(V)MenuItem', '查看(V)', 'MenuItem5']
   |    | child_window(title="查看(V)", control_type="MenuItem")
   |    | 
   |    | MenuItem - '帮助(H)'    (L704, T465, R759, B484)
   |    | ['帮助(H)', 'MenuItem6', '帮助(H)MenuItem']
   |    | child_window(title="帮助(H)", control_type="MenuItem")


```

注意：此方法打印的标识符已经通过标识的唯一进程。所以如果你有两个编辑框，它们都会在其中列出。实际上，第一个可以被称之为“编辑”，“编辑0”，“编辑1”和第二个应该被称为“编辑2”

注意：你不需要精确！



### 如何使用pywinauto在英文之外的环境

在py2中，Python的编码一直是蛋疼的问题，但是py3的出现改变了这一现状。Python3中，字符串是以Unicode编码的，也就是说，Python的字符串支持多语言。使用如下方法来进行属性控制

> 在英文文档中，此部分还是以Python2为基础

1. `app.dialog_ident.control_ident.click()`
2. `app['dialog_ident']['control_ident'].click()`
3. `app.window(title_re="NonAsciiCharacters").window(title="MoreNonAsciiCharacters").click()`



### 如何处理不按照预期进行响应的控件（例如OwnerDraw控件）

一些控件不按照预期的方式响应事件。例如，如果你查看任何HLP文件，并转到索引选项（单击”搜索“按钮），您将会看见一个列表框。运行控件查看工具（spy++）你就发现它确实是一个列表，但是是`ownerdrawn`。这意味着开发人员以及告诉windows，它们会覆盖项目的显示方式。在这种情况下，这样的一些字符串无法被检索。

这些问题是怎么导致的那？

```python
app.HelpTopics.ListBox.texts()                # 1
app.HelpTopics.ListBox.select("ItemInList")   # 2
```

1. 这将返回空字符串的列表，这意味着`pywinauto`无法获取列表框中的字符串
2. 这将因为`IndexError`而失败，因为`ListBox`中的`select（string）`模块查找文本中的项目去了解其索引。

应用此控件的解决方法：

`app.HelpTopics.ListBox.select(1)`

这将选择列表框中的第二个项目，因为这不是正确的查找字符串。

不幸的是，它永远不会工作。开发人员可以使其控制不响应事件，如`select`。在这种情况下，您可以使用`TypeKeys()`的键盘模拟来选择列表框中的项目。



这允许您将任何按键发送到控件。所以选择第三个项目你应该使用

`app.Helptopics.ListBox1.type_keys("{HOME}{DOWN 2}{ENTER}")`

- `{HOME}` 将确保第一个项目被突出显示。
- `{DOWN 2}` 然后将亮点向下移动两项
- `{ENTER}` 将选择突出显示的项目

如果你的应用程序广泛使用类似的控件类型，那么你可以通过从`ListBox`派生一个新类来简化使用，可以作为你特定程序的额外知识



###  如何访问系统托盘（SysTray，通知区域）

```python
import pywinauto.application
app = pywinauto.application.Application().connect(path="explorer")
systray_icons = app.ShellTrayWnd.NotificationAreaToolbar
```

任务栏模块提供对系统托盘的初步访问。

它定义了以下变量:

- explorer_app

  定义连接到正在运行的资源管理器的`Application()`对象。你可能不需要直接使用它。

- 任务栏

  任务栏的句柄(包括开始按钮，QuickLaunch图标，正在运行的任务等)

- 开始按钮

  “启动我”:-)我想你可能会知道这是什么！

- 快速启动

  具有快速启动图标的工具栏

- SystemTray中

  包含时钟和系统托盘图标的窗口

- 时钟

- SystemTrayIcons

  表示系统托盘图标的工具栏

- RunningApplications

  工具条表示运行中的应用程序

我还在模块中提供了两个可以用来点击系统托盘图标的功能：

- `ClickSystemTrayIcon(button)`

  您可以使用此按钮左键单击系统托盘中的可见图标。我不得不具体说可见的图标，因为可能有许多看不见的图标显然不能被点击。按钮可以是任意整数。如果您指定3，那么它会找到并单击第3个可见按钮。（几乎不会在这里执行错误检查，但这种方法将来会更有可能被移动/重命名。）

- `RightClickSystemTrayIcon(button)`

  类似于`ClickSytemTrayIcon`但执行右键单击。

通常，当您点击/右键单击图标时，您将收到一个弹出菜单。在这一点上要记住的是，弹出菜单是应用程序的一部分，而不是资源管理器的一部分。

例如：

```python
# connect to outlook
outlook = Application.connect(path='outlook.exe')

# click on Outlook's icon
taskbar.ClickSystemTrayIcon("Microsoft Outlook")

# Select an item in the popup menu
outlook.PopupMenu.Menu().get_menu_path("Cancel Server Request")[0].click()
```

