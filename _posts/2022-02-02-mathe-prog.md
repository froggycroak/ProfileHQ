---

layout: post_a
abbrev: MatheProg
tags: 技术分享
year: 2022

title: 
title-cn: 数学规划在建筑设计中的应用
team:
team-cn: 胡潜

---

---

<p class="plainpassage-brief">本文将介绍数学规划方法在建筑设计问题中的应用。

数学规划（Mathematical Programming）是一类通过构建数学模型并求解最优解的方法，核心是在给定约束条件下最大化或最小化目标函数。随着计算机技术的发展，其在复杂系统优化中的优势日益凸显。建筑设计作为融合功能、美学、经济与可持续性的多目标决策过程，常面临空间布局、资源分配、性能优化等复杂问题。数学规划通过量化目标与约束，为建筑设计提供高效决策支持，成为探索设计可能、提升设计效率的重要工具。</p>

`编辑中...`

<h5 class="chapter-heading-left">问题形式</h5>

数学规划的常见形式包括：
<ul>
<li>整数规划（Integer Programming, IP）：变量需取整数值，适用于离散决策场景</li>
<li>混合整数线性规划（Mixed-Integer Linear Programming, MILP）：部分变量为整数，目标函数与约束均为线性，计算效率较高</li>
<li>混合整数二次规划（Mixed-Integer Quadratic Programming, MIQP）：目标函数含二次项（如平方误差）</li>
<li>非线性规划（Nonlinear Programming, NLP）：目标或约束含非线性项</li>
<li>多目标规划（Multi-objective Programming）：同时优化多个冲突目标，输出 Pareto 最优解集供决策者权衡。</li>
</ul>
对于建筑设计问题而言核心在于
<ul>
<li>是否能用格网填充表示?绝大多数问题更适宜用在。在不使用“填充”定义问题时，离散变量实质上是连续变量的一种特殊取样，并非真正的离散建模</li>
<li>能否用纯线性方式建模?</li>
</ul>

<h5 class="chapter-heading-left">通用求解器</h5>

Gurobi
SCIP 开源，有pyscipopt，但没有C#接口。
...

<table border="1" cellpadding="5" cellspacing="0">
  <thead>
    <tr>
      <th><strong>求解器</strong></th>
      <th><strong>特点</strong></th>
      <th><strong>接口支持</strong></th>
      <th><strong>适用场景</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>Gurobi</strong></td>
      <td>商业求解器，速度快、稳定性强，支持 LP/MIP/MIQP/NLP 等全类型问题，提供免费学术许可。</td>
      <td>Python/C++/Java/C#/MATLAB 等主流语言</td>
      <td>...</td>
    </tr>
    <tr>
      <td><strong>CPLEX</strong></td>
      <td>商业求解器，与 Gurobi 性能相当，擅长 MIP 问题，提供可视化建模工具。</td>
      <td>类似 Gurobi，支持 C#/Python 等</td>
      <td>...</td>
    </tr>
    <tr>
      <td><strong>SCIP</strong></td>
      <td>开源求解器，支持 MIP/NLP/CP 等，灵活性强，可通过插件扩展算法。</td>
      <td>提供 pyscipopt（Python）、C 接口；无官方 C# 接口</td>
      <td>...</td>
    </tr>
    <tr>
      <td><strong>GLPK</strong></td>
      <td>轻量级开源求解器，适合教学与简单 LP/MIP 问题，计算效率较低。</td>
      <td>支持 C/Python/Java 等</td>
      <td>...</td>
    </tr>
    <tr>
      <td><strong>COIN-OR</strong></td>
      <td>开源项目集合，包含 CBC（MIP 求解器）、Clp（LP 求解器）等，可定制化高。</td>
      <td>多语言接口，社区活跃</td>
      <td>开源项目集成</td>
    </tr>
  </tbody>
</table>

<h5 class="chapter-heading-left">应用方向</h5>
...
<!-- PeterWonka
中科大2018
飚门系列
野人2021系列 -->

<h5 class="chapter-heading-left">一个实例</h5>

**问题描述**

  - 给定w×h的有限格网容器(Container)、出发点（绿）、目标点（红），求连接出发点和各个目标点的最短路径

**求解原理**

  - 一般地，在图论问题中，由系列顶点和边（对弧）构成的简单图中可以通过给点和边赋值的方法求解最小路径问题；格网中的最小路径问题更为特殊，将一般情形中任意的相邻关系和距离值用格网的方式明确定义出来。
  - 简单图的最短路径求解问题中，需要两组赋值来定义连通关系：一组是边（对弧）赋值，用每边所包含一弧的赋值（图论中通常称作出入度）表示该弧对应方向上存在连通关系；一组是对顶点赋值，出发点和目标点分别赋予正负值，其余点赋予0值；而后在两组值之间建立出、入度方程，以顶点值为已知量，边值为求解变量，再求取路径最小值。
  - 在求取路径最小值的问题中，没有路径可以看作路径最短的极端情形。此时可以看作所有边（弧）被赋予0值。当我们需要连通出发点和目标点时，给出发点和目标点赋予非零值，以方程关系传递非零值，而方程又仅建立在容许连通的点边之间，也就起到了标识连通关系的作用。
  - 当我们容许目标点被路径穿越、容许路径分岔时，方程关系将变得更为复杂。可将每个目标点的非0值设定为1，则（最短路径解中）所有边（弧）上的值可以追溯到某一目标点上（否则可以用反证法证明非优）。上述两种情况时均可以看作某一边（弧）上的值同时追溯多个目标点，因此边（弧）的赋值可能是小于目标点数量的任意整数。

**模型构建**

  - 变量
  
    - 水平方向“的BINARY弧变量 $rf[i][j]，lf[i][j]，(0<i≤w+1，0<j≤h)$
  
    - 竖直方向的BINARY弧变量 $uf[i][j]，df[i][j]，(0<i≤w，0<j≤h+1)$
            
    - 水平方向“的INTEGER边权重值 $rlw[i][j]，(0<i≤w+1，0<j≤h)$
            
    - 竖直方向“的INTEGER边权重值 $udw[i][j]，(0<i≤w，0<j≤h+1)$

  - 目标

    - 路径总长度  $Minimize（\displaystyle\sum rf[i][j]+\displaystyle\sum lf[i][j]+\displaystyle\sum uf[i][j]+\displaystyle\sum df[i][j]）$

  - 约束

    - 目标点入度为1，出度为0（入度-出度=1）
      $\forall$ 目标点$（i，j）$，
      $rlw[i+1][j]（lf[i+1][j]-rf[i+1][j]）$
      $+rlw[i][j]（rf[i][j]-lf[i][j]）$
      $+udw[i+1][j]（df[i+1][j]-uf[i+1][j]）$
      $+udw[i][j]（uf[i][j]-df[i][j]）$$=1$

    - 出发点入度为0，出度为正整数（入度-出度=负整数）

      $\forall$ 出发点$（i，j）$，
      $rlw[i+1][j]（lf[i+1][j]-rf[i+1][j]+rlw[i][j]（rf[i][j]-lf[i][j]）+udw[i+1][j]（df[i+1][j]-uf[i+1][j]）+udw[i][j]（uf[i][j]-df[i][j]<0$

    - 普通点入度、出度相等（入度*权重-出度*权重=0）

      $\forall$ 普通点$（i，j）$，
      $rlw[i+1][j]（lf[i+1][j]-rf[i+1][j]）$
      $+rlw[i][j]（rf[i][j]-lf[i][j]）$
      $+udw[i+1][j]（df[i+1][j]-uf[i+1][j]）$
      $+udw[i][j]（uf[i][j]-df[i][j]）$$=0$

    - 超出容器的边（弧）权重为0
      $rfw[i][j]=0$，$（i=0$ or $w+1）$
      $udw[i][j]=0$，$（j=0$ or $h+1）$
