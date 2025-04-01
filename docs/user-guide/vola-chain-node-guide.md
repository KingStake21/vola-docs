---
sidebar_position: 1
---

# Vola Chain Node Guide

## Running a Vola Chain Node

This guide will walk you through setting up and running a Vola Chain Node using Docker.

### System Requirement

:::note

All system requirements currently apply to the devnet phase. As we transition to testnet and mainnet, these requirements may evolve, and the document will be updated accordingly.

:::

- 4 GB Ram
- 2 Core CPU
- 20 GB Storage (50 GB for Archive Node)

### Prerequisites

Before you begin, ensure you have the following installed:

- **Docker:**
  For containerization.
- **Docker Compose:**
  For orchestrating multi-container setups.

### Quick Start

1. **Clone the Repository:**
   Clone the Vola Node Docker repository to your local machine:

```bash
git clone https://github.com/Nuvola-Digital/vola-node-docker
cd vola-node-docker
```

2. **Configure Environment Variables:**

   - Copy the _.env.example_ file to _.env_:

   ```bash
   cp .env.example .env
   ```

   - Open the _.env_ file and update the following variables:

     - **AURA_SURI**: Specifies the SURI for generating Aura keys. This should be 32 bytes in hex format (0x-prefixed with 64 additional 0-f bytes). Eg: `0x1bbae82623cd6e7475cfd80f59da9f0e2b65597a8e681d60b7eb2a5353d64097`

     - **GRAN_SURI**: Specifies the SURI for generating Grandpa keys. This should be 32 bytes in hex format (0x-prefixed with 64 additional 0-f bytes). Can be same as _AURA_SURI_. Eg: `0x1bbae82623cd6e7475cfd80f59da9f0e2b65597a8e681d60b7eb2a5353d64097`

     - **NODE_NAME**: The name of the node. This is used to identify your node within the network.

     - **NODE_KEY**: Secret key to use for p2p networking. The value is parsed as a hex-encoded Ed25519 32 byte secret key (64 hex chars).Eg: `5c507c46d949698a3ff6ae2e5cc9808b26b3b33c0623440dc4c563c1a0d0b2be`

     - **KEYSTORE_PASSWORD**: The password used to unlock the node's keystore.

     - **PORT**: The port number used for the node's peer-to-peer network communication (default is 30333).

     - **RPC_PORT**: The port number for remote procedure calls (RPC), which is used for interacting with the node (default is 9944).

     - **PROMETHEUS_PORT**: The port number for prometheus exporter, for monitoring node (default is 9615).

     Ensure that these variables are properly configured to match your environment and security requirements.

3. **Generate SURI and Node Key:**

   - To generate the required SURI for Aura and Grandpa keys:

   ```bash
   docker run --rm -it nuvoladigital/vola-node key generate
   ```

   This will output a secret seed, which is the required SURI.

   - To generate the node key:

   ```bash
   docker run --rm -it nuvoladigital/vola-node key generate-node-key
   ```

   This will provide the node key, which is the second value output by the command.

4. **Start the Node:**
   With your environment variables configured, you can start the node by running Docker Compose:

   ```bash
   docker-compose up
   ```

   This will bring up the Vola node and begin syncing with the network.

5. **Access the Node:**
   Once the node is up and running, you can interact with it using the RPC port (default: 9944).

## Updating Vola Chain Node

### Updating the Vola Chain Node Docker Image

If you have an older version of the vola node running, update the **Docker image** using the following steps:

1. **Pull the latest image:**

```bash
docker pull nuvoladigital/vola-node:latest
```

2. **Shut down the existing container:**

```bash
docker compose down
```

3. **Restart the container with the updated image:**

```bash
docker compose up
```

:::note
Make sure you are on the same directory as aggregator node docker repo.
:::

### Upgrading Chain to new Devnet (v2)

If you already have a Vola chain running during the first devnet release, follow the steps below to upgrade your **Vola Chain Node**.

1. **Purge previous chain**

:::important
As we are currently in the development phase, many unstable changes may require the chain to be purged. Once the chain transitions to the testnet phase, there will be no need for purging, and proper migration will be managed through a runtime upgrade.
:::

```bash
docker exec -it vola-devnet-node /usr/local/bin/vola-node purge-chain --chain devnet
```

2. **Shut down the existing container:**

```bash
docker compose down
```

3. **Restart the container with the updated image:**

```bash
docker compose up
```
