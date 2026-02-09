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

`编辑中...`

本文将介绍数学规划方法在建筑设计问题中的应用。

数学规划（Mathematical Programming）是一类通过构建数学模型并求解最优解的方法，核心是在给定约束条件下最大化或最小化目标函数。随着计算机技术的发展，其在复杂系统优化中的优势日益凸显。建筑设计作为融合功能、美学、经济与可持续性的多目标决策过程，常面临空间布局、资源分配、性能优化等复杂问题。数学规划通过量化目标与约束，为建筑设计提供高效决策支持，成为探索设计可能、提升设计效率的重要工具。

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
      <td>大规模复杂模型（如城市级建筑群优化）</td>
    </tr>
    <tr>
      <td><strong>CPLEX</strong></td>
      <td>商业求解器，与 Gurobi 性能相当，擅长 MIP 问题，提供可视化建模工具。</td>
      <td>类似 Gurobi，支持 C#/Python 等</td>
      <td>工业级项目（如建筑供应链优化）</td>
    </tr>
    <tr>
      <td><strong>SCIP</strong></td>
      <td>开源求解器，支持 MIP/NLP/CP 等，灵活性强，可通过插件扩展算法。</td>
      <td>提供 pyscipopt（Python）、C 接口；无官方 C# 接口，需通过 C++ 封装调用</td>
      <td>学术研究、中小规模模型</td>
    </tr>
    <tr>
      <td><strong>GLPK</strong></td>
      <td>轻量级开源求解器，适合教学与简单 LP/MIP 问题，计算效率较低。</td>
      <td>支持 C/Python/Java 等</td>
      <td>入门级模型验证</td>
    </tr>
    <tr>
      <td><strong>COIN-OR</strong></td>
      <td>开源项目集合，包含 CBC（MIP 求解器）、Clp（LP 求解器）等，可定制化高。</td>
      <td>多语言接口，社区活跃</td>
      <td>开源项目集成</td>
    </tr>
  </tbody>
</table>

<h5 class="chapter-heading-left">应用案例</h5>

PeterWonka
中科大2018
飚门系列
野人2021系列
