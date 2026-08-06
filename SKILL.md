---
name: 3d-layered-network
description: 3D 分层功能网络可视化 skill。把分层系统（6 层架构、流水线、治理链）呈现为可交互的单文件 HTML：层沿 Z 轴、组内径向分布、主节点点击展开子节点、所有边带逻辑词、双主题（黑夜/白天米黄）。触发词：3D 网络、分层网络、功能呈现网络、workflow-3d、主节点展开、逻辑词连线。
version: 1.0.0
---

# 3D 分层功能网络

单文件 HTML，把"分层系统 + 功能节点 + 关联关系"呈现为可交互 3D 网络。参考实现：`examples/workflow-network.html`（工作流功能呈现网络）。

## 适用场景

- 分层架构全景（如 SMEP 六层工作流：编排/记忆/技能/上下文/成本/治理）
- 功能点很多（30+）且天然分层的系统
- 需要"先看骨架（主节点）、点击展开细节（子节点）"的渐进式呈现

## 设计流程（五步）

1. **归层**：把全部功能节点归并到 6 层（或 N 层）。每层一个主节点（层核心），主节点初始可见；子节点默认收起。
2. **定逻辑词**：每条边一个语义动词。层间用工作流真实关系（调度/沉淀/归档/供给/注入/计量/约束/回卷）；子节点间用具体关系（复核下发/审查通道/迁移调用/规则检测…）。没有直接关联不连。
3. **布局**：层沿 Z 轴（z = 层序 × LAYER_SPACING，如 52），组内节点在 XY 同心圆上均匀分布（r = BASE_RADIUS + 步进）。
4. **交互**：点击主节点 toggle 展开/收起该层；点击任意节点右侧面板显示详情（功能/微观设计逻辑/工作流作用/触发条件）；拖拽旋转、滚轮缩放、双击聚焦、自动旋转。
5. **双主题**：黑夜（默认深底）+ 白天（米黄底），节点色双套，CSS 变量切换。

## 关键实现模式（必须遵守）

### 投影居中三件套（否则主体必然偏出屏幕）
```js
// 1) 动态云中心: 可见节点旋转后均值 (mx,my,mz)
// 2) persp 基准 = mz 而非 0: persp = PERSP/(PERSP + (rp.z - mz))
// 3) 屏幕包围盒居中: 求投影点 min/max 中心 (shX,shY), 所有元素减去它
```
- 绝对 z=0 做 persp 基准 → 负 z 节点被放大 2-3 倍推挤出屏（血泪教训）
- 均值中心与包围盒中心在分布不对称时差 35px+，必须加包围盒平移

### 布局与渲染 gotcha
- scene 必须 CSS `position:absolute;inset:0`（父容器 relative），**JS 里禁止改 style.position**，否则高度塌陷为 0、cy=0、节点画到视口外
- 边 = 投影 div：`width=dist;transform:rotate(angle);transform-origin:0 50%`，z-index 取两端 depth 均值
- 节点 z-index 按 depth，边减半避让
- **主节点不能全放 XY 原点**：否则沿 Z 轴叠成一串，高层节点盖住低层，点击被拦截。主节点须螺旋错开（角度 = i/N*2π，半径随层递增）
- 逻辑词标签：边中点 + 法线方向偏移 9px（nx=-dy/dist*9, ny=dx/dist*9），transform:translate(-50%,-50%) 保证文字水平可读，z-index 620（边 500+/节点 1000+ 之间）
- 数据模型：主节点详情放 `G[k].info`（四段数组），子节点详情放独立 `INFO[id]` 四段数组（[功能, 微观设计逻辑, 工作流作用, 触发条件]），updatePanel 统一按 `isMain(id)` 取源

### 交互 gotcha
- `#scene, #scene *{user-select:none}`：否则拖拽旋转时文本被选中，观感崩坏
- 展开状态用 `Set`（expanded layers），边只在两端都可见时渲染
- 点击主节点 = toggle 展开 + 详情；点击空白 = 取消选中
- 子节点 span 设 pointer-events:none 后，Playwright 的 getByText().click() 命中测试会误判"元素被拦截"（真实用户点击无碍，事件冒泡到父 div 的 onclick）。自动化验证用 `span.parentElement.click()` 或直接调 toggleLayer()

### 双主题
- `:root` 黑夜变量 + `body.light` 覆盖（白天米黄底如 `#f5efe0`）
- 节点色双套数组 `COLORS.dark / COLORS.light`，切换时整体重渲
- 左侧渐变背景 `radial-gradient` 也要双套（按主题切换）

## 交付物

- 单文件 HTML（无外部依赖，离线可开）
- 标题用用户指定名（如"工作流功能呈现网络"）
- 右侧解释框：功能 / 内部微观设计逻辑 / 工作流中的作用 / 触发条件 四段
- 验证清单：6 主节点初始可见、点击展开子节点+边带逻辑词、主题切换后配色协调、任意视角下云居中（偏移 ≤2%）

## 参考

- 理论：Sugiyama 分层图（IEEE TSMC 1981）、径向树、Hong & Nikolov 2005 3D 分层
- 演示：`examples/workflow-network.html`
