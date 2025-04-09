---
sidebar_position: 2
---

# Vola Aggregator Node Guide

## Setting Up the Aggregator Node

This guide will walk you through setting up and running a Aggregator node using Docker.

### System Requirement

:::note

All system requirements currently apply to the devnet phase. As we transition to testnet and mainnet, these requirements may evolve, and the document will be updated accordingly.

:::

- 8 GB Ram
- 4 Core CPU
- Storage Based-On Commitment

### Prerequisites

Before you begin, ensure you have the following installed:

- **Docker:**
  For containerization.
- **Docker Compose:**
  For orchestrating multi-container setups.
- **A Public Domain:**
  For pointing to the running node.

### Quick Start

1. **Clone the Repository:**
   Clone the Vola Aggregator Node Docker repository to your local machine:

```bash
git clone https://github.com/Nuvola-Digital/aggregator-node-docker
cd aggregator-node-docker
```

2.  **Configure Environment Variables:**

    - Copy the _.env.example_ file to _.env_:

    ```bash
    cp .env.example .env
    ```

    - Open the _.env_ file and update the following variables:

      - **LISTEN_ADDR**:
        IP address interface the node listens on. Default is `0.0.0.0`.
      - **LISTEN_PORT**:
        Port the node listens on. Default is `1331`.
      - **ACCOUNT**:
        Account address of the owner account.
      - **SURI**:
        Secret phrase for owner account.
      - **KEYSTORE_PASSWORD**:
        Password for the keystore. (Optional)
      - **CHAIN_RPC**:
        RPC for vola chain devnet.
      - **STORAGE_CAPACITY**:
        Amount of storage in GB to offer to the network.
      - **GATEWAY_DOMAIN**:
        Public domain that points to the running aggregator node.
        The aggregator node listening at **_$LISTEN_ADDR:$LISTEN_PORT_** should be accessible through **_https://$GATEWAY_DOMAIN:$GATEWAY_PORT_**.
        Eg: mynode.example.com (This is just an example domain, do not use this. Use your own domain.)
      - **GATEWAY_PORT**:
        Port to use with the **_$GATEWAY_DOMAIN_** to reach the running node with SSL access. Default is `443`.

    Ensure that these variables are properly configured to match your environment and security requirements.

3.  **Generate Keys:**
    If `SURI` or `ACCOUNT` is missing, the script will automatically generate them and log the details.

    - To generate the required `SURI` and `ACCOUNT`:

      ```bash
         docker run --rm -it nuvoladigital/aggregator-node key generate
      ```

    The Secret seed or Secret phrase can be passed as `SURI` and public key will be the `ACCOUNT`

4.  **Verify Environment Configuration:**
    Run the following script to check if all required environment variables are set:

    ```bash
    bash env-check.sh
    ```

    If there are missing or incorrect values, update the _.env_ file accordingly.

5.  **Setup SSL and Start the Node:**

    Run the `setup.sh` script, which will:

    - Verify the environment variables.
    - Obtain an SSL certificate using Let's Encrypt.
    - Configure an Nginx reverse proxy with SSL.
    - Start the Aggregator Node using Docker Compose.

    ```bash
    bash setup.sh
    ```

    To run in detached mode, use:

    ```bash
    bash setup.sh --detach
    ```

6.  **Register the Node:**
    Before participating on the aggregation, node should be register in the chain.

    - You would need some funds on the owner account for registering the node (for transaction fee and registration fee, which is based on storage capacity offered). You can use faucet to load test funds on your account.

    - To register the node:

          ```bash
            source .env
            docker exec -it aggregator-node /usr/local/bin/aggregator-node node register --chain-rpc $CHAIN_RPC --address $ACCOUNT --gateway $GATEWAY_DOMAIN --gateway-port $GATEWAY_PORT --capacity $STORAGE_CAPACITY
          ```

    After the registration is completed, your node can start receiving upload requests.

## Updating Aggregator Node Registration Information

When updating your **Aggregator Node** registration details (such as **gateway domain and port**), follow the steps below.

### Run the Update Command

Use the following command to update your node’s registration information:

```bash
docker exec -it aggregator-node /usr/local/bin/aggregator-node node update
  --chain-rpc $CHAIN_RPC
  --address $ACCOUNT
  --gateway $GATEWAY_DOMAIN
  --gateway-port $GATEWAY_PORT
  --node-id {your-node-id-here}
```

:::note
Replace \{your-node-id-here\} with your node id that was recieved during registration.
:::

### Updating the Aggregator Node Docker Image

If you have an older version of the aggregator node running, update the **Docker image** using the following steps:

1. **Pull the latest image:**

```bash
docker pull nuvoladigital/aggregator-node:latest
```

2. **Shut down the existing container:**

```bash
docker compose down
```

3. **Restart the container with the updated image:**

```bash
bash setup.sh
```

To run in detached mode, use:

```bash
bash setup.sh --detach
```

:::note
Make sure you are on the same directory as aggregator node docker repo.
:::

:::important
To upgrade to the new devnet release, you will need to re-register the node on the chain, as the chain has been purged.

As we are currently in the development phase, many unstable changes may require the chain to be purged. Once the chain transitions to the testnet phase, there will be no need for purging, and proper migration will be managed through a runtime upgrade.
:::
