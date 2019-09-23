# Staking

Polkadot使用NPoS（提名权益证明）作为其选择validator set的机制。它设计了“validators”和“nominators”两个角色，以最大化链的安全性。有兴趣维护网络的参与者可以运行validator node。在创世之初，Polkadot将为这些validators提供数量有限的插槽，但是随着时间的推移，这个数量将增长到一千多个。该系统还鼓励不限数量的DOT持有人作为nominator参加，nominator可以支持一个或多个受信任的validators候选人，以确保网络不会仅集中在少数validators上。validators既负责要块的验证，又要负责保证链的finality，而nominators则通过他们的DOT来选择validator set。validators通过完成协议中的付费操作（例如，在BABE中产生一个新的块）来获得奖励，nominators将根据其押在特定验证者身上的份额获得报酬，validators可以先拿掉一部分奖励（validator自己决定多少）。

## How does staking work in Polkadot?

### 1. Identifying which role you are

In staking，您可以是[nominator 或者 validator](＃validators-and-nominators)。 作为nominator，您可以提名您信任的多个validator候选人，以帮助您赚取DOT奖励。 您可以看一下[提名指南](http://wiki.polkadot.network/en/latest/polkadot/node/guides/how-to-nomination/)，以了解主网启动时您需要做些什么。 同时，validator node需要24/7响应，及时执行其预期的职责，并避免任何可能被惩罚的行为。 如果要在当前的测试网中测试成为validator，可以看看我们的[validator指南](http://wiki.polkadot.network/en/latest/polkadot/node/guides/how-to-validate/)。

### 2.  Nomination period

任何潜在的validators都可以表明他们打算成为validator候选人。 候选人对所有nominators都公开，nominators可以提交一份其支持的候选人名单（译注：同时，押上自己的DOT）。 在下一个epoch（几个小时后），那些具有最多DOT支持的候选人会被选出来成为validators。 对于DOT持有人成为nominator没有特殊要求，尽管我们希望每个提名人都仔细跟踪验证人的表现和声誉。

提名期结束后，NPoS选举机制将提名人及其相关选票作为输入，并输出一组所需数量的验证人，这将最大化任何validator的股权支持，并使validators间的股权尽可能地平均分配。 该选举机制的目标是最大化网络的安全性，并实现nominators的公平代表。 如果您想进一步了解NPoS的工作原理（例如选举，运行时间复杂度等），请阅读[here](http://research.web3.foundation/zh/latest/polkadot/NPoS/)。

### 3. Staking Rewards Distribution

To explain how rewards are paid to validators and nominators, we need to consider **validator pools**, where a validator pool consists of an elected validator together with the nominators backing it. (Note: if a nominator n with stake s backs several elected validators, say k, the NPoS election mechanism will split its stakes into pieces s_1, s_2, …, s_k, so that it backs validator i with stake s_i. In that case, nominator n will be rewarded the same as if there were k nominators in different pools, each backing a single validator i with stake s_i). For each validator pool, we keep a list of nominators with the associated stakes.

The general rule for rewards across validator pools is that two validator pools get paid the **same amount of DOTs** for equal work, i.e. they are NOT paid proportional the stakes in each pool. Within a validator pool, a part of the reward goes to pay the validator’s commission fees, and the remainder is paid **pro-rata** (i.e. proportional to stake) to the nominators and validator. Notice in particular that the validator is rewarded twice: once as commission fees for validating, and once for nominating itself with stake.

To estimate the inflation rate and how many DOTs you can get each month as a nominator or validator, you can use this [Excel sheet](https://docs.google.com/spreadsheets/d/1-9Hc3kZ23EhZC3X6feRUKSTv6gj4xR7cvUbJD2zUEZk/edit?usp=sharing) as a reference and play around with it by changing some parameters (e.g. validator pools, total supply, commission fees, etc.) to have a better estimate. Even though it may not be entirely accurate since staking participation is changing dynamically, it works well as an indicator.

### 4. Rewards Mechanism

We highlight two features of this payment scheme. The first is that since validator pools are paid the same, pools with less stake will pay more to nominators per-DOT than pools with more stake. We thus give nominators an economic incentive to gradually shift their preferences to lower staked validators that gain a sufficient amount of reputation. The reason for this is that we want the stake across validator pools to be as evenly distributed as possible, to avoid a concentration of power among a few validators. In the long term, we expect all validator pools to have similar levels of stake, with the stake being higher for higher reputation validators (meaning that a nominator that is willing to risk more by backing a validator with a low reputation will get paid more, which is reasonable).

Let's take a look at the following example.

For simplicity, we have the following assumptions.

* These validators do not have a stake of their own.
* They do NOT charge any commission fees (although they can)
* Rewards amount is 100 DOT tokens
* The least amount of DOTs to be a validator is 350


||**A - Validator Pool**|||
|:----:|:----:|:----:|:----:|
|Nominator (4) | Stake (600) | Fraction of the Total Stake | Rewards|
|Jin| 100 | 0.167 | 16.7|
|**Sam**| 50 | 0.083 | 8.3|
|Anson| 250 | 0.417 | 41.7|
|Bobby | 200 | 0.333 | 33.3|

||**B - Validator Pool**|||
|:----:|:----:|:----:|:----:|
|Nominator (4) | Stake (400) | Fraction of the Total Stake | Rewards|
|Alice | 100 | 0.25 | 25|
|Peter | 100 | 0.25 | 25|
|John | 150 | 0.375 | 37.5|
|**Kitty** | 50 | 0.125 | 12.5|

_Both validator pools A & B have 4 nominators with the total stake 600 and 400 respectively._

Based on the above rewards distribution, nominators in validator pool B get more rewards per DOT than those in pool A because pool A has more overall stake. Sam has staked 50 DOTs in pool A, but he only gets 8.3 in return, whereas Kitty gets 12.5 with the same amount of stake.

We also remark that when the network slashes a validator slot for a misbehavior (e.g. validator offline, validating an invalid block, etc.) the slashed amount is a fixed percentage (and NOT a fixed amount of DOTs), which means that validator pools with more stake get slashed more DOTs. Again, this is done to provide nominators with an economic incentive to shift their preferences and back less popular validators whom they consider to be trustworthy.

The second point we want to highlight is that each validator candidate is free to name their desired commission fee (as a fixed amount of DOTs) to cover operational costs. Since validator pools are paid the same, pools with lower commission fees pay more to nominators than pools with higher fees. Thus, each validator can choose between increasing their fees to earn more DOTs, or decreasing their fees to attract more nominators and increase their chances of being elected. We will let the market regulate itself in this regard. In the long term, we expect that all validators will need to be cost efficient to remain competitive, and that validators with higher reputation will be able to charge slightly higher commission fees (which is fair).

## Accounts

There are three different accounts for managing your funds: `Stash`, `Controller` and `Session` accounts.

![staking](../../img/NPoS/staking-keys.png)

- **Stash:** This is the primary account that holds the funds and has a portion bonded for participation; The funds can be kept in a cold wallet; All bonded DOTs are locked. After unbonding, users must wait a certain amount of time in order to access the locked funds (600 blocks at the time of writing).
- **Controller** This is used to control the operation of the validator or nominator, switching between validating, nominating and idle; (It only needs enough funds to send transactions when actions are taken).
- **Session** 
> Note: This only for the current Alexander testnet. For details about session keys in Kusama Network or Polkadot mainnet, please read [here](./keys.md#session-keys). 

The seed of this account should be passed to the node using the `--key` parameter. You may pass in either a mnemonic (recommended) or a legacy raw seed for the key parameter. The session account does not need to have funds as it does not need to send any transaction. The best practice is to create a dedicated account to be used as session account. Although a single account can theoretically be used as both `session` and `controller`, it is not recommended to do so. Having a dedicated `session` account would prevent the theft of funds should the validator node be compromised and the `--key` leaked.  Note that Session keys should always be of crypto type `Edwards (Ed25519)`, not the default `Schnorrkel (sr25519)`. 

For more on how keys are used in Polkadot and the cryptography behind it [see here](./keys.md).

## Validators and nominators

Since validator slots will be limited, most of those who wish to stake their DOTs and contribute economic security to the network will be nominators. Validators do most of the heavy lifting, they produce new block candidates in BABE, vote and come to consensus in GRANDPA, validate STF of parachains, and possibly some other responsibilities in regard to data availability. Nominators, on the other hand do not need to do anything once they have bonded their DOTs. The experience of the nominator is similar to "set it and forget it" while the validator will be doing an actual service for the network by performing the critical operations. For this reason, the validator has certain privileges in regard to the payout of the staking mechanism and will be able to declare its own allocation before the share is divided to nominators.

![staking](../../img/NPoS/article-2.png)

### Want to stake DOTs?

- [Nominator Guide](../node/guides/how-to-nominate.md) - Become a nominator on the Alexander testnet.
- [Validator Guide](../node/guides/how-to-validate.md) - Become a validator on the Alexander testnet.

## Slashing

Slashing will happen if a validator misbehaves(e.g. always offline, attack the network or running modified software) in the network, they and their nominators will get slashed by losing a percentage of their bonded/staked DOTs. 

As validators have more DOTs staked, they will get slashed more, so we encourage nominators to shift their nominations to less popular validators to reduce the risk of being lost more. 

Based on the latest Polkadot's codebase, the following slashing have been implemented:

### Unresponsiveness

For every session, validators will send a "I'm Online" message to indicate they are online while unresponsiveness means that the validator fails to send the heartbeat. Depending on the repeated offences and how many other validators were offline, slashing will occur. If one-third of all validators are unresponsive, 5% of their bonded DOTs will be slashed.

Here is the formula for calculation:

!!! info
    Let x = offenders, n = total no. validators

    Min( (3 * (x - 1)) / n, 1) * 0.05


Validators should have a well-architected network infrastructure to ensure the node is running to reduce the risk of being slashed, for example, having high availability support to guarantee even though when the node went offline, you can still have another machine to take place.

### Grandpa Equivocation

A validator signed two or more votes in the same round on different chains.

### Babe Equivocation 

A validator produces two or more blocks on the relay chain in the same time slot. 


Grandpa and Babe equivocation slashing amount is calculated as below:

!!! info
    Let x = offenders, n = total no. validators

    Min( (3 * x / n )^2, 1)

Validators may run their nodes on multiple machines to make sure they can still perform validation work in case if one of their nodes goes down, but if they do not have a good coordination to manage those machines to do signing that can potentially cause the equivocation problem, hence, it would be better to have something like KMS (Key Management Server) as a middleware in between those machines to coordinate those tasks in order to avoid this kind of problem occurs.

> Notice:
If a validator is reported for anyone of the offences they will be removed from the validator set and they will not be paid while they are kicked out.

If you want to know the details of the slashing, please look at our [research page](https://research.web3.foundation/en/latest/polkadot/slashing/amounts/).

## Reward Distribution

Based on the the current configuration in the Alexander testnet, rewards are recorded per session that is roughly 5 minutes and paid per era. It takes 1 hour to finish an era; that means rewards will be distributed to the validators and nominators per hour.

!!! info
    **Example:**

    PER_ERA * BLOCK_TIME = **Reward Distribution Time**
    
    600 * 6 = 3600 = 1 Hour
    
    ***These parameters can be changed by proposing a referendum***

Validator can create a cut of the reward that is not shared with the nominators. After value gets deducted, the remaining portion is based on their staked value and split between the validator and all of the nominators who have voted for this validator.

For example, assume reward is 100 DOTs.
A validator may specify `validator_payment = 50 DOTs` and the remaining 50 DOTs would split between the validator and their nominators based on the portion of stakes they had.

Rewards can be used by the same account (controller) to keep accumulating the rewards or by the stash account (increasing the staked value/not increasing the staked value). Also, it is possible to top-up/withdraw partial bonded DOTs without having to complete un-stake everything.

## Inflation

It will be closed to 10% in the first year. Each validator will get 1,000 - 2,000 DOTs per month to share with their nominators.

![staking](../../img/NPoS/staking-participation-rate.png)

<sub><sup>Source: [Research - Web3 Foundation](https://research.web3.foundation)</sup></sub>

**x-axis**: amount of DOTs staked

**y-axis**: the annualized percentage

**Green line**: return rate based on the staking participation

**Blue line**:  inflation rate

The above chart shows the inflation model of the network. Depending on the staking participation, the inflation rate will be dynamically changed to incentivize / disincentivize token holders to participate in staking. For instance, inflation would be 10% if there is 50% of DOTs being staked to the network.

Determining the ideal staking rate is not an easy task as the network requires enough DOTs to be staked to provide the security guarantees we want and to avoid illiquidity on the market.

For those who are interested in knowing more about the design of inflation model for the network, please see [here](https://research.web3.foundation/en/latest/polkadot/Token%20Economics/).

## Why stake?

- 10% inflation/year when the network launches
- 50% targeted active staking
- ~20% annual return

## Why not stake?

- Tokens will be locked for about 12 weeks
- Punishment in case of validator found to be misbehaving

## How many validators will Polkadot have?

The plan is to start with somewhere between 50 to 100 open validator positions and open more gradually. The top bound on the number of validators has not been determined yet, but should only be limited by the bandwidth strain of the network due to frequent and voluminous peer-to-peer message passing. The practical estimation of the number of validators that Polkadot will have at maturity is around 1000.

## Resources

- [How Nominated Proof of Stake will work in Polkadot](https://medium.com/web3foundation/how-nominated-proof-of-stake-will-work-in-polkadot-377d70c6bd43) - Blog post by Web3 Foundation researcher Alfonso Cevallos covering NPoS in Polkadot.
