# 在 Remix VM 之外部署智能合约

在之前的步骤中，你可能已经在 Remix IDE 的模拟环境（通常称为 Remix VM）中部署并测试了你的智能合约。虽然这对于初始开发和快速检查非常好，但它不能完全复制与真实区块链网络的交互。本课将指导你使用像 MetaMask 这样的工具，并介绍一个强大的开发工具：**Tenderly 虚拟测试网 (Tenderly Virtual Testnets)**，将你的智能合约部署到一个行为类似于真实区块链的网络环境中。

## 公共测试网（如 Sepolia）的挑战

公共测试网，如 Sepolia，旨在反映主区块链网络（如以太坊主网）的功能，但使用没有现实世界价值的测试以太币 (Test ETH)。它们对于部署到主网之前的最后测试阶段至关重要。

然而，经常会出现一个重大的实际障碍：**获取测试 ETH**。水龙头 (Faucets)，即旨在分发免费测试 ETH 的网站，以难以使用而闻名。它们经常有严格的要求，施加严格的速率限制，或者干脆资金耗尽。这种困难会严重拖慢开发进度。

**重要的开发理念**：通常认为在日常开发中严重依赖公共测试网是低效的。你的主要测试循环理想情况下应该是本地的（使用 Remix VM 或其他本地区块链模拟器）。

## 介绍 Tenderly 虚拟测试网：更好的替代方案

Tenderly (tenderly.co) 是一个全面的区块链开发平台，提供各种工具，包括调试、模拟和监控。对于开发者而言，其突出的功能之一是**虚拟测试网 (Virtual Testnets)**。

虚拟测试网是私有的、按需的区块链网络模拟。至关重要的是，你可以按照当前状态 fork（分叉）现有的网络（如 Sepolia 甚至以太坊主网），创建你自己的私有副本。

## 设置你的 Tenderly 虚拟测试网

要使用 Tenderly 虚拟测试网，你需要一个 Tenderly 账户。

1.  **注册/登录**：在 Tenderly 上创建一个账户。
2.  **创建项目**：登录后，创建一个新项目（例如 "My First DApp"）。
3.  **创建虚拟测试网**：
    *   导航到 "Virtual TestNets" 部分。
    *   点击 "Create Virtual TestNet"。
    *   **父网络 (Parent Network)**：选择 Sepolia。
    *   **链 ID (Chain ID)**：选择 Custom。公共 Sepolia 是 11155111。选择一个唯一的自定义 ID，如 111555111，以避免冲突。
    *   **公共浏览器 (Public Explorer)**：设置为 On。
    *   **智能合约可见性 (Smart Contract Visibility)**：设置为 Full。
    *   点击 "Create"。

## 通过 MetaMask 将 Remix 连接到 Tenderly

要部署你的合约，你需要通过 MetaMask 将 Remix 连接到你的新 Tenderly 虚拟测试网。

1.  **安装 MetaMask**：确保你有 MetaMask 浏览器扩展。
2.  **编译合约**：在 Remix 中编译你的 `favorites.vy` 合约。
3.  **Remix 环境**：在 "Deploy & Run Transactions" 选项卡中，将 "Environment" 更改为 **"Injected Provider - MetaMask"**。
4.  **连接 MetaMask**：批准 MetaMask 中的连接请求。
5.  **添加网络**：你可能需要在 MetaMask 中将 Tenderly 虚拟测试网添加为一个网络。Tenderly 通常在他们的仪表板中提供 "Add to MetaMask" 按钮，或者你可以使用 Tenderly 提供的 RPC URL 手动添加。

## 将智能合约部署到 Tenderly

随着 Remix 通过 MetaMask 连接到已注资的 Tenderly 虚拟测试网：

1.  **验证设置**：确保 Remix Environment 是 "Injected Provider - MetaMask"，并且 MetaMask 位于你的 "My Dev Fork" 网络上。
2.  **部署**：选择 `favorites` 合约并点击 "Deploy"。
3.  **在 MetaMask 中确认**：查看交易详情（在 Tenderly 上 Gas 费应该可以忽略不计）并点击 "Confirm"。
4.  **验证**：Remix 终端应确认功能。你也可以在 Tenderly Explorer 上查看交易。

**Bug 说明**：如果你通过 MetaMask 部署 Vyper 时遇到问题，请尝试在编译和部署 Vyper 合约之前，在同一个 Remix 会话中编译一个虚拟的 Solidity 文件。这是针对 Remix/插件怪癖的一个已知解决方法。

## 与已部署的合约交互

现在你的合约存在于你的虚拟测试网上，你可以像在 Remix VM 中一样与它交互，但现在涉及 MetaMask 进行状态更改。

1.  **使用已部署实例**：在 Remix 中展开已部署的合约。
2.  **读取状态**：点击蓝色按钮（例如 `my_favorite_number`）。这些是免费的值查找。
3.  **写入状态**：调用一个改变状态的函数，如 `add_person` ("Alice", 42)。
4.  **确认**：MetaMask 将弹出。确认交易。
5.  **验证**：等待确认，然后再次检查状态（例如，读取 `name_to_favorite_number("Alice")`）以查看更新。

此工作流程为在“真实”网络拓扑上测试智能合约提供了一个稳健的环境，而没有公共测试网的摩擦。
