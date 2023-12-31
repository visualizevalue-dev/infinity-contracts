# Infinity (NFT)

## setup

1. run `yarn` to install project packages
2. run any hardhat task via `npx hardhat` or `hh` (if you have the [shorthand](https://hardhat.org/hardhat-runner/docs/guides/command-line-completion) installed)
3. e.g. run tests via `npx hardhat test`

## intro

> *"ages like 0.008 eth..."*

infinities are an infinitely collectable collectible in the ethereum ecosystem.

you create infinities by depositing 0.008 eth into the contract. burning an infinity token returns that eth to the owner of the nft.

the visuals are randomly arranged grids with symbols created of the base shape that makes up the checks and opepen projects, generated and rendered fully onchain.

## core functionality

there are basically three different logic flows in the contract:

1. **generate**: create a token based on an eth deposit
2. **degenerate**: burn a token and withdraw the deposited eth
3. **regenerate**: burn + remint a token to generate new artwork

each of those functions has a corresponding _xyzMany_ call, which performs the action on multiple tokens at once.

## design considerations

the contract is a simplified version of a typical OpenZeppelin ERC1155 token. 

the most important adjustment is that infinities cannot be `approved` to operators, like typical NFTs. thus, programmability of infinity tokens is restricted to token `onReceive` hooks.

a few unused functionalities like beforeTransfer and afterTransfer hooks and approval checks were removed from the base contract, to make token interactions as cheap as possible.

token attributes and visuals are seeded via token ids, which are generated by using a combination of `prevrandao`, the account address and the remaining gas during mint.

by using randomness during mint, users cannot specify a specific token id to mint, unless that token already exists. existing tokens can be reminted indefinitely by anyone at any time. over time, this will visualize *"taste"* at the collection level.
in order to save gas during token generation, we don't store whether a token has been minted. in order to validate a token exists, minters have to additionally pass the account address of any existing holder of the token as the token `source`.

unlike all other accounts, the visualize value creator account (currently `visualizevalue.eth`) has the right to force-mint any token it pleases.

as a nod to the black check (see checks.art), every 4096th token is a monochromatic, black on white design. token #0 is referred to the "genesis" piece, a full flower single infinity check, and will be airdropped to eligible checks elements (see elements.checks.art) auction bidders.

### reasoning for preventing traditional approval flows

infinities are to be collected, not traded on marketplaces. there is zero cost benefit to do so, so we consciously prevent doing this via the typical flows used for it (approvals). the only time people would trade these is if they don't know they are infinitely mintable on the contract.

programmability still exists, by utilizing the onReceive hooks outlined in the [EIP](https://eips.ethereum.org/EIPS/eip-1155) (contracts can run arbitrary logic when being sent infinity tokens).


## deployment (bytecode state of `7e4e389...f722ba`) (out of date)

- salt: `0x5d41dbb04825b039a98d9169dbfc569f510c6e25f4995cb6822e4015f13fd716`; final address: `0x000D39614481839520901aF2eC5bB81F87888888`
- salt: `0x3997c69a39dd451b5503e35287918552c9384b529b80b77476919bfe2def4f36`; final address: `0x0000943A32B3902d5e6C9aF906009c0024148888`
