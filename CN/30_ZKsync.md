# 尝试通过 Remix 部署 ZKVyper

本课探讨直接在 Remix IDE 中使用 Vyper 智能合约与 **zkSync** 交互的过程。虽然 Remix 是一个强大的智能合约开发工具，但在使用官方 ZKSYNC 插件和 Vyper 时，我们会遇到一个特定的限制。

让我们从 Remix IDE 环境 (remix.ethereum.org) 开始，我们在编辑器中打开了一个示例 Vyper 合约，例如 `favorites.vy`。我们的目标是编译并将此合约部署到 zkSync 网络。

## 激活 ZKSYNC 插件

为了促进与 zkSync 的交互，Remix 提供了插件。按照以下步骤激活 ZKSYNC 插件：

1.  导航到左侧边栏的 **Plugin manager**（插件管理器）图标。
2.  在搜索栏中，输入 "**zksync**"。
3.  找到名为 "**ZKSYNC**" 的模块（描述："Compile and deploy smart contracts for zkSync Era"）。
4.  点击 "**Activate**"（激活）按钮。

激活后，一个新的 "ZKSYNC" 图标将出现在左侧边栏。此面板提供了专门针对 zkSync Era 环境编译和部署合约的选项。

## 限制：Vyper 支持

然而，需要注意的一个关键点是此特定插件目前的能力。截至撰写本文时，**Remix ZKSYNC 插件仅支持 Solidity 合约**。它不具备编译或准备 Vyper 合约以部署到 zkSync 的功能。

因此，尝试使用此插件编译和部署我们的 `favorites.vy` 合约（或任何其他 Vyper 合约）将不会成功。必要的 **ZKVyper** 编译步骤未集成到此特定的 Remix 插件工作流中。

## 结论

由于该插件缺乏 Vyper 支持，目前使用 Remix IDE 内集成的 ZKSYNC 插件将 Vyper 智能合约直接部署到 zkSync **是不可行的**。

要部署 ZKVyper 合约，我们必须利用此特定 Remix 插件之外的替代工具链，例如基于 Python 的框架 **Moccasin** 和 **Titanoboa**，它们提供了必要的编译和部署功能。这些替代方法将在随后的课程中探讨。
