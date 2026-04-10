# rustdesk-api-web 重构设计文档

> 本文档记录对 rustdesk-api-web 项目进行的所有视觉与交互重构，便于后期追溯与维护。
> 
> **最后更新**：2026-04-11
> **部署地址**：https://claw.u1so1.eu.org/_admin/

---

## 一、全局设计系统

### 1.1 CSS 变量体系

**文件**：`src/styles/style.scss`

定义了项目级 CSS 变量，统一管理颜色、阴影、圆角：

| 变量 | 值 | 用途 |
|------|-----|------|
| `--gray-bg` | `#F2F3F5` | 页面底层灰 |
| `--gray-white` | `#FFFFFF` | 卡片白 |
| `--gray-1` | `#1D2129` | 标题/设备名（深枪灰） |
| `--gray-2` | `#4E5969` | 正文/IP（中灰） |
| `--gray-3` | `#86909C` | 辅助/时间戳（浅灰） |
| `--gray-border` | `#E4E7ED` | 边框线 |
| `--primary` | `#1E5EFF` | 科技蓝主色 |
| `--primary-bg` | `#EBF0FF` | 主色浅背景 |
| `--status-online` | `#1DBF61` | 在线绿 |
| `--status-warning` | `#FF7A00` | 警告橙 |
| `--status-offline` | `#F53F3F` | 离线红 |
| `--sidebar-bg` | `#1D2129` | 侧边栏深色 |
| `--sidebar-hover` | `rgba(255,255,255,0.08)` | 侧边栏悬停 |
| `--sidebar-active` | `rgba(30,94,255,0.2)` | 侧边栏激活 |

**暗色模式**（通过 `html.dark` 选择器）：覆盖上述变量为暗色适配值。

### 1.2 Element Plus 主题覆盖

**文件**：`src/styles/element-theme.scss`

覆盖 Element Plus 默认样式，统一项目视觉语言：

- **弹出子菜单**（折叠态侧边栏）：强制深色背景 `#1D2129`，白色文字
- **侧边栏菜单左侧指示条**：Hover 从底部向上展开 60%，Active 全高，蓝色发光
- **顶部标签页 TagsView**：
  - `el-tag` 统一高度 36px，字号 14px，圆角 4px
  - 使用 `gap: 4px` + `flex` 精确控制间距
  - 关闭按钮 Hover 红色圆形背景 + 白色叉号 + 放大 1.15x
  - 激活态：浅蓝底 `#EBF0FF` + 蓝色边框 + 蓝色文字
  - Hover 背景从底部向上生长的流光动画（`scaleY` + `cubic-bezier`）
- **弹窗 Dialog**：
  - 头部改为白色底 + 浅灰分割线（移除原有深黑头）
  - 关闭按钮 Hover 红色圆形 + 白色叉号 + 放大 1.1x
  - 圆角 12px，与全局规范一致

### 1.3 圆角体系

| 场景 | 圆角 |
|------|------|
| 控件（输入框、按钮、标签） | 4px |
| 卡片/列表/信息块 | 8px |
| 弹窗/侧边栏/抽屉 | 12px |
| Logo/头像 | 16-20px |

---

## 二、布局组件重构

### 2.1 主布局

**文件**：`src/layout/index.vue`
- Header 背景改为 `#FFFFFF`
- 内容区背景使用 `--gray-bg`
- App-main 设置 `overflow-y: scroll`

### 2.2 侧边栏

**文件**：`src/layout/components/aside.vue`
- 背景绑定 `var(--sidebar-bg)`，支持暗色模式切换

### 2.3 头部

**文件**：`src/layout/components/header.vue`
- Logo + 系统标题样式
- 折叠图标 Hover 背景过渡
- 右侧设置区（深色/明亮切换、语言切换）配色修复：`color: var(--gray-2)`

### 2.4 侧边菜单

**文件**：`src/layout/components/menu/index.vue`
- 移除全局 `.setting .title` 样式污染（导致 Header 图标看不见）
- 菜单支持 `collapse` 状态

---

## 三、TagsView 标签页

**文件**：`src/layout/components/tags/index.vue` + `src/styles/element-theme.scss`

**当前状态**：
- 尺寸：高度 36px，字号 14px，padding 0 14px
- 间距：父容器使用 `gap: 4px`，消除外边距叠加
- 激活态：浅蓝底 `#EBF0FF` + 蓝色边框 `#1E5EFF` + 蓝色文字 + 字重 600
- 关闭按钮 Hover：红色圆形背景 `#F53F3F`，白色叉号，放大 1.15x
- Hover 背景流光动画：`::before` 伪元素 `scaleY(0→1)`，底部向上展开

**注意**：模板中移除了 `:effect="t.active?'dark':'plain'"` 条件绑定，改为固定 `effect="plain"`，避免 Element Plus 的 `.el-tag--dark` 类用 `!important` 强制覆盖激活态样式。

---

## 四、用户信息页面

**文件**：`src/views/my/info.vue`

- 移除欢迎卡片（应属于 Dashboard）
- 引入头像 `el-avatar :size="64"`，显示用户名首字母
- 使用 `el-descriptions` 组件结构化展示信息
- 邮箱空状态显示灰色占位文字「未设置」
- 修改密码按钮：`type="primary" plain`（蓝色线框），非危险操作
- OIDC 表格独立为单独卡片，最大宽度 680px
- 清理未使用的 `computed`、`marked`、`appStore` 导入

---

## 五、登录页

**文件**：`src/views/login/login.vue`

### 左右分栏布局

- **左侧品牌面板（60%）**：
  - 蓝色渐变背景：`#4F8EFF → #1E5EFF → #0A2E8C`
  - CSS 弧线装饰（右上/左下两个圆形半透明边框）
  - 白色大标题 "New here?"
  - Lorem ipsum 副标题
  - 白色实心「注册」按钮，Hover 变透明描边

- **右侧表单面板（40%）**：
  - 白色背景 `#FFFFFF`
  - 输入框：浅灰底 `#F2F3F5`，圆角 8px，无边框设计
  - 前缀图标（User / Lock / CircleCheck）
  - 聚焦时蓝色边框 + 浅蓝阴影
  - 登录按钮：科技蓝 + 蓝色发光阴影，Hover 上浮
  - 注册文字链接：「没有账号？立即注册」
  - 第三方登录：4 个圆形图标（Google/GitHub/Facebook/Twitter/X）
  - OIDC 快捷入口按钮组

---

## 六、注册页

**文件**：`src/views/register/index.vue`

**布局**：左右分栏（与登录页一致）
- 左侧：蓝色渐变 + 弧线装饰 + 白色「登录」按钮
- 右侧：白色背景 + 灰色输入框 + 科技蓝「注册」按钮
- 输入框前缀图标：User / Lock / Message

---

## 七、部署信息

| 项目 | 值 |
|------|-----|
| 部署域名 | https://claw.u1so1.eu.org/_admin/ |
| 构建命令 | `npm run build` |
| Web 服务器 | Caddy |
| 构建产物目录 | `/root/.openclaw/workspace/rustdesk-api-web/dist/` |
| 最新 hash | `DqQTRlDz`（登录 + 注册） |

---

## 八、修复记录

| 日期 | 问题 | 修复方案 |
|------|------|---------|
| 2026-04-11 | Header 文字/图标看不见 | setting 组件 `.title { color: #fff }` 改为 `var(--gray-2)` |
| 2026-04-11 | menu/index 非 scoped 样式污染全局 | 将 `.setting .title` 限定在 `.menus` 容器内 |
| 2026-04-11 | 侧边栏指示条常驻不消失 | `::before` 默认 `opacity: 0` + `scaleY(0)`，仅 Hover/Active 展开 |
| 2026-04-11 | 标签激活态被 Element Plus `!important` 覆盖 | 移除 `:effect="dark"` 条件绑定，固定 `effect="plain"` |
| 2026-04-11 | Dialog 头部深黑色块 | `style.scss` 中 `.el-dialog__header` 从 `$gray-1` 改为 `var(--gray-white)` |
| 2026-04-11 | 浏览器自动填充白底/蓝底 | `-webkit-box-shadow: 0 0 0 1000px rgba(20,20,21,0.95) inset` |
| 2026-04-11 | 密码眼睛图标默认隐藏 | `.el-input__suffix-inner { display: flex; opacity: 1 }` 常驻可见 |
| 2026-04-11 | 关闭按钮颜色过浅 | 从 `#86909C` 加深到 `#4E5969` + `font-weight: 600` |

---

## 九、待优化项

- [ ] 登录页 OIDC 按钮目前只有图标，第三方登录 SVG 可考虑用 `@element-plus/icons-vue` 替代硬编码
- [ ] 构建产物超过 500KB，建议配置 `rollupOptions.output.manualChunks` 做代码分割
- [ ] TagsView 的 `el-tag` 组件 `effect="plain"` 后，暗色模式下激活态是否需要额外适配待验证
