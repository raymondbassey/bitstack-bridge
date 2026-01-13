# BitStack Bridge Protocol

A secure, validator-driven cross-chain bridge enabling seamless asset transfers between Bitcoin and Stacks Layer 2 network.

## 🎯 Overview

BitStack Bridge Protocol facilitates trustless cross-chain operations by leveraging a decentralized validator network to verify Bitcoin transactions and mint corresponding assets on Stacks. The protocol implements robust security measures including multi-signature validation, minimum confirmation requirements, and emergency controls to ensure safe and reliable cross-chain asset management.

## 🏗️ System Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Bitcoin       │    │   Validators    │    │   Stacks L2     │
│   Network       │    │   Network       │    │   Network       │
├─────────────────┤    ├─────────────────┤    ├─────────────────┤
│                 │    │                 │    │                 │
│ BTC Transaction │───▶│ Tx Verification │───▶│ Asset Minting   │
│                 │    │                 │    │                 │
│ Deposit Address │    │ Multi-Sig       │    │ Bridge Balance  │
│                 │    │ Consensus       │    │                 │
│ Confirmations   │    │                 │    │ Withdrawal      │
│                 │    │ Signature       │    │ Processing      │
│                 │    │ Validation      │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 📋 Contract Architecture

### Core Components

#### 1. **Administrative Layer**

- **Contract Deployer**: Ultimate authority with emergency controls
- **Validator Management**: Add/remove trusted validators
- **Bridge Controls**: Pause/resume operations

#### 2. **Validation Layer**

- **Multi-Validator Consensus**: Requires multiple validator confirmations
- **Signature Verification**: Cryptographic signature validation
- **Transaction Verification**: Bitcoin transaction hash validation

#### 3. **Asset Management Layer**

- **Bridge Balances**: User asset tracking on Stacks
- **Deposit Processing**: Bitcoin-to-Stacks asset flow
- **Withdrawal Processing**: Stacks-to-Bitcoin asset flow

#### 4. **Security Layer**

- **Emergency Pause**: Immediate halt of all operations
- **Amount Validation**: Min/max deposit limits
- **Double-Spend Protection**: Transaction hash uniqueness

### Data Structures

```clarity
;; Deposit Record
{
  amount: uint,
  recipient: principal,
  processed: bool,
  confirmations: uint,
  timestamp: uint,
  btc-sender: (buff 33)
}

;; Validator Signature
{
  signature: (buff 65),
  timestamp: uint
}
```

## 🔄 Data Flow

### Deposit Flow (Bitcoin → Stacks)

```
1. User sends BTC to bridge address
2. Validator detects Bitcoin transaction
3. Validator calls initiate-deposit()
4. Multiple validators confirm via confirm-deposit()
5. Assets minted to user's Stacks address
6. Bridge balance updated
```

### Withdrawal Flow (Stacks → Bitcoin)

```
1. User calls withdraw() with BTC address
2. Bridge balance decremented
3. Withdrawal event emitted
4. Validators process Bitcoin payout
5. Transaction completed off-chain
```

## 🚀 Quick Start

### Prerequisites

- Stacks CLI installed
- Access to Stacks testnet/mainnet
- Bitcoin testnet/mainnet for cross-chain testing

### Deployment

```bash
# Deploy to Stacks testnet
clarinet deploy --network testnet

# Initialize the bridge
stx call-contract-func \
  --contract-address <CONTRACT_ADDRESS> \
  --contract-name bitstack-bridge \
  --function-name initialize-bridge \
  --network testnet
```

### Adding Validators

```bash
# Add a trusted validator
stx call-contract-func \
  --contract-address <CONTRACT_ADDRESS> \
  --contract-name bitstack-bridge \
  --function-name add-validator \
  --function-args 'SP...' \
  --network testnet
```

## 📡 API Reference

### Administrative Functions

| Function | Access | Description |
|----------|--------|-------------|
| `initialize-bridge()` | Deployer | Activate bridge operations |
| `pause-bridge()` | Deployer | Emergency halt all operations |
| `resume-bridge()` | Deployer | Resume operations after pause |
| `add-validator(principal)` | Deployer | Grant validator privileges |
| `remove-validator(principal)` | Deployer | Revoke validator privileges |

### Core Functions

| Function | Access | Description |
|----------|--------|-------------|
| `initiate-deposit(tx-hash, amount, recipient, btc-sender)` | Validators | Create new deposit record |
| `confirm-deposit(tx-hash, signature)` | Validators | Multi-sig deposit confirmation |
| `withdraw(amount, btc-recipient)` | Users | Initiate Stacks→Bitcoin transfer |
| `emergency-withdraw(amount, recipient)` | Deployer | Emergency asset recovery |

### Query Functions

| Function | Returns | Description |
|----------|---------|-------------|
| `get-deposit(tx-hash)` | Deposit details | Retrieve deposit information |
| `get-bridge-status()` | Boolean | Check if bridge is paused |
| `get-validator-status(principal)` | Boolean | Check validator authorization |
| `get-bridge-balance(principal)` | Uint | Get user's bridge balance |

## 🔒 Security Features

### Multi-Layer Protection

- **Validator Consensus**: Requires multiple validator confirmations
- **Amount Limits**: Configurable min/max deposit amounts
- **Emergency Controls**: Immediate pause capability
- **Transaction Uniqueness**: Prevents double-spending attacks
- **Signature Validation**: Cryptographic signature verification

### Constants

```clarity
MIN-DEPOSIT-AMOUNT: 100,000 units
MAX-DEPOSIT-AMOUNT: 1,000,000,000 units
REQUIRED-CONFIRMATIONS: 6 blocks
```

## ⚡ Error Codes

| Code | Constant | Description |
|------|----------|-------------|
| 1000 | `ERROR-NOT-AUTHORIZED` | Insufficient permissions |
| 1001 | `ERROR-INVALID-AMOUNT` | Amount outside valid range |
| 1002 | `ERROR-INSUFFICIENT-BALANCE` | Insufficient bridge balance |
| 1003 | `ERROR-INVALID-BRIDGE-STATUS` | Invalid bridge state |
| 1004 | `ERROR-INVALID-SIGNATURE` | Invalid signature provided |
| 1005 | `ERROR-ALREADY-PROCESSED` | Transaction already processed |
| 1006 | `ERROR-BRIDGE-PAUSED` | Bridge operations paused |

## 🧪 Testing

### Unit Tests

```bash
# Run contract tests
clarinet test

# Run specific test file
clarinet test tests/bridge_test.ts
```

### Integration Testing

1. Deploy to testnet
2. Add test validators
3. Perform test deposits
4. Verify cross-chain functionality

## 🛠️ Development

### Local Development

```bash
# Clone repository
git clone https://github.com/raymondbassey/bitstack-bridge.git
cd bitstack-bridge

# Install dependencies
clarinet requirements

# Run local development environment
clarinet integrate
```

### Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/enhancement`)
3. Commit changes (`git commit -am 'Add enhancement'`)
4. Push to branch (`git push origin feature/enhancement`)
5. Create Pull Request
