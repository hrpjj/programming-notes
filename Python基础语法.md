# Python 核心语法速查表（含代码示例）

> 面向已经学过 Python、需要快速回忆语法的读者。本文默认使用 **Python 3.10+**，每个知识点只保留用途和最小示例。

@[TOC](Python 核心语法速查目录)

<font color="#1E88E5"><b>蓝色：用途说明</b></font>　
<font color="#2E7D32"><b>绿色：推荐写法</b></font>　
<font color="#EF6C00"><b>橙色：版本或补充信息</b></font>

---

## 1. 对象、类型与复制

这一章讲的是 Python 最底层、也最容易被人跳过的一件事：**变量到底是什么**。字符串、列表、字典的几乎所有"奇怪行为"，根源都在这里。

先建立三个必须记住的结论，后面的内容都是它们的展开：

1. **变量是贴在对象上的名字，不是装对象的盒子。** `a = [1, 2]` 不是"把列表放进 a"，而是"创建了一个列表对象，然后给它贴上一个叫 a 的标签"。
2. **赋值永远不会复制对象。** `b = a` 只是给同一个对象又贴了一个标签，两个名字指向同一份数据。
3. **对象分可变和不可变两类。** 可变对象能原地修改，于是所有指向它的名字都会"跟着变"；不可变对象改了就是新对象，原来的名字不受影响。

理解了这三条，"为什么我改了 b，a 也变了"这类问题就不用再靠试错了。

### 1.1 变量是名字，对象才有类型

**在 Python 里没有"变量的类型"，只有"对象的类型"。** 同一个名字可以先指向整数，再指向字符串，解释器不会阻止你：

```python
value = 100
print(type(value))        # <class 'int'>

value = "一百"
print(type(value))        # <class 'str'>，同一个名字换了对象类型

value = [1, 2]
print(type(value))        # <class 'list'>
```

所以判断类型时，你判断的永远是"**这个名字当前指向的对象**是什么类型"。这也是为什么写代码时不要依赖"变量类型"，而要依赖"对象类型"。

#### `type()` 和 `isinstance()` 的区别

```python
value = True

print(type(value))                    # <class 'bool'>
print(isinstance(value, bool))        # True
print(isinstance(value, (int, str)))  # True，bool 是 int 的子类
print(isinstance(value, float))       # False
```

- `type(x)` 返回**精确类型**，用于"到底是什么"。
- `isinstance(x, T)` 判断"是不是 T 或其子类"，用于"能不能当 T 用"。

**实践中的选择：** 做类型判断时优先用 `isinstance()`，因为它能正确处理继承关系。`bool` 是 `int` 的子类，所以 `isinstance(True, int)` 是 `True`；而 `type(True) is int` 是 `False`。

```python
print(isinstance(True, int))   # True：bool 继承自 int
print(type(True) is int)       # False：精确类型是 bool

# 用 type() 做相等判断会漏掉子类，所以不推荐
def describe(value):
    if isinstance(value, int):
        return "整数（含布尔）"
    return "其他"

print(describe(True))          # 整数（含布尔）
```

#### `repr()` 与 `str()`：两种"变成文字"的方式

这两个函数都能把对象转成字符串，但用途不同：

- `str()` 给**人看的**可读形式；
- `repr()` 给**开发者看的**精确形式，理想情况下复制粘贴回去能得到同样的对象。

```python
text = "hello"

print(str(text))     # hello，没有引号
print(repr(text))    # 'hello'，带引号，能看出这是字符串

value = [1, "a", None]
print(str(value))    # [1, 'a', None]
print(repr(value))   # [1, 'a', None]，容器内部的字符串会用 repr 显示
```

**为什么在调试时 `repr()` 更可靠？** 因为它能让你看出"到底是字符串 `123` 还是数字 `123`"——`str()` 会把两者都显示成 `123`，`repr()` 会显示 `'123'` 和 `123`。

### 1.2 真假值：什么算"假"

Python 里任何对象都能当条件用，**假的只有固定的一小撮**：

| 类别 | 假值 | 说明 |
|:---|:---|:---|
| 空值 | `None` | 表示"没有值" |
| 布尔 | `False` | |
| 数字零 | `0`、`0.0`、`0j`、`Decimal(0)` | 任何等于零的数字 |
| 空容器 | `""`、`[]`、`()`、`{}`、`set()`、`range(0)` | 长度为 0 |
| 自定义对象 | 定义了 `__bool__` 返回 `False`，或 `__len__` 返回 0 | |

**除此之外的对象都是真值**，包括 `"0"`、`"False"`、`[0]`、`[[]]` 这些"看起来像假"的东西：

```python
print(bool(""))        # False，空字符串
print(bool("0"))       # True！非空字符串，哪怕内容是 0
print(bool("False"))   # True！非空字符串

print(bool([]))        # False，空列表
print(bool([0]))       # True：列表中有一个元素
print(bool([[]]))      # True：内层是空列表，但外层有一个元素

print(bool(0))         # False
print(bool(0.0))       # False
print(bool(None))      # False
```

**最常见的坑：把字符串 `"0"` 或 `"False"` 当成假值。** 它们都是非空字符串，一律为真。要做这种判断必须显式比较：

```python
answer = "False"

if answer:                    # 注意：这里永远成立
    print("会被执行，因为非空字符串是真值")

if answer == "False":         # 正确：显式比较
    print("这才是判断内容是否为 False")
```

#### 用 `not` 和 `bool()` 的惯用写法

```python
items = []

if not items:
    print("列表为空")          # 惯用写法，等价于 len(items) == 0

if items:
    print("列表非空")

# 需要显式拿到真假值时用 bool()
print(bool(items))            # False
```

**为什么推荐 `if not items:` 而不是 `if len(items) == 0:`？** 前者对任何"空"的对象都成立（空字符串、空字典、`None`），而且更快、更符合 Python 风格。前提是你确实想表达"空"，而不是"长度恰好是 0"。

#### `any()` 与 `all()`：对整个序列做真假判断

```python
print(any([0, 1, 0]))   # True：至少有一个为真
print(all([1, 2, 3]))   # True：全部为真
print(any([]))          # False：空序列，没有任何一个为真
print(all([]))          # True：空序列，"全部为真"是空真（vacuous truth）
```

`all([])` 返回 `True` 是很多人觉得反直觉的地方，但逻辑上说得通：没有任何一个元素为假。写代码时要记住这个边界，否则可能把"空数据"误判成"全部合格"。

**实用例子：检查输入是否都合法：**

```python
names = ["Alice", "", "Bob"]

print(all(names))              # False：有空字符串
print(any(names))              # True：至少有一个非空
print(all(name.strip() for name in names))   # False，用生成器逐个判断
```

### 1.3 `==` 比较值，`is` 比较身份

这是本章最容易混的一对操作符：

- `a == b` 问的是"**两个对象的内容相等吗**"，由对象的 `__eq__` 决定；
- `a is b` 问的是"**这两个名字是不是指向同一个对象**"，比较的是内存地址。

```python
a = [1, 2]
b = [1, 2]
c = a

print(a == b)   # True：内容相同
print(a is b)   # False：是两个不同的列表对象
print(a is c)   # True：c 就是 a 的另一个名字
```

用图来理解：`a` 和 `b` 是两个内容一样的盒子，`a` 和 `c` 是同一个盒子上的两张标签。

#### `is` 只该用在三个场景

**场景一：判断 `None`。** 这是最标准的用法：

```python
result = None

print(result is None)      # True，推荐
print(result == None)      # True，但不推荐（见下面的坑）
```

**为什么判断 `None` 必须用 `is`？** 因为 `==` 会调用对象的 `__eq__`，某些对象的 `__eq__` 实现可能让它"等于 None"，从而产生意外结果。`is` 不调用任何自定义逻辑，永远不会被骗。

**场景二：需要确认"就是同一个对象"**，比如缓存、单例、避免重复处理：

```python
cache = {}
key = "user:1"

if key not in cache:
    cache[key] = {"name": "Alice"}
    print("第一次查询，已缓存")

value1 = cache[key]
value2 = cache[key]
print(value1 is value2)    # True：命中的是同一个对象
```

**场景三：判断 `True` / `False`**（可选，写成 `if flag:` 更常见）。

#### 为什么不能用 `is` 比较数值

这是 CPython 的实现细节造成的陷阱。小整数和短字符串会被**缓存/驻留**（interning），于是"看起来能用 `is` 比较"，换个值就失效：

```python
a = 256
b = 256
print(a is b)      # True：CPython 缓存了 -5 到 256 的小整数

x = 257
y = 257
print(x is y)      # False（在脚本文件中通常如此）：超出缓存范围，是两个对象
print(x == y)      # True：值相等，这才是可靠的判断
```

```python
s1 = "hello"
s2 = "hello"
print(s1 is s2)    # True：短字符串被驻留

s3 = "hello world!"
s4 = "hello world!"
print(s3 is s4)    # 结果取决于实现，不可依赖
print(s3 == s4)    # True：永远用 == 比较字符串内容
```

**结论：永远用 `==` 比较值，只在判断 `None` 或确认对象同一性时用 `is`。** 上面这些 `is` 返回 `True` 的情况是解释器的优化，不是语言保证，换一个 Python 版本或换一种运行方式（脚本 vs REPL）就可能变。

### 1.4 可变对象与不可变对象

这是本章的核心概念。判断标准很简单：**创建之后能不能原地修改**。

| 类型 | 可变性 | 常见类型 | 修改时的行为 |
|:---|:---:|:---|:---|
| 不可变 | 创建后不能原地修改 | `int`、`float`、`bool`、`str`、`tuple`、`frozenset`、`None` | 只能创建新对象 |
| 可变 | 可以原地增删改 | `list`、`dict`、`set`、自定义类的实例 | 原地修改，所有引用同步可见 |

#### 不可变对象：改了就是新对象

```python
text = "Py"
new_text = text + "thon"

print(text)                # Py，原字符串没变
print(new_text)            # Python
print(text is new_text)    # False：是新对象

number = 10
number = number + 5        # 看起来是"改了 number"
print(number)              # 15
# 其实：新建了整数对象 15，然后让 number 这个名字指向它
```

**"字符串不能改"的实际含义：** 你不能改这个字符串的某个字符，但可以让名字指向一个新字符串。所以字符串有大量"返回新字符串"的方法：

```python
text = "  hello  "

print(text.strip())     # hello，返回新字符串
print(text)             #   hello  ，原字符串完全没变
print(text.upper())     #   HELLO  ，也是新字符串
```

#### 可变对象：原地修改，所有名字一起变

```python
numbers = [1, 2]
alias = numbers              # 只是多一个名字，不是复制

alias.append(3)              # 通过 alias 原地修改
print(numbers)               # [1, 2, 3]，numbers 也"变了"
print(numbers is alias)      # True：本来就是同一个对象
```

**这是初学者最常踩的坑：** 以为 `alias = numbers` 复制了一份，结果改动互相影响。要真正独立，必须显式复制（见 1.5 节）。

#### 正确区分"修改"和"重新绑定"

同一个名字，写法不同，行为完全不同：

```python
data = [1, 2, 3]
other = data

data.append(4)      # 修改：原地改动对象，other 也看到变化
print(data, other)  # [1, 2, 3, 4] [1, 2, 3, 4]

data = [9, 9]       # 重新绑定：让 data 指向一个全新的列表
print(data, other)  # [9, 9] [1, 2, 3, 4]，other 还指着老对象
```

一句话总结：**`.方法()` 和 `[下标] =` 是修改对象；`名字 =` 是重新绑定名字。** 分清这两类操作，就不会再被"为什么有时会互相影响、有时不会"搞混。

### 1.5 传参时发生了什么（值传递的真相）

Python 的参数传递既不是"传值"也不是"传引用"，准确说法是**传递对象的引用**（类似其他语言说的"按对象共享调用"）。这解释了一个常见困惑：

```python
def add_item(items):
    items.append("新元素")        # 修改传入的对象

def rebind(items):
    items = ["全新的列表"]         # 只重新绑定局部名字

cart = ["苹果"]

add_item(cart)
print(cart)          # ['苹果', '新元素']，原对象被改了

rebind(cart)
print(cart)          # ['苹果', '新元素']，没变！函数里的 items 只是个局部名字
```

**规则：** 函数内 **修改** 参数对象，调用者能看到；函数内 **重新绑定** 参数名字，调用者看不到。

这也解释了为什么不可变对象传进函数后"永远不会被改"——因为对它们的任何"修改"本质上都是重新绑定：

```python
def add_suffix(text):
    text = text + "!"            # 重新绑定，与调用者无关
    return text

word = "hello"
result = add_suffix(word)

print(word)      # hello，没变
print(result)    # hello!，新对象
```

**实践建议：** 如果函数不想改动传入的可变对象，就在函数内部先复制一份：

```python
def add_item_safe(items):
    items = items[:]             # 先复制，后面的修改不影响调用者
    items.append("新元素")
    return items

cart = ["苹果"]
new_cart = add_item_safe(cart)

print(cart)        # ['苹果']，调用者的数据安全
print(new_cart)    # ['苹果', '新元素']
```

### 1.6 浅拷贝与深拷贝

要"独立一份"，就得复制。但复制分两个层次：

- **浅拷贝**：只复制最外层容器，里面的元素还是原来那些对象；
- **深拷贝**：递归复制所有层级，得到完全独立的一份数据。

#### 浅拷贝的四种写法

```python
import copy

source = [1, 2, 3]

by_slice = source[:]              # 切片
by_constructor = list(source)     # 构造
by_method = source.copy()         # copy() 方法（list/dict/set 都有）
by_module = copy.copy(source)     # 通用写法，任何对象都能用

print(by_slice, by_constructor, by_method, by_module)
# [1, 2, 3] [1, 2, 3] [1, 2, 3] [1, 2, 3]
print(source is by_slice)         # False，是新对象
```

四种写法对一维数据效果一样。`copy.copy()` 的优势是通用——元组、自定义对象、嵌套结构都能用。

#### 浅拷贝"浅"在哪里

浅拷贝只复制"容器这一层"，元素如果是可变对象，两个容器仍然共享它：

```python
import copy

source = [[1, 2], [3, 4]]
shallow = copy.copy(source)

print(source is shallow)        # False：外层确实是两个不同的列表
print(source[0] is shallow[0])  # True：内层还是同一个列表！

source[0].append(99)            # 修改内层
print(source)                   # [[1, 2, 99], [3, 4]]
print(shallow)                  # [[1, 2, 99], [3, 4]]，跟着变了
```

**判断依据：** 如果容器里的元素都是不可变对象（数字、字符串、元组），浅拷贝就等于"完全独立"，够用了；一旦嵌套了列表或字典，浅拷贝就会留下共享。

#### 深拷贝：完全独立，代价更高

```python
import copy

source = [[1, 2], [3, 4]]
deep = copy.deepcopy(source)

print(source[0] is deep[0])     # False：内层也是新对象

source[0].append(99)
print(source)                   # [[1, 2, 99], [3, 4]]
print(deep)                     # [[1, 2], [3, 4]]，完全不受影响
```

**什么时候用哪个？**

| 数据结构 | 推荐 | 原因 |
|:---|:---|:---|
| 一维列表/字典，元素不可变 | 切片、`list()`、`copy()` | 够用，最快 |
| 嵌套列表/字典 | `copy.deepcopy()` | 浅拷贝会留下共享 |
| 自定义对象，属性里有可变对象 | `copy.deepcopy()` | 需要连带复制属性 |
| 数据里有循环引用 | `copy.deepcopy()` | 它能正确处理循环引用 |

**深拷贝的代价：** 它要递归遍历整个结构并创建新对象，数据量大时明显更慢、更占内存。所以不要无脑用 `deepcopy`，先判断层级。

#### 一个真实场景：二维表格

```python
import copy

# 一个 3x3 的表格
table = [[0] * 3 for _ in range(3)]

# 想把第一行单独拿出来做实验，不能直接赋值
experiment = copy.deepcopy(table)
experiment[0][0] = 9

print(table)         # [[0, 0, 0], [0, 0, 0], [0, 0, 0]]，原表格干净
print(experiment)    # [[9, 0, 0], [0, 0, 0], [0, 0, 0]]
```

注意这里创建表格用的是 `[[0] * 3 for _ in range(3)]` 而不是 `[[0] * 3] * 3`——后者会让三行共用同一个列表，这个坑在第 3 章详细讲过。

### 1.7 可变默认参数：最隐蔽的陷阱

这是 Python 最著名的坑之一。先看现象：

```python
def add_item(item, items=[]):
    items.append(item)
    return items

print(add_item("a"))     # ['a']
print(add_item("b"))     # ['a', 'b']，上一次的结果还在！
print(add_item("c"))     # ['a', 'b', 'c']
```

**为什么？** 默认参数的值**只在函数定义时求值一次**，`[]` 这个列表对象被创建后一直挂在函数对象上，每次调用不传 `items` 时用的都是它。

**正确写法：用 `None` 占位，在函数内部创建。**

```python
def add_item(item, items=None):
    if items is None:
        items = []          # 每次调用都新建一个列表
    items.append(item)
    return items

print(add_item("a"))     # ['a']
print(add_item("b"))     # ['b']，干净了
print(add_item("c"))     # ['c']
print(add_item("d", ["已有"]))   # ['已有', 'd']，显式传入时用传入的
```

**同样的坑也存在于字典和其他可变对象：**

```python
# 错误
def bad(config={}):
    config["used"] = True
    return config

# 正确
def good(config=None):
    if config is None:
        config = {}
    config["used"] = True
    return config

print(bad())    # {'used': True}
print(bad())    # {'used': True}，但其实是同一个字典
print(good())   # {'used': True}
print(good())   # {'used': True}，每次都是新字典
```

**判断规则：默认值里出现 `[]`、`{}`、`set()` 或任何可变对象，就有这个风险。** 不可变默认值（数字、字符串、`None`、元组）没有这个问题：

```python
def greet(name, greeting="你好"):    # 字符串不可变，安全
    return f"{greeting}，{name}"

print(greet("Alice"))    # 你好，Alice
print(greet("Bob"))      # 你好，Bob
```

### 1.8 本章小结

**四个必须记住的结论：**

1. 变量是名字，对象才有类型；`a = b` 不复制，只是多一个名字。
2. `==` 比较值，`is` 比较身份；判 `None` 用 `is`，判值用 `==`。
3. 可变对象原地改，所有引用一起变；不可变对象改了就是新对象。
4. 函数参数也是"引用"：改对象调用者能看到，重新绑定名字看不到。

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 查看精确类型 | `type(x)` |
| 判断"是不是某类" | `isinstance(x, T)`（能处理继承） |
| 判断为空 | `if not x:` |
| 判断 `None` | `if x is None:` |
| 判断值相等 | `if a == b:` |
| 判断同一对象 | `if a is b:` |
| 复制一维列表 | `x[:]`、`list(x)`、`x.copy()`、`copy.copy(x)` |
| 复制嵌套结构 | `copy.deepcopy(x)` |
| 函数安全的可变默认值 | `def f(items=None)` 再在函数内建 |

**四个坑：**

| 坑 | 表现 | 正确做法 |
|:---|:---|:---|
| 以为赋值是复制 | 改 `b` 影响了 `a` | 显式 `copy` / 切片 |
| 用 `is` 比较数值 | 小值对、大值错 | 一律用 `==` |
| 浅拷贝嵌套结构 | 内层改动互相影响 | `copy.deepcopy()` |
| 可变默认参数 | 调用之间数据累积 | 用 `None` 占位 |

**往下衔接：** 这一章建立了"对象—名字—可变性"的模型，第 3 章的容器、第 5 章的传参、第 10 章的对象属性都会反复用到它。

---


---

## 2. 字符串

字符串是 Python 里最常用的类型之一，也是最容易被"用错而不报错"的类型。这一章讲清四件事：**字符串不可变意味着什么、切片规则、编码到底在做什么、以及格式化怎么写最清楚**。

### 2.1 字符串为什么不可变

第 1 章讲过：`str` 是不可变对象。它的实际影响是——**所有"修改字符串"的方法都返回新字符串，原字符串一动不动。**

```python
text = "  hello  "

print(text.strip())      # hello，返回新字符串
print(text.upper())      #   HELLO  ，也是新字符串
print(text.replace("h", "H"))   #   Hello  ，还是新字符串

print(text)              #   hello  ，原字符串从没变过
```

**为什么这个设计重要？** 因为不可变对象可以被安全共享。字符串是使用最频繁的类型，如果可变，任何一处修改都可能影响别的地方，而且没法安全地做缓存（哈希值会变）。Python 用"不可变"换来了效率和安全性。

**推论一：字符串方法都能链式调用**，因为每一步都返回新字符串：

```python
text = "  Hello, Python World  "

result = text.strip().lower().replace(",", "").replace(" ", "-")
print(result)     # hello-python-world
print(text)       #   Hello, Python World  ，原串不变
```

**推论二：想"原地修改"必须重新赋值**，这就是最常见的写法：

```python
text = "hello"
text = text.upper()      # 让 text 指向新字符串
print(text)              # HELLO
```

**推论三：循环里反复用 `+` 拼字符串很慢**，因为每次都创建新对象并复制已有内容：

```python
# 慢：每次都新建字符串并复制前缀，n 次就是 O(n²)
slow = ""
for i in range(1000):
    slow += str(i)

# 快：先收集到列表，最后一次性拼接（join 只遍历一次）
parts = []
for i in range(1000):
    parts.append(str(i))
fast = "".join(parts)

print(slow == fast)      # True，结果一样
```

**`join()` 是拼接多个字符串的标准做法**，注意它的调用方式容易记反——**分隔符在前，列表在后**：

```python
words = ["Python", "Java", "Go"]

print(" | ".join(words))     # Python | Java | Go
print("".join(words))        # PythonJavaGo，无分隔
print("-".join(["2026", "10", "08"]))   # 2026-10-08，拼日期

# 记反了就会报错
# words.join(" | ")          # 报错：AttributeError，列表没有 join 方法
```

### 2.2 定义字符串的五种写法

```python
single = '单引号'                   # 单引号
double = "双引号"                   # 双引号，和单引号完全等价
triple = """三个双引号
可以跨行"""                          # 多行字符串
raw = r"C:\Users\demo"              # 原始字符串，不处理转义
fstring = f"结果是 {1 + 1}"          # f-string，可嵌入表达式

print(single, double)               # 单引号 双引号
print(triple)                       # 三引号字符串保留换行
print(raw)                          # C:\Users\demo，反斜杠不被转义
print(fstring)                      # 结果是 2
```

**单引号和双引号没有区别**，选哪个只看哪种能少转义：

```python
print("他说：'你好'")        # 他说：'你好'，外面双引号，里面单引号不用转义
print('他说："你好"')        # 他说："你好"，反过来也一样
print('他说：\'你好\'')      # 需要转义时的写法，不推荐
```

**原始字符串 `r"..."` 的用途**是让反斜杠保持字面意义，写 Windows 路径和正则表达式时特别有用：

```python
print("C:\new\test")        # C:
                            # ew	est，\n 和 \t 被当成转义了！
print(r"C:\new\test")       # C:\new\test，原样输出

# 正则表达式几乎都要用原始字符串
import re
print(re.findall(r"\d+", "订单 123 和 456"))   # ['123', '456']
```

### 2.3 索引与切片

字符串的切片规则和列表完全一致：**左闭右开、支持负数和步长**。

```python
text = "Python"

print(text[0])      # P，第一个字符
print(text[-1])     # n，最后一个字符
print(text[1:4])    # yth，下标 1 到 3
print(text[:3])     # Pyt，从头到下标 2
print(text[3:])     # hon，下标 3 到末尾
print(text[::2])    # Pto，隔一个取一个
print(text[::-1])   # nohtyP，反转字符串
print(text[100:])   # 空字符串，切片越界不报错（与索引不同）
```

**切片越界不会报错，但索引越界会**，这是两者的重要区别：

```python
text = "abc"

print(text[10:20])      # 空字符串，安全
# print(text[10])       # 报错：IndexError: string index out of range
```

**常用切片场景：**

```python
filename = "report_2026.csv"

print(filename[-3:])        # csv，取扩展名（不含点）
print(filename[:-4])        # report_2026，去掉扩展名
print(filename[:6])         # report，取前缀
print(filename[7:11])       # 2026，取中间一段

text = "hello world"
print(text[:5], text[6:])   # hello world，按空格拆成两半
```

### 2.4 查找与判断

```python
text = "  Hello, Python World  "

print(len(text))                    # 24，长度（含空格）
print(text.startswith("  He"))      # True，是否以某内容开头
print(text.endswith("  "))          # True，是否以某内容结尾
print("Python" in text)             # True，是否包含
print(text.find("Python"))          # 9，第一次出现的位置（找不到返回 -1）
print(text.index("Python"))         # 9，同上，但找不到会抛 ValueError
print(text.count("o"))              # 2，出现了几次
```

**`find()` 和 `index()` 的区别就是"找不到时怎么办"：**

```python
text = "hello"

print(text.find("z"))       # -1，安全
# text.index("z")           # 报错：ValueError: substring not found

# 只想判断存在性时用 in，最直观
print("z" in text)          # False
```

### 2.5 大小写与空白处理

```python
text = "  Hello, Python World  "

print(text.strip())          # 去掉两端空白
print(text.lstrip())         # 只去左边
print(text.rstrip())         # 只去右边
print(text.lower())          # 全部小写
print(text.upper())          # 全部大写
print(text.title())          # 每个单词首字母大写
print(text.swapcase())       # 大小写互换
print("hello world".capitalize())   # 只有首字母大写
```

**`strip()` 系列还能去掉指定字符**，不只是空白：

```python
print("---abc---".strip("-"))     # abc，去掉两端的 -
print("###标题###".strip("#"))    # 标题
print("abc\n".rstrip())           # abc，处理读文件时常见的换行符
print("xxabcxx".strip("x"))       # abc
```

**读文件时最常见的写法就是 `line.rstrip("\n")`**，因为文件每行末尾都有换行符。

### 2.6 分割与替换

```python
line = "Python,Java,Go"

print(line.split(","))              # ['Python', 'Java', 'Go']，按逗号分割
print(line.split(",", maxsplit=1))  # ['Python', 'Java,Go']，最多切一刀
print(line.split())                 # ['Python,Java,Go']，不传参数按任意空白分割
print("a  b   c".split())           # ['a', 'b', 'c']，连续空白当成一个分隔
print(line.replace(",", " | "))     # Python | Java | Go，替换全部
print(line.replace(",", " | ", 1))  # Python | Java,Go，只替换第一次
```

**不传参数的 `split()` 是处理用户输入的利器**，它会自动处理多余空格：

```python
user_input = "  Alice   20   深圳  "

parts = user_input.split()
print(parts)          # ['Alice', '20', '深圳']

name, age, city = parts
print(name, int(age), city)     # Alice 20 深圳
```

**`splitlines()` 按行分割**，比 `split("\n")` 更严谨（能处理 `\r\n`）：

```python
text = "第一行\n第二行\r\n第三行"

print(text.splitlines())    # ['第一行', '第二行', '第三行']
print(text.split("\n"))     # ['第一行', '第二行\r', '第三行']，\r 残留了
```

### 2.7 编码：为什么会有乱码

**计算机只存字节，字符串是人读的字符，编码就是"字符 ↔ 字节"的翻译规则。**

```python
text = "你好"

# 编码：字符串 → 字节
data_utf8 = text.encode("utf-8")
data_gbk = text.encode("gbk")

print(data_utf8)        # b'\xe4\xbd\xa0\xe5\xa5\xbd'，UTF-8 用 3 字节一个汉字
print(data_gbk)         # b'\xc4\xe3\xba\xc3'，GBK 用 2 字节一个汉字
print(len(data_utf8), len(data_gbk))   # 6 4，字节数不同

# 解码：字节 → 字符串
print(data_utf8.decode("utf-8"))       # 你好
print(data_gbk.decode("gbk"))          # 你好
```

**乱码的根本原因：用 A 规则编码，却用 B 规则解码。**

```python
try:
    data_utf8.decode("gbk")            # 用 GBK 解 UTF-8 的字节
except UnicodeDecodeError as error:
    print("解码失败：", error)          # 解码失败：'gbk' codec can't decode ...

# 有些组合不会报错，但会解出乱码（更危险，因为不报错）
print(data_gbk.decode("utf-8", errors="replace"))   # 一堆替换字符
```

**实践规则：**

| 场景 | 做法 |
|:---|:---|
| 读写文件 | **永远显式写 `encoding="utf-8"`** |
| 处理中文乱码 | 先确认原始编码，再 `.decode(那个编码)` |
| 不确定编码 | 用 `errors="replace"` 或 `errors="ignore"` 容错，但要意识到会丢数据 |
| 网络/接口 | 统一用 UTF-8，这是事实标准 |

```python
# 读文件时不写 encoding 会依赖系统默认编码，跨平台就是隐患
with open("demo.txt", "w", encoding="utf-8") as file:
    file.write("中文内容")

with open("demo.txt", "r", encoding="utf-8") as file:
    print(file.read())      # 中文内容
```

**关于 `len()` 的一个细节：它数的是字符数，不是字节数。**

```python
text = "你好abc"

print(len(text))                    # 5，5 个字符
print(len(text.encode("utf-8")))    # 9，UTF-8 下 3+3+1+1+1 字节
print(len(text.encode("gbk")))      # 7，GBK 下 2+2+1+1+1 字节
```

**为什么 `len` 数字符？** 因为 Python 3 的字符串是"字符序列"，而不同编码下同一个字符占的字节数不同。这个区分在处理中英混排的宽度计算、截断时很重要。

### 2.8 格式化：三种写法与推荐

```python
name = "Alice"
score = 92.567

# 1. % 格式化：老写法，Python 2 时代的主流
print("姓名：%s，成绩：%.2f" % (name, score))     # 姓名：Alice，成绩：92.57

# 2. str.format()：Python 2.6+ 引入，比 % 灵活
print("姓名：{}，成绩：{:.2f}".format(name, score))   # 姓名：Alice，成绩：92.57
print("姓名：{n}，成绩：{s:.2f}".format(n=name, s=score))  # 命名参数

# 3. f-string：Python 3.6+，推荐
print(f"姓名：{name}，成绩：{score:.2f}")          # 姓名：Alice，成绩：92.57
```

**为什么推荐 f-string？** 三个理由：要格式化的变量就写在它出现的位置（不用回头对参数顺序）、支持任意表达式、最快。

```python
price = 19.9
count = 3

print(f"总价：{price * count:.2f} 元")        # 总价：59.70 元，直接写表达式
print(f"单价 {price} × {count} 件")           # 单价 19.9 × 3 件

# 调试神器：3.8+ 可以在表达式后面加 = 自动打印"表达式 = 值"
print(f"{price * count = }")                  # price * count = 59.699999999999996
```

#### 数字格式化的常用格式符

```python
number = 1234567.8912

print(f"{number:.2f}")      # 1234567.89，保留 2 位小数
print(f"{number:,.2f}")     # 1,234,567.89，千分位分隔
print(f"{number:.0f}")      # 1234568，四舍五入到整数
print(f"{0.875:.1%}")       # 87.5%，百分比
print(f"{0.875:.2%}")       # 87.50%，百分比保留 2 位

print(f"{42:5d}")           #    42，宽度 5 右对齐
print(f"{42:<5d}|")         # 42   |，左对齐
print(f"{42:^5d}|")         #  42  |，居中
print(f"{42:05d}")          # 00042，用 0 填充

print(f"{255:b}")           # 11111111，二进制
print(f"{255:o}")           # 377，八进制
print(f"{255:x}")           # ff，十六进制
print(f"{255:#x}")          # 0xff，带前缀
```

**对齐语法要记住：`:` 后面依次是"填充字符 + 对齐符 + 宽度 + 类型"。**

```python
# 制作对齐的表格输出
rows = [("Alice", 92.5), ("Bob", 85.0)]
for name, score in rows:
    print(f"{name:<10}{score:>8.1f}")
# Alice         92.5
# Bob           85.0
```

### 2.9 字符串与数字的互转

```python
# 字符串 → 数字
print(int("42"))                # 42
print(int("  42  "))            # 42，自动去空白
print(float("3.14"))            # 3.14
print(int("ff", 16))            # 255，按 16 进制解析
print(int("1010", 2))           # 10，按 2 进制解析

# 数字 → 字符串
print(str(42))                  # '42'
print(str(3.14))                # '3.14'
print(f"{42}")                  # '42'，f-string 也能转

# 错误处理：不是所有字符串都能转
try:
    int("abc")
except ValueError as error:
    print("转换失败：", error)   # 转换失败：invalid literal for int() with base 10: 'abc'

# 安全的转换写法
def to_int(text, default=0):
    try:
        return int(text)
    except (ValueError, TypeError):
        return default

print(to_int("42"))         # 42
print(to_int("abc"))        # 0
print(to_int(None))         # 0，TypeError 也被兜住了
```

### 2.10 字符串判断方法

处理用户输入和数据清洗时常用：

```python
print("123".isdigit())        # True，全是数字
print("12.3".isdigit())       # False，小数点不算
print("abc".isalpha())        # True，全是字母
print("abc123".isalnum())     # True，字母或数字
print("   ".isspace())        # True，全是空白
print("Hello".isupper())      # False
print("HELLO".isupper())      # True
print("hello".islower())      # True
print("Hello World".istitle())  # True，每个单词首字母大写

# 实用场景：判断输入是不是纯数字
user_input = "13800138000"
if user_input.isdigit():
    print("是纯数字，可以当手机号格式初筛")
```

**注意 `isdigit()` 对负号和小数点返回 `False`**，判断"能不能转成数字"更可靠的方式还是 `try/except`：

```python
for text in ["123", "-5", "3.14", "abc"]:
    try:
        float(text)
        print(f"{text!r} 可以转成数字")
    except ValueError:
        print(f"{text!r} 不能转成数字")
# '123' 可以转成数字
# '-5' 可以转成数字
# '3.14' 可以转成数字
# 'abc' 不能转成数字
```

### 2.11 本章小结

**核心结论：** 字符串不可变，所有方法都返回新字符串；`join()` 是拼接的标准做法；读写文件必须显式指定 `encoding="utf-8"`。

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 去掉两端空白/字符 | `text.strip()`、`text.strip("-")` |
| 拼接一批字符串 | `"分隔符".join(list)` |
| 分割 | `text.split(",")`、`text.split()`、`text.splitlines()` |
| 替换 | `text.replace("旧", "新")` |
| 查找位置 | `text.find(x)`（找不到返回 -1） |
| 判断包含 | `x in text` |
| 反转 | `text[::-1]` |
| 取扩展名 | `filename[-3:]` |
| 编码/解码 | `text.encode("utf-8")` / `data.decode("utf-8")` |
| 格式化 | `f"{var}"`、`f"{num:.2f}"`、`f"{num:,}"` |
| 对齐输出 | `f"{name:<10}{score:>8.1f}"` |
| 调试打印 | `f"{expr = }"` |

**五个坑：**

1. **循环里用 `+=` 拼字符串** —— 用列表收集再 `join`。
2. **`join` 的参数顺序记反** —— 分隔符在前：`",".join(items)`。
3. **读文件不写 `encoding`** —— 跨平台会乱码，一律显式写 UTF-8。
4. **`len()` 数的是字符不是字节** —— 中英混排时要注意这个区别。
5. **`isdigit()` 不等于"能转数字"** —— 负号、小数点都会让它返回 False。

**往下衔接：** 这一章的 `split()`、`strip()`、`join()` 是第 9 章读文件、解析 CSV/JSON 的基础工具。

---

## 3. 列表、元组、字典与集合

前一章讲的是字符串，它和这一章的容器有一个共同点：**都是用来装多个值的数据结构**。区别在于，字符串只能装字符、而且不能改，容器则能装任意类型的对象，并且各自有不同的"改不改得动、找得快不快、能不能重复"的特性。

先把这一章的三个底层事实摆出来，后面所有细节都是它们的推论：

- **`list` 是动态数组。** 元素在一段连续内存里挨着放，所以按下标取元素是 $O(1)$（地址可以直接算出来），但在中间插一个元素必须把后面所有元素整体后移，代价是 $O(n)$。
- **`dict` 和 `set` 是哈希表。** 它们靠"把键算成一个位置"来存放数据，所以查找是接近 $O(1)$ 的常数时间，代价是键必须**可哈希**（能算出一个固定位置），并且不保证遍历顺序。
- **`tuple` 是不可变序列。** 创建之后长度和元素都不能变，换来的是它能当字典的键、能被安全地共享、语义上表示"一条固定结构的数据"。

理解了这三条，你在选容器时就不是背规则，而是在判断"我要的是快速随机访问、快速查找、还是不可变的结构"。

这一章按容器逐个展开：每个容器先讲它是什么、什么时候用，再把常用方法集中列完，最后单独说它的坑。第 1 章讲过的"引用"概念在这里会反复出现——**容器都是可变对象（元组除外），所以把容器赋给另一个名字，两个名字指向的是同一份数据**。

### 3.1 先选容器：四种容器的分工

选容器时按顺序问三个问题：

1. **元素允许重复吗？** 要去重就只能是 `set`（或把 `dict` 的键当集合用）。
2. **需要按下标/位置访问，还是按"名字"查？** 按位置访问用 `list` / `tuple`，按名字查用 `dict`。
3. **创建之后还需要改吗？** 不需要改就用 `tuple`，需要改就用 `list`。

| 容器 | 是否有序 | 是否可变 | 是否去重 | 典型用途 |
|:---|:---:|:---:|:---:|:---|
| `list` | 是 | 是 | 否 | 保存一组可增删的数据，按下标访问 |
| `tuple` | 是 | 否 | 否 | 保存固定结构的数据，可当字典的键 |
| `dict` | 按插入顺序 | 是 | 键唯一 | 建立"名字 → 值"的映射，快速查找 |
| `set` | 不保证顺序 | 是 | 是 | 去重、集合运算、快速判断存在性 |

**为什么"查找快"这件事值得单独强调？** 因为它决定了你写出的代码是"能跑"还是"跑得完"。看下面的对比：

```python
import time

data_list = list(range(200_000))
data_set = set(data_list)

target = 199_999

start = time.perf_counter()
result = target in data_list     # 列表要逐个比较，从前往后找
list_cost = time.perf_counter() - start

start = time.perf_counter()
result = target in data_set      # 集合算一次哈希就直接定位
set_cost = time.perf_counter() - start

print(f"列表查找耗时：{list_cost * 1000:.2f} 毫秒")   # 约 0.8 毫秒
print(f"集合查找耗时：{set_cost * 1000:.4f} 毫秒")    # 约 0.001 毫秒
```

同一件事，`list` 要把 20 万个元素逐个比一遍，`set` 只算一次哈希。数据量再大十倍，列表的耗时也跟着涨十倍，集合几乎不变——这就是 $O(n)$ 和 $O(1)$ 的实际差别，也是 408 里哈希表这一章的工程意义。

**两个必须记住的规则：**

- **字典的键和集合的元素必须可哈希。** `int`、`str`、`tuple`、`frozenset` 可以，`list`、`dict`、`set` 不行。原因很直接：可变对象的内容会变，哈希值也会跟着变，那存放它的位置就失效了。
- **`dict` 和 `set` 不保证业务顺序。** 字典在 Python 3.7+ 保证按插入顺序遍历（这是语言规范，可以依赖），集合没有这个保证，需要有序输出就自己排序。

```python
print(hash("hello") != 0)          # True：字符串可哈希
print(hash((1, 2)) != 0)           # True：元组可哈希
# hash([1, 2])                     # 报错：TypeError，list 不可哈希
```

### 3.2 列表：最常用的可变序列

`list` 是 Python 里使用频率最高的容器：它有序、可重复、可以随意增删改，适用于"一组按顺序排列、后续还要变动"的数据。

#### 3.2.1 创建列表的四种方式

```python
empty = []                       # 1. 空列表
literal = [1, 2, 3]              # 2. 字面量，最常用
from_iterable = list("abc")      # 3. 由任意可迭代对象构造
repeated = [0] * 3               # 4. 重复同一份内容

print(empty)          # []
print(literal)        # [1, 2, 3]
print(from_iterable)  # ['a', 'b', 'c']，字符串被拆成单个字符
print(repeated)       # [0, 0, 0]
```

`list()` 这个构造方式很重要：任何可迭代对象（字符串、元组、字典、集合、生成器、文件对象）都能转成列表，它是"把一批数据一次性取出来"的标准手法。

```python
user = {"name": "Alice", "age": 20}

print(list(user))          # ['name', 'age']，转字典得到的是键
print(list(user.items()))  # [('name', 'Alice'), ('age', 20)]，要键值对就转 items
print(list(range(3)))      # [0, 1, 2]
```

**`[x] * n` 的陷阱：** 当 `x` 是可变对象时，`*` 复制的是**引用**而不是内容，结果 n 个位置指向同一个对象。

```python
# 错误示范：想做 3 行表格，一行改动却全变
bad_table = [[0] * 3] * 2
bad_table[0][0] = 1
print(bad_table)          # [[1, 0, 0], [1, 0, 0]]，两行是同一个列表

# 正确示范：用推导式，每次循环都新建一个列表
good_table = [[0] * 3 for _ in range(2)]
good_table[0][0] = 1
print(good_table)         # [[1, 0, 0], [0, 0, 0]]
```

这是初学者最常见的 bug 之一，判断方法很简单：**`*` 只能用来复制不可变对象**（数字、字符串、元组）；一旦元素是列表或字典，就必须用推导式。

#### 3.2.2 添加元素

列表是动态数组，**从尾部添加最便宜**，从头部或中间插入都要挪动后面的元素。

| 写法 | 作用 | 返回值 | 复杂度 | 要点 |
|:---|:---|:---:|:---:|:---|
| `lst.append(x)` | 末尾追加**一个**元素 | `None` | 均摊 $O(1)$ | 最常用 |
| `lst.extend(it)` | 末尾追加一批元素 | `None` | $O(k)$ | 等价于 `lst += it` |
| `lst.insert(i, x)` | 在下标 `i` 前插入 | `None` | $O(n)$ | 插到头部代价最大 |

```python
cart = ["苹果", "香蕉"]

cart.append("橙子")            # 追加一个元素
print(cart)                    # ['苹果', '香蕉', '橙子']

cart.extend(["葡萄", "梨"])     # 追加一批元素
print(cart)                    # ['苹果', '香蕉', '橙子', '葡萄', '梨']

cart.insert(1, "草莓")          # 插到下标 1 的位置
print(cart)                    # ['苹果', '草莓', '香蕉', '橙子', '葡萄', '梨']
```

**`append()` 和 `extend()` 的区别**：前者把参数当成**一个元素**放进去，后者把参数**拆开逐个**放进去。把列表追加进列表时写错，就会得到嵌套结构。

```python
a = [1, 2]
a.append([3, 4])
print(a)          # [1, 2, [3, 4]]，列表成了第三个元素

b = [1, 2]
b.extend([3, 4])
print(b)          # [1, 2, 3, 4]，元素被摊平

# 如果确实想拼接两个列表并生成新列表，用 + 或解包更直观
print([1, 2] + [3, 4])       # [1, 2, 3, 4]
print([*[1, 2], *[3, 4]])    # [1, 2, 3, 4]
```

**什么时候不能用 `+`：** `+` 每次都创建一个新列表，在循环里累加会反复复制，性能很差；循环里应该用 `append()` 或 `extend()` 就地追加。

```python
# 慢：每轮都新建列表并复制已有元素
result = []
for number in range(1000):
    result = result + [number]

# 快：就地追加，均摊 O(1)
result = []
for number in range(1000):
    result.append(number)

print(len(result))   # 1000
```

#### 3.2.3 删除元素

删除有"按下标"和"按值"两条路，选错了不只是写法不同，**报错类型和性能都不一样**。

| 写法 | 依据 | 元素不存在时 | 返回值 | 复杂度 |
|:---|:---:|:---|:---|:---:|
| `lst.pop()` | 下标（默认最后一个） | `IndexError` | 被删掉的元素 | $O(1)$ |
| `lst.pop(i)` | 下标 | `IndexError` | 被删掉的元素 | $O(n)$ |
| `lst.remove(x)` | 值（删第一个匹配） | `ValueError` | `None` | $O(n)$ |
| `del lst[i]` | 下标 | `IndexError` | 无 | $O(n)$ |
| `lst.clear()` | 清空全部 | 无 | `None` | $O(n)$ |

```python
tasks = ["写作业", "交作业", "复习", "交作业"]

removed = tasks.pop()          # 弹出末尾并拿到它
print(removed, tasks)          # 交作业 ['写作业', '交作业', '复习']

tasks.remove("交作业")          # 按值删，只删第一个匹配
print(tasks)                   # ['写作业', '复习']

del tasks[0]                   # 按下标删，不需要返回值
print(tasks)                   # ['复习']
```

**需要"删掉并拿到那个值"就用 `pop()`，不需要返回值就用 `del`，只知道值不知道位置才用 `remove()`。**

`pop()` 的默认值参数是**只有字典才有**的，列表没有，这是个很容易记混的差异：

| 写法 | 支持默认值吗 | 越界 / 不存在时 |
|:---|:---:|:---|
| `list.pop(i)` | **不支持**，只能传一个参数 | 抛 `IndexError` |
| `dict.pop(key, default)` | 支持 | 有默认值就返回默认值，没有就抛 `KeyError` |
| `set.pop()` | 不支持 | 集合为空时抛 `KeyError` |

```python
# 列表：要安全取末尾元素，先判断再用
tasks = ["任务1"]
if tasks:
    print(tasks.pop())     # 任务1
else:
    print("列表为空")

tasks = []
if tasks:
    print(tasks.pop())
else:
    print("列表为空")       # 列表为空

# 列表越界会直接报错
# [].pop()                   # 报错：IndexError: pop from empty list

# 字典：可以直接给默认值
user = {"name": "Alice"}
print(user.pop("age", "没有这个键"))   # 没有这个键
print(user.pop("name", "没有这个键"))  # Alice
```

**从头部删除为什么慢：** 动态数组要保证元素连续存放，删掉第 0 个之后，剩下的所有元素都得往前挪一格。所以要频繁从头部增删时，应该换成 `collections.deque`（3.2.8 节）。

#### 3.2.4 查找与统计

```python
scores = [88, 92, 79, 92, 95]

print(scores.index(92))       # 1，第一个 92 的位置
print(scores.count(92))       # 2，92 出现了两次
print(92 in scores)           # True，是否存在
print(len(scores))            # 5
print(max(scores), min(scores), sum(scores))   # 95 79 446
print(sum(scores) / len(scores))               # 89.2，平均分
```

`index()` 找不到会抛 `ValueError`，`in` 只返回真假。所以**只想判断"在不在"时用 `in`**，它不会中断程序：

```python
scores = [88, 92, 79]

print(100 in scores)            # False，安全
# scores.index(100)             # 报错：ValueError，找不到就中断

# 想在找不到时给默认值，用 try 或先判断
if 100 in scores:
    position = scores.index(100)
else:
    position = -1
print(position)                 # -1
```

#### 3.2.5 排序与反转

排序是列表最常用的操作之一，两个入口的区别必须分清：

| 写法 | 是否改原列表 | 返回值 | 适用场景 |
|:---|:---:|:---|:---|
| `lst.sort(...)` | **是**，原地排序 | `None` | 不再需要原顺序 |
| `sorted(lst, ...)` | 否 | 新列表 | 原数据还要用，或对象是任意可迭代 |

```python
numbers = [3, 1, 2]

result = numbers.sort()     # 原地排序
print(numbers)              # [1, 2, 3]
print(result)               # None，千万别写 numbers = numbers.sort()

original = [3, 1, 2]
new_list = sorted(original)
print(original)             # [3, 1, 2]，原列表没动
print(new_list)             # [1, 2, 3]
```

**`key` 参数决定按什么排。** 它接收一个函数，对每个元素算出"排序依据"，Python 再比较这些依据：

```python
students = [
    {"name": "Tom", "score": 88},
    {"name": "Alice", "score": 92},
    {"name": "Bob", "score": 88},
]

# 按分数从高到低
by_score = sorted(students, key=lambda s: s["score"], reverse=True)
print([s["name"] for s in by_score])     # ['Alice', 'Tom', 'Bob']

# 元组 key：先比分数，分数相同再比姓名（两个字段都是降序）
by_score_then_name = sorted(students, key=lambda s: (s["score"], s["name"]), reverse=True)
print([s["name"] for s in by_score_then_name])   # ['Alice', 'Tom', 'Bob']

# 字符串也可以当 key：按名字长度排
words = ["banana", "apple", "fig"]
print(sorted(words, key=len))            # ['fig', 'apple', 'banana']
```

**稳定性是 `sort()` 的一个重要性质：** 当两个元素"排序依据相等"时，它们保持原有的相对顺序。利用这一点，"多级排序"可以拆成"按次要关键字先排一遍、再按主要关键字排一遍"：

```python
records = [
    ("Tom", 88),
    ("Alice", 92),
    ("Bob", 88),
]

records.sort(key=lambda r: r[0], reverse=True)   # 第一步：姓名降序
records.sort(key=lambda r: r[1])                 # 第二步：分数升序（稳定，不破坏第一步结果）
print(records)                                   # [('Tom', 88), ('Bob', 88), ('Alice', 92)]
```

两步之后，分数是主要顺序，同分时姓名保持降序——这就是"分数为主、姓名为辅"的排序，代码比写一个复杂 key 更好读。

**反转有两种方式**，注意返回值不同：

```python
numbers = [1, 2, 3]

numbers.reverse()                # 原地反转，返回 None
print(numbers)                   # [3, 2, 1]

# reversed() 返回迭代器，适合只遍历一次的场景
print(list(reversed(numbers)))   # [1, 2, 3]，需要 list 取出来
for value in reversed(numbers):
    print(value)                 # 3 2 1，边遍历边产生，不占额外内存
```

**排序的前提是元素能比较。** 数字之间、字符串之间可以比；数字和字符串混在一起会直接报错，字典默认也不能比，必须靠 `key` 指定比较依据：

```python
print(sorted(["b", "a"]))        # ['a', 'b']
# print(sorted([1, "a"]))        # 报错：TypeError，int 和 str 无法比较
```

#### 3.2.6 切片：读取、替换与复制

切片的语法是 `lst[开始:结束:步长]`，规则是**左闭右开**（包含开始，不包含结束），三个参数都可以省略。

```python
numbers = [0, 1, 2, 3, 4, 5]

print(numbers[1:4])      # [1, 2, 3]，下标 1 到 3
print(numbers[:3])       # [0, 1, 2]，省略开始就是从头
print(numbers[3:])       # [3, 4, 5]，省略结束就是到末尾
print(numbers[1:5:2])    # [1, 3]，步长为 2，隔一个取一个
print(numbers[-3:])      # [3, 4, 5]，负数从末尾数
print(numbers[::-1])     # [5, 4, 3, 2, 1, 0]，步长为 -1 即倒序
```

**切片赋值**：把切片放在赋值号左边，可以一次替换掉一段元素，长度不必相等。

```python
numbers = [0, 1, 2, 3, 4, 5]

numbers[1:3] = [9, 9, 9]      # 用 3 个元素替换掉原来的 2 个
print(numbers)                # [0, 9, 9, 9, 3, 4, 5]

numbers[0:2] = []             # 替换成空列表 = 删除这一段
print(numbers)                # [9, 9, 3, 4, 5]

numbers[:0] = [-1, -2]        # 在下标 0 之前插入
print(numbers)                # [-1, -2, 9, 9, 3, 4, 5]
```

**切片会创建新列表**，所以 `lst[:]` 是最常见的浅拷贝写法：

```python
original = [1, 2, 3]
copied = original[:]
copied.append(4)

print(original)   # [1, 2, 3]，原列表不受影响
print(copied)     # [1, 2, 3, 4]
print(original is copied)   # False，是两个不同的列表
```

但**切片只复制最外层**，元素如果是列表或字典，里层仍然是同一份数据（第 1 章讲过的浅拷贝）：

```python
matrix = [[1, 2], [3, 4]]
shadow = matrix[:]

shadow[0].append(99)      # 改的是内层列表
print(matrix)             # [[1, 2, 99], [3, 4]]，原数据也被改了！
print(shadow)             # [[1, 2, 99], [3, 4]]

# 要完全独立就用深拷贝
import copy
real_copy = copy.deepcopy(matrix)
real_copy[0].append(100)
print(matrix)             # [[1, 2, 99], [3, 4]]，这次没受影响
```

判断用哪种：**一维列表用 `[:]` 就够，嵌套结构必须 `copy.deepcopy()`。**

#### 3.2.7 遍历与推导式

遍历元素用 `for ... in`，需要下标时用 `enumerate()`，不要用 `range(len(...))` 的笨写法：

```python
names = ["Alice", "Bob", "Cindy"]

for name in names:                       # 只要元素
    print(name)

for index, name in enumerate(names):     # 同时要下标，从 0 开始
    print(index, name)

for index, name in enumerate(names, start=1):   # 下标从 1 开始
    print(f"第 {index} 位：{name}")
```

**需要把列表变成另一个列表时，用推导式**，它比"建空列表 + 循环 append"更短也更快：

```python
numbers = [1, 2, 3, 4, 5, 6]

squares = [n ** 2 for n in numbers]              # 映射：每个元素做变换
evens = [n for n in numbers if n % 2 == 0]       # 筛选：只留符合条件的
labels = [f"第{n}个" for n in numbers if n > 3]   # 筛选 + 变换
nested = [[n, n ** 2] for n in numbers[:3]]      # 每个元素变成一个小列表

print(squares)   # [1, 4, 9, 16, 25, 36]
print(evens)     # [2, 4, 6]
print(labels)    # ['第4个', '第5个', '第6个']
print(nested)    # [[1, 1], [2, 4], [3, 9]]
```

**遍历时不要增删元素。** 列表在遍历过程中会重新编号，导致有的元素被跳过、有的被重复访问：

```python
numbers = [1, 2, 2, 3, 2]

# 错误示范：删掉一个 2 之后，后面的元素前移，遍历下标却继续走
wrong = numbers[:]
for value in wrong:
    if value == 2:
        wrong.remove(value)
print(wrong)          # [1, 3, 2]，还留了一个 2 没删干净

# 正确示范一：推导式筛出要保留的，生成新列表
right = [value for value in numbers if value != 2]
print(right)          # [1, 3]

# 正确示范二：确实要原地修改，就倒序遍历，删除不影响前面的下标
in_place = numbers[:]
for index in range(len(in_place) - 1, -1, -1):
    if in_place[index] == 2:
        del in_place[index]
print(in_place)       # [1, 3]
```

顺带说清**列表推导式里的变量作用域**：循环变量只在推导式内部存在，不会污染外面的同名变量。

```python
n = 100
squares = [n ** 2 for n in range(3)]

print(squares)   # [0, 1, 4]
print(n)         # 100，外面的 n 没被改动
```

#### 3.2.8 列表的复杂度，顺便对上 408 考点

Python 的 `list` 就是数据结构里的**顺序表（动态数组）**。逐个操作看它的代价，就能明白为什么某些写法"看起来很简洁但很慢"：

| 操作 | 复杂度 | 原因 |
|:---|:---:|:---|
| 按下标访问 `lst[i]` | $O(1)$ | 元素连续存放，地址 = 首地址 + i × 元素大小 |
| 修改 `lst[i] = x` | $O(1)$ | 同上，直接定位 |
| 尾部追加 `append()` | 均摊 $O(1)$ | 容量够就放，不够才申请更大空间并整体搬迁（按倍数扩容，所以平均下来是常数） |
| 尾部删除 `pop()` | $O(1)$ | 不需要挪动任何元素 |
| 任意位置插入 `insert(i, x)` | $O(n)$ | 下标 i 之后的元素全部后移一位 |
| 任意位置删除 `del lst[i]` | $O(n)$ | 后面的元素全部前移一位 |
| 按值查找 `in`、`index()`、`remove()` | $O(n)$ | 只能从头逐个比较 |
| 切片 `lst[a:b]` | $O(b-a)$ | 要逐个复制元素 |
| 排序 `sort()` | $O(n \log n)$ | Timsort，比较排序的下界 |

**这个表直接解释了两个常见的性能写法：**

```python
# 慢：每次都往头部插，每次都是 O(n)，n 次就是 O(n²)
slow = []
for number in range(1000):
    slow.insert(0, number)

# 快：先往尾部追加（O(1)），最后反转一次（O(n)），总共 O(n)
fast = []
for number in range(1000):
    fast.append(number)
fast.reverse()

print(slow[:3], fast[:3])     # [999, 998, 997] [999, 998, 997]，结果一样，代价差很多
```

**需要频繁从两端增删时，标准库给了更合适的结构。** `collections.deque` 是双向链表式的实现，头部和尾部的增删都是 $O(1)$，还带 `maxlen` 参数可以固定长度、自动丢弃旧数据：

```python
from collections import deque

queue = deque([1, 2, 3])

queue.append(4)          # 右侧入队
queue.appendleft(0)      # 左侧入队，O(1)
print(queue)             # deque([0, 1, 2, 3, 4])

print(queue.popleft())   # 0，左侧出队，O(1)
print(queue.pop())       # 4，右侧出队，O(1)
print(queue)             # deque([1, 2, 3])

# 固定长度的滑动窗口：满了之后自动丢弃最旧的
recent = deque(maxlen=3)
for number in range(5):
    recent.append(number)
print(recent)            # deque([2, 3, 4], maxlen=3)，只留最近三个
```

选择依据一句话：**只在一端增删用 `list`，两端都要增删用 `deque`，需要在中间插删就用 `dict` 或重新设计数据结构。**

### 3.3 元组：一旦创建就不能改

`tuple` 和 `list` 几乎一样，只差一件事：**不可变**。这个限制带来三个好处——可以当字典的键、可以被安全共享、能表达"这是一条固定的记录"。

#### 3.3.1 创建元组：括号不是关键，逗号才是

```python
point = (10, 20)          # 最常规的写法
also_point = 10, 20       # 括号可以省略，逗号才是元组的标志
single = (1,)             # 单元素元组，逗号不能省
not_tuple = (1)           # 这不是元组，就是整数 1
empty = ()                # 空元组

print(type(point))        # <class 'tuple'>
print(type(also_point))   # <class 'tuple'>
print(type(single))       # <class 'tuple'>
print(type(not_tuple))    # <class 'int'>
print(len(empty))         # 0
```

**`(1)` 和 `(1,)` 的区别是考试和实战都会考的点**：前者括号只起分组作用，后者才是元组。需要一元元组时忘了逗号，会在后续解包或当字典键时报出很难懂的错误。

#### 3.3.2 元组能做什么：读取、统计、解包

元组没有 `append`、`insert`、`remove`、`sort` 这些修改方法，能用的只有读取类操作：

```python
record = ("Alice", 20, "深圳")

print(record[0])            # Alice，按下标访问
print(record[-1])           # 深圳
print(record[1:])           # (20, '深圳')，切片得到的还是元组
print(len(record))          # 3
print(record.count(20))     # 1
print(record.index("深圳"))  # 2
print("深圳" in record)      # True
print(max((3, 1, 2)))       # 3，元组也能比大小
print(tuple([1, 2, 3]))     # (1, 2, 3)，由列表转成元组
```

**"不可变"指的是元组这层结构不能变，里面的可变对象照样能改。** 这一点经常被误解：

```python
mixed = (1, [2, 3], "text")

mixed[1].append(4)        # 合法：改的是内层列表
print(mixed)              # (1, [2, 3, 4], 'text')

# mixed[0] = 99           # 报错：TypeError，不能替换元组的元素
# mixed[1] = [9]          # 报错：TypeError，不能换掉内层对象的引用
```

所以严格说，元组的"不可变"是**浅层不可变**：它保证自己的三个格子永远指向同样的对象，但不保证那些对象内部不变。要真正意义上的常量结构，元素也必须是不可变的。

#### 3.3.3 元组最有价值的两个用途

**用途一：当字典的键 / 集合的元素。** 因为可哈希，元组能表示"复合的键"，这在处理网格、坐标、图结构时非常常见：

```python
# 坐标到名称的映射：列表做不到这件事
locations = {(0, 0): "起点", (3, 4): "终点", (1, 2): "休息点"}
print(locations[(3, 4)])          # 终点
print((0, 0) in locations)        # True

# 图上找相邻格子：把坐标当集合元素，自动去重
visited = {(0, 0), (1, 0), (0, 0)}
print(visited)                    # {(1, 0), (0, 0)}，顺序不保证，去重后只剩 2 个
print(len(visited))               # 2

# locations[[3, 4]] = "x"         # 报错：TypeError，list 不可哈希
```

**用途二：解包。** 函数返回多个值、从固定结构里取值，都靠它：

```python
point = (10, 20)
x, y = point                      # 按位置拆给两个名字
print(x, y)                       # 10 20

# 元组解包最常见的场景：函数返回多个结果
def divide(a, b):
    return a // b, a % b          # 返回的其实是一个元组

quotient, remainder = divide(17, 5)
print(quotient, remainder)        # 3 2

# 交换变量也是解包的功劳
a, b = 1, 2
a, b = b, a
print(a, b)                       # 2 1
```

#### 3.3.4 什么时候该用元组

| 场景 | 用元组的原因 |
|:---|:---|
| 函数返回多个值 | 语法天然支持，一行就能拆开 |
| 表示固定字段的记录，如 `(姓名, 年龄, 城市)` | 结构固定，不会被误改 |
| 当字典的键、集合的元素 | 可哈希 |
| 作为不可变的配置或常量表 | 共享时不用担心被别处改掉 |
| 只需要遍历、不需要修改的数据 | 语义更清楚，开销略小 |

反过来，只要**长度会变或元素要替换**，就该用 `list`。选择困难时的默认答案：**不确定就用 `list`，确定不会改再用 `tuple`。**

### 3.4 字典：键到值的映射

`dict` 解决的是另一类问题：**不是"第几个"，而是"叫什么"**。它把键映射到值，靠哈希表实现快速查找，是 Python 里最常用的数据结构之一。

#### 3.4.1 创建字典的常见方式

```python
from collections import defaultdict

empty = {}                                    # 空字典（注意不是集合）
literal = {"name": "Alice", "age": 20}        # 字面量，最常用
pairs = dict([("name", "Alice"), ("age", 20)])  # 由键值对序列构造
keywords = dict(name="Alice", age=20)         # 关键字形式，键必须是合法标识符
keys_only = dict.fromkeys(["a", "b"], 0)      # 所有键共用同一个初始值
counted = defaultdict(int)                    # 访问不存在的键时自动生成默认值

counted["apple"] += 1                         # 不用先判断键是否存在
print(empty)       # {}
print(literal)     # {'name': 'Alice', 'age': 20}
print(pairs)       # {'name': 'Alice', 'age': 20}
print(keywords)    # {'name': 'Alice', 'age': 20}
print(keys_only)   # {'a': 0, 'b': 0}
print(counted)     # defaultdict(<class 'int'>, {'apple': 1})
```

**`dict.fromkeys()` 的陷阱：** 它给所有键填的是**同一个对象**的引用，如果那个对象可变，就会出现"改一个全变"：

```python
# 错误示范
bad = dict.fromkeys(["a", "b"], [])
bad["a"].append(1)
print(bad)          # {'a': [1], 'b': [1]}，两个键共用同一个列表

# 正确示范：用推导式，每个键都新建一个列表
good = {key: [] for key in ["a", "b"]}
good["a"].append(1)
print(good)         # {'a': [1], 'b': []}
```

这和 3.2.1 节 `[[]] * 3` 是同一类错误：**只要值是可变对象，就不能用"复制一份"的方式批量创建。**

#### 3.4.2 读取值：`[]` 和 `get()` 的取舍

| 写法 | 键不存在时 | 适用场景 |
|:---|:---|:---|
| `d[key]` | 抛 `KeyError`，程序中断 | 键一定存在，或"缺失就是严重错误" |
| `d.get(key)` | 返回 `None` | 只是想知道有没有 |
| `d.get(key, default)` | 返回 `default` | 需要兜底值 |

```python
user = {"name": "Alice", "age": 20}

print(user["name"])                    # Alice
print(user.get("city"))                # None
print(user.get("city", "未知"))         # 未知
print("city" in user)                  # False，判断键是否存在
print(len(user))                       # 2
```

**怎么选？** 如果这个键缺失说明数据出了问题、应该暴露出来，就用 `d[key]` 让它报错；如果缺失是正常情况、需要默认行为，就用 `get()`。

```python
# 用 get 做累加统计：第一次见到某个键时给它 0
votes = ["Python", "Java", "Python", "Go", "Python"]
counter = {}

for language in votes:
    counter[language] = counter.get(language, 0) + 1

print(counter)      # {'Python': 3, 'Java': 1, 'Go': 1}
```

**`d.get(key, [])` 的一个隐患：** 每次调用都会新建一个空列表，所以它**不能**用来"往不存在的键里追加"：

```python
groups = {}
# groups.get("A", []).append("Alice")   # 无效！空列表是临时的，追加完就丢了
# print(groups)                         # {}，什么也没存进去

# 正确：用 setdefault，键不存在时先建好列表并写入字典
groups.setdefault("A", []).append("Alice")
groups.setdefault("A", []).append("Amy")
groups.setdefault("B", []).append("Bob")
print(groups)      # {'A': ['Alice', 'Amy'], 'B': ['Bob']}
```

#### 3.4.3 增删改

```python
user = {"name": "Alice"}

user["age"] = 20                            # 键不存在 → 新增
print(user)                                 # {'name': 'Alice', 'age': 20}

user["age"] = 21                            # 键已存在 → 覆盖旧值
print(user)                                 # {'name': 'Alice', 'age': 21}

user.update({"city": "深圳", "age": 22})     # 批量增改，同名键被覆盖
print(user)                                 # {'name': 'Alice', 'age': 22, 'city': '深圳'}

removed = user.pop("city")                  # 弹出该键并返回值
print(removed, user)                        # 深圳 {'name': 'Alice', 'age': 22}

missing = user.pop("none", "默认值")         # 给默认值，键不存在也不报错
print(missing)                              # 默认值

del user["age"]                             # 只删除，不需要返回值
print(user)                                 # {'name': 'Alice'}
```

**两个容易混淆的方法，记住它们的返回值：**

```python
user = {"name": "Alice"}

# setdefault：键不存在就写入并返回，存在则什么都不做
print(user.setdefault("age", 20))     # 20，键不存在，写进去了
print(user.setdefault("age", 99))     # 20，键已存在，不覆盖
print(user)                           # {'name': 'Alice', 'age': 20}

# get 不会写入
print(user.get("city", "未知"))        # 未知
print(user)                           # {'name': 'Alice', 'age': 20}，字典没变
```

要批量合并两个字典，`update()` 是原地修改；如果需要**生成新字典而不动原来两个**，有三种等价写法：

```python
defaults = {"theme": "light", "page_size": 20}
custom = {"page_size": 50}

merged_operator = defaults | custom          # Python 3.9+，最简洁
merged_unpack = {**defaults, **custom}       # 3.5+ 都能用
copy_then_update = defaults.copy()
copy_then_update.update(custom)              # 传统写法

print(merged_operator)   # {'theme': 'light', 'page_size': 50}
print(merged_unpack)     # {'theme': 'light', 'page_size': 50}
print(copy_then_update)  # {'theme': 'light', 'page_size': 50}
print(defaults)          # {'theme': 'light', 'page_size': 20}，原字典都没动
```

三种写法都是**右边的键覆盖左边**，并且都不修改原字典。如果需要**原地更新**，用 `defaults |= custom`。

#### 3.4.4 遍历字典

```python
user = {"name": "Alice", "age": 20, "city": "深圳"}

for key in user:                        # 默认遍历键
    print(key)

for key, value in user.items():         # 键值对，最常用
    print(key, "=", value)

for value in user.values():             # 只要值
    print(value)

print(list(user.keys()))                # ['name', 'age', 'city']，Python 3.7+ 按插入顺序
print(list(user.items()))               # [('name', 'Alice'), ('age', 20), ('city', '深圳')]
```

**遍历时不能增删键**，因为哈希表的内部结构会变，Python 会直接抛错拦住你：

```python
scores = {"Alice": 90, "Bob": 55, "Amy": 72}

# 错误示范
# for name in scores:
#     if scores[name] < 60:
#         del scores[name]        # 报错：RuntimeError: dictionary changed size during iteration

# 正确示范：先收集要删的键，再单独删
failed = [name for name, score in scores.items() if score < 60]
for name in failed:
    del scores[name]

print(scores)     # {'Alice': 90, 'Amy': 72}
```

**边遍历边修改值是可以的**，因为改值不改变字典的大小和结构：

```python
scores = {"Alice": 90, "Bob": 55}

for name in scores:
    scores[name] += 5      # 合法：只是替换值

print(scores)     # {'Alice': 95, 'Bob': 60}
```

#### 3.4.5 用字典做"查找表"

字典最典型的用法是替代一长串 `if/elif`，把"判断链"变成"一次查找"：

```python
# 笨写法：每次都要从上往下比
def level_text(score):
    if score >= 90:
        return "优秀"
    elif score >= 80:
        return "良好"
    elif score >= 60:
        return "及格"
    else:
        return "不及格"

# 查表写法：把映射关系写成数据，逻辑变成一次查找
LEVELS = {90: "优秀", 80: "良好", 60: "及格", 0: "不及格"}

def level_text_v2(score):
    for threshold in sorted(LEVELS, reverse=True):
        if score >= threshold:
            return LEVELS[threshold]
    return "不及格"

print(level_text(85), level_text_v2(85))     # 良好 良好

# 更常见的场景：键完全精确匹配时，查表直接就是 O(1)
STATUS_TEXT = {"created": "已创建", "paid": "已支付", "shipped": "已发货"}
print(STATUS_TEXT.get("paid", "未知状态"))    # 已支付
print(STATUS_TEXT.get("closed", "未知状态"))   # 未知状态
```

**用 `dict` 还能简化"多分支分发"**，把函数当值存进字典（函数也是对象，可以当值）：

```python
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

operations = {"+": add, "-": subtract}

print(operations["+"](3, 5))       # 8
print(operations["-"](3, 5))       # -2
print(operations.get("*", add)(3, 5))   # 8，没有这个键就用兜底函数
```

#### 3.4.6 排序、取值与统计

字典本身没有 `sort()`，要排序就把它转成"键值对列表"再排：

```python
scores = {"Alice": 90, "Bob": 55, "Amy": 72}

# 按分数从高到低
by_score = dict(sorted(scores.items(), key=lambda item: item[1], reverse=True))
print(by_score)                        # {'Alice': 90, 'Amy': 72, 'Bob': 55}

# 按姓名排序
by_name = dict(sorted(scores.items()))
print(by_name)                         # {'Alice': 90, 'Amy': 72, 'Bob': 55}

# 只要排序结果用于遍历，不必转回字典
for name, score in sorted(scores.items(), key=lambda item: item[1], reverse=True):
    print(name, score)
```

**常用的几个统计操作：**

```python
scores = {"Alice": 90, "Bob": 55, "Amy": 72}

print(max(scores, key=scores.get))     # Alice，取分数最高的姓名
print(min(scores, key=scores.get))     # Bob，取分数最低的姓名
print(sum(scores.values()))            # 217
print(round(sum(scores.values()) / len(scores), 2))   # 72.33，平均分

# 过滤：只保留及格的
passed = {name: score for name, score in scores.items() if score >= 60}
print(passed)                          # {'Alice': 90, 'Amy': 72}

# 取子集：只取需要的键
subset = {name: scores[name] for name in ["Alice", "Bob"]}
print(subset)                          # {'Alice': 90, 'Bob': 55}
```

#### 3.4.7 用 `Counter` 统计频次

"统计每个元素出现几次"是极高频的需求，标准库直接提供了工具，不必手写循环：

```python
from collections import Counter

words = ["apple", "banana", "apple", "cherry", "apple", "banana"]
counts = Counter(words)

print(counts)                        # Counter({'apple': 3, 'banana': 2, 'cherry': 1})
print(counts["apple"])               # 3
print(counts["missing"])             # 0，不存在的键返回 0，不会报错
print(counts.most_common(2))         # [('apple', 3), ('banana', 2)]，取前两名
print(sum(counts.values()))          # 6，总数
```

`Counter` 是 `dict` 的子类，所以字典的用法它都支持，还多了几个好用的操作：

```python
from collections import Counter

text = "hello world"
letter_count = Counter(text)

print(letter_count)                    # Counter({'l': 3, 'o': 2, 'h': 1, ...})，按次数从多到少
print(letter_count.most_common(3))     # [('l', 3), ('o', 2), ('h', 1)]，出现最多的三个字符

# 统计多个文本合并后的词频
all_words = Counter(["a", "b"]) + Counter(["b", "c"])
print(all_words)                       # Counter({'b': 2, 'a': 1, 'c': 1})

# 减法只保留正数结果
print(Counter(["a", "a", "b"]) - Counter(["a"]))   # Counter({'a': 1, 'b': 1})
```

#### 3.4.8 用 `defaultdict` 省掉"先判断再初始化"

前面用 `setdefault()` 解决"往不存在的键里追加"，`defaultdict` 提供了更干净的写法：访问一个不存在的键时，它会自动调用你指定的工厂函数生成默认值。

```python
from collections import defaultdict

# 按班级把学生分组：不用判断键是否存在
by_class = defaultdict(list)
for name, class_name in [("Alice", "A"), ("Bob", "B"), ("Amy", "A")]:
    by_class[class_name].append(name)

print(dict(by_class))     # {'A': ['Alice', 'Amy'], 'B': ['Bob']}

# 计数：不用 get(key, 0) + 1
word_count = defaultdict(int)
for word in ["a", "b", "a"]:
    word_count[word] += 1

print(dict(word_count))   # {'a': 2, 'b': 1}
```

**注意 `defaultdict` 的一个副作用：** 读取一个不存在的键也会把它创建出来，所以它不适合"只查询不修改"的场景：

```python
from collections import defaultdict

d = defaultdict(int)
print(d["missing"])      # 0，看起来只是读了一下
print(dict(d))           # {'missing': 0}，但键已经被真的创建了
print("missing" in d)    # True
```

如果只是想安全读取、不想改变字典，还是要用 `d.get(key, default)`。

### 3.5 集合：去重与集合运算

`set` 是"只有键、没有值"的哈希表，所以它的行为很像字典的键集合：**自动去重、查找 $O(1)$、不保证顺序**。

#### 3.5.1 创建集合

```python
unique = {1, 2, 3}                  # 字面量
empty_set = set()                   # 空集合必须写 set()
empty_dict = {}                     # 这是空字典，不是集合！

from_list = set([1, 2, 2, 3])       # 自动去重
from_string = set("hello")          # 字符串被拆成字符
from_range = set(range(3))
comprehension = {n % 3 for n in range(6)}

print(unique)               # {1, 2, 3}
print(type(empty_set))      # <class 'set'>
print(type(empty_dict))     # <class 'dict'>
print(from_list)            # {1, 2, 3}
print(sorted(from_string))  # ['e', 'h', 'l', 'o']，集合本身不保证顺序
print(from_range)           # {0, 1, 2}
print(comprehension)        # {0, 1, 2}
```

**`{}` 是空字典，这一点必须记牢。** 想要空集合只能写 `set()`，写错类型会在后续操作里报出莫名其妙的错误。

**集合元素必须可哈希**，所以列表不能放进去，元组可以：

```python
print({(1, 2), (3, 4)})       # {(1, 2), (3, 4)}，元组可以当元素
# print({[1, 2]})             # 报错：TypeError，list 不可哈希
```

#### 3.5.2 增删与判断

| 写法 | 元素不存在时 | 返回值 | 备注 |
|:---|:---|:---|:---|
| `s.add(x)` | 直接加入 | `None` | 元素已存在则集合不变 |
| `s.remove(x)` | **抛 `KeyError`** | `None` | 只在确定存在时用 |
| `s.discard(x)` | 什么也不做 | `None` | 最安全的删除方式 |
| `s.pop()` | 抛 `KeyError` | 被删掉的元素 | 删哪个不确定 |
| `s.clear()` | 无 | `None` | 清空 |

```python
tags = {"java", "sql"}

tags.add("python")
tags.add("java")           # 已存在，集合不变
tags.discard("go")         # 不存在也不报错
print(sorted(tags))        # ['java', 'python', 'sql']

tags.remove("sql")         # 存在，正常删除
print(sorted(tags))        # ['java', 'python']

# tags.remove("go")        # 报错：KeyError，不存在就中断
print("python" in tags)    # True，哈希查找接近 O(1)
print(len(tags))           # 2
```

**为什么推荐 `discard()` 而不是 `remove()`？** 因为"删掉一个可能不存在的东西"是常态，用 `remove()` 就得先 `if x in s` 判断，或者写 try/except；`discard()` 一步到位。

#### 3.5.3 集合运算

集合的数学运算在 Python 里有运算符和方法两套写法，**运算符产生新集合，`_update` 系列方法原地修改**：

| 运算 | 运算符 | 方法 | 含义 |
|:---|:---:|:---|:---|
| 并集 | `a \| b` | `a.union(b)` | 两个集合的所有元素 |
| 交集 | `a & b` | `a.intersection(b)` | 两个集合都有的元素 |
| 差集 | `a - b` | `a.difference(b)` | 只在 a 里的元素 |
| 对称差集 | `a ^ b` | `a.symmetric_difference(b)` | 只在一个集合里的元素 |

```python
python_devs = {"Alice", "Bob", "Cindy"}
java_devs = {"Bob", "Cindy", "David"}

print(python_devs | java_devs)   # 会至少一门：{'David', 'Alice', 'Bob', 'Cindy'}
print(python_devs & java_devs)   # 两门都会：{'Bob', 'Cindy'}
print(python_devs - java_devs)   # 只会 Python：{'Alice'}
print(java_devs - python_devs)   # 只会 Java：{'David'}
print(python_devs ^ java_devs)   # 只会一门：{'David', 'Alice'}
```

**原地修改的版本**，适合处理大数据时避免复制：

```python
a = {1, 2, 3}
b = {3, 4, 5}

a.intersection_update(b)
print(a)      # {3}，a 自己变成了交集

c = {1, 2}
c.update({3})            # 并集原地更新，等价于 c |= {3}
c.difference_update({1})  # 去掉交集部分，等价于 c -= {1}
print(c)                 # {2, 3}
```

**判断包含关系**用到子集和超集：

```python
basic = {"java", "sql"}
all_skills = {"java", "sql", "python"}

print(basic.issubset(all_skills))       # True，basic 是子集
print(all_skills.issuperset(basic))     # True，all_skills 是超集
print(basic <= all_skills)              # True，运算符写法
print(basic < all_skills)               # True，真子集（不相等）
print(basic.isdisjoint({"go", "rust"}))  # True，没有交集
```

#### 3.5.4 用集合去重

```python
numbers = [1, 2, 2, 3, 3, 3]

print(set(numbers))                    # {1, 2, 3}，最简单，但顺序不保证
print(list(dict.fromkeys(numbers)))    # [1, 2, 3]，去重且保持原来的先后顺序
```

**需要"去重且保序"时必须用 `dict.fromkeys()`**：`set()` 不保证顺序，用它去重后重新遍历，顺序可能和你预期的不一样。而字典在 Python 3.7+ 保证插入顺序，用键去重天然保序。

```python
words = ["banana", "apple", "banana", "cherry"]

print(list(dict.fromkeys(words)))       # ['banana', 'apple', 'cherry']，稳定保序
```

#### 3.5.5 集合的典型使用场景

**场景一：判断"有没有重复"或"是否都出现过"：**

```python
required = {"读", "写", "执行"}
done = {"读", "写"}

print(required - done)          # {'执行'}，还差哪些没做
print(required <= done)         # False，没全部完成
print(done <= required)         # True，做的都在要求里
```

**场景二：两个列表找公共元素 / 找差异**，比双层循环快得多：

```python
class_a = ["Alice", "Bob", "Cindy", "Bob"]
class_b = ["Bob", "Cindy", "David"]

common = set(class_a) & set(class_b)
only_a = set(class_a) - set(class_b)

print(common)     # {'Bob', 'Cindy'}
print(only_a)     # {'Alice'}
```

**场景三：给数据打标记、记录"访问过的节点"**（图和搜索算法里极常见）：

```python
edges = {
    "A": ["B", "C"],
    "B": ["A", "D"],
    "C": ["A"],
    "D": ["B"],
}

visited = set()
stack = ["A"]

while stack:
    node = stack.pop()
    if node in visited:      # 用集合判断"来过没有"，O(1)
        continue
    visited.add(node)
    stack.extend(edges[node])

print(visited)               # {'A', 'B', 'D', 'C'}，集合不保证顺序，用 len 判断数量更稳妥
print(len(visited))          # 4，四个节点都被访问过
```

### 3.6 解包：一次性拆开容器

解包（unpacking）用一条赋值语句把可迭代对象里的元素分配给多个名字。它的规则很少，但用起来非常频繁：函数返回值、交换变量、合并容器、传参都靠它。

#### 3.6.1 基本规则

**左边的名字个数必须和右边的元素个数一致**，也可以用星号收集"剩下的"：

```python
point = (10, 20)
x, y = point
print(x, y)                  # 10 20

first, *middle, last = [1, 2, 3, 4, 5]
print(first, middle, last)   # 1 [2, 3, 4] 5

head, *tail = (1, 2, 3)
print(head, tail)            # 1 [2, 3]
```

**带星号的名字收集到的永远是列表**（不是元组），即使右边是元组也是如此。星号只能出现一次，否则 Python 不知道该怎么分配。

```python
a, b = [1, 2]          # 正好两个，成功
print(a, b)            # 1 2

# a, b, c = [1, 2]     # 报错：ValueError，值不够
# a, b = [1, 2, 3]     # 报错：ValueError，值太多
# a, *b, *c = [1, 2, 3]  # 报错：SyntaxError，星号只能一个
```

这个"个数必须匹配"的特性可以当**结构校验**用：如果数据的形状和你预期的不一样，解包会立刻报错，而不是让你带着错误数据继续跑。

#### 3.6.2 用解包处理函数参数

| 位置 | 写法 | 含义 |
|:---|:---|:---|
| 函数**定义**里 | `def f(*args, **kwargs)` | 收集：把多余位置参数打包成元组、关键字参数打包成字典 |
| 函数**调用**时 | `f(*values, **options)` | 展开：把序列拆成位置参数、把字典拆成关键字参数 |

```python
def introduce(name, age, city):
    return f"{name}，{age} 岁，来自 {city}"

# 定义侧：收集
def show_all(title, *args, **kwargs):
    return title, args, kwargs

print(show_all("标题", 1, 2, key="value"))
# ('标题', (1, 2), {'key': 'value'})

# 调用侧：展开
values = ["Alice", 20, "深圳"]
options = {"name": "Bob", "age": 22, "city": "广州"}

print(introduce(*values))       # Alice，20 岁，来自 深圳
print(introduce(**options))     # Bob，22 岁，来自 广州
```

**注意两者含义相反、位置不同：** 定义里的 `*` 是"把散的收起来"，调用时的 `*` 是"把整的摊开"。混用会引起 `TypeError: got multiple values for argument`。

```python
numbers = [1, 2, 3]

print(max(*numbers))     # 3，展开成 max(1, 2, 3)
print(max(numbers))      # 3，直接传列表，效果相同
print(list(range(*[1, 5])))   # [1, 2, 3, 4]，range(1, 5)
```

#### 3.6.3 用解包合并容器

```python
left = [1, 2]
right = [3, 4]

print([*left, *right])       # [1, 2, 3, 4]，合并列表
print((*left, *right))       # (1, 2, 3, 4)，合并元组
print({*left, *right})       # {1, 2, 3, 4}，合并集合并自动去重

base = {"name": "Alice", "age": 20}
print({**base, "age": 21})   # {'name': 'Alice', 'age': 21}，后面的键覆盖前面
print({**base, "city": "深圳"})  # {'name': 'Alice', 'age': 20, 'city': '深圳'}
```

**`{**a, **b}` 会创建新字典**，`a` 和 `b` 都不受影响：

```python
a = {"x": 1}
b = {"y": 2}
merged = {**a, **b}

merged["z"] = 3
print(a, b, merged)      # {'x': 1} {'y': 2} {'x': 1, 'y': 2, 'z': 3}
```

#### 3.6.4 嵌套解包与忽略部分值

右边的结构可以嵌套，左边用同样的形状去接：

```python
data = ("Alice", (20, "深圳"))

name, (age, city) = data       # 嵌套解包
print(name, age, city)         # Alice 20 深圳

# 只想要其中一个值时，用下划线作为"不关心"的占位
_, _, city_only = ("Alice", 20, "深圳")
print(city_only)               # 深圳

first, *_ = [1, 2, 3, 4]
print(first)                   # 1

*_, last = [1, 2, 3, 4]
print(last)                    # 4
```

**下划线只是普通变量名**，它不会阻止赋值，只是一个"我知道这里有值但不用"的约定信号。

### 3.7 本章小结

**选容器的判断顺序：** 要去重就 `set`；按名字查就 `dict`；按位置访问且要改就 `list`；不需要改就 `tuple`。

**最常用的写法速查：**

| 想做什么 | 推荐写法 |
|:---|:---|
| 末尾加一个元素 | `lst.append(x)` |
| 末尾加一批元素 | `lst.extend(iterable)` |
| 按下标删除并拿到它 | `value = lst.pop(i)` |
| 按值删除 | `lst.remove(x)` |
| 原地排序 / 生成新列表 | `lst.sort(key=...)` / `sorted(lst, key=...)` |
| 多级排序 | 按次要关键字排一遍，再按主要关键字排一遍（利用稳定性） |
| 复制一维列表 | `lst[:]` 或 `list(lst)` |
| 复制嵌套结构 | `copy.deepcopy(obj)` |
| 带下标遍历 | `for i, v in enumerate(lst):` |
| 筛选出新列表 | `[x for x in lst if 条件]` |
| 安全读取字典 | `d.get(key, default)` |
| 分组 / 计数 | `defaultdict(list)` / `defaultdict(int)` |
| 统计频次 | `Counter(items)` |
| 去重且保序 | `list(dict.fromkeys(items))` |
| 判断存在性 | 先把数据放进 `set` 或 `dict`，不要反复扫 `list` |
| 两端增删频繁 | `collections.deque` |
| 拆开固定结构 | `a, b = pair` / `first, *rest = items` |
| 合并容器 | `[*a, *b]` / `{**a, **b}` |

**五个最容易踩的坑（都在本章出现过）：**

1. `[[]] * 3` 和 `dict.fromkeys(keys, [])` 会让多个位置共用同一个可变对象，要用推导式。
2. `append()` 把参数当一个元素、`extend()` 把它拆开，写错就得到嵌套结构。
3. 遍历时增删元素：列表会漏删、字典直接抛 `RuntimeError`。
4. 切片和 `copy.copy()` 都是浅拷贝，嵌套结构要 `copy.deepcopy()`。
5. `{}` 是空字典，空集合必须写 `set()`；`(1)` 是整数，单元素元组必须写 `(1,)`。

**下一步的衔接：** 这一章的容器都有具体长度、一次性放进内存。如果数据量很大、或者需要"边算边取"，就要用到第 6 章的迭代器和生成器；如果要把容器持久化到磁盘，就是第 9 章的文件与 JSON。

---


---

## 4. 条件与循环

这一章看起来简单，但有几个 Python 特有的机制容易用错：**`for-else`、短路求值、`range` 是惰性的、以及循环变量泄漏。**

### 4.1 条件判断

```python
score = 85

if score >= 90:
    level = "优秀"
elif score >= 60:
    level = "及格"
else:
    level = "不及格"

print(level)     # 及格
```

**条件判断按顺序执行，第一个成立的分支执行后就跳过其余分支。** 所以顺序很重要：

```python
# 错误顺序：>= 60 先成立，后面的 >= 90 永远不会执行
score = 95
if score >= 60:
    print("及格")      # 会打印这个
elif score >= 90:
    print("优秀")      # 永远到不了

# 正确顺序：从严格到宽松
if score >= 90:
    print("优秀")      # 优秀
elif score >= 60:
    print("及格")
```

#### 真值判断的惯用写法

第 1 章讲过真假值规则，这里给出实际写法：

```python
items = []
name = None

# 判断"空"：不要写 == [] 或 == ""
if not items:
    print("列表为空")

if items:
    print("列表非空")

# 判断 None：必须用 is
if name is None:
    print("名字未设置")

# 判断不是 None 且非空
if name and name.strip():
    print("有有效名字")
else:
    print("名字无效或未设置")
```

#### 条件表达式（三元运算）

简单二选一用一行写更清楚：

```python
age = 20
status = "成年" if age >= 18 else "未成年"
print(status)      # 成年

# 嵌套会迅速变难读，两层以上就该用 if 语句
score = 85
level = "优秀" if score >= 90 else ("及格" if score >= 60 else "不及格")
print(level)       # 及格，但这种写法不推荐
```

#### 链式比较

Python 支持数学式的连续比较，比 `and` 更直观：

```python
age = 25

print(18 <= age < 60)          # True，等价于 18 <= age and age < 60
print(0 < age <= 100)          # True
print(1 < 2 < 3 < 4)           # True

# 注意：这种写法只计算中间值一次，用 and 会算两次
def get_value():
    print("被调用")
    return 5

print(1 < get_value() < 10)    # 只打印一次"被调用"
```

#### 短路求值

`and` 和 `or` **在结果已经确定时不会计算右边**，这既是优化也是常用技巧：

```python
# 利用短路避免报错
text = None

if text is not None and text.startswith("a"):
    print("以 a 开头")

# 等价的安全写法：or 提供默认值
name = ""
display = name or "匿名用户"
print(display)      # 匿名用户，因为空字符串是假值

# 注意：这个技巧对"0 或空"也会生效，可能不是你想要的
count = 0
print(count or 10)      # 10，因为 0 是假值（如果想保留 0，要用 if count is None）
print(count if count is not None else 10)   # 0，这样才对
```

**`and` 和 `or` 返回的不是 `True/False`，而是参与运算的对象**，这是理解上面代码的关键：

```python
print("" or "默认值")       # 默认值
print("有值" or "默认值")    # 有值
print(0 and "不会到这里")    # 0
print("a" and "b")          # b，两个都真就返回最后一个
```

### 4.2 `for` 循环

`for` 遍历的是"可迭代对象"（第 6 章讲过它的机制），不需要下标：

```python
# 直接遍历元素
for char in "Python":
    print(char)          # P y t h o n

for item in [1, 2, 3]:
    print(item)

# 遍历字典
user = {"name": "Alice", "age": 20}
for key in user:
    print(key)                  # name age
for key, value in user.items():
    print(key, value)           # name Alice / age 20
```

#### `range()` 是惰性的

`range` 不生成列表，只在需要时算下一个数，所以**再大的范围也不占内存**：

```python
import sys

r = range(1_000_000)
print(sys.getsizeof(r))         # 48 字节，几乎不占内存
print(sys.getsizeof(list(r)))   # 约 8 MB，转成列表就占了

# 三种用法
print(list(range(5)))           # [0, 1, 2, 3, 4]，从 0 开始
print(list(range(2, 5)))        # [2, 3, 4]，指定起点
print(list(range(1, 10, 3)))    # [1, 4, 7]，指定步长
print(list(range(5, 0, -1)))    # [5, 4, 3, 2, 1]，倒着数

# 判断成员也是 O(1)，不遍历
print(999999 in range(1_000_000))    # True
```

**注意 `range` 的终点不包含在内**（左闭右开），这是最常见的差一错误来源：

```python
# 想打印 1 到 5，必须写 1..6
for i in range(1, 6):
    print(i)        # 1 2 3 4 5

# 想遍历列表下标，范围必须是 len(items)
items = ["a", "b", "c"]
for i in range(len(items)):
    print(i, items[i])      # 0 a / 1 b / 2 c

# 但更推荐直接遍历或用 enumerate（第 6 章）
for i, item in enumerate(items):
    print(i, item)
```

#### 遍历多个序列用 `zip`

```python
names = ["Alice", "Bob"]
scores = [90, 85]

for name, score in zip(names, scores):
    print(f"{name}: {score}")

# 同时要下标
for index, (name, score) in enumerate(zip(names, scores), start=1):
    print(f"{index}. {name} = {score}")
# 1. Alice = 90
# 2. Bob = 85
```

### 4.3 `while` 循环

`while` 适合"不知道要循环多少次"的场景，条件是继续的依据：

```python
count = 3

while count > 0:
    print(count)        # 3 2 1
    count -= 1
```

**`while` 最危险的地方是死循环**，写的时候要确认循环变量一定会变：

```python
# 死循环：count 永远大于 0
# while count > 0:
#     print(count)

# 安全的写法：明确退出条件，并留一个最大次数兜底
def retry(attempts=3):
    while attempts > 0:
        print(f"剩余尝试次数：{attempts}")
        attempts -= 1
    return "结束"

print(retry())      # 剩余尝试次数：3 / 2 / 1 → 结束
```

**常见用法：处理"取到空为止"的输入：**

```python
def read_until_empty(inputs):
    result = []
    index = 0
    while index < len(inputs) and inputs[index] != "":
        result.append(inputs[index])
        index += 1
    return result

print(read_until_empty(["a", "b", "", "c"]))    # ['a', 'b']
```

### 4.4 `break`、`continue` 与 `pass`

| 关键字 | 作用 | 影响 |
|:---|:---|:---|
| `break` | 立刻结束整个循环 | 跳到循环之后 |
| `continue` | 结束本轮，进入下一轮 | 循环继续 |
| `pass` | 什么也不做 | 占位，语法需要 |

```python
for number in range(10):
    if number == 2:
        continue            # 跳过 2
    if number == 5:
        break               # 到 5 就停
    print(number)           # 0 1 3 4
```

**`pass` 的三个用途：**

```python
# 1. 占位：先写框架，逻辑后补
def todo_later():
    pass

# 2. 空的分支：暂时什么都不做
if True:
    pass
else:
    print("暂未实现")

# 3. 需要语法结构但逻辑为空
class Placeholder:
    pass
```

### 4.5 `for-else`：循环没被 `break` 打断时执行

这是 Python 特有的语法，**`else` 属于 `for`，不属于 `if`**。它的含义是"循环正常走完（没遇到 `break`）就执行"。

```python
# 场景：检查是否所有数字都是偶数
numbers = [2, 4, 6, 8]

for number in numbers:
    if number % 2 == 1:
        print(f"{number} 是奇数")
        break               # 找到奇数就退出
else:
    print("全部是偶数")      # 没被打断，说明全是偶数
# 全部是偶数
```

**典型用途：搜索"找不到"的分支。**

```python
def find_user(users, name):
    for user in users:
        if user["name"] == name:
            return user         # 找到了直接返回
    else:
        return None             # for 走完都没 return，说明没找到

users = [{"name": "Alice"}, {"name": "Bob"}]
print(find_user(users, "Bob"))      # {'name': 'Bob'}
print(find_user(users, "Cindy"))    # None
```

**用标志变量对比一下，就能看出 `for-else` 的价值：**

```python
# 传统写法：需要一个标志变量
found = False
for user in users:
    if user["name"] == "Cindy":
        found = True
        break
if not found:
    print("没找到")

# for-else：不用标志变量
for user in users:
    if user["name"] == "Cindy":
        break
else:
    print("没找到")
```

### 4.6 循环变量会泄漏（与推导式不同）

**`for` 循环的变量在循环结束后依然存在**，而推导式的变量不会（第 6 章讲过）。这个差异会导致意外的 bug：

```python
for i in range(3):
    pass

print(i)        # 2，循环结束后 i 仍然存在

# 推导式的变量不会泄漏
squares = [n ** 2 for n in range(3)]
# print(n)      # 报错：NameError，n 不存在
```

**实际影响：循环结束后误用循环变量。**

```python
items = []
for item in items:
    print(item)

# 循环一次都没执行，item 从未被赋值
# print(item)     # 报错：NameError（因为列表为空，循环体没执行过）

# 如果列表非空，循环变量就会残留
items = ["a", "b"]
for item in items:
    pass
print(item)        # b，残留值
```

### 4.7 嵌套循环与提前退出

**`break` 只能跳出它所在的那一层**，跳出多层需要额外手段：

```python
matrix = [[1, 2], [3, 4], [5, 6]]

# 错误想法：以为 break 能跳出两层
for row in matrix:
    for value in row:
        if value == 3:
            break           # 只跳出内层
    print(f"处理完一行：{row}")
# 处理完一行：[1, 2]
# 处理完一行：[3, 4]      ← 内层 break 后外层还在继续
# 处理完一行：[5, 6]

# 方法一：用标志变量
found = False
for row in matrix:
    for value in row:
        if value == 3:
            found = True
            break
    if found:
        break
print("找到了 3")

# 方法二：封装成函数，用 return 直接退出（推荐）
def find_value(matrix, target):
    for row in matrix:
        for value in row:
            if value == target:
                return row, value
    return None

print(find_value(matrix, 3))     # ([3, 4], 3)
```

**推荐方法二**：把嵌套循环放进函数，用 `return` 退出比标志变量干净得多。

### 4.8 `match` 模式匹配（Python 3.10+）

`match` 不是"加强版 switch"，它做的是**结构化匹配**：能按值的形状、类型、结构来分支。

```python
def handle(command):
    match command:
        case "start":                                    # 匹配字面量
            return "启动"
        case "stop" | "quit":                            # 匹配多个值
            return "停止"
        case ["move", x, y]:                             # 匹配序列结构
            return f"移动到 ({x}, {y})"
        case {"action": "jump", "height": h}:            # 匹配字典结构
            return f"跳跃 {h} 米"
        case int() as number if number > 0:              # 匹配类型 + 守卫条件
            return f"正整数 {number}"
        case _:                                          # 兜底
            return "未知命令"

print(handle("start"))                      # 启动
print(handle("stop"))                       # 停止
print(handle(["move", 3, 5]))               # 移动到 (3, 5)
print(handle({"action": "jump", "height": 2}))   # 跳跃 2 米
print(handle(42))                           # 正整数 42
print(handle(3.14))                         # 未知命令
```

**和 `if/elif` 的分工：**

| 场景 | 推荐 |
|:---|:---|
| 判断一个值等于几个常量之一 | `if/elif` 就够，`match` 更整齐 |
| 按数据结构形状分支（解析协议、处理 JSON） | `match` 明显更好 |
| 需要同时判断类型并绑定变量 | `match` 的 `case Type() as x` |

**注意 `case _` 是可选的兜底**，不写的话不匹配时什么都不做（不会报错），这容易掩盖问题，建议显式写出。

### 4.9 海象运算符 `:=`（Python 3.8+）

它解决的是"**算一次，既要用于判断又要用于后续**"的重复计算问题：

```python
# 传统写法：要么算两次，要么先赋值再判断
text = "Python"
length = len(text)
if length > 5:
    print(f"长度为 {length}")

# 海象运算符：在条件里赋值
text = "Python"
if (length := len(text)) > 5:
    print(f"长度为 {length}")     # 长度为 6
```

**最实用的场景是 `while` 循环读取**，它把"读取"和"判断"合成一行：

```python
# 传统写法
def read_lines_old(lines):
    result = []
    index = 0
    line = lines[index] if index < len(lines) else None
    while line is not None:
        result.append(line)
        index += 1
        line = lines[index] if index < len(lines) else None
    return result

# 海象运算符：干净很多
def read_lines_new(lines):
    result = []
    index = 0
    while (line := lines[index] if index < len(lines) else None) is not None:
        result.append(line)
        index += 1
    return result

print(read_lines_new(["a", "b", "c"]))     # ['a', 'b', 'c']
```

**更常见的实际用法：处理可能为空的返回值。**

```python
def get_config():
    return {"theme": "dark"}

# 一次调用，既判断又使用
if (config := get_config()) and config.get("theme"):
    print(f"主题是 {config['theme']}")     # 主题是 dark
```

**不要把海象用在不必要的地方**，它会让代码变难读。只在"避免重复计算"或"减少一层赋值"时用。

### 4.10 本章小结

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 多分支判断 | `if/elif/else`，条件从严格到宽松排 |
| 二选一赋值 | `x if 条件 else y` |
| 连续区间判断 | `18 <= age < 60` |
| 提供默认值 | `value or 默认值`（注意 0 和空字符串也算假） |
| 遍历固定次数 | `for i in range(n):` |
| 遍历并要下标 | `for i, v in enumerate(items):` |
| 并行遍历 | `for a, b in zip(x, y):` |
| 提前结束 | `break` |
| 跳过本轮 | `continue` |
| 占位 | `pass` |
| 循环没被打断时执行 | `for ... else:` |
| 按结构分支 | `match ... case`（3.10+） |
| 条件里赋值复用 | `if (n := f()) > 0:`（3.8+） |

**五个坑：**

1. **`if/elif` 顺序反了** —— 宽松条件放前面会让严格分支永远执行不到。
2. **`range` 终点不含** —— `range(1, 5)` 是 1 到 4。
3. **`value or default` 吞掉 0 和空字符串** —— 需要区分"没有值"和"值是 0"时必须用 `is None`。
4. **`break` 只跳一层** —— 多层嵌套要封装成函数用 `return`。
5. **循环变量会泄漏到循环外** —— 循环没执行时用它会报 `NameError`。

**往下衔接：** 循环和条件是最基础的组合，第 5 章的函数会把它们封装成可复用的逻辑。

---

## 5. 函数

函数是把一段逻辑封装成可重复调用的单元。这一章讲五个容易出错的机制：**参数的收集与展开、默认参数的求值时机、作用域查找规则、闭包的变量捕获、装饰器的包装。**

### 5.1 定义、文档字符串与返回值

```python
def add(a, b):
    """返回两个数的和。

    参数：
        a: 第一个数
        b: 第二个数
    返回：
        两数之和
    """
    return a + b


print(add(3, 5))         # 8
print(add.__doc__.splitlines()[0])   # 返回两个数的和。
```

**`__doc__` 保存文档字符串**，写清楚参数和返回值是专业习惯——`help(add)` 和 IDE 提示都靠它。

#### `return` 的行为

```python
# 没有 return：返回 None
def no_return():
    print("只是打印")

print(no_return())      # 只是打印 / None

# return 可以提前结束函数
def check(value):
    if value < 0:
        return "负数"    # 提前返回，后面的代码不执行
    return "非负数"

print(check(-1), check(1))    # 负数 非负数

# return 不带值：也返回 None
def early_exit(items):
    if not items:
        return            # 等价于 return None
    return items[0]

print(early_exit([]))          # None
```

**多返回值本质是返回一个元组**，调用时可以解包：

```python
def calculate(a, b):
    return a + b, a - b, a * b

# 返回的其实是元组
result = calculate(8, 3)
print(result)               # (11, 5, 24)
print(type(result))         # <class 'tuple'>

# 解包接收
total, difference, product = calculate(8, 3)
print(total, difference, product)     # 11 5 24

# 只要其中一个值时用下划线占位
total, _, _ = calculate(8, 3)
print(total)                # 11
```

#### 函数也是对象

函数可以赋值、当参数、放进容器——这是装饰器和回调的基础：

```python
def greet(name):
    return f"你好，{name}"

# 赋值给另一个名字
say_hello = greet
print(say_hello("Alice"))       # 你好，Alice

# 当参数传递
def apply_twice(func, value):
    return func(func(value))

def add_exclamation(text):
    return text + "!"

print(apply_twice(add_exclamation, "嗨"))   # 嗨!!

# 放进容器（第 3 章的分发表用法）
operations = {"greet": greet}
print(operations["greet"]("Bob"))           # 你好，Bob

print(type(greet))              # <class 'function'>
print(greet.__name__)           # greet
```

### 5.2 参数的五种形式

| 写法 | 名称 | 作用 |
|:---|:---|:---|
| `def f(a, b)` | 位置或关键字参数 | 最普通 |
| `def f(a, b=0)` | 默认参数 | 调用时可省略 |
| `def f(a, /)` | 仅限位置参数 | 只能按位置传（3.8+） |
| `def f(*, a)` | 仅限关键字参数 | 必须写参数名 |
| `def f(*args, **kwargs)` | 收集参数 | 接收任意数量 |

**参数顺序是固定的**：位置参数 → 默认参数 → `*args` → 仅关键字参数 → `**kwargs`。

```python
def full_example(pos1, pos2, default=0, *args, keyword_only, **kwargs):
    return {
        "pos": (pos1, pos2),
        "default": default,
        "args": args,
        "keyword_only": keyword_only,
        "kwargs": kwargs,
    }

print(full_example(1, 2, 3, 4, 5, keyword_only="必须写", extra="额外"))
# {'pos': (1, 2), 'default': 3, 'args': (4, 5), 'keyword_only': '必须写', 'extra': '额外'}
```

#### 默认参数在定义时求值一次（重要）

这是第 1 章讲过的坑，这里给出完整版：

```python
# 错误：列表只创建一次
def append_bad(item, items=[]):
    items.append(item)
    return items

print(append_bad("a"))      # ['a']
print(append_bad("b"))      # ['a', 'b']，上次的数据还在

# 正确：用 None 占位
def append_good(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

print(append_good("a"))     # ['a']
print(append_good("b"))     # ['b']
```

**这个坑的本质是：默认值是和函数对象绑定的，只在 `def` 执行时求值一次。** 验证一下：

```python
def show_default(items=[]):
    print(id(items))
    return items

show_default()      # 打印了某个 id
show_default()      # 打印的是同一个 id！证明是同一个对象
```

#### 仅限关键字参数：让调用更难写错

```python
# 反例：两个都是布尔值的参数，调用时完全看不出含义
def create_user_bad(name, is_admin, is_active):
    return f"{name}: admin={is_admin}, active={is_active}"

print(create_user_bad("Alice", True, False))    # 这两个 True/False 是什么意思？

# 正例：强制写参数名
def create_user_good(name, *, is_admin=False, is_active=True):
    return f"{name}: admin={is_admin}, active={is_active}"

print(create_user_good("Alice", is_admin=True, is_active=False))
# Alice: admin=True, active=False，一眼看懂

# create_user_good("Alice", True)   # 报错：TypeError，只能按关键字传
```

**实践建议：** 布尔参数、以及含义不直观的参数，都应该用 `*` 强制写成关键字形式。

#### 仅限位置参数：避免参数名被依赖

```python
def connect(host, port, /, timeout=5):
    return f"{host}:{port} timeout={timeout}"

print(connect("localhost", 8080))            # localhost:8080 timeout=5
print(connect("localhost", 8080, timeout=10))  # 可以用关键字传 timeout

# connect(host="localhost", port=8080)      # 报错：host 和 port 只能按位置传
```

**用途：** 参数名不算公开 API，以后想改名时不会破坏调用者。

### 5.3 `*args` 与 `**kwargs`：收集与展开

**同一套符号，位置不同含义完全相反：**

```python
# 定义侧：收集（把散的打包起来）
def collect(*args, **kwargs):
    print(f"args = {args}")         # 位置参数打包成元组
    print(f"kwargs = {kwargs}")     # 关键字参数打包成字典

collect(1, 2, 3, name="Alice", age=20)
# args = (1, 2, 3)
# kwargs = {'name': 'Alice', 'age': 20}
```

```python
# 调用侧：展开（把整的摊开）
def introduce(name, age, city):
    return f"{name}，{age} 岁，来自 {city}"

values = ["Alice", 20, "深圳"]
options = {"name": "Bob", "age": 22, "city": "广州"}

print(introduce(*values))       # Alice，20 岁，来自 深圳，列表展开成位置参数
print(introduce(**options))     # Bob，22 岁，来自 广州，字典展开成关键字参数

# 部分展开
extra = [20, "深圳"]
print(introduce("Cindy", *extra))   # Cindy，20 岁，来自 深圳
```

**`*args` 的实用模式：转发参数。**

```python
def logged_add(*args, **kwargs):
    print(f"调用参数：{args}, {kwargs}")
    return add(*args, **kwargs)     # 原样转发给真正的函数

print(logged_add(1, 2))     # 调用参数：(1, 2), {} → 3
```

### 5.4 作用域：LEGB 规则

Python 查找一个名字的顺序是 **L → E → G → B**：

| 层级 | 名称 | 范围 |
|:---|:---|:---|
| L | Local | 当前函数内部 |
| E | Enclosing | 外层函数（闭包） |
| G | Global | 模块级 |
| B | Built-in | 内置名字（`len`、`print`） |

```python
x = "全局"                  # G

def outer():
    x = "外层"              # E
    def inner():
        # x = "局部"        # L（如果取消注释，就会用这个）
        print(x)            # 先找 L，没有就找 E
    inner()

outer()                     # 外层
```

**关键点：函数内部"赋值"会创建局部变量，而不是修改全局变量。**

```python
count = 0

def increment():
    count = 1               # 这是新建局部变量，不是修改全局的
    print(f"函数内：{count}")

increment()                 # 函数内：1
print(f"函数外：{count}")    # 函数外：0，全局的没变

# 想在函数里改全局变量，必须声明 global
def increment_global():
    global count
    count += 1

increment_global()
print(count)                # 1
```

**为什么这个设计好？** 因为函数不会意外改动全局状态。`global` 显式声明让"我要改全局"这件事在代码里一眼可见。

**注意 `global` 的两个限制：**

```python
# 1. 声明必须在使用之前
def bad():
    # print(count)      # 报错：SyntaxError，声明前不能使用
    global count
    count = 5

# 2. 只读全局变量不需要 global
def read_only():
    return count        # 合法，读不需要声明

# 3. 可变全局对象原地修改也不需要 global
items = []

def add_item():
    items.append(1)     # 合法：是修改对象，不是重新绑定名字

add_item()
print(items)            # [1]
```

#### `nonlocal`：修改外层函数变量

```python
def make_counter():
    count = 0
    def counter():
        nonlocal count      # 声明"我要改的是外层函数的 count"
        count += 1
        return count
    return counter

c = make_counter()
print(c())      # 1
print(c())      # 2
print(c())      # 3
```

**`nonlocal` 和 `global` 的区别：** `global` 指向模块级，`nonlocal` 指向最近的外层函数。

### 5.5 闭包：函数记住了它的环境

**闭包 = 内层函数 + 它引用的外层变量。** 即使外层函数已经返回，那些变量依然存活：

```python
def make_multiplier(factor):
    def multiply(number):
        return number * factor      # factor 来自外层，被"记住"了
    return multiply

double = make_multiplier(2)
triple = make_multiplier(3)

print(double(5))    # 10
print(triple(5))    # 15

# 两个闭包各自记住了不同的 factor
print(double.__closure__[0].cell_contents)   # 2
print(triple.__closure__[0].cell_contents)   # 3
```

**闭包的实用场景：把配置"固化"进函数。**

```python
def make_formatter(prefix, suffix="!"):
    def format_text(text):
        return f"{prefix}{text}{suffix}"
    return format_text

info = make_formatter("[信息] ")
error = make_formatter("[错误] ", "!!")

print(info("启动完成"))       # [信息] 启动完成!
print(error("连接失败"))      # [错误] 连接失败!!
```

#### 闭包的经典陷阱：循环变量

```python
# 错误：所有函数共用同一个 i，循环结束后 i 停在最后一个值
functions = []
for i in range(3):
    functions.append(lambda: i)

print([f() for f in functions])     # [2, 2, 2]，全都返回 2！

# 原因：闭包捕获的是变量本身，不是当时的值
```

**三种修法：**

```python
# 方法一：用默认参数把当前值"固化"进去（默认参数在定义时求值）
functions = []
for i in range(3):
    functions.append(lambda i=i: i)
print([f() for f in functions])     # [0, 1, 2]

# 方法二：再包一层函数，创建独立作用域
def make_func(i):
    return lambda: i

functions = [make_func(i) for i in range(3)]
print([f() for f in functions])     # [0, 1, 2]

# 方法三：用 functools.partial
from functools import partial

functions = [partial(lambda i: i, i) for i in range(3)]
print([f() for f in functions])     # [0, 1, 2]
```

**记住这个规则：闭包捕获变量，不捕获值。** 想捕获"当时的值"就用默认参数。

### 5.6 Lambda：只写一个表达式的匿名函数

```python
# lambda 参数: 表达式
square = lambda x: x ** 2
print(square(5))        # 25

# 等价于
def square_def(x):
    return x ** 2
```

**lambda 的限制：只能有一个表达式，不能有语句**（不能写 `if/else` 语句、不能赋值、不能多行）。

```python
# lambda 里可以用条件表达式（这是表达式，不是语句）
classify = lambda n: "正数" if n > 0 else ("零" if n == 0 else "负数")
print(classify(5), classify(0), classify(-1))    # 正数 零 负数
```

**唯一的推荐用途：当 `key` 参数这种"用完就丢"的小函数。**

```python
users = [
    {"name": "Tom", "age": 20},
    {"name": "Alice", "age": 18},
]

# 推荐：lambda 只写一句，读起来不费力
by_age = sorted(users, key=lambda u: u["age"])
print([u["name"] for u in by_age])      # ['Alice', 'Tom']

# 如果逻辑复杂，就该定义正式函数
def sort_key(user):
    return (user["age"], user["name"])

by_age_name = sorted(users, key=sort_key)
print([u["name"] for u in by_age_name])  # ['Alice', 'Tom']
```

**不要给 lambda 起名字赋值**（`square = lambda x: ...`），PEP 8 明确不推荐——这种情况下 `def` 更清楚，还能写文档字符串。

### 5.7 装饰器：在不改原函数的前提下增强它

装饰器的本质是**"接收函数、返回新函数"的函数**：

```python
def log_call(function):
    def wrapper(*args, **kwargs):
        print(f"调用 {function.__name__}，参数 {args} {kwargs}")
        result = function(*args, **kwargs)
        print(f"{function.__name__} 返回 {result}")
        return result
    return wrapper


@log_call
def add(a, b):
    return a + b

print(add(2, 3))
# 调用 add，参数 (2, 3) {}
# add 返回 5
# 5
```

**`@log_call` 等价于 `add = log_call(add)`**，验证一下：

```python
def plain_add(a, b):
    return a + b

decorated = log_call(plain_add)
print(decorated(1, 2))
# 调用 plain_add，参数 (1, 2) {}
# plain_add 返回 3
# 3
```

#### 必须加 `@wraps`：否则函数信息会丢

```python
from functools import wraps

def bad_decorator(function):
    def wrapper(*args, **kwargs):
        return function(*args, **kwargs)
    return wrapper          # 没有 @wraps

def good_decorator(function):
    @wraps(function)        # 复制原函数的元信息
    def wrapper(*args, **kwargs):
        return function(*args, **kwargs)
    return wrapper


@bad_decorator
def func_a():
    """文档字符串 A"""

@good_decorator
def func_b():
    """文档字符串 B"""

print(func_a.__name__, func_a.__doc__)   # wrapper None，信息丢了！
print(func_b.__name__, func_b.__doc__)   # func_b 文档字符串 B，保住了
```

**`@wraps` 的作用是复制 `__name__`、`__doc__`、`__module__` 等元信息**，不加它会让调试、文档、序列化全部出问题。

#### 带参数的装饰器：再包一层

```python
from functools import wraps

def repeat(times):
    """把被装饰的函数重复执行 times 次。"""
    def decorator(function):
        @wraps(function)
        def wrapper(*args, **kwargs):
            result = None
            for _ in range(times):
                result = function(*args, **kwargs)
            return result
        return wrapper
    return decorator


@repeat(3)
def say(message):
    print(message)
    return message

say("你好")
# 你好
# 你好
# 你好
```

**结构记法：带参数的装饰器需要三层——最外层收参数，中间层收函数，内层收调用参数。**

#### 装饰器的实际用途

```python
import time
from functools import wraps

def timer(function):
    @wraps(function)
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        try:
            return function(*args, **kwargs)
        finally:
            cost = time.perf_counter() - start
            print(f"{function.__name__} 耗时 {cost:.4f} 秒")
    return wrapper


@timer
def slow_sum(n):
    return sum(range(n))

print(slow_sum(1_000_000))      # 499999500000 / slow_sum 耗时 0.0xxx 秒
```

常见用途：计时、日志、缓存（`functools.lru_cache`）、权限校验、重试。

### 5.8 类型注解

注解只是**给人和工具看的元信息，运行时不检查**：

```python
def find_user(user_id: int, names: list[str]) -> str | None:
    if 0 <= user_id < len(names):
        return names[user_id]
    return None

print(find_user(1, ["Alice", "Bob"]))       # Bob
print(find_user(5, ["Alice", "Bob"]))       # None

# 注解不会阻止传错类型
print(find_user("一", ["Alice"]))           # 报错：TypeError（是运行时操作报的，不是注解报的）
```

**注解的价值：** IDE 补全和检查、文档自明、静态检查工具（mypy）能提前发现问题。

```python
from typing import Optional

# 几种常见注解写法
def greet(name: str, times: int = 1) -> str:
    return ("你好，" + name) * times

def process(items: list[int]) -> dict[str, int]:
    return {"count": len(items), "sum": sum(items)}

def maybe(value: Optional[str] = None) -> Optional[str]:
    return value

print(greet("Alice", 2))            # 你好，Alice你好，Alice
print(process([1, 2, 3]))           # {'count': 3, 'sum': 6}
print(maybe())                      # None
```

### 5.9 递归

递归要满足两个条件：**有基线条件（停止）、每次调用都向基线靠近。**

```python
def factorial(number):
    if number <= 1:             # 基线条件
        return 1
    return number * factorial(number - 1)   # 规模缩小

print(factorial(5))     # 120
```

**展开看它怎么算的：**

```text
factorial(5)
= 5 * factorial(4)
= 5 * 4 * factorial(3)
= 5 * 4 * 3 * factorial(2)
= 5 * 4 * 3 * 2 * factorial(1)
= 5 * 4 * 3 * 2 * 1
= 120
```

**递归的代价是调用栈深度**，层数太深会崩：

```python
import sys

print(sys.getrecursionlimit())     # 1000，默认最大递归深度

def infinite(n):
    return infinite(n + 1)

# infinite(0)     # 报错：RecursionError: maximum recursion depth exceeded
```

**递归 vs 循环的选择：**

| 场景 | 推荐 |
|:---|:---|
| 树、图、嵌套结构的遍历 | 递归（结构本身就是递归的） |
| 简单的累加、计数 | 循环（不受深度限制） |
| 深度可能很大 | 循环或显式用栈模拟 |

```python
# 递归处理嵌套结构很自然
def flatten(items):
    result = []
    for item in items:
        if isinstance(item, list):
            result.extend(flatten(item))    # 递归展开
        else:
            result.append(item)
    return result

print(flatten([1, [2, [3, 4]], 5]))     # [1, 2, 3, 4, 5]
```

**Python 没有尾递归优化**，所以递归函数不会自动转成循环，深度大时务必改用迭代。

### 5.10 本章小结

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 默认参数 | `def f(a, b=0)`，可变默认值用 `None` 占位 |
| 强制关键字参数 | `def f(a, *, b)` |
| 接收任意位置参数 | `def f(*args)` |
| 接收任意关键字参数 | `def f(**kwargs)` |
| 展开序列传参 | `f(*items)` |
| 展开字典传参 | `f(**options)` |
| 修改全局变量 | `global x`（尽量少用） |
| 修改外层函数变量 | `nonlocal x` |
| 闭包 | 内层函数引用外层变量 |
| 循环里创建闭包 | 用默认参数固化值 `lambda i=i: i` |
| 排序依据 | `key=lambda item: ...` 或定义正式函数 |
| 装饰器 | 三层结构 + `@wraps` |
| 类型注解 | `def f(a: int) -> str:`（不检查，只提示） |
| 递归 | 必须有基线条件，注意深度上限 |

**六个坑：**

1. **可变默认参数** —— 用 `None` 占位，在函数内创建。
2. **闭包捕获变量而不是值** —— 循环里用默认参数固化。
3. **装饰器忘了 `@wraps`** —— `__name__` 和 `__doc__` 会丢。
4. **函数内赋值是新建局部变量** —— 要改全局得声明 `global`。
5. **`*args` 在定义侧是收集、调用侧是展开** —— 方向相反，别混。
6. **递归没有基线条件或深度过大** —— 会 `RecursionError`。

**往下衔接：** 装饰器和闭包是第 10 章理解 `@property`、`@classmethod`、`@dataclass` 的基础（它们都是装饰器）；`*args/**kwargs` 会在第 9 章封装通用工具函数时反复用到。

---

## 6. 迭代器、生成器与推导式

![for 循环、迭代器与生成器的调用时序：iter() 拿迭代器、next() 逐个取值、yield 暂停与恢复](.archify/sequence-py-iter-20261008-154500/visual/py-iter.visual-check.2048x1320.light.png)

> 上图的完整交互版：[打开交互图](.archify/sequence-py-iter-20261008-154500/py-iter.html)

这一章回答一个看起来简单、但很多人说不清的问题：**`for x in something` 到底是怎么工作的？**

理解了它，你会同时搞明白三件事：为什么生成器能省内存、为什么"迭代器只能用一次"、以及为什么推导式有时比循环更好写。

### 6.1 `for` 循环背后发生了什么

`for` 能遍历任何"可迭代对象"，靠的不是下标，而是两个内置函数 `iter()` 和 `next()`：

1. `iter(对象)` 向对象要一个**迭代器**；
2. 反复调 `next(迭代器)`，每调一次取一个元素；
3. 取完时迭代器抛 `StopIteration`，`for` 捕获它并结束循环。

把 `for` 拆开手写一遍，就能看清这个过程：

```python
numbers = [10, 20, 30]
iterator = iter(numbers)

print(next(iterator))  # 10
print(next(iterator))  # 20
print(next(iterator))  # 30

# 取完之后再取，就抛 StopIteration
try:
    next(iterator)
except StopIteration:
    print("取完了")     # 取完了
```

`for` 循环做的就是上面这段代码，只是它自动捕获了 `StopIteration`：

```python
numbers = [10, 20, 30]

for value in numbers:
    print(value)        # 10 20 30

# 等价于
iterator = iter(numbers)
while True:
    try:
        value = next(iterator)
    except StopIteration:
        break
    print(value)
```

**这就是为什么 `for` 能遍历字符串、列表、字典、文件、生成器**——它们都实现了"怎么取下一个"这套协议，`for` 不关心具体是什么类型。

### 6.2 可迭代对象与迭代器不是一回事

这两个概念经常被混用，但它们是两个角色：

| 概念 | 要实现的协议 | 职责 | 例子 |
|:---|:---|:---|:---|
| **可迭代对象** `Iterable` | `__iter__()` 返回一个迭代器 | 能"被遍历" | `list`、`str`、`dict`、`range` |
| **迭代器** `Iterator` | `__next__()` 取下一个；`__iter__()` 返回自己 | 记住"取到哪了" | `iter([1,2])`、生成器对象 |

**关系：可迭代对象提供迭代器，迭代器负责记录进度。** 一个可迭代对象可以被反复遍历，而一个迭代器只能走一遍。

```python
numbers = [1, 2, 3]

print(hasattr(numbers, "__iter__"))     # True：列表是可迭代对象
print(hasattr(numbers, "__next__"))     # False：列表本身不是迭代器

iterator = iter(numbers)
print(hasattr(iterator, "__next__"))    # True：迭代器有 __next__
print(iter(iterator) is iterator)       # True：迭代器的 __iter__ 返回自己
```

#### 迭代器只能走一遍

这是最容易踩的坑：**迭代器耗尽后不会自动重置，第二次遍历是空的。**

```python
numbers = [1, 2, 3]
iterator = iter(numbers)

print(list(iterator))   # [1, 2, 3]，第一次取完了
print(list(iterator))   # []，第二次什么都没了
```

**可迭代对象没有这个问题**，因为每次 `iter()` 都生成一个新的迭代器：

```python
numbers = [1, 2, 3]

print(list(numbers))    # [1, 2, 3]
print(list(numbers))    # [1, 2, 3]，照样能取
```

所以记住：**要遍历多次，就把数据存成列表；只能遍历一次的场景（大文件、无限序列）才用迭代器/生成器。**

#### 自己写一个迭代器

理解协议最好的方式是实现一遍。下面这个类从 1 数到 n：

```python
class CountUp:
    """从 1 数到 limit 的迭代器。"""

    def __init__(self, limit):
        self.limit = limit
        self.current = 0            # 记录"取到哪了"

    def __iter__(self):
        return self                 # 迭代器的 __iter__ 返回自己

    def __next__(self):
        if self.current >= self.limit:
            raise StopIteration      # 取完必须抛这个异常
        self.current += 1
        return self.current

for number in CountUp(3):
    print(number)                   # 1 2 3

print(list(CountUp(5)))             # [1, 2, 3, 4, 5]
```

**两个必须记住的细节：** `__next__` 取完时必须 `raise StopIteration`（而不是返回 `None`），`__iter__` 必须返回一个带 `__next__` 的对象。

### 6.3 生成器：写迭代器不用定义类

上面那个 `CountUp` 类写了十几行，用**生成器函数**只要三行——只要函数体里出现 `yield`，它就是一个生成器函数：

```python
def count_up(limit):
    current = 0
    while current < limit:
        current += 1
        yield current               # 产出一个值并暂停

for number in count_up(3):
    print(number)                   # 1 2 3

print(list(count_up(5)))            # [1, 2, 3, 4, 5]
```

**调用生成器函数不会执行函数体**，而是返回一个生成器对象；只有开始取元素时才真正执行：

```python
def generator_demo():
    print("开始执行")
    yield 1
    print("继续执行")
    yield 2
    print("执行结束")

gen = generator_demo()      # 注意：这里什么都没打印
print(type(gen))            # <class 'generator'>

print(next(gen))            # 开始执行 / 1
print(next(gen))            # 继续执行 / 2
# 再取就抛 StopIteration（并打印"执行结束"）
```

#### `yield` 的"暂停"是真正的暂停

`yield` 和 `return` 的区别不是"返回一个值"这么简单：

| | `return` | `yield` |
|:---|:---|:---|
| 函数状态 | 结束，局部变量销毁 | **暂停**，局部变量全部保留 |
| 下次调用 | 从头开始 | 从暂停处继续 |
| 能返回几个值 | 一次，然后函数结束 | 可以产出任意多次 |

```python
def tracker():
    count = 0
    while True:                 # 无限循环也没关系
        count += 1
        yield count             # 每次暂停时 count 都被保留

counter = tracker()
print(next(counter))    # 1
print(next(counter))    # 2
print(next(counter))    # 3
# 可以一直取下去，因为状态一直在
```

**"暂停时保留局部变量"是生成器的核心机制**，也是它能替代手写迭代器类的原因——`count` 这个局部变量就相当于 `CountUp.current`，但不用你自己维护。

#### 生成器为什么省内存

生成器**不一次性算出所有结果**，只在你要的时候算下一个：

```python
import sys

# 列表推导式：立刻算出 100 万个数字并全部存进内存
big_list = [n for n in range(1_000_000)]
print(sys.getsizeof(big_list))          # 约 8.1 MB

# 生成器表达式：只保存"怎么算"，几乎不占内存
big_gen = (n for n in range(1_000_000))
print(sys.getsizeof(big_gen))           # 200 字节左右
```

代价是**生成器只能遍历一次**，而且不能按下标访问、不能求长度：

```python
gen = (n for n in range(3))

print(len(gen))       # 报错：TypeError，生成器没有长度
print(gen[0])         # 报错：TypeError，不支持下标
```

**选择依据：** 只需要遍历一次、数据量大或无法一次装进内存（大文件、日志、无限序列）→ 生成器；需要反复遍历、随机访问、求长度 → 列表。

#### 处理大文件的典型场景

```python
def read_lines(path):
    """逐行读取，不会把整个文件读进内存。"""
    with open(path, "r", encoding="utf-8") as file:
        for line in file:
            line = line.strip()
            if line:                    # 跳过空行
                yield line

# 文件有几百 MB 也不会撑爆内存
with open("demo.txt", "w", encoding="utf-8") as f:
    f.write("第一行\n\n第二行\n第三行\n")

for line in read_lines("demo.txt"):
    print(line)     # 第一行 / 第二行 / 第三行
```

### 6.4 生成器表达式

把列表推导式的方括号换成圆括号，就得到**生成器表达式**——语法几乎一样，但返回的是生成器而不是列表：

```python
list_comp = [n ** 2 for n in range(5)]      # 立刻算出所有结果
gen_expr = (n ** 2 for n in range(5))       # 只保存算法

print(type(list_comp))      # <class 'list'>
print(type(gen_expr))       # <class 'generator'>
print(list_comp)            # [0, 1, 4, 9, 16]
print(next(gen_expr))       # 0
print(list(gen_expr))       # [1, 4, 9, 16]，注意剩下的是 1 开始
```

**函数调用时括号可以省略一层**，这是生成器表达式最常见的写法：

```python
print(sum(n ** 2 for n in range(5)))         # 30，省掉了括号
print(max(len(word) for word in ["a", "bbb"]))   # 3
print(any(n > 3 for n in range(5)))          # True

# 对比：如果用列表推导式，会先建一个完整列表
print(sum([n ** 2 for n in range(5)]))       # 30，结果相同但多占了内存
```

### 6.5 推导式：列表、字典、集合

三种推导式语法一致，只是外面的括号不同：

```python
numbers = range(6)

list_comp = [n ** 2 for n in numbers if n % 2 == 0]        # 列表
dict_comp = {n: n ** 2 for n in numbers}                   # 字典
set_comp = {n % 3 for n in numbers}                        # 集合
gen_expr = (n ** 2 for n in numbers)                       # 生成器

print(list_comp)     # [0, 4, 16]
print(dict_comp)     # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
print(set_comp)      # {0, 1, 2}
print(list(gen_expr))  # [0, 1, 4, 9, 16, 25]
```

#### 推导式的完整结构

```text
[表达式 for 变量 in 可迭代对象 if 条件]
```

三部分各有用处，**顺序不能颠倒**（`if` 必须在 `for` 之后）：

```python
words = ["Python", "Go", "Java", "C"]

upper = [w.upper() for w in words]                   # 变换
long_words = [w for w in words if len(w) > 2]        # 筛选
mapping = {w: len(w) for w in words}                 # 建立映射
pairs = [(w, len(w)) for w in words if len(w) > 3]   # 变换 + 筛选

print(upper)        # ['PYTHON', 'GO', 'JAVA', 'C']
print(long_words)   # ['Python', 'Java']
print(mapping)      # {'Python': 6, 'Go': 2, 'Java': 4, 'C': 1}
print(pairs)        # [('Python', 6), ('Java', 4)]
```

**嵌套循环**也支持，顺序和写普通嵌套循环一样：

```python
# 生成坐标对
pairs = [(x, y) for x in range(2) for y in range(2)]
print(pairs)        # [(0, 0), (0, 1), (1, 0), (1, 1)]

# 等价于
pairs2 = []
for x in range(2):
    for y in range(2):
        pairs2.append((x, y))
print(pairs2)       # [(0, 0), (0, 1), (1, 0), (1, 1)]
```

#### 什么时候不该用推导式

**推导式适合"把一批数据变换成另一批"，不适合有复杂逻辑或副作用。** 一旦出现多个条件分支、需要 try/except、或者要在循环里打印、写文件，就该用普通循环：

```python
# 好：简单变换
squares = [n ** 2 for n in range(5)]

# 差：逻辑太复杂，可读性急剧下降
# result = [a if a > 0 else -a for a in [x.strip() for x in data if ":" in x]]

# 应该改回普通循环
def parse_lines(data):
    result = []
    for raw in data:
        text = raw.strip()
        if ":" not in text:
            continue
        key, value = text.split(":", 1)
        result.append((key, value))
    return result

print(parse_lines(["a:1", "  b:2  ", "无效行"]))
# [('a', '1'), ('b', '2')]
```

#### 推导式的变量作用域

推导式里的循环变量**不会泄漏到外面**，它有自己的作用域：

```python
n = 100
squares = [n ** 2 for n in range(3)]

print(squares)   # [0, 1, 4]
print(n)         # 100，外面的 n 没被改动
```

### 6.6 `enumerate()` 与 `zip()`

这两个函数解决"遍历时还需要别的信息"的需求，比手写下标干净得多。

#### `enumerate()`：同时拿到下标和元素

```python
names = ["Alice", "Bob", "Cindy"]

# 笨写法：手动维护下标
index = 0
for name in names:
    print(index, name)
    index += 1

# 笨写法二：用 range(len(...))
for index in range(len(names)):
    print(index, names[index])

# 推荐：enumerate
for index, name in enumerate(names):
    print(index, name)                    # 0 Alice / 1 Bob / 2 Cindy

for index, name in enumerate(names, start=1):
    print(f"第 {index} 名：{name}")         # 第 1 名：Alice ...
```

**`enumerate` 返回的是迭代器**，需要列表时转一下：

```python
print(list(enumerate(["a", "b"], start=1)))   # [(1, 'a'), (2, 'b')]
```

#### `zip()`：同时遍历多个序列

```python
names = ["Alice", "Bob", "Cindy"]
scores = [90, 85, 92]
cities = ["深圳", "广州", "北京"]

for name, score, city in zip(names, scores, cities):
    print(f"{name}：{score} 分，来自 {city}")
# Alice：90 分，来自 深圳
# Bob：85 分，来自 广州
# Cindy：92 分，来自 北京

# 直接构造字典：这个写法非常常用
print(dict(zip(names, scores)))
# {'Alice': 90, 'Bob': 85, 'Cindy': 92}
```

**长度不一致时 `zip` 会截断到最短的那个**，这很容易悄悄丢掉数据：

```python
names = ["Alice", "Bob", "Cindy"]
scores = [90, 85]

print(list(zip(names, scores)))     # [('Alice', 90), ('Bob', 85)]，Cindy 被丢了！
```

**Python 3.10+ 可以加 `strict=True` 让它直接报错**，避免静默丢数据：

```python
try:
    print(list(zip(names, scores, strict=True)))
except ValueError as error:
    # 长度不一致： zip() argument 2 is shorter than argument 1
    print("长度不一致：", error)
```

**`zip(*矩阵)` 还能做"转置"**，这是处理二维数据的实用技巧：

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
]

print(list(zip(*matrix)))     # [(1, 4), (2, 5), (3, 6)]，行变成了列
```

### 6.7 `map()` 与 `filter()`

这两个函数是"函数式风格"的工具，**用推导式通常更清晰**，但读别人的代码时会遇到。

```python
numbers = [1, 2, 3, 4]

# map：对每个元素应用函数
doubled_map = list(map(lambda n: n * 2, numbers))
doubled_comp = [n * 2 for n in numbers]

# filter：保留满足条件的元素
even_filter = list(filter(lambda n: n % 2 == 0, numbers))
even_comp = [n for n in numbers if n % 2 == 0]

print(doubled_map, doubled_comp)      # [2, 4, 6, 8] [2, 4, 6, 8]
print(even_filter, even_comp)         # [2, 4] [2, 4]
```

**对比与选择：**

| 需求 | 推荐 | 原因 |
|:---|:---|:---|
| 变换 + 筛选 | 推导式 | 一个表达式搞定，可读性好 |
| 已有现成函数 | `map(f, data)` | 不用写 lambda，更短 |
| 惰性处理大数据 | `map` / `filter` / 生成器表达式 | 返回迭代器，不占内存 |
| 多个序列 | `map(f, a, b)` | 自动并行取值（类似 `zip`） |

```python
# 已有函数时 map 更简洁
words = ["hello", "world"]
print(list(map(str.upper, words)))      # ['HELLO', 'WORLD']

# map 支持多个序列
left = [1, 2, 3]
right = [10, 20, 30]
print(list(map(lambda a, b: a + b, left, right)))   # [11, 22, 33]

# map/filter 返回的是迭代器，同样只能走一遍
result = map(str.upper, words)
print(list(result))     # ['HELLO', 'WORLD']
print(list(result))     # []
```

**注意 `map` 和 `filter` 返回迭代器**，不是列表。这个特性和生成器一样，很多人第一次遇到 `print(map(...))` 输出 `<map object at 0x...>` 时会困惑。

### 6.8 本章小结

**核心模型：** `for` = `iter()` 拿迭代器 + 反复 `next()` + 捕获 `StopIteration`。

| 概念 | 特点 | 典型场景 |
|:---|:---|:---|
| 可迭代对象 | 能提供迭代器，可反复遍历 | `list`、`dict`、`str`、文件 |
| 迭代器 | 记住进度，只能走一遍 | `iter(lst)`、`map`/`filter` 的返回值 |
| 生成器 | 用 `yield` 暂停，状态保留，省内存 | 大文件、无限序列、惰性管线 |
| 生成器表达式 | 圆括号版推导式，惰性 | 配合 `sum`/`max`/`any` 用 |
| 推导式 | 立刻算出结果，返回容器 | 小数据量的变换与筛选 |

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 拿到迭代器 | `iter(obj)` |
| 取下一个 | `next(it)`；取完抛 `StopIteration` |
| 取下一个并给默认值 | `next(it, 默认值)` |
| 定义生成器 | 函数里写 `yield` |
| 惰性表达式 | `(表达式 for x in data)` |
| 带下标遍历 | `for i, v in enumerate(data):` |
| 并行遍历多个序列 | `for a, b in zip(x, y):` |
| 长度必须一致 | `zip(x, y, strict=True)`（3.10+） |
| 转置二维数据 | `list(zip(*matrix))` |
| 一次算完 | 列表推导式 `[...]` |
| 省内存 | 生成器表达式 `(...)` |

**五个容易踩的坑：**

1. **迭代器只能走一遍** —— 遍历两次要用列表，或者重新 `iter()`。
2. **生成器函数调用时不执行** —— 只创建对象，`next()` 或 `for` 才开始跑。
3. **`zip` 会按最短截断** —— 长度不一致时加 `strict=True` 让它报错。
4. **`map`/`filter`/生成器不能求长度、不能下标** —— 需要这些就先 `list()`。
5. **推导式别写太复杂** —— 出现多层嵌套或多分支就该改回普通循环。

**往下衔接：** 这一章的惰性求值会在第 9 章读大文件时直接用到；生成器也常用于第 8 章把数据处理逻辑拆成可复用的流水线。

---

## 7. 异常与上下文管理

异常处理的目标不是"让程序不报错"，而是三件事：

1. **区分可恢复的错误和真正的 bug** —— 文件不存在可以提示用户重试，除以零说明代码逻辑有问题；
2. **在错误传播时补上上下文** —— 让上层知道"哪一步失败了、为什么"；
3. **保证资源一定被释放** —— 文件、连接、锁不能因为中途出错就泄漏。

这一章先讲异常对象本身，再讲 `try` 的四个分支各自什么时候执行，然后讲怎么主动抛和怎么用 `with` 管资源。

### 7.1 异常是什么：对象、类型和传播

Python 里**异常也是对象**，抛出异常就是"创建一个异常类实例并中断当前流程"。每个异常都有类型和消息：

```python
try:
    int("abc")
except ValueError as error:
    print(type(error))       # <class 'ValueError'>
    print(error)             # invalid literal for int() with base 10: 'abc'
    print(str(error))        # 与上面相同，可读消息
    print(repr(error))       # ValueError("invalid literal for int() with base 10: 'abc'")
```

`as error` 把这个异常对象绑定到一个名字，你就能读它的消息、类型，甚至往上抛。

#### 异常的继承体系

所有异常都继承自 `BaseException`，但日常只需要关心两支：

```text
BaseException
├── SystemExit            解释器退出（sys.exit 抛的）
├── KeyboardInterrupt     用户按 Ctrl+C
├── GeneratorExit         生成器被关闭
└── Exception             所有"普通"异常的基类
    ├── ValueError            值不合适
    ├── TypeError             类型不对
    ├── KeyError              字典键不存在
    ├── IndexError            下标越界
    ├── AttributeError        属性不存在
    ├── FileNotFoundError     文件不存在（OSError 的子类）
    ├── ZeroDivisionError     除以零
    └── RuntimeError          运行期错误
```

**为什么不要捕获 `BaseException`？** 因为它会把 `KeyboardInterrupt`（用户想中断程序）和 `SystemExit`（程序想退出）也吞掉，导致脚本无法用 Ctrl+C 停掉。**日常只捕获 `Exception` 及其子类。**

```python
# 反例：连 Ctrl+C 都被吞掉，只能强杀进程
# try:
#     do_something()
# except BaseException:
#     pass

# 正例：只处理普通异常
try:
    int("abc")
except Exception as error:
    print("出错了：", error)      # 出错了： invalid literal for int() with base 10: 'abc'
```

**常用内置异常速查：**

| 异常 | 触发场景 | 典型写法 |
|:---|:---|:---|
| `ValueError` | 类型对但值不合适 | `int("abc")` |
| `TypeError` | 类型不对 | `"a" + 1` |
| `KeyError` | 字典键不存在 | `{}["x"]` |
| `IndexError` | 下标越界 | `[1][5]` |
| `AttributeError` | 属性/方法不存在 | `"a".nonexist()` |
| `ZeroDivisionError` | 除数为 0 | `1 / 0` |
| `FileNotFoundError` | 文件不存在 | `open("无此文件")` |

### 7.2 `try` 的四个分支

`try` 语句最多有四个部分：`try`、`except`、`else`、`finally`。它们的执行时机是固定的：

| 分支 | 什么时候执行 | 用途 |
|:---|:---|:---|
| `try` | 总是先执行 | 放可能出错的代码 |
| `except` | `try` 里抛出匹配的异常时 | 处理错误 |
| `else` | `try` 里**没有**异常时 | 放"只有成功才该做"的事 |
| `finally` | **无论成功失败都执行** | 释放资源 |

```python
def parse(text):
    try:
        number = int(text)              # 可能失败
    except ValueError as error:
        print(f"转换失败：{error}")
        return None
    else:
        print(f"转换成功：{number}")      # 只有没异常才走
        return number
    finally:
        print("这次解析结束")            # 无论走哪条路都走

print(parse("42"))
# 转换成功：42
# 这次解析结束
# 42

print(parse("abc"))
# 转换失败：invalid literal for int() with base 10: 'abc'
# 这次解析结束
# None
```

**`else` 为什么存在？** 因为把"成功之后才做的事"放进 `else`，可以让 `try` 块尽量小——只有真正可能出错的那一行留在 `try` 里。这样设计的好处是：**不会误捕获不该捕获的异常**。

```python
# 反例：把不该捕获的代码也放进了 try
try:
    number = int("42")
    result = 100 / number      # 这行如果出错，会被误当成"转换问题"
    print(result)
except ValueError:
    print("转换失败")           # 除零错误不会被这里捕获，但逻辑上混淆了

# 正例：try 只包住可能失败的那一句
try:
    number = int("42")
except ValueError:
    print("转换失败")
else:
    result = 100 / number      # 出错时抛的是 ZeroDivisionError，语义清晰
    print(result)              # 2.5
```

**`finally` 一定会执行吗？** 绝大多数情况下是的，包括 `try` 里 `return`、`break`、`continue` 甚至又抛了异常。但有两个例外：**进程被强杀**，或者 `try` 里执行了 `sys.exit()` / `os._exit()`。

```python
def demo():
    try:
        return "try 的返回值"
    finally:
        print("finally 依然执行了")     # 先打印这行

print(demo())
# finally 依然执行了
# try 的返回值

# 注意：finally 里如果有 return，会覆盖 try 里的返回值
def tricky():
    try:
        return "try"
    finally:
        return "finally"      # 覆盖掉上面的

print(tricky())      # finally
```

**实践建议：不要在 `finally` 里写 `return`。** 它会悄悄吞掉异常并覆盖返回值，是最难排查的 bug 之一。

### 7.3 捕获异常的正确姿势

#### 按需捕获，不要一把抓

```python
# 反例：捕获所有异常，问题被隐藏，调试时完全不知道发生了什么
# try:
#     risky()
# except Exception:
#     pass

# 正例：只捕获你知道怎么处理的异常，其余的让它暴露
def read_number(text):
    try:
        return int(text)
    except ValueError:
        return None            # 明确：格式不对时返回 None

print(read_number("42"))       # 42
print(read_number("abc"))      # None
```

**为什么"捕获所有异常"是坏习惯？** 因为它会把 `TypeError`（你写错了代码）、`AttributeError`（方法名打错）这些真正的 bug 一起吞掉，程序继续跑但结果错了，排查成本极高。

#### 捕获多个异常

```python
def divide(left, right):
    try:
        return int(left) / int(right)
    except (ValueError, ZeroDivisionError) as error:
        return f"计算失败：{error}"

print(divide("10", "2"))     # 5.0
print(divide("十", "2"))     # 计算失败：invalid literal for int() with base 10: '十'
print(divide("10", "0"))     # 计算失败：division by zero
```

**多个 `except` 是自上而下匹配的，所以子类异常必须写在父类前面：**

```python
# 错误顺序：FileNotFoundError 是 OSError 的子类，永远轮不到它
# try:
#     open("无此文件")
# except OSError:
#     print("IO 错误")
# except FileNotFoundError:      # 永远不可达，逻辑上无意义
#     print("文件不存在")

# 正确顺序：具体的在前，笼统的在后
try:
    open("无此文件")
except FileNotFoundError:
    print("文件不存在")            # 文件不存在
except OSError:
    print("其他 IO 错误")
```

#### 拿到异常信息

```python
try:
    {}["missing"]
except KeyError as error:
    print(f"缺少的键是 {error}")     # 缺少的键是 'missing'
    print(f"异常类型是 {type(error).__name__}")   # 异常类型是 KeyError
```

**`KeyError` 的消息带引号**（`'missing'`），因为它打印的是键的 `repr()`。要取键本身用 `error.args[0]`：

```python
try:
    {}["missing"]
except KeyError as error:
    key = error.args[0]
    print(f"键 {key} 不存在")        # 键 missing 不存在
    print(f"键类型 {type(key).__name__}")   # 键类型 str
```

### 7.4 主动抛出异常与异常链

#### `raise`：主动拒绝非法状态

**在函数入口检查参数、发现不合法就立刻抛异常**，比让错误数据往下传播好得多：

```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError(f"年龄必须是整数，收到 {type(age).__name__}")
    if age < 0 or age > 150:
        raise ValueError(f"年龄必须在 0 到 150 之间，收到 {age}")
    return age

print(set_age(20))     # 20

try:
    set_age(-5)
except ValueError as error:
    print("捕获到：", error)      # 捕获到： 年龄必须在 0 到 150 之间，收到 -5

try:
    set_age("20")
except TypeError as error:
    print("捕获到：", error)      # 捕获到： 年龄必须是整数，收到 str
```

这套写法叫**"尽早失败"**：错误在源头就暴露，调用栈还没跑远，定位成本最低。错误消息里带上实际收到的值，是排查效率的关键。

**该抛哪种异常？**

| 情况 | 用哪个 |
|:---|:---|
| 参数类型不对 | `TypeError` |
| 参数类型对但值不合法 | `ValueError` |
| 对象状态不允许这个操作 | `RuntimeError` 或自定义异常 |
| 功能还没实现 | `NotImplementedError` |

#### `raise ... from`：保留最初的异常

捕获一个异常、转换后重新抛出时，**一定要用 `from` 保留原始原因**：

```python
def read_port(config):
    try:
        port = int(config["port"])
    except (KeyError, ValueError) as error:
        raise RuntimeError("port 配置缺失或格式错误") from error

try:
    read_port({})
except RuntimeError as error:
    print("错误：", error)                       # 错误： port 配置缺失或格式错误
    print("原因：", type(error.__cause__).__name__)   # 原因： KeyError
```

如果不写 `from`，Python 3 仍会隐式地把当前异常挂在 `__context__` 上，但加 `from` 是**显式声明因果**，更清楚，也让 `__cause__` 可以直接读取。

```python
# 不带 from：上下文是隐式的
try:
    try:
        int("abc")
    except ValueError:
        raise RuntimeError("处理失败")
except RuntimeError as error:
    print(type(error.__context__).__name__)   # ValueError
    print(error.__cause__)                    # None
```

### 7.5 自定义异常

内置异常不够表达业务含义时，就自定义。**自定义异常让调用者能精确捕获"你这种错误"，而不误伤其他错误。**

```python
class ConfigError(Exception):
    """配置无效。"""

class ConfigMissingError(ConfigError):
    """缺少必需的配置项。"""

class ConfigFormatError(ConfigError):
    """配置项格式不对。"""


def validate(config):
    if "database_url" not in config:
        raise ConfigMissingError("缺少 database_url")
    if not config["database_url"].startswith("postgres://"):
        raise ConfigFormatError(f"database_url 格式不对：{config['database_url']}")
    return config

# 可以精确捕获某一种
try:
    validate({})
except ConfigMissingError as error:
    print("缺配置：", error)        # 缺配置： 缺少 database_url

# 也可以按基类捕获整类问题——这正是分层的意义
try:
    validate({"database_url": "mysql://localhost"})
except ConfigError as error:
    print("配置问题：", error)      # 配置问题： database_url 格式不对：mysql://localhost
```

**设计自定义异常的要点：**

| 要点 | 做法 |
|:---|:---|
| 继承谁 | 业务错误继承 `Exception`；如果要和内置某类错误归为一类，就继承它（如继承 `ValueError`） |
| 要不要分层 | 建议设一个基类（如 `ConfigError`），具体错误继承它，调用者就能按粒度捕获 |
| 要不要带数据 | 除了消息，可以把出错的字段、值存成属性，方便上层处理 |
| 类名 | 以 `Error` 结尾，一眼看出是异常 |

```python
class ValidationError(Exception):
    def __init__(self, field, value, reason):
        super().__init__(f"{field}={value!r} 不合法：{reason}")
        self.field = field          # 上层可以拿到结构化信息
        self.value = value
        self.reason = reason

try:
    raise ValidationError("age", -5, "必须是非负数")
except ValidationError as error:
    print(error)              # age=-5 不合法：必须是非负数
    print(error.field)        # age
    print(error.reason)       # 必须是非负数
```

### 7.6 `with`：让资源释放不依赖你的记忆

#### 手写 `try/finally` 的问题

打开文件后必须关闭，否则可能丢数据、耗尽文件句柄。手写的话是这样：

```python
file = open("data.txt", "w", encoding="utf-8")
try:
    file.write("内容")
finally:
    file.close()          # 保证关闭
```

这段代码是对的，但**样板太多**：每个资源都要写一遍，而且容易忘。`with` 把它变成一行：

```python
with open("data.txt", "w", encoding="utf-8") as file:
    file.write("内容")
# 离开 with 块时自动关闭，无论中间是否抛异常
print(file.closed)        # True
```

#### `with` 背后的协议

`with` 要求对象实现两个方法：

| 方法 | 什么时候调用 | 返回值 |
|:---|:---|:---|
| `__enter__()` | 进入 `with` 块时 | 绑定给 `as` 后面的名字 |
| `__exit__(exc_type, exc_value, traceback)` | 离开 `with` 块时（正常或异常） | 返回 `True` 表示"异常已处理，不再向上抛" |

自己实现一个，就能看清机制：

```python
class Resource:
    def __enter__(self):
        print("1. 获取资源")
        return self                  # 这个返回值给 as 后面的名字

    def __exit__(self, exc_type, exc_value, traceback):
        print(f"3. 释放资源（异常类型：{exc_type.__name__ if exc_type else '无'}）")
        return False                 # False：异常继续向上抛

    def work(self):
        print("2. 使用资源")

with Resource() as resource:
    resource.work()
# 1. 获取资源
# 2. 使用资源
# 3. 释放资源（异常类型：无）

with Resource() as resource:
    raise ValueError("出问题了")
# 1. 获取资源
# 3. 释放资源（异常类型：ValueError）
# 然后 ValueError 继续向上抛
```

**`__exit__` 返回 `True` 会吞掉异常**，所以要谨慎——只在确实处理完错误时才返回 `True`：

```python
class Suppress:
    def __enter__(self):
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type is ValueError:
            print(f"已忽略：{exc_value}")
            return True              # 吞掉 ValueError
        return False                 # 其他异常照常抛出

with Suppress():
    raise ValueError("会被忽略")

print("程序继续往下走")

try:
    with Suppress():
        raise TypeError("不会被忽略")
except TypeError as error:
    print("TypeError 被抛出：", error)
# 已忽略：会被忽略
# 程序继续往下走
# TypeError 被抛出： 不会被忽略
```

#### 用 `@contextmanager` 快速写一个

实现一个完整的上下文管理器类太啰嗦，`contextlib.contextmanager` 允许你用生成器写：

```python
from contextlib import contextmanager
import time

@contextmanager
def timer(label):
    start = time.perf_counter()
    try:
        yield                    # yield 之前 = __enter__，之后 = __exit__
    finally:
        cost = time.perf_counter() - start
        print(f"{label} 耗时 {cost:.4f} 秒")

with timer("求和"):
    total = sum(range(1_000_000))
    print("结果：", total)
# 结果： 499999500000
# 求和 耗时 0.0xxx 秒
```

**关键点：`yield` 必须放在 `try/finally` 里**，否则 `with` 块里抛异常时释放逻辑不会执行。`yield` 的值会绑定给 `as` 后面的名字（不写 `as` 就忽略）。

```python
from contextlib import contextmanager

@contextmanager
def managed_file(path, mode="r"):
    print("打开文件")
    file = open(path, mode, encoding="utf-8")
    try:
        yield file               # 把文件对象交出去
    finally:
        file.close()
        print("关闭文件")

with managed_file("demo.txt", "w") as file:
    file.write("hello")
# 打开文件
# 关闭文件
```

#### 标准库现成的上下文管理器

日常不用自己写，标准库已经提供了常用的：

| 用途 | 写法 |
|:---|:---|
| 文件 | `with open(...) as f:` |
| 多个资源 | `with open(a) as f1, open(b) as f2:` |
| 忽略指定异常 | `with contextlib.suppress(FileNotFoundError):` |
| 临时切换工作目录 | `with contextlib.chdir(path):`（3.11+） |
| 线程锁 | `with lock:` |
| 数据库事务 | `with connection:` |

```python
import contextlib
import os

# 忽略"文件不存在"这类预期内的错误
with contextlib.suppress(FileNotFoundError):
    os.remove("不存在的文件.txt")
print("没有因为文件不存在而中断")

# 同时管理多个资源：任何一个出错，两个都会正常关闭
with open("a.txt", "w", encoding="utf-8") as f1, \
     open("b.txt", "w", encoding="utf-8") as f2:
    f1.write("第一个")
    f2.write("第二个")

print("两个文件都写完了")
```

### 7.7 本章小结

**`try` 四个分支的执行时机：**

| 分支 | 执行条件 |
|:---|:---|
| `try` | 总是先执行 |
| `except` | 抛出了匹配的异常 |
| `else` | `try` 没抛异常 |
| `finally` | 无论有没有异常都执行 |

**常见写法速查：**

| 想做什么 | 写法 |
|:---|:---|
| 捕获特定异常 | `except ValueError as error:` |
| 捕获多种异常 | `except (ValueError, TypeError) as error:` |
| 转换后重抛并保留原因 | `raise RuntimeError("...") from error` |
| 读异常消息 | `str(error)`；取 `KeyError` 的键用 `error.args[0]` |
| 自定义异常 | `class MyError(Exception): pass` |
| 保证资源释放 | `with open(...) as f:` |
| 忽略预期内的异常 | `with contextlib.suppress(FileNotFoundError):` |
| 自己写上下文管理器 | `@contextmanager` + `yield` 放在 `try/finally` 里 |

**六个实践原则：**

1. **只捕获你知道怎么处理的异常**，其余让它暴露；不要写 `except Exception: pass`。
2. **不要捕获 `BaseException`**，它会吞掉 Ctrl+C 和程序退出。
3. **多个 `except` 里，子类异常写在父类前面**，否则永远匹配不到。
4. **`try` 块尽量小**，把"只有成功才做"的事放进 `else`。
5. **不要在 `finally` 里写 `return`**，它会覆盖返回值、吞掉异常。
6. **资源管理一律用 `with`**，不要手写 `close()` 或依赖垃圾回收。

**往下衔接：** 这一章的错误处理模型会和第 5 章的函数、第 9 章的文件读写配合使用——读文件、解析 JSON 是最需要异常处理的两个场景。

---

## 8. 模块与包

![Python 的 import 到底做了什么：从 sys.modules 缓存到 sys.path 搜索，再到加载成模块对象的完整链路](.archify/architecture-py-import-20261008-154500/visual/py-import.visual-check.2048x1320.light.png)

> 上图的完整交互版（可切换明暗主题、搜索节点、导出图片）：[打开交互图](.archify/architecture-py-import-20261008-154500/py-import.html)

### 8.1 常用导入方式

```python
import math
import json as json_module
from pathlib import Path
from collections import Counter, defaultdict

print(math.sqrt(16))
print(Path.cwd())
```

### 8.2 自定义模块

```python
# calculator.py
PI = 3.14159

def add(a, b):
    return a + b
```

```python
# main.py
import calculator

print(calculator.PI)
print(calculator.add(3, 5))
```

### 8.3 `__name__ == "__main__"`

<font color="#1E88E5"><b>用途：</b></font> 只在文件被直接运行时执行入口代码，被导入时不执行。

```python
def main():
    print("程序入口")


if __name__ == "__main__":
    main()
```

### 8.4 包结构

```text
my_project/
├── main.py
└── my_package/
    ├── __init__.py
    ├── math_utils.py
    └── services/
        ├── __init__.py
        └── report.py
```

### 8.5 绝对导入与相对导入

```python
# my_package/services/report.py

# 绝对导入：从顶层包开始
from my_package.math_utils import add

# 相对导入：一个点表示当前包，两个点表示上一级包
from ..math_utils import add
```

按模块方式运行包内文件：

```bash
python -m my_package.services.report
```

### 8.6 `__init__.py`

<font color="#1E88E5"><b>用途：</b></font> 初始化包、集中导出公共接口、保存少量包级元数据。

```python
# my_package/__init__.py
"""my_package 的公共接口。"""

from .math_utils import add

__all__ = ["add"]
__version__ = "1.0.0"
```

### 8.7 包中的常用特殊变量

| 变量 | 来源 | 用途 |
|:---|:---:|:---|
| `__name__` | Python 自动提供 | 当前模块或包的完整名称 |
| `__package__` | Python 自动提供 | 当前模块所属包，用于解析相对导入 |
| `__file__` | 加载器通常提供 | 当前模块文件路径 |
| `__path__` | 包对象具有 | 查找子模块时使用的包路径 |
| `__spec__` | Python 自动提供 | 模块的导入规范信息 |
| `__all__` | 开发者定义 | 声明 `import *` 时公开的名称 |
| `__version__` | 开发者约定 | 保存包版本号 |

```python
import my_package

print(my_package.__name__)
print(my_package.__package__)
print(my_package.__file__)
print(my_package.__path__)
print(my_package.__version__)
```

### 8.8 获取已安装发行包版本

```python
from importlib.metadata import version

print(version("requests"))
```

### 8.9 模块搜索路径

```python
import sys

for path in sys.path:
    print(path)
```

---


---

## 9. 文件、路径与 JSON

这一章把数据从内存持久化到磁盘。核心是四件事：**文件模式的选择、字符编码、用 `with` 保证关闭、以及 `pathlib` 处理路径。**

### 9.1 打开文件的模式

| 模式 | 含义 | 文件不存在时 | 文件已存在时 |
|:---:|:---|:---|:---|
| `r` | 只读（默认） | 抛 `FileNotFoundError` | 从头读 |
| `w` | 只写 | 创建 | **清空原有内容** |
| `a` | 追加 | 创建 | 在末尾接着写 |
| `x` | 独占创建 | 创建 | 抛 `FileExistsError` |
| `r+` | 读写 | 抛错 | 不截断，从开头读写 |
| `rb` / `wb` | 二进制读写 | 同 `r` / `w` | 同 `r` / `w` |

**`w` 会清空文件，这是最危险的模式**，误用会丢数据：

```python
# 演示 w 和 a 的区别
with open("demo.txt", "w", encoding="utf-8") as file:
    file.write("第一行\n")

with open("demo.txt", "a", encoding="utf-8") as file:      # 追加
    file.write("第二行\n")

with open("demo.txt", "r", encoding="utf-8") as file:
    print(repr(file.read()))        # '第一行\n第二行\n'

with open("demo.txt", "w", encoding="utf-8") as file:      # 清空重写
    file.write("只有这行\n")

with open("demo.txt", "r", encoding="utf-8") as file:
    print(repr(file.read()))        # '只有这行\n'
```

**`x` 模式适合"绝不能覆盖已有文件"的场景：**

```python
with open("new_file.txt", "x", encoding="utf-8") as file:
    file.write("首次创建")

try:
    with open("new_file.txt", "x", encoding="utf-8") as file:
        file.write("想再创建一次")
except FileExistsError as error:
    print("文件已存在，拒绝覆盖：", error)
```

**二进制模式用于图片、音视频等非文本数据**，读出来是 `bytes`：

```python
with open("demo.txt", "wb") as file:
    file.write(b"\xe4\xbd\xa0\xe5\xa5\xbd")     # 直接写字节

with open("demo.txt", "rb") as file:
    data = file.read()
    print(type(data), len(data))                # <class 'bytes'> 6
    print(data.decode("utf-8"))                 # 你好
```

### 9.2 读文件的三种方式

```python
with open("demo.txt", "w", encoding="utf-8") as file:
    file.write("第一行\n第二行\n第三行\n")

# 方式一：一次读全部 —— 简单，但大文件会占满内存
with open("demo.txt", "r", encoding="utf-8") as file:
    content = file.read()
print(repr(content))            # '第一行\n第二行\n第三行\n'

# 方式二：逐行迭代 —— 推荐，内存友好
with open("demo.txt", "r", encoding="utf-8") as file:
    for line in file:
        print(line.rstrip("\n"))     # 第一行 / 第二行 / 第三行

# 方式三：读成列表 —— 每行带换行符
with open("demo.txt", "r", encoding="utf-8") as file:
    lines = file.readlines()
print(lines)                    # ['第一行\n', '第二行\n', '第三行\n']
```

**三个要点：**

1. **`for line in file` 是最省内存的方式**，它不会把整个文件读进来（第 6 章讲的迭代器机制）。
2. **每行末尾都带 `\n`**，几乎总要用 `rstrip("\n")` 去掉。
3. **`read()` 对大文件很危险**，几百 MB 的文件会把内存吃光。

```python
# 处理大文件的标准写法
def count_lines(path):
    total = 0
    with open(path, "r", encoding="utf-8") as file:
        for line in file:
            if line.strip():        # 跳过空行
                total += 1
    return total

with open("big.txt", "w", encoding="utf-8") as file:
    for i in range(100):
        file.write(f"第 {i} 行\n")

print(count_lines("big.txt"))       # 100
```

### 9.3 写文件的几种方式

```python
# write()：写一个字符串
with open("out.txt", "w", encoding="utf-8") as file:
    file.write("第一行\n")
    file.write("第二行\n")

# writelines()：写一批字符串（注意：不会自动加换行）
lines = ["Python\n", "Java\n", "Go\n"]
with open("lang.txt", "w", encoding="utf-8") as file:
    file.writelines(lines)

with open("lang.txt", "r", encoding="utf-8") as file:
    print(file.read())          # Python\nJava\nGo\n

# print() 也能写文件，用 file 参数
with open("print.txt", "w", encoding="utf-8") as file:
    print("用 print 写", file=file)
    print("自动带换行", file=file)

with open("print.txt", "r", encoding="utf-8") as file:
    print(repr(file.read()))    # '用 print 写\n自动带换行\n'
```

**`writelines()` 不会自动加换行**，这是容易踩的坑——如果 `lines` 里的元素没有 `\n`，写出来会连成一行。

**追加日志的实用写法：**

```python
import datetime

def log(message):
    timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open("app.log", "a", encoding="utf-8") as file:
        file.write(f"[{timestamp}] {message}\n")

log("程序启动")
log("处理完成")

with open("app.log", "r", encoding="utf-8") as file:
    print(file.read())
```

### 9.4 `with`：为什么必须用它

不用 `with` 的话，忘记 `close()` 会怎样？**数据可能没写进磁盘，文件句柄也会泄漏。**

```python
# 危险写法：中间出错就不会关闭
file = open("demo2.txt", "w", encoding="utf-8")
file.write("内容")
# 如果这里抛异常，close() 永远执行不到
file.close()

# 安全写法：with 保证离开代码块时一定关闭
with open("demo2.txt", "w", encoding="utf-8") as file:
    file.write("内容")
print(file.closed)      # True，已经关闭

# 即使抛出异常，文件也会被正确关闭
try:
    with open("demo2.txt", "w", encoding="utf-8") as file:
        file.write("写一半")
        raise ValueError("模拟出错")
except ValueError:
    pass
print(file.closed)      # True，照样关闭了
```

**同时打开多个文件**（比如复制文件）：

```python
with open("source.txt", "w", encoding="utf-8") as file:
    file.write("要复制的内容\n第二行\n")

# 一行打开两个文件
with open("source.txt", "r", encoding="utf-8") as src, \
     open("target.txt", "w", encoding="utf-8") as dst:
    for line in src:
        dst.write(line)

with open("target.txt", "r", encoding="utf-8") as file:
    print(file.read())      # 要复制的内容\n第二行\n
```

### 9.5 `pathlib`：更现代的路径处理

**旧写法（字符串拼路径）的问题**是斜杠在不同系统不一样，容易拼错：

```python
# 旧写法：手动拼接，Windows 和 Linux 分隔符不同
import os

path_old = os.path.join("data", "sub", "file.txt")
print(path_old)                     # data\sub\file.txt（Windows）

# 新写法：Path 对象用 / 运算符拼接，自动适配系统
from pathlib import Path

path = Path("data") / "sub" / "file.txt"
print(path)                         # data\sub\file.txt
print(type(path))                   # <class 'pathlib.WindowsPath'>
```

**推荐 `pathlib` 的三个理由：** 用 `/` 拼路径不用管分隔符、路径是对象有丰富方法、读写文本一行搞定。

```python
from pathlib import Path

# 常用属性
p = Path("D:/desk/学习笔记/demo.txt")
print(p.name)           # demo.txt，文件名
print(p.stem)           # demo，不含扩展名
print(p.suffix)         # .txt，扩展名
print(p.parent)         # D:\desk\学习笔记，父目录
print(p.parts)          # ('D:\\', 'desk', '学习笔记', 'demo.txt')

# 常用方法
print(p.exists())       # 是否存在
print(p.is_file())      # 是否是文件
print(p.is_dir())       # 是否是目录
print(p.absolute())     # 绝对路径
```

**文件读写的一行写法：**

```python
from pathlib import Path

file_path = Path("demo3.txt")

file_path.write_text("用 pathlib 写内容", encoding="utf-8")
print(file_path.read_text(encoding="utf-8"))     # 用 pathlib 写内容

# 追加需要显式打开
with file_path.open("a", encoding="utf-8") as file:
    file.write("\n追加一行")
print(file_path.read_text(encoding="utf-8"))     # 用 pathlib 写内容\n追加一行
```

**创建目录和检查：**

```python
from pathlib import Path

data_dir = Path("output") / "reports"
data_dir.mkdir(parents=True, exist_ok=True)     # parents 建多级，exist_ok 已存在不报错
print(data_dir.exists())        # True

# 写文件到该目录
report = data_dir / "summary.txt"
report.write_text("报告内容", encoding="utf-8")
print(report.read_text(encoding="utf-8"))       # 报告内容
```

**`parents=True` 会连父目录一起创建**，`exist_ok=True` 让目录已存在时不报错——这两个参数几乎总是一起用。

### 9.6 遍历目录与筛选文件

```python
from pathlib import Path

# 列出目录下的文件（不含子目录）
for item in Path(".").iterdir():
    if item.is_file() and item.suffix == ".md":
        print(item.name)
```

**`glob()` 用通配符筛选，`rglob()` 递归所有子目录：**

```python
from pathlib import Path

# 当前目录的 .txt 文件
for path in Path(".").glob("*.txt"):
    print("glob:", path.name)

# 递归所有子目录的 .md 文件（等价于 glob("**/*.md")）
for path in Path(".").rglob("*.md"):
    print("rglob:", path.name)

# 多个模式筛选
for path in Path(".").iterdir():
    if path.is_file() and path.suffix in {".txt", ".log"}:
        print("按后缀筛选:", path.name)
```

**做一个实用的小工具：统计目录下各类文件的数量。**

```python
from pathlib import Path
from collections import Counter

def count_by_suffix(directory):
    counter = Counter()
    for path in Path(directory).rglob("*"):
        if path.is_file():
            counter[path.suffix or "(无扩展名)"] += 1
    return counter

# 在当前目录统计
result = count_by_suffix(".")
for suffix, count in result.most_common(5):
    print(f"{suffix}: {count}")
```

### 9.7 JSON 读写

**JSON 是数据交换的事实标准**：结构简单（对象、数组、字符串、数字、布尔、null），几乎所有语言都支持。Python 的 `json` 模块负责在 Python 对象和 JSON 文本之间转换。

**对应关系：**

| Python | JSON | 说明 |
|:---|:---|:---|
| `dict` | object `{}` | 键必须是字符串 |
| `list` / `tuple` | array `[]` | 元组会变成数组 |
| `str` | string | |
| `int` / `float` | number | |
| `True` / `False` | true / false | 注意大小写不同 |
| `None` | null | |
| `datetime` | **不支持** | 需要自己转成字符串 |

#### 写 JSON

```python
import json

data = {
    "name": "Alice",
    "age": 20,
    "skills": ["Python", "SQL"],
    "active": True,
    "score": None,
}

# 写进文件
with open("user.json", "w", encoding="utf-8") as file:
    json.dump(data, file, ensure_ascii=False, indent=2)

with open("user.json", "r", encoding="utf-8") as file:
    print(file.read())
```

输出：

```json
{
  "name": "Alice",
  "age": 20,
  "skills": [
    "Python",
    "SQL"
  ],
  "active": true,
  "score": null
}
```

**两个必加参数：**

| 参数 | 作用 | 不加会怎样 |
|:---|:---|:---|
| `ensure_ascii=False` | 允许直接输出中文 | 中文变成 `\u4f60\u597d` |
| `indent=2` | 缩进美化 | 全部挤在一行 |

```python
import json

data = {"name": "中文"}

# 不加 ensure_ascii：中文被转义
print(json.dumps(data))                                  # {"name": "\u4e2d\u6587"}

# 加了之后正常显示
print(json.dumps(data, ensure_ascii=False))               # {"name": "中文"}
print(json.dumps(data, ensure_ascii=False, indent=2))
# {
#   "name": "中文"
# }
```

#### 读 JSON

```python
import json

# 从文件读
with open("user.json", "r", encoding="utf-8") as file:
    loaded = json.load(file)            # load：读文件

print(type(loaded))                     # <class 'dict'>
print(loaded["name"])                   # Alice
print(loaded["skills"][0])              # Python

# 从字符串读
text = '{"a": 1, "b": [2, 3]}'
parsed = json.loads(text)               # loads：读字符串
print(parsed["b"])                      # [2, 3]
```

**`load` 和 `loads` 的区别就是"从哪读"**：带 `s` 的接收字符串（s = string），不带 `s` 的接收文件对象。

#### 处理 JSON 解析错误

**外部数据永远不可信**，一定要处理格式错误：

```python
import json

bad_texts = ['{"a": 1}', '{"a": 1,}', '不是 JSON', '[1, 2, 3]']

for text in bad_texts:
    try:
        parsed = json.loads(text)
        print(f"{text!r} → 解析成功，类型 {type(parsed).__name__}")
    except json.JSONDecodeError as error:
        print(f"{text!r} → 解析失败：{error.msg}（位置 {error.pos}）")
# '{"a": 1}' → 解析成功，类型 dict
# '{"a": 1,}' → 解析失败：Expecting property name enclosed in double quotes（位置 8）
# '不是 JSON' → 解析失败：Expecting value（位置 0）
# '[1, 2, 3]' → 解析成功，类型 list
```

**注意最后一例：JSON 顶层可以是数组**，解析出来是 `list` 而不是 `dict`。写代码时不能假设一定是字典。

#### 安全地取嵌套数据

JSON 嵌套很深时，一路 `["key"]` 很容易 `KeyError`。**用 `get()` 链式兜底，或者用 try/except：**

```python
import json

data = json.loads('{"user": {"profile": {"name": "Alice"}}}')

# 危险：任何一层缺失都报错
# print(data["user"]["profile"]["age"])     # KeyError: 'age'

# 安全写法一：逐层 get
age = data.get("user", {}).get("profile", {}).get("age", "未知")
print(age)      # 未知

# 安全写法二：try/except
def get_deep(data, *keys, default=None):
    for key in keys:
        try:
            data = data[key]
        except (KeyError, TypeError, IndexError):
            return default
    return data

print(get_deep(data, "user", "profile", "name"))    # Alice
print(get_deep(data, "user", "profile", "age", default=0))   # 0
```

#### 对象与 JSON 的互转

**自定义对象不能直接序列化**，要先转成字典：

```python
import json
from dataclasses import dataclass, asdict

@dataclass
class User:
    name: str
    age: int

user = User("Alice", 20)

# 直接序列化对象会报错
try:
    json.dumps(user)
except TypeError as error:
    print("不能直接序列化：", error)      # Object of type User is not JSON serializable

# 正确：用 asdict 转成字典
print(json.dumps(asdict(user), ensure_ascii=False))     # {"name": "Alice", "age": 20}

# 反向：由字典创建对象
loaded = json.loads('{"name": "Bob", "age": 22}')
print(User(**loaded))       # User(name='Bob', age=22)
```

### 9.8 本章小结

**速查表：**

| 想做什么 | 写法 |
|:---|:---|
| 读全部内容 | `with open(p, encoding="utf-8") as f: f.read()` |
| 逐行读（省内存） | `for line in f:` |
| 读成列表 | `f.readlines()` |
| 写入 | `f.write("文本")` |
| 追加 | `open(p, "a", encoding="utf-8")` |
| 复制文件 | 同时 `with` 打开两个文件逐行写 |
| 用 Path 拼路径 | `Path("a") / "b" / "c.txt"` |
| 一行读写 | `p.write_text(...)` / `p.read_text(...)` |
| 建多级目录 | `p.mkdir(parents=True, exist_ok=True)` |
| 递归找文件 | `Path(".").rglob("*.md")` |
| 写 JSON | `json.dump(data, f, ensure_ascii=False, indent=2)` |
| 读 JSON | `json.load(f)` |
| 字符串版 JSON | `json.dumps()` / `json.loads()` |
| 对象转字典 | `dataclasses.asdict(obj)` |

**六个坑：**

1. **`w` 模式会清空文件** —— 想追加必须用 `a`。
2. **不写 `encoding="utf-8"`** —— 跨平台会乱码。
3. **`writelines()` 不会自动加换行** —— 要自己保证每个元素带 `\n`。
4. **忘了 `with`** —— 用 `with` 保证关闭，不要手写 `close()`。
5. **JSON 的中文变 `\uXXXX`** —— 加 `ensure_ascii=False`。
6. **直接索引嵌套 JSON** —— 用 `get()` 链或 try/except 兜底。

**往下衔接：** 第 11 章会讲到处理这批数据的常用内置函数；实际做数据处理时，读 JSON/CSV → 清洗 → 写回，就是这一章加上第 3 章容器的组合。

---

## 10. 面向对象编程

先建立整体印象：**类和对象不是"把数据和方法装在一起"这么简单**。这一章要讲清的是一套查找和绑定机制：

1. **属性查找**：访问 `obj.x` 时，Python 按"实例 → 类 → 父类 → … → object"的顺序找，找到就停。
2. **方法绑定**：方法定义在类里只是普通函数，通过实例访问时才会自动把实例作为第一个参数传进去（这就是 `self` 的来源）。
3. **继承与 MRO**：`super()` 不是"父类"，而是"沿着方法解析顺序的下一站"。

![属性和方法是怎么被找到的：实例 → 类 → 父类 → object 的查找顺序，以及 self 是怎么绑定的](.archify/sequence-py-attr-20261008-154500/visual/py-attr.visual-check.2048x1320.light.png)

> 上图的完整交互版：[打开交互图](.archify/sequence-py-attr-20261008-154500/py-attr.html)

### 10.1 类、实例属性与类属性

```python
class Student:
    school = "Python University"      # 类属性：所有实例共享

    def __init__(self, name, age):
        self.name = name              # 实例属性：每个实例独有
        self.age = age

    def introduce(self):
        return f"我是 {self.name}，今年 {self.age} 岁"


student = Student("Alice", 20)
print(student.introduce())     # 我是 Alice，今年 20 岁
print(Student.school)          # Python University，通过类名访问类属性
print(student.school)          # Python University，通过实例也能读到
```

#### 读取和赋值的行为不一样

这是最容易误解的地方：**读属性会往上找，赋值只会在当前对象上创建。**

```python
class Config:
    theme = "light"          # 类属性


first = Config()
second = Config()

print(first.theme, second.theme)     # light light，都读到了类属性

first.theme = "dark"        # 注意：这是在 first 上新建实例属性
print(first.theme)          # dark，读到自己的实例属性
print(second.theme)         # light，second 没有实例属性，还是读类的
print(Config.theme)         # light，类属性本身没变
```

**结论：给实例属性赋值永远不会修改类属性。** 想改类属性必须显式写类名：

```python
Config.theme = "dark"
print(Config().theme)       # dark，新实例也读到了新值
```

#### `__init__` 不是构造函数

严格说 `__new__` 负责创建对象，`__init__` 负责初始化。日常只需记住：**`__init__` 在对象创建后被自动调用，`self` 就是刚创建的那个对象**。

```python
class Counter:
    def __init__(self, start=0):
        self.value = start

    def add(self, step=1):
        self.value += step
        return self.value


counter = Counter(10)
print(counter.add())        # 11
print(counter.add(5))       # 16
print(counter.value)        # 16
```

### 10.2 `self` 到底是什么

`self` 不是关键字，只是**惯例上的第一个参数名**。它的机制是"绑定"：

```python
class Student:
    def introduce(self):
        return f"我是 {self.name}"


student = Student()
student.name = "Alice"

print(student.introduce())        # 我是 Alice
print(Student.introduce(student))  # 我是 Alice，完全等价
```

**通过实例访问方法时，Python 把方法变成了"绑定方法"**，自动把实例塞进第一个参数：

```python
print(type(Student.introduce))     # <class 'function'>，类里就是普通函数
print(type(student.introduce))     # <class 'method'>，通过实例访问变成绑定方法
print(student.introduce.__self__ is student)   # True，绑定的就是 student
```

**这也解释了两个常见报错：**

```python
class Demo:
    def method(self, value):
        return value

demo = Demo()

# 报错一：调用时少传了参数，self 被当成了 value
# demo.method()      # TypeError: method() missing 1 required positional argument: 'value'

# 报错二：通过类名调用忘了传实例
# Demo.method(1)     # 能跑，此时 self=1，value 缺失
```

### 10.3 实例方法与类属性计数：常见写法对比

```python
class User:
    count = 0                        # 类属性，统计创建了多少个用户

    def __init__(self, name):
        self.name = name
        type(self).count += 1        # 注意用 type(self) 而不是 User


first = User("Alice")
second = User("Bob")
print(User.count)      # 2
```

**为什么写 `type(self).count += 1` 而不是 `User.count += 1`？** 因为子类继承时，`type(self)` 会指向实际调用的那个类，计数会落在正确的类上：

```python
class Admin(User):       # 继承 User
    pass

admin = Admin("Cindy")
print(Admin.count)       # 1，计数落在 Admin 上
print(User.count)        # 2，User 自己的计数没被污染
```

如果写死 `User.count += 1`，创建 `Admin` 时会把 `User.count` 也加上，统计就错了。

### 10.4 类方法、静态方法与实例方法

三种方法装饰器的区别必须分清：

| 类型 | 第一个参数 | 能访问什么 | 什么时候用 |
|:---|:---|:---|:---|
| 实例方法 | `self`（实例） | 实例属性和类属性 | 需要操作具体对象的数据 |
| 类方法 | `cls`（类） | 类属性，能创建实例 | 替代构造器、需要知道"哪个类" |
| 静态方法 | 无 | 都访问不到 | 逻辑上属于这个类，但不需要对象数据 |

```python
class User:
    count = 0

    def __init__(self, name):
        self.name = name
        type(self).count += 1

    # 实例方法：操作具体对象
    def greet(self):
        return f"你好，我是 {self.name}"

    # 类方法：常用于"另一种构造方式"
    @classmethod
    def from_text(cls, text):
        """从 "姓名,年龄" 这样的文本创建对象。"""
        name = text.strip()
        return cls(name)

    # 静态方法：纯工具函数，不依赖对象和类
    @staticmethod
    def is_valid_name(name):
        return bool(name and name.strip())


user = User.from_text(" Alice ")
print(user.greet())                 # 你好，我是 Alice
print(User.count)                   # 1

print(User.is_valid_name("Bob"))    # True
print(User.is_valid_name(""))       # False
```

**`from_text` 里用 `cls(name)` 而不是 `User(name)`**，道理和前面一样：子类调用时能创建出子类实例，而不是硬编码的父类实例。

```python
class Admin(User):
    pass

admin = Admin.from_text("Cindy")
print(type(admin).__name__)     # Admin，创建的是子类实例
```

### 10.5 继承、重写与 `super()`

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "动物叫声"

    def describe(self):
        return f"{self.name}：{self.speak()}"     # 调用会动态绑定到子类实现


class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)          # 先让父类初始化自己那部分
        self.breed = breed

    def speak(self):                     # 重写父类方法
        return "汪汪叫"


dog = Dog("旺财", "柴犬")
print(dog.speak())          # 汪汪叫
print(dog.describe())       # 旺财：汪汪叫，注意这里调用的是子类的 speak
```

**最后一行体现了多态：** `describe` 定义在父类里，但它调用 `self.speak()` 时实际执行的是子类的版本。父类不需要知道子类存在，行为却能自动扩展。

#### `super()` 的真正含义

`super()` 不是"父类对象"，而是**"在 MRO 顺序里排在当前类之后的那个类"**。单继承时看起来像父类，多继承时才能看出区别：

```python
class A:
    def who(self):
        return ["A"]

class B(A):
    def who(self):
        return ["B"] + super().who()

class C(A):
    def who(self):
        return ["C"] + super().who()

class D(B, C):              # 多继承
    def who(self):
        return ["D"] + super().who()

print(D().who())            # ['D', 'B', 'C', 'A']
print([c.__name__ for c in D.__mro__])   # ['D', 'B', 'C', 'A', 'object']
```

`D` 的 `super()` 走到了 `B`，`B` 的 `super()` 走到了 `C`（不是 `A`！），`C` 的才走到 `A`。这就是**方法解析顺序（MRO）**——合作式多继承的基础。

**实践建议：** 日常单继承占绝大多数，记住"`super()` 就是调用父类的同名方法，尤其是 `__init__`"就够用；多继承尽量用 Mixin 模式，保持每个类的 `__init__` 都调用 `super().__init__(...)`。

#### 重写时的规则

```python
class Shape:
    def area(self):
        raise NotImplementedError("子类必须实现 area")


class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2


class Square(Shape):
    def __init__(self, side):
        self.side = side

    def area(self):
        return self.side ** 2


for shape in [Circle(1), Square(2)]:
    print(round(shape.area(), 2))     # 3.14 / 4
```

**"抛 `NotImplementedError` 的父类方法"是 Python 里表达"子类必须实现"的常见手法**，它比抽象基类简单，而且错误信息明确。

### 10.6 `property`：用属性语法做校验

直接暴露属性无法校验，但写 `get_x()` / `set_x()` 又很啰嗦。`property` 让"读"和"写"都保持属性语法，同时插入逻辑：

```python
class Temperature:
    def __init__(self, celsius):
        self.celsius = celsius        # 注意：这里就会触发下面的 setter

    @property
    def celsius(self):                # getter
        return self._celsius

    @celsius.setter
    def celsius(self, value):         # setter
        if value < -273.15:
            raise ValueError("温度不能低于绝对零度")
        self._celsius = value

    @property
    def fahrenheit(self):             # 只读的计算属性
        return self._celsius * 9 / 5 + 32


t = Temperature(25)
print(t.celsius)          # 25
print(t.fahrenheit)       # 77.0，算术产生，没有存储

t.celsius = 30            # 会用属性语法，但实际执行 setter
print(t.celsius)          # 30

try:
    t.celsius = -300
except ValueError as error:
    print("校验失败：", error)     # 校验失败： 温度不能低于绝对零度
```

**三个要点：**

1. **内部存到 `self._celsius`**（带下划线），不能存回 `self.celsius`，否则 setter 会无限递归。
2. **只定义 getter 就是只读属性**，赋值会抛 `AttributeError`。
3. **`fahrenheit` 没有 setter**，所以它是只读的计算属性——这正是 `property` 相比普通属性的优势：**计算值能以属性形式暴露，而调用者看不出区别**。

### 10.7 数据类 `dataclass`

写一个"主要用于存数据"的类，需要写 `__init__`、`__repr__`、`__eq__` 一大堆样板。`@dataclass` 自动生成：

```python
from dataclasses import dataclass, field

@dataclass
class User:
    name: str
    age: int = 18                                    # 有默认值的字段要放后面
    tags: list[str] = field(default_factory=list)    # 可变默认值必须用 field


user = User("Alice", tags=["Python"])
print(user)                      # User(name='Alice', age=18, tags=['Python'])

other = User("Alice", tags=["Python"])
print(user == other)             # True，dataclass 自动生成了按字段比较的 __eq__
print(user.name, user.age)       # Alice 18，属性照常访问
```

**为什么可变默认值要用 `field(default_factory=list)`？** 这正是第 1 章讲的可变默认参数问题——直接写 `tags: list = []` 会让所有实例共用同一个列表，`dataclass` 直接禁止这种写法：

```python
from dataclasses import dataclass

# 这样写会直接报错：ValueError: mutable default <class 'list'> for field tags is not allowed
# @dataclass
# class Bad:
#     tags: list = []
```

`field` 还能控制很多细节：

```python
from dataclasses import dataclass, field

@dataclass(frozen=True)              # frozen：创建后不可修改
class Point:
    x: int
    y: int

p = Point(1, 2)
print(p)                             # Point(x=1, y=2)
print(hash(p) is not None)           # True，frozen 之后可哈希，能放进集合

points = {Point(1, 2), Point(1, 2)}
print(len(points))                   # 1，相同内容被去重
# p.x = 9                            # 报错：FrozenInstanceError
```

### 10.8 常用魔术方法

魔术方法（dunder 方法）让自定义对象支持内置语法。常用的一批：

| 魔术方法 | 触发的写法 | 作用 |
|:---|:---|:---|
| `__init__` | `Obj(...)` | 初始化 |
| `__repr__` | `repr(obj)`、交互式直接输出 | 给开发者看的表示 |
| `__str__` | `str(obj)`、`print(obj)` | 给用户看的表示 |
| `__eq__` | `obj1 == obj2` | 相等判断 |
| `__hash__` | `hash(obj)`、放进集合 | 哈希值 |
| `__len__` | `len(obj)` | 长度 |
| `__iter__` | `for x in obj` | 可迭代 |
| `__getitem__` | `obj[key]` | 下标访问 |
| `__contains__` | `x in obj` | 包含判断 |
| `__call__` | `obj(...)` | 像函数一样调用 |

```python
class Team:
    def __init__(self, members):
        self.members = list(members)

    def __len__(self):
        return len(self.members)

    def __iter__(self):
        return iter(self.members)

    def __contains__(self, member):
        return member in self.members

    def __getitem__(self, index):
        return self.members[index]

    def __repr__(self):
        return f"Team({self.members!r})"


team = Team(["Alice", "Bob"])

print(len(team))            # 2，触发了 __len__
print("Alice" in team)      # True，触发了 __contains__
print(team[0])              # Alice，触发了 __getitem__
print(list(team))           # ['Alice', 'Bob']，触发了 __iter__
print(team)                 # Team(['Alice', 'Bob'])，触发了 __repr__

for name in team:           # 因为有 __iter__ 和 __getitem__，可以直接遍历
    print(name)             # Alice / Bob
```

**`__eq__` 与 `__hash__` 必须一起改**，这是最容易出错的地方：

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented        # 交给另一方处理，而不是直接 False
        return (self.x, self.y) == (other.x, other.y)

    def __hash__(self):
        return hash((self.x, self.y))    # 用同样的字段算哈希

    def __repr__(self):
        return f"Point({self.x}, {self.y})"


print(Point(1, 2) == Point(1, 2))       # True
print(len({Point(1, 2), Point(1, 2)}))  # 1，相同内容被正确去重
```

**规则：`__eq__` 认为相等的两个对象，`__hash__` 必须返回相同的值。** 只改 `__eq__` 会导致对象在字典和集合里行为异常（同样的内容却能重复存进去）。如果对象本来就该"不可哈希"，可以写 `__hash__ = None`。

### 10.9 单下划线与双下划线

| 写法 | 含义 | 能否从外部访问 |
|:---|:---|:---|
| `name` | 公开 | 能 |
| `_name` | 内部使用，约定俗成 | 能，但表示"别碰" |
| `__name` | 触发名称改写 | 能，但名字被改写成 `_类名__name` |
| `__name__` | 系统定义的特殊名字 | 能，不要自己造 |

```python
class Account:
    def __init__(self, balance):
        self._currency = "CNY"        # 内部约定
        self.__balance = balance      # 名称改写

    def get_balance(self):
        return self.__balance


account = Account(100)
print(account._currency)              # CNY，能访问，只是约定
print(account.get_balance())          # 100

# print(account.__balance)           # 报错：AttributeError
print(account._Account__balance)      # 100，名字其实被改写成了 _Account__balance
print(account.__dict__)               # {'_currency': 'CNY', '_Account__balance': 100}
```

**双下划线是"名称改写"，不是"私有"。** 它的真实目的是避免子类意外覆盖父类的属性，而不是阻止访问。真要严格限制，应该用 `property` 或者直接不暴露。

### 10.10 本章小结

**核心模型：** 属性查找走"实例 → 类 → 父类 → object"这条链；方法通过实例访问时自动绑定 `self`；`super()` 是 MRO 的下一站。

**什么时候用什么：**

| 需求 | 用什么 |
|:---|:---|
| 每个对象独有的数据 | 实例属性（`self.x`） |
| 所有对象共享的数据 | 类属性 |
| 需要操作对象数据的方法 | 实例方法（`self`） |
| 另一种构造方式 | 类方法（`cls`）+ `@classmethod` |
| 纯工具函数 | 静态方法 + `@staticmethod` |
| 读取/赋值时要校验或计算 | `@property` |
| 主要用来存数据 | `@dataclass` |
| 想让对象支持 `len`/`in`/`[]`/`for` | 实现对应魔术方法 |

**七个坑：**

1. **给实例属性赋值不会改类属性** —— 它只是在实例上新建了一个名字。
2. **`super().__init__()` 不能忘** —— 否则父类那部分数据没初始化。
3. **`property` 内部要用另一个名字存值** —— 存回同名属性会无限递归。
4. **可变默认值要用 `field(default_factory=...)`** —— 直接写 `[]` 会被 `dataclass` 拒绝。
5. **改了 `__eq__` 就要改 `__hash__`** —— 否则集合和字典里行为异常。
6. **`__str__` 和 `__repr__` 分工不同** —— `repr` 给开发者，`str` 给用户。
7. **双下划线不是私有** —— 只是名字被改写，仍可通过 `_类名__属性` 访问。

**往下衔接：** 第 11 章会用到这些内置函数处理对象（`hasattr`、`getattr`、`vars`）；第 9 章读写 JSON 时，把对象转成字典再序列化是最常见的做法。

---


---

## 11. 常用内置函数速查

这一章按用途把常用内置函数归类，并标出容易记错的地方。

### 11.1 数值与统计

```python
numbers = [3, 1, 4, 1, 5]

print(sum(numbers))         # 14，求和
print(min(numbers))         # 1，最小值
print(max(numbers))         # 5，最大值
print(abs(-10))             # 10，绝对值
print(round(3.14159, 2))    # 3.14，四舍五入到 2 位
print(divmod(17, 5))        # (3, 2)，同时得到商和余数

# sum 可以带起始值
print(sum(numbers, 100))    # 114，从 100 开始累加
print(sum([1, 2], 10))      # 13
```

**`min` / `max` 的 `key` 参数**（第 3 章用过）可以按自定义依据取极值：

```python
scores = {"Alice": 90, "Bob": 55, "Amy": 72}

print(max(scores))                      # Bob，默认比较键（字母序）
print(max(scores, key=scores.get))      # Alice，按值比较，取出分数最高的名字
print(max(scores.values()))             # 90，直接取最大值
print(min(scores, key=scores.get))      # Bob
```

**`round` 的银行家舍入**——这是容易踩的坑：

```python
print(round(0.5))       # 0，不是 1！
print(round(1.5))       # 2
print(round(2.5))       # 2，不是 3！
print(round(3.5))       # 4

# 原因：round 用的是"四舍六入五成双"（银行家舍入），减少统计偏差
# 需要严格的四舍五入要用 decimal
from decimal import Decimal, ROUND_HALF_UP

print(Decimal("2.5").quantize(Decimal("1"), rounding=ROUND_HALF_UP))   # 3
```

### 11.2 长度、排序与反转

```python
numbers = [3, 1, 2]

print(len(numbers))                 # 3，元素个数
print(sorted(numbers))              # [1, 2, 3]，返回新列表
print(list(reversed(numbers)))      # [2, 1, 3]，reversed 返回迭代器
print(list(range(1, 6, 2)))         # [1, 3, 5]

# len 对不同类型的含义
print(len("abc"))                   # 3，字符数
print(len({"a": 1}))                # 1，键值对数量
print(len({1, 2, 3}))               # 3，元素个数
```

**`sorted` 和 `reversed` 都返回新对象，不改原数据**；对应的原地版本是 `list.sort()` 和 `list.reverse()`（第 3 章讲过）。

### 11.3 真假判断与条件筛选

```python
values = [1, 2, 0]

print(any(values))      # True，至少一个为真
print(all(values))      # False，并非全部为真
print(any([]))          # False，空序列
print(all([]))          # True，空序列（空真）

# 实用：检查是否都满足条件
numbers = [2, 4, 6]
print(all(n % 2 == 0 for n in numbers))     # True
print(any(n > 5 for n in numbers))          # True
```

### 11.4 类型转换

```python
print(int("42"))                # 42
print(int(3.99))                # 3，截断不是四舍五入
print(float("3.14"))            # 3.14
print(str(100))                 # '100'
print(bool(0))                  # False
print(list("abc"))              # ['a', 'b', 'c']
print(tuple([1, 2]))            # (1, 2)
print(set([1, 1, 2]))           # {1, 2}
print(dict([("name", "Alice")]))  # {'name': 'Alice'}
print(chr(65))                  # 'A'，编码转字符
print(ord("A"))                 # 65，字符转编码
```

**`int()` 对浮点数是截断，不是四舍五入**，这一点和 `round()` 不同：

```python
print(int(3.99))        # 3，直接砍掉小数
print(round(3.99))      # 4，四舍五入
print(int(-3.99))       # -3，向零截断（不是 -4）
```

### 11.5 对象检查与属性访问

```python
class User:
    def __init__(self, name):
        self.name = name


user = User("Alice")

print(type(user))                       # <class '__main__.User'>
print(isinstance(user, User))           # True
print(id(user) != 0)                    # True，内存地址（唯一标识）
print(hasattr(user, "name"))            # True，有没有这个属性
print(hasattr(user, "age"))             # False
print(getattr(user, "name"))            # Alice
print(getattr(user, "age", "未知"))      # 未知，给默认值

setattr(user, "age", 20)                # 动态设置属性
print(user.age)                         # 20
print(vars(user))                       # {'name': 'Alice', 'age': 20}，转成字典
```

**这几个函数是"反射"的基础**，处理不确定结构的数据时很有用：

```python
# 用 getattr 动态调用方法
class Calculator:
    def add(self, a, b):
        return a + b

    def multiply(self, a, b):
        return a * b

calc = Calculator()
operation = "add"

# 根据名字拿方法再调用
method = getattr(calc, operation)
print(method(3, 5))         # 8

# 用 vars 把对象转成字典（序列化常用）
print(vars(calc))           # {}，实例没有属性
```

### 11.6 遍历配对与索引

```python
names = ["Alice", "Bob"]
scores = [90, 85]

print(list(enumerate(names)))               # [(0, 'Alice'), (1, 'Bob')]
print(list(enumerate(names, start=1)))      # [(1, 'Alice'), (2, 'Bob')]
print(list(zip(names, scores)))             # [('Alice', 90), ('Bob', 85)]
print(list(zip(*[names, scores])))          # [('Alice', 'Bob'), (90, 85)]，转置
```

### 11.7 `map` / `filter` / `functools.reduce`

```python
from functools import reduce

numbers = [1, 2, 3, 4]

print(list(map(lambda n: n * 2, numbers)))          # [2, 4, 6, 8]
print(list(filter(lambda n: n % 2 == 0, numbers)))  # [2, 4]
print(reduce(lambda a, b: a + b, numbers))          # 10，累积求和
print(reduce(lambda a, b: a * b, numbers, 1))       # 24，带初始值
```

**`reduce` 现在不推荐常用**，`sum`、`max`、`min` 或显式循环更清楚：

```python
# reduce 能做的，通常有更清楚的写法
print(sum(numbers))                     # 10，比 reduce 清楚
print(max(numbers))                     # 4
```

### 11.8 生成序列与组合

```python
from itertools import combinations, permutations, product, chain

items = ["A", "B", "C"]

print(list(combinations(items, 2)))     # [('A', 'B'), ('A', 'C'), ('B', 'C')]，组合
print(list(permutations(items, 2)))     # 6 种排列（有序）
print(list(product([1, 2], ["a", "b"])))  # 笛卡尔积
print(list(chain([1, 2], [3, 4])))      # [1, 2, 3, 4]，串联多个可迭代对象
```

**这些函数返回的都是迭代器**，需要列表时用 `list()` 转换。

### 11.9 常用高阶函数

```python
# zip 的 strict 参数（3.10+）
print(list(zip([1, 2], ["a", "b"], strict=True)))   # [(1, 'a'), (2, 'b')]

# sorted 的多种用法
words = ["banana", "apple", "fig"]
print(sorted(words))                        # 字母序
print(sorted(words, key=len))               # 按长度：['fig', 'apple', 'banana']
print(sorted(words, key=len, reverse=True))  # 长度降序
```

### 11.10 本章小结

| 用途 | 函数 |
|:---|:---|
| 求和、极值、绝对值 | `sum`、`min`、`max`、`abs` |
| 商和余数 | `divmod(a, b)` |
| 四舍五入 | `round(x, n)`（注意银行家舍入） |
| 长度 | `len(x)` |
| 排序 / 反转（不改原数据） | `sorted(x)`、`reversed(x)` |
| 真假判断 | `any(x)`、`all(x)` |
| 类型转换 | `int`、`float`、`str`、`bool`、`list`、`tuple`、`set`、`dict` |
| 编码与字符 | `chr(n)`、`ord(c)` |
| 对象反射 | `type`、`isinstance`、`hasattr`、`getattr`、`setattr`、`vars` |
| 遍历配对 | `enumerate`、`zip` |
| 映射与筛选 | `map`、`filter` |
| 累积 | `functools.reduce` |
| 组合与排列 | `itertools.combinations`、`permutations`、`product`、`chain` |

**四个容易记错的点：**

| 函数 | 容易记错的地方 |
|:---|:---|
| `round` | 银行家舍入，`round(2.5)` 是 `2` 不是 `3` |
| `int` | 对小数是截断（向零），不是四舍五入 |
| `all([])` | 返回 `True`（空真），别把空数据当成"不合格" |
| `map`/`filter`/`reversed`/`zip` | 返回迭代器，只能走一遍 |

**收尾：** 这一章是"工具箱"，建议配合第 12 章的一页式索引一起复习——先看索引定位到具体函数，再回到对应章节看用法和坑。

---

## 12. 一页式语法索引

| 想做什么 | 常用写法 |
|:---|:---|
| 判断为空 | `if not value:` |
| 判断为 `None` | `if value is None:` |
| 安全读取字典 | `mapping.get(key, default)` |
| 带索引遍历 | `for i, value in enumerate(items):` |
| 同时遍历多个序列 | `for a, b in zip(left, right):` |
| 按规则排序 | `sorted(items, key=..., reverse=True)` |
| 展开序列参数 | `function(*items)` |
| 展开字典参数 | `function(**options)` |
| 合并字典 | `merged = left \| right` |
| 保留剩余元素 | `first, *rest = items` |
| 快速构建列表 | `[f(x) for x in items if condition]` |
| 按需产生数据 | `yield value` |
| 捕获指定异常 | `except ValueError as error:` |
| 自动释放资源 | `with resource as value:` |
| 构造文件路径 | `Path(base) / "file.txt"` |
| 包内相对导入 | `from .module import name` |
| 模块方式运行 | `python -m package.module` |
| 定义数据类 | `@dataclass` |

> 复习时先查本页索引，再跳到对应章节查看最小示例即可。
