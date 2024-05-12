# My findings

1. `whiteListLendingMarket` may set `lastRewardBlock` incorrectly if it is whitelisted, then blacklisted, then whitelisted again, e.g.
```
address lendingMarket = vm.addr(5201314);

// this set lastRewardBlock to current block
vm.prank(goverance);
ledger.whiteListLendingMarket(lendingMarket, true);

vm.warp(block.timestamp + 7 days);
ledger.whiteListLendingMarket(lendingMarket, false);

// this set lastRewardBlock to current block, which is incorrect?
vm.warp(block.timestamp + 7 days);
ledger.whiteListLendingMarket(lendingMarket, true);

Looking back, it makes sense to not generate any rewards for market being blacklisted

```

# Learnings
1. [H-01] When call external contract, is the parameter correct? similar learnings about calling external contract in 2023-02-kuma 
2. [H-02] Verify simple business logic
3. [M-01] [M-02] math precision issue


# Canto Invitational audit details

- Total Prize Pool: $16,425 
  - HM awards: $12,285 
  - Analysis awards: $683 
  - QA awards: $341 
  - Gas awards: $341 
  - Judge awards: $2,275 
  - Scout awards: $500 
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-01-canto-invitational/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts January 25, 2024 20:00 UTC
- Ends January 29,2024 20:00 UTC
- ❗️Awarding Note for Wardens, Judges, and Lookouts: If you want to claim your awards in $ worth of CANTO, you must follow the steps outlined in this [thread](https://discord.com/channels/810916927919620096/1199083429174718464/1199722579259310100); otherwise you'll be paid out in USDC.

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-01-canto/blob/main/4naly3er-report.md).

_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

Risks deemed acceptable:

- Everything related to governance / centralization abuse: We assume that governance is non-malicious.

# Overview

## Links

- **Previous audits:** <https://code4rena.com/audits/2023-08-verwa>
- **Documentation:** <https://code4rena.com/audits/2023-08-verwa> and below

# Scope

*See [scope.txt](https://github.com/code-423n4/2024-01-canto/blob/main/scope.txt)*

| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [src/LendingLedger.sol](https://github.com/code-423n4/2024-01-canto/blob/main/src/LendingLedger.sol) | 106 | Implements the bookkeeping for the rewards and is used for claiming. Moreover, provides data for third-party contracts that want to use this information for secondary rewards | [`@openzeppelin/*`](https://openzeppelin.com/contracts/) |

## Out of scope

All other contracts and interfaces, namely `src/GaugeController.sol`, `src/VotingEscrow.sol`, `interface/Turnstile.sol`, and all tests (`src/test/`).

# Additional Context

Since the previous audit, the `LendingLedger` logic was completely rewritten. We now use an approach that is very similar to [MasterChef / Synthetix](https://www.rareskills.io/post/staking-algorithm). The main motivation for doing that was to enable users to claim accrued rewards whenever they want (instead of only after a week / epoch has passed). Moreover, we also introduced the field `secRewardDebt`. The idea of this field is to enable any lending platforms that are integrated with Neofinance Coordinator to send their own rewards based on this value (or rather the difference of this value since the last time secondary rewards were sent) and their own emission schedule for the tokens.

The code will only be deployed to CANTO.

The only trusted role is the governance address. Only this address can set the rewards per block.

## Attack ideas (Where to look for bugs)

Miscalculations / significant rounding errors

## Main invariants

The total rewards that are sent for one block should never be higher than the rewards that were configured for this block.

## Scoping Details

```
- If you have a public code repo, please share it here:  
- How many contracts are in scope?:   1
- Total SLoC for these contracts?:  107
- How many external imports are there?: 4 
- How many separate interfaces and struct definitions are there for the contracts within scope?:  2
- Does most of your code generally use composition or inheritance?:   Composition
- How many external calls?:   1
- What is the overall line coverage percentage provided by your tests?: 94
- Is this an upgrade of an existing system?: True - LendingLedger of the already audited veRWA (https://code4rena.com/audits/2023-08-verwa) was rewritten. It now supports per-block claiming (vs. per-epoch previously) and we expose data in the contract that enables secondary rewards (i.e. for other systems to incentivize deposits with their own tokens)
- Check all that apply (e.g. timelock, NFT, AMM, ERC20, rollups, etc.): 
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?:   True
- Please describe required context:  The changes since the last audit only affect one contract and are isolated, but it can be helpful for context to look at the overall system, which was described in the previous audit (https://code4rena.com/audits/2023-08-verwa) 
- Does it use an oracle?:  No
- Describe any novel or unique curve logic or mathematical models your code uses: The staking logic is adapted from Sushi / Synthetix: https://www.rareskills.io/post/staking-algorithm
- Is this either a fork of or an alternate implementation of another project?:   True
- Does it use a side-chain?: 
- Describe any specific areas you would like addressed:
```

# Setup and test instructions

```bash
# Cloning with recurse
git clone --recurse https://github.com/code-423n4/2024-01-canto.git
# Going into the contest directory
cd 2024-01-canto
# Installing npm dependencies
npm install
# Installing forge dependencies in case --recurse was forgotten when cloning
forge install
# Compiling
forge build
# Testing
forge test
# Generating gas report
forge test --gas-report
# Running coverage with minimum-IR (Stack too deep otherwise)
forge coverage --ir-minimum
# Generating lcov report file (keep in mind that the result will be a bit off when displaying the result such as with the Coverage Gutters extension on VSCode due to --ir-minimum).
forge coverage --ir-minimum --report lcov
# Running slither (alternatively, see the provided "slither.txt" file)
slither .
```

## Miscellaneous

Canto contributors that were involved in the creation of Neofinance Coordinator and their family members are ineligible to participate in this audit.
