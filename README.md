# minter-amqp
Routing key structure for minter data

The standard allows you to instantly receive information in real time without endless parsing of blocks, transactions and events.

* [Entities](#entities)
    * [Transactions](#transactions)
    * [Events](#events)
    * [Blocks](#blocks)
* [Recommended node addition process](#recommended-node-addition-process)
 
## Entities 

### Transactions

Message body: `Transaction hash`

Шаблон: 

```t{TypeTransaction}.{Recipient}.c_{Coin}.f{Sender}.{Node}.g_{GasCoin}.h{Height}.p{payload}```

Name  | Options, examples
----------- | -----------
Type transaction [?](https://docs.minter.network/#section/Transactions/Types) | 1, 2, 3, ..., 14
Recipient address  | Mx0123xxxabcd
Coin  | POPE, ROBOT, BTCSECURE
Sender address  | Mx0123xxxabcd
Node address  | Mp4321xxxxxdcba
Gas coin  | BIP
Height  | 1, 120, 240, 1000000
Availability payload  | 0, 1

Routing key examples
- t1.Mx7c39xxxxa8dc.c_ROBOT.fMxa161axxxxd444.null.g_BIP.h743.p0
- t7.null.c_BIP.fMxc26xxxx2c06.Mp4881xxxxxx045b.g_BIP.h743.p0

Binding keys example
- *.Mx0123xxxabcd.#
- t7.#.Mp4321xxxdcba.#
- t4.*.c_ROBOT.#
- t5.#
- \#.p1

### Events

Message body: `Block number`

Шаблон: 

```{EventType}.{Wallet}.c_{Coin}.{Node}.r{Role}.h{Height}```

Name  | Options, examples
----------- | -----------
Event type  | Reward, Slash, Unbond
Wallet address  | Mx0123xxxabcd
Coin  | ROBOT, POPE, BTCSECURE
Node address  | Mp4321xxxxxdcba
Type role  | DAO, Developers, Validator, Delegator
Height  | 1, 120, 240, 1000000

Routing key examples
- Reward.Mx0123xxxabcd.c_.Mp4321xxxxxdcba.rDelegator.h120
- Reward.Mx0123xxxabcd.c_.Mp4321xxxxxdcba.rValidator.h120
- Slash.Mx3dd6xxxxee8d.c_BIP.Mp2c82xxxxxxa2e9.rnull.h1455

Binding keys example
- *.Mx0123xxxabcd.#
- Slash.#
- Unbond.#.Mp4321xxxxxdcba.#
- Unbond.*.c_ROBOT.#

### Blocks
Message body: `Block number`

Шаблон: `h{Block}.{Signature}`

Name  | Options, examples
----------- | -----------
Block number  | 1, 120, 240, 1000000
Signature  | missed, sign

Routing key examples
- h1455.missed
- h120.sign

Binding keys example
- *.missed
- h1000000.*

## Recommended node addition process

- Add parameters to the config node:
    - node_url (amqp://guest:guest@localhost:5672)
    - Storage time in minutes (1440, 10080)
- Create `Exchanges` type `topic` if it did not exist
- Insert in [node](https://github.com/MinterTeam/minter-go-node) a code that sends one parameter with the necessary routing keys)
