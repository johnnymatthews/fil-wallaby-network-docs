# Filecoin Wallaby Testnet Documentation


## ToC
* [About](#about)
    * [Filecoin official Information Sources](#filecoin-official-information-sources)
* [How to join the network](#how-to-join-the-network)
    * [Building](#building)
    * [Running a 512MiB miner](#running-a-512mib-miner)
        * [Tips and Tricks](#tips-and-tricks)
* [Infrastructure](#infrastructure)
    * [Faucet](#faucet)
    * [Notary](#notary)
    * [Deal Miners](#deal-miners)
        * [t01017 - 512MiB Sectors](#t01017---512mib-sectors)
        * [t01018 - 32GiB Sectors](#t01018---32gib-sectors)
    * [Chain Snapshots](#chain-snapshots)
        * [Full chain snapshots](#full-chain-snapshots)
        * [Lightweight (pruned) chain snapshots](#lightweight-pruned-chain-snapshots)
* [Contact](#contact)
    * [Slack](#slack)
        * [#fil-net-wallaby-help](#fil-net-wallaby-help)
        * [#fil-net-wallaby-discuss](#fil-net-wallaby-discuss)
    * [eMail](#email)


## About

The wallaby testnet is a filecoin testnet dedicated to wasm testing for the upcoming [Filecoin Virtual Machine - FVM](https://fvm.filecoin.io)

Maintained and Infrastructure provided by Factor8 Solutions. 

### Filecoin official Information Sources

[Filecoin documentation: Available networks](https://docs.filecoin.io/networks/overview/#available-networks)

[Wallaby Testnet github (filecoin-project)](https://github.com/filecoin-project/testnet-wallaby#wallaby-testnet)

## How to join the network

The wallaby testnet is like any other filecoin testnet besides being build on a specific branch to allow for wasm related fvm testing.

### Building

Please follow the [guide in the lotus documentation](https://lotus.filecoin.io/lotus/install/linux/) on joining filecoin networks. 

In Step 5 of [Build and install lotus](https://lotus.filecoin.io/lotus/install/linux/#build-and-install-lotus) replace 

`git checkout releases` with `git checkout experimental/fvm-m2`

and replace

`make clean all` with `make clean wallabynet`

In Short:

```bash
git clone https://github.com/filecoin-project/lotus
cd ./lotus
git checkout experimental/fvm-m2

[possibly export some vars here]

make clean wallabynet
```

### Running a 512MiB miner

The wallaby testnet can run not only 512MiB sectors but also 32GiB and 64GiB sectors

Please follow the [guide in the lotus documentation](https://lotus.filecoin.io/storage-providers/get-started/overview/) on running a filecoin miner. 

During the [Initialization](https://lotus.filecoin.io/storage-providers/setup/initialize/#initialization) step add the flag `--sector-size=512MiB` to the `lotus-miner init --owner=<address>  --worker=<address> --no-local-storage` command to runa  512MiB miner

`lotus-miner init --owner=<address>  --worker=<address> --no-local-storage --sector-size=512MiB`

Please take note that [Performance tweaks](https://lotus.filecoin.io/storage-providers/setup/prerequisites/#performance-tweaks) needs to be adjusted for 512MiB sectors too!

#### Tips and Tricks

- 512MiB sector PC1 are fast. Due to the fact that PC2 takes up all available cores for a single task it is impossible to get more than one PC2 per machine done in parallel without resource limitation per worker.

- If you batch precommits - be careful with the C2 tasks. A single worker will start as many as it can at once, leading to slow down of all of them (same for restarts of a c2 worker with a lot of sectors expecting to leave `WaitSeed` during the down time). We recommend to turn off precommit batching all together with 512MiB miners

- It seems to make no sense to do span deals for speed purposes on 512MiB sector miners. The RU2 task alone takes longer than sealing a whole sector (sure - 5 mins `WaitSeed` plays it part in that)

## Infrastructure

### Faucet

The faucet for the Wallaby Testnet can be found at [https://wallby.network](https://wallaby.network/#faucet).

The faucet currently emits 50 FIL per request. Should you need more FIl at once to conduct testing please [Contact](#contact) us and we will discuss your needs for more FIL.

### Notary

There is currently no publicly available notary interface for the Wallaby Testnet. The testnet is prepared to deploy notary infrastructure but at the current point the feature is not available to the public.

### Deal Miners

#### t01017 - 512MiB Sectors

This miner provides an untested sealing capacity per day. The throughput will be limited to 1 sector at a time (approx. 5-10 mins + 5 mins WaitSeed per sector). The miner is not sealing CC sectors. 

If you explicitly need 512MiB snap deal testing please [Contact](#contact) us - we'd prefer to do snap deal testing with the [t01018 - 32GiB Sectors](#t01018---32gib-sectors) miner as it utilizes resources way better.

##### On chain published address

`/ip4/194.45.196.130/tcp/11111`

##### Configurations parameters

Please refer to the following configuration params to get a clearer picture what to expect from the miner side when doing deals:

```toml
[Dealmaking] 
  ConsiderOnlineStorageDeals = true
  ConsiderOfflineStorageDeals = false
  ConsiderOnlineRetrievalDeals = true
  ConsiderOfflineRetrievalDeals = false
  ConsiderVerifiedStorageDeals = true
  ConsiderUnverifiedStorageDeals = true
  ExpectedSealDuration = "1h0m0s"
  MaxDealStartDelay = "480h0m0s"
  PublishMsgPeriod = "0h1m0s"
  MaxDealsPerPublishMsg = 2
  SimultaneousTransfersForStorage = 5
  SimultaneousTransfersForStoragePerClient = 1
  SimultaneousTransfersForRetrieval = 5
  StartEpochSealingBuffer = 480
[IndexProvider]
  Enable = false
[Sealing]
  MaxWaitDealsSectors = 1
  MaxSealingSectors = 1
  MaxSealingSectorsForDeals = 1
  PreferNewSectorsForDeals = true
  MaxUpgradingSectors = 0
  WaitDealsDelay = "1h0m0s"
  AlwaysKeepUnsealedCopy = true
  FinalizeEarly = true
  BatchPreCommits = false
  AggregateCommits = false
```

#### t01018 - 32GiB Sectors

This miner provides around 1TiB (32-34 sectors) of sealing capacity per day. The miner is not sealing CC sectors actively after an initial ~64 sectors for possible on demand snap deal testing. 

All deals will be normally sealed - please [Contact](#contact) us for snap deal testing!

##### On chain published address

`/ip4/194.45.196.130/tcp/11113`

##### Configurations parameters

Please refer to the following configuration params to get a clearer picture what to expect from the miner side when doing deals:

```toml
[Dealmaking] 
  ConsiderOnlineStorageDeals = true
  ConsiderOfflineStorageDeals = false
  ConsiderOnlineRetrievalDeals = true
  ConsiderOfflineRetrievalDeals = false
  ConsiderVerifiedStorageDeals = true
  ConsiderUnverifiedStorageDeals = true
  ExpectedSealDuration = "8h0m0s"
  MaxDealStartDelay = "480h0m0s"
  PublishMsgPeriod = "0h1m0s"
  MaxDealsPerPublishMsg = 2
  SimultaneousTransfersForStorage = 5
  SimultaneousTransfersForStoragePerClient = 1
  SimultaneousTransfersForRetrieval = 5
  StartEpochSealingBuffer = 480
[IndexProvider]
  Enable = false
[Sealing]
  MaxWaitDealsSectors = 2
  MaxSealingSectors = 20
  MaxSealingSectorsForDeals = 20
  PreferNewSectorsForDeals = true
  MaxUpgradingSectors = 0
  WaitDealsDelay = "1h0m0s"
  AlwaysKeepUnsealedCopy = true
  FinalizeEarly = true
  BatchPreCommits = false
  AggregateCommits = false
```

### Chain Snapshots

Currently the network is so small it makes no sense to provide snapshots. As soon as the network is proven to be stable and running for around 1 month we will start providing full chain and lightweight (pruned) snapshots. If you need a snapshot, for whatever reason, now please [Contact](#contact) us. Thanks!

#### Full chain snapshots

Coming Soon!

#### Lightweight (pruned) chain snapshots

Coming Soon!

## Contact

There are 2 ways to get in contact with us for issues regarding the wallaby testnet:

### Slack

We are maintaining 2 slack channels in the [Filecoin Slack Workspace](https://filecoin.io/slack) dedicated to the wallaby testnet:

#### #fil-net-wallaby-help

Run `/join #fil-net-wallaby-help` after joining the [Filecoin Slack Workspace](https://filecoin.io/slack)

The chan is meant for help with joining the network and running specific tests on the network

#### #fil-net-wallaby-discuss

Run `/join #fil-net-wallaby-discuss` after joining the [Filecoin Slack Workspace](https://filecoin.io/slack)

This chan is meant for updates and general discussion around the wallaby testnet 

### eMail

Please see the available [Slack Channels](#slack) as a primary way to contact us. 

Should the slack be offline or the factor8 team offline/unresponsive for longer periods of time please feel free to send us a mal to [wallaby@factor8.io](mailto:wallaby@factor8.io).
