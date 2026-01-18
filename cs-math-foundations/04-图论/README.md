# 第四阶段：图论（4-6周）

## 概述

图论是离散数学的核心，也是算法和数据结构的数学基础。

---

## 1. 图的基本概念

### 定义

```
图 G = (V, E)
V = 顶点集
E = 边集 ⊆ V×V
```

### 图的类型

| 类型 | 描述 |
|------|------|
| 无向图 | 边没有方向 |
| 有向图 | 边有方向 (u,v) ≠ (v,u) |
| 简单图 | 无自环、无重边 |
| 多重图 | 允许重边 |
| 加权图 | 边有权重 |
| 二部图 | 顶点可分两组，边只在组间 |

### 基本术语

| 术语 | 定义 |
|------|------|
| 度（degree） | 与顶点关联的边数 |
| 入度/出度 | 有向图中进入/离开的边数 |
| 路径（path） | 顶点序列，相邻顶点有边 |
| 环（cycle） | 起点=终点的路径 |
| 连通 | 任意两点间有路径 |
| 连通分量 | 最大连通子图 |

### 握手定理
```
Σ deg(v) = 2|E|
```

---

## 2. 特殊图类

### 完全图 Kₙ
每对顶点都有边，边数 = n(n-1)/2

### 完全二部图 Kₘ,ₙ
两部分分别有m和n个顶点，所有可能边都存在

### 树（Tree）
连通无环图
- n个顶点的树有n-1条边
- 任意两点间有唯一路径

### 平面图
可以画在平面上边不相交

**欧拉公式**：V - E + F = 2（F是面数）

**库拉托夫斯基定理**：G是平面图 ⟺ G不包含K₅或K₃,₃的细分

---

## 3. 图的遍历

### 深度优先搜索（DFS）

```python
def dfs(v, visited):
    visited.add(v)
    for u in neighbors(v):
        if u not in visited:
            dfs(u, visited)
```

**应用**：
- 连通分量
- 拓扑排序
- 强连通分量（Tarjan/Kosaraju）
- 环检测

### 广度优先搜索（BFS）

```python
def bfs(start):
    queue = [start]
    visited = {start}
    while queue:
        v = queue.pop(0)
        for u in neighbors(v):
            if u not in visited:
                visited.add(u)
                queue.append(u)
```

**应用**：
- 最短路径（无权图）
- 层次遍历

---

## 4. 最短路径

| 算法 | 适用场景 | 时间复杂度 |
|------|----------|------------|
| BFS | 无权图 | O(V+E) |
| Dijkstra | 非负权 | O((V+E)logV) |
| Bellman-Ford | 可有负权 | O(VE) |
| Floyd-Warshall | 全源最短路 | O(V³) |

### Dijkstra算法

```python
def dijkstra(graph, start):
    dist = {v: ∞ for v in graph}
    dist[start] = 0
    pq = [(0, start)]
    
    while pq:
        d, u = heappop(pq)
        if d > dist[u]:
            continue
        for v, w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                heappush(pq, (dist[v], v))
    return dist
```

---

## 5. 最小生成树

### Kruskal算法
按边权排序，贪心选择不成环的边

### Prim算法
从一个顶点开始，贪心扩展

---

## 6. 网络流

### 最大流问题
从源s到汇t的最大流量

### Ford-Fulkerson方法
沿增广路径增加流量

### 最大流最小割定理
> 最大流 = 最小割

---

## 7. 匹配问题

### 二部图最大匹配
匈牙利算法，O(VE)

### Hall定理
二部图G=(X∪Y,E)有完美匹配 ⟺ ∀S⊆X: |N(S)| ≥ |S|

---

## 8. 图着色

### 顶点着色
相邻顶点不同色

**色数 χ(G)**：最少需要的颜色数

- χ(Kₙ) = n
- 二部图 χ(G) = 2
- 平面图 χ(G) ≤ 4（四色定理）

### 边着色
相邻边不同色

---

## 9. 欧拉路径与哈密顿路径

| | 欧拉 | 哈密顿 |
|---|------|--------|
| 定义 | 经过每条边一次 | 经过每个顶点一次 |
| 判定 | 多项式时间 | NP完全 |
| 条件 | 0或2个奇度顶点 | 无简单条件 |

---

## 图论在CS中的应用

| 应用 | 图论概念 |
|------|----------|
| 社交网络 | 图、中心性、社区发现 |
| 网页排名 | PageRank、随机游走 |
| 路由算法 | 最短路径 |
| 调度问题 | 图着色、拓扑排序 |
| 编译器 | 数据流图、支配树 |
| 神经网络 | 计算图 |

---

## 推荐资源

| 资源 | 内容 |
|------|------|
| Graph Theory (Diestel) | 图论圣经，免费在线 |
| Introduction to Graph Theory (West) | 标准教材 |
| Algorithm Design (Kleinberg & Tardos) | 算法视角的图论 |
