
# Astreum: A Next Generation Blockchain for Apps, Storage and Compute

## Author

Roy R. O. Okello

[Email](mailto:0xR3y@protonmail.com)

[Github](https://github.com/0xR3y)

[Twitter](https://twitter.com/0xR3y)

## Introduction

This paper introduces a next generation blockchain for apps, storage and compute.

- apps: decentralized applications
- storage: distributed storage
- compute: distributed compute

Astreum introduces a model for pricing, verification and payment for storage and compute in a decentralized system.

Astreum primarily works by keeping track of all the accounts and their details such as the balance, code, number of transactions and storage in a block and changes through applying transactions.

Applications are written in the Fusion Language, which is a Lisp dialect whose lists are addressable from Nebula, Distributed Storage Protocol.

Applications can run through Transactions or Reactor, Distributed Compute Protocol.

## Protocols

### Pulsar

Pulsar Network is the distributed hash table peer-to-peer communication protocol for the Astreum Blockchain.

### Nova

Nova is the proof of stake consensus protocol for executing transactions and creating new blocks through participants named Validators.

A Validators must be staked, by sending `Astre` to the nova account, to participate in the protocol.

The nova account address is 0x 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 6E 6F 76 61.

The block time target is three seconds. A slot is the three seconds period when new blocks are created.

Slots are allocated pro-rata to a Validators's stake.

A slot miss occurs when a Validators does not create a new block or creates a malicious block when selected.

Slot selection determines the Validators for the next block at any time:

- Check slot misses from the latest block
- Half the stakes of Validators for each of their missed slots
- If no slot miss, the latest block time proof is used as the seed in the weighted random address selector
- If slot misses, the linear-feedback shift register, shifted to the number of slot misses, of the latest block time proof is used as the seed in the weighted random address selector

Validators who miss a slot get half their stake returned.

Transactions are ordered by their hash.

The creator of a new block is payed a fee of 10^9 `Solar` at the current block's solar price.

### Nebula

Nebula is a protocol for storing and retrieving Nebula Objects.

Users pay the present value of the perpetual storage cost of the object.

Astreum pays storage providers in perpetuity for proofs of storage.

The storage cost is derived from the current supply and demand.

A Nebula Object is a data structure with two fields:

- Leaf: True/False
- Data: a blob of binary data of size < 32KB

All data is erasure coded as a method of data protection in which data is encoded and fragments stored across the nodes on the network.

When nodes go offline, data can be reconstructed from the fragments on the nodes that are online.

`Object Get Flow`

```text

        Client                                          Nebula
        + - - - +                                       
        |       |             object hash               + - - - - - - - +
        |       | - - - - - - - - - - - - - - - - - - > |    nearest    |
        |       | < - - - - - - - - - - - - - - - - - - |     node      |
        |       |       nearer node or object           + - - - - - - - +
        |       |
        |       |
        |       |             object hash               + - - - - - - - +
        |       | - - - - - - - - - - - - - - - - - - > |    nearer     |
        |       | < - - - - - - - - - - - - - - - - - - |     node      |
        |       |         index node or object          + - - - - - - - +
        |       |
        |       |
        |       |               object hash             + - - - - - - - +
        |       | - - - - - - - - - - - - - - - - - - > |    index      |
        |       | < - - - - - - - - - - - - - - - - - - |     node      |
        |       |        storage node or object         + - - - - - - - +
        |       |
        |       |
        |       |             object hash               + - - - - - - - +
        |       | - - - - - - - - - - - - - - - - - - > |    storage    |
        |       | < - - - - - - - - - - - - - - - - - - |     node      |
        |       |               object                  + - - - - - - - +
        |       |
        + - - - +

```

Storage Nodes store the distributed index of the object hashes and their storage provider's id.

The storage provider is paid by providing proofs of storage.

An indexer earns a commission for indexing the underlying data and forwarding storage proofs to validators.

### Reactor

Reactor is a protocol for distributed computation.

Applications can be confidential enabling users to use private data, preserving privacy and use proprietary software, protecting intellectual property.

The compute cost is derived from the current supply and demand.

## Components

### Accounts

An Astreum Account is a data structure of an address and details associated to that account. Each account has a balance, code, counter and storage.

`Accounts State`

```text

                                                        + - - - - - - - - - - - +
                                                        |     accounts hash     |
                                                        + - - - - - - - - - - - +
                                                                ^
                                                . - - - - - - - - - - - - - - - - - - - .
                                                ^                                       ^
                                        + - - - - - - - - +                   + - - - - - - - - - +
                                        | account 1 hash  |                   |   account 2 hash  |
                                        + - - - - - - - - +                   + - - - - - - - - - +
                                                ^
                                . - - - - - - - - - - - - - .
                                ^                           ^
                      + - - - - - - - - - +       + - - - - - - - - - +
                      |   address hash    |       |   details hash    |
                      + - - - - - - - - - +       + - - - - - - - - - +
                                                            ^
                  . - - - - - - - - - - - - - . - - - - - - + - - - - - - . - - - - - - - - - - - - - .
                  ^                           ^                           ^                           ^
          + - - - - - - - - +       + - - - - - - - - - +       + - - - - - - - - - +       + - - - - - - - - - +
          |  balance hash   |       |     code hash     |       |   counter hash    |       |   storage hash    |
          + - - - - - - - - +       + - - - - - - - - - +       + - - - - - - - - - +       + - - - - - - - - - +

```

`Account Storage`

```text

                                                  + - - - - - - - +
                                                  |  storage hash |
                                                  + - - - - - - - +
                                                          ^
                                        . - - - - - - - - + - - - - - - - - - .
                                        ^                                     ^
                                + - - - - - - - - - +               + - - - - - - - - - - - +
                                |  key-value 1 hash |               |   key-value 2 hash    |
                                + - - - - - - - - - +               + - - - - - - - - - - - +
                                        ^
                        . - - - - - - - + - - - - - - - .
                        ^                               ^
                + - - - - - - - +               + - - - - - - - +
                |  key 1 hash   |               |  value 1 hash |
                + - - - - - - - +               + - - - - - - - +

```

`Accounts State Transition`

```text

            Block #1                                Block #2
        + - - - - - - - +                       + - - - - - - - +
        |               |                       |               | 
        |   Previous    |      Transactions     |     Latest    |
        |   Accounts    |   - - - - - - - - ->  |    Accounts   |
        |     State     |                       |     State     |
        |               |                       |               |
        + - - - - - - - +                       + - - - - - - - +

```

The account balance is in the native unit of value which is an `Astre`.

Value magnitudes are:

- 10^24: YottaAstre
- 10^21: ZettaAstre
- 10^18: ExaAstre
- 10^15: PetaAstre
- 10^12: TeraAstre
- 10^9:  GigaAstre
- 10^6:  MegaAstre
- 10^3:  KiloAstre
- 10^0:  Astre

### Blocks

A Block consists of the block body and the validator's ed25519 signature of the body's merkle tree root.

The block body has:

1. accounts hash
2. chain
3. number
4. previous block hash
6. receipts hash
7. solar price
8. solar used
9. time
10. transactions hash
11. validator

### Transactions

A Transaction consists of the transaction body and the sender's ed25519 signature of the body's merkle tree root.

The transaction body has:

1. chain
2. counter
3. data
4. recipient
5. sender
6. solar limit
7. solar price
8. type
9. value

### Solar

`Solar` is the currency for work done on-chain, in contrast with `Astre` with is the currency for value.

The solar limit of a block is set at 10^9 `Solar`.

Transaction Processing costs 10^3 `Solar`.

Solar pricing mechanism:

- The solar price is fixed for every block.
- The solar price varies by 1 `Astre`.
- The solar price doubles when more than 0.9, and halves when less than 0.1, of the previous solar limit was used.
- The base solar price is set at 10^6 `Astre`.

### Fusion

Fusion is the applications platform running on the Astreum Blockchain.

Applications can run through transaction calls on-chain, computation contracts off-chain and natively on nodes.

The Fusion Language is a dialect of the Lisp programming language for developing Fusion Applications.

Fusion capabilities include templating user interfaces that run on nodes.

The Fusion Machine is a stack based native runtime for Fusion Machine Code interfacing with the Astreum Accounts State.

## References

1. Bitcoin: A Peer-to-Peer Electronic Cash System - Satoshi Nakamoto
2. Ethereum White Paper - Vitalik Buterin
3. Ethereum Yellow Paper - Gavin Wood
4. Kademlia: A Peer-to-Peer Information System Based on the XOR Metric - Petar Maymounkov & David Mazières
5. Recursive Functions of Symbolic Expressions and Their Computation by Machine - John McCarthy
6. High-speed high-security signatures - Daniel J. Bernstein, Niels Duif, Tanja Lange, Peter Schwabe and Bo-Yin Yang

2022-06-18
