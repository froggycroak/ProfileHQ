---
layout: research
projid: R22-C01
abbrev: UnitéPacking
permalink: /Research/UnitéPacking/
icon-image:
featured-image: https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R22-C01-UnitéPacking/featured.png
sort-year: 2023
include-website: true
include-pdf: true

title: Rethinking Le Corbusier’s Unités d'Habitation via Integer Programming
title-cn: 整数规划实验 / 柯布西耶"尺度相当的居住单位"的研究
team: [ HU Qian, TANG Peng* ]
team-cn: [ 胡潜, 唐芃* ]

tags:
tags-cn: [ 主导, 《建筑学报》, 生成式设计 ]

---

{% include link_citation.html key="PUB-2402-JAC" note="引用 "%}

<!-- {% include link_clip.html link='' content='演示文件-slides' iconstr='fa-solid fa-file-pdf fa-fw' %} -->

{% include link_pdf.html content='文献直达-article' link='https://workhub.oss-cn-shanghai.aliyuncs.com/pdf/articles/%E8%AE%BA%E6%96%87%5B%E8%83%A1%E6%BD%9C%5D(2024)J%25%E5%BB%BA%E7%AD%91%E5%AD%A6%E6%8A%A5-%E6%A8%A1%E6%95%B0%E3%80%81%E5%B1%82%E7%BA%A7%E3%80%81%E9%87%8F%E5%BD%A2.pdf'  %}

---

<div class="plainpassage-brief">柯布西耶“尺度相当的居住单位”包含高度复杂的空间分配问题。通过整数规划驱动的模板拼贴实验模拟该设计问题的求解，从模数、层级、量形维度讨论整数规划程序与柯布西耶设计思想的关联性，进而窥探柯布西耶对数学工具的运用方式，发掘其中建筑师独有的价值。</div>

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R22-C01-UnitéPacking/prototype-fourcolumn.jpg" note="问题原型：尺度相当的居住单位" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R22-C01-UnitéPacking/cases.png" note="典型案例拆解分析" %}

<h5 class="chapter-heading-left">转译原理</h5>

对于“尺度相当的居住单位”中的模板拼贴问题，通常的描述方法是：模板和容器被相同模数的正交格网划分；模板由一个基准格点和若干依附格点组成；容器每一格点根据是否贴有特定模板的基准格点而被赋值。此时，对于给定的模板类型和容器，将形成一个三维数阵。每个三维数阵可以看作一个“图层”，相互叠加即形成数据意义上的拼贴结果。这类模板拼贴问题的能够被整数规划所描述，并通过调用成熟的计算机程序包高效求解。

在此过程中，需要首先梳理问题中涉及的全部变量，并按照取值范围进行分类；而后，变量被组织成若干待用的代数式并依次序匹配系数，形成方程和不等式并录入程序。一旦所关心的模板拼贴问题被描述成上述形式，计算机程序包便能将复杂的空间分配问题自动地解决。使用者可以根据实际需要编写程序，将所得数据转化成建筑师偏好的图像形式，也便形成图纸意义上的模板拼贴求解结果。

<h5 class="chapter-heading-left">生成实验</h5>

在“尺度相当的居住单元”设计中，设计的限制大多可以被转译为模板相关的语句，如：“所有模板个体位于限定范围内”、“任意两个模板个体相互不重叠”、“每个户型模板至少一处水平与走廊模板相连接”、“交通筒模板和俱乐部模板应当均匀地服务整座建筑”等等。各个类型的句子可以先被转化为图形问题，再由合适数量的参数和方程来描述。在马赛公寓的设计规则中，涉及的图形问题主要包括：不重叠、不越界、单向相邻数量、总数控制等。由此，“尺度相当的居住单元”中颇为重要的户型排布问题方与所述计算机程序产生联系。

以马赛公寓为例，进行“尺度相当的居住单元”生成实验，可得到大量符合要求的“仿制品”。图中展示了部分生成结果。这些生成结果在户型组合、公共空间布置等方面表现出更大程度的多样性和随机性，同时也为技术问题适配带来更大的挑战。从结果来看，整数规划驱动的模板拼贴模型能够模拟马赛公寓中户型排布的基本规律。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R22-C01-UnitéPacking/experiment.png" note="实验原理与测试输出" %}

<!-- <h5 class="chapter-heading-left">模板拼贴实验原理</h5> -->
