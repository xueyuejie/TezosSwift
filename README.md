# TezosSwift

A comprehensive Swift library for Tezos blockchain interactions, providing wallet management, RPC communication, and transaction operations.

## Features

- **Wallet Management**: Key pair generation with BIP39 mnemonic support
- **RPC Provider**: Complete Tezos JSON-RPC API client
- **Transaction Operations**: Create, simulate, and send Tezos operations
- **Fee Estimation**: Automatic transaction fee calculation
- **Token Support**: FA1.2 and FA2 token operations
- **NFT Support**: Tezos NFT fetching and management

## Requirements

- Swift 5.5+
- iOS 13.0+ / macOS 10.15+

## Installation

### Swift Package Manager

Add TezosSwift to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/xueyuejie/TezosSwift.git", from: "1.0.0")
]
```

## Usage

### Key Pair Generation

```swift
import TezosSwift

// Generate random key pair
let keyPair = try TezosKeypair.randomKeyPair()

// Create from mnemonic with derivation path
let mnemonic = "abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about"
let keyPair = try TezosKeypair(mnemonics: mnemonic, path: "m/44'/1729'/0'/0'")

// Create from private key
let keyPair = try TezosKeypair(privateKey: "edsk...")

// Access keys
print(keyPair.address.address)  // tz1...
print(keyPair.privateKey)       // edsk...
print(keyPair.publicKey.hex)    // public key bytes
```

### RPC Provider

```swift
import TezosSwift

// Initialize RPC provider
let provider = TezosRpcProvider(nodeUrl: "https://mainnet.smartpy.io")

// Get balance
let balance = try await provider.getXTZBalance(address: "tz1...")

// Get chain ID
let chainId = try await provider.getChainID()

// Get block head
let blockHead = try await provider.getBlocksHead()

// Get account counter
let counter = try await provider.getCounter(address: "tz1...")

// Get manager key
let managerKey = try await provider.getManagerKey(address: "tz1...")
```

### Token Operations

```swift
import TezosSwift

// Get FA1.2 token balance
let fa12Balance = try await provider.getFa1_2TokenBalancee(
    address: "tz1...",
    mint: "KT1...", // token contract
    chainId: "NetXdQpr9Vk9Aw"
)

// Get FA2 token balance
let fa2Balance = try await provider.getFa2TokenBalancee(
    address: "tz1...",
    contract: "KT1...", // token contract
    tokenId: "0",
    chainId: "NetXdQpr9Vk9Aw"
)
```

### NFT Operations

```swift
import TezosSwift

// Get NFTs for address
let nfts = try await provider.getNfts(address: "tz1...")

// NFT result includes
// - image: URL to NFT image
// - description: NFT description
// - name: NFT name
```

### Transaction Building

```swift
import TezosSwift

// Get blockchain metadata
let metadata = try await provider.getMetadata(address: "tz1...")

// Create transaction
let transaction = TezosTransaction(
    branch: metadata.blockHash,
    metadata: metadata
)

// Add transaction operation
let operation = TransactionOperation(
    source: keyPair.address.address,
    destination: "tz1...",
    amount: "1000000", // amount in mutez
    counter: "\(metadata.counter)",
    fee: "0",
    gas_limit: "0",
    storage_limit: "0"
)
transaction.operations.append(operation)
```

### Transaction Simulation

```swift
import TezosSwift

// Get simulation response
let simulation = try await provider.getSimulationResponse(
    operations: transaction.operations,
    headHash: "head",
    metadata: metadata
)

// Preapply transaction
let preappliedTx = try await provider.preapplyTransaction(transaction: transaction)
```

### Signing & Sending

```swift
import TezosSwift

// Sign transaction
let messageDigest = try transaction.forge()
let signature = try keyPair.signDigest(messageDigest: messageDigest)

// Send signed transaction
let txHash = try await provider.sendTransaction(transaction: transaction)
print("Transaction hash: \(txHash)")
```

### Network Constants

```swift
import TezosSwift

// Get network constants
let constants = try await provider.getConstants()

// Constants include
// - hard_gas_limit_per_operation
// - hard_gas_limit_per_block
// - cost_per_byte
// - cost_per_bit
```

## Address Format

```swift
import TezosSwift

// Tezos address prefix: tz1 (Ed25519 public key hash)
// Private key prefix: edsk
// Signature prefix: edsig, sig

// Validate address
let address = TezosAddress("tz1...")
if let validAddress = address {
    print(validAddress.address)
}
```

## Dependencies

- **Base58Swift**: Base58Check encoding/decoding
- **Blake2**: Hash functions
- **BIP39swift**: Mnemonic generation
- **PromiseKit**: Promise-based async operations
- **TweetNacl**: Ed25519 signatures
- **BigInt**: Arbitrary precision integers

## RPC Endpoints

```swift
// Mainnet
"https://mainnet.smartpy.io"
"https://rpc.tzbeta.net"

// Testnet (Hangzhounet)
"https://hangzhou.smartpy.io"
```

## License

MIT License

---

# TezosSwift 中文文档

一个全面的 Tezos 区块链交互 Swift 库，提供钱包管理、RPC 通信和交易操作功能。

## 功能特性

- **钱包管理**：支持 BIP39 助记词的密钥对生成
- **RPC 提供者**：完整的 Tezos JSON-RPC API 客户端
- **交易操作**：创建、模拟和发送 Tezos 操作
- **费用估算**：自动交易手续费计算
- **代币支持**：FA1.2 和 FA2 代币操作
- **NFT 支持**：Tezos NFT 获取和管理

## 系统要求

- Swift 5.5+
- iOS 13.0+ / macOS 10.15+

## 安装方式

### Swift Package Manager

在 `Package.swift` 中添加依赖：

```swift
dependencies: [
    .package(url: "https://github.com/xueyuejie/TezosSwift.git", from: "1.0.0")
]
```

## 使用示例

### 密钥对生成

```swift
import TezosSwift

// 生成随机密钥对
let keyPair = try TezosKeypair.randomKeyPair()

// 从助记词创建并指定派生路径
let mnemonic = "abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon abandon about"
let keyPair = try TezosKeypair(mnemonics: mnemonic, path: "m/44'/1729'/0'/0'")

// 从私钥创建
let keyPair = try TezosKeypair(privateKey: "edsk...")

// 访问密钥
print(keyPair.address.address)  // tz1...
print(keyPair.privateKey)       // edsk...
print(keyPair.publicKey.hex)    // 公钥字节
```

### RPC 提供者

```swift
import TezosSwift

// 初始化 RPC 提供者
let provider = TezosRpcProvider(nodeUrl: "https://mainnet.smartpy.io")

// 获取余额
let balance = try await provider.getXTZBalance(address: "tz1...")

// 获取链 ID
let chainId = try await provider.getChainID()

// 获取区块头
let blockHead = try await provider.getBlocksHead()

// 获取账户计数器
let counter = try await provider.getCounter(address: "tz1...")

// 获取管理密钥
let managerKey = try await provider.getManagerKey(address: "tz1...")
```

### 代币操作

```swift
import TezosSwift

// 获取 FA1.2 代币余额
let fa12Balance = try await provider.getFa1_2TokenBalancee(
    address: "tz1...",
    mint: "KT1...", // 代币合约
    chainId: "NetXdQpr9Vk9Aw"
)

// 获取 FA2 代币余额
let fa2Balance = try await provider.getFa2TokenBalancee(
    address: "tz1...",
    contract: "KT1...", // 代币合约
    tokenId: "0",
    chainId: "NetXdQpr9Vk9Aw"
)
```

### NFT 操作

```swift
import TezosSwift

// 获取地址的 NFT
let nfts = try await provider.getNfts(address: "tz1...")

// NFT 结果包含
// - image: NFT 图片 URL
// - description: NFT 描述
// - name: NFT 名称
```

### 交易构建

```swift
import TezosSwift

// 获取区块链元数据
let metadata = try await provider.getMetadata(address: "tz1...")

// 创建交易
let transaction = TezosTransaction(
    branch: metadata.blockHash,
    metadata: metadata
)

// 添加交易操作
let operation = TransactionOperation(
    source: keyPair.address.address,
    destination: "tz1...",
    amount: "1000000", // 金额单位 mutez
    counter: "\(metadata.counter)",
    fee: "0",
    gas_limit: "0",
    storage_limit: "0"
)
transaction.operations.append(operation)
```

### 交易模拟

```swift
import TezosSwift

// 获取模拟响应
let simulation = try await provider.getSimulationResponse(
    operations: transaction.operations,
    headHash: "head",
    metadata: metadata
)

// 预应用交易
let preappliedTx = try await provider.preapplyTransaction(transaction: transaction)
```

### 签名与发送

```swift
import TezosSwift

// 签名交易
let messageDigest = try transaction.forge()
let signature = try keyPair.signDigest(messageDigest: messageDigest)

// 发送已签名交易
let txHash = try await provider.sendTransaction(transaction: transaction)
print("交易哈希: \(txHash)")
```

### 网络常量

```swift
import TezosSwift

// 获取网络常量
let constants = try await provider.getConstants()

// 常量包含
// - hard_gas_limit_per_operation
// - hard_gas_limit_per_block
// - cost_per_byte
// - cost_per_bit
```

## 地址格式

```swift
import TezosSwift

// Tezos 地址前缀：tz1（Ed25519 公钥哈希）
// 私钥前缀：edsk
// 签名前缀：edsig, sig

// 验证地址
let address = TezosAddress("tz1...")
if let validAddress = address {
    print(validAddress.address)
}
```

## 依赖库

- **Base58Swift**: Base58Check 编解码
- **Blake2**: 哈希函数
- **BIP39swift**: 助记词生成
- **PromiseKit**: 基于 Promise 的异步操作
- **TweetNacl**: Ed25519 签名
- **BigInt**: 任意精度整数

## RPC 节点

```swift
// 主网
"https://mainnet.smartpy.io"
"https://rpc.tzbeta.net"

// 测试网（Hangzhounet）
"https://hangzhou.smartpy.io"
```

## 许可证

MIT License
