# Developing

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


