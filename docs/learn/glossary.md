---
title: Glossary
id: glossary
---


## Swarm

The Swarm network consists of a collection of [Bee nodes](glossary#bee) which work together to enable decentralised data storage for the next generation of censorship-resistant, unstoppable, serverless dapps. 

Swarm is also the name of the core organization that oversees the development and success of the Bee Swarm as a whole. They can be found at [ethswarm.org](https://www.ethswarm.org/).

## Gnosis Chain

[Gnosis Chain](https://www.gnosis.io/) (previously known as xDai chain) is a [PoS](https://www.gnosis.io/validators), [EVM](https://ethereum.org/en/developers/docs/evm/) compatible Ethereum [sidechain](https://ethereum.org/en/developers/docs/scaling/sidechains/) which uses the same addressing scheme as Ethereum. Swarm's smart contracts have been issued on Gnosis Chain.

## Smart Contracts

Smart contracts are automatically executable code which can be published on a blockchain to ensure immutability. Swarm uses smart contracts on Gnosis Chain for a variety of key aspects of the network including [incentivization](glossary#xbzz-token), [inter-node accounting](glossary#swap), and [payments for storage](glossary#postage-stamps).

## Bee

Swarm nodes are referred to as "Bee" nodes. Bee nodes can run on a wide variety of computer types including desktop computers, hobbyist computers like Raspberry Pi 4, remotely hosted virtual machines, and much more. When running, Bee nodes interact with Swarm smart contracts on Gnosis Chain and connect with other Bee nodes to form the Swarm network.

Bee nodes can act as both client and service provider, or solely as client or service provider, depending on the needs of the node operator. Bee nodes pay each other for services on the Swarm network with the xBZZ token.

## Overlay

An overlay network is a virtual or logical network built on top of some lower level "underlay" network. Examples include the Internet as an overlay network built on top of the telephone network, and the p2p Bittorent network built on top of the Internet. 

With Swarm, the overlay network is based on a [Kademlia DHT](https://en.wikipedia.org/wiki/Kademlia) with overlay addresses derived from each node's [Gnosis](glossary#gnosis-chain) address. Swarm's overlay network addresses are permanent identifiers for each node and do not change over time.

## Overlay Address

Overlay addresses are a Keccak256 hash of a node’s Gnosis Chain address and the Swarm network ID (the Swarm network ID is included to prevent address collisions). The overlay address for a node does not change over time and is a permanent identifier for the node. If not otherwise specified, when referring to a "node address", it typically is referring to the overlay address, not the underlay address. The overlay address is the address used to determine which nodes connect to each other and which chunks nodes are responsible for.

## Underlay

An underlay network is the low level network on which an overlay network is built. It allows nodes to find each other, communicate, and transfer data. Swarm's underlay network is a p2p network built with [libp2p](https://libp2p.io/). Underlay address are not permanent and may change over time. 

## Swap

Swap is the p2p accounting protocol used for Bee nodes. It allows for the automated accounting and settlement of services between Bee nodes in the Swarm network. In the case that services exchanged between nodes is balanced equally, no settlement is necessary. In the case that one node is unequally indebted to another, settlement is made to clear the node's debts. Two key elements of the Swap protocol are [cheques and the chequebook contract](glossary#cheques--chequebook).    

## Cheques & Chequebook

Cheques are the off-chain method of accounting used by the Swap protocol where the issuing node signs a cheque specifying a beneficiary, a date, and an amount, and gives it to the recipient node as a token of promise to pay at a later date. 

The chequebook is the smart contract where the cheque issuer's funds are stored and where the beneficiary can cash the cheque received. 

The cheque and chequebook system reduces the number of required on-chain transactions by allowing multiple cheques to accumulate and be settled together as a group, and in the case that the balance of cheques between nodes is equal, no settlement transaction is required at all. 

## Postage Stamps

Postage stamps can be purchased with [xBZZ](glossary#xbzz-token) and represent the right to store data on the Swarm network. In order to upload data to Swarm, a user must purchase a batch of stamps which they can then use to upload an equivalent amount of data to the network. 


## Kademlia

Kademlia is a distributed hash table (DHT) which is commonly used in distributed peer-to-peer networks. A distributed hash table is a type of hash table which is designed to be stored across a decentralized group of nodes in order to be persistent and fault tolerant. It is designed so that each node is only required to store a subset of the total set of key / value pairs. One of the unique features of the Kademlia DHT design is a distance metric based on the XOR bitwise operation. It is referred to as "Kademlia distance" or just "distance". Swarm’s DISC uses a modified version of Kademlia which has been specialized for storage purposes, and understanding the concepts behind Kademlia is necessary for understanding Swarm.

## Kademlia distance

Within Kademlia, nodes have numeric ids with the same length and format taken from the same namespace as the keys of the key/value pairs. Kademlia distance between node ids and keys is calculated through the XOR bitwise operation done over any ids or keys. 


Note: For a Kademlia DHT, any standardized numerical format can be used for ids. However, within Swarm, ids are derived from a Keccak256 digest and are represented as 256 bit hexadecimal numbers. They are referred to as addresses or hashes.

Example:   We have a Kademlia DHT consisting of only ten nodes with ids of 1 - 10. We want to find the distance between node 4 and 7. In order to do that, we perform the XOR bitwise operation:

4 | 0100   
7 | 0111  
————XOR  
3 | 0011  

And we find that the distance between the two nodes is 3. 



## Chunk

When data is uploaded to Swarm, it is broken down into 4kb sized pieces which are each assigned an address in the same format as node’s overlay addresses. Chunk addresses are formed by taking the BMT hash of the chunk content along with an 8 byte measure of the number of the chunk’s child chunks, the `span`. The BMT hashing algorithm is based on the Keccac256 hashing algorithm, so it produces an address with the same format as that for the node overlay addresses.

## Proximity Order (PO)

Proximity Order is a concept defined in The Book of Swarm and is closely related to Kademlia distance. Proximity order is defined as the number of shared prefix bits of any two addresses. It is found by performing the XOR bitwise operation on the two addresses and counting how many leading 0 there are before the first 1. In other words, PO is equal to the number of shared binary prefix bits.

Taking the previous example used in the Kademlia distance definition:

4 | 0100   
7 | 0111  
————XOR  
3 | 0011  

In the result we find that the distance is 3, and that there are two leading zeros. Therefore for the PO of these two nodes is 2.


Both Proximity Order and distance are measures of the relatedness of ids, however Kademlia distance is a more exact measurement. 


Taking the previous example used in the Kademlia distance definition:  

5 | 0101   
7 | 0111  
————XOR  
2 | 0010  

Here we find that the distance between 5 and 7 is 2, and the PO is also two. Although 5 is closer to 7 than 4 is to 7, they both fall within the same PO, since PO is only concerned with the shared leading bits. PO is a fundamental concept to Swarm’s design and is used as the basic unit of relatedness when discussing the addresses of chunks and nodes. PO is also closely related to the concept of depth.   

## Depth types

There are three fundamental categories of depth:
 
### 1. Topology related depth

This depth is defined in relation to the connection topology of a single node as the subject in relation to all the other nodes it is connected to. It is referred to using several different terms which all refer to the same concept (Connectivity depth / Kademlia depth / neighbourhood depth / physical depth)

Connectivity depth refers to the saturation level of the node’s topology - the level to which the topology of a node’s connections has Kademlia connectivity. Defined as one level deeper than the deepest fully saturated level. A PO is defined as saturated if it has at least the minimum required level of connected nodes, which is set at 8 nodes in the current implementation of Swarm. 

The output from the Bee debug API's `topology` endpoint:

![](/img/depths1.png)

Here we can see the depth is 8, meaning that PO bin 7 is the deepest fully saturated PO bin:

![](/img/depths2.png) 

Here we can confirm that at least 8 nodes are connected in bin 7.

Connectivity depth is defined from the point of view of individual nodes, it is not defined as characteristic of the entire network. However, given a uniform distribution of node ids within the namespace and given enough nodes, all nodes should converge towards the same connectivity depth. 

While this is sometimes referred to as Kademlia depth, the term “Kademlia depth” is not defined within the original Kademlia paper, rather it refers to the depth at which the network in question (Swarms) has the characteristics which fulfill the requirements described in the Kademlia paper.
 
### 2. Area of responsibility related depths

Area of responsibility refers to which chunks a node is responsible for storing. There are two concepts of depth related to a node’s area of responsibility - storage depth and reserve depth. Both reserve depth and storage depth are measures of PO which define the chunks a node is responsible for storing.  
 
### 2a. Reserve Depth

The PO which measures the node’s area of responsibility based on the theoretical 100% utilization of all postage stamp batches (all the chunks which are eligible to be uploaded and stored are uploaded and stored). Has an inverse relationship with area of responsibility - as depth grows, area of responsibility gets smaller.

### 2b. Storage Depth

The PO which measures the node’s effective area of responsibility. Storage depth will equal reserve depth in the case of 100% utilization - however 100% utilization is uncommon. If after syncing all the chunks within the node’s area of responsibility at its reserve depth then node still has sufficient space left, then the storage depth will decrease so that the area of responsibility doubles. 


### 3. Postage stamp batch and chunk related depths

### 3a. Batch depth
Batch depth is the value `d` which is defined in relation to the size of a postage stamp batch. The size of a batch is defined as the number of chunks which can be stamped by that batch (also referred to as the number of slots per batch, with one chunk per slot). The size is calculated by:

* $$2^{d}$$
* $$d$$ is a value selected by the batch issuer which determines how much data can be stamped with the batch

### 3b. Bucket depth 

Bucket depth is the constant value which defines how many buckets the address space for chunks is divided into for postage stamp batches. Bucket depth is set to 16, and the number of buckets is defined as $$2^{bucket depth}$$

## PLUR

PLUR (name inspired by the [PLUR principles](https://en.wikipedia.org/wiki/PLUR)) is the smallest denomination of BZZ. 1 PLUR is equal to 1e-16 BZZ.

## Bridged Tokens

Bridged tokens are tokens from one blockchain which have been _bridged_ to another chain through a smart contract powered bridge. For example, xDAI and xBZZ on Gnosis Chain are the bridged version of DAI and BZZ on Ethereum. 

## BZZ Token

BZZ is Swarm's [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) token issued on Ethereum.   

| Blockchain             | Contract address                                                                                                       |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Ethereum, BZZ          | [`0x19062190b1925b5b6689d7073fdfc8c2976ef8cb`](https://ethplorer.io/address/0x19062190b1925b5b6689d7073fdfc8c2976ef8cb)                |
| Gnosis Chain, xBZZ     | [`0xdBF3Ea6F5beE45c02255B2c26a16F300502F68da`](https://blockscout.com/xdai/mainnet/tokens/0xdBF3Ea6F5beE45c02255B2c26a16F300502F68da/) |
| Goerli (testnet), gBZZ | [`0x2ac3c1d3e24b45c6c310534bc2dd84b5ed576335`](https://goerli.etherscan.io/address/0x2ac3c1d3e24b45c6c310534bc2dd84b5ed576335)         |

## xBZZ Token

xBZZ is BZZ bridged to the [Gnosis Chain](https://www.gnosis.io/) using [OmniBridge](https://omni.gnosischain.com/bridge).

It is used as payment for [postage stamps](glossary#postage-stamps) and as the unit of accounting between the nodes. It is used to incentivize nodes to provide resources to the Swarm.

## DAI Token

[DAI](https://developer.makerdao.com/dai/1/) is an [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) stable token issued on the Ethereum blockchain, tracking USD.

## xDai Token

xDai is [DAI](https://developer.makerdao.com/dai/1/) [bridged](#bridged-tokens) to the [Gnosis Chain](https://www.gnosis.io) using [xDai Bridge](https://bridge.gnosischain.com/). It is also the native token of the Gnosis Chain, i.e. transaction fees are paid in xDai.

## Goerli

Goerli is an Ethereum testnet. It is an environment where smart contracts can be developed and tested without spending cryptocurrency with real value, and without putting valuable assets at risk. Tokens on Goerli are often prefixed with a lower-case 'g', example: 'gBZZ' and 'gETH,' and because this is a test network carry no monetary value. It is an environment where Bee smart contracts can be tested and interacted with without any risk of monetary loss.

## Faucet

A cryptocurrency faucet supplies small amounts of cryptocurrency to requestors (typically for testing purposes).

It supplies small amounts of gBZZ and gETH for anyone who submits a request at the [Swarm Discord](https://discord.gg/wdghaQsGq5) server by using the `/faucet` command in the #develop-on-swarm channel.
