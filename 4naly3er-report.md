**Table of Contents**

- [Report](#report)
  - [Gas Optimizations](#gas-optimizations)
    - [\[GAS-1\] Using bools for storage incurs overhead](#gas-1-using-bools-for-storage-incurs-overhead)
    - [\[GAS-2\] For Operations that will not overflow, you could use unchecked](#gas-2-for-operations-that-will-not-overflow-you-could-use-unchecked)
    - [\[GAS-3\] Use Custom Errors](#gas-3-use-custom-errors)
    - [\[GAS-4\] Functions guaranteed to revert when called by normal users can be marked `payable`](#gas-4-functions-guaranteed-to-revert-when-called-by-normal-users-can-be-marked-payable)
    - [\[GAS-5\] Using `private` rather than `public` for constants, saves gas](#gas-5-using-private-rather-than-public-for-constants-saves-gas)
    - [\[GAS-6\] Splitting require() statements that use \&\& saves gas](#gas-6-splitting-require-statements-that-use--saves-gas)
    - [\[GAS-7\] Use != 0 instead of \> 0 for unsigned integer comparison](#gas-7-use--0-instead-of--0-for-unsigned-integer-comparison)
  - [Non Critical Issues](#non-critical-issues)
    - [\[NC-1\] Missing checks for `address(0)` when assigning values to address state variables](#nc-1-missing-checks-for-address0-when-assigning-values-to-address-state-variables)
    - [\[NC-2\]  `require()` / `revert()` statements should have descriptive reason strings](#nc-2--requirerevertstatements-should-have-descriptive-reason-strings)
    - [\[NC-3\] TODO Left in the code](#nc-3-todo-left-in-the-code)
  - [Low Issues](#low-issues)
    - [\[L-1\] Empty Function Body - Consider commenting why](#l-1-empty-function-body---consider-commenting-why)

# Report

## Gas Optimizations

| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 1 |
| [GAS-2](#GAS-2) | For Operations that will not overflow, you could use unchecked | 73 |
| [GAS-3](#GAS-3) | Use Custom Errors | 5 |
| [GAS-4](#GAS-4) | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 |
| [GAS-5](#GAS-5) | Using `private` rather than `public` for constants, saves gas | 1 |
| [GAS-6](#GAS-6) | Splitting require() statements that use && saves gas | 1 |
| [GAS-7](#GAS-7) | Use != 0 instead of > 0 for unsigned integer comparison | 2 |

### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

16:     mapping(address => bool) public lendingMarketWhitelist;

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-2"></a>[GAS-2] For Operations that will not overflow, you could use unchecked

*Instances (73)*:

```solidity
File: src/LendingLedger.sol

4: import {VotingEscrow} from "./VotingEscrow.sol";

5: import {GaugeController} from "./GaugeController.sol";

6: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

6: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

6: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

6: import {Math} from "@openzeppelin/contracts/utils/math/Math.sol";

7: import {IERC20, SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {IERC20, SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {IERC20, SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {IERC20, SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

7: import {IERC20, SafeERC20} from "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

11:     uint256 public constant BLOCK_EPOCH = 100_000; // 100000 blocks, roughly 1 week

11:     uint256 public constant BLOCK_EPOCH = 100_000; // 100000 blocks, roughly 1 week

20:         uint256 amount; // Amount of cNOTE that the user has provided.

20:         uint256 amount; // Amount of cNOTE that the user has provided.

21:         int256 rewardDebt; // Amount of CANTO entitled to the user.

21:         int256 rewardDebt; // Amount of CANTO entitled to the user.

22:         int256 secRewardDebt; // Amount of secondary rewards entitled to the user.

22:         int256 secRewardDebt; // Amount of secondary rewards entitled to the user.

32:     mapping(address => mapping(address => UserInfo)) public userInfo; // Info of each user for the different lending markets

32:     mapping(address => mapping(address => UserInfo)) public userInfo; // Info of each user for the different lending markets

33:     mapping(address => MarketInfo) public marketInfo; // Info of each lending market

33:     mapping(address => MarketInfo) public marketInfo; // Info of each lending market

35:     mapping(uint256 => uint256) public cantoPerBlock; // CANTO per block for each epoch

35:     mapping(uint256 => uint256) public cantoPerBlock; // CANTO per block for each epoch

38:     mapping(address => uint256) public lendingMarketTotalBalance; // Total balance locked within the market

38:     mapping(address => uint256) public lendingMarketTotalBalance; // Total balance locked within the market

64:                     uint256 epoch = (i / BLOCK_EPOCH) * BLOCK_EPOCH; // Rewards and voting weights are aligned on a weekly basis

64:                     uint256 epoch = (i / BLOCK_EPOCH) * BLOCK_EPOCH; // Rewards and voting weights are aligned on a weekly basis

64:                     uint256 epoch = (i / BLOCK_EPOCH) * BLOCK_EPOCH; // Rewards and voting weights are aligned on a weekly basis

64:                     uint256 epoch = (i / BLOCK_EPOCH) * BLOCK_EPOCH; // Rewards and voting weights are aligned on a weekly basis

65:                     uint256 nextEpoch = i + BLOCK_EPOCH;

66:                     uint256 blockDelta = Math.min(nextEpoch, block.number) - i;

67:                     uint256 cantoReward = (blockDelta *

68:                         cantoPerBlock[epoch] *

69:                         gaugeController.gauge_relative_weight_write(_market, epoch)) / 1e18;

70:                     market.accCantoPerShare += uint128((cantoReward * 1e18) / marketSupply);

70:                     market.accCantoPerShare += uint128((cantoReward * 1e18) / marketSupply);

70:                     market.accCantoPerShare += uint128((cantoReward * 1e18) / marketSupply);

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

72:                     i += blockDelta;

84:         update_market(lendingMarket); // Checks if the market is whitelisted

84:         update_market(lendingMarket); // Checks if the market is whitelisted

89:             user.amount += uint256(_delta);

90:             user.rewardDebt += int256((uint256(_delta) * market.accCantoPerShare) / 1e18);

90:             user.rewardDebt += int256((uint256(_delta) * market.accCantoPerShare) / 1e18);

90:             user.rewardDebt += int256((uint256(_delta) * market.accCantoPerShare) / 1e18);

91:             user.secRewardDebt += int256((uint256(_delta) * market.secRewardsPerShare) / 1e18);

91:             user.secRewardDebt += int256((uint256(_delta) * market.secRewardsPerShare) / 1e18);

91:             user.secRewardDebt += int256((uint256(_delta) * market.secRewardsPerShare) / 1e18);

93:             user.amount -= uint256(-_delta);

93:             user.amount -= uint256(-_delta);

94:             user.rewardDebt -= int256((uint256(-_delta) * market.accCantoPerShare) / 1e18);

94:             user.rewardDebt -= int256((uint256(-_delta) * market.accCantoPerShare) / 1e18);

94:             user.rewardDebt -= int256((uint256(-_delta) * market.accCantoPerShare) / 1e18);

94:             user.rewardDebt -= int256((uint256(-_delta) * market.accCantoPerShare) / 1e18);

95:             user.secRewardDebt -= int256((uint256(-_delta) * market.secRewardsPerShare) / 1e18);

95:             user.secRewardDebt -= int256((uint256(-_delta) * market.secRewardsPerShare) / 1e18);

95:             user.secRewardDebt -= int256((uint256(-_delta) * market.secRewardsPerShare) / 1e18);

95:             user.secRewardDebt -= int256((uint256(-_delta) * market.secRewardsPerShare) / 1e18);

97:         int256 updatedMarketBalance = int256(lendingMarketTotalBalance[lendingMarket]) + _delta;

98:         require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

98:         require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

105:         update_market(_market); // Checks if the market is whitelisted

105:         update_market(_market); // Checks if the market is whitelisted

108:         int256 accumulatedCanto = int256((uint256(user.amount) * market.accCantoPerShare) / 1e18);

108:         int256 accumulatedCanto = int256((uint256(user.amount) * market.accCantoPerShare) / 1e18);

109:         int256 cantoToSend = accumulatedCanto - user.rewardDebt;

129:         for (uint256 i = _fromEpoch; i <= _toEpoch; i += BLOCK_EPOCH) {

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-3"></a>[GAS-3] Use Custom Errors

[Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)
Instead of using error strings, to reduce deployment and runtime cost, you should use Custom Errors. This would save both deployment and runtime cost.

*Instances (5)*:

```solidity
File: src/LendingLedger.sol

57:         require(lendingMarketWhitelist[_market], "Market not whitelisted");

98:         require(updatedMarketBalance >= 0, "Market balance underflow"); // Sanity check performed here, but the market should ensure that this never happens

115:             require(success, "Failed to send CANTO");

128:         require(_fromEpoch % BLOCK_EPOCH == 0 && _toEpoch % BLOCK_EPOCH == 0, "Invalid block number");

138:         require(lendingMarketWhitelist[_market] != _isWhiteListed, "No change");

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-4"></a>[GAS-4] Functions guaranteed to revert when called by normal users can be marked `payable`

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (2)*:

```solidity
File: src/LendingLedger.sol

52:     function setGovernance(address _governance) external onlyGovernance {

137:     function whiteListLendingMarket(address _market, bool _isWhiteListed) external onlyGovernance {

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-5"></a>[GAS-5] Using `private` rather than `public` for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

11:     uint256 public constant BLOCK_EPOCH = 100_000; // 100000 blocks, roughly 1 week

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-6"></a>[GAS-6] Splitting require() statements that use && saves gas

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

128:         require(_fromEpoch % BLOCK_EPOCH == 0 && _toEpoch % BLOCK_EPOCH == 0, "Invalid block number");

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="GAS-7"></a>[GAS-7] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (2)*:

```solidity
File: src/LendingLedger.sol

61:             if (marketSupply > 0) {

113:         if (cantoToSend > 0) {

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

## Non Critical Issues

| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Missing checks for `address(0)` when assigning values to address state variables | 2 |
| [NC-2](#NC-2) |  `require()` / `revert()` statements should have descriptive reason strings | 1 |
| [NC-3](#NC-3) | TODO Left in the code | 1 |

### <a name="NC-1"></a>[NC-1] Missing checks for `address(0)` when assigning values to address state variables

*Instances (2)*:

```solidity
File: src/LendingLedger.sol

47:         governance = _governance;

53:         governance = _governance;

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="NC-2"></a>[NC-2]  `require()` / `revert()` statements should have descriptive reason strings

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

41:         require(msg.sender == governance);

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

### <a name="NC-3"></a>[NC-3] TODO Left in the code

TODOs may signal that a feature is missing or not ready for audit, consider resolving the issue and removing the TODO comment

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

71:                     market.secRewardsPerShare += uint128((blockDelta * 1e18) / marketSupply); // TODO: Scaling

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)

## Low Issues

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Empty Function Body - Consider commenting why | 1 |

### <a name="L-1"></a>[L-1] Empty Function Body - Consider commenting why

*Instances (1)*:

```solidity
File: src/LendingLedger.sol

145:     receive() external payable {}

```

[Link to code](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol)
