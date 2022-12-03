# Security

Noir Network provides an unbiasable publicly verifiable source of randomness to
dapps. We assume the following threat model:

## Security of the drand network

At the moment, the Noir Network relies on the
drand network. Drand relies on honest majority assumption, i.e. more than 50%
of the drand operators must be honest, and so far this assumption has held in
practice thanks to the variety in terms of nodes: different jurisdictions, platforms,
OS, and deployments. Drand has stood up through time as it is being used by multiple projects,
including the popular Filecoin blockchain for more than 2 years without a single [downtime](https://status.drand.love/).

## Security of the Noir Network

There is a list of validators in charge of managing the consensus because this chain is new and based on Ethereum. The reputation of the validators is ranked on Ethereum (highest uptime, number of chains, etc.). A trustworthy source of validators is already available on this list. The supermajority assumption, which states that 2/3 plus 1 of the total stake must be owned by sincere validators, is what underpins the consensus. In the future, we hope to increase the permissionlessness of our validator set while still including certain autonomous groups like the Drand network.

Given these two assumptions, the security of the model is pretty straightforward:

- The Noir Oracle contract guarantees the correct verification of all beacons
  submitted to it.
- The Noir chain guarantees the correct logic execution of the Noir Oracle
  contract, i.e. it will reply to IBC beacon requests with exactly the correct beacon,
  already validated.

## Application Security

Similar to conventional programmes, handling randomness is not an easy undertaking, and many security flaws have their roots in how randomness is handled. Dapps require a level of randomness that has not yet been seen or is not skewed. Take a lottery app as an example. If someone knows the randomness before the allotted period, they can easily cheat by inputting the correct ticket. A dapp should request the _next_ randomness being available when the lottery is finished. This is exactly the main API endpoint
the Noir contract is offering: `GetNextRandomness { .. }`.
This reduces the chance of using the randomness source insecurely compared to lower
level APIs.

For advanced use cases we also offer a `GetRandomnessAfter { .. }` request type,
which allows requesting future randomness (e.g. 24h after the request) and receive it
automatically once available. Using this securely is more challenging and this is only
recommended for apps that have special needs and are aware of the risks.
