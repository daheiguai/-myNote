# pykeyboard

官方文档地址https://pyautogui.readthedocs.io/en/latest/keyboard.html#the-hold-context-manager

## 键盘

### write()

```python
pyautogui.write('Hello world!') #敲键盘
pyautogui.write('Hello world!', interval=0.25) #设置每个按键之间的延迟
```

### press()

```python
pyautogui.press('enter')  #按空格
pyautogui.press('f1')     # 按f1
pyautogui.press('left')   # 按左键

pyautogui.press(['left', 'left', 'left']) #连按三键
pyautogui.press('left', presses=3) #按的次数
```

大全

```python
['\t', '\n', '\r', ' ', '!', '"', '#', '$', '%', '&', "'", '(',
')', '*', '+', ',', '-', '.', '/', '0', '1', '2', '3', '4', '5', '6', '7',
'8', '9', ':', ';', '<', '=', '>', '?', '@', '[', '\\', ']', '^', '_', '`',
'a', 'b', 'c', 'd', 'e','f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o',
'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '{', '|', '}', '~',
'accept', 'add', 'alt', 'altleft', 'altright', 'apps', 'backspace',
'browserback', 'browserfavorites', 'browserforward', 'browserhome',
'browserrefresh', 'browsersearch', 'browserstop', 'capslock', 'clear',
'convert', 'ctrl', 'ctrlleft', 'ctrlright', 'decimal', 'del', 'delete',
'divide', 'down', 'end', 'enter', 'esc', 'escape', 'execute', 'f1', 'f10',
'f11', 'f12', 'f13', 'f14', 'f15', 'f16', 'f17', 'f18', 'f19', 'f2', 'f20',
'f21', 'f22', 'f23', 'f24', 'f3', 'f4', 'f5', 'f6', 'f7', 'f8', 'f9',
'final', 'fn', 'hanguel', 'hangul', 'hanja', 'help', 'home', 'insert', 'junja',
'kana', 'kanji', 'launchapp1', 'launchapp2', 'launchmail',
'launchmediaselect', 'left', 'modechange', 'multiply', 'nexttrack',
'nonconvert', 'num0', 'num1', 'num2', 'num3', 'num4', 'num5', 'num6',
'num7', 'num8', 'num9', 'numlock', 'pagedown', 'pageup', 'pause', 'pgdn',
'pgup', 'playpause', 'prevtrack', 'print', 'printscreen', 'prntscrn',
'prtsc', 'prtscr', 'return', 'right', 'scrolllock', 'select', 'separator',
'shift', 'shiftleft', 'shiftright', 'sleep', 'space', 'stop', 'subtract', 'tab',
'up', 'volumedown', 'volumemute', 'volumeup', 'win', 'winleft', 'winright', 'yen',
'command', 'option', 'optionleft', 'optionright']
```

### 长按

```python
pyautogui.keyDown('shift')  #按住不放
pyautogui.press('left') 
pyautogui.press('left') 
pyautogui.press('left') 
pyautogui.keyUp('shift')    # 松开   
```

按住shift 之后按的键	 和上面的方法相同效果

```python
with pyautogui.hold('shift'):
	pyautogui.press(['left', 'left', 'left'])     
```

### 其他

```python
pyautogui.hotkey('ctrl', 'shift', 'esc')
 # 和以下相同
pyautogui.keyDown('ctrl')
pyautogui.keyDown('shift')
pyautogui.keyDown('esc')
pyautogui.keyUp('esc')
pyautogui.keyUp('shift')
pyautogui.keyUp('ctrl')
```

## 鼠标



## 截屏
