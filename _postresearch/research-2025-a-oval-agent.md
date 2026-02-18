---
layout: research
projid: R25-C01
abbrev: OvalAgent
permalink: /Research/OvalAgent/
icon-image: 
featured-image: https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/featured.png
sort-year: 2025
sort-order: 1
include-website: true
include-pdf: true



title: Intelligent Stadium Bowl Design Assistant with Compound Abilities
title-cn: 体育建筑智能设计助手
tags: [ ]
tags-cn: [ 深度参与, 网页应用, 生成式设计, 体育场馆 ]
team: [ SSTD-DTA ]
team-cn: [ 华建科技数字化所 ]


---

{% include link_citation_pre.html content="李星桥,胡潜,徐晓明. 从工具生产到模式拓新——OVAL AI体育建筑智能设计助手的转型[J]. 建筑技艺,2026(待发表)." note="引用 "%}

{% include link_clip.html link='https://aiovaltool.com/' content='试用链接' %}

---

<div class="plainpassage-brief">OVAL Agent体育建筑智能设计助手是华建科技数字化所开发的智能设计平台，由知名体育建筑设计师Henry Li开创。我在项目中负责的工作包括：后端智能配参系统和几何计算服务的开发；网页产品架构的多项可行性实验；专利/软著/论文的书写等。目前，项目已经完成发布并开启全网公测；同时，项目获得上海市“模塑申城”住建行业人工智能创新应用十佳案例。</div>

<h5 class="chapter-heading-left">项目简介</h5>

体育建筑智能设计助手是面向体育场馆方案设计与前期策划的智能设计平台。平台以AI技术重塑建筑设计流程，融合多智能体协同、大语言模型、生成式设计算法与行业大数据，构建从语义输入到策略推荐、从模型生成到性能分析的一体化智能设计体系。依托覆盖全球144个国家、4000余座场馆与6万余张实景图片的体育建筑数据库，OVAL Agent可理解设计意图，提供专业的看台碗生成式设计与智能分析功能，同时辅以前期策划、案例推荐、知识问答等多维能力，进而形成云端协同的一体化工作环境。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/intro_viewstage.gif" note="智能配参与形态生成"  %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/intro_agent.gif" note="自然语言对话编辑形态"  %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/intro_planning.gif" note="体育建筑项目前期策划"  %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/img%20(1).png" note="网页应用的入口界面" link-jump="https://aiovaltool.com/" %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/sixwebpages.jpg" note="网页应用的各功能页面"  %}

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/sixanalysis.jpg" note="看台碗视觉质量分析功能实例"  %}


<h5 class="chapter-heading-left">关键方法</h5>

项目的技术亮点包括：

- 参数化与业务逻辑结合：通过分层参数体系，将几何生成与设计决策解耦，兼顾灵活性与专业性。
- 数据驱动策略生成：利用随机森林、支持向量回归等模型，从案例数据中学习设计规律，支持智能决策。
- 知识分层建模：显性化编码专项设计知识，构建从语义理解到参数映射的推理链路。
- 云端协同架构：基于Rhino.Compute技术实现稳定的几何服务，支持多用户实时协作与轻量化数据管理。

更为具体地，看台碗生成设计的实现可以分为以下模块：

- 对象描述：建立涵盖场芯、剖面、轴网等192个参数的标准化描述体系，实现设计对象的业务化、可理解表达。
- 知识编码：将专家经验转化为可配置的参数模块，通过特征值控制复杂参数组合，降低专业门槛。
- 策略合成：基于全球体育场馆数据库，采用机器学习算法自动生成设计策略与关键参数。
- 形态编辑：引入自然语言交互与大模型推理，支持通过语义描述直接调整形态，并辅以视觉质量等多维度分析。

{% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/maintecture.png" note="网页应用功能架构图"  %}

<h5 class="chapter-heading-left">总结</h5>

随着数字技术的更新迭代，建筑设计的工具库也日益丰富。然而，建筑智能的探索大多止于工具生产，未能塑造细分领域内的工作模式。本研究提出的OVAL AI体育建筑智能设计助手，是人工智能在体育建筑设计领域的落地应用，体现了以建筑师业务为主体的模式创新。OVAL AI紧跟人工智能技术前沿，通过对象描述、知识编码、策略合成、形态编辑等模块的连接，配合案例推荐、前期策划、知识问答等多种功能，形成云端协同的智能助手。本研究详述了以业务问题为核心，随技术更迭而拓展边界的开发和应用模式，为人工智能时代建筑师业务的数字化转型提供了参考样例。

<strong>附：体育建筑系列开发</strong>

<div class="table-container">
    <table>
        <thead>
            <tr>
                <th>分项名称</th>
                <th>功能描述</th>
                <th>我的工作</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>ConfigManager</td>
                <td>设计参数智能配置</td>
                <td>已完成开发，正在维护</td>
            </tr>
            <tr>
                <td>ModelSculptor</td>
                <td>看台三维模型生成</td>
                <td>已完成开发，正在维护</td>
            </tr>
            <tr>
                <td>SpaceAnalyst</td>
                <td>空间视觉质量分析</td>
                <td>已完成开发，正在维护</td>
            </tr>
            <tr>
                <td>ChatEditor</td>
                <td>语言交互形态编辑</td>
                <td>/</td>
            </tr>
            <tr>
                <td>InterlocHub</td>
                <td>问答数据库</td>
                <td>/</td>
            </tr>
            <tr>
                <td>CaseBase</td>
                <td>案例数据库</td>
                <td>数据补充</td>
            </tr>
            <tr>
                <td>PhotoBase</td>
                <td>图片数据库</td>
                <td>数据补充</td>
            </tr>
            <tr>
                <td>PricePredict</td>
                <td>造价测算（进行中...）</td>
                <td>正在研究</td>
            </tr>
            <tr>
                <td>OvalAcc</td>
                <td>加速版本</td>
                <td>正在研究</td>
            </tr>
            <tr>
                <td>StandRecons</td>
                <td>基于实景图片的看台碗三维重建</td>
                <td>已完成研究</td>
            </tr>
        </tbody>
    </table>
</div>
<!-- {% include figure_full.html link="https://workhub.oss-cn-shanghai.aliyuncs.com/picture/research/imghost-R25-C01-OvalAgent/moresults.jpg" note="生成功能的成果输出示例"  %}-->


