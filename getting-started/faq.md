# FAQ

## Basics

### What is the Yogi Protocol?

Yogi is a protocol for multi-token [automated market-making](../core-concepts/protocol/background-1.md). It enables portfolio owners to create Yogi Pools, and traders to trade against them. Yogi Pools contain two or more tokens, each with an independent weight representing its proportion of the total pool value. The pools provide the Yogi Protocol with liquidity, and charge traders a fee for access to it. Pools can be considered automated market-makers, since anyone can swap any two tokens, in any pool.

### How is the Yogi Protocol useful?

There are two categories of users who can benefit from the Yogi Protocol: liquidity providers - who own Yogi Pools or participate in shared pools, and traders - who buy or sell the underlying pool assets on the open market.

Anyone with two or more ERC20 tokens can be a liquidity provider. For example:

* Portfolio managers, who want to have controlled exposure to different assets without complicated and expensive rebalancing 
* Investors who have ERC20 tokens sitting idly in a wallet, and would like to put them to work earning passive income from fees 

Traders can choose from a diverse set of pools, each presenting a unique set of investment opportunities and challenges through its particular configuration of tokens, weights, and fees. The interplay between these settings, pool volume, and external prices generates market forces which incentivize traders to maintain stable token ratios, thereby preserving asset value for liquidity providers.

There are three main categories:

* "Retail" traders seeking to exchange tokens with low slippage at favorable rates
* Arbitrageurs seeking profit through leveling market inefficiencies between DEXs or CEXs 
* BSC smart contracts seeking liquidity for a variety of reasons, such as liquidating positions on other protocols, trading on behalf of users, etc. 

### Is Yogi Protocol fully permissionless?

Yes. Yogi Pools cannot be censored or whitelisted. Traders cannot be censored or whitelisted. Yogi Studio does not have the power to halt or edit the smart contracts in any way after they’ve been deployed. The contracts are not upgradeable, and there is no admin functionality or "backdoor" present in the code.

Of course, Yogi has no control over the contracts of ERC20 tokens placed in Yogi pools. If a centralized token \(e.g., USDC\) were to blacklist an address or freeze all transfers, that would affect all USDC tokens everywhere, including those in Yogi Pools.

### What is the development roadmap?

We are working on putting together a more detailed roadmap. The of V1 Yogi went live on April 26th, 2021.

The next step is V2 - set to launch in Q3 2021. It will be a quantum leap forward! We've vastly simplified the interfaces, rewritten the UI from scratch, improved gas efficiency and performance, and added significant new functionality.

Yogi V2 is an open, flexible, generalized AMM launch platform, achieved primarily through decoupling token storage and accounting from pool price computation logic. This allows projects to create new kinds of pools optimized for particular tokens or use cases, without changing the core protocol.

V2 also supports Asset Managers - contracts that can remove tokens from the vault for yield farming, staking, voting, etc. - and flash loans.

At launch, we will have two kinds of pools - Weighted pools similar to V1 \(but with up to 16 tokens, vs 8 tokens on V1\), and Stable pools, which have price logic designed for stablecoins or other pegged assets.

### Does Yogi Protocol charge any fees at the protocol layer?

No. There is a placeholder for an`EXIT_FEE` in the code, but it is currently set to zero on V1. \(In fact, because it is zero, tokens that do not allow zero-value transfers cannot be held in Yogi pools.\)

V2 Yogi will have three kinds of protocol fees: a swap fee \(charged as a percentage of the pool swap fee, which can be zero\), a withdrawal fee \(charged only when removing tokens from the protocol entirely, not internal trades\), and a flash loan fee.

### Is there a Yogi Protocol token?

Not currently. A token will never be required to trade or interact with the protocol. Any protocol upgrade in that direction would be discussed with the community well in advance.

### Is there a Yogi Governance Token?

Yes, Yogi Governance Token, [YOGI](https://bscscan.com/address/0x88888C8783a88aD40d995073Ab7FBbe8d34aCdA8), can be used to vote on proposals and steer the direction of the protocol. Every week 384,000 YOGIs, or approximately 20M per year, are distributed to liquidity providers.

* 20M YOGI tokens were initially allocated to founders, all subject to vesting periods.
* 10M were allocated for the Yogi Ecosystem Fund. This fund will be deployed to attract and incentivize strategic partners that will help the Yogi ecosystem grow and thrive. YOGI holders will ultimately decide how this fund is used over the coming years.
* 10M were allocated for community contributors to help create and raise awareness of Yogi.
* 10M were allocated for the IDO of Yogi.
* The remaining 50M tokens are intended to be mostly distributed to liquidity providers in the coming years.

## Yogi Pools

### What is a Yogi Pool?

The fundamental building block of the Yogi Protocol is the Yogi Pool. Pools are smart contracts that implement the Yogi Protocol, and hold value in two or more ERC20 tokens.

You can think of a Yogi Pool as an automated, market-making portfolio. Each token asset has an independent weight, and can be traded against any other token in the pool. For example, you could have a pool with three tokens in the following proportions 50% WBNB, 25% BTC and 25% BUSD.

The value proposition of Yogi flows from two main features:

1. Even as the relative unit prices of the tokens vary, the pool as a whole is continuously rebalanced \(in an efficient market\) to maintain each token's proportion of the total value. 
2. Each trade that takes place in a Yogi Pool generates a fee for the pool owner. The fee is a percentage of the trading volume, and is customizable by the pool owner when the pool is created.

Thus the incentives of both participants are aligned. Liquidity providers earn trading fees, while the overall value of their portfolio is preserved through continuous rebalancing. Traders pay these fees for the opportunity to either swap tokens with low slippage, or profit from arbitrage opportunities between pools and the open market.

### Are there constraints for setting up a Yogi Pool?

Only a few. Yogi Protocol limits pools in the following ways:

* Number of tokens: pools must contain at least two, and may contain up to eight tokens on V1 \(16 on V2 Weighted pools\).
* Swap fee: the fee must be between 0.0001% and 10% 
* ERC20 compliance: pool tokens must be ERC20 compliant. Bronze does not support ERC20 tokens that do not return `bools` for `transfer` and `transferFrom`. V2 is a bit more flexible, but will not support some token types, such as tokens that change balances \(e.g., elastic supply tokens\).
* There are a few additional ratio and balance constraints that can be found at [Limitations](../core-concepts/protocol/limitations.md).

### How are Yogi Pools continuously rebalanced?

We believe this is the main innovation introduced by the Yogi Protocol. Pools are efficiently rebalanced through a multi-dimensional invariant function used to continuously define swap prices between any two tokens in a pool. Essentially, it is an n-dimensional generalization of Uniswap's x \* y = k formula.

Imagine a portfolio that contains a certain proportion of token A, and its external market price increases. In a conventional rebalancing process, the portfolio manager would need to take action - in this case, pay a trading fee to sell token A \(and probably pay another fee to buy something else to replace it\).

In contrast, Yogi Protocol lets rational market actors actively buy token A from the pool, presumably to sell elsewhere on the open market at a profit. The portfolio manager \(Yogi Pool creator in this context\) does nothing but collect the fees.

So instead of doing work and _paying_ fees to rebalance their portfolio, Yogi Pool creators _earn_ fees while traders do the rebalancing work for them. Conversely, traders benefit in two ways - high liquidity allows low slippage on "retail trades," and arbitrageurs can directly profit from swings in external market prices.

### How do Yogi Pools charge fees and how much are they?

Yogi pools charge a percentage of the input amount traded for each trade. The fee goes entirely to the Yogi Pool liquidity providers. In V2, there is a small additional protocol fee, charged as a percentage of the pool's swap fee percentage \(which can be zero\).

### What types of Yogi pools are there?

V1 Core Yogi Pools can be controlled or finalized. Essentially, finalized pools are "public" - their parameters are fixed, and anyone can add/remove liquidity and swap tokens. Controlled pools are "private" - their parameters are not fixed, but only the pool creator can add liquidity. See more details in [Core Concepts](../smart-contracts/smart-pools/concepts.md).

There are also various kinds of Smart Pools - Core Yogi Pools controlled by a smart contract. Yogi is designed to be easily extensible; the Core Concepts page referenced above has some suggestions for Smart Pool designs.

Some Smart Pool concepts have already been implemented, and many more are in development. The first implementation was actually [PieDAO](https://piedao.org/).

Since Yogi Pool tokens are also compliant ERC20 tokens, they can also be composed into "meta" pools.

The home-grown "Configurable Rights" Smart Pool is less trustless than a Core Yogi Pool, since Smart Pool creators can \(by definition\) alter the parameters of a "live" Yogi Pool that allows public LPs. To mitigate this, CRP creators can choose exactly which parameters can be changed, at create time. As the name implies, this is done by granting the Smart Pool contract one or more "Rights" - the right to change one of the parameters. \(A CRP with no rights would be equivalent to a Core Yogi Pool.\)

## Using Yogi Protocol

### How can I use Yogi as a trader?

There are two ways a trader can interact with Yogi Protocol:

* Through our [Exchange](https://exchange.yogi.fi/#/swap) front-end
* Directly through our smart contracts on BSC 

### How can I use Yogi as a liquidity provider or portfolio manager?

There are two main ways a liquidity provider or portfolio manager can interact with Yogi Protocol:

* Through our [Pool Manager](https://pools.yogi.fi/#/) front-end
* Directly through our smart contracts on BSC 

### How can I claim YOGI tokens as a Liquidity Provider?

$YOGI tokens will be directly distributed to Liquidity providers on a weekly basis. If you are providing liquidity to a pool with at least 2 whitelisted assets, you will receive $YOGI tokens periodically without any other interaction required.

### Can Yogi be used as an Index Fund?

Yogi Protocol is ideal for Index Funds, as it takes away all the hassle that managers have regarding rebalancing - and on top of that generates fees from trading.

## Miscellaneous

### Does using Yogi Protocol generate taxable events?

We cannot provide tax or accounting advice. Tax regulations are specific to jurisdiction where you or your company reside. For any legal or tax matters we recommend consulting your own attorney.

### Are there risks in using Yogi Protocol?

Yogi Protocol smart contracts have been designed with security as a top priority. The core protocol code has been reviewed by Consensys Diligence, and formally audited by both [Trail of Bits](https://github.com/balancer-labs/balancer-core/blob/master/Trail%20of%20Bits%20Full%20Audit.pdf) and [Open Zeppelin](https://blog.openzeppelin.com/balancer-contracts-audit/). \(Of course, we cannot guarantee that bugs won’t be found in the future.\)

Yogi Pools are not [upgradeable](https://medium.com/consensys-diligence/upgradeability-is-a-bug-dba0203152ce) \(though other third-party Smart Pool implementations might be\), and there are no admin keys or backdoors.

Remember that the tokens held in Yogi Pools are also smart contracts - not controlled by Yogi - and may have their own risks. Yogi does not support non-ERC20-conforming tokens, but pools may have been created that use them anyway.

The CRP contains safeguards to prevent categories of tokens with known issues from being used in pools \(e.g., non ERC20-conforming tokens, and tokens that disallow zero-transfers\), and further safeguards to allow other kinds of tokens to interact safely with the protocol \(e.g., tokens with callbacks, or those which require 0 prior spend approval\).

Since Smart Pool operators can, by definition, alter the parameters of the pool during active trading, all require some level of trust in the pool creators \(beyond the general smart contract risk\) - the more parameters they can change, the more trust is required.

As a liquidity provider, there is also the financial risk of [Impermanent Loss](https://medium.com/balancer-protocol/calculating-value-impermanent-loss-and-slippage-for-balancer-pools-4371a21f1a86).

