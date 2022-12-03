# Performance

## Commit to Callback Time (C2C Time)

Each Drand round is published at fixed points in time, calculated as follows:

```
publish_time := genesis + (round - 1) * roundTime
```

where `genesis` is the drand network's start time (UNIX timestamp), `round` is an incrementing integer starting at 1 and `roundTime` is the drand round time in seconds. At the beginning, `roundTime` is 30s but the drand project is planning to release a new network with much higher frequency (3-6s).

The randomness must be regarded as public once this "publish time" has passed. Any user can determine the random value by observing the off-chain random network, regardless of whether the chain or the contract already knows it. It is crucial that no further activities for that round be authorised after "publish timean determine the random value by observing the off-chain random network, regardless of whether the chain or the contract already knows it. It is crucial that no further activities for that round be authorised after "publish time." Consider it the last day to submit your lottery tickets. The round should now be processed as quickly as feasible to show the results and enable the operation to continue in case the subsequent steps depend on it.
 
 

The time between beacon publishing an the callback consists of the following components:

- **Noir Bots Registration:** Through a variety of communication protocols, bots should observe the Drake gossip network. They should create a transaction, sign it, and send it to the Noisy mempool as soon as a fresh round is discovered. Keep in mind that only the bot with the fastest submission wins. This should be possible in under a second when connected well. Anyone can contribute to this layer because it has no restrictions on participation. Operators of registered bots get paid for their efforts.
 
 
- **Block inclusion time:** is as follows: the chain should make sure the beacon is included in a block as soon as possible after it is in the mempool. This should take up to 10 seconds with 5-second block durations and an inclusion in the first or second block. The same transaction transmits IBC messages and creates open jobs. The block is accomplished in a fraction of a second.
 
 
-**The IBC relaying:** The message is retrieved from the Noir chain and forwarded to the destination chain by an IBC relayer. This is dependent on properly setting up and interconnecting relayers, as well as on block space being available on the destination chain. The relayer should pay a transaction fee that is accepted by all validators in order to be included as soon as practicable. We don't get assurances that we'll be included right away, though, when there is a lot of traffic. So a reasonable estimate would be 5 to 20 seconds.
- **Acknowledgement:** An IBC acknowledgement is sent to Noir, but this is nothing the contract needs to wait for.

Once those steps are done, the callback is executed within 30 seconds of publishing. On a well configured network, and depending on the block time, average timings can be much faster though.

## Choice of round

<!---
XXX Do we really need this ? I really dont get why we need a safety margin: at the point in time where the dapp request noise.getNextRandomness() then at this point, regardless of how th request is handled, the randomness is gonna come from a future round, so the app has nothing to worry about.
What I am missing ?


XXX Simon, Rewrite
-->

The application needs to commit to a round number before the beacon is revealed.
Fortunately we have a reliable [BFT Time](https://docs.tendermint.com/master/spec/consensus/bft-time.html) but this is not perfectly accurate and can be behind. In case the contract thinks `publish_time` is not yet reached while the beacon is already published, an attacker can abuse the knowledge of the randomness. So we introduce a duration `safety_margin` and require `publish_time` to be at least `safety_margin` after the current BFT time (`block_time`).

Using the formula from above, we want

```
(1) publish_time := genesis + (round - 1) * 30
(2) publish_time >= block_time + safety_margin

genesis + (round - 1) * 30 >= block_time + safety_margin
(round - 1) * 30 >= block_time + safety_margin - genesis
round - 1 >= (block_time + safety_margin - genesis) / 30
round >= ((block_time + safety_margin - genesis) / 30) + 1
```

This inequality is satisfied when doing

```
round := ceil((block_time + safety_margin - genesis) / 30) + 1
```

Assuming `safety_margin` is set generously to 2 seconds, the `round` calculated that way is 2-32 seconds in the future.

This calculation can be generalized if an end time should be set in advance instead of closing right away. With flexible end times, the publishing times of beacons can be matched, and only the safety margin need to be considered.

## Short Block Times

The Noir network can consider reducing block times from the typical 5-7 seconds in Ethereum to something shorter. Doing so has to be carefully tested in environments with many globally distributed validators. Fortunately, there has been teams successfully testing 1s block times and thus we believe it's a viable path forward ([1](https://twitter.com/fekunze/status/1542490680446050304), [2](https://twitter.com/crypto25807202/status/1551197364529967104), [3](https://docs.seinetwork.io/introduction/overview)).

## Process all drand rounds

The user experience is determined by the amount of time that elapses between the release of a random beacon and its availability on chain. Depending on the product's design, the user may be waiting for the outcome while looking at a spinner at this point. Fast bots that are well-connected to Dragon nodes via a variety of transports (HTTP, pubsub, gRPC) and send a transaction to the Noir chain containing the beacon right away when they first see it are essential for a nice user experience.

Time would be lost if the bots had to scan the Noir chain or even different customer chains to see if a round was requested. There may already be a beacon request in a mempool, but it has not yet been allocated to a block. A block may contain it, but the events within the block are not yet classified.

We save communication costs and hasten round processing by processing every second round on the Noir chain. Additionally, we prepare the chain for deep authentication while assuring it won't affect performance or storage.
