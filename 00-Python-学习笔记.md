# 纪旭 Python学习记录
日期：2026-06-16～17 | 学员：纪旭，18岁，2008年生，零基础小白

---

## 环境
- Python 3.13.14 (64-bit) ✅
- VS Code ✅
- 文件夹：C:\Users\40315\Desktop\python学习\
- 运行命令：`python3 你的文件名.py`
- ✅ python3别名已永久生效！（写进了 $PROFILE 配置文件，每次开终端自动加载，不用再手动设置）
- 如果以后又失效：检查 `Get-Content $PROFILE` 里有没有那行Set-Alias，并确认执行过 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

---

## 已掌握知识点

### print()
```python
print('hello, world')
print('我的名字是纪旭')
```

### 变量
```python
name = '纪旭'
print('我的名字是' + name)
```

### 数据类型
```python
name = '纪旭'      # str 文字
age = 18           # int 整数
height = 1.75      # float 小数
is_student = True  # bool 是/否
print(type(name))  # → <class 'str'>
```

### 数字计算
```python
age = 18
year = 2026
birth_year = year - age  # → 2008
print(f'我今年{age}岁')
print(f'我出生于{birth_year}年')
```

### 字符串方法
```python
name = '纪旭'
len(name)          # → 2
'hello'.upper()    # → HELLO
'HELLO'.lower()    # → hello
'  你好  '.strip() # → 你好
```

### f-string（推荐写法）
```python
age = 19
print(f'生日后年龄：{age}')  # {} 是占位符，填入变量值
# 注意：()是函数入口，{}是f-string占位符，[]是列表/字典取值
```

### list 列表
```python
fruits = ['苹果', '香蕉', '橙子', '西瓜']
fruits[0]              # → 苹果（从0开始数！）
len(fruits)            # → 4
fruits.append('葡萄')  # 末尾添加
```

### tuple 元组
```python
seasons = ('春', '夏', '秋', '冬')
seasons[0]           # → 春
seasons.append('?')  # ❌ 报错！tuple不能修改
# list用[] 可修改；tuple用() 不可修改
```

### if 条件判断
```python
age = 15
if age < 6:
    print('幼儿')
elif age < 18:
    print('青少年')  # ← 15岁走这里
elif age < 60:
    print('成年人')
else:
    print('老年人')
```

### for 循环
```python
# 数字循环
for i in range(1, 6):  # 左闭右开，输出1~5
    print(i)

# 列表循环
fruits = ['苹果', '香蕉', '橙子', '西瓜']
for fruit in fruits:
    print('我喜欢吃' + fruit)
```

### dict 字典
```python
person = {'name': '纪旭', 'age': 18, 'city': '北京'}
person['name']          # → 纪旭（用名字取值，不用编号）
person['age'] = 19      # 修改值
person['hobby'] = 'Python'  # 新增键
# list适合同类数据；dict适合一个对象的多种属性
```

---

### input 输入（4.2）
```python
# 基本用法
name = input('请输入你的名字：')
print(f'你好，{name}！欢迎学习Python！')

# ⚠️ 重要：input收到的永远是文字（str）！
# 要做数学运算必须先用int()转换
age = int(input('请输入你的年龄：'))
print(f'明年你{age + 1}岁')
```
> input → 永远是str → 要算数先套int()

---

### 内置函数（6.1）
```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
max(numbers)     # → 9 最大值
min(numbers)     # → 1 最小值
sum(numbers)     # → 31 求和
sorted(numbers)  # → [1,1,2,3,4,5,6,9] 排序
# 之前用过的内置函数：print() len() int() str() type() input()
```

---

### 定义函数（6.2）
```python
# def = define，定义函数
def greet():
    print('你好！')
    print('欢迎学习Python！')

greet()  # 调用函数，不调用就不执行！
greet()  # 可以多次调用
```
> 定义函数 = 教会助手怎么做；调用函数 = 真正叫助手去做

---

### 函数参数（6.3）
```python
# 普通参数
def greet(name):
    print(f'你好，{name}！')

greet('纪旭')   # → 你好，纪旭！
greet('小明')   # → 你好，小明！

# 多个参数
def introduce(name, age, city):
    print(f'我叫{name}，今年{age}岁，来自{city}。')

# 默认参数（调用时可以不传，自动用默认值）
def introduce(name, age, city='北京'):
    print(f'我叫{name}，今年{age}岁，来自{city}。')

introduce('纪旭', 18)          # → 来自北京（用默认值）
introduce('小明', 20, '上海')  # → 来自上海（用传入值）

# return 返回值（把结果递出来给外面用）
def add(a, b):
    result = a + b
    return result

total = add(3, 5)        # → total = 8
print(add(10, 20))       # → 直接用返回值：30
```
> 没有return = 助手做完走了，结果消失；有return = 助手把结果递给你

---

### 递归函数（6.4）了解即可
```python
# 递归 = 函数自己调用自己，把大问题拆成小问题
def factorial(n):
    if n == 1:        # 终点条件，必须有！不然永远循环
        return 1
    return n * factorial(n - 1)

factorial(5)  # → 120（5×4×3×2×1）
```
> 现阶段几乎用不到，知道概念即可

---

### 调用AI的API（核心！）
```python
from openai import OpenAI
# from 工具箱名 import 工具名
# = 从openai工具箱里，取出OpenAI这个工具

client = OpenAI(
    api_key="你的API Key",        # 身份证明，不能给别人看！
    base_url="https://api.deepseek.com"  # 去DeepSeek，不是OpenAI
)
# client = 快递员，知道去哪、用谁的账号

response = client.chat.completions.create(
    model="deepseek-chat",         # 用哪个AI模型
    messages=[
        {"role": "user", "content": "你想问的问题写这里"}
        # role: user = 这句话是用户说的
        # content = 具体说的内容
    ]
)

print(response.choices[0].message.content)
# response = AI返回的整个包裹
# .choices[0] = 取第一个回答
# .message.content = 取出文字内容
```

---

## 🤖 五大AI使用模板（核心技能！）

> 比喻：你开了一家"文案代写公司"
> 你=老板 / client=快递员 / DeepSeek=写手 / txt文件=客户清单和成品文件夹

### 模板1：基础对话（已掌握）
就是上面调用AI的API，发一个问题得到一个回答。

---

### 模板2：批量处理（最实用！）
> 手动一条条复制 vs 写一次代码自动处理N条

```python
from openai import OpenAI
client = OpenAI(api_key="你的Key", base_url="https://api.deepseek.com")

# ① 换成你的数据
data = ["第1条", "第2条", "第3条"]

for item in data:
    response = client.chat.completions.create(
        model="deepseek-chat",
        messages=[
            {"role": "user", "content": f"② 你的指令：{item}"}
        ]
    )
    result = response.choices[0].message.content
    print(f"{item} => {result}")
    print("---")
```
**每次只改两处：① data数据  ② 给AI的指令**

练习过：批量翻译、批量生成小红书标题、批量情感分析

⭐ 经验：指令越精确AI越听话！关键词「只输出XXX、不要解释、不要标点、不超过XX字」

---

### 模板3：读取文件
> 数据存在txt里，让Python自动读出来，不用手动复制

```python
# 读取txt，每行是一条数据
with open('你的文件.txt', 'r', encoding='utf-8') as f:
    lines = f.readlines()

# 清理空行和换行符
data = [line.strip() for line in lines if line.strip()]

# 然后接批量处理模板
for item in data:
    ...
```
- `'r'` = read 只读模式
- `readlines()` = 把每行读出来
- `strip()` = 去掉两边空格和换行符
- `with open` = 用完自动关闭文件（自动关门的房间）

---

### 模板4：保存结果
> AI处理完自动写进文件，永久保存随时能用

```python
# 'w' = write 写入模式（会清空原内容重写）
with open('results.txt', 'w', encoding='utf-8') as result_file:
    for item in data:
        result = # ...AI处理...
        result_file.write(f"内容：{result}\n")  # \n 是换行，必须加！
```

文件模式对照：
| 模式 | 含义 | 作用 |
|---|---|---|
| `'r'` | read 读取 | 读文件内容 |
| `'w'` | write 写入 | 写入（清空原内容重写）|
| `'a'` | append 追加 | 末尾添加（不清空）|

完整工具链：📄读取文件 → 🔄批量处理 → 🤖AI生成 → 💾保存结果

---

### 模板5：多轮对话（让AI记住上下文）
> 关键：把历史对话也一起发给AI，它才记得住

```python
from openai import OpenAI
client = OpenAI(api_key="你的Key", base_url="https://api.deepseek.com")

messages = []  # 用列表保存所有对话历史

print("开始聊天吧！（输入 退出 结束）")

while True:  # 一直循环，直到输入"退出"
    user_input = input("你：")
    if user_input == "退出":
        break  # 跳出循环

    # 把你的话加进历史
    messages.append({"role": "user", "content": user_input})

    # 把完整历史发给AI（关键！这样AI才记得住）
    response = client.chat.completions.create(
        model="deepseek-chat",
        messages=messages
    )
    reply = response.choices[0].message.content

    # 把AI的回答也加进历史
    messages.append({"role": "assistant", "content": reply})

    print(f"AI：{reply}")
```
- `while True` = 一直重复
- `break` = 跳出循环
- 关键：每轮把 user 和 assistant 都 append 进 messages，AI就能看到完整历史

---

## 学习进度
- ✅ 4.1 第一个Python程序
- ✅ 4.2 输入和输出
- ✅ 5.1 数据类型和变量
- ✅ 5.2 字符串和编码
- ✅ 5.3 list和tuple
- ✅ 5.4 条件判断
- ⏭️ 5.5 模式匹配（跳过）
- ✅ 5.6 循环
- ✅ 5.7 dict和set
- ✅ 6.1 调用函数
- ✅ 6.2 定义函数
- ✅ 6.3 函数的参数
- ✅ 6.4 递归函数（了解概念即可）
- ✅ 用Python成功调用DeepSeek API！

---

## 🤖 五大AI模板进度
- ✅ 模板1：基础对话
- ✅ 模板2：批量处理（翻译、生成标题、情感分析）
- ✅ 模板3：读取文件
- ✅ 模板4：保存结果
- ✅ 模板5：多轮对话
- ✅ python3别名永久生效问题已解决

> 掌握这5个模板，日常90%的AI使用场景都能覆盖！

---

## 重要笔记
- 括号区别：`()`函数入口 / `{}`f-string占位符或字典 / `[]`列表或取值
- 中文括号`（）`会报SyntaxError，必须用英文括号`()`
- 数字+文字拼接需要str()转换，或用f-string
- tuple创建后不可修改，list可以
- 列表编号从0开始，不是1
- range(1,6)输出1~5，左闭右开
- str类型不能直接和int用+拼接，需要str()或f-string
- input()收到的永远是str，要算数先用int()转换
- 定义函数不会自动执行，必须调用才执行
- API Key = 身份证明，绝对不能发给任何人！
- import = 我要用，我来取（站在自己程序的角度）
- 调用API不需要从零写，会改模板、能跑起来就够了

---

## 工具安装记录
- `pip install openai` ✅（用于调用DeepSeek API）
- DeepSeek API Key ✅（保存在本地，不要外泄）

---

## 我的目标程度（不需要更多）
- ✅ 看得懂别人/AI写的代码
- ✅ 会改代码里的关键内容
- ✅ 遇到报错能描述问题、会求助
- ✅ 能跑起来、能用起来
- ❌ 不需要从零写出复杂程序（AI会帮你写）
