---
description: >-
  BRI native bridge is used to relay the BRI native token between BRI and
  Ethereum networks
---

# BRI: Ethereum ↔ BRI

BRI native bridge between Ethereum and BRI is used to relay the BRI native token from BRI to Ethereum network

## Architecture Overview

The BRI bridged is based on POA's bridge implementation, it is used to transfer BRI tokens between the BRI chain and the Ethereum network.

Tokens sent to the respective bridge contract on one network \(whether it's BRI or Ethereum\) are "locked" in the bridge, "unlocked" on the other network bridge and transferred to the sender. The bridge contracts are deployed on both networks, and bridge oracle processes run on each validators machine as part of the validator deployment stack.

Besides the transfer of BRI tokens between the two networks, the bridge is also responsible for network core functionality events of relaying the block rewards to the Ethereum network:

**Mint block reward distributed on the BRI chain on Ethereum**

Each cycle the total block reward distributed on BRI chain is minted on Ethereum and locked on the bridge contract.

This works by listening to the \`RewardedOnCycle\` event emitted by the BlockReward contract on BRI chain, waiting for all bridge validators on BRI chain to sign it, and eventually sending a transaction to mint on Ethereum \(by the last signing validator\).

## Contracts

Home side of the bridge on the BRI network: [0xd617774b9708F79187Dc7F03D3Bdce0a623F6988](https://briscan.io/address/0xd617774b9708F79187Dc7F03D3Bdce0a623F6988/transactions)

Foreign side of the bridge on the Ethereum network: [0xF3Df99C209bd25E20fF59d2040C870e6A1286CF2](https://briscan.io/address/0xF3Df99C209bd25E20fF59d2040C870e6A1286CF2/transactions)

BRI token on the Ethereum network: [0x970B9bB2C0444F5E81e9d0eFb84C8ccdcdcAf84d](https://etherscan.io/token/0x970b9bb2c0444f5e81e9d0efb84c8ccdcdcaf84d)

## Source Code

{% embed url="https://github.com/briscan/bri-bridge/tree/master/native-to-erc20/contracts" %}

## How to use

On BRI you send native BRI token to the home bridge contract. Then you receive an equal amount of the BRI token on the Ethereum network, sent from the foreign bridge contract

On Ethereum network you transfer the BRI token to the foreign bridge contract. After a couple of confirmations, you will receive equal amount of the BRI native token, sent from the home bridge contract.

#### 

