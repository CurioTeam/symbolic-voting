# Symbolic Voting

This repository contains a general voting contract designed to be Curio Stablecoin community polling (CGT holders).

## Summary

The symbolic voting contract is an extremely lightweight contract that can really be thought of as a specialized event emitter. All votes and polls are simply events emitted by this contract and all tallying is done off-chain (a fact which means the contract itself requires almost no storage and very little gas). The tradeoff is that, without state, other smart contracts can't query it for information. However, because symbolic votes don't need to be binding on-chain, it was decided that whatever could be pushed to social layer, should be. In brief, the idea here is that voters signal their intent with on-chain events, then services and community members can, using the rules defined in each poll, tally the polls and imbue them with meaning off-chain (eg by summing up the amount of CGT voting for each poll option at a certain block).

## The Polls

The polls are, like everything in the symbolic voting contract, emitted events. Specifically, the poll event contains the poll's start date, its end date, and the hash of the contents of the poll details, rules, and metadata. By emitting the hash of a poll document, voters can trust that the content and rules of a poll does not change throughout its lifetime. It also means that Curio Stablecoin community members can cryptographically verify for themselves that the hash of the content shown on frontends matches what they're voting for on-chain. It's worth mentioning that *all* the information for the poll is contained within the hashed document, including the voting options, the context for the poll, and the rules that would allow one to tally the poll.

## Vote Tallying

The votes are not tallied on-chain. Instead, votes are tracked off-chain, cached, and presented via governance application. While this is done in a centralized way, because the all voting events and CGT token balance are available on-chain, any voter is able to make his/ her own cache and serve the same data, or, in the very least, they could verify that what the governance dashboard displays is correct. The current tallying method for polls is to take an address's voting weight at the either the latest block or the block specified by the poll's end date (if the poll has ended) where voting weight is determined to be:

- `CGT amount locked in chief directly + CGT account balance` for all voters
- if the voter has a proxy associated with their address, then it's that plus `CGT amount locked in chief via vote proxy + (for their linked wallet) CGT locked in chief directly + (for linked wallet) CGT account balance`

## Requirements
You must have a Unix-like operating system. To compile, test and deploy smart contracts, utilities are used that require a Nix environment.

## Installation

Install [Nix](https://nixos.org/) if you haven't already:

```shell script
# user must be in sudoers
curl -L https://nixos.org/nix/install | sh

# Run this or login again to use Nix
. "$HOME/.nix-profile/etc/profile.d/nix.sh"
```

Then install [dapptools](https://github.com/dapphub/dapptools):

```shell script
curl https://dapp.tools/install | sh
```
## Usage

### Compile

```shell script
dapp build
```

### Test

```shell script
dapp test
```

### Deploy

Configuration of deployer wallet:

* use ETH v3 encoded private key and copy JSON part to ```./keystore/me.json```
* copy the password of JSON file to ```./keystore/pwd```
* replace the values ```{deployer_address}``` (as in JSON file), ```{eth_net}``` (```mainnet```, ```kovan```, ... ), ```{infura_project_id}``` you need in the command below

**Note:** Deployment of smart contract requires about 500,000 gas units (used gas). You can set variables ```ETH_GAS``` (gas limit in gas units), ```ETH_GAS_PRICE``` (gas price in wei) to adjust the gas.	 

Run deploy command:

```shell script
ETH_FROM={deployer_address} \
ETH_PASSWORD=./keystore/pwd \
ETH_KEYSTORE=./keystore \
ETH_GAS=800000 \
ETH_RPC_URL=https://{eth_net}.infura.io/v3/{infura_project_id} \
TMPDIR=/tmp \
dapp create PollingEmitter
```

The deployed contract address will be displayed in the command output.

### Clean output directory

```shell script
dapp clean
```
