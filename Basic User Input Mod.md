# 用户基本输入模块

## pywinauto.mouse

跨平台来模拟鼠标事件

`pywinauto.mouse.click(*button='left', coords=(0, 0))`

点击指定的坐标

`pywinauto.mouse.click(*button='left', coords=(0, 0))`





## pywinauto.keyboard

键盘输入仿真模块

通过调用`SendKeys`方法自动将键入到活动窗口。您可以使用任何Unicode字符（在Windows上）和下面列出的一些特殊键。该模块也可在Linux上使用。

可用键码：

```
{ SCROLLLOCK }， { VK_SPACE }， { VK_LSHIFT }， { VK_PAUSE }， { VK_MODECHANGE }，
{ BACK }， { VK_HOME }， { F23 }， { F22 }， { F21 }， { F20 }， { VK_HANGEUL }， { VK_KANJI }，
{ VK_RIGHT }， { BS }， { HOME }， { VK_F4 }， { VK_ACCEPT}， { VK_F18 }， { VK_SNAPSHOT }，
{ VK_PA1 }， { VK_NONAME }， { VK_LCONTROL }， { ZOOM }， { VK_ATTN }， { VK_F10 }， { VK_F22 }，
{ VK_F23 }， { VK_F20 }， { VK_F21 }  { VK_SCROLL }， { TAB }， { VK_F11 }， { VK_END }，
{ LEFT }， {VK_UP }， { NUMLOCK }， { VK_APPS }， { PGUP }， { VK_F8 }， { VK_CONTROL }，
{ VK_LEFT }， { PRTSC }， { VK_NUMPAD4 }， { CAPSLOCK }， { VK_CONVERT }， { VK_PROCESSKEY }，
{ ENTER } ， { VK_SEPARATOR }， { VK_RWIN }， { VK_LMENU }， { VK_NEXT }， { F1}， { F2 }，
{ F3 }， { F4 }， { F5 }， { F6 }， { F7 }， { F8 }， { F9 }， { VK_ADD }， { VK_RCONTROL }，
{ VK_RETURN }， { BREAK }， { VK_NUMPAD9 }， { VK_NUMPAD8 }， { RWIN }， { VK_KANA }，
{ PGDN }， { VK_NUMPAD3}， { DEL }， { VK_NUMPAD1 }， { VK_NUMPAD0 }， { VK_NUMPAD7 }，
{ VK_NUMPAD6 }， { VK_NUMPAD5 }， { DELETE }， { VK_PRIOR }， { VK_SUBTRACT }， { HELP }，
{ VK_PRINT }， { VK_BACK }  { CAP }， { VK_RBUTTON }， { VK_RSHIFT }， { VK_LWIN }， { DOWN }，
{ VK_HELP }， { VK_NONCONVERT }， { BACKSPACE }， { VK_SELECT }， { VK_TAB }， { VK_HANJA }，
{ VK_NUMPAD2 }， { INSERT }， { VK_F9 }， { VK_DECIMAL }， { VK_FINAL }， { VK_EXSEL }，
{ RMENU }， { VK_F3 }， { VK_F2 }， { VK_F1 }， { VK_F7 }， {VK_F6 }， { VK_F5 }， { VK_CRSEL }，
{ VK_SHIFT }， { VK_EREOF }， { VK_CANCEL }， { VK_DELETE }， { VK_HANGUL }， { VK_MBUTTON }，
{ VK_NUMLOCK }， { VK_CLEAR }， { END }， { VK_MENU } ， { SPACE }， { BKSP }， { VK_INSERT }，
{ F18 }， { F19}， { ESC }， { VK_MULTIPLY }， { F12 }， { F13 }， { F10 }， { F11 }， { F16 }，
{ F17 }， { F14 }， { F15 }， { F24 }， { RIGHT }  { VK_F24 }， { VK_CAPITAL }， { VK_LBUTTON }，
{ VK_OEM_CLEAR }， { VK_ESCAPE }， { UP}， { VK_DIVIDE }， { INS }， { VK_JUNJA }，
{ VK_F19 }， { VK_EXECUTE }， { VK_PLAY }， { VK_RMENU }， { VK_F13 }， { VK_F12 }， { 伦}，
{ VK_DOWN }， { VK_F17 }  { VK_F16 }， { VK_F15 }， { VK_F14 }
```

**修饰符：**

- `'+': {VK_SHIFT}`
- `'^': {VK_CONTROL}`
- `'%': {VK_MENU}` 又名Alt键

示例如何使用修饰符：

```python
SendKeys('^a^c') # select all (Ctrl+A) and copy to clipboard (Ctrl+C)
SendKeys('+{INS}') # insert from clipboard (Shift+Ins)
SendKeys('%{F4}') # close an active window with Alt+F4
```

可以为特殊键指定重复计数。`{ENTER 2}`意思是按两次Enter