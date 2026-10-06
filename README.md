# foundry-dao-governance

A small Foundry project that wires up a minimal DAO: governance token, OpenZeppelin Governor, timelock, and a simple contract the DAO actually controls.

This follows the common ERC20-votes pattern on purpose—it’s a learning baseline, not a recommendation for production. Transferable governance tokens invite speculation and vote buying; if you ship something real, look at soulbound voting, conviction voting, or other models that separate “membership” from tradable assets.

## What’s in here

- **Box** — Ownable storage contract; only the timelock can call `store`.
- **GovToken** — ERC20 with permit and vote checkpoints (OpenZeppelin wizard stack).
- **MyGovernor** — Governor with settings, simple counting, quorum fraction, and timelock control.
- **Timelock** — Thin wrapper around `TimelockController` with a configurable delay.
- **Tests** — End-to-end flow: propose → vote → queue → execute, plus a check that random callers can’t touch the box.
- **Bonus** — `BadAirdrop` vs `GoodAirdrop` gas patterns (loops, calldata, immutables).

## Requirements

- [Foundry](https://book.getfoundry.sh/getting-started/installation)

Dependencies are vendored via git submodules (`forge-std`, OpenZeppelin Contracts).

```bash
git clone --recurse-submodules https://github.com/silvergin7/foundry-dao-governance.git
cd foundry-dao-governance
```

If you already cloned without submodules:

```bash
git submodule update --init --recursive
```

## Commands

```bash
forge build
forge test
forge test --mt testGovernanceUpdatesBox -vvv
```

## How it fits together

1. Deploy timelock, governor, token, and box.
2. Grant the governor proposer (and open executor) roles on the timelock; tighten admin.
3. Transfer box ownership to the **timelock** (not the governor).
4. Token holders delegate, propose actions against the box (or anything else), vote, then queue and execute through the timelock after the delay.

Default governor settings in this repo use a 7200-block voting delay and 50400-block voting period (block-based clock), 4% quorum, and a one-hour timelock delay in tests.

## License

MIT
