# 完整的 for 循环语法

- 在 `Python` 中完整的 `for 循环` 的语法如下：

```python
for 变量 in 集合:

    循环体代码
else:
    没有通过 break 退出循环，循环结束后，会执行的代码
```

#### 应用场景

- 在 **迭代遍历** 嵌套的数据类型时，例如 **一个列表包含了多个字典**
- 需求：要判断 某一个字典中 是否存在 指定的 值
  - 如果 **存在**，提示并且退出循环
  - 如果 **不存在**，在 **循环整体结束** 后，希望 **得到一个统一的提示**

```python
students = [
    {"name": "阿土",
     "age": 20,
     "gender": True,
     "height": 1.7,
     "weight": 75.0},
    {"name": "小美",
     "age": 19,
     "gender": False,
     "height": 1.6,
     "weight": 45.0},
]

find_name = "阿土"

for stu_dict in students:

    print(stu_dict)

    # 判断当前遍历的字典中姓名是否为find_name
    if stu_dict["name"] == find_name:
        print("找到了")

        # 如果已经找到，直接退出循环，就不需要再对后续的数据进行比较
        break

else:
    print("没有找到")

print("循环结束")
```

---

# 函数的递归

> 函数调用自身的 编程技巧 称为递归

### 递归函数的特点

特点

- 一个函数 内部 调用自己

- 函数内部可以调用其他函数，当然在函数内部也可以调用自己

代码特点

1. 函数内部的 代码 是相同的，只是针对 参数 不同，处理的结果不同

2. 当 参数满足一个条件 时，函数不再执行
- 这个非常重要，通常被称为递归的出口，否则 会出现死循环！

```py
def sum_numbers(num):

    print(num)

    # 递归的出口很重要，否则会出现死循环

    if num == 1:

        return

sum_numbers(num - 1)

sum_numbers(3)
```
