+++
date = "2015-03-07T16:40:06+08:00"
draft = false
title = "全局优化简介"
tags = ["Matlab","Optimization"]
categories = ["Math"]

+++

### 局部最优和全局最优

优化问题的目的是寻找一个点使得目标函数达到最小值。

* 局部最优点：该点的目标函数值比相邻的点都要小或者相等。
* 全局最优点：该点的目标函数值比其他所有可能点都要小或相等。

![local_vs_global](http://loongmxbt.qiniudn.com/local_vs_global.png)

Matlab 提供的 Optimization Toolbox 能寻找局部最优点。它能找到初始点所在的吸引域（basin of attraction）的最优点。

Global Optimization Toolbox 寻找多余一个的吸引域的最优点。它可以采用如下几种方法：

* GlobalSearch 和 MultiStart 产生多个初始点，然后使用局部求解器找到位于初始点所在的吸引域的最优点。
* ga 使用一组初始点集（也叫种群 population），通过迭代在种群中产生更加好的点。初始种群覆盖了多少吸引域，ga 就能搜索到这些域。
* simulannealbnd 进行随机搜索。一般而言，simulannealbnd 接受比之前更好的点，但它也会偶尔接受较坏的点，来达到不同的吸引域。
* patternsearch 在接受一个点之前先查看一组相邻的点。如果一些相邻点属于不同的吸引域，那么 patternsearch 同时查找一系列吸引域。

### 吸引域

如果目标函数 $f(x)$ 是平滑的，那么梯度 $–\nabla f(x)$ 的方向是其下降最快的方向。

$$ \frac{\mathrm{d}}{\mathrm{d}t}x(t) = -\nabla f(x(t))$$

根据此式可以得出一条路径 $x(t)$ 随着 $t$ 的增加而趋向于局部最小点。一般来说，相互靠近的 $x(0)$ 的最速降线会趋向于同一个最小点。吸引域中的初始点的各最速降线会趋向同一个局部最小点。

下图显示了两个一维的最小点。该图用不同的线型代表了不同的吸引域，并用箭头显示不同的最速降线。其中黑点代表了局部最小点。每条从 $x(0)$ 起始的最速降线，最终都会达到吸引域中的黑点。

![one_d1.png](http://loongmxbt.qiniudn.com/one_d1.png)

下图展示了在多维情况下的最速降线。

![basin1](http://loongmxbt.qiniudn.com/basin1.png)

下图展示了更复杂的路径和吸引域。

![basins1](http://loongmxbt.qiniudn.com/basins1.png)

限制可以将一个吸引域分割成几个部分。例如，考虑最小化 $y$ 满足：

<div>$$
\begin{align}
y & \geq |x|  \\
y & \geq 5 - 4(x-2)^2 
\end{align}
$$</div>

该图展示了两个含有终点的吸引域。

![steepest_descent_barrier](http://loongmxbt.qiniudn.com/steepest_descent_barrier.png)

绘制该图的代码：

```matlab
syms x y
myabs = abs(x);
mycurve = 5 - 4*(x-2)^2;
myline = feval(symengine,'max',myabs,mycurve);
mybrdr = 1 + heaviside(x-2);
myarea = heaviside(y - myline)*mybrdr;
% myarea = 0 where infeasible, 1 when x < 2, 2 when x > 2
marinv = 1/myarea; % removes the infeasible region

plot(0,0,'k.','MarkerSize',25);
hold on
plot(11/4,11/4,'k.','MarkerSize',25);
ezplot(mycurve,[.9 3])
ezplot(myabs,[-2 5])
ezsurf(marinv,[-2,5,-1,6],500)
```

Enter plottools at the command line to edit the figure properties:

* Set Edges of the colored area to no line.
* From the More properties button, set FaceAlpha to 0.5.
* Set the curve widths to 2, and their color to Black.
* Add arrows using the arrow drawing tool.

The steepest descent paths are straight lines down to the constraint boundaries. From the constraint boundaries, the steepest descent paths travel down along the boundaries. The final point is either (0,0) or (11/4,11/4), depending on whether the initial x-value is above or below 2.

来源：[What Is Global Optimization?](http://cn.mathworks.com/help/gads/what-is-global-optimization.html)