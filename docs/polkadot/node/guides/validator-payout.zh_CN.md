## 支付方案（奖励）
Validators在中继链上的出块和为平行链区块签名，这些行为都是有回报的。 Validators会在每个era结束的时候（在Alexander 测试网上，这大约是每小时一次，Kusama和Polkadot主网上可能会有所不同）拿到奖励。 不管validator背后有多少质押（译注：stake）（validator自身的质押加上提名者质押的），所有validators平分总收入。  

例如：
```
Validator Set Size (v): 4
Validator 1 Stake (v1): 18 DOTs
Validator 2 Stake (v2):  9 DOTs
Validator 3 Stake (v3):  8 DOTs
Validator 4 Stake (v4):  7 DOTs
Payout (p): 8 DOTs

Payout for each validator (v1 - v4):
p / v = 8 / 4 = 2 DOTs
```
请注意，这与大多数其他权益证明系统（例如Cosmos）不同。 Validator Set中的Validator都将获得同等的奖励。 这个era中，拥有18个DOT的Validator v1与只有7个DOT的v4获得了相同的奖励（2个DOT）。

## 运行多个Validators
单个实体有可能运行多个Validators。 运行多个Validators可以获得更好的“风险/回报”率。 假设您有足够的DOT或足够的股份提名您的Validator，能确保您的Validator留在验证者集中，运行多个验证者将比运行单个验证者产生更高的收益。

假设您要投入18个DOT，为简单起见，我们将忽略提名人。 如上例所示，运行一个验证器将使您在该era获得2个DOT。
```
Validator Set Size (v): 4
Validator 1 Stake (v1): 18 DOTs <- Your validator
Validator 2 Stake (v2):  9 DOTs
Validator 3 Stake (v3):  8 DOTs
Validator 4 Stake (v4):  7 DOTs
Payout (p): 8 DOTs

Your payout = (p / v) * 1 = (8 / 4) * 1 = 2
```
运行两个Validators，并平均分割赌注，将导致原来的v4被踢出Validator Set，因为只有最高的v个验证程序（以Stake衡量）才被选入验证程序集。 更重要的是，您能获得翻倍回报：
```
Validator Set Size (v): 4
Validator 1 Stake (v1): 9 DOTs <- Your first validator
Validator 2 Stake (v2): 9 DOTs <- Your second validator
Validator 3 Stake (v3): 9 DOTs
Validator 4 Stake (v4): 8 DOTs
Payout (p): 8 DOTs

Your payout = (p / v) * 1 = (8 / 4) * 2 = 4
```
有了足够的Stake，您可以运行两个以上的Validators。 但是，每个Validator必须具有足够的Stake，因为只有如此才能确保进入Validator Set中。

##  惩罚（Slashing）
尽管奖励的分配是相等的，但惩罚却是根据Validator的stake数量。 因此，如果您确实有足够的DOT可以运行多个验证程序，则这样做最符合您的利益。 对于30％的惩罚，拥有18个DOT的Validator比起拥有9个DOT的Validator，当然前者罚的更多。

运行多个Validators不会使您免于不当行为的后果。Polkadot punishes attacks that appear coordinated more severely than individual attacks。 例如，您不应在同一基础设施（ infrastructure）上托管多个Validators。 正确地配置多Validators可以确保它们不会同时失效。

Nominators有动机提名Stake最低的Validator，因为这将以最低的风险获得最高的回报。

## Nominators和Validators分账
通过“投票”给Validators，您无需亲自运行Validator Node即可分享奖励（也可能是惩罚）。 Validators可以选择保留部分奖励，以“补偿”自己的费用以运行Validator  Node。 除此之外，所有奖励都是根据每个Validator的股份来分配的。 这包括Validator本身的股份，以及Validators的股份。

> 注意：验证者设置其偏好时用的是在DOT数量，而不是奖励的百分比。 Polkadot的块奖励基于总Stake量，当总Stake量达到总供给量的50％时，奖励达到顶峰。 在Stake量较少，因此奖励较低的时期，验证人偏好设置可能意味着Nominators的剩余为零。

在以下示例中，我们可以看到几种不同的Validator付款方案的结果，并在Validators和Nominators之间分配。 我们将为每个Validators假设一个提名人。 在实际情况中每个Validator可以有很多Nominators。 奖励仍按比例分配 - 例如，如果要给Nominator的总奖励为2个DOT，并且有四个Nominators，他们的股份相等，则每个人将获得0.5个DOT。 另请注意，单个Nominator可能会押不同的Validators。

示例中的每个Validator都选择了不同的validator payment（即，在与其他Nominators分享奖励之前，直接为validator自己预留了奖励）（译注：其他文章里说的是commission fees，即佣金）。 Validator的佣金（以DOT为单位）列在每个Validator旁边的方括号（[]）中。 请注意，由于Validator的佣金是公开的，因此Validator的佣金少或没有可能会吸引更多的Nominators来stake，因为他们知道他们将获得更多的奖励。

```
Validator Set Size (v): 4
Validator 1 Stake (v1) [0.2]: 18 DOTs (9 validator, 9 nominator)
Validator 2 Stake (v2) [0.4]:  9 DOTs (3 validator, 6 nominator)
Validator 3 Stake (v3) [0.1]:  8 DOTs (4 validator, 4 nominator)
Validator 4 Stake (v4) [0.0]:  6 DOTs (1 validator, 5 nominator)
Payout (p): 8 DOTs

Payout for each validator (v1 - v4):
p / v = 8 / 4 = 2 DOTs

v1:
0.2 DOTs -> validator payment
(2 - 0.2) = 1.8 -> shared between all stake
(9 / 18) * 1.8 = 0.9 -> validator stake share
(9 / 18) * 1.8 = 0.9 -> nominator stake share
v1 validator total reward: 0.2 + 0.9 = 1.1 DOTs
v1 nominator reward: 0.9 DOTs

v2:
0.4 DOTs -> validator payment
(2 - 0.4) = 1.6 -> shared between all stake
(3 / 9) * 1.6 = 0.53 -> validator stake share
(6 / 9) * 1.6 = 1.07 -> nominator stake share
v2 validator total reward: 0.4 + 0.53 = 0.93 DOTs
v2 nominator reward: 1.07 DOTs

v3:
0.1 DOTs -> validator payment
(2 - 0.1) = 1.9 -> shared between all stake
(4 / 8) * 1.9 = 0.95 -> validator stake share
(4 / 8) * 1.9 = 0.95 -> nominator stake share
v3 validator total reward: 0.1 + 0.95 DOTs = 1.05 DOTs
v3 nominator reward: 0.95 DOTs

v4:
0 DOTs -> validator payment
(2 - 0) = 2.0 -> shared between all stake
(1 / 6) * 2 = 0.33 -> validator stake share
(5 / 6) * 2 = 1.67 -> nominator stake share
v4 validator total reward: 0 + 0.33 DOTs = 0.33 DOTs
v4 nominator reward: 1.67 DOTs
```
