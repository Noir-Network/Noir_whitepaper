# Cost Efficiency

Noir uses highly efficient  blockchain technology to provide cost-efficent solutions to users.

## Verify on each app chain

The beacon could be verified once per app chain as an alternative to being verified on one chain and then distributed across chains. The beacon would then need to be submitted to each chain by the bot network. A Noir chain and ETH relays would no longer be required as a result. The interval between publication and callback might also be eliminated.

However, drand verification consumes a lot of gas and doing that once per chain is potentially inefficient. When blockspace is limited, the beacon submission transaction might not get committed for a long time.

There are pros and cons on both sides. When thinking about hundreds of connected app chains, the deduplication of the verification feels right. With ETH queries upcoming, our state becomes your state and thus it makes sense to have one "randomness" chain accessible from all chain very easily.

## The app chain model

A drand verifier that is accessible via ETH can be implemented on an existing chain with [Ethereum](https://ewasm.readthedocs.io/en/mkdocs/). This would be easier to start with and would not require a new token. However, going for a custom app chain has the following motivation:

- In the ecosystem of app-specific chains on Ethereum, building a chain is comparatively simple.
The ecosystem functions best when there are numerous chains that are both independent and connected.
For numerous chains, the tooling and personnel are prepared.
- Running on one of the competing app chains renders us biassed in favour of that chain in a world where those chains host our consumers.
Being chain-neutral is tremendously valuable in politics.
- The randomness chain would not halt if the smart contract chain halts.
- The ability to create overlapping validator and drand MPC sets is a way to incentivise drand node operators and get new players into Ethereum.
- The following optimizations are possible:
  1. The drand verification contract is 550 KB large.
     A custom chain can allow larger contract sizes and we now have the logic
     implemented in one contract.
  2. The verification consumes significant block space and may get expensive on other general purpose chains.
  3. EthereumWasm can be used to pin contracts.These contracts are loaded and performed more quickly since they are stored in memory.This functionality can be applied to a unique chain, and it also lowers the price of beacon verification.
 
  4. EthereumWasm has a somewhat unknown cronjob feature that allows governance to run contract execution in every block.
     That’s useful to e.g. process queues.
  5. We can use chain governance to upgrade the contract, which is more transparent and safer than multisig upgradability.
  6. Due to permissioned contract uploads, the use of a Wasm runtime backend with a slower but stronger optimizer becomes possible,
     which can lead to faster verification.
