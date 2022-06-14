# 第一个程序

## 安装

1. PySimpleGUI基于tkinter，在安装python的时候把tkinter勾上，否则没有tkinter并且这个包无法从pip进行下载

2. ```
   pip install PySimpleGUI
   ```

3. ```python
   import PySimpleGUI as sg  # Part 1 - 导包
   if __name__ == '__main__':
       # Define the window's contents
       layout = [[sg.Text("What's your name?")],  # Part 2 -设置布局
                 [sg.Input()],
                 [sg.Button('Ok')]]
       # Create the window
       window = sg.Window('Window Title', layout)  # Part 3 -定义窗口（title,布局）
   
       #event -> 事件  values -> Input() 方法的值
       event, values = window.read()  # Part 4 - 启动窗口、监听事件
   
       # 做一些事
       print('Hello', values[0], "! Thanks for trying PySimpleGUI")
   
       # 关闭窗口
       window.close()
   ```

   endline

# 事件

## 例子

```python
 # Define the window's contents
    layout = [[sg.Text("What's your name?")],
              [sg.Input(key='-INPUT-')],
              [sg.Text(size=(40, 1), key='-OUTPUT-')],
              [sg.Button('Ok'), sg.Button('Quit')]]

    # 设置窗口标题和布局
    window = sg.Window('Window Title', layout)

    # Display and interact with the Window using an Event Loop
    while True:
        event, values = window.read()
        # See if user wants to quit or window was closed
        if event == sg.WINDOW_CLOSED or event == 'Quit':
            break
        # Output a message to the window
        window['-OUTPUT-'].update('Hello ' + values['-INPUT-'] + "! Thanks for trying PySimpleGUI")

    # Finish up by removing from the screen
    window.close()
```

## 事件大全

官方文档：https://pysimplegui.readthedocs.io/en/latest/call reference/

以下事件基于【例子】扩展，基本流程请看【例子】

### 弹出窗口

```python
sg.Popup("这是弹出的窗口") #默认标题和内容一样
sg.Popup("这是弹出的窗口",title=None) #设置弹窗标题
```

### 按钮事件

```python
if event == '按钮名': #按钮事件
    
if event in ('exit',None): #多个按钮指向同一个处理
  
if event.startwith('确认'): #以确认开头的按钮，如【确认1】【确认哈哈】按钮都会产生这个事件
    
if event.endwith('确认'): #以确认结尾的按钮
```

### 窗口事件

```python
if event == None: #窗口关闭事件
    
if event == sg.WIN_CLOSED: #窗口关闭事件2
```

### 获取表单的值

```python
print(values)#所有表单的值，这是一个的字典，当没有设置key时，key为0,1,2....

print(values[0])# 没设置key时，通过索引获取表单的值

print(values['key'])#设置key 通过字典获取表单的值
```



# 布局样式

## 主题

查看主题

```python
sg.theme_previewer() #显示可用的主题
```

选择主题

```python
print(sg.theme_list()) #打印主题列表

sg.theme('主题名称') #设置主题

sg.theme('')  #随机主题
```

修改部分主题

```python
print(sg.theme_button_color()) #('fff','black')  打印当前主题的按钮颜色

sg.theme_button_color(('新按钮背景颜色','新按钮字体颜色'))

sg.Popup('弹窗验证')
```

## 布局

用for循环写布局

```python
layout[
[sg.Text(i) for i in 'abcd']  #显示一行标签  a  b  c  d
]
```

# 组件

## 组件更新

```python
window[key].update()

#文本标签的更新方法： 更多查看官方文档
update(value = None,
    background_color = None,
    text_color = None,
    font = None,
    visible = None)  #是否可见
```



## 组件大全

### Text标签

以text标签说明组件的一些常见技巧，具体的看官方文档

```python
layout[
    sg.T(text = "",   #sg.Text()缩写sg.T()
    size = (None, None),  #宽度、高度（int,int）
    s = (None, None),
    auto_size_text = None,  #bool,自动适应窗口
    click_submits = False,
    enable_events = False,   #bool，是否运行点击事件
    relief = None,
    font = None,  #font='宋体' or font=('宋体',int) or font=['宋体',int]
    text_color = None,
    background_color = None,
    border_width = None,
    justification = None,
    pad = None,
    p = None,
    key = None,
    k = None,
    right_click_menu = None,
    expand_x = False,
    expand_y = False,
    grab = None,
    tooltip = None,
    visible = True,
    metadata = None))
]
```

一两个使用技巧

```python
the_text = '''
1121212
dsdds
'''
layout[
    sg.T(the_text,   #text=''不用写，直接写文本，可以用变量代表文本
    size = (None, None),  #宽度、高度（int,int）
    )
]

#设置了点击事件后
if event == 'the_text':
    #用那个文本变量直接定位事件
```

更新Text组件

# endline

# 

