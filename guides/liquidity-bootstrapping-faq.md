# Liquidity Bootstrapping FAQ

### How do I launch a Liquidity Bootstrapping Pool?

Decide on critical parameters, such as sale duration, starting and ending weights, and estimate the demand \(i.e., expected sale rate\), using the [LBP simulator](https://docs.google.com/spreadsheets/d/1naQDt7BFCNtfdd2wUYm6Z3ENgZEI-5sdUud3eOkHYnQ/edit?usp=sharing) to adjust the settings until you are happy with the resulting price curve. \(Best practice is to copy/download it, then customize to your own use case.\)

Post on [\#token-requests](https://discord.gg/VbDahbKd2e) to request eligibility for governance token rewards. \(At least a week’s advance notice is recommended.\)

If your pool is eligible for weekly YOGI rewards, they will be distributed to your LPs automatically. However, to receive YOGI rewards yourself, you must inform the Yogi team and share a EOA wallet for you to receive the distribtion. 

Here's the general process to deploy a Liquidity Bootstrapping Smart Pool, conduct the token sale, and recover the proceeds:

* When you've decided on parameters as described above, [create the smart pool](../../guides/creating-a-smart-pool.md).
* Call updateWeightsGradually to start the sale. \(Note that unless you pause the pool, it will be visible to the exchange and available for trading immediately, though the weights are fixed until you start the update.\)
* \(Optionally\) call pokeWeights periodically to cause the weights to change on schedule. This is a public function that anyone can call - including from the public Pool Management interface. You can advertise this, and encourage buyers to poke for you \(it can be fairly expensive\). Large buyers would do well to pokeWeights before buying.
* At the end of the sale, Remove Liquidity to recover the proceeds; i.e., the reserve balance, plus any unsold tokens. If you reserved the add/remove tokens right \(and disallowed public LPs\), you can simply call removeToken. Otherwise, you can remove liquidity directly - though you need to leave at least some dust behind, since there is a minimum balance of each token.

### How long should an LBP last?

This is a fully customizable parameter that is up to you, based on your objectives. However we can provide some general tips. It’s important to give your potential investors sufficient time to participate and for healthy price discovery to occur. We’ve seen this process work well over a span of 3 days, but as LBPs are a new innovation, we believe there are other lengths of time that could work as well or even better. 

Considering the fast pace and unpredictability of events happening regularly in DeFi, making your LBP too short could allow obstacles that are unrelated to your project \(i.e., a spike in BSC network congestion or a new yield farming craze\) to get in the way of a successful sale. We would recommend at least 3 days, but you are certainly free to make it shorter if you believe it would better serve your particular case.

#### _Note that the default minimum duration is approximately 2 weeks \(and 2 hours for the add token time lock\); using a shorter period will require overriding that default when you create the pool. This mainly applies to those writing scripts; if you use the smart pool GUI, the defaults are very short \(both 10 blocks\)._

### How should I choose a starting price?

You can think of the starting price of your LBP as the ceiling you’d want to set for the token sale. This may seem counterintuitive, but since LBPs work differently than other token sales, your starting price should be set much higher than what you believe is the fair price. 

This does not mean you’re trying to sell the token above what it’s worth. Setting a high starting price allows the changing pool weights of your LBP to make their full impact, lowering the price progressively until a market equilibrium is reached. Unlike older token sale models such as bonding curves, LBP investors are disincentivized to buy early, and instead benefit from waiting for the price to decrease until it reaches a level they believe is fair.

For example, if you believe the fair price for your token is $2, you may want to consider an opening price that is up to $10.

### What are the recommended starting and ending weight configurations for an LBP?

The general idea is to start with the pool weights skewed towards your token and end with the pool weights skewed towards the reserve asset\(s\). This configuration is designed to make it so that the majority of your tokens end up being exchanged for the reserve asset\(s\) you have chosen.

Be sure to think all the way through your intended sale, since there are some technical subtleties. For instance, if your weights are very close to the min/max weight boundaries, `pokeWeights` could fail in some edge cases where the total would temporarily be exceeded. Unless you need the maximum 98%/2% \(49/1 denorm\) weights, it's best to use weights that sum to a lower total number \(e.g., 38/2\), and put the project token first in the list when you create the pool, so that the weight _decrease_ would be processed first.

### How can I use an LBP to conduct a token sale with minimal seed capital?

The lower you set the weight\(s\) of the reserve asset\(s\) in your LBP, the less upfront capital you would need to seed your LBP. In a two-token LBP, the most skewed ratio you can set is 2:98, meaning that the pool composition will be 2% your reserve asset, and 98% the project token. 

However, since the value of your project’s tokens is proportional to the value of the reserve assets you provide for the sale, the amount of funds you can raise in the sale is limited by the total value of the reserve assets you provide to the LBP.

For example, with an LBP weight ratio of 2:98 \(reserve asset:project token\), if your upfront capital is 50K USDC, and your starting price is 10 USDC, the amount of tokens you can sell is \[\(50K / 0.02\) / 10\] = approx. 250K tokens.

In contrast, if your upfront capital is 1M USDC, you’d be able to sell around 5M tokens.

### How can I calculate different scenarios for the amount of tokens to sell, based on the amount of seed capital and pool weights?

You can use our [LBP simulator](https://docs.google.com/spreadsheets/d/1naQDt7BFCNtfdd2wUYm6Z3ENgZEI-5sdUud3eOkHYnQ/edit?usp=sharing) to plug in your variables and see the projected results. \(Best practice is to copy it so you have your own version, or even download to Excel.\)

There's a lot going on there, but a good place to start is the "ad hoc" simulator at the top right. There, you can type in balances, weights, and the swap fee, and see what the initial price would be. Then you can type your starting values into the main interface on the top left, and experiment with different ending weights and sale rates to come up with a reasonable price curve. It will also display the total proceeds and leftover tokens.

You'll see right away that it is very sensitive to the sale rate. The price is derived from two values - balances and weights. And you only control one of them! Your weight "flipping" schedule determines how the weights will change over time, but \(unless you intervene with further issuance or buybacks\), balances only change through trades: mostly people buying your token with the reserve currency.

If people buy while the weights are constant - or someone places a huge order - the token price may go up sharply. This is how a token sale can get "stuck" on a platform like Uniswap, where the prices are set by balances only. This may be optimal for retail liquidity, but is much less so for a token sale.

In an LBP, buyers suffering sticker shock need only wait for the weights to adjust and the price to start dropping. \(If they are impatient, they can call the public `pokeWeights` function themselves to tell the contract to update weights to the proper values at the current block.\)

In practice, we have seen that the market is efficient. Traders do indeed keep the price within a fairly narrow band around the discovered market value. That is one reason we believe this is the fairest token distribution method yet discovered. And as a bonus, this price curve tends to maximize total proceeds of the sale.

Note that it may not always be possible to sell all available tokens in the desired price range with the capital available. In this case, you could either split up the sale into multiple tranches \(either re-using the same pool, or making new pools, using the proceeds of each to fund the next\), or alter the supply or weight function during the sale in various ways, as described below.

### How can I influence the token price while running an LBP?

Unless you deposit tokens yourself during the sale \(e.g, by adding additional liquidity through joinPool\), the only way to affect prices during an LBP is by setting the target pool weights. Changing the target pool weights influences price, such that increasing the weight of an asset increases its relative price, and vice versa. The change in price gradually incentivizes investors to buy your token in exchange for the reserve asset\(s\), which then changes the balances of assets in the pool.

### How do I change the weights of my LBP while it’s live?

Use the `updateWeightsGradually` function to put the contract into a state where it will respond to the `pokeWeights` call by setting all the weights according to the point on the "weight curve" corresponding to the current block. `pokeWeights` can be called by you, or anyone, to update the weights according to your LBP’s configuration.

You can also call `updateWeight` \(as long as a gradual update is not running\) to set weights arbitrarily - but this will not affect the price. It will transfer tokens as required such that the new balances and weights maintain current prices.

### What are the reserve assets I can sell my token for?

You can sell your token for any ERC20 tokens. You can choose up to 7 other tokens to be used as reserve assets in your LBP token sale. Projects typically sell their tokens for highly liquid stablecoins such as DAI, USDC, or USDT; and/or for WETH.

### How do I get my token listed by name and logo on the Yogi exchange UI?

The Yogi team regularly monitors the crypto landscape and adds new tokens to our listings based on internal requirements. Tokens do not need to request to be listed on the exchange, as it is done proactively.

If you are launching an LBP and want to make sure that your token is listed on the exchange before launch, please [contact the team](mailto:yogi.fi@protonmail.com) for assistance.

### How do I get my token whitelisted for YOGI mining rewards?

[This page](../protocol/yogi-liquidity-mining/exchange-and-reward-listing.md) describes the process.

### After providing the initial seed capital needed to launch an LBP, do I need to deposit additional capital later?

No, you do not need any additional capital beyond the initial seed amount based on the starting weights you’ve selected for your pool. However, you do have the optional ability to deposit new capital into the pool as a buyback mechanism while the LBP is running.

