---

layout: post_a
abbrev: FreePacking
tags: 技术分享
year: 2026

title: Using AngleGrid and RectAgent to Generate Building Site Plans
title-cn: 在总平面图排布中运用角度场和矩形代理
team:
team-cn: 胡潜

---
---

<p class="plainpassage-brief">
本文供建筑原型智能设计项目[ProtoMass]({{ProtoMass.url | prepend: site.baseurl}})算法脚本开发人员参考：如何用向量场让底面矩形构成符合建筑设计要求的总平面布局。阅读本文档前需要了解自研计算几何库ArchiGeoSharp和建筑体量原型的核心定义ProtoMass.Core。
</p>
<h5 class="chapter-heading-left">目录</h5>

- [1 总述](#1总述)
  - [1.1 它解决什么问题](#11-它解决什么问题)
  - [1.2 分层：ags → utils → syntax → 脚本](#12-分层ags--utils--syntax--脚本)
  - [1.3 脚本最小引用](#13-脚本最小引用)
- [2 AngleGrid 相关](#2anglegrid-相关)
  - [2.1 AngleGrid API](#21-anglegrid-api)
  - [2.2 AngleGridExtension API（约束打标）](#22-anglegridextension-api约束打标)
  - [2.3 AngleGridExtension2 API（平滑）](#23-anglegridextension2-api平滑)
  - [2.4 相关 Angle 行为](#24-相关-angle-行为)
  - [2.5 进阶：自定义约束后再平滑](#25-进阶自定义约束后再平滑)
- [3 RectAgent 相关](#3rectagent-相关)
  - [3.1 设计约定](#31-设计约定)
  - [3.2 构造、继承、状态](#32-构造继承状态)
  - [3.3 积分：UpdatePos / UpdateDir](#33-积分updatepos--updatedir)
  - [3.4 力：Acc_*（直接累加）](#34-力acc_直接累加)
  - [3.5 力：GetAcc_*（返回向量）](#35-力getacc_返回向量)
  - [3.6 标准迭代循环](#36-标准迭代循环)
  - [3.7 RectAgentExtension / RectAgent2](#37-rectagentextension--rectagent2)
- [4 Util 和 Syntax 的具体实现](#4util-和-syntax-的具体实现)
  - [4.1 为什么必须两层一起看](#41-为什么必须两层一起看)
  - [4.2 DistributionScheme.FieldDir_*](#42-distributionschemefielddir_)
  - [4.3 造「初始矩形」](#43-造初始矩形)
  - [4.4 Syntax：模块复制](#44-syntax模块复制)
  - [4.5 Syntax：基座散置](#45-syntax基座散置)
  - [4.6 脚本完整片段](#46-脚本完整片段)
- [5 其他附加内容](#5其他附加内容)
  - [5.1 Volume 侧：场 + BSP 剖分](#51-volume-侧场--bsp-剖分)
  - [5.2 常见问题](#52-常见问题)
  - [5.3 API 速查](#53-api-速查)

---

<h5 class="chapter-heading-left" id="1总述">1 总述</h5>

<span id="11-它解决什么问题"></span>

**1.1 它解决什么问题**

场地边界往往是斜边、折线，不是正交网格。体块如果全部朝北，边会对不齐红线；如果各自乱转，整体又会显得散。

`AngleGrid` 是一张铺在场地上的 **90° 周期方向场**（交叉场 / 张量场）：

- 每个格点存一个 `[0, 90)` 的角度，表示「这里的正交十字朝哪」。
- 边界附近的十字会贴着边界走，内部会平滑过渡。
- 矩形体块取场上的方向后，长边/短边会跟着红线转，而不是死守世界坐标轴。

它不是普通向量场：`0°` 与 `90°` 表示同一组正交轴（只是把哪条边当「宽」对调了）。所以格点写入时会自动 `Mod90`。

```
红线边方向 ──约束──► 格点打标 ──MathematicalSolver 平滑──► AngleGrid
                                              │
                    ┌─────────────────────────┴─────────────────────────┐
                    │  GetAngle(点)          流线 / 十字可视化            │
                    │  RectAgent 随场转向     BSP 沿场剖分                │
                    └───────────────────────────────────────────────────┘
```

<span id="12-分层ags--utils--syntax--脚本"></span>

**1.2 分层：ags → utils → syntax → 脚本**

脚本开发要同时看两层，不要只抄 syntax 里的两行，也不要绕过 utils 自己重写推挤。

```
┌─────────────────────────────────────────────────────────────────┐
│ ArchiGeoSharp                                                     │
│   ArchiGeo.Basic.Geometry.AngleGrid                               │
│   ArchiGeo.Basic.Geometry.AngleGridExtension   约束打标           │
│   ArchiGeo.MathematicalSolver.AngleGridExtension2  平滑 / 一键生成  │
└───────────────────────────────┬─────────────────────────────────┘
                                │ PolySmoothGrid / GetAngle / SmoothGrid
┌───────────────────────────────▼─────────────────────────────────┐
│ ProtoMass.Library · utils                                        │
│   RectAgent1（质点推挤 + UpdateDir 随场转向）                      │
│   DistributionScheme.FieldDir_SeparatePos_AnySize_RectsIn         │
│   职责：矩形代理如何「跟着场转 + 互不重叠 + 不越界」               │
└───────────────────────────────┬─────────────────────────────────┘
                                │ 先造初始矩形，再交给场驱动松弛
┌───────────────────────────────▼─────────────────────────────────┐
│ ProtoMass.Library · syntax / volume                               │
│   SyntaxFreeRepeats / SyntaxFreeSimilar / SyntaxMixedTemplate     │
│   VolumeSubdivideOffset（场 + BSP 剖分）                          │
│   职责：场地指标 → 数量/尺寸 → 调用 utils → 得到底面矩形 / VBB    │
└───────────────────────────────┬─────────────────────────────────┘
                                │ 脚本照同一条链写，只是类型带 Geo. 前缀
┌───────────────────────────────▼─────────────────────────────────┐
│ scripts/template/*.cs                                             │
│   你实现 ISyntaxBox / ISyntaxMixed / IVolume*                      │
└─────────────────────────────────────────────────────────────────┘
```

一句话：

- **ags**：场从哪来、某点朝哪、怎么可视化。
- **utils**：矩形怎么跟着场走（转向、推挤、间距）。
- **syntax**：什么时候建场、初始矩形怎么抽样、再交给 utils。
- **脚本**：复用上面三层，不要重新发明推挤循环。

本文后4章对应：2 = ags 场；3 = 矩形代理；4 = utils 方案 + syntax 如何串起来；5 = BSP、FAQ、速查。

<span id="13-脚本最小引用"></span>

**1.3 脚本最小引用**

`PolySmoothGrid` / `SmoothGrid` 在 **MathematicalSolver 求解器程序集**里，不是 `AngleGrid` 所在的 Basic 程序集。缺任一引用，扩展方法会「找不到」。

```csharp
#r "ref/ProtoMass.Public.dll"
#r "ref/ProtoMass.Library.dll"
#r "ref/ArchiGeoSharp.Basic.dll"
#r "ref/ArchiGeoSharp.MathematicalSolver.dll"
#r "ref/ArchiGeoSharp.OpenNurbs.dll"
#r "ref/ArchiGeoSharp.Depute.dll"

using ArchiGeo.MathematicalSolver;        // PolySmoothGrid / SmoothGrid
using Geo = ArchiGeo.Basic.Geometry;      // AngleGrid / Polyline / Rect
using ProtoMass.Utils;                    // DistributionScheme / RectAgent1
```

脚本里 Rhino 的 `Polyline` 与 ArchiGeo 的 `Polyline` 重名，所以几何类型一律写 `Geo.Polyline`、`Geo.AngleGrid`、`Geo.Rect`。

库内等价写法是直接 `using ArchiGeo.Basic.Geometry;`，因此没有 `Geo.` 前缀。逻辑相同。

本机需能加载 **MathematicalSolver**。`SmoothGrid` 默认时限 10 秒；场地很大时第一次调用会明显停顿。

---

<h5 class="chapter-heading-left" id="2anglegrid-相关">2 AngleGrid 相关</h5>

网格覆盖一个轴对齐矩形：原点 `BasePt`，格距 `Unit`，X 向 `XUnitCount` 格、Y 向 `YUnitCount` 格。格点矩阵尺寸是 `(XUnitCount+1) × (YUnitCount+1)`。

<span id="21-anglegrid-api"></span>

**2.1** `AngleGrid` **API**

命名空间：`ArchiGeo.Basic.Geometry`。

**构造**

```csharp
new Geo.AngleGrid(int xUnitCount, int yUnitCount, double unit)
new Geo.AngleGrid(int xUnitCount, int yUnitCount, double unit, Geo.Point basePt)
new Geo.AngleGrid(Geo.Angle[,] valueMatrix, double unit)   // BasePt = 原点
```

新建的网格角度全是默认 `0°`。不打约束、不 `SmoothGrid`，全场都是正交世界轴，没有「贴边」效果。

**属性**


| 成员                          | 类型         | 含义                    |
| --------------------------- | ---------- | --------------------- |
| `AngleMatrix`               | `Angle[,]` | 格点角度矩阵                |
| `this[int i, int j]`        | `Angle`    | 读写格点；**写入自动** `Mod90` |
| `Unit`                      | `double`   | 格距                    |
| `ColCount` / `RowCount`     | `int`      | 格点数（= 格数 + 1）         |
| `XUnitCount` / `YUnitCount` | `int`      | 格数                    |
| `Width` / `Height`          | `double`   | 覆盖宽高 = 格数 × `Unit`    |
| `BasePt`                    | `Point`    | 网格原点（通常取红线 AABB 角点）   |


索引 `i` 沿 X，`j` 沿 Y。越界读写会抛异常。

**查询**

```csharp
Geo.Point  GetPoint(int i, int j)
Geo.Angle  GetAngle(int i, int j)
Geo.Angle  GetAngle(int i, int j, double rx, double ry)  // 格内双线性；rx,ry ∈ [0,1]
Geo.Angle  GetAngle(Geo.Point pt)                        // 世界坐标 → 插值角度
bool       PtInRange(Geo.Point pt)
```

脚本里几乎只用 `GetAngle(pt)`：给矩形中心，得到该处十字方向。点在网格外时返回默认角 `0°`。

插值在「四倍角」空间里做，所以 `0°` 与 `89°` 会被当成接近的方向（它们对应几乎同一组十字），而不是差了 89 度的两个向量。

取到的角度仍在 `[0, 90)`。矩形真正朝向常再加 `k * 90`（`k = 0,1,2,3`），表示把哪条边当宽。

**流线与可视化**

```csharp
Geo.Polyline GenerateSinglePath(
    Geo.Point startPt,
    double stepDist = 1,
    Geo.Angle minAngle = default,   // 起点限制在 [minAngle, minAngle+90)
    int countLim = 100)

Geo.LineSeg[] GenerateCrossRep(
    double repUnit = 0,             // 采样间距；≤0 则用 Unit
    double crossLength = 0)         // 十字臂长；≤0 则为 repUnit/5
```

- `GenerateCrossRep`：在覆盖范围内铺十字线段，适合画到 Rhino 里检查场是否贴边、内部是否平滑。
- `GenerateSinglePath`：从一点沿场走，每步把下一角限制在当前角 ±45° 的 90° 周期内，避免突然翻转 90°。`countLim` 是步数上限，不是长度上限。

日常排布 **不必** 自己走流线；Library 的 BSP 剖分在内部沿场割缝。需要看场长什么样时，用十字表示即可：

```csharp
Geo.LineSeg[] crosses = ag.GenerateCrossRep(repUnit: 8, crossLength: 2);
var doc = RhinoDoc.ActiveDoc;
for (int i = 0; i < crosses.Length; i++)
{
    var a = crosses[i].StartPt;
    var b = crosses[i].EndPt;
    doc.Objects.AddLine(new Rg.Point3d(a[0], a[1], 0), new Rg.Point3d(b[0], b[1], 0));
}
```

<span id="22-anglegridextension-api约束打标"></span>

**2.2** `AngleGridExtension` **API（约束打标）**

命名空间：`ArchiGeo.Basic.Geometry`  
作用：在平滑之前，把「这些格点必须朝哪」写进网格，并打标。

打标用的是 **静态** `MarkMatrix`。同一时刻只能服务一张正在编辑的网格；每次新场必须先 `InitMarkMatrix()`。


| 方法                     | 签名                                                                             | 作用                            |
| ---------------------- | ------------------------------------------------------------------------------ | ----------------------------- |
| `InitMarkMatrix`       | `void InitMarkMatrix(this AngleGrid ag)`                                       | 分配全 `false` 的标记矩阵，尺寸与 `ag` 一致 |
| `ReadMark`             | `bool ReadMark(this AngleGrid ag, int i, int j)`                               | 该格是否被约束                       |
| `AddDirectNomination`  | `void AddDirectNomination(this AngleGrid ag, int i, int j, Angle angle)`       | 指定格点角度（`Mod90`）并打标            |
| `AddLineSegConstraint` | `void AddLineSegConstraint(this AngleGrid ag, LineSeg ls, double bufferDist)`  | 线段缓冲带内格点对齐线段方向                |
| `AddPolyConstraint`    | `void AddPolyConstraint(this AngleGrid ag, IPoly poly, double bufferDist)`     | 折线每一段都做线段约束（红线贴边）             |
| `AddRadialConstraint`  | `void AddRadialConstraint(this AngleGrid ag, Point center, double bufferDist)` | 圆心缓冲区内格点朝向径向（再 `Mod90`）       |
| `AddRadialConstraint`  | `void AddRadialConstraint(this AngleGrid ag, ICircleExpr c)`                   | 同上，半径取圆半径                     |




`bufferDist` 是半带宽：线段约束内部会构造「长 = 线段长、宽 = `2 * bufferDist`」的定向矩形，落在里面的格点被钉死。

只打标、不平滑：被钉死的格点有方向，其余仍是 `0°`，场是硬切的。要内部过渡，必须再调用下一节的 `SmoothGrid`。

<span id="23-anglegridextension2-api平滑"></span>

**2.3** `AngleGridExtension2` **API（平滑）**

命名空间：`ArchiGeo.MathematicalSolver`  
作用：把已打标的网格交给 MathematicalSolver，最小化相邻格点方向差，得到平滑场。

`SmoothGrid`

```csharp
Geo.AngleGrid SmoothGrid(this Geo.AngleGrid input, double timeLim = 10)
```

标准四步（扩展方法都挂在 `AngleGrid` / `Polyline` 上，不必写类名）：

```csharp
Geo.AngleGrid input = new Geo.AngleGrid(20, 20, 30, basePt);
input.InitMarkMatrix();
input.AddPolyConstraint(boundary, bufferDist);
input.AddRadialConstraint(center, radialBuffer);   // 可选
Geo.AngleGrid ag = input.SmoothGrid();             // 可传 timeLim
```

返回 **新网格**，不改 `input`。未打标的格点由求解器填；已打标的格点保持约束方向。

`PolySmoothGrid`**（脚本默认入口）**

```csharp
Geo.AngleGrid PolySmoothGrid(this Geo.Polyline poly)
```

内部自动：

1. 取 `poly` 的轴对齐包围盒（AABB）
2. `unit = (int)(AABB.W / 20)`，大约 20 格宽
3. 网格原点 = AABB 角点
4. `InitMarkMatrix` + `AddPolyConstraint(poly, unit * 2)` + `SmoothGrid()`

红线贴边、内部平滑，这一条就够大多数 Syntax。

场地 AABB 宽度小于 20 时 `unit` 会变成 `0`，随后会除零。控制线过小或单位不是米时不要用这一键方法，改用手造网格（见 [2.5](#25-进阶自定义约束后再平滑)）。

`PolyAxisSmoothGrid`

在红线约束之外，再钉一批轴线：

```csharp
Geo.AngleGrid PolyAxisSmoothGrid(this Geo.Polyline poly, Geo.LineSeg[] axes, double[] axesbuffer = null)
Geo.AngleGrid PolyAxisSmoothGrid(this Geo.Polyline poly, ILineExpr[] axes, double[] axesbuffer = null)
```

- `LineSeg[]`：每条轴线按线段缓冲打标。
- `ILineExpr[]`：无限长直线，只取与 AABB 的两个交点再当线段。不相交则跳过。
- `axesbuffer` 可空，空则全 0；实际缓冲为 `unit * 2 + axesbuffer[i]`。

有明确道路、景观轴、建筑主轴时用这个，比纯 `PolySmoothGrid` 更能「顺着轴排」。

<span id="24-相关-angle-行为"></span>

**2.4 相关** `Angle` **行为**

读场、写约束时会碰到这些：


| 成员                    | 含义                                                              |
| --------------------- | --------------------------------------------------------------- |
| `Mod90`               | 收到 `[0, 90)`。格点写入、约束写入都走它                                       |
| `limitTo(min, cycle)` | 收到 `[min, min+cycle)`。`UpdateDir` 用 `limitTo(当前-45°, 90°)` 防止跳轴 |
| `UnitVec`             | 单位方向向量，画十字、走流线用                                                 |
| `angle + k * 90`      | 在同一组十字上改「哪条边当宽」                                                 |


矩形初始朝向用 `GetAngle(pt) + k*90` 是刻意的：场只决定正交框架，不决定宽边朝哪一侧。

<span id="25-进阶自定义约束后再平滑"></span>

**2.5 进阶：自定义约束后再平滑**

`PolySmoothGrid` 不够时（要径向、要轴线、要改格距），手走四步。

```csharp
Geo.Rect aabb = siteBoundary.AxisAlignedBoundingBox();
double unit = Math.Max(4, aabb.W / 20);            // 避免 unit==0
Geo.Point basePt = aabb.BasePt;
int nx = (int)(aabb.W / unit) + 1;
int ny = (int)(aabb.D / unit) + 1;

Geo.AngleGrid input = new Geo.AngleGrid(nx, ny, unit, basePt);
input.InitMarkMatrix();
input.AddPolyConstraint(siteBoundary, unit * 2);

// 可选：景观轴
input.AddLineSegConstraint(axisSeg, unit * 2);

// 可选：圆心辐射（庭院、塔楼）
input.AddRadialConstraint(courtCenter, courtRadius);

Geo.AngleGrid ag = input.SmoothGrid(timeLim: 10);
```

有一批轴线时优先 `PolyAxisSmoothGrid`，少写样板代码。

`InitMarkMatrix` 必须在任何 `Add*Constraint` 之前调用。标记矩阵是静态的：连续造两张场时，第二张也要重新 `InitMarkMatrix`，否则会读到上一张的打标。

造好的 `ag` 再交给第4章的 `FieldDir_*`（或自己用 `RectAgent1`）。

---

<h5 class="chapter-heading-left" id="3rectagent-相关">3 RectAgent 相关</h5>

场造好后，矩形如何跟着走，由 `ProtoMass.Utils.RectAgent1` 负责。日常排布优先走第4章的 `DistributionScheme.FieldDir_*`（内部就是它）；只有要改力、改停止条件、或可视化推挤过程时，才直接调本节 API。

命名空间：`ProtoMass.Utils`。脚本里已有 `using ProtoMass.Utils;` 即可。

<span id="31-设计约定"></span>

**3.1 设计约定**

`RectAgent1` 继承 `Geo.Rect`，在矩形上加了质点动力学。约定：


| 做                                  | 不做               |
| ---------------------------------- | ---------------- |
| 平移（质心运动）                           | 伸缩（`W` / `D` 不变） |
| 推挤产生力，沿中心连线分开                      | 推挤产生转矩（不会被撞转）    |
| 方向只跟**位置**有关：随 `AngleGrid` 转，或始终不变 | 方向跟邻居碰撞有关        |


需要「被撞会转」时用 `RectAgent2`——该类目前只有构造函数，**尚未实现**，不要用。

<span id="32-构造继承状态"></span>

**3.2 构造、继承、状态**

```csharp
public RectAgent1(Geo.Point center, double width, double depth, Geo.Angle dir)
```

继承 `Rect`，脚本里可直接用这些几何成员：


| 成员                | 含义                  |
| ----------------- | ------------------- |
| `Center`          | 中心点；`UpdatePos` 会改它 |
| `W` / `D`         | 宽、深，过程中不变           |
| `Angle`           | 朝向；`UpdateDir` 会改它  |
| `Area`            | `W * D`             |
| `RelateSimp(...)` | 与另一矩形 / 折线的内含·相交·相离 |


自身状态：


| 成员             | 类型            | 含义                          |
| -------------- | ------------- | --------------------------- |
| `Velocity`     | `Vector`（只读）  | 当前速度                        |
| `Acceleration` | `Vector`（可读写） | 当前加速度；一步结束时 `UpdatePos` 会清零 |
| `ToRect`       | `Rect`        | 拷成普通矩形（丢掉速度）                |


初始速度、加速度都是 `(0,0)`。

<span id="33-积分updatepos--updatedir"></span>

**3.3 积分：**`UpdatePos` **/** `UpdateDir`

每步必须先把力累进 `Acceleration`，再积分。顺序固定为 **先动后转**：

```csharp
void UpdatePos()                 // v += a; Center += v; a = 0
void UpdateDir(Geo.AngleGrid ag) // 随场转向，可省略（方向锁定时不调）
```

`UpdatePos`：把当前加速度积到速度，再把速度积到中心，然后把加速度清零。下一步的力必须重新累加。

`UpdateDir`：`ag.GetAngle(Center)`，再 `limitTo(当前角 - 45°, 90°)`。跟着场转，但不会突然跳 90°。场本身不产生力，只改朝向。

方向锁定（例如全部平行于 OBB）时只调 `UpdatePos`，不要调 `UpdateDir`。`AlignedDir_*` 方案就是这样做的。

<span id="34-力acc_直接累加"></span>

**3.4 力：**`Acc_`***（直接累加）**

把力直接加到自己的 `Acceleration` 上。成对方法会对 **两个** 代理施加大小相等、方向相反的力，每对只调一次。

```csharp
void Acc_VelocityDecay(double decayRate)
void Acc_KeepDistcance(RectAgent1 other, double accVecMag, double minDist = 0)
void Acc_NoOverlap(RectAgent1 other, double accVecMag)
void Acc_InsideBoundary(IPoly ipoly, double accVecMag, double minDistToBoundary = 0)
```

方法名里的 `KeepDistcance` 是库内既有拼写，脚本必须照写。


| 方法                   | 何时有力                                | 方向                            | 成对？                            |
| -------------------- | ----------------------------------- | ----------------------------- | ------------------------------ |
| `Acc_VelocityDecay`  | 始终（速度的反向）                           | `-sign(decayRate) * Velocity` | 否                              |
| `Acc_KeepDistcance`  | 相交，或净距 `< minDist`                  | 沿两中心连线分开                      | 是：`this += acc`，`other -= acc` |
| `Acc_NoOverlap`      | 非 `OUTSIDE`（相交 / 接触）                | 同上                            | 是                              |
| `Acc_InsideBoundary` | 未完全在边界内，或到边距离 `< minDistToBoundary` | 指向场内（沿中心到边界最近点）               | 否                              |


`accVecMag` 是力的模长，不是物理单位加速度。场地变大时要跟着放大，否则代理几乎不动。库内经验值见 [3.6](#36-标准迭代循环)。

`Acc_KeepDistcance` 比 `Acc_NoOverlap` 多一层净距，排布里优先用前者。

源码注释里的推荐套用法（直接累加版）：

```csharp
for (int i = 0; i < agents.Count; i++)
{
    agents[i].Acc_InsideBoundary(boundary, accVecMag, boundDist);
    agents[i].Acc_VelocityDecay(0.5);
    for (int j = i + 1; j < agents.Count; j++)
        agents[i].Acc_KeepDistcance(agents[j], accVecMag, minDist);
}
```

`j` 从 `i + 1` 起，避免同一对加两次（第二次会把力抵消或加倍）。

<span id="35-力getacc_返回向量"></span>

**3.5 力：**`GetAcc_`***（返回向量）**

与上一节一一对应，**只返回向量，不改状态**。库内 `FieldDir_`* 走这条，便于按场地尺度乘 `step`、自己决定加到哪一侧。

```csharp
Geo.Vector GetAcc_VelocityDecay(double decayRate)
Geo.Vector GetAcc_KeepDistcance(RectAgent1 other, double accVecMag, double minDist = 0)
Geo.Vector GetAcc_NoOverlap(RectAgent1 other, double accVecMag)
Geo.Vector GetAcc_InsideBoundary(IPoly ipoly, double accVecMag, double minDistToBoundary = 0)
```

成对力必须两侧都写：

```csharp
Geo.Vector acc = agents[i].GetAcc_KeepDistcance(agents[j], mag, minDist);
agents[i].Acceleration += acc;
agents[j].Acceleration -= acc;
```

`GetAcc_VelocityDecay(0.5)` 返回 `-0.5 * Velocity`。紧接着 `UpdatePos` 会做 `v += a`，等效于每步速度减半。`decayRate` 取绝对值，符号无意义。

两中心重合时，分开方向用随机单位向量，避免除零。

<span id="36-标准迭代循环"></span>

**3.6 标准迭代循环**

手写推挤时按这个骨架。`FieldDir_*` 就是它再加 `UpdateDir`。

```csharp
int n = agents.Count;
double area = Math.Abs(boundary.Area);
int step = (int)(Math.Sqrt(area) / 100) + 1;   // 场地越大，力越大
int maxIter = 10000;

for (int iter = 0; iter < maxIter; iter++)
{
    for (int i = 0; i < n; i++)
    {
        agents[i].Acceleration += agents[i].GetAcc_InsideBoundary(boundary, 1.0 * step, boundDist);
        agents[i].Acceleration += agents[i].GetAcc_VelocityDecay(0.5);
        for (int j = i + 1; j < n; j++)
        {
            Geo.Vector acc = agents[i].GetAcc_KeepDistcance(agents[j], 0.6 * step, rectsDist);
            agents[i].Acceleration += acc;
            agents[j].Acceleration -= acc;
        }
    }
    for (int i = 0; i < n; i++)
    {
        agents[i].UpdatePos();
        agents[i].UpdateDir(ag);   // 方向锁定则删掉这行
    }
}
Geo.Rect[] baseRects = agents.ToRects();
```

库内在 `iter > 100` 之后，若所有代理加速度模长都 `≤ 0.5` 会提前结束。手写时同样建议加停止条件，不要死跑 `maxIter`。

典型系数（与 `FieldDir_*` 一致）：


| 项      | 值                        |
| ------ | ------------------------ |
| 边界力模长  | `1.0 * step`             |
| 间距力模长  | `0.6 * step`             |
| 速度衰减   | `0.5`（每步速度减半）            |
| `step` | `(int)(√场地面积 / 100) + 1` |


<span id="37-rectagentextension--rectagent2"></span>

**3.7** `RectAgentExtension` **/** `RectAgent2`

```csharp
Geo.Rect[] ToRects(this RectAgent1[] agents)
Geo.Rect[] ToRects(this IEnumerable<RectAgent1> agents)
```

交给 `HeightScheme` 之前转成普通 `Rect[]`。`ToRect` 是单个；`ToRects` 是一批。

`RectAgent2`：预留给「推挤产生转矩」的代理，目前不能用。

---

<h5 class="chapter-heading-left" id="4util-和-syntax-的具体实现">4 Util 和 Syntax 的具体实现</h5>

库内 `SyntaxFreeRepeats`（模块复制）与 `SyntaxFreeSimilar`（基座散置）是同一条链，只在「初始矩形」上不同。脚本应复制这条链，而不是只调用 `PolySmoothGrid`。

<span id="41-为什么必须两层一起看"></span>

**4.1 为什么必须两层一起看**


| 只做 AngleGrid             | 只做 RectAgent               |
| ------------------------ | -------------------------- |
| 有场，矩形不会自己排开              | `FieldDir_*` 没有 `ag` 就无法转向 |
| `GetAngle` 得到方向，但重叠、越界还在 | 推挤循环已经写好，不必在 syntax 里复制    |


syntax 的职责只是：**定 n 和尺寸 → 抽样初始矩形 → 建场 → 交给** `FieldDir_`* **→ 赋高**。

`FieldDir_*` 封装了第3章的循环；syntax / 脚本不要再写一遍，除非要改力。

<span id="42-distributionschemefielddir_"></span>

**4.2** `DistributionScheme.FieldDir_`*

大多数脚本停在这一层，不必自己管 `RectAgent1`。

```csharp
public static Geo.Rect[] FieldDir_SeparatePos_AnySize_RectsIn(
    Geo.Polyline boundary,
    Geo.AngleGrid ag,
    Geo.Rect[] rects,
    double rectsDist = 4,     // 矩形之间最小净距
    double boundDist = 2,     // 离红线最小距离
    int maxIter = 1000)
```

输入 `rects` 真正用到的是 **个数 / 长 / 宽**；初始方向和位置会被场和推挤改掉。

内部循环：

1. 每个矩形做成 `RectAgent1`，初始角 = `ag.GetAngle(中心) + k*90`（`k` 随机 0–3）
2. 每步用 `GetAcc_*` 累加：不越界、速度衰减、两两保持间距（系数见 [3.6](#36-标准迭代循环)）
3. `UpdatePos()` 再 `UpdateDir(ag)`
4. 迭代够久且加速度足够小则提前结束
5. `ToRects()` 返回

造好的 `ag` 若来自 [2.5](#25-进阶自定义约束后再平滑) 的自定义场，同样传进本方法。

<span id="43-造初始矩形"></span>

**4.3 造「初始矩形」**

场驱动松弛需要先有一批矩形。Syntax 先抽样、再交给上一节。

```csharp
// 固定宽深、随机位置、随机朝向（模块复制）
Geo.Rect[] RandDir_RandPos_SameSize_RectsIn(
    Geo.Polyline boundary, int rectNum, double rectW, double rectD)

// 面积在 averageArea 附近波动、长宽比可配、随机位置（基座散置）
Geo.Rect[] RandDir_RandPos_RandSize_RectsIn(
    Geo.Polyline boundary, int rectNum, double averageArea,
    double aspectRatioMin = 0.5, double aspectRatioMax = 2.0)
```

`RandSize` 版本实际个数可能少于 `rectNum`（AABB 内随机点多次仍放不进红线时会放弃）。脚本里应对空数组做回退。

尺寸方案（间距、单体宽深）用：

```csharp
SizeNumScheme sns = SizeNumScheme.SetNumAverageRect(siteArea, site.BuildingDensity, n);
// sns.RectSize1 / RectSize2 / SpacingSize / UnitNum
```

<span id="44-syntax模块复制"></span>

**4.4 Syntax：模块复制**

对应 `ISyntaxBox.GetVolumeBoundingBoxes` 的底面排布段（同尺寸 + 场方向）：

```csharp
Geo.Polyline siteBoundary = new Geo.Polyline(site.Boundary, true) ^ 2;
siteBoundary.AntiClockwise();
double siteArea = Math.Abs(siteBoundary.Area);

SizeNumScheme sns = SizeNumScheme.SetNumAverageRect(siteArea, site.BuildingDensity, n);
double unitWidth = sns.RectSize1;
double unitDepth = sns.RectSize2;

Geo.Rect[] initialRects = DistributionScheme.RandDir_RandPos_SameSize_RectsIn(
    siteBoundary, n, unitWidth, unitDepth);

Geo.AngleGrid ag = siteBoundary.PolySmoothGrid();   // ags：红线 → 平滑场

Geo.Rect[] baseRects = DistributionScheme.FieldDir_SeparatePos_AnySize_RectsIn(
    siteBoundary, ag, initialRects, sns.SpacingSize, boundDist: 2, maxIter: 10000);
```

`^ 2` 把点投到 2D。库内 `site.ControlBoundaryPoly2d()` 就是这两行；脚本里扩展方法不一定导出，请手写。

完整可运行文件：`scripts/template/ScriptSyntaxB1cFreeRepeats.cs`。

<span id="45-syntax基座散置"></span>

**4.5 Syntax：基座散置**

唯一差别：初始矩形用面积抽样（变尺寸 + 场方向）。

```csharp
double unitArea = unitWidth * unitDepth;
Geo.Rect[] initialRects = DistributionScheme.RandDir_RandPos_RandSize_RectsIn(
    siteBoundary, n, unitArea);

Geo.AngleGrid ag = siteBoundary.PolySmoothGrid();
Geo.Rect[] baseRects = DistributionScheme.FieldDir_SeparatePos_AnySize_RectsIn(
    siteBoundary, ag, initialRects, sns.SpacingSize, 2, 10000);
```

完整可运行文件：`scripts/template/ScriptSyntaxB1cFreeSimilar.cs`。

<span id="46-脚本完整片段"></span>

**4.6 脚本完整片段**

下面可直接放进 `ISyntaxBox` 实现。前置 `#r` / `using` 见 [1.3](#13-脚本最小引用)。

```csharp
public VolumeBoundingBox[] GetVolumeBoundingBoxes(
    SiteSlice site, double totalAreaOccupyRate, double bottomAreaOccupyRate)
{
    site = site.PreTreat(totalAreaOccupyRate, bottomAreaOccupyRate);

    int n = site.VolumeNum != null && site.VolumeNum.Length == 1 && site.VolumeNum[0] > 0
        ? site.VolumeNum[0]
        : 6;

    Geo.Polyline siteBoundary = new Geo.Polyline(site.Boundary, true) ^ 2;
    siteBoundary.AntiClockwise();
    double siteArea = Math.Abs(siteBoundary.Area);

    SizeNumScheme sns = SizeNumScheme.SetNumAverageRect(siteArea, site.BuildingDensity, n);
    double unitWidth = sns.RectSize1;
    double unitDepth = sns.RectSize2;

    Geo.Rect[] initialRects = DistributionScheme.RandDir_RandPos_SameSize_RectsIn(
        siteBoundary, n, unitWidth, unitDepth);
    if (initialRects == null || initialRects.Length == 0)
        return Array.Empty<VolumeBoundingBox>();

    Geo.AngleGrid ag = siteBoundary.PolySmoothGrid();

    Geo.Rect[] baseRects = DistributionScheme.FieldDir_SeparatePos_AnySize_RectsIn(
        siteBoundary, ag, initialRects, sns.SpacingSize, 2, 10000);

    double coverage = 0;
    for (int i = 0; i < baseRects.Length; i++)
        coverage += baseRects[i].Area;
    if (coverage < 1e-6)
        coverage = n * unitWidth * unitDepth;

    double averageHeight = site.FloorAreaRatio * siteArea / coverage * HeightScheme.FLOOR_HEIGHT;
    return HeightScheme.AllSame(baseRects, site.HeightLim, averageHeight);
}
```

---

<h5 class="chapter-heading-left" id="5其他附加内容">5 其他附加内容</h5>

<span id="51-volume-侧场--bsp-剖分"></span>

**5.1 Volume 侧：场 + BSP 剖分**

`VolumeSubdivideOffset` 不拿场去转矩形，而是拿场去 **沿流线剖分场地**：

```csharp
Geo.AngleGrid field = siteInside.PolySmoothGrid();
var adapt = new Geo.BspAdaptiveOptions
{
    MaxDepth = 3,
    MinLeafArea = areaInside * 0.08,
    MinElongation = 0.28,
    Ratio = 0.5,
};
Geo.Polyline[] blocks = siteInside.SubdivideBspAdaptive(field, adapt).Blocks();
```

`SubdivideBspAdaptive` / `SubdivideBsp` 是 `Polyline` 的扩展方法（Basic）。场决定割缝方向；叶多边形再各自做成体量。这是「场 → 地块」，与第4章「场 → 矩形朝向」并列的第二条产品路径。

<span id="52-常见问题"></span>

**5.2 常见问题**

`PolySmoothGrid` **编译不过 / 运行时找不到扩展方法**  
缺 `using ArchiGeo.MathematicalSolver;`，或没引用 `ArchiGeoSharp.MathematicalSolver.dll`。见 [1.3](#13-脚本最小引用)。

**场全是横平竖直，完全不贴红线**  
只 `new AngleGrid(...)` 就 `GetAngle`，没有 `AddPolyConstraint` + `SmoothGrid`。用 `PolySmoothGrid`，或走 [2.5](#25-进阶自定义约束后再平滑) 四步。

**体块重叠或跑出红线**  
场只负责朝向。位置必须经 `FieldDir_SeparatePos_AnySize_RectsIn`（或自己调 `RectAgent1` 的边界力 / 间距力）。不要只对每个随机点 `GetAngle` 后直接 `new Rect`。

**手写** `RectAgent1` **时矩形对穿、力抵消或飞出**  
成对力（`Acc_KeepDistcance` / `GetAcc_KeepDistcance`）每对只处理一次（`j = i + 1`）。`GetAcc_*` 不会改状态，忘了 `Acceleration +=` 等于没受力。`UpdatePos` 之后加速度已被清零，下一步必须重新累加。`accVecMag` 过小则几乎不动，过大则飞出红线；按 `√面积 / 100` 取 `step`。

`KeepDistcance` **编译不过**  
库内拼写是 `KeepDistcance`（少一个 `a`），不是 `KeepDistance`。

**想改力却去改** `FieldDir_`* **的参数不够用**  
`rectsDist` / `boundDist` / `maxIter` 只覆盖间距、退界、迭代上限。要加新力、改衰减、画力箭头，直接用 `RectAgent1` 按 [3.6](#36-标准迭代循环) 写循环。

`GetAngle` **总是 0**  
点落在网格外（`PtInRange` 为 false）。`PolySmoothGrid` 的网格是红线 AABB；AABB 外的点没有定义。先保证矩形中心在红线内。

**第一次排布很慢**  
MathematicalSolver 在解整张格网。可加大 `unit` 减格点，或把 `SmoothGrid(timeLim)` 调小（质量下降）。`FieldDir_*` 的 `maxIter` 也会耗时间，与求解器是两段。

**场地很小就崩溃**  
`PolySmoothGrid` 用 `(int)(AABB.W / 20)` 当地板格距。宽度 < 20 时为 0。改用手造网格并 `Math.Max` 格距。

**想「全部平行于某一边」而不是平滑过渡**  
不要用场。用 `DistributionScheme.AlignedDir_*` 一类方法，方向取 OBB 或指定 `Angle`。场是给非正交、需要随边旋转的原型用的。

<span id="53-api-速查"></span>

**5.3 API 速查**

**ags ·** `AngleGrid`

```
构造  (xUnitCount, yUnitCount, unit[, basePt]) | (Angle[,], unit)
读写  this[i,j]  Unit  ColCount  RowCount  XUnitCount  YUnitCount
      Width  Height  BasePt  AngleMatrix
查询  GetPoint(i,j)  GetAngle(i,j)  GetAngle(i,j,rx,ry)  GetAngle(pt)  PtInRange(pt)
可视化 GenerateSinglePath(start, stepDist=1, minAngle=0, countLim=100)
      GenerateCrossRep(repUnit=0, crossLength=0)
```

**ags ·** `AngleGridExtension`

```
InitMarkMatrix()  ReadMark(i,j)  AddDirectNomination(i,j,angle)
AddLineSegConstraint(ls, bufferDist)
AddPolyConstraint(poly, bufferDist)
AddRadialConstraint(center, bufferDist) | AddRadialConstraint(circle)
```

**ags ·** `AngleGridExtension2`**（需** `using ArchiGeo.MathematicalSolver`**）**

```
SmoothGrid(timeLim=10)
Polyline.PolySmoothGrid()
Polyline.PolyAxisSmoothGrid(LineSeg[] axes, double[] axesbuffer=null)
Polyline.PolyAxisSmoothGrid(ILineExpr[] axes, double[] axesbuffer=null)
```

**Library ·** `RectAgent1`

```
构造  (center, width, depth, dir)          继承 Rect：Center / W / D / Angle
状态  Velocity  Acceleration  ToRect
积分  UpdatePos()  UpdateDir(ag)
Acc_  Acc_VelocityDecay(decayRate)
      Acc_KeepDistcance(other, mag, minDist=0)     // 成对，每对一次
      Acc_NoOverlap(other, mag)
      Acc_InsideBoundary(poly, mag, minDist=0)
GetAcc_  同上四个，返回 Vector，不改状态
扩展  agents.ToRects()
勿用  RectAgent2（未实现）
```

**Library · utils 方案**

```
DistributionScheme.FieldDir_SeparatePos_AnySize_RectsIn(boundary, ag, rects, rectsDist=4, boundDist=2, maxIter=1000)
DistributionScheme.RandDir_RandPos_SameSize_RectsIn(boundary, n, w, d)
DistributionScheme.RandDir_RandPos_RandSize_RectsIn(boundary, n, averageArea, ...)
SizeNumScheme.SetNumAverageRect(siteArea, density, n)
```

**Library · syntax 调用顺序**

```
site.PreTreat
→ 控制线 2D（脚本手写 Polyline ^ 2 + AntiClockwise）
→ SizeNumScheme 定 n / 宽深 / 间距
→ RandDir_* 初始矩形
→ PolySmoothGrid（或自定义约束 + SmoothGrid）
→ FieldDir_SeparatePos_AnySize_RectsIn
→ HeightScheme 赋高 → VolumeBoundingBox[]
```

