Here is a polished, professional, and ready-to-use `README.md` file based on your notes. You can copy and paste this directly into your GitHub repository.

---

# 🎲 MonadGacha — Hackathon Starter

A fully on-chain gacha game built for the Monad Testnet where items are real ERC-1155 NFTs you can trade freely with zero friction and lightning-fast finality.

## 📁 Repository Structure

* `GachaGame.sol` — Solidity smart contract (Handles ERC-1155 minting, RNG, and the P2P marketplace)
* `GachaApp.jsx` — React frontend (Built with wagmi v2 + viem for seamless Web3 interactions)

---

## 🚀 Quick Start

### 1. Deploy the Smart Contract

First, set up your smart contract environment using Foundry.

```bash
# Install Foundry (Solidity toolkit)
curl -L https://foundry.paradigm.xyz | bash
foundryup

# Create project
forge init gacha-contracts && cd gacha-contracts
cp ../GachaGame.sol src/GachaGame.sol

# Install OpenZeppelin dependencies
forge install OpenZeppelin/openzeppelin-contracts

# Add remapping (in foundry.toml or remappings.txt)
echo '@openzeppelin/=lib/openzeppelin-contracts/' > remappings.txt

# Deploy to Monad Testnet
forge create src/GachaGame.sol:GachaGame \
  --rpc-url https://testnet-rpc.monad.xyz \
  --private-key YOUR_PRIVATE_KEY \
  --broadcast

```

> **Important:** Copy the deployed contract address from your terminal output — you'll need it for the frontend configuration!

### 2. Set Up the Frontend

Next, spin up the React application.

```bash
# Scaffold a new React project with Vite
npm create vite@latest gacha-frontend -- --template react
cd gacha-frontend

# Install Web3 dependencies
npm install wagmi viem @tanstack/react-query

# Copy the frontend logic into your source folder
cp ../GachaApp.jsx src/App.jsx

```

**Configuration Steps:**

1. Open `src/App.jsx` and update line 1 with your deployed contract:
```javascript
const CONTRACT_ADDRESS = "0xYOUR_DEPLOYED_CONTRACT_ADDRESS";

```


2. Update `src/main.jsx` with the `WagmiProvider` and `QueryClientProvider` setup (found at the bottom of `GachaApp.jsx`).

**Run the App:**

```bash
npm run dev
# Open http://localhost:5173 in your browser

```

---

## 🗺️ Feature Roadmap & Status

| Feature | Status |
| --- | --- |
| ERC-1155 Item NFTs | ✅ |
| Simple Gacha Roll (`simpleRoll`) | ✅ |
| Secure Commit-Reveal Roll | ✅ *(in contract)* |
| On-Chain Marketplace (List/Buy/Cancel) | ✅ |
| React UI with Wallet Connect | ✅ |
| Dynamic Inventory View | ✅ |
| Live Market View | ✅ |

---

## 🔧 Customizing for the Hackathon

### Add More Items

To expand the loot pool, open `GachaGame.sol`, add new constants, and update the arrays:

```solidity
uint256 public constant THUNDER_RING = 6;

// Update arrays in the constructor:
itemIds     = [1, 2, 3, 4, 5, 6];
itemWeights = [380, 280, 170, 90, 30, 50];  // Note: Must still sum to exactly 1000
totalWeight = 1000;

```

*Don't forget to add the new item metadata to the `ITEMS` object in `GachaApp.jsx`!*

### Change the Roll Price

If you want to adjust the economy after deploying, call `setRollPrice(newPriceInWei)` from the owner wallet. Alternatively, change the default in the constructor before you deploy:

```solidity
uint256 public rollPrice = 0.005 ether; // Cheaper rolls for hackathon testing

```

### Parse Actual Mint Events (Production-Grade)

Instead of relying on frontend math, ensure your UI parses the actual blockchain receipt for true decentralization (already implemented in the updated `GachaApp.jsx`):

```javascript
import { decodeEventLog } from "viem";
// After txSuccess, the app fetches the receipt and decodes ItemMinted logs to display the real result.

```

---

## 📝 A Note on Randomness (RNG)

Currently, `simpleRoll()` uses `block.prevrandao` for randomness. This is perfect and highly efficient for a hackathon demo. For a mainnet production launch, you would want to upgrade to the commit-reveal flow included in the contract, or integrate a VRF (Verifiable Random Function) oracle once supported on Monad.

---

