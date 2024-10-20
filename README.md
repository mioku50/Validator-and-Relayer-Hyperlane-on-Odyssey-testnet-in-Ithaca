---
cover: .gitbook/assets/IMG_9498 (1).JPG
coverY: 0
---

# Guide

**I also have a guide (in Russian) with screenshots in my Medium** - https://bit.ly/3XvBdru



_**Validators**_

Hyperlane validators are the offchain agents responsible for security: they monitor messages in a chain mailbox and, if necessary, sign the merkle root to confirm the current state of the mailbox.

This signature is stored and made publicly available , which is then used by the off-chain relay and the inter-chain security modules in the network. Validators are not networked and do not need consensus; nor do they perform regular transactions on the onchain.

Following this guide, you should be able to run a Hyperlane validator on any of the existing chains on which the protocol runs. Hyperlane validators are run on the original chains.

In this guide, we will run the validator on the Odyssey testnet chain, but you can run it on any other chain.

_**Relayer**_

Each Hyperlane message requires two transactions to be delivered: one in the sender chain to send the message, and one in the receiver chain to receive it. A relay is responsible for sending the second transaction.

Hyperlane relayers are configured to relay messages between one or more origin and destination chains. Relayer does not have special permissions in Hyperlane. If Relayer keys are compromised, only the tokens stored on those keys are at risk.

**Validator installation**

_**Minimum technical specifications**_

_2 CPU, 2GB RAM, 4GB SSD_

_**Server preparation**_

```
sudo apt update
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
sudo apt-get install git cargo clang cmake build-essential pkg-config openssl libssl-dev protobuf-compiler
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

_**Install Foundry**_

```
curl -L https://foundry.paradigm.xyz | bash
source /root/.bashrc
foundryup
```

_**Install Screen**_

```
sudo apt screen
```



_**Install Hyperlane CLI**_

```
npm install -g @hyperlane-xyz/cli
```

If there are errors during installation and NPM cusses, install NVM and then install CLI again.

```
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.nvm/nvm.sh
nvm install --lts
nvm use --lts
npm --version
```

_**Next, initialize the multisig ISM configuration**_

`hyperlane core init --advanced`



Select - _messageIdMultisigIsm_

_Enter threshold of validators (number) for message ID multisig ISM - **writing 1**_

_Enter validator addresses (comma separated list) for message ID multisig ISM - **writing your EVM public address**_

_Select default hook type - **select merkleTreeHook**_

Next, select - _aggregationHook **and**_** enter 1**

Next, **select protocolFee** and answer the following questions::

_For protocol fee hook, enter owner address — **your EVM public address**_

_Use this same address — **YES**_

_Enter max protocol fee for protocol fee hook — **100000000000000000**_

_Enter protocol fee for protocol fee hook — **0.1**_



_**Deploy Contracts**_

&#x20;_**Your selected network should have at least**_ _**0.02 ETH**_

```
hyperlane core deploy
```

HYP\_Key — **enter your Private key EVM (Metamask)**

_Select network type — **select testnet or mainnet**_

_Select chain to connect —_ **I chose** _Odyssey testnet_

_Do you want to use an API key to verify on this (basesepolia) chain’s block explorer — **N (No) and ENTER**_



**We are now ready to generate an agent configuration using our deployed contracts**

```
hyperlane registry agent-config --chains odysseytestnet

```

_**Prescribing dependencies**_

`export CONFIG_FILES=$HOME/configs/agent-config.json mkdir r -p tmp/hyperlane-validator-signatures-odysseytestnet export VALIDATOR_SIGNATURES_DIR=/tmp/hyperlane-validator-signatures-odysseytestnet mkdir -p $VALIDATOR_SIGNATURES_DIR`



**Starting the Validator**

_**Clone Repository**_

```
git clone git@github.com:hyperlane-xyz/hyperlane-monorepo.git
```

We will be asked for permissions in the form of an SSH Key and a password to it If you don't have an SSH Key, we'll make one.

```
ssh-keygen -t rsa -b 4096 -C your Email
cat ~/.ssh/id_rsa.pub
```

Copy the key that the team gave us and go to your Github page. Click “Setting” in the right corner and get to the profile settings.

Next, click SSH and GPG Keys and in the SSH key tab enter our previously copied value and clone the repository again

Once the repository has been successfully cloned, open a new tab and **write the following**

```
screen -S hypval
cd hyperlane-monorepo
cd rust
cd main
```

**Run Validator**

```
cargo run --release --bin validator -- \
    --db ./hyperlane_db_validator_odysseytestnet \
    --originChainName odysseytestnet\
    --checkpointSyncer.type localStorage \
    --checkpointSyncer.path $VALIDATOR_SIGNATURES_DIR \
    --validator.key <your_validator_key
```

\<your\_validator\_key>  - Your **EVM private key (Metamask)** Be sure to **insert 0x in front of our private key !!!!** We wait for “Build” from half an hour to several hours and when it is finished we should see "Green" logs

That's it! The validator has been successfully installed

We minimize our session (tab) by pressing **CTRL+A+D** and open a **new window.**

**Run Relayer**

```
screen -s hyprel
cd hyperlane-monorepo
cd rust
cd main
```

```
cargo run --release --bin relayer -- \
    --db ./hyperlane_db_relayer \
    --relayChains <chain_1_name>,<chain_2_name> \
    --allowLocalCheckpointSyncers true \
    --defaultSigner.key <your_relayer_key> \
    --metrics-port 9091
```

\<chain\_1\_name>,\<chain\_2\_name> _**Example** odysseytestnet,sepolia_

\<your\_relayer\_key>  - **your private key **_**EVM (Metamask)**_



**Logs**

**Screen -ls**

```
screen -x hypval
screen -x hyprel
```



Official Doc — [https://docs.hyperlane.xyz/docs/guides/deploy-hyperlane-local-agents](https://docs.hyperlane.xyz/docs/guides/deploy-hyperlane-local-agents)

_Twitter_ — [https://x.com/hyperlane](https://x.com/hyperlane)

_Discord_ — [https://discord.com/invite/hyperlane](https://discord.com/invite/hyperlane)\
\
