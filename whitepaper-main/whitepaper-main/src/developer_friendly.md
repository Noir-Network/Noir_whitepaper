# Developer Friendly

## Noise Proxy Contract

Noir is meant to be a layer on top of which dapps can tap into. Therefore, its usage must be as simple as possible and not less.
In this spirit, our proxy contract has a main entry point:

```
GetNextRandomness { .. }
```

This instantly determines the next random round and returns the corresponding unpredictability.The developer does not have to consider when or how to ask for volatility. We believe that by using onchain randomness, a single API request can accommodate the vast majority of use cases.However, the API provides more nuanced entry points that must be handled carefully for certain applications.
 

## Standard Library

On top of this API, we offer a toolbox that contains functions
to convert the randomness to values usable in the application, such as:

- `coinflip(seed)` that returns heads or tails
- `random_decimal(seed)` that returns a number between 0 and 1
- `int_in_range(seed, min, max)` that returns a random integer in the `[min,max(` range
- `shuffle(seed, list)` that shuffles a given list

The team has observed numerous unreliable implementations of these functions in the real world, so we think it's critical to include them in the Nois ecosystem to encourage dapp developers to build reliable and secure applications.
 
