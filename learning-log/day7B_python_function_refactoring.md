# Day 7B · Python 函数化重构与报告保存

日期：2026-08-19

今天把前几天的英语学情分析从“一整段顺序代码”重构成多个各负其责的函数，并把最终报告保存为文本文件。

> 成绩数据 → 计算平均分 → 生成教学建议 → 生成图表解读 → 组合报告 → 保存文件

本笔记不包含学生姓名或个人成绩记录，只记录汇总分析和学习过程。

## 一、今天完成的内容

- 理解函数的基本结构：输入、处理、输出
- 使用 `def` 定义函数
- 理解参数名只是函数内部的代称
- 区分 `return` 和 `print`
- 使用多个参数向函数传入不同材料
- 使用 f-string 把变量值插入文字
- 使用 `+` 和 `"\n\n"` 拼接多段字符串
- 将顺序代码拆成 5 个单一职责函数
- 使用 `with open()` 和 UTF-8 编码保存中文报告
- 建立“模块之间传递数据”的程序设计思维

## 二、函数的基本结构

函数可以理解成一个小工具：

```text
输入
 ↓
处理
 ↓
输出
```

Python 中的基本写法：

```python
def 函数名(参数):
    处理过程
    return 结果
```

例如，计算一组成绩的平均分：

```python
def calculate_mean(scores):
    mean_score = round(scores.mean(), 1)
    return mean_score
```

调用函数：

```python
mean_score = calculate_mean(english_scores)
```

这句话的过程是：

```text
english_scores
      ↓
calculate_mean()
      ↓
返回平均分
      ↓
mean_score 接住结果
```

## 三、参数名只是函数内部的代称

在下面的函数定义中：

```python
def calculate_mean(scores):
    mean_score = round(scores.mean(), 1)
    return mean_score
```

`scores` 不是固定写法，也不是 Python 关键字。它只是函数内部给传入数据起的名字。

调用：

```python
calculate_mean(english_scores)
```

进入函数后，可以这样理解：

```text
函数外部的名字：english_scores
函数内部的名字：scores

它们指向本次传入的同一组数据。
```

参数也可以写成 `data` 或 `x`，但 `scores` 更容易让人看懂它表示成绩数据。变量名主要是给人阅读代码用的。

## 四、return 和 print 的区别

### `return`：把结果交给程序继续使用

```python
def calculate_mean(scores):
    mean_score = round(scores.mean(), 1)
    return mean_score
```

`return` 会把平均分送回函数调用的位置，后面的代码还能继续使用这个结果：

```python
mean_score = calculate_mean(english_scores)
advice = generate_advice(mean_score)
```

### `print`：把内容展示给人看

```python
print(report)
```

`print` 主要负责在屏幕上显示内容，不负责把结果交给下一个模块。

可以这样记：

```text
return → 模块之间传递数据
print  → 最后把内容展示给人
```

## 五、函数可以接收多个参数

生成报告时需要平均分、教学建议和图表解读三个材料，因此函数可以定义多个参数：

```python
def generate_report(mean_score, advice, chart_summary):
    report = (
        f"平均分：{mean_score}分\n"
        f"教学建议：{advice}\n\n"
        f"图表解读：{chart_summary}"
    )
    return report
```

调用：

```python
report = generate_report(
    mean_score,
    advice,
    chart_summary
)
```

参数按位置依次传入，所以调用时的顺序要与函数定义保持一致。

## 六、f-string 和大括号

```python
report = f"平均分：{mean_score}分"
```

- 字符串前面的 `f` 表示这是一段格式化字符串。
- 大括号 `{}` 是变量或表达式的插槽。
- `{mean_score}` 会被替换成变量保存的实际值。

假设：

```python
mean_score = 40.1
```

那么结果是：

```text
平均分：40.1分
```

如果不写大括号：

```python
f"平均分：mean_score分"
```

Python 会把 `mean_score` 当成普通文字，不会自动读取变量值。

## 七、字符串拼接和换行

字符串可以使用 `+` 连接：

```python
updated_report = full_report + "\n\n" + chart_summary
```

其中：

- `+`：连接多段字符串
- `\n`：换一行
- `\n\n`：换两行，中间留一个空行

例如：

```python
a = "英语成绩"
b = "分析报告"

title = a + b
```

结果：

```text
英语成绩分析报告
```

## 八、从顺序代码到函数化结构

重构前，所有步骤写在一条长流程中：

```text
读取成绩
→ 计算平均分
→ 判断教学建议
→ 生成图表解读
→ 生成报告
→ 保存文件
```

重构后，每个函数只负责一件事：

```text
calculate_mean()
        ↓
generate_advice()
        ↓
generate_chart_summary()
        ↓
generate_report()
        ↓
save_report()
```

这样做不是单纯为了减少代码，而是让每个模块的输入、处理和输出都更清楚。以后增加数学分析、多个班级或多次考试时，可以复用或替换其中某个模块。

## 九、完整函数化代码

```python
# 1. 计算平均分
def calculate_mean(scores):
    mean_score = round(scores.mean(), 1)
    return mean_score


# 2. 根据平均分生成教学建议
def generate_advice(mean_score):
    if mean_score < 60:
        advice = "基础薄弱，需要加强基础训练"
    else:
        advice = "继续保持"

    return advice


# 3. 生成图表解读
def generate_chart_summary(mean_score):
    chart_summary = (
        "本班英语成绩主要集中在30—50分，"
        f"平均分为{mean_score}分，"
        "90分及格线右侧没有学生。"
    )
    return chart_summary


# 4. 组合完整报告
def generate_report(mean_score, advice, chart_summary):
    report = (
        f"平均分：{mean_score}分\n"
        f"教学建议：{advice}\n\n"
        f"图表解读：{chart_summary}"
    )
    return report


# 5. 保存报告
def save_report(report):
    with open(
        "英语学情报告.txt",
        "w",
        encoding="utf-8"
    ) as file:
        file.write(report)


# 主程序：让数据按顺序经过各个函数
english_scores = valid_df["英语"].dropna()

mean_score = calculate_mean(english_scores)
advice = generate_advice(mean_score)
chart_summary = generate_chart_summary(mean_score)
report = generate_report(mean_score, advice, chart_summary)

save_report(report)

print(report)
print("报告保存成功")
```

主程序的数据流：

```text
english_scores
      ↓
calculate_mean()
      ↓
mean_score
      ↓
generate_advice()
      ↓
advice
      ↓
generate_chart_summary()
      ↓
chart_summary
      ↓
generate_report()
      ↓
report
      ↓
save_report()
      ↓
英语学情报告.txt
```

## 十、文件保存与 UTF-8

```python
with open(
    "英语学情报告.txt",
    "w",
    encoding="utf-8"
) as file:
    file.write(report)
```

关键理解：

- `open()`：打开或创建文件。
- `"w"`：写入模式；同名文件存在时会覆盖原内容。
- `encoding="utf-8"`：指定保存和读取文字时使用的字符编码规则。
- `as file`：给打开的文件对象起一个临时名字。
- `file.write(report)`：把报告内容写入文件。
- `with` 代码块结束后，文件会自动关闭。

`UTF-8` 不是“中文”的意思，而是一种通用字符编码。它支持中文、英文、数字、emoji 和许多其他语言。明确指定 UTF-8 可以减少在不同电脑上出现乱码的风险。

## 十一、今天建立的程序思维

以前更关注：

> 这一行代码是什么意思？

今天开始关注：

> 这个模块输入什么、输出什么，以及它怎样与下一步连接？

这是从“写出能运行的代码”向“设计程序结构”迈出的一步。

## Day 7B 小结

今天已经能够完成：

```text
定义函数
→ 传入参数
→ 在函数内部处理
→ 使用 return 返回结果
→ 把结果传给下一个函数
→ 组合完整报告
→ 使用 UTF-8 保存文本文件
```

下一次继续：文件、路径和异常处理，让程序在文件不存在、列名写错或缺少“英语”列时，也能给出清楚的提示。
