# Deploying Your Smart Contract Beyond the Remix VM

In previous steps, you likely deployed and tested your smart contract within the Remix IDE's simulated environment, often called the Remix VM. While excellent for initial development and quick checks, it doesn't fully replicate interacting with a real blockchain network. This lesson guides you through deploying your smart contract to a network environment that behaves like a real blockchain, using tools like MetaMask and introducing a powerful development tool: **Tenderly Virtual Testnets**.

## The Challenge of Public Testnets (Like Sepolia)

Public testnets, such as Sepolia, are designed to mirror the functionality of main blockchain networks (like Ethereum Mainnet) but use test Ether (ETH) with no real-world value. They are crucial for final testing stages before deploying to mainnet.

However, a significant practical hurdle often arises: **acquiring test ETH**. Faucets, the websites designed to distribute free test ETH, are notoriously difficult to use. They frequently have strict requirements, impose severe rate limits, or simply run out of funds. This difficulty can severely slow down development.

**Important Development Philosophy**: Relying heavily on public testnets for day-to-day development is generally considered inefficient. Your primary testing loop should ideally be local (using Remix VM or other local blockchain simulators).

## Introducing Tenderly Virtual Testnets: A Better Alternative

Tenderly (tenderly.co) is a comprehensive blockchain development platform offering various tools, including debugging, simulation, and monitoring. One of its standout features for developers is **Virtual Testnets**.

Virtual Testnets are private, on-demand simulations of blockchain networks. Crucially, you can fork an existing network (like Sepolia or even Ethereum Mainnet) at its current state, creating your own private copy.

## Setting Up Your Tenderly Virtual Testnet

To use Tenderly Virtual Testnets, you'll need a Tenderly account.

1.  **Sign Up/Login**: Create an account on Tenderly.
2.  **Create a Project**: Once logged in, create a new project (e.g., "My First DApp").
3.  **Create a Virtual TestNet**:
    *   Navigate to the "Virtual TestNets" section.
    *   Click "Create Virtual TestNet".
    *   **Parent Network**: Select Sepolia.
    *   **Chain ID**: Select Custom. Public Sepolia is 11155111. Choose a unique custom ID like 111555111 to avoid conflicts.
    *   **Public Explorer**: Set to On.
    *   **Smart Contract Visibility**: Set to Full.
    *   Click "Create".

## Connecting Remix to Tenderly via MetaMask

To deploy your contract, you need to connect Remix to your new Tenderly Virtual Testnet via MetaMask.

1.  **Install MetaMask**: Ensure you have the MetaMask browser extension.
2.  **Compile Contract**: Compile your `favorites.vy` contract in Remix.
3.  **Remix Environment**: In the "Deploy & Run Transactions" tab, change "Environment" to **"Injected Provider - MetaMask"**.
4.  **Connect MetaMask**: Approve the connection request in MetaMask.
5.  **Add Network**: You likely need to add your Tenderly Virtual Testnet as a network in MetaMask. Tenderly often provides an "Add to MetaMask" button in their dashboard, or you can add it manually using the RPC URL provided by Tenderly.

## Deploying Your Smart Contract to Tenderly

With Remix connected via MetaMask to your funded Tenderly Virtual Testnet:

1.  **Verify Setup**: Ensure Remix Environment is "Injected Provider - MetaMask" and MetaMask is on your "My Dev Fork" network.
2.  **Deploy**: Select `favorites` contract and Click "Deploy".
3.  **Confirm in MetaMask**: Review the transaction details (gas fee should be negligible on Tenderly) and click "Confirm".
4.  **Verify**: The Remix terminal should confirm functionality. You can also view the transaction on the Tenderly Explorer.

**Bug Note**: If you encounter issues deploying Vyper via MetaMask, try compiling a dummy Solidity file in the same Remix session before compiling and deploying your Vyper contract. This is a known workaround for a Remix/plugin quirk.

## Interacting with Your Deployed Contract

Now that your contract lives on your virtual testnet, you can interact with it just like you did in the Remix VM, but now involving MetaMask for state changes.

1.  **Use Deployed Instance**: Expand your deployed contract in Remix.
2.  **Read State**: Click blue buttons (e.g., `my_favorite_number`). These are free value lookups.
3.  **Write State**: Call a state-changing function like `add_person` ("Alice", 42).
4.  **Confirm**: MetaMask will pop up. Confirm the transaction.
5.  **Verify**: Wait for confirmation, then check the state again (e.g., read `name_to_favorite_number("Alice")`) to see the update.

This workflow provides a robust environment for testing smart contracts on a "real" network topology without the friction of public testnets.
