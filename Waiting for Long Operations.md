# Waiting for Long Operations

GUI应用程序行为通常是不稳定的，您的脚本需要等待直到出现新窗口或现有窗口被关闭/隐藏。pywinauto可以隐含地等待对话初始化（默认超时）。有几种方法/功能可以帮助您使代码更容易，更可靠。

### 申请方式

- `wait_cpu_usage_lower`

  该方法对于允许在另一个线程中进行延迟初始化的多线程接口是有用的，而GUI响应并且所有控件已经存在并且可以使用。所以等待一个特定的窗口存在/状态是无用的。在这种情况下，整个过程的CPU使用情况表明任务计算尚未完成.

  `app.wait_cpu_usage_lower(threshold=5) # 等到CPU使用率低于5％`



###  WindowSpecification方法

所有控件都可以使用

- `wait`
- `wait_not`

一个`WindowSpecification`对象不一定与现有的窗口/控件相关。这只是一个描述，即搜索窗口的几个条件。该`wait`方法（如果没有引发任何异常）可以保证目标控件存在，甚至可见，启用或活动。



### `timings`模块功能

对任何Python代码都有用的低级方法

- wait_until
- wait_until_passes

装饰器`pywinauto.timings.always_wait_until()`和`pywinauto.timings.always_wait_until_passes()`可以被每个函数进行调用。

```python
＃call ensure_text_changed（ctrl）每2秒，直到通过或超时（4秒）过期

@always_wait_until_passes （4 ， 2 ）
DEF  ensure_text_changed （CTRL ）：
    如果 previous_text  ==  CTRL 。window_text （）：
        raise  ValueError （'ctrl文本保持不变而改变是预期的' ）
```



### 识别控制

帮你找到所需控件的方法。

- `print_control_identifiers`
- `draw_outline`



