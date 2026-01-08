---
theme: default
title: Vyper 智能合约开发入门
info: |
  ## Vyper 智能合约安全课程
  第一课：欢迎与基础入门
author: Cyfrin Updraft
transition: slide-left
highlighter: shiki
drawings:
  persist: false
colorSchema: dark
layout: cover
background: https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=1920
---

# Vyper 智能合约开发

Python 风格的安全智能合约语言

<div class="pt-12">
  <span class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    第一课 · 欢迎与基础入门
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <a href="https://github.com/vyperlang/vyper" target="_blank"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
layout: intro
---

# 课程概览

<div class="leading-10 opacity-80">

本节课你将学到：

- 为什么选择 Python 和 Vyper
- Remix IDE 环境配置
- Vyper 基础语法
- 编译与调试入门

</div>

---
layout: section
---

# 第一部分
## 为什么学习 Vyper?

---
layout: two-cols
---

# Python 的重要性

<v-clicks>

- **金融科技主导地位**
  - 对冲基金、资产管理首选
  - 生产级应用理想选择

- **AI 革命的支柱**
  - PyTorch、TensorFlow
  - 机器学习通用语言

- **创新的基石**
  - Mojo 等新语言基于 Python
  - 长期相关性保证

</v-clicks>

::right::

<div class="pl-4">

```python {all|1-2|4-5|7-8}
# Python 无处不在

# 金融分析
import pandas as pd

# 机器学习
import torch

# Web3 开发
from web3 import Web3
```

<div v-click class="mt-8 p-4 bg-green-500/10 rounded-lg">

**竞争优势**

掌握 Python = 站在 AI + Web3 的最前沿

</div>

</div>

---

# Vyper：安全至上的设计

<div class="grid grid-cols-2 gap-8">

<div>

### 核心特点

<v-clicks>

- **Python 风格语法** - 学习曲线平缓
- **简洁性优先** - 代码更易审计
- **安全导向** - 减少攻击面
- **可读性** - 人类和 AI 都易理解

</v-clicks>

</div>

<div>

### 生态地位

<v-clicks>

- Curve Finance - 顶级 DEX
- Lido - 最大质押平台
- Yearn Finance - 收益聚合器

</v-clicks>

<div v-click class="mt-6 p-4 bg-blue-500/10 rounded-lg text-sm">

虽然 Vyper TVL 占比约 1-3%，但保护着 DeFi 最关键的协议

</div>

</div>

</div>

---
layout: section
---

# 第二部分
## 开发环境配置

---
layout: image-right
image: https://remix.ethereum.org/assets/img/remix-logo.webp
backgroundSize: contain
---

# Remix IDE

**基于浏览器的开发环境**

<v-clicks>

- 无需安装，打开即用
- 支持 Vyper 和 Solidity
- 内置编译器和部署工具
- 实时调试和测试

</v-clicks>

<div v-click class="mt-6">

```
访问地址
remix.ethereum.org
```

</div>

---

# Remix 初始设置

<div class="grid grid-cols-2 gap-6">

<div>

### 1. 清理默认文件

<v-clicks>

- 删除 `contracts/` 文件夹
- 删除 `scripts/` 文件夹
- 删除 `tests/` 文件夹
- 删除配置文件

</v-clicks>

</div>

<div>

### 2. 激活 Vyper 插件

<v-clicks>

- 点击 **插件管理器** 图标
- 搜索 `vyper`
- 点击 **Activate** 激活
- 侧边栏出现 Vyper 图标

</v-clicks>

</div>

</div>

<div v-click class="mt-8 p-4 bg-yellow-500/10 rounded-lg">

**创建第一个文件**

新建文件 → 命名为 `favorites.vy` → `.vy` 是 Vyper 的文件扩展名

</div>

---
layout: section
---

# 第三部分
## Vyper 基础语法

---

# Pragma 版本声明

每个 Vyper 合约的第一行

```vyper {all|1|2|all}
# pragma version 0.4.0
# 指定编译器版本
```

<v-clicks>

### 为什么重要？

- **兼容性保证** - 确保代码正确编译
- **功能一致性** - 不同版本语法可能不同
- **安全可预测** - 锁定版本避免意外

</v-clicks>

<div v-click class="mt-6 grid grid-cols-2 gap-4">

<div class="p-3 bg-green-500/10 rounded">

**推荐写法**
```vyper
# pragma version 0.4.0
```

</div>

<div class="p-3 bg-red-500/10 rounded">

**不推荐**
```vyper
# pragma version ^0.4.0
```

</div>

</div>

---

# 注释的使用

使用 `#` 符号添加注释

```vyper {all|1|2|4|all}
# 这是一个完整行注释
variable = 1  # 这是行尾注释

# pragma version 0.4.0  # 这是特殊指令，不是普通注释
```

<div class="grid grid-cols-2 gap-6 mt-8">

<div>

### 注释用途

<v-clicks>

- 解释复杂逻辑
- 记录设计决策
- 留下 TODO 提醒
- 临时禁用代码

</v-clicks>

</div>

<div>

### 特殊指令

<v-clicks>

- `# pragma version` - 版本声明
- `# @license` - 许可证声明
- 编译器会解析这些指令

</v-clicks>

</div>

</div>

---

# 许可证声明

开源软件的法律保障

```vyper
# pragma version 0.4.0
# @license MIT
```

<div class="grid grid-cols-3 gap-4 mt-8">

<div v-click class="p-4 bg-blue-500/10 rounded-lg">

### MIT 许可证

- 最宽松的开源许可
- 允许商业使用
- 允许修改和分发
- Web3 社区首选

</div>

<div v-click class="p-4 bg-purple-500/10 rounded-lg">

### 为什么需要？

- 法律清晰度
- 避免编译警告
- 符合开源规范
- 社区信任基础

</div>

<div v-click class="p-4 bg-green-500/10 rounded-lg">

### 其他选项

- GPL-3.0
- Apache-2.0
- BSD-3-Clause
- UNLICENSED

</div>

</div>

---
layout: section
---

# 第四部分
## 编译入门

---

# 什么是编译？

<div class="text-center mb-8">

```mermaid {scale: 0.8}
flowchart LR
    A[Vyper 源代码<br/>人类可读] --> B[编译器]
    B --> C[字节码<br/>机器可执行]
    style A fill:#10b981,color:#fff
    style B fill:#6366f1,color:#fff
    style C fill:#f59e0b,color:#fff
```

</div>

<v-clicks>

- **源代码** → 你编写的 `.vy` 文件
- **编译器** → Vyper 编译器（翻译工具）
- **字节码** → EVM 可执行的低级指令

</v-clicks>

<div v-click class="mt-6 p-4 bg-blue-500/10 rounded-lg">

**EVM（以太坊虚拟机）不理解 Vyper，只理解字节码**

</div>

---

# 在 Remix 中编译

<div class="grid grid-cols-2 gap-8">

<div>

### 编译方法

<v-clicks>

1. 点击 Vyper Compiler 图标
2. 点击 **Compile** 按钮

**或使用快捷键**

- Mac: `Cmd + S`
- Windows: `Ctrl + S`

</v-clicks>

</div>

<div>

### 编译输出

<v-clicks>

**ABI（应用程序二进制接口）**
```json
[
  {
    "name": "store",
    "type": "function",
    "inputs": [...]
  }
]
```

**字节码**
```
0x608060405234801561001057...
```

</v-clicks>

</div>

</div>

---

# 处理编译错误

<div class="grid grid-cols-2 gap-6">

<div>

### 错误示例

```vyper {all|3}
# pragma version 0.4.0
# @license MIT
asdfhasjh invalid code
```

<v-click>

编译器报错：
```
SyntaxError: invalid syntax
Line 3, Column 1
```

</v-click>

</div>

<div v-click>

### 调试技巧

<v-clicks>

- 仔细阅读错误信息
- 检查指定的行号
- 对照语法规则
- 逐步排除问题

</v-clicks>

<div class="mt-4 p-3 bg-green-500/10 rounded">

**成功编译标志**

Vyper 图标显示 ✓ 绿色对勾

</div>

</div>

</div>

---
layout: section
---

# 第五部分
## 合约设计

---

# 需求先行

在编码之前，明确合约需要做什么

```vyper
# pragma version 0.4.0
# @license MIT

# Favorites List 合约需求：
# 1. 存储一个喜爱的数字
# 2. 存储人名和对应的喜爱数字
```

<div class="grid grid-cols-2 gap-6 mt-6">

<div v-click class="p-4 bg-blue-500/10 rounded-lg">

### 为什么要先规划？

- 智能合约一旦部署不可更改
- 提前发现设计问题
- 代码更有条理
- 减少返工成本

</div>

<div v-click class="p-4 bg-purple-500/10 rounded-lg">

### 设计思路

- 需要存储什么数据？
- 需要什么操作功能？
- 谁可以访问？
- 有什么限制条件？

</div>

</div>

---

# 最小可编译合约

```vyper
# pragma version 0.4.0
# @license MIT
```

<v-clicks>

- 这就是一个有效的 Vyper 合约！
- 虽然没有任何功能
- 但可以成功编译
- 字节码几乎为空

</v-clicks>

<div v-click class="mt-8 p-4 bg-yellow-500/10 rounded-lg">

**下一步：添加状态变量和函数**

在后续课程中，我们将：
- 定义数据类型
- 创建状态变量
- 编写函数逻辑
- 部署到区块链

</div>

---
layout: section
---

# 学习资源

---

# 推荐资源

<div class="grid grid-cols-2 gap-8">

<div>

### 课程平台

<v-clicks>

- **Cyfrin Updraft** - updraft.cyfrin.io
  - 文字版课程
  - 进度追踪
  - 课后测验

- **GitHub 仓库**
  - 完整代码示例
  - 课程讨论区

</v-clicks>

</div>

<div>

### 开发工具

<v-clicks>

- **Remix IDE** - remix.ethereum.org
- **Vyper 文档** - docs.vyperlang.org
- **AI 助手** - Claude / ChatGPT
  - 辅助学习和调试
  - 需要验证输出正确性

</v-clicks>

</div>

</div>

<div v-click class="mt-8 p-4 bg-green-500/10 rounded-lg text-center">

**重复是技能之母** - 动手实践，跟随编码

</div>

---
layout: center
class: text-center
---

# 本课小结

<div class="grid grid-cols-4 gap-4 mt-8">

<div class="p-4 bg-blue-500/20 rounded-lg">

**Python + Vyper**

Web3 + AI 的未来

</div>

<div class="p-4 bg-purple-500/20 rounded-lg">

**Remix IDE**

浏览器即开发环境

</div>

<div class="p-4 bg-green-500/20 rounded-lg">

**基础语法**

pragma / 注释 / 许可证

</div>

<div class="p-4 bg-orange-500/20 rounded-lg">

**编译流程**

源码 → 字节码

</div>

</div>

<div class="mt-12">

下一课：**Vyper 数据类型与变量**

</div>

---
layout: end
---

# 感谢学习

开始你的 Vyper 之旅

[开始练习](https://remix.ethereum.org) · [课程仓库](https://github.com/Cyfrin/moccasin-full-course-cu)
