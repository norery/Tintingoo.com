---
title: 从泰勒展开推导弹性波动方程的有限差分近似
author: Tintingo
date: '2022-02-22'
slug: FDM-WaveEquation
show_toc: ture
toc_depth: 3
categories:
  - WaveEquation
tags:
  - WaveEquation
  - FDM
---



以一维弹性波动方程为例：

\begin{equation}
\label{eq:1}
\frac{\partial^{2}u(x,t)}{\partial x^{2}} = \frac{1}{c^{2}} \frac{\partial^{2} u(x,t)}{\partial t^{2}}
\end{equation}

式中，位移 $u(x,t)$ 是空间坐标 x 和时间 t 的单值连续函数，$c$ 为波速。


<a id="org0ba3982"></a>

## 空间差分算子的推导

首先对位移函数 $u(x,t)$ 在坐标 $x$ 两侧附近作泰勒展开：

\begin{equation}
\label{eq:2}
u(x + \Delta x, t) = u(x, t) + \Delta x \frac{\partial u(x, t)}{\partial x} + \frac{1}{2} \Delta x^{2} \frac{\partial^{2} u(x,t)}{\partial x^{2}} + \frac{1}{6}\Delta x^{3}\frac{\partial^{3}u(x,t)}{\partial x^{3}} + \cdots
\end{equation}

\begin{equation}
\label{eq:3}
u(x - \Delta x, t) = u(x, t) - \Delta x \frac{\partial u(x, t)}{\partial x} + \frac{1}{2} \Delta x^{2} \frac{\partial^{2} u(x,t)}{\partial x^{2}} - \frac{1}{6}\Delta x^{3}\frac{\partial^{3}u(x,t)}{\partial x^{3}} + \cdots
\end{equation}


<a id="org526bfd3"></a>

### 位移对空间一阶偏导数的近似形式推导

用 式 \ref{eq:2} 减去 式 \ref{eq:3}：

\begin{equation}
\label{eq:4}
\nonumber
u(x+\Delta x, t) - u(x-\Delta x, t) = 2 \Delta \frac{\partial u(x, t)}{\partial x} + \frac{1}{3}\Delta x^{3} \frac{\partial^{3}u(x,t)}{\partial x^{3}} + O(\Delta x^{4})
\end{equation}

将 $\frac{\partial u(x, t)}{\partial x}$ 移到左边，就可以得到：

\begin{equation}
\label{eq:5}
\nonumber
\frac{\partial u(x, t)}{\partial x} = \frac{u(x+\Delta x, t) - u(x-\Delta x, t)}{2\Delta x} - \frac{1}{6}\Delta x^{2} \frac{\partial^{3} u(x,t)}{\partial x^{3}} - O(\Delta x^{3})
\end{equation}

考虑到 $\Delta x \rightarrow 0$ 是一个小量，因此得到：

\begin{equation}
\label{eq:6}
\nonumber
\frac{\partial u(x, t)}{\partial x} = \frac{u(x+\Delta x, t) - u(x-\Delta x, t)}{2\Delta x} + O(\Delta x^{2})  = D_{x}u + O(\Delta x^{2})
\end{equation}

式中，$D_{x}$ 称为 `差分算子` ，$O(\Delta x^{2})$ 称为差分算子的 `截断误差` 。

与公式中的其他项相比，小量 $\Delta x$ 的二阶及更高阶指数项可以忽略时，得到 `位移对空间一阶偏导数的近似形式` ：

\begin{equation}
\label{eq:7}
\frac{\partial u(x, t)}{\partial x} \approx D_{x}u = \frac{u(x+\Delta x, t) - u(x-\Delta x, t)}{2\Delta x}
\end{equation}


<a id="org4a25ccd"></a>

### 位移对空间二阶偏导数的近似形式推导

用 式 \ref{eq:2} 加上 式 \ref{eq:3}，得到：

\begin{equation}
\label{eq:8}
\nonumber
u(x+\Delta x, t) + u(x-\Delta x, t) = 2 u(x, t) + \Delta x^{2} \frac{\partial^{2} u(x, t)}{\partial x^{2}} + O(\Delta x^{4})
\end{equation}

将 $\frac{\partial^{2} u(x, t)}{\partial x^{2}}$ 移到左边，整理为：

\begin{equation}
\label{eq:9}
\nonumber
\frac{\partial^{2} u(x, t)}{\partial x^{2}}  = \frac{u(x+\Delta x, t) + u(x - \Delta x, t) - 2u(x,t)}{\Delta x^{2}} + O(\Delta x^{2}) = D_{xx}u + O(\Delta x^{2})
\end{equation}

式中，$D_{xx}$ 为 `二阶差分算子` ，$O(\Delta x^{2})$ 称为差分算子的 `截断误差` 。

与公式中的其他项相比，小量 $\Delta x$ 的二阶及更高阶指数项可以忽略时，得到 `位移对空间二阶偏导数的近似形式` ：

\begin{equation}
\label{eq:10}
\frac{\partial^{2} u(x, t)}{\partial x^{2}}  \approx D_{xx}u = \frac{u(x+\Delta x, t) + u(x - \Delta x, t) - 2u(x,t)}{\Delta x^{2}}
\end{equation}

因此，位移函数在坐标 x 处的一阶和二阶偏导数，都可以近似表示为 x 附近函数值的差商形式，截断误差均为 $O(\Delta x^{2})$ 。


<a id="org127a3cc"></a>

## 时间差分算子的推导

首先对位移函数 $u(x,t)$ 在坐标 $t$ 两侧附近作泰勒展开：

\begin{equation}
\label{eq:11}
u(x, t + \Delta t) = u(x, t) + \Delta t \frac{\partial u(x, t)}{\partial t} + \frac{1}{2} \Delta t^{2} \frac{\partial^{2} u(x,t)}{\partial t^{2}} + \frac{1}{6}\Delta t^{3}\frac{\partial^{3}u(x,t)}{\partial t^{3}} + \cdots
\end{equation}

\begin{equation}
\label{eq:12}
u(x, t - \Delta t) = u(x, t) - \Delta t \frac{\partial u(x, t)}{\partial t} + \frac{1}{2} \Delta t^{2} \frac{\partial^{2} u(x,t)}{\partial t^{2}} -\frac{1}{6}\Delta t^{3}\frac{\partial^{3}u(x,t)}{\partial t^{3}} + \cdots
\end{equation}

与上述空间差分算子的推导过程类似，位移函数对时间的偏导数用差分算子 `$D_t$`、 `$D_{tt}$` 表示为 ：

\begin{equation}
\label{eq:13}
\frac{\partial u(x, t)}{\partial t} \approx D_{t}u = \frac{u(x, t+\Delta t) - u(x, t+\Delta t)}{2\Delta t}
\end{equation}

\begin{equation}
\label{eq:14}
\frac{\partial^{2} u(x, t)}{\partial t^{2}}  \approx D_{tt}u = \frac{u(x, t+\Delta t) + u(x, t+\Delta t) - 2u(x,t)}{\Delta t^{2}}
\end{equation}

截断误差为 $O(\Delta t^{2})$ 。


<a id="org3a5e377"></a>

## 一维波动方程的有限差分近似

使用公式 \ref{eq:7}, \ref{eq:10}, \ref{eq:13}, \ref{eq:14} 中的差分算子代替波动方程 \ref{eq:1} 中的微分算子，可以将波动方程近似为：

\begin{equation}
\label{eq:15}
\frac{u(x+\Delta x, t) + u(x - \Delta x, t) - 2 u(x, t)}{\Delta x^{2}} = \frac{1}{c^{2}} \frac{u(x, t+\Delta t) + u(x, t - \Delta t) - 2u(x, t)}{\Delta t^{2}}
\end{equation}

接下来我们对连续的波动方程求解域离散化，对求解域进行均匀网格划分，空间和时间步长分别为 $\Delta x$, $\Delta t$, 如下图所示：

![img](https://vde05-1256575153.cos.ap-beijing.myqcloud.com/img/20220222195645.png)

经过离散化的空间坐标和时间坐标可以表示为：

\begin{equation}
\label{eq:17}
\nonumber
x = x_{j} = j \Delta x, \quad j=0, \pm1, \pm2, \cdots
\end{equation}

\begin{equation}
\label{eq:18}
\nonumber
t = t_{n} = n \Delta t, \quad n=0, \pm1, \pm2, \cdots
\end{equation}

对求解域离散化后，可以将离散化的波动方程表示为如下式所示的形式：

<div>
\begin{equation}
\label{eq:16}
\frac{u_{j+1}^{n}+u_{j-1}^{n}-2u_{j}^{n}}{\Delta x^{2}} = \frac{1}{c^{2}} \frac{u_{j}^{n+!}+u_{j}^{n-1}-2u_{j}^{n}}{\Delta t^{2}}, \quad j=0,\pm1, \pm2, \cdots;n=0,1,2,\cdots
\end{equation}
</div>


To Be Continued!









