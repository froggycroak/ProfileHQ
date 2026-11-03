---
layout: research
projid: R25-D01
abbrev: ArchiGeo
permalink: /Research/ArchiGeo/
icon-image: 
sort-year: 2025
include-website: true
include-pdf: false

title: Geometric Toolkit Series for Computational Designers
title-cn: 
tags: [ ]
tags-cn: [ 主导, 计算几何, 生成式设计]
team: [ personal ]
team-cn: [ 个人项目 ]
---

<!-- Research Assignment -->
{% assign UrbageSurvey = site.postresearch | where: "abbrev", "UrbageSurvey" | first %}
{% assign UnitePacking = site.postresearch | where: "abbrev", "UnitePacking" | first %}
{% assign QuadraBlock = site.postresearch | where: "abbrev", "QuadraBlock" | first %}
{% assign AlphaPlot = site.postresearch | where: "abbrev", "AlphaPlot" | first %}
{% assign StandRecons = site.postresearch | where: "abbrev", "StandRecons" | first %}
{% assign OvalAgent = site.postresearch | where: "abbrev", "OvalAgent" | first %}
{% assign ProtoMass = site.postresearch | where: "abbrev", "ProtoMass" | first %}

{% include link_clip.html link='https://pypi.org/project/archigeo/' content='PyPI' %}
{% include link_clip.html link='https://www.nuget.org/packages/ArchiGeoSharp.Basic' content='Nuget' %}
{% include link_clip.html link='https://ags.archifrog.com' content='apidoc' %}
---


<div class="plainpassage-brief">
本文将介绍我开发的计算几何工具箱<strong>ArchiGeo</strong>。ArchiGeo系列工具包主要包含几何类库、优化计算、快速可视化等功能，旨在为建筑设计提供便携、集成、易用的运算功能。目前，工具包主要支持Python和C#调用。
</div>

<h5 class="chapter-heading-left">开发历史</h5>


2023年
archigeo
2025年大量研发依赖于C#语言，ArchiGeoSharp
2026年10月发布nuget包和文档网页。
ArchiGeoNative

<h5 class="chapter-heading-left">Nuget: ArchiGeoSharp</h5>
C#

<h5 class="chapter-heading-left">PyPI: archigeo</h5>
python








