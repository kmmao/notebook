# 第二阶段：机器学习基础（4-6周）

## 概述

在深入深度学习之前，理解传统机器学习的核心概念。

## 核心概念

### 1. 机器学习范式

| 类型 | 描述 | 例子 |
|------|------|------|
| 监督学习 | 有标签数据 | 分类、回归 |
| 无监督学习 | 无标签数据 | 聚类、降维 |
| 强化学习 | 奖励信号 | 游戏AI、RLHF |
| 自监督学习 | 从数据自身构造标签 | LLM预训练 |

### 2. 模型评估

- [ ] 训练集/验证集/测试集划分
- [ ] 交叉验证
- [ ] 过拟合与欠拟合
- [ ] 偏差-方差权衡
- [ ] 评估指标（Accuracy、Precision、Recall、F1、AUC）

### 3. 经典算法（了解即可）

| 算法 | 类型 | 核心思想 |
|------|------|----------|
| 线性回归 | 回归 | 最小化MSE |
| 逻辑回归 | 分类 | Sigmoid + 交叉熵 |
| 决策树 | 分类/回归 | 信息增益划分 |
| 随机森林 | 集成 | Bagging + 决策树 |
| SVM | 分类 | 最大间隔 |
| K-means | 聚类 | 最小化类内距离 |
| PCA | 降维 | 最大方差方向 |

### 4. 特征工程

- [ ] 特征缩放（标准化、归一化）
- [ ] 特征选择
- [ ] 特征编码（One-hot、Embedding）
- [ ] 缺失值处理

---

## 关键概念深入

### 损失函数

```python
# 回归：均方误差
MSE = (1/n) * Σ(y_pred - y_true)²

# 分类：交叉熵
CrossEntropy = -Σ y_true * log(y_pred)
```

### 梯度下降

```python
# 基本梯度下降
while not converged:
    gradient = compute_gradient(loss, parameters)
    parameters = parameters - learning_rate * gradient
```

### 正则化

- L1正则化（Lasso）：稀疏性
- L2正则化（Ridge）：防止过拟合
- Dropout：神经网络专用

---

## 实践项目

1. **Kaggle入门竞赛**
   - Titanic生存预测
   - House Prices回归

2. **Scikit-learn实战**
   ```python
   from sklearn.ensemble import RandomForestClassifier
   from sklearn.model_selection import train_test_split
   
   X_train, X_test, y_train, y_test = train_test_split(X, y)
   model = RandomForestClassifier()
   model.fit(X_train, y_train)
   score = model.score(X_test, y_test)
   ```

---

## 推荐资源

| 资源 | 链接 |
|------|------|
| 吴恩达机器学习课程 | https://www.coursera.org/learn/machine-learning |
| Scikit-learn官方教程 | https://scikit-learn.org/stable/tutorial/ |
| Kaggle Learn | https://www.kaggle.com/learn |
| 统计学习方法（李航） | 经典教材 |

## 检验标准

1. 能解释过拟合是什么，如何解决
2. 能说明训练集/验证集/测试集的作用
3. 能用Scikit-learn完成一个分类任务
4. 理解交叉熵损失的含义
