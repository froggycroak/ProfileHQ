---
layout: research
projid: R24-C01
abbrev: AlphaPlot
permalink: /Research/AlphaPlot/
icon-image:
featured-image: https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/featured.png
sort-year: 2024
include-website: true
include-pdf: true

title: Generative Design of Building Volumes Dominated by Corridors
title-cn: 走道式组合主导的建筑体量集合生成研究——以高校生活区为例
team: [ HU Qian, TANG Peng(supervisor) ]
team-cn: [ 胡潜, 唐芃(导师) ]
tags:
tags-cn: [ 硕士论文, 主导, 生成式设计 ]



location:
location-cn:
---

{% include link_citation.html key="PUB-2406-MTC" note="引用 "%}

{% include link_pdf.html content='文献直达-article' link='https://workhub.oss-cn-shanghai.aliyuncs.com/pdf/articles/%E8%AE%BA%E6%96%87%5B%E8%83%A1%E6%BD%9C%5D(2024)M%25%E4%B8%9C%E5%8D%97%E5%A4%A7%E5%AD%A6%E7%A1%95%E5%A3%AB%E8%AE%BA%E6%96%87-%E8%B5%B0%E9%81%93%E5%BC%8F%E7%BB%84%E5%90%88%E4%B8%BB%E5%AF%BC%E7%9A%84%E5%BB%BA%E7%AD%91%E4%BD%93%E9%87%8F%E9%9B%86%E5%90%88%E7%94%9F%E6%88%90%E7%A0%94%E7%A9%B6.pdf'  %}

{% include link_clip.html content='相关推文-post' link='https://mp.weixin.qq.com/s/Kh6RqFmwjeXjkcWCTDvzUQ'  %}

---

<div class="plainpassage-brief">本文是对我硕士论文研究内容的简介。研究以大学校园中的学生公寓组团、生活区建筑群为例，研究走道式组合主导下单一地块上建筑体量集合的生成式设计。理论研究部分从空间组合模式和城市形态现象出发，依据形态类型学理论梳理走道式组合的内在要素、结构、逻辑，并由此引出两个生成实验的研究问题；在此基础上以“类型”语境的探讨回应了研究背景中的现象认知；最后从数据结构和算法范式的角度，探析了实验中可能用到的建模方式和算法工具。</div>

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/thesis-structure.png" note="研究框架与论文目录" %}

<h5 class="chapter-heading-left">研究背景</h5>

走道式组合是一种常见的空间组合模式，其影响能扩展到建筑体量的形态特征，因而产生了走道式组合主导的建筑体量集合。在城市中，这类建筑体量集合普遍存在，且在形态规律和设计方法上呈现出共性，因而具有广泛的研究价值。随着计算机技术应用的普及，程序作为建筑知识载体和建筑设计工具成为可能。通过编写程序来把握一类建筑体量的形态特征，能够将模糊零散的设计知识整合到更为明确的结构中。另一方面，实际设计工作也需要高效的工作流程和合适的辅助工具，来解决与之相关规划和建筑设计问题。

研究所采取的具体问题样例是大学校园中的两类设计问题：生活区设计和学生公寓组团设计。学生公寓所在的生活区作为功能区之一，其建筑布局受多重约束，时常需要设计师“估摸”着尝试求解，生成式设计有助于提升其效率。规模更小的学生公寓组团设计也是校园常见且相对独立的局部设计任务，计算机程序在此过程中有助于探索设计方案并快速提供视觉反馈。在这些设计问题中，设计师需要以跨尺度层级的复合决策，回应多要素限定；实际工作中可能出现设计过程繁琐往复或设计结果简单粗暴的现象。

本研究借助这两个典型设计问题，分析走道式组合主导下的建筑形态规律，探究该类建筑的内在结构和设计方法；而后，应用这些规律和方法解决大学校园中的设计问题，通过生成式设计给出初步方案；进而，突破个体经验和构思的局限，拓展设计思路并提高工作效率。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/background.jpg" note="研究背景" %}

<h5 class="chapter-heading-left">生成实验</h5>

这一部分包含两个实验。学生公寓组团生成实验以规则系统为主要范式，完成了一种学生公寓建筑组团的生成方法。该实验将常见的学生公寓走道生成概括为一系列可操作的步骤，给出了一种由地块形态“译制”走道形态的方法，同时通过引入建筑师常用的“模数”概念简化了转角处理的问题（这是项目AlphaPlot的主要实验）。生活区建筑群生成实验以数学规划为主要模型，转译了高校生活区中的要素和约束条件，借助通用解算器求解问题。该实验尝试了建筑生成设计中应用尚且有限的二次规划连续模型，给出了二次规划模型中复杂凹多边形地块，以及建筑编组这一“类语法”秩序的表述方法，这些内容尚未被解决且具有实际意义。在生成实验的基础上，研究通过应用框架组织算法模块，并在实例中检验可行性（与项目QuadraBlock有所重合）。

<h5 class="chapter-heading-left">实验一：学生公寓组团生成</h5>

学生公寓组团的生成实验主要分为三组功能：参数化模型模块建立建筑体量和走道中心线之间的链接关系，由代表走道的线段组生成具体的方案；距离与组合模块探索给定地块中的走道系统生成方法，给出一套实用、可扩展的生成步骤；适应与调节模块为追求非正交形体变化的设计师提供额外的选择，在距离与组合模块的基础上运用分布式的规则和逐帧迭代的运行方式对建筑形态加以调节。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/experiment-11-ppt.jpg" note="学生公寓组团生成实验的原理解析1" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/experiment-12-ppt.jpg" note="学生公寓组团生成实验的原理解析2" %}

<h5 class="chapter-heading-left">实验二：生活区建筑群生成</h5>

高校生活区建筑群的生成实验主要分为三组功能：基础模型将离散建筑单体在给定地块中的排布转化为一个二次规划问题，并用Gurobi求解器进行求解；附加功能主要是符合指定表达式形式限定下的其他要求添加，例如建筑单体之间两两编组、指定建筑单体的参数限定、不同类型建筑单体的混合等；附加功能中还提供了调节各建筑单体朝向的可能性，是基础模型以外的辅助方法。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R24-C01-AlphaPlot/experiment-2-ppt.jpg" note="生活区建筑群生成实验的原理解析" %}

<h5 class="chapter-heading-left">总结</h5>

本研究从空间组合模式和城市形态现象出发，依据形态类型学理论梳理走道式组合的内在结构和逻辑，并由此引出两个生成实验的研究问题；以规则系统为主要范式，综合几何计算和排列组合方法，给出了一种生成学生公寓建筑组团的方法；以数学规划为主要模型，转译了高校生活区规划设计中的要素本体和约束条件，借助通用解算器自动求解；整合了上述实验的应用框架，将算法模块组织到便于建筑师使用的秩序中，并通过与建筑师的交流和小样试用验证其可行性。研究在建筑形态认知和基于形态特征的生成设计中做出了有益尝试，为大学校园中一些实践问题提供了数字化解决方案，在学科知识转化、算法工具应用、设计生产提效等方面具有创新价值。

<!-- **表 CorriBase系列研究** `编辑中...`

|项目|研究对象||||
|---|---|---|---|---|
|UnitéPacking|建筑内部|马赛公寓等案例|||
|AlphaPlot|建筑单体|高校学生公寓|||
|QuandraBlock|建筑群体|高校学生公寓||| -->
