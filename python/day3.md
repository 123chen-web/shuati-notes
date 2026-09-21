# Day 3 · 2026-09-21

## 今日概览

- 学习模块：Python 函数定义、默认参数、关键字实参、`*args` 与 `**kwargs`
- 来源：BroCode Python 入门课程 32-35 节 + 课程截图批注，这是目前学到的部分里和 C/C++ 差别最大的内容
- 今日重点：C++ 也有默认参数，但没有"关键字实参"这种按参数名传值的调用方式，也没有 `*args`/`**kwargs` 这种收集任意数量实参的机制

## 知识点 1：函数定义与返回值

**概念**

`def 函数名(参数):` 定义函数，函数体缩进；调用时才会执行。形参是定义时写的名字，实参是调用时传进去的值。`return` 返回结果并立即结束这次调用；没写 `return`，或者只写了 `return` 不带值，返回的都是 `None`。`return a, b` 可以一次返回两个值，调用处通常用元组接收或者直接拆包成两个变量。

**和 C/C++ 不同的地方**

- 不用在定义时写参数和返回值的类型
- `print()` 只是把内容显示出来，`return` 才是把结果"交回去"给调用者，两者不能互相替代——这个道理在 C/C++ 里也一样（`printf` vs `return`），但 Python 新手很容易把"函数里 print 了结果"误当成"函数返回了这个值"

**易错点**

- 只在函数里 `print` 结果而不 `return`，调用处拿到的变量会是 `None`
- 字符串方法（比如 `capitalize()`）都返回新字符串，必须重新赋值接住，比如 `first = first.capitalize()`，只调用不赋值不会改变原变量

## 知识点 2：默认参数

**概念**

定义函数时给形参写上默认值，调用时可以省略这个实参，也可以传值覆盖默认值。比如 `def count(end, start=0)`：`end` 必须传，`start` 不传就用 `0`。

**易错点**

- 没有默认值的必填参数必须写在有默认值的参数前面，顺序反了会报语法错误
- 不要把列表这种可变对象直接写成默认值：默认值只在函数定义时创建一次，多次调用会共享同一个对象。C++ 的默认参数是每次调用都重新求值，没有这个坑，这是 Python 特有的经典陷阱
- `range(start, end + 1)`：因为 `range()` 不包含终点，想让循环数到 `end` 本身，得手动 +1

## 知识点 3：关键字实参 —— C/C++ 没有的调用方式

**概念**

调用函数时可以写成 `参数名=值` 的形式，按名字而不是位置对应形参，此时实参的书写顺序可以和定义时的形参顺序不一样。比如：

```python
def hello(greeting, title, first, last):
    print(f"{greeting} {title} {first} {last}")

hello("Hello", title="Mr.", last="Squarepants", first="Spongebob")
# Hello Mr. Spongebob Squarepants
```

`"Hello"` 按位置传给 `greeting`，`title`、`last`、`first` 全部按参数名匹配，所以 `last` 写在 `first` 前面也没关系。

**这是和 C/C++ 差别最大的地方之一**：C/C++ 函数调用严格按参数位置传值（哪怕用了默认参数，调用时也只能省略末尾的参数，不能跳过中间某个、只传后面的），Python 的关键字实参可以任意指定要覆盖哪个参数、顺序随意。

**易错点**

- 位置实参必须写在关键字实参前面，顺序反了会报错
- 同一个形参不能既按位置又按关键字重复赋值一次
- 定义里的 `start=0` 是"默认值"，调用里的 `start=1` 是"关键字传参"，两者都用等号，但含义不同，别搞混

## 知识点 4：`*args` 与 `**kwargs` —— 收集不定数量的实参

**概念**

调用时参数个数不确定的情况下，定义函数时用 `*args` 收集多余的位置实参（函数体内 `args` 是一个元组），用 `**kwargs` 收集多余的关键字实参（函数体内 `kwargs` 是一个字典）。这是 C/C++ 里完全没有对应写法的机制（C 的可变参数 `...` / `va_list` 语法和用法都差很远，也拿不到参数名）。

**常用语法**

```python
def add(*args):
    total = 0
    for arg in args:       # 依次取出每个位置实参
        total += arg
    return total            # return 必须在 for 外面

add(1, 2, 3)  # args 是 (1, 2, 3)，返回 6；不传参数时 args 是空元组，返回 0

def print_address(**kwargs):
    for key, value in kwargs.items():   # 拆成 key、value 两个变量
        print(f"{key}: {value}")

print_address(street="123 Fake St.", city="Detroit", state="MI", zip="54321")
# street: 123 Fake St.
# city: Detroit
# state: MI
# zip: 54321
```

- `kwargs.keys()` / `values()` / `items()` 分别拿键、值、键值对
- 同时收集两类实参：`def shipping_label(*args, **kwargs)`，`*args` 必须写在 `**kwargs` 前面
- 调用时反过来用：已经有一个列表/字典，想把它"展开"成多个实参传进去，用 `func(*numbers)` 或 `func(**address)`——`*` / `**` 在定义处是"收集"，在调用处是"展开"，是同一套符号的两种用法

**易错点**

- `args`、`kwargs` 只是约定俗成的名字，真正起作用的是 `*` 和 `**` 这两个符号；调用 `add(1, 2, 3)` 时不需要、也不能在调用处加星号
- `return total` 缩进错位到 `for` 循环里面，第一次循环加完就会直接返回，只加了一个数
- 遍历 `kwargs` 或 `kwargs.keys()` 效果一样，都只是拿到键；想同时要键和值必须用 `items()`
- `kwargs.get('street')`：键不存在时返回 `None`，格式化后会显示成字符串 `"None"`；可以写 `kwargs.get('street', '未填写')` 指定缺失时的替代文本

## 今日总结

- 掌握了：函数定义和 return 的基本用法、默认参数、关键字实参（Python 特有的按名传参）、`*args`/`**kwargs` 收集不定数量的实参
- 还需加强：`*args`/`**kwargs` 在"展开已有容器传参"（调用处的 `*` / `**`）这个方向还需要多写几个例子巩固
