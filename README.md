# SBT Minting Platform

A Soul Bound Token (SBT) minting platform deployed on Base Chain Mainnet. SBTs are non-transferable tokens that represent identity, achievements, or credentials.

## Features

- **Soul Bound**: Tokens cannot be transferred once minted
- **Minting Fee**: 0.000001 ETH per mint
- **One Per Address**: Each address can only mint once
- **Script Friendly**: Easy integration with automated scripts

## Contract Details

- **Network**: Base Chain Mainnet (Chain ID: 8453)
- **Token Standard**: ERC-721 (Modified for non-transferability)
- **Mint Fee**: 0.000001 ETH
- **Symbol**: SBT
- **Name**: SoulBoundToken

## Project Structure

```
sbt-minting-platform/
├── contracts/
│   └── SoulBoundToken.sol     # Main SBT contract
├── scripts/
│   ├── deploy.js              # Deployment script
│   ├── generateWallets.js     # Generate 500 wallets
│   ├── fundWallets.js         # Fund wallets from funding address
│   └── mintSBT.js             # Batch minting script
├── wallets.json               # Generated wallets (PRIVATE - DO NOT SHARE)
├── wallet_addresses.json      # Addresses only
├── address_list.txt           # Simple address list
├── deployment.json            # Deployment info
├── .env                       # Environment variables
└── hardhat.config.js          # Hardhat configuration
```

## Setup Instructions

### 1. Install Dependencies

```bash
cd sbt-minting-platform
npm install
```

### 2. Configure Environment

Edit the `.env` file with your credentials:

```env
# Your deployer wallet private key
DEPLOYER_PRIVATE_KEY=your_private_key_here

# Funding wallet private key (0x30d99e4e396D0986853921c4F1eF0e4a93a9aAcd)
FUNDING_WALLET_PRIVATE_KEY=your_funding_wallet_private_key_here

# Base Chain RPC URL (default works fine)
BASE_RPC_URL=https://mainnet.base.org

# Contract address (set after deployment)
SBT_CONTRACT_ADDRESS=
```

### 3. Compile Contract

```bash
npm run compile
```

### 4. Deploy Contract

```bash
npm run deploy
```

After deployment, copy the contract address and add it to your `.env` file:

```env
SBT_CONTRACT_ADDRESS=0x...your_deployed_contract_address...
```

## Usage

### Generate Wallets (Already Done - 500 wallets created)

If you need to regenerate wallets:

```bash
npm run generate-wallets
```

This creates:
- `wallets.json` - Full wallet data with private keys (KEEP SECURE!)
- `wallet_addresses.json` - Addresses with indices
- `address_list.txt` - Simple list of addresses

### Fund Wallets

Fund all 500 wallets from the funding address:

```bash
npm run fund-wallets
```

This will:
- Transfer 0.000051 ETH to each wallet (0.000001 ETH mint fee + 0.00005 ETH gas buffer)
- Skip already funded wallets
- Save results to `funding_results.json`

**Total ETH needed**: ~0.0255 ETH for 500 wallets

### Mint SBTs

After wallets are funded:

```bash
npm run mint
```

This will:
- Mint one SBT per wallet
- Skip wallets that have already minted
- Save results to `minting_results.json`

## Contract Interface

### Key Functions

```solidity
// Mint a Soul Bound Token (requires 0.000001 ETH)
function mint() external payable;

// Check if address has minted
function hasMinted(address account) external view returns (bool);

// Get total tokens minted
function totalSupply() external view returns (uint256);

// Get minting fee
function MINT_FEE() external view returns (uint256);
```

### Interacting with Contract (JavaScript/TypeScript)

```javascript
import { ethers } from "ethers";

const SBT_ABI = [
  "function mint() external payable",
  "function MINT_FEE() view returns (uint256)",
  "function hasMinted(address) view returns (bool)"
];

// Connect to contract
const provider = new ethers.JsonRpcProvider("https://mainnet.base.org");
const wallet = new ethers.Wallet(PRIVATE_KEY, provider);
const contract = new ethers.Contract(CONTRACT_ADDRESS, SBT_ABI, wallet);

// Mint
const tx = await contract.mint({ value: ethers.parseEther("0.000001") });
await tx.wait();
```

## Script Configuration

### Funding Script (`scripts/fundWallets.js`)

- `BATCH_SIZE`: 10 (transactions per batch)
- `BATCH_DELAY_MS`: 2000 (delay between batches)
- `AMOUNT_PER_WALLET`: 0.000051 ETH (mint fee + gas buffer)

### Minting Script (`scripts/mintSBT.js`)

- `BATCH_SIZE`: 10 (mints per batch)
- `BATCH_DELAY_MS`: 3000 (delay between batches)
- `MAX_RETRIES`: 3 (retry attempts for failed transactions)

## Security Notes

⚠️ **IMPORTANT**:
- Never share `wallets.json` - it contains private keys
- Never commit `.env` to version control
- Keep backup of wallet data
- The funding wallet private key should be kept secure

## Troubleshooting

### "Insufficient balance" errors
- Ensure funding wallet has enough ETH
- Each wallet needs ~0.000051 ETH

### "Already minted" errors
- Expected if rerunning the mint script
- Each address can only mint once

### Transaction failures
- Check gas prices on Base Chain
- Increase `GAS_BUFFER` if needed
- Ensure RPC endpoint is responsive

## License

ISC
