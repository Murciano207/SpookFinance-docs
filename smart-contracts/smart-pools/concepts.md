# Overview

## Smart Contract Owned Controlled Pools \("Smart Pools"\)

One very powerful feature of Yogi is the concept of Smart Pools. A smart contract controlled pool can fully emulate a finalized pool, while also allowing complex logic to readjust balances, weights, and fees. Some examples include:

* [An interest bearing stablecoin pool without impermanent loss](https://medium.com/balancer-protocol/zero-impermanent-loss-stablecoin-pool-with-lending-interests-a3da6d8bb782)
* A pool that adjusts swap fees as a function of the volatility of the pool's assets
* A pool that updates weights to implement a particular market strategy \(e.g., a [Liquidity Bootstrapping Pool](https://balancer.finance/2020/03/04/building-liquidity-into-token-distribution/)\).
* More complex [dynamic strategies](https://caia.org/sites/default/files/dynamic_strategies_for_asset_allocation.pdf) for asset allocation

For clarity, here is a graphical representation of the process for creating both Core Pools and Smart Pools - it can be a little confusing keeping all the addresses straight!

![](https://github.com/yogi-fi/yogi-docs/tree/cc5cc586ce3506cdd8ecb27b34cc15dd24cebe44/smart-contracts/.gitbook/assets/deployment.jpg)

In a nutshell, Yogi has deployed Factory contracts for creating pools. Users deploy new pool contracts by calling create methods on these factories. In both cases, the actual "pool" visible to traders on the public interfaces is a new BPool contract.

If you deploy a Core Pool directly, you are the controller of that pool. If you deploy a Smart Pool, you need to pass in the core BFactory address, since two contracts will be deployed. You are the controller of the Smart Pool - and the Smart Pool itself is the controller of the BPool.

### Conceptual Capabilities

There are three categories of things you can do with pools.

* **Trade** - using the swap functions
* **Provide liquidity** - using the join/exit pool functions \(both single- and multi-asset entry and exit are supported\)
* **Manage the pool** - if you are the controller \(e.g., change its parameters\)

Core Pools can only be managed until "finalized," after which their parameters are immutable.

Smart Pools are managed according to the rights granted to the controller on creation. For instance, you can create a Smart Pool where the weights can be changed, but the swap fee and token composition are fixed.

* `SWAP`  \(`swap_*`, `joinswap_*`, `exitswap_*`\)
* `JOIN` \(`joinPool`, `joinswap_*`\)
* `EXIT` \(`exitPool`, `exitswap_*`\)
* `CONTROL` \(`bind`, `unbind`, `rebind`, `setSwapFee`, `finalize`\)

Notice that e.g. `joinswap` requires both `JOIN` and `SWAP`.

