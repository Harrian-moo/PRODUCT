---
name: build-prototype
description: 将PRD或功能方案中的页面交互描述转化为可直接在浏览器中运行的可交互HTML原型文件，使用Tailwind CSS CDN和内联JavaScript实现页面跳转、状态切换、弹窗、动画等交互，不依赖构建工具或后端服务。当需要快速验证产品交互方案、向团队演示页面流程，或为PRD配套可点击原型时使用。原型的终端、视口、品牌和交互规范应根据当前项目资料校准；不替代生产级前端开发。
---

# 可交互原型快速生成

## 目标

把产品方案中的"什么东西在什么位置，点击后发生什么"直接转化为一个能打开、能点击、能演示的 HTML 文件。

交付物：一个独立 HTML 文件，双击即可在浏览器中运行。包含完整的页面结构、交互逻辑和模拟数据，不依赖 npm/构建工具/后端服务。

## 工作边界

本 Skill 负责：

- 将页面交互描述（入口、按钮、弹窗、列表、卡片、切换等）转化为 HTML + Tailwind + 内联 JS。
- 实现页面间的切换（多屏原型）。
- 实现交互状态变化（点击高亮、弹窗打开/关闭、tab 切换、表单输入等）。
- 使用模拟数据填充列表、卡片、图表等组件。
- 实现基本的过渡动画（淡入淡出、滑动等）。

本 Skill 不负责：

- 后端逻辑、数据库操作、API 调用。
- 真实的用户认证或权限控制。
- 生产级响应式和像素级适配；原型只覆盖本次确认的目标终端与关键视口。
- 生产级代码质量——原型代码可以快速、可以 hack，不需要可维护。
- 替代正式的前端开发。

## 基本原则

1. **只做交互原型，不做产品**——能用模拟数据绝不用真实逻辑。
2. **一个文件走天下**——所有 HTML/CSS/JS 在一个文件中，Tailwind 用 CDN。
3. **优先用 Tailwind CDN**——`<script src="https://cdn.tailwindcss.com"></script>` 一行搞定样式。
4. **先校准目标终端**——根据项目确定移动端、桌面端、平板或小程序视口；未说明时先做最小确认，不擅自固定终端。
5. **交互优先于视觉**——按钮能点、弹窗能开关、列表能切换，比颜色好看更重要。
6. **模拟数据要有代表性**——使用不指向真实个人或业务事实的示例数据，并在原型中明确其模拟性质；避免“测试1”“xxx”等无意义占位符。

## 工作流程

### 1. 确认原型范围

从 PRD 或方案描述中提取：

- **需要几个页面/屏幕**（如：主页面、详情弹窗、表单页、管理后台页）。
- **每个页面的核心交互**（点击什么 → 发生什么）。
- **需要展示的数据**（列表项、卡片内容、图表数据等）。
- **关键的过渡和动画**（弹窗进出、tab 切换、loading 状态）。

如果描述不完整，补充 2-3 个问题确认（如"这个弹窗有关闭按钮吗？""点击后是跳转新页面还是弹窗？"）。

### 2. 生成原型

#### 技术选型

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>原型 - [模块名]</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>/* 目标视口 + 自定义动画 */</style>
</head>
<body>
  <!-- 所有页面内容 -->
  <script>/* 所有交互逻辑 */</script>
</body>
</html>
```

#### 交互实现方式

- **页面切换**：用显示/隐藏 div，不用路由。每个页面一个 `<div id="page-xxx">`。
- **弹窗**：固定定位的遮罩层 + 居中弹窗，`display:none` ↔ `display:flex` 切换。
- **状态切换**：用 JS 切换 class 或内联样式。
- **列表数据**：用 JS 数组存储模拟数据，`forEach` 渲染。
- **动画**：用 CSS transition 或 Tailwind 的 `transition` 类。

#### 视觉规范

- 页面宽度：依据目标终端和参考设计设置；移动端可使用项目确认的设备宽度，桌面端按关键断点展示。
- 颜色与字体：优先使用当前项目的品牌和设计规范；未提供时使用中性、可访问的基础样式，并标记为临时视觉方案。
- 圆角：卡片和按钮统一 `rounded-2xl` 或 `rounded-3xl`。
- 字体：系统默认中文字体栈。
- 图标：用 emoji 代替（🍽🏃📋🎯），如果需要精致图标用内联 SVG。

### 3. 写入文件并告知路径

生成后写入用户指定的目录（默认写到当前工作目录）。文件名格式：`prototype-[模块名]-[日期].html`。

告知用户：
- 文件路径
- 如何打开（双击或用浏览器打开）
- 页面间的导航说明

## 页面模板速查

### 多页面容器

```html
<div id="app" class="mx-auto min-h-screen bg-gray-50 relative overflow-hidden">
  <!-- 页面1 -->
  <div id="page-main" class="absolute inset-0 bg-white"></div>
  <!-- 页面2（默认隐藏） -->
  <div id="page-detail" class="absolute inset-0 bg-white hidden"></div>
  <!-- 弹窗遮罩（默认隐藏） -->
  <div id="modal-fortune" class="fixed inset-0 bg-black/80 z-50 hidden items-center justify-center"></div>
</div>
```

### 页面切换函数

```javascript
function showPage(pageId) {
  document.querySelectorAll('[id^="page-"]').forEach(p => p.classList.add('hidden'));
  document.getElementById(pageId).classList.remove('hidden');
}
function toggleModal(modalId) {
  const el = document.getElementById(modalId);
  el.classList.toggle('hidden');
  el.classList.toggle('flex');
}
```

### 列表渲染函数

```javascript
const dishes = [
  { name: '红烧肉', tag: '经典', emoji: '🥩' },
  { name: '地三鲜', tag: '素食', emoji: '🍆' },
];
function renderDishes() {
  const container = document.getElementById('dish-list');
  container.innerHTML = dishes.map(d => `
    <div class="min-w-[140px] p-4 rounded-3xl bg-white shadow-sm">
      <div class="text-2xl mb-2">${d.emoji}</div>
      <div class="font-bold text-sm">${d.name}</div>
      <span class="text-xs text-gray-400">${d.tag}</span>
    </div>
  `).join('');
}
```

## 常见交互模式

| 需求 | 实现 |
|------|------|
| 横向滑动卡片 | `overflow-x-auto flex gap-4 snap-x` |
| 底部 Tab 切换 | 固定底部导航栏 + showPage() 切换 |
| 全屏遮罩弹窗 | `fixed inset-0 bg-black/80 z-50 flex items-center justify-center` |
| 日历日期选择 | 7 个 div 循环，点击切换 active 样式 |
| 表单输入 | `<input>` + 实时更新显示值 |
| 计数器增减 | 两个按钮 + 中间数字 + JS 增减 |
| Loading 动画 | CSS spinner + 2 秒定时器模拟加载完成 |
| Toast 提示 | 固定定位顶部居中，2 秒后自动消失 |

## 输出要求

- 一个独立 HTML 文件，双击可打开。
- 所有 CSS 通过 Tailwind CDN + 少量内联 `<style>`。
- 所有 JS 内联在 `<script>` 标签中。
- 模拟数据放在 JS 数组/对象中，不写死在 HTML 里。
- 页面命名清晰（id="page-main", id="page-admin"）。
- 在文件头部注释说明页面结构和导航方式。

## 质量检查

1. 文件是否双击即可在浏览器中正常打开。
2. 所有按钮/卡片/入口是否有可点击的交互反馈。
3. 弹窗是否能正确打开和关闭。
4. 页面切换是否流畅且可往返。
5. 模拟数据是否具有代表性、已明确为模拟且不冒充项目事实。
6. 是否避免了“测试1”“xxx”等无意义占位文案。
7. 视口和布局是否符合本次确认的目标终端。
