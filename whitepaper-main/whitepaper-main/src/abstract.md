# Abstract

Randomness is a basic building block for all sorts of applications.
The use cases range from lotteries that fully rely on randomness over games that may have some random elements to probabilistic modeling, simulations and governance applications.

Blockchains are networks where each node can independently execute transactions and examine the results to authenticate the state of a replicated database. All computations need to be systematic in order to accomplish that. Every year, the operations carried out on blockchains become more complex. While Bitcoin can only send tokens, Ethereum's next generation brought Turing-complete computations to the blockchain, enabling the development of sophisticated financial products and the first applications for governance. As the cost of implementation for Layer 2 solutions and independent Ethereum blockchains decreases noticeably today, we are witnessing an increasing amount of use of this concept. Blockchains can now use sandboxing technology that was created to operate at nearly native speed on modern CPUs thanks to the use of WebAssembly. There are several projects in progress working on rich governance applications and games.

Within these new applications, the access to a secure _public_ source of
randomness has been an unsolved growing need. For example, games need randomness
to distribute new random items as NFTs in game or lottery platforms need true
unbiased randomness to draw the winner. Unfortunately, current applications draw
randomness from insecure sources such as block hash and timestamp.

In this document we describe how Noir brings publicly-verifiable, unbiasable and decentralised randomness on chain and distributes it to a multitude of other ETH enabled chains in a very secure, fast, decentralised, cost-efficient, and developer-focused manner.


