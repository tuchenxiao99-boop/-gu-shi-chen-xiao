# Day 7 · Matplotlib 英语成绩分布图

日期：2026-08-17

今天完成了从英语成绩数据到可视化学情分析的完整流程：

> 提取有效成绩 → 绘制直方图 → 添加平均分线和及格线 → 美化并保存图片 → 生成读图结论

本笔记不包含学生姓名或个人成绩记录，只记录汇总统计和学习过程。

## 一、今天完成的内容

- 导入并使用 Matplotlib
- 从 `valid_df` 提取有效英语成绩
- 使用 `plt.hist()` 绘制成绩分布直方图
- 按每 10 分一个区间进行分组
- 设置画布大小、颜色、标题和坐标轴
- 解决中文字体警告
- 添加 90 分及格线和 40.1 分平均分线
- 使用图例和网格辅助读图
- 将图表保存为高分辨率 PNG
- 自动生成读图结论
- 使用 `"\n\n"` 把读图结论接入学情报告

## 二、准备英语成绩

`valid_df` 有 72 行、11 列。英语列有 3 个缺失值，因此英语有效人数为 69。

```python
english_scores = valid_df["英语"].dropna()

english_scores.count()
```

结果：

```text
69
```

`english_scores` 是一个 pandas Series，保存去掉缺失值后的有效英语成绩。

## 三、导入 Matplotlib

```python
import matplotlib.pyplot as plt
```

名称理解：

- `matplotlib`：Python 常用绘图库。
- `pyplot`：负责快速画图的模块。
- `plt`：`pyplot` 的常用别名，不是 Python 关键字。

今天曾把 `pyplot` 写成 `pyploy`，正确拼写是 `pyplot`。

如果 Jupyter 当前环境缺少 Matplotlib，可以运行：

```python
%pip install matplotlib
```

必要时安装后重启 Kernel。

## 四、绘制直方图

```python
plt.figure(figsize=(10, 6))

plt.hist(
    english_scores,
    bins=range(0, 151, 10),
    edgecolor="black",
    color="skyblue"
)
```

参数含义：

- `figsize=(10, 6)`：画布宽 10 英寸、高 6 英寸。
- `hist`：histogram，直方图。
- `bins`：分组区间。
- `range(0, 151, 10)`：生成 0、10、20……150，每 10 分一个区间。
- `edgecolor="black"`：柱子边框为黑色。
- `color="skyblue"`：柱子为天蓝色。

本次汇总数据中，30—40 分区间人数最多，共 21 人；成绩主要集中在 30—50 分。

## 五、设置中文和坐标轴

Mac 使用苹方字体：

```python
plt.rcParams["font.sans-serif"] = ["PingFang SC"]
```

`PingFang SC` 中间有空格。写成 `PingFangSC` 时，Matplotlib 找不到字体，会出现 `findfont` 警告。

```python
plt.title("班级英语成绩分布")
plt.xlabel("英语分数")
plt.ylabel("学生人数")
plt.xticks(range(0, 151, 10))
```

- `title`：标题。
- `xlabel`：横轴名称。
- `ylabel`：纵轴名称。
- `xticks`：横轴刻度。

`plt.title()` 应写在 `plt.hist(...)` 结束之后，不能作为 `plt.hist()` 的参数。

## 六、添加及格线和平均分线

本次考试满分 150 分，及格线为 90 分：

```python
plt.axvline(
    x=90,
    color="red",
    linestyle="--",
    label="及格线（90分）"
)
```

`axvline` 可拆为：

- `ax`：Axes，坐标轴区域。
- `v`：vertical，垂直。
- `line`：线。

先计算平均分并保留 1 位小数：

```python
mean_score = round(english_scores.mean(), 1)
```

结果是 `40.1`。

添加绿色平均分线：

```python
plt.axvline(
    x=mean_score,
    color="green",
    linestyle=":",
    label=f"平均分（{mean_score}分）"
)
```

f-string 中：

- 前面的 `f` 表示格式化字符串。
- `{mean_score}` 表示把变量值填入文字。

`label` 负责给线起名字，`plt.legend()` 才负责显示图例。两条线都画完以后再写：

```python
plt.legend()
```

## 七、添加网格

```python
plt.grid(axis="y", alpha=0.3)
```

- `grid`：网格。
- `axis="y"`：按照纵轴刻度添加横向辅助线。
- `alpha=0.3`：透明度为 0.3。

`alpha` 范围为 0～1：0 完全透明，1 完全不透明。

## 八、保存图表

```python
plt.savefig(
    "英语成绩分布图.png",
    dpi=300,
    bbox_inches="tight"
)
```

- PNG：Portable Network Graphics，使用无损压缩，适合图表。
- JPG / JPEG：Joint Photographic Experts Group，通常使用有损压缩，更适合照片。
- DPI：Dots Per Inch，每英寸点数。
- `dpi=300`：保存较清晰的图片。
- bbox：Bounding Box，边界框。
- `bbox_inches="tight"`：收紧图表四周多余的空白。

`plt.savefig()` 要放在 `plt.show()` 前面，并与绘图代码放在同一个单元格中，否则可能保存出空白图片。

## 九、完整绘图代码

```python
import matplotlib.pyplot as plt

english_scores = valid_df["英语"].dropna()
mean_score = round(english_scores.mean(), 1)

plt.rcParams["font.sans-serif"] = ["PingFang SC"]
plt.figure(figsize=(10, 6))

plt.hist(
    english_scores,
    bins=range(0, 151, 10),
    edgecolor="black",
    color="skyblue"
)

plt.title("班级英语成绩分布")
plt.xlabel("英语分数")
plt.ylabel("学生人数")
plt.xticks(range(0, 151, 10))

plt.axvline(
    x=90,
    color="red",
    linestyle="--",
    label="及格线（90分）"
)

plt.axvline(
    x=mean_score,
    color="green",
    linestyle=":",
    label=f"平均分（{mean_score}分）"
)

plt.grid(axis="y", alpha=0.3)
plt.legend()

plt.savefig(
    "英语成绩分布图.png",
    dpi=300,
    bbox_inches="tight"
)

plt.show()
```

## 十、生成读图结论

```python
chart_summary = (
    "本班英语成绩主要集中在30—50分，"
    f"平均分为{mean_score}分，"
    "90分及格线右侧没有学生。"
)

print(chart_summary)
```

输出：

```text
本班英语成绩主要集中在30—50分，平均分为40.1分，90分及格线右侧没有学生。
```

注意：

- 最高的柱子表示人数最多的分数区间，本次为 30—40 分。
- 绿色平均线表示全班成绩的平均位置，本次为 40.1 分。
- 两者不是同一个概念，位置不一定完全重合。

## 十一、接入学情报告

```python
updated_report = full_report + "\n\n" + chart_summary

print(updated_report)
```

- `+`：连接字符串。
- `\n`：换到下一行，相当于一次回车。
- `\n\n`：连续换两次行，相当于两次回车，中间空一行。

## 十二、今天遇到的问题

### 1. ModuleNotFoundError

原因：Jupyter 当前环境没有可用的 Matplotlib。

处理：使用 `%pip install matplotlib` 安装到 Notebook 当前环境，并在需要时重启 Kernel。

### 2. 中文字体警告

默认字体 DejaVu Sans 不完整支持中文。将字体设置为 `PingFang SC` 后解决。

### 3. 图例没有显示

`label` 只记录名称。必须在两条线都画完后调用 `plt.legend()`。

### 4. 保存出空白图片

正确顺序是：

```text
完成绘图
→ plt.savefig(...)
→ plt.show()
```

## Day 7 小结

今天已经能够完成：

```text
提取有效成绩
→ 按 10 分分段
→ 绘制直方图
→ 添加平均分线和及格线
→ 添加中文标题、坐标轴、图例和网格
→ 保存高清 PNG
→ 生成读图结论
→ 接入文字学情报告
```

下一次可以继续把图表和文字报告整理成一个完整的班级学情分析成果。
