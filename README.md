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
