# Development & Examples

Documentation for working with the `yogi-sor` package. For a description of the SOR and math, see this [page](https://github.com/yogi-fi/yogi-docs/tree/cc5cc586ce3506cdd8ecb27b34cc15dd24cebe44/smart-contracts/protocol/sor.md#overview).

{% hint style="danger" %}
Please take caution as the SOR is under heavy development and may have breaking changes.
{% endhint %}

The SOR package includes a primary `SOR` object with an `SOR.getSwaps` function and several helper functions for retireving Yogi pool data.

## SOR Object

When instantiating a new SOR object we must pass five parameters to the constructor:

`const SOR = new sor.SOR(Provider: JsonRpcProvider, GasPrice: BigNumber, MaxPools: number, ChainId: number, PoolsUrl: string)`

Where:

* Provider is a BSC network provider.
* GasPrice is used by the SOR as a factor to determine how many pools to swap against. i.e. higher cost means more costly to trade against lots of different pools. This value can be changed.
* MaxPools is the max number of pools to split the trade across. Limit to a reasonable number given gas costs.
* ChainId is the network chain ID \(i.e. 56=mainnet, 97=testnet\)
* PoolsUrl is a URL used to retrieve a JSON list of Yogi Pools to be considered. 
  * Mainnet: [https://thegraph.com/explorer/subgraph/yogi-fi/yogi-subgraph](https://thegraph.com/explorer/subgraph/yogi-fi/yogi-subgraph)​

## Fetching Pool Data

The SOR requires an up to date list of pool data when calculating swap information and retrieves on-chain token balances for each pool. There are two available methods:

### `await SOR.fetchPools()` <a id="await-sor-fetchpools"></a>

This will fetch all pools \(using the URL in constructor\) and on-chain balances. Returns `true` on success or `false` if there has been an error.

### `await SOR.fetchFilteredPairPools(TokenIn, TokenOut)` <a id="await-sor-fetchfilteredpairpools-tokenin-tokenout"></a>

A subset of valid pools for token pair, TokenIn/TokenOut, is found and on-chain balances retrieved. Returns `true` on success or `false` if there has been an error. This can be a quicker alternative to using fetchPools but will need to be called for every token pair of interest.

## Processing Swaps <a id="processing-swaps"></a>

### `async SOR.getSwaps(...)` <a id="async-sor-getswaps"></a>

The `getSwaps` function will use the pool data and the trade parameters to perform an optimization for the best price execution. It returns swap information and the total that can then be used to execute the swaps on-chain.

```javascript
[swaps, total] = await SOR.getSwaps(
        tokenIn,
        tokenOut,
        swapType,
        swapAmount
    );
```

`tokenIn` - string: address of token in

`tokenOut` - string: address of token out

`swapType` - string: either `swapExactIn` or `swapExactOut`

`swapAmount` - BigNumber: amount to be traded, in Wei

### `async SOR.setCostOutputToken(tokenOut)` <a id="async-sor-setcostoutputtoken-tokenout"></a>

The cost of the output token in ETH multiplied by the gas cost to perform the swap. This is used to determine whether the lower price obtained through including an additional pool in the transaction outweigh the gas costs. This function can be called before getSwaps to retrieve and cache the cost which will then be used in any getSwap calls using that token. Defaults to 0 for a token if not previously set.

## Example - Using SOR To Get List Of Swaps

Below is an example snippet that uses the SOR to return a final list of swaps and the expected output. The `swaps` returned can then be passed on to the exchange proxy or otherwise used to atomically execute the trades.

```javascript
import { sor } from 'yogi-sor';
import { BigNumber } from 'bignumber.js';
import { JsonRpcProvider } from '@ethersproject/providers';

// MAINNET
const tokenIn = '0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c'; // WBNB
const tokenOut = '0xe9e7CEA3DedcA5984780Bafc599bD69ADd087D56'; // BUSD

(async function() {
    const provider = new JsonRpcProvider(`https://bsc-dataseed.binance.org/`);

    const poolsUrl = `https://thegraph.com/explorer/subgraph/yogi-fi/yogi-subgraph`;

    const gasPrice = new BigNumber('30000000000');

    const maxNoPools = 4;

    const SOR = new sor.SOR(provider, gasPrice, maxNoPools, poolsUrl);

    // isFetched will be true on success
    let isFetched = await SOR.fetchPools();

    await SOR.setCostOutputToken(tokenOut);

    const swapType = 'swapExactIn';

    const amountIn = new BigNumber('1000000000000000000');

    let [swaps, amountOut] = await SOR.getSwaps(
        tokenIn,
        tokenOut,
        swapType,
        amountIn
    );
    console.log(`Total Return: ${amountOut.toString()}`);
    console.log(`Swaps: `);
    console.log(swaps);
})()
```

