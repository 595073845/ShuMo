---
created: 2026-07-10
tags: [基础共建, Python, NumPy, Pandas, Matplotlib]
---

# Python 三件套速查

> **时间**: 第2周（7.21 - 7.27）  
> **本地已有资料**: 见 [[现有资料索引]]，有Python学习笔记、思维导图、零基础手册  
> **目标**: 能独立完成数据的读写→清洗→分析→可视化全流程

---

## NumPy（矩阵运算核心）

### 创建数组
```python
import numpy as np

# 从列表创建
a = np.array([[1, 2, 3], [4, 5, 6]])

# 特殊数组
zeros = np.zeros((3, 4))      # 全零矩阵
ones = np.ones((2, 3))        # 全一矩阵
eye = np.eye(5)               # 单位矩阵
lin = np.linspace(0, 1, 100)  # 0到1等分100个点

# 随机数
rand = np.random.rand(3, 3)   # [0,1)均匀分布
norm = np.random.randn(100)   # 标准正态分布
choice = np.random.choice([1,2,3], size=10)  # 随机选择
```

### 核心运算
```python
# 矩阵运算
A @ B          # 矩阵乘法（Python 3.5+）
np.dot(A, B)   # 同上
np.linalg.inv(A)    # 逆矩阵
np.linalg.eig(A)    # 特征值+特征向量
np.linalg.svd(A)    # SVD分解
np.linalg.lstsq(A, b)  # 最小二乘解

# 统计
a.mean(), a.std(), a.var()
a.sum(axis=0)     # 按列求和
a.max(axis=1)     # 按行取最大
np.corrcoef(a)    # 相关系数矩阵
```

---

## Pandas（数据处理核心）

### 读写数据
```python
import pandas as pd

# 读取各种格式
df = pd.read_excel('data.xlsx', sheet_name='Sheet1')
df = pd.read_csv('data.csv', encoding='utf-8')
df = pd.read_table('data.txt', sep='\t')
df = pd.read_json('data.json')

# 写入
df.to_excel('output.xlsx', index=False)
df.to_csv('output.csv', index=False)
```

### 数据清洗（比赛中最常用）
```python
# 查看基本信息
df.head(), df.info(), df.describe()
df.shape, df.columns, df.dtypes

# 缺失值处理
df.isnull().sum()           # 查看每列缺失数量
df.dropna()                 # 删除含缺失的行
df.fillna(df.mean())        # 用均值填充
df.fillna(method='ffill')   # 前向填充

# 异常值检测（3σ法）
mean, std = df['col'].mean(), df['col'].std()
df = df[(df['col'] > mean - 3*std) & (df['col'] < mean + 3*std)]

# 数据变换
df.groupby('category').mean()   # 分组求均值
pd.get_dummies(df['cat'])       # 独热编码
df['new'] = (df['a'] - df['a'].mean()) / df['a'].std()  # 标准化
```

### 合并技巧
```python
pd.merge(df1, df2, on='key', how='left')  # SQL风格合并
pd.concat([df1, df2], axis=0)              # 纵向拼接
pd.pivot_table(df, values='val', index='row', columns='col')  # 透视表
```

---

## Matplotlib（可视化核心）

### 快速出图模板
```python
import matplotlib.pyplot as plt
import numpy as np

# 中文显示设置（重要！）
plt.rcParams['font.sans-serif'] = ['SimHei']  # Windows
plt.rcParams['axes.unicode_minus'] = False

x = np.linspace(0, 10, 100)
y1 = np.sin(x)
y2 = np.cos(x)

# 子图布局
fig, axes = plt.subplots(2, 2, figsize=(12, 8))
fig.suptitle('标题')

# 折线图
axes[0, 0].plot(x, y1, 'r-', label='sin')
axes[0, 0].plot(x, y2, 'b--', label='cos')
axes[0, 0].legend()
axes[0, 0].set_xlabel('x'); axes[0, 0].set_ylabel('y')

# 散点图
axes[0, 1].scatter(x, y1 + 0.1*np.random.randn(100), c='blue', alpha=0.5)

# 柱状图
axes[1, 0].bar(['A', 'B', 'C'], [10, 20, 15])

# 热力图
im = axes[1, 1].imshow(np.random.rand(5, 5), cmap='hot')
plt.colorbar(im, ax=axes[1, 1])

plt.tight_layout()
plt.savefig('output.png', dpi=300)
# plt.show()  # 比赛时不需要，保存即可
```

### Plotly（交互式可视化）
你本地有《Python数据分析：基于Plotly的动态可视化绘图》，参考那本书。
```python
import plotly.express as px

# 一句话出交互图
fig = px.scatter(df, x='col1', y='col2', color='category', size='col3')
fig.write_html('output.html')  # 保存为交互式网页
```

---

## 常见比赛流水线

```python
# 1. 读取数据
df = pd.read_excel('data.xlsx')

# 2. 查看基本信息
print(df.info())
print(df.describe())

# 3. 清洗
df = df.dropna()
# 或 df = df.fillna(df.median())

# 4. 提取特征和标签
X = df.iloc[:, :-1].values
y = df.iloc[:, -1].values

# 5. 标准化
from sklearn.preprocessing import StandardScaler
X = StandardScaler().fit_transform(X)

# 6. 建模
from sklearn.linear_model import LinearRegression
model = LinearRegression()
model.fit(X, y)

# 7. 预测
y_pred = model.predict(X)

# 8. 可视化对比
plt.scatter(y, y_pred, alpha=0.5)
plt.xlabel('真实值')
plt.ylabel('预测值')
plt.title('模型效果')
plt.savefig('result.png')
```

---

## 本地资料对照

你桌面上已有这些Python资料，**阶段一过一遍即可，不需重新学**：

| 资料 | 用途 | 何时看 |
|------|------|--------|
| python 学习笔记.pdf | Python基础通识 | 第2周快速浏览 |
| Python思维导图/ | 各语法概念速查 | 写代码时查阅 |
| python背记手册.pdf | 语法速查手册 | 写代码时查阅 |
| Python零基础知识手册.docx | 零基础入门 | 如果基础薄弱 |
| Python数据分析：基于Plotly的动态可视化绘图.pdf | 可视化进阶 | 第2周 |

---
*参考: [[模型分类全景图]] ← 知道模型后，用Python实现它*
