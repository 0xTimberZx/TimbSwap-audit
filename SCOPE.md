# Audit scope: contracts that hold, move or pay out other people's funds

Base commit: `df37e3f` (the beta set). This branch removes everything that is not needed to read or test the scoped contracts: the website, keepers, other deploy scripts, farms, governance, vesting and the token layer.

Solidity 0.8.24, Foundry, OpenZeppelin v5. Build with `forge build`; run the tests with `forge test`.

## In scope (nSLOC counted as non-blank, non-comment lines)

| Contract | Why it is in scope | nSLOC |
|---|---|---|
| `GameRegistry` | Holds ticket principal (ETH, and TIMBS when enabled); refunds, lapse and forfeit paths | 886 |
| `PrizeEscrow` | Holds the prize pot; pays winners and the protocol cut | 87 |
| `TimbPrize` | Settlement and winner payout: claims, protocol cut, harvest into the pot | 593 |
| `TimbSwapPair` | Holds pool liquidity; swap, mint and burn accounting | 235 |
| `TimbSwapRouter` | Users approve it and send it funds; swaps, liquidity, fee and nudge logic | 772 |
| `TimbSwapFactory` | Creates the pairs (CREATE2) and sets fee recipients | 119 |
| `scripts/PotCarry.sol` | Moves an unpaid pot between game generations | 113 |
| `scripts/FreshEntropy.sol` | Re-points a prize to a new entropy source in a migration | 11 |

Total in scope: about 2,816 nSLOC. The game contracts alone (`GameRegistry`, `PrizeEscrow`, `TimbPrize`) are about 1,566.

Suggested tiers if a smaller first pass is wanted:
1. Payout and escrow: `GameRegistry`, `PrizeEscrow`, `TimbPrize`.
2. Plus the swap path: `TimbSwapRouter`, `TimbSwapPair`, `TimbSwapFactory`.
3. Plus the migration helpers in `scripts/`.

## Included only so the tests build (out of scope)

| Contract | Role in the tests |
|---|---|
| `VRFEntropy` | Randomness source the prize reads through an interface |
| `TimbTreasury` | Receives fees; tests fund the pot through it |
| `TimbYieldVault` | Yield reserve the prize harvests from |
| `GasFaucet` | Faucet payouts, funded separately from the treasury |

These hold the team's own funds, not other users', and are not part of the review. Findings that depend on them are welcome as context only.

## Tests

`tests/` holds the unit, money-path and invariant suites for the scoped contracts. Tests for contracts that were removed from this branch are not included.

## Not deployed in the beta

Staking, farms, governance, vesting, lock vaults and the airdrop are not part of the beta and are not in this branch.

## Known issues

Provided separately in the handover pack.
