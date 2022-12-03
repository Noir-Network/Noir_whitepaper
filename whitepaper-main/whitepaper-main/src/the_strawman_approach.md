# The Strawman Approach

Applications using blockchain technology can obtain data from the environment that, at first glance, seems random but is actually deterministic. However, those values are frequently predictable or malleable. E.g.

1. Solidity's `block.timestamp` is set by a single miner.
2. Solidity's `blockhash` is a value the miner can influence.
3. The block time of Tendermint ([BFT Time]) is of low entropy and can be influenced.
4. Block height has very low entropy as the height in which a transaction is included can easily be guessed.
5. Another thing that was spotted in the wild is using signatures as randomness. A pre-defined signer is asked to sign a given challenge. However, [it turns out](https://medium.com/@simonwarta/signature-determinism-for-blockchain-developers-dbd84865a93e) that common signing algorithms produce a deterministic but not unique signature, such that the signer can choose whatever value suits them.

The block hash may be inaccurately considered unpredictable but can be changed by the block proposer, hence the EthereumWasm maintainers decided not to expose it in an attempt to lessen the risk of misuse.

We must rely on superior technology, which might not be included in the native blockchain, to obtain trustworthy randomization.

[bft time]: https://docs.tendermint.com/master/spec/consensus/bft-time.html
