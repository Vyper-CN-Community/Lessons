# Vyper 教学课程 Slidev PPT 使用指南

本项目使用 [Slidev](https://sli.dev/) 制作 Vyper 智能合约课程的演示文稿。
所有课程均已合并为两个主要的演示文件，方便教学与录制。

## 目录结构

```
slides/
├── README.md              # 本使用指南
├── lesson01_03.md         # 基础篇（Lesson 01-03）
├── lesson04_07.md         # 进阶篇（Lesson 04-07）
├── package.json           # 项目依赖
└── style.css              # 自定义样式
```

## 课程内容

| 文件名 | 包含课程 | 主要主题 |
|--------|----------|----------|
| **lesson01_03.md** | Lesson 01 | 欢迎与基础入门 (Remix, Syntax) |
| | Lesson 02 | 数据类型与函数 (Types, Functions) |
| | Lesson 03 | 构造函数与结构体 (Init, Structs) |
| **lesson04_07.md** | Lesson 04 | HashMaps 与 Workshop 实战 |
| | Lesson 05 | 工具生态 (Tenderly, ZKSync) 与 EVM |
| | Lesson 06 | 核心逻辑 (Pure/View, If/Else) |
| | Lesson 07 | 综合回顾与本地开发展望 |

---

## 快速开始

### 1. 安装依赖

```bash
# 在 slides 目录下运行
npm install
```

### 2. 运行讲义

你可以通过以下命令启动特定部分的讲义：

```bash
# 运行基础篇 (L01-L03)
npx slidev lesson01_03.md

# 运行进阶篇 (L04-L07)
npx slidev lesson04_07.md
```

或者，如果你在 `package.json` 中配置了 script：

```bash
npm run lesson01-03
npm run lesson04-07
```

浏览器打开 `http://localhost:3030` 即可查看。

---

## 常用快捷键

| 按键 | 功能 |
|------|------|
| `Space` / `→` | 下一页/下一动画 |
| `←` | 上一页 |
| `o` | 打开幻灯片概览 |
| `d` | 切换暗色/亮色模式 |
| `f` | 全屏模式 (录制推荐) |

## 录制建议

1. 使用 **全屏模式 (f)**。
2. 配合对应的 **口播稿 (Script)** 进行讲解。
3. 遇到代码页，可以使用演讲者视图 (`http://localhost:3030/presenter`) 查看备注。
