# Vyper 教学课程 Slidev PPT 使用指南

本项目使用 [Slidev](https://sli.dev/) 制作 Vyper 智能合约课程的演示文稿，专为教学视频录制设计。

## 目录结构

```
slides/
├── README.md              # 本使用指南
├── lesson01/              # 第一课：欢迎与基础入门
│   ├── slides.md          # PPT 主文件
│   ├── package.json       # 项目依赖
│   └── style.css          # 自定义样式
├── lesson02/              # 第二课（待创建）
└── ...
```

## 课程分组规划

| 课程 | 讲义范围 | 主题 | 详细程度 |
|------|----------|------|----------|
| Lesson 01 | 01-11 | 欢迎与基础入门 | 详细 |
| Lesson 02 | 12-17 | 数据类型与函数 | 详细 |
| Lesson 03 | 19-24 | 进阶语法与结构 | 中等 |
| Lesson 04 | 26-28 | HashMap与回顾 | 中等 |
| Lesson 05 | 29-37 | 工具与实践 | 简略/大纲 |

---

## 快速开始

### 环境要求

- **Node.js** >= 18.0.0
- **npm** 或 **pnpm**（推荐）

### 安装步骤

1. **进入课程目录**

```bash
cd slides/lesson01
```

2. **安装依赖**

```bash
# 使用 npm
npm install

# 或使用 pnpm（推荐，更快）
pnpm install
```

3. **启动开发服务器**

```bash
npm run dev
```

4. **访问演示文稿**

浏览器打开 `http://localhost:3030`

---

## 常用命令

| 命令 | 说明 |
|------|------|
| `npm run dev` | 启动开发服务器（热重载） |
| `npm run build` | 构建静态网站（生成 dist 文件夹） |
| `npm run export` | 导出为 PDF（需要 playwright） |
| `npm run export-pdf` | 导出为 PDF |
| `npm run export-png` | 导出为 PNG 图片序列 |

---

## 演示文稿操作

### 键盘快捷键

| 按键 | 功能 |
|------|------|
| `Space` / `→` / `↓` | 下一页/下一动画 |
| `←` / `↑` | 上一页 |
| `o` | 打开幻灯片概览 |
| `d` | 切换暗色/亮色模式 |
| `f` | 全屏模式 |
| `g` | 跳转到指定页面 |
| `Escape` | 退出当前模式 |

### 演讲者视图

访问 `http://localhost:3030/presenter` 打开演讲者视图，包含：
- 当前幻灯片和下一张预览
- 演讲笔记
- 计时器
- 幻灯片缩略图导航

### 录制视频时

1. 按 `f` 进入全屏模式
2. 使用 OBS 或其他录屏软件捕获窗口
3. 建议分辨率：1920x1080
4. 使用 `Space` 控制动画和翻页节奏

---

## 导出为 PDF

### 方法一：命令行导出

```bash
# 安装 playwright（首次需要）
npx playwright install chromium

# 导出 PDF
npm run export-pdf
```

### 方法二：浏览器打印

1. 启动开发服务器
2. 访问 `http://localhost:3030/export`
3. 使用浏览器打印功能 (Ctrl+P)
4. 选择"保存为 PDF"

---

## 自定义与编辑

### 修改内容

编辑 `slides.md` 文件即可，Slidev 支持热重载。

### Markdown 语法

```markdown
---
layout: section    # 布局类型
---

# 标题

内容支持 **Markdown** 语法

\`\`\`vyper
# 代码高亮
pragma version 0.4.0
\`\`\`
```

### 常用布局

| 布局 | 说明 |
|------|------|
| `default` | 默认布局 |
| `cover` | 封面页 |
| `section` | 章节分隔页 |
| `two-cols` | 双栏布局 |
| `center` | 居中布局 |
| `image-right` | 右侧图片 |
| `end` | 结束页 |

### 动画效果

使用 `<v-clicks>` 实现逐条显示：

```markdown
<v-clicks>

- 第一条（点击后显示）
- 第二条
- 第三条

</v-clicks>
```

### 代码高亮

```markdown
\`\`\`vyper {all|1-2|3|all}
# pragma version 0.4.0
# @license MIT
my_number: uint256
\`\`\`
```

`{all|1-2|3|all}` 表示动画顺序：全部 → 1-2行 → 第3行 → 全部

---

## 主题与样式

### 修改主题颜色

编辑 `style.css` 文件：

```css
:root {
  --slidev-theme-primary: #10b981;   /* 主色调 */
  --slidev-theme-secondary: #6366f1; /* 次要色 */
}
```

### 更换主题

修改 `slides.md` 头部：

```yaml
---
theme: seriph    # 可选: default, seriph, apple-basic, etc.
---
```

可用主题列表：https://sli.dev/themes/gallery

---

## Mermaid 图表

Slidev 内置 Mermaid 支持：

```markdown
\`\`\`mermaid
flowchart LR
    A[源代码] --> B[编译器] --> C[字节码]
\`\`\`
```

---

## 故障排除

### 问题：npm install 报错

```bash
# 清除缓存重试
npm cache clean --force
rm -rf node_modules
npm install
```

### 问题：PDF 导出失败

```bash
# 确保安装了 playwright
npx playwright install chromium

# 或尝试使用浏览器打印方法
```

### 问题：中文显示异常

确保系统安装了中文字体，或在 `style.css` 中指定字体：

```css
body {
  font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif;
}
```

---

## 创建新课程

1. 复制 `lesson01` 文件夹为 `lesson02`
2. 修改 `slides.md` 内容
3. 更新 `package.json` 中的 `name` 字段

---

## 更多资源

- [Slidev 官方文档](https://sli.dev/)
- [Slidev 主题库](https://sli.dev/themes/gallery)
- [Vyper 官方文档](https://docs.vyperlang.org/)
- [课程 GitHub 仓库](https://github.com/Cyfrin/moccasin-full-course-cu)

---

## 贡献

如有建议或发现问题，请提交 Issue 或 Pull Request。
