---
layout: research
projid: R25-B01
abbrev: ProtoMass
permalink: /Research/ProtoMass/
icon-image: 
featured-image: https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/featured.jpg
sort-year: 2026
include-website: true
include-pdf: true

title: Exploring Architectural Design with Digital Ptototypes of Building Volume
title-cn: 面向原创设计的建筑体量原型智能演绎工具
tags: [ ]
tags-cn: [ 进行中...,  主导, AI, 原创建筑设计]
team: [ SSTD-DTA, ECADI-CC ]
team-cn: [ 华建科技数字化所, 华东院创作中心 ]
---


---

<div class="plainpassage-brief">
ProtoMass建筑体量原型智能演绎工具是华建科技数字化所与华东院创作中心合作的数字化开发项目，旨在为原创设计师提供自动化交互式的初步体量方案生成服务。我在项目中担任技术负责人。从问题梳理，到技术路线的制订，再到实验执行，主要工作均有我负责计划和推进。当前该项目已完成立项开题，并在预实验阶段取得一定进展，包括：
  <ul>
    <li>基于几何计算、多代理系统、数学规划的建筑体量生成(部分原型样例)</li>
    <li>基于Rhino的几何计算云服务和通信实现</li>
    <li>基于SentenceTransformer的建筑原型语义匹配</li>
  </ul>
</div>


<div class="videos-container">
    {% include video_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/SyntaxB1cFreeRepeats-VolumeRoofDoubleBox-24-0.4-1.0.mp4" 
        note="体量生成预实验：录屏1"
    %}
    {% include video_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/SyntaxB1dGridAligned-VolumeRoofSingleBox-24-0.4-1.0.mp4"
        note="体量生成预实验：录屏2"
    %}
</div>

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/pre-experiment-01.png" note="体量生成预实验: 随手绘制场地边界，生成符合指标要求的重复体量组合" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/syntax-compare2-01.jpg" note="选定组合要素原型，仍具有方案多样性" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/img%20(4).jpg" note="建筑体量原型转译的接口设计" %}

<h5 class="chapter-heading-left">项目背景</h5>

建筑原创设计对建筑设计企业至关重要。建筑师需解构重组建筑知识经验、快速提出大量方案、并在其中充分考虑场地条件而迭代选优。丁顺总建筑师团队基于多年实践，发展了一套“原型”工作方法，通过几何形态分析形成建筑体量原型，基于场地条件演绎原型，从而穷举初步方案。数字化和人工智能技术的进步为这一方法带来新机遇：能够将原型进一步抽象和提炼，组合出更多新颖的建筑体量方案；能够快速大量执行，提高方案选优迭代的规模和速度。本课题结合数字化与人工智能技术，开发建筑体量原型智能演绎工具，为原创设计方案试排体制增效，弥补了通用大模型底层逻辑的不足，进一步推动原创设计的发展。

<div class="page-break"></div>

<h5 class="chapter-heading-left">建筑体量原型</h5>

建筑体量原型是指具备匹配特定设计条件潜力的、可重复使用的形式特征与操作规则。它们通常从既往设计案例中提取，并可在条件匹配时应用于新项目。为满足创新设计需求，基于原型的方法已在建筑师长期实践中逐步完善。通过对典型案例的几何分析，本研究已建立涵盖数百个代表性建筑体量原型的集合，其中凝练了核心建筑知识。在新项目启动时，建筑师可选取潜在适用的原型，置于新场地语境中进行测试，从而全面探索可行方案，进而展开细致讨论与对比分析。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/img%20(3).jpg" note="建筑体量原型的作用原理" %}

<h5 class="chapter-heading-left">流程策划</h5>

生成大量建筑体量方案的操作流程大致如图所示。首先，用户上传任务书和地形图，另外可以手动划定范围、编辑条件、附加文字；而后，由后台程序解析用户输入，为原型网络图结构的节点赋初始权重，根据权重选择排名靠前的原型；由此，触发对应的体量生成程序，基于场地形状和参考指标等入参计算3D体量成果，并通过图像大模型做关键视角的细节赋予；经用户交互确定喜欢、不喜欢的体量方案，遵照衰减函数反向调整网络中的节点权重。上述过程可循环多轮，而得到一系列差异化方案。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/img%20(2).jpg" note="用户操作流程策划" %}

<h5 class="chapter-heading-left">关键方法</h5>

建筑师的设计诠释与原型操作仍具有高度模糊性与不确定性，难以直接对接计算机程序，阻碍了数字化转译与效率提升。为此，本研究构建了一套多层系统以弥合此鸿沟：在建筑师总结的原型与计算设计方法之间插入两个中间层——一层集成生成式设计方法以解决典型建筑设计问题，另一层将解决这些问题的逻辑顺序归纳为若干范式，这些范式同时作为原型的分类依据。通过该框架，大多数建筑原型得以绕过逐一应对模糊性的困境，转化为可执行的计算程序。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/img%20(1).jpg" note="建筑体量原型转译的多层级系统" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/volume-purebox-02.jpg" note="给定指标要求，盒状单一要素在不同地块中的摆放" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-B01-ProtoMass/volume-purebox-01.jpg" note="给定地块，盒状单一要素受指标影响而变化" %}

<h5 class="chapter-heading-left">总结</h5>

项目弥合了计算算法与建筑体量原型方法之间的断层，将创新建筑设计重构为特定场地约束下建筑知识的编码与重用。它不仅拓展了建筑师的创作可能性，减轻了传统创新设计耗时费力的负担，同时也弥补了当前建筑数字化转译中的一项重要不足——现有技术多集中于创新需求较低的项目（如标准化住宅或办公楼层布局），而本项目则直面创新设计的高维挑战。当前项目仍在进行中，新的成果也将在个人网页更新，敬请期待...
