# Cost Efficiency

Noir uses highly efficient  blockchain technology to provide cost-efficent solutions to users.

## Verify on each app chain

The beacon could be verified once per app chain as an alternative to being verified on one chain and then distributed across chains. The beacon would then need to be submitted to each chain by the bot network. A Noir chain and ETH relays would no longer be required as a result. The interval between publication and callback might also be eliminated.

However, drand verification consumes a lot of gas and doing that once per chain is potentially inefficient. When blockspace is limited, the beacon submission transaction might not get committed for a long time.

There are pros and cons on both sides. When thinking about hundreds of connected app chains, the deduplication of the verification feels right. With ETH queries upcoming, our state becomes your state and thus it makes sense to have one "randomness" chain accessible from all chain very easily.

## The app chain model

A drand verifier that is accessible via ETH can be implemented on an existing chain with [Ethereum](https://ewasm.readthedocs.io/en/mkdocs/). This would be easier to start with and would not require a new token. However, going for a custom app chain has the following motivation:

-  is an ecosystem of app specific chains where creating one chain is relatively easy.
  The mentality in the ecosystem is to have many chains that are independent and interconnected.
  The tooling and people are ready for many chains.
- In a world with competing app chains that host our users, running on one of those chains makes us biased towards this chain.
  Being neutral regarding chains is very helpful in politics.
- The randomness chain would not halt if the smart contract chain halts.
- The ability to create overlapping validator and drand MPC sets is a way to incentivise drand node operators and get new players into Cosmos.
- The following optimizations are possible:
  1. The drand verification contract is 550 KB large. Terrand had to split the code in two contracts to deploy it to Terra.
     A custom chain can allow larger contract sizes and we now have the logic
     implemented in one contract.
  2. The verification consumes significant block space and may get expensive on other general purpose chains.
  3. CosmWasm allows pinning contracts. Those contracts are kept in memory and are loaded and executed faster.
     We can utilize this feature on a custom chain, and we can also reduce the
     cost of verifying the beacons.
  4. CosmWasm has a somewhat unknown cronjob feature that allows governance to run contract execution in every block.
     That’s useful to e.g. process queues.
  5. We can use chain governance to upgrade the contract, which is more transparent and safer than multisig upgradability.
  6. Due to permissioned contract uploads, the use of a Wasm runtime backend with a slower but stronger optimizer becomes possible,
     which can lead to faster verification.
