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
tags-cn: [ 主导, 建筑群, 数学规划, 生成式设计 ]



location: 
location-cn: 
---

{% include link_citation.html key="PUB-2504-CPE" note="引用 "%}
{% include link_pdf.html content='文献直达-article' link='http://workhub.oss-cn-shanghai.aliyuncs.com/pdf/articles/%E8%AE%BA%E6%96%87%5B%E8%83%A1%E6%BD%9C%5D(2025)C%25CDRF-Flexible%20Plot-scale%20Urban%20Design%20using%20QP-FullVersion.pdf'  %}


---
<!-- <div class="plainpassage-brief"></div> -->

研究聚焦于生成式城市设计领域，特别是针对高校生活区的建筑布局问题。该研究创新性地采用二次规划（Quadratic Programming, QP）作为数学编程工具，结合通用求解器（如Gurobi）来实现设计方案的自动生成。研究核心在于通过QP模型将复杂的城市设计约束（如建筑密度、容积率、日照间距等）转化为数学表达式，从而摆脱传统手工设计的迭代过程，提升设计效率。这种方法允许设计师专注于问题形式化而非求解过程，为处理多指标、多边界形状的城市设计问题提供了新思路。

研究的关键突破体现在QP模型的具体构建上。模型首先定义了灵活的建築模板，其中宿舍建筑通过变量（如房间数量nₕ、nₗ）参数化表示深度、长度和高度，并引入控制区域概念来处理建筑间的防火和日照距离约束。针对复杂边界形状，研究提出了凹多边形分解方法，将不规则边界转化为凸多边形组合，确保数学表达式不超出二次约束限制。这些技术创新使模型能够适应真实的校园地块形状，并生成符合规范的设计方案。

此外，研究通过外部模块扩展了QP模型的灵活性。建筑对齐模块实现了规则式布局生成，向量场方法解决了建筑方向多样化问题，最终形成完整的建模管道。这些增强措施使生成结果更贴近人工设计效果，验证了该方法在真实设计项目中的实用性，为生成式城市设计提供了可扩展的解决方案框架。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R23-C01-QuadraBlock/pipeline.png" note="模块结构图" %}


