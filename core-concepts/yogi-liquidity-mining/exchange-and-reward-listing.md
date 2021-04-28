# Exchange and YOGI Mining Listing

Token listings are managed in [this repository](https://github.com/yogi-fi/yogi-assets), with the following categories:

* `eligible.json`: assets eligible for YOGI mining as per weekly proposals
* `listed.json`: assets listed on exchange.yogi.fi
* `ui-not-eligible.json`: assets vetted by community members
* `untrusted.json`: assets that are incompatible with Yogi

Note that you can always create a pool or trade with an "unlisted" token, using the token address. You just can't search by token name, and the UIs will display a warning when interacting with "custom" tokens. \(Needless to say, do your own research when using such tokens!\)

There are two kinds of token "listings" on Yogi. The first is listing on the [Exchange](https://exchange.yogi.fi/#/swap). Listed tokens appear on the main page as swapping options. It is possible to access unlisted pairs through a "deep link" with their addresses \(yogi.fi/\#/swap/&lt;address1&gt;/&lt;address2&gt;\), but it displays unlisted tokens as addresses, and with a warning.

There is no formal process for listing tokens on the Yogi exchange UI. That is up to the team's discretion and relies on internal factors around trading volume, usage, and legitimacy. \(As noted above, it's always possible to trade tokens via contract address.\)

The second kind of listing is eligibility for YOGI earnings. New tokens are listed \(and occasionally removed\) through a weekly governance process There is a streamlined process for simply listing a new token - tokens that meet a set of technical criteria below can be approved without requiring community vote. Only increasing the cap, introducing "controversial" or non-conforming tokens, or adjusting the earnings process or its parameters requires a vote.

To request approving your token for YOGI earnings, just post to \#token-requests on the [Discord](https://discord.gg/VbDahbKd2e) channel. Each weekly earnings period begins at 00:00 UTC on Monday; any requests approved after then will take effect the following week.

The listing criteria includes:

1. The token’s smart contract must be verified on [BscScan](https://bscscan.com/). Neglecting this small degree of transparency adds unnecessary friction to the process of vetting for the remaining criteria.
2. The token must conform to the BEP-20 interface described [here](https://github.com/binance-chain/BEPs/blob/master/BEP20.md). Namely, the functions `transfer()`, `transferFrom()`, and `approve()` must return booleans.
3. The token’s `transfer()` and `transferFrom()` implementations must exhibit the expected behavior - namely, transferring N tokens from one address to another. Certain divergences from this behavior, such as transfer fees, can cause issues with Yogi pools, and these tokens will be rejected.
4. The token’s `approve()` implementation must exhibit the expected behavior - namely, it must allow for infinite approval, or the token cannot be sold using the Yogi exchange proxy.
5. The token must not be vulnerable to the so-called "`gulp()` attack," which is exposed when a pool’s token balance changes unbeknownst to the pool. For now, known cases include tokens that charge a transfer fee \(as described in \#3\) and tokens that periodically rebase. A rebasing token utilizing an atomic rebase+gulp should be safe from such attacks, but none has yet been discovered.
6. The token must not possess any mechanisms which, when combined with a Yogi pool, result in material losses to the principal value of the pooled token. This criterion covers all value-loss edge cases which may be difficult to anticipate but can still preclude a token’s whitelisting. Examples will be added as they are discovered; the only currently known example is [VBZRX](https://etherscan.io/address/0xB72B31907C1C95F3650b64b2469e08EdACeE5e8F), whose `claim()` mechanism entitles token holders to receive BZRX airdrops on each token transfer. In isolation, this mechanism is perfectly safe, but if a token is airdropped to a Yogi pool whose asset list does not contain said token, then the airdropped tokens are forever locked in the pool contract and cannot be recovered by anyone \(including Yogi Studio\).


