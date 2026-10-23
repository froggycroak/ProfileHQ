---
layout: research
projid: R26-C01
abbrev: StockRenew
permalink: /Research/StockRenew/
icon-image: 
sort-year: 2026
include-website: false
include-pdf: false

title: Building Asset Revitalization Toolkit Series
title-cn: 楼宇资产活化更新系列工具
tags: [ ]
tags-cn: [ 进行中..., 主导, 存量楼宇, 策划咨询]
team: [ SSTD-DUR ]
team-cn: [ 华建科技城市更新所 ]
---


---

<div class="plainpassage-brief">
楼宇资产活化更新智能体是华建科技城市更新所面向存量楼宇更新策划业务建设的系列智慧工具，目标是将案例检索、区位、设施研判、场地表达、成本测算等能力逐步集成为一体，并以智能体形态支撑「区位-政策-案例-存量」联动的策划工作流。我在项目中担任系列整体的技术负责人，统筹各模块的技术路线、系统集成与智能体能力编排。系列包含 StocKase 智能案例库、UrbanCheck 公服设施体检平台、RegionShot 场地模型生成工具，以及计划中的成本测算、资产管理、快速方案等模块。
</div>

<h5 class="chapter-heading-left">总体介绍</h5>

城市更新中的楼宇资产活化策划需要大量案例对标、政策与区位研判、空间与存量信息整合，以及可快速迭代的成果表达。通用大模型对话虽便于获取泛化知识，但在专业策划场景中往往缺少结构化案例底座、可验证的数据字段与稳定的多工具协同，难以直接承担从资料筛选到方案编写的完整链路。本系列按业务需求建设可独立使用、又可互通的能力单元，通过 LLM 编排与 MCP 类集成机制整合内外系统，支撑策划侧从资料筛选到成果编写的稳定协同。当前成果已在 WDCC 世界城市日以「上海城市更新智能数据库」名义亮相。

<h5 class="chapter-heading-left">StocKase 城市更新智能案例库与网页应用</h5>

<div class="figures-container">
    {% include figure_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_cases.gif"
        note="案例检索与筛选"
    %}
    {% include figure_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_detail.gif"
        note="案例详情查看"
    %}
</div>

<div class="figures-container">
    {% include figure_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_assistant.gif"
        note="策划智能体"
    %}
    {% include figure_part.html
        link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_map.gif"
        note="综合看板"
    %}
</div>

StocKase 面向城市更新策划阶段，是集案例对标、相似推荐与智能策划于一体的辅助平台，用于补足通用智能体在专业策划问题上的局限。模块通过系统梳理城市更新实践样本，搭建涵盖项目类型、区位条件、技术路径等维度的智能案例图谱。用户可进行多维检索与相似项目对标，查看结构化案例详情，并在综合看板上联动空间与项目信息；策划智能体则依托案例与政策语境，支持政策研判、方案策划与成果应用类任务，缩短从「找案例」到「写策划」的路径。

在整体项目技术路线下，我指导团队成员搭建数据收集 skills，从公开网络文本中分离与策划相关的高价值字段，逐步形成覆盖上海地区典型更新案例、包含 70 余个关键字段的结构化库。系列采用 mcphub 集成分发模式，使各应用与外部系统在能力层互通；策划智能体侧以 LLM 编排将区位、政策、案例与存量信息纳入同一对话与生成流程。在试点项目中，StocKase 显著提高了资料筛选、信息提取与成果编写的效率，策划业务时间消耗约减少 70%。


<h5 class="chapter-heading-left">UrbanCheck 城市区位分析与公服设施体检平台</h5>

UrbanCheck 服务更新前期策划中的微观区位与公服配套研判：按地址或地图点选目标，在圈层内汇总 POI 分布与多维度评分，并给出可读摘要与导出结果，供策划使用。同一套引擎覆盖单点、多点、片区等场景；区位助手依据程序生成的站点事实卡回答用户提问。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_urbancheck.gif" note="UrbanCheck 城市区位分析与公服设施体检平台" %}



<h5 class="chapter-heading-left">StockBudget 楼宇资产投改测算</h5>

StockBudget 用于楼宇资产投资改造的成本和回报计算。目前该平台只置入了不同类型资产的简单计算功能，需要手动填写较多参数。后续将进一步研究数据驱动的智能预测方法，作为现有计算函数的入参，以实现更加高效和准确的测算能力。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/seg_stockbudget.gif" note="StockBudget 楼宇资产投改测算" %}


<h5 class="chapter-heading-left">RegionShot 城市区域场地模型快速生成工具</h5>

RegionShot 用于策划阶段的场地快速表达：按地名或地图圈定范围，从开放地图数据生成周边建筑与道路的简易三维场景，通过规则推算缺少的数据，并实现轻量化传输，网页预览，以及导出 Json/Mesh/Brep 模型，减少手工建模时间。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R26-C01-StockRenew/webview-regionshot-002.png" note="RegionShot 城市区域场地模型快速生成工具" %}