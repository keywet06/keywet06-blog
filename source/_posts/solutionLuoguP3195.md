---
title: LuoguP3195 | solution
date: 2020-01-28 12:03:33
mathjax: true
comments: true
tags: 
- solution
- Luogu
categories: 
- solution
- Luogu
---

### 题目大意

P 教授要去看奥运，但是他舍不下他的玩具，于是他决定把所有的玩具运到北京。他使用自己的压缩器进行压缩，其可以将任意物品变成一堆，再放到一种特殊的一维容器中。

P 教授有编号为 $1 \cdots n$ 的 $n$ 件玩具，第 $i$ 件玩具经过压缩后的一维长度为 $C_i$。

为了方便整理，P教授要求：

- 在一个一维容器中的玩具编号是连续的。
- 同时如果一个一维容器中有多个玩具，那么两件玩具之间要加入一个单位长度的填充物。形式地说，如果将第 $i$ 件玩具到第 $j$ 个玩具放到一个容器中，那么容器的长度将为 $x=j-i+\sum\limits_{k=i}^{j}C_k$。

制作容器的费用与容器的长度有关，根据教授研究，如果容器长度为 $x$，其制作费用为 $(x-L)^2$。其中 $L$ 是一个常量。P 教授不关心容器的数目，他可以制作出任意长度的容器，甚至超过 $L$。但他希望所有容器的总费用最小。

### 思路

#### 公式推导

$$
令 f_i 表示前 i 个物品制作容器的最小费用 ， sum_i = \sum_{k = 1}^{i} C_k\\
由题意得 f_i = min\{ f_j + (i - j - 1 + sum_i - sum_j - L)^2 \ |\ j \in [1, i - 1] \}\\
令 a_i = sum_i + i ，  L' = L + 1\\
\therefore f_i = f_j + (a_i - a_j - L')^2\\
若决策j_2优于决策j_1(j_1 < j_2)\\
\therefore f_{j_1} + (a_i - a_{j_1} - L')^2 \ge f_{j_2} + (a_i - a_{j_2} - L')^2\\
\therefore 2 a_i(a_{j_2} + L') - 2 a_i(a_{j_1} + L') \ge f_{j_2} + (a_{j_2} + L')^2 - f_{j_1} - (a_{j_1} + L')^2\\
令 b_i = f_{i} + (a_{i} + L')^2\\
\therefore 2 a_i(a_{j_2} - a_{j_1}) \ge b_{j_2} - b_{j_1}\\
\therefore 2 a_i \ge \frac{b_{j_2} - b_{j_1}}{a_{j_2} - a_{j_1}}\\
$$

#### 做法

首先将已经决策完毕的部分按 $(a_i, b_i)​$ 画在平面直角坐标系上

在按 $2a_i$ 与斜率的大小决定在单调队列上决策点的取舍

相当于用单调队列维护下凸包

### 代码

```cpp
#include <bits/stdc++.h>
const int N = 50000;
const int Np = N + 5;
long long n, L, Lp, head, tail;
long long a[Np], b[Np], c[Np], f[Np], s[Np];
template <typename _Tp>
  _Tp sqr(_Tp x);
template <typename _Tp>
  _Tp sqr (_Tp x) { return x * x; }
int main() {
    std::ios::sync_with_stdio(0);
    std::cin.tie(0);
    std::cout.tie(0);
    std::cin >> n >> L;
    for (int i = 1; i <= n; ++i) {
        std::cin >> c[i];
        a[i] = a[i - 1] + c[i] + 1;
    }
    Lp = L + 1;
    b[0] = sqr(Lp);
    head = 1;
    tail = 0;
    s[++tail] = 0;
    for (int i = 1; i <= n; ++i) {
        while (head < tail && 1.0 * ((b[s[head + 1]] - b[s[head]]) /
          (a[s[head + 1]] - a[s[head]]) <= 2 * a[i] )) {
            ++head;
        }
        f[i] = f[s[head]] + sqr(a[i] - a[s[head]] - Lp);
        b[i] = f[i] + sqr(a[i] + Lp);
        while (head < tail && 1.0 * (b[i] - b[s[tail]]) / (a[i] - a[s[tail]]) <=
          1.0 * (b[s[tail]] - b[s[tail - 1]]) / (a[s[tail]] - a[s[tail - 1]])) {
            --tail;
        }
        s[++tail] = i;
    }
    std::cout << f[n] << std::endl;
    return 0;
}
```



