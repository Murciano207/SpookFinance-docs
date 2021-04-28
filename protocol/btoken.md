# YPTs

All pools in Balancer are also ERC20 tokens known as YPTs \(Yogi Pool Tokens\), which represent proportional ownership in the pool's liquidity. When users add liquidity through `joinPool` or `joinswap*` they receive YPTs proportional to the amount of assets they are adding to the pool.

## Notes

YPTs are an opinionated ERC20 token implementation, and have a few subtle differences. `transferFrom` from itself does not require a previous allowance.

