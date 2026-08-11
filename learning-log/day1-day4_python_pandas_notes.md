# Python + pandas 三天学习笔记

> 学习范围：Day 1—Day 4（Day 3 与 Day 4 在第三个学习日合并进行）  
> 真实项目：读取并检查《8班分班成绩.xlsx》

## 一、三天学习路线

这三天不是按“背 Python 语法”的方式推进，而是逐步建立一条数据分析主线：

```text
现实问题
→ 把数据组织成表
→ 查看表的结构
→ 按条件筛选对象
→ 检查缺失、重复和异常
→ 排序并为分组统计做准备
```

目前已经走通：

- Python 中变量、列表、字典和括号的基础含义；
- pandas、Series、DataFrame 的基本认识；
- 行、列、索引和数据类型；
- 条件判断与布尔筛选；
- `loc` 的“选行 + 选列”结构；
- 真实 Excel 的读取与结构检查；
- 缺失值、重复值、逻辑矛盾检查；
- 单条件和多条件筛选；
- 按成绩排序与重设索引。

尚未正式完成：

- `groupby()` 分组统计；
- 均值、中位数等描述统计；
- matplotlib / pandas 简单可视化。

这些留作下一次课程继续。

---

## 二、Day 1：Python 的基础结构

### 1. 变量是“给数据取名字”

```python
students = ["小明", "小红"]
```

- `students`：变量名；
- `=`：赋值，把右边的数据放进左边的变量；
- `students` 不是固定关键词，也可以换成其他合理名称。

要区分：

```python
x = 10       # 赋值：把 10 保存给 x
x == 10      # 判断：x 是否等于 10
```

一个等号 `=` 是赋值，两个等号 `==` 是比较。

### 2. 三种括号的基本模型

#### 方括号 `[]`

可以创建列表，也可以从对象中取数据：

```python
students = ["小明", "小红"]
students[0]
df["数学"]
```

可先理解为：

> 方括号既可以装一组东西，也可以从一个结构里取东西。

#### 大括号 `{}`

常用于字典 `dict`，保存“键 → 值”的对应关系：

```python
student = {
    "姓名": "小明",
    "数学": 80
}
```

#### 小括号 `()`

常用于调用功能：

```python
print("Hello")
df.head()
df.info()
```

有括号通常表示“请执行这个功能”。

### 3. 常见数据类型

```text
int / int64       整数
float / float64   小数
str / string      文字
bool              True / False
```

`float64` 中的 `64` 表示通常使用 64 个二进制位保存数值，不是“64 个小数位”。

### 4. 下划线 `_`

Mac 输入方法：`Shift + -`。

下划线常用于连接多个英文单词：

```python
student_name
math_score
drop_duplicates
```

这种命名方式叫 snake_case（蛇形命名法）。

---

## 三、Day 2：认识 pandas 与 DataFrame

### 1. 导入 pandas

```python
import pandas as pd
```

- `pandas` 是处理表格数据的 Python 工具库；
- `as pd` 给 pandas 取一个常用短名；
- 后面可以通过 `pd` 调用 pandas 的功能。

### 2. 从字典创建 DataFrame

```python
data = {
    "姓名": ["小明", "小红", "小李"],
    "数学": [80, 95, 60],
    "英语": [70, 90, 95]
}

df = pd.DataFrame(data)
```

这里完成了转换：

```text
字典 data
→ pd.DataFrame(data)
→ 二维表 df
```

`df` 只是 DataFrame 的习惯缩写，不是 Python 强制规定的名称。

### 3. 行和列

最重要的认知模型：

```text
一行 = 一个对象 / 一条记录
一列 = 对象的一种属性
```

在学生表中：

- 一个学生占一行；
- 姓名、班级、性别、数学、英语等分别是列。

增加一个学生，是增加行；增加一个科目，是增加列。

### 4. 查看表格结构

```python
df.shape
```

返回：

```text
(行数, 列数)
```

口诀：`shape` 先看高，再看宽；先行，后列。

```python
df.columns
```

查看所有列名。

```python
df.head()
```

默认查看前 5 行。

### 5. 属性与方法

```python
df.shape       # 属性，没有括号
df.columns     # 属性，没有括号
df.head()      # 方法/功能，有括号
df.info()      # 方法/功能，有括号
```

可先记为：

> 没括号通常是读取已有信息；有括号通常是执行一个动作。

### 6. 取列与按条件筛行

```python
df["数学"]
```

取得数学这一列。

```python
df["数学"] < 70
```

对数学列逐行判断，返回一串布尔值：

```text
False
False
True
```

```python
df[df["数学"] < 70]
```

保留条件为 `True` 的整行。

核心区别：

```text
df["列名"]   → 取列
df[条件]     → 筛行
```

### 7. `loc`：行条件 + 列选择

```python
df.loc[df["数学"] < 70, ["姓名", "数学"]]
```

固定结构：

```text
df.loc[哪些行, 哪些列]
```

- 逗号左边决定保留哪些对象；
- 逗号右边决定显示对象的哪些属性。

### 8. 脏数据四类问题

#### 类型不一致

```text
80
"85"
"缺考"
```

可以尝试转为数字：

```python
df["数学"] = pd.to_numeric(df["数学"], errors="coerce")
```

`errors="coerce"` 表示无法转换的内容变成 `NaN`。

#### 缺失值

```python
df.isna()
df.isna().sum()
```

- `isna()`：每个格子是否为空；
- `sum()`：按列统计 `True` 的数量。

#### 重复值

```python
df.duplicated()
df.duplicated().sum()
df.drop_duplicates()
```

删除前必须先定义“什么叫重复”，不能看到重复就直接删。

#### 异常值

```python
df[df["数学"] > 150]
```

异常值需要结合业务规则判断。它可能是录入错误，也可能是真实但极端的数据。

---

## 四、Day 3：读取并检查真实 Excel

### 1. 启动 JupyterLab

终端中可运行：

```bash
python3 -m jupyter lab
```

如果当前 Jupyter 内核没有 pandas，可在 Notebook 代码格中运行：

```python
%pip install pandas openpyxl
```

安装后重启 Kernel。

### 2. 先检查工作表名称

```python
import pandas as pd

file_path = "8班分班成绩.xlsx"

excel = pd.ExcelFile(file_path)
print(excel.sheet_names)
```

结果：

```text
['Sheet1']
```

使用 `pd.ExcelFile()` 的原因是先确认文件中有哪些工作表，避免默认读取错误的表。

### 3. 读取工作表

```python
df = pd.read_excel(file_path, sheet_name="Sheet1")
```

结构：

```text
pd.read_excel(文件位置, sheet_name=工作表名称)
```

`sheet_name` 是函数的参数名，不是另一个 DataFrame。

### 4. 查看真实文件结构

```python
print(df.shape)
print(df.columns.tolist())
df.head()
df.info()
```

真实文件检查结果：

```text
76 行 × 11 列
```

列名包括：

```text
新班级、原班级、姓名、性别、赋分总分、
语文、数学、英语、是否住宿、宿舍号、教室
```

`df.info()` 会显示：

- 行数与索引范围；
- 列名；
- 每列的非空数量；
- 每列的数据类型；
- 内存占用。

当前新版 pandas 把文字列显示为 `str`；旧教程中可能显示 `object`。

### 5. 真实文件的缺失值

```python
df.isna().sum()
```

检查到：

```text
语文       1
数学       3
英语       3
宿舍号    45
教室       1
```

重要判断：宿舍号缺失不一定是错误。非住宿生没有宿舍号是合理空白。

因此数据清洗不是：

```text
发现 NaN → 全部删除或填 0
```

而是：

```text
发现 NaN → 理解业务含义 → 决定处理方法
```

### 6. 找到具体缺失记录

```python
df[df["数学"].isna()]
```

只显示需要的列：

```python
math_missing = df.loc[
    df["数学"].isna(),
    ["姓名", "数学"]
]
```

```python
math_missing.shape
```

返回：

```text
(3, 2)
```

注意：表头不算数据行，左侧 index 不算数据列。

### 7. 变量名与结果展示

```python
result = 某个筛选结果
```

等号右边先计算，再把结果保存到左边的变量。

在 Jupyter 中：

```python
result
```

会以较美观的表格展示。

```python
print(result)
```

会以普通文本格式展示。二者都不会修改原数据。

### 8. 检查重复值

整行重复：

```python
df.duplicated().sum()
```

结果为 0。

只按姓名检查：

```python
df.duplicated(subset="姓名").sum()
```

结果也为 0。

`subset` 的含义是“只选部分列作为重复判断依据”。

### 9. 多条件逻辑检查

```python
df.loc[
    (df["是否住宿"] == "否") & (df["宿舍号"].notna()),
    ["姓名", "是否住宿", "宿舍号"]
]
```

含义：找出标记为非住宿、但宿舍号不为空的记录。

```python
df.loc[
    (df["是否住宿"] == "是") & (df["宿舍号"].isna()),
    ["姓名", "是否住宿", "宿舍号"]
]
```

含义：找出标记为住宿、但宿舍号为空的记录。真实结果为空表，说明没有记录同时满足这两个条件。

关键符号：

```text
&          并且，两个条件同时成立
|          或者，满足至少一个条件
isna()     是空值
notna()    不是空值
```

多个 pandas 条件分别加圆括号：

```python
(条件一) & (条件二)
```

---

## 五、Day 4：排序与索引

### 1. 按某列排序

```python
math_sorted = df.sort_values(by="数学", ascending=False)
```

结构：

```text
对象.功能(参数名=参数值)
```

对应：

```text
df               要处理的表
sort_values      排序功能
by="数学"        按数学列排序
ascending=False  不升序，即从高到低
```

查看前五行：

```python
math_sorted.head(5)
```

### 2. 为什么 `by="数学"` 不写成 `df["数学"]`

```python
df["数学"]
```

是在 DataFrame 中直接取出一列。

```python
df.sort_values(by="数学")
```

则是在给排序功能传入参数：“请按名为数学的列排序”。

### 3. index 是什么

index 是每一行的行标签。默认通常是：

```text
0、1、2、3……
```

它不是成绩排名，也不是普通数据列。

排序只改变行的顺序，不自动改变原来的 index。可以理解为学生重新排队，但手里的原号码牌没变。

### 4. 重设索引

```python
math_sorted_reset = math_sorted.reset_index(drop=True)
```

- `reset_index()`：按当前顺序重新编号；
- `drop=True`：丢弃旧索引，不把旧索引保存成新的一列；
- 默认生成新表，不直接修改原表，因此通常要赋值给新变量。

---

## 六、这三天最重要的思维模型

### 模型 1：先理解结构，再处理数据

```text
shape     表有多大
columns   有哪些字段
head()    前几行是什么
info()    类型和非空数量
```

### 模型 2：行是对象，列是属性

```text
找哪些学生 → 筛行
看哪些信息 → 选列
loc        → 同时控制行和列
```

### 模型 3：布尔条件就是逐行回答“是或否”

```python
df["数学"] < 70
```

得到 `True / False`，再让外层 DataFrame 保留 `True` 对应的行。

### 模型 4：清洗不是机械删除

缺失、重复、异常都必须结合现实含义判断：

- 缺失成绩不等于 0 分；
- 非住宿生缺少宿舍号可能完全合理；
- 极低分可能是缺考，也可能是真实低分；
- 看似重复的记录可能来自不同考试。

### 模型 5：代码从右向左完成赋值

```python
new_table = df.loc[条件, 列]
```

先执行右边的筛选，再把结果保存到左边的新变量。

---

## 七、常见错误记录

### 1. 中文全角符号

错误：

```text
（ ）
```

正确：

```text
( )
```

Python 代码尽量切换到英文输入法再输入标点。

### 2. 参数放错括号位置

错误：

```python
pd.read_excel(file_path, sheet_name) = "Sheet1"
```

正确：

```python
pd.read_excel(file_path, sheet_name="Sheet1")
```

### 3. 单复数或方法名拼写

```text
column      → columns
duplicate   → duplicated
```

### 4. 把字符串放进错误的方括号

比较文字时：

```python
df["是否住宿"] == "否"
```

`"否"` 是一个字符串，不是列名列表。

### 5. `notna()` 的位置

```python
df["宿舍号"].notna()
```

先取宿舍号这一列，再调用非空检查。

### 6. 多条件缺少括号

```python
(条件一) & (条件二)
```

每个完整条件分别加圆括号，再用 `&` 连接。

---

## 八、复习练习（暂不附答案）

1. 查看 `df` 的行数与列数。
2. 找出语文成绩为空的学生，只显示姓名和语文。
3. 检查原班级与姓名组合是否存在重复。
4. 找出“是否住宿”为“否”且宿舍号不为空的记录。
5. 按英语从高到低排序，保存为 `english_sorted`。
6. 将 `english_sorted` 重设索引并显示前 10 行。
7. 解释为什么 `NaN` 和 `0` 不能直接当作一回事。
8. 解释 `df["数学"]` 与 `df.loc[条件, ["姓名", "数学"]]` 的区别。

---

## 九、下一次学习计划

下一次从 Day 4 未完成部分继续：

```text
groupby() 分组
→ count() 人数统计
→ mean() 平均数
→ median() 中位数
→ describe() 描述统计
→ 简单柱状图 / 直方图
```

建议继续保持新的训练方式：

1. 先给任务，不直接给完整代码；
2. 自己写第一版；
3. 根据报错定位问题；
4. 第二次仍卡住，再逐层获得提示；
5. 最后整理成可复用的代码模板。

这会减少“看着会、自己写不出”的错觉，把理解真正转化成独立操作能力。
