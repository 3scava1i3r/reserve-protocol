# Test Coverage

We aim to reach in this project 100% test coverage of `P1` (Production version) through our  *unit*, *scenario*, and *integration* tests. Because we designed our tests to run and be compatible with both versions (P0 and P1) we mainly use the `public` interface of each contract in our test cases, and we rely on mocks only when strictly required. 

While working on these tests, we were able to identify some checks and validations in our contracts that would produce always the same outcome and thus, could be removed if desired. However, we decided to leave them (though may be show as uncovered) to provide additional security and alert developers during future upgrades/modifications to the code.

Below we provide a detailed list of these checks to serve as a reference when running `coverage` for this project.

## Uncovered sections

### contracts/p1/StRSR.sol:StRSRP1

- `_burn()`:  Both require statements that check for `account != address(0)` and `accountBalance >= amount` will never be false. Within the `unstake()` function, right before calling `_burn()` we are validating the user has enough balance which covers these two validations, as the zero address will never have a positive balance. No other calls to `_burn` are included in the contract.

### contracts/p1/RToken.sol:RTokenP1
- `refundSpan()`: The revert stamement `revert("Bad refundSpan")` will never occur with the current version of the code, as this is an `internal` function and the parameters for `left` and `right` are always stored in the contract and maintained with valid values doing the entire lifecycle.

### contracts/p1/mixins/TradingLib.sol:TradingLibP1

- `nextTradePair()`: 
    * The check for `bal.lt(surplusAmt)` when calculating the *surplus* component, will never be *true*, because `surplusAmt` can be at most equal to `bal` in the current version. But for additional security the check is maintained to make sure we never revert if for some reason these occurs after implementing changes to how `surplusAmt` is calculated.
    
    * The check at the end for `rsrAvailable.gt(rsrAsset.minTradeSize())` will never be *false*, because if the RSR balances is below the `minTradeSize` will be discarded previously as part of the `basket.top` and `basket.bottom` calculations. But we will leave this as an extra safety check. Could be tested if the values of the `BasketRange` are manipulated in a way to force this situation but would not occur when using the contracts through its `public` interface.
            
### P0 contracts

There might be a slighly lower coverage for `P0` due to differences in the implementation which are not worth tackling with specific test cases, but we make sure it is also very close to full coverage, and that all relevant paths and logic statements are covered as well.

- `RTokenP0.vest()`: The final check `if (totalVested > 0)` will always be *true* because if there are no issuances to process it will return at the top of the function with the initial `endIf` validation. And if the issuances are not ready to vest they would revert with `Issuance not ready`. So at that point in the code there is always something that was processed and vested successfully.


      