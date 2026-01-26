---
layout: research
projid: R23-C01
abbrev: QuadraBlock
icon-image:
featured-image: http://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R23-C01-QuadraBlock/featured.png
sort-year: 2023
sort-order: 1



title: Flexible Plot-Scale Urban Design Using Quadratic Programming
title-cn: 二次规划求解建筑群生成——以学生公寓为例
team: [ HU Qian, WANG Yujiao, TANG Peng* ]
team-cn: [ 胡潜, 汪瑜娇, 唐芃* ]

tags:
tags-cn: [ 主导, 建筑群生成, 二次规划 ]



location: 
location-cn: 
---

{% include link_citation.html key="PUB-2504-CPE" %}

---

本研究运用数学规划的通用求解方法，解决城市尺度的生成式设计问题。研究以高校生活区为例，该类设计受多重规划指标与多样化场地边界的制约。二次规划为此类问题提供了数学模型，借助先进的数学规划求解器，可自动处理带二次约束的设计生成。然而，其核心挑战在于如何有效构建复杂的场地边界、灵活的建筑模板并处理建筑朝向的可变性。为应对这些挑战，本研究融合了模型内部的表征技术与模型外部的几何算法模块，共同增强二次规划主模型，并构建了一套适用于实际城市设计项目的完整流程。生成结果验证了增强模型及该应用流程的有效性。

This research aims to tackle the problem of the generative urban design of residential areas using a general-solving machine of mathematical programming. Residential areas on university campuses are taken as examples. As a type of urban design problem, the layout of residential areas on campuses is subject to multiple indicators and various boundary shapes. Quadratic Programming (QP) offers a representation of this problem, and with the assistance of cutting-edge mathematical programming solvers, the urban design problem with quadratic constraints can be automatically tackled. However, the difficulties lie in formulating complex boundaries, flexible building templates, and directional variability. To overcome these challenges, this research combines inside-model techniques of representation and outside-model modules utilizing geometric methods to enhance the main model of QP. A pipeline is provided to apply the approach in real urban design projects. The generated results validate the effectiveness of the enhanced model and the pipeline.

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R23-C01-QuadraBlock/pipeline.png" note="模块结构图" %}