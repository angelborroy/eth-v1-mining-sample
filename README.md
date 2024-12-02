# Running a Private Ethereum 1.0 Blockchain Using Docker

This repository contains a sample configuration to deploy a private Ethereum 1.0 blockchain using Docker. The setup includes essential components like a bootnode for peer discovery, an RPC endpoint for external interactions, and a miner for blockchain operations.

## Features

- **Bootnode**: Facilitates peer discovery and manages network connections (port `30303`).
- **JSON-RPC Endpoint**: Provides an API to interact with the blockchain (port `8545`).
- **Miner**: Handles block mining to maintain the blockchain.

## Prerequisites

1. Docker and Docker Compose installed on your system.
2. Basic understanding of Ethereum and blockchain concepts.

## Getting Started

### 1. Clone the Repository

Clone this project to your local machine: 

```bash
git clone https://github.com/angelborroy/eth-v1-mining-sample.git
cd eth-v1-mining-sample
```

### 2. Explore the Genesis Block Configuration

The `genesis.json` file contains the genesis block information:

- `config.chainId`: Blockchain identifier. Set to `1214` to avoid conflicts with public Ethereum networks.
- `config.ethash`: Defines the consensus mechanism. An empty value indicates **proof-of-work**.

### 3. Docker Configuration

The setup includes:

- **Dockerfile**: Builds the Go Ethereum (Geth) image with required configurations.
- **compose.yaml**: Defines the services:
  - **`geth-bootnode`**: Bootnode for peer discovery with a Node Key.
  - **`geth-rpc-endpoint`**: Connects to the bootnode (`enode://` calculated from the Node Key) and exposes port `8545`.
  - **`geth-miner`**: Mining node connected to the bootnode.

### 4. Start the Blockchain Network

Run the following command to build and start the Docker containers:

```bash
docker-compose up --build --force-recreate
```

### 5. Verify Connectivity

Check if the nodes are connected using the `admin_peers` method:

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "admin_peers",
    "params": []
}'
```

Ensure the `enode` in the response matches the one in `docker-compose.yaml`.

## Interacting with the Blockchain

### Get the Latest Block Number

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 2,
    "method": "eth_blockNumber",
    "params": []
}'
```

### List Accounts

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 3,
    "method": "eth_accounts",
    "params": []
}'
```

### Check Account Balance

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 4,
    "method": "eth_getBalance",
    "params": ["<account_address>", "latest"]
}'
```

### Create a New Account

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 5,
    "method": "personal_newAccount",
    "params": ["<password>"]
}'
```

### Transfer Ether

Unlock the sender account and send 1 ETH to a recipient:

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 6,
    "method": "personal_unlockAccount",
    "params": ["<sender_address>", "<password>"]
}'

curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 7,
    "method": "eth_sendTransaction",
    "params": [{
        "from": "<sender_address>",
        "to": "<recipient_address>",
        "value": "0xde0b6b3a7640000"
    }]
}'
```

### Check Transaction Status

Retrieve the transaction status using the transaction hash:

```bash
curl -X POST http://localhost:8545 -H "Content-Type: application/json" -d '{
    "jsonrpc": "2.0",
    "id": 8,
    "method": "eth_getTransactionByHash",
    "params": ["<transaction_hash>"]
}'
```

## Additional Resources

- [Ethereum JSON-RPC API Documentation](https://eth.wiki/json-rpc/API)
- [Node Key and Enode Information](https://geth.ethereum.org/docs/getting-started/private-net)
- [Ethereum Keystore File Explanation](https://julien-maffre.medium.com/what-is-an-ethereum-keystore-file-86c8c5917b97)

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

Contributions and suggestions are welcome! Feel free to open issues or submit pull requests. 😊
