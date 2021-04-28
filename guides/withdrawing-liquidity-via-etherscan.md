# Interact via BscScan

Our UI allows for any liquidity provider to withdraw liquidity by clicking on the button "Remove Liquidity" on the detailed pool view page. This article though focus on how to withdraw liquidity without our UI \(in case it is down or inaccessible\). To do that we'll be using bscscan.com.

If a pool is still private \(i.e. not finalized\), you can withdraw liquidity by simply unbinding each of its tokens:

![](https://lh5.googleusercontent.com/epkR6-l5a2awnjvRAjNtBkFqE2rjHe7gfZ5fo2BH0l1uj87IA3fN2n6mfhZEtIJ-VbrtHEhosjgo35k7sdEMhJhZSbyVz0AWeEoNCFn-YPG4fSRHtTznRaYSEGXQ_OM0KlsGOggo)

In case we’re dealing with a shared pool, things look a bit different. Once a pool is finalized, a BPT \(Yogi Pool Token\) associated with that pool is created with an initial supply of 100 units \(always with 18 decimals\). From that point on, ownership of the pool is tracked by BPT: holders are pro-rata owners of the pool’s liquidity.

WIP !