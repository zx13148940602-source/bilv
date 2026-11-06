# 毕旅 · UI 审查与改版记录

> 审查工具：`ui-ux-pro-max`（UI/UX Pro Max，已接入本会话）
> 审查对象：毕旅 Demo v1（藏青 + 琥珀、卡片化移动端 H5）
> 产出：v2 已部署 —— https://zx13148940602-source.github.io/bilv/

---

## 1. 设计系统与取舍

用技能的设计系统生成器跑出的方向：

```bash
python .agents/skills/ui-ux-pro-max/scripts/search.py \
  "graduation trip group travel planning social coordination mobile app" \
  --design-system -p "Bilv" --variance 3 --motion 3 --density 6
```

| 项 | 技能建议 | 本次采纳 |
|---|---|---|
| 风格 | Minimalism & Swiss Style | 采纳：纸感底色、强对比、克制的分隔线、单一主色 |
| 色板 | Adventure orange `#EA580C` + map teal `#0891B2` | 调整为 `#C2410C` + `#0F766E`，满足正文 4.5:1 |
| 背景 | `#FFF7ED` | 采用更柔和的 `#FBF7F2` |
| 字体 | Nunito + DM Sans | 不采纳：不含中文字形，气质偏儿童教育；改用系统中文字体 + 800 字重 |
| 版式模式 | Scroll-Triggered Storytelling | 不采纳：这是落地页叙事模式，不适用于工具型应用外壳 |
| 动效 | 300–400ms 滚动揭示 | 降级为 150–250ms 的按压/弹层反馈，并支持 reduced-motion |

> 结论：采纳「Swiss 极简 + 旅行橙 + 地图青」的视觉骨架，拒绝与产品形态不匹配的落地页叙事模式。

---

## 2. 审查发现（改版前）

| # | 问题 | 违反的规则 | 严重度 | 处理 |
|---|---|---|---|---|
| 1 | 用 📝🗳️💰🧾🔗 等 emoji 当结构图标 | No Emoji as Structural Icons | 高 | 全部替换为统一 1.9px 描边的内联 SVG 图标 |
| 2 | 最小字号 9px，正文 14px | Text < 12px / Base 16px | 高 | 正文 15px，说明文字下限 11px，输入框 16px 防 iOS 缩放 |
| 3 | 灰字灰底：`#6E7787 / #F3F4F7` 仅 4.1:1；浅灰 `#9AA3B2` 仅 2.54:1 | Contrast 4.5:1 | 高 | 语义化 token：muted `#4B5563`（7.09:1）、faint `#6B7280`（4.53:1） |
| 4 | 主色 `#B97227` 白字仅 3.82:1 | 按钮文字对比度 | 高 | 主色改 `#C2410C`（白字 5.18:1） |
| 5 | 标签 30px、选方案按钮 38px、关闭按钮 40px | Touch target ≥44px | 高 | 全部 ≥44px；主按钮 52px；导航项 54px |
| 6 | 待办、快捷入口用 `div` 绑定点击 | Semantic native controls | 高 | 改为 `<button>`，键盘可达 |
| 7 | 没有自定义 focus 样式 | Visible focus | 高 | `:focus-visible` 2px 青色描边 + 2px offset |
| 8 | 弹层无 `role="dialog"`、无 ESC、无焦点管理、背景可滚动 | Modal focus / scrim | 高 | 补 `aria-modal`、焦点移入/陷阱/归还、ESC 关闭、滚动锁定 |
| 9 | 底部导航无选中态语义 | ARIA state | 中 | `role="tablist"` + `aria-selected` + 漫游 tabindex |
| 10 | 表单错误只有 toast | Error near field | 高 | 行内错误 + `aria-invalid` + 聚焦到出错字段 |
| 11 | toast 不会被读屏播报 | Live region | 中 | `role="status" aria-live="polite"` |
| 12 | 无 reduced-motion 适配 | Motion preference | 中 | `prefers-reduced-motion` 下关闭动效与平滑滚动 |
| 13 | 375px 下第 4 个日期 tab 被横向截断 | No hidden content | 中 | 改为 4 等分网格，四个日期全部可见 |
| 14 | 320px 下待办标题出现「截 / 止」孤字 | Orphan line | 中 | 缩短文案 + `text-wrap: balance` |
| 15 | 三个方案全选后三颗橙色按钮，视觉过载 | Visual hierarchy | 中 | 选中改为浅橙底 + 序号角标，主按钮仍唯一 |
| 16 | 结算列出全部 11 笔转账，卡片过长 | Progressive disclosure | 低 | 默认 4 笔，其余收进「查看其余 N 笔」 |

---

## 3. 改版内容

### 3.1 视觉系统

- **色板**：纸感背景 `#FBF7F2`、墨色 `#111827`、旅行橙 `#C2410C`、地图青 `#0F766E`、语义色 success/warn。
- **字体层级**：34（目的地）/ 20（卡片标题）/ 16（区块）/ 15（正文）/ 12（说明），数字统一 `tabular-nums`。
- **空间**：4/8 节奏，区块 24px，卡片 16–18px 内边距，圆角 13/18/24 三档。
- **图标**：统一 24 viewBox、1.9 描边、圆角端点；装饰性图标 `aria-hidden`。
- **信息层级**：首页从「三件同级待办」改为「一件主任务 + 两件次任务」；主任务用墨色反白卡片。

### 3.2 交互与无障碍

- 底部导航支持 `role="tablist"`、`aria-selected` 与 URL hash 深链（`#decide`）。
- 弹层：焦点进入标题、Tab 循环、ESC 关闭、关闭后焦点归还触发按钮。
- 表单：可见 label、行内错误、错误时聚焦字段；金额/名称错误不再只靠 toast。
- 所有可点元素有按压反馈（scale 0.98–0.99 / 背景变化），时长 150–250ms。
- 颜色不作为唯一信息：预算条、票型条、状态标签都带数字或文字。

### 3.3 响应式

- 375px：无横向滚动，四个日期 tab 完整可见。
- 320px：待办、方案卡、弹层均不截断；标题不再出现孤字。
- 底部安全区：`env(safe-area-inset-bottom)`，内容区预留 92px 防遮挡。
- 桌面：480px 手机容器居中，保留产品演示的「手机感」。

---

## 4. 验证记录

| 项 | 结果 |
|---|---|
| 对比度（正文 / 次文 / 主按钮） | 7.09:1 / 4.53:1 / 5.18:1，均 ≥4.5 |
| 最小字号 | 11px（仅说明文字），正文 15px |
| 触控目标 | 标签 44、主按钮 52、关闭 44、导航 54 |
| emoji 结构图标 | 0 |
| 键盘路径 | 导航 → 待办 → 方案 → 排序 → 提交 → 结果标题，均有可见焦点 |
| 弹层 | ESC 关闭、焦点陷阱、背景锁定、焦点归还 |
| reduced-motion | 动效与平滑滚动关闭 |
| 视口 | 375 / 320 截图验证，无横向滚动 |
| 部署 | GitHub Pages，HTTPS，微信内可直接打开 |

截图：`bilv/live375.png`、`bilv/m5-ledger.png`、`bilv/m2-modal.png`。

---

## 5. 已知边界

- 仅提供浅色主题；深色模式需要单独做一轮 token 映射与对比度验证。
- Demo 数据存于 `localStorage`，没有真实多人协作后端。
- 「记一笔」的“按参与人”暂未提供成员勾选，统一按全员计算；这是下一版 P1。
- 结算采用净额贪心配对，优先减少笔数，但不保证数学最优。
