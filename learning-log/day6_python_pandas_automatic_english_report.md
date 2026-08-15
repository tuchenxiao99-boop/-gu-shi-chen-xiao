# Day 6 · 自动生成班级英语学情报告

日期：2026-08-15

今天完成了一条完整的小流程：

> 有效数据 → 统计摘要 → 及格率 → 自动文字报告 → 教学建议 → 保存文件

## 今天完成的内容

- 使用字典整理英语成绩指标
- 计算有效人数、平均分、最高分、最低分和中位数
- 根据满分 150 分确定及格线为 90 分
- 用布尔判断和 `.sum()` 统计及格人数
- 计算及格率
- 使用 f-string 自动生成文字报告
- 使用 `if / else` 自动选择教学建议
- 把完整报告保存为 `英语学情报告.txt`
- 理解 Python 缩进和 `IndentationError`

## 1. 回顾 valid_df

`valid_df` 是从原始数据 `df` 中筛选出来的“暂定有效数据表”。

原始数据有 76 行，其中 4 名学生的语文、数学、英语同时为 0。由于暂时不能判断这些记录是真实 0 分、缺考还是尚未录入，因此先排除：

```python
valid_df = df[
    ~(
        (df["语文"] == 0)
        & (df["数学"] == 0)
        & (df["英语"] == 0)
    )
]
```

筛选后：

```text
valid_df.shape
(72, 11)
```

`valid_df` 有 72 行，但英语列有 3 个缺失值，所以英语有效人数是 69 人。

## 2. 建立英语统计摘要

```python
english_summary = {
    "有效人数": valid_df["英语"].count(),
    "平均分": round(valid_df["英语"].mean(), 2),
    "最高分": valid_df["英语"].max(),
    "最低分": valid_df["英语"].min(),
    "中位数": valid_df["英语"].median(),
    "及格人数": pass_count,
    "及格率": pass_rate
}
```

统计结果：

```text
有效人数：69
平均分：40.1
最高分：75.6
最低分：9.0
中位数：38.4
及格人数：0
及格率：0.0%
```

补充理解：

- `=` 用来赋值，把右边的内容保存给左边的变量。
- 字典中的 `:` 用来连接“键”和“值”。
- `40.1` 与 `40.10` 数值相同；数字显示时会省略末尾的 0。
- 中位数是成绩排序后位于中间位置的成绩。

## 3. 统计及格人数和及格率

本次考试满分 150 分，按 60% 计算，及格线为 90 分。

```python
pass_count = (valid_df["英语"] >= 90).sum()
```

这段代码的过程：

```text
逐个判断英语成绩是否大于等于 90
→ 得到一组 True / False
→ .sum() 把 True 当作 1
→ 得到及格人数
```

本次最高分为 75.6 分，所以及格人数为 0。

```python
valid_count = valid_df["英语"].count()

pass_rate = round(
    pass_count / valid_count * 100,
    2
)
```

结果为：

```text
0 ÷ 69 × 100 = 0.0%
```

## 4. 使用 f-string 自动生成报告

```python
report = (
    f"本次英语考试的有效人数为{english_summary['有效人数']}人。\n"
    f"平均分为{english_summary['平均分']}分，最高分为{english_summary['最高分']}分，最低分为{english_summary['最低分']}分，中位数为{english_summary['中位数']}分。\n"
    f"及格人数为{english_summary['及格人数']}人，及格率为{english_summary['及格率']}%。"
)

print(report)
```

关键理解：

- 字符串前的 `f` 表示可以在文字中插入数据。
- `{english_summary['有效人数']}` 从字典中取出对应的值。
- `\n` 表示换行。
- `report = (...)` 使用小括号把多行文字组成一个整体。
- 大括号里放 Python 表达式，“人、分、%”等普通文字写在大括号外。

## 5. 自动生成教学建议

```python
if pass_rate < 30:
    advice = "班级整体基础较薄弱，建议优先巩固基础词汇、核心句型和基本阅读能力。"
else:
    advice = "班级整体基础较稳定，可以增加综合运用和拔高训练。"
```

这里的 30% 是本次练习中自行设定的报告判断标准，以后可以根据学校的实际情况调整。

合并统计报告和教学建议：

```python
full_report = report + "\n教学建议：" + advice

print(full_report)
```

字符串之间的 `+` 表示把多段文字连接起来。

## 6. 保存文字报告

```python
with open("英语学情报告.txt", "w", encoding="utf-8") as file:
    file.write(full_report)

print("报告保存成功")
```

代码逻辑：

- `open()` 打开或创建文件。
- `"w"` 表示写入模式，同名文件已存在时会覆盖原内容。
- `encoding="utf-8"` 避免中文乱码。
- `as file` 把打开的文件暂时命名为 `file`。
- `file.write(full_report)` 把完整报告写入文件。
- `with` 结束后会自动关闭文件。

今天成功生成并保存了 `英语学情报告.txt`。

## 7. 今天遇到的错误

### 引号和括号没有配对

f-string 中需要注意结束顺序：

```text
}   结束数据
%   普通文字
。  句号
"   结束字符串
)   结束外层小括号
```

### IndentationError

错误信息：

```text
expected an indented block
```

意思是 Python 期待一个向右缩进的代码块。

```python
with open("英语学情报告.txt", "w", encoding="utf-8") as file:
    file.write(full_report)
```

`file.write(...)` 前面需要 4 个空格。上一行以冒号结尾时，下一行通常需要缩进，例如 `with`、`if` 和 `else`。

## Day 6 小结

今天已经能够让 Python 根据真实成绩自动完成：

```text
统计数据
→ 组织字典
→ 判断条件
→ 生成文字
→ 给出建议
→ 保存报告
```

下一次继续：绘制英语成绩分布图，并把图表和文字报告组合起来。
