# Events

## Events

`LOG_CALL` is an anonymous event which uses the function signature as the event signature. It is fired by all stateful functions. The following applies to Yogi Core pools. Smart Pools have similar events and modifiers.

```text
event LOG_CALL(
    bytes4  indexed sig,
    address indexed caller,
    bytes           data
) anonymous;
```

`LOG_SWAP` is fired \(along with `LOG_CALL`\) for all [swap variants](core-contracts.md).

```text
event LOG_SWAP(
    address indexed caller,
    address indexed tokenIn,
    address indexed tokenOut,
    uint256         tokenAmountIn,
    uint256         tokenAmountOut
);
```

`LOG_JOIN` and `LOG_EXIT` are fired for each individual token join / exit

```text
event LOG_JOIN(
    address indexed caller,
    address indexed tokenIn,
    uint256         tokenAmountIn
);

event LOG_EXIT(
    address indexed caller,
    address indexed tokenOut,
    uint256         tokenAmountOut
);
```

## Mutex

All stateful functions use either a `lock` or `viewlock` function modifier. A mutex places a lock on contract state and prevents any sort of re-entrancy.

```text
modifier _lock_() {
    require(!_mutex, "ERR_REENTRY");
    _mutex = true;
    _;
    _mutex = false;
}

modifier _viewlock_() {
    require(!_mutex, "ERR_REENTRY");
    _;
}
```

