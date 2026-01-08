---
theme: default
title: Vyper 基础入门 (Lesson 01-03)
info: |
  ## Vyper 智能合约安全课程
  Lessons 01-03: 基础入门、数据类型与结构
author: Cyfrin Updraft
transition: slide-left
highlighter: shiki
drawings:
  persist: false
colorSchema: dark
layout: cover
background: https://images.unsplash.com/photo-1639762681485-074b7f938ba0?w=1920
---

# Vyper 基础入门 (Lesson 01-03)

从零开始掌握安全智能合约开发

<div class="pt-12">
  <span class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    课程 01-03 合集
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

# 合集概览

<div class="leading-10 opacity-80">

本讲义涵盖课程的前半部分，打下坚实的基础：

- **L01: 欢迎与基础** - 环境配置与 Hello World
- **L02: 核心语法** - 数据类型、变量与函数
- **L03: 进阶结构** - 构造函数、列表与结构体

</div>

---
layout: section
---

# Lesson 01
## 欢迎与基础入门

---

# 为什么学习 Vyper?

<div class="grid grid-cols-2 gap-8">
<div>

### Python 风格
- 学习曲线平缓
- 代码可读性极高
- 金融/AI 领域的首选语法

</div>
<div>

### 安全至上
- 故意限制部分功能以减少 Bug
- **Curve Finance**, **Lido** 等顶级协议使用
- 审计友好

</div>
</div>

---

# 开发环境: Remix IDE

无需安装，浏览器即刻开始

<div class="grid grid-cols-2 gap-8">
<div>

### 准备工作
1. 访问 [remix.ethereum.org](https://remix.ethereum.org)
2. 清空默认文件 (contracts/scripts 等)
3. 激活 **Vyper** 插件

</div>
<div>

### Hello World (Setup)
1. 新建 `favorites.vy`
2. 写下第一行代码：
   ```vyper
   # pragma version 0.4.0
   ```

</div>
</div>

---
layout: section
---

# Lesson 02
## 数据类型与函数

---

# 基本数据类型 (v0.4.0)

<div class="grid grid-cols-2 gap-8">

<div>
### 值类型

- `uint256`: 非负整数 (余额、数量)
- `bool`: True / False
- `address`: 0x... (账户地址)

```vyper
num: uint256
active: bool
```
</div>

<div>
### 初始化规则

Vyper v0.4.0 禁止在声明时赋值
```vyper
# ❌ 错误
num: uint256 = 10 

# ✅ 正确 (自动初始为 0)
num: uint256
```
</div>
</div>

---

# 变量可见性

<div class="grid grid-cols-2 gap-8">

<div>

### Internal (默认)
- `secret: uint256`
- 仅合约内部可见
- 外部无法读取

</div>

<div>

### Public
- `val: public(uint256)`
- 自动生成 Getter 函数
- 外部可读取 (蓝色按钮)

</div>
</div>

---

# 函数定义

使用了 Python 的 `def` 关键字

```vyper {all|1|2|3-4|all}
@external
def store(val: uint256):
    # self 代表合约实例
    self.num = val
```

<div class="mt-4 grid grid-cols-2 gap-4">
<div class="p-2 bg-blue-500/10 rounded">
<b>@external</b>: 仅限外部调用
</div>
<div class="p-2 bg-green-500/10 rounded">
<b>@view</b>: 只读不写 (省 Gas)
</div>
</div>

---
layout: section
---

# Lesson 03
## 构造函数与结构体

---

# 构造函数 (__init__)

合约的"出生证明"，仅执行一次

```vyper
owner: public(address)

@deploy
def __init__():
    # 部署时执行
    self.owner = msg.sender
```

<div class="mt-4 p-4 bg-purple-500/10 rounded-lg">
可用于初始化状态变量，或者接收部署参数（如代币总量、配置项等）。
</div>

---

# 引用类型: 列表 (List)

<div class="grid grid-cols-2 gap-8">

<div>
### 固定大小列表
- `nums: uint256[10]`
- 大小固定，不可变
- 必须通过索引访问

</div>

<div>
### 安全性
- 越界访问会由 Vyper 自动拦截 (Revert)
- 比动态数组更安全、Gas 更可控

</div>
</div>

---

# 自定义类型: 结构体 (Struct)

将相关数据打包

```vyper
struct Person:
    name: String[100]
    favorite_number: uint256

# 使用结构体
myself: public(Person)
```

<div class="mt-4 p-2 bg-yellow-500/10 rounded text-sm">
注意：String 必须指定最大长度 (如 String[100])，这是 Vyper 的安全特性。
</div>


---

# 高级函数特性

<div class="grid grid-cols-2 gap-8">

<div>
### 返回多个值

Vyper 支持函数返回多个结果 (元组)

```vyper
@external
@pure
def get_pair() -> (uint256, bool):
    return (123, True)
```

</div>

<div>
### 整数除法 (Floor Division)

使用 `//` 进行整除 (向下取整)

```vyper
# ⚠️ 普通除法 / 不可用
val: uint256 = 5 // 2  
# 结果为 2
```

</div>
</div>

<div class="mt-4 p-2 bg-gray-500/10 rounded">
<b>占位符 `pass`</b>: 如果函数体还没想好，写个 `pass` 占位，避免编译错误。
</div>

---
layout: end
---

# 准备进入实战

基础已打牢，下一阶段我们将深入
**映射 (HashMaps)** 与 **工具生态**！

[Remix IDE](https://remix.ethereum.org)
