---
title: Staking
id: staking
---

In order to participate in the redistribution of xBZZ from uploaders to storers, storers must first deposit a non-refundable xBZZ stake with a smart contract. Then, they are going to be chosen for payout with a probability proportional to their stake in their neighbourhood, as long as they can log storing the part of the content that they are supposed to be storing according to protocol rules.

In order to participate in redistribution, storers need to do the following:

- Join the network and download all the data that the protocol assigns to them. They can only participate if they are fully synchronised with the network.
- Deposit a stake with the staking contract. There is a minimum staking requirement, presently 10 xBZZ. It can change in the future.
- Stay online and fully synced, so that when a redistribution round comes, their node can check whether their neighbourhood (nodes that are assigned the same content to store) has been selected and if so, they can perform a certain calculation (a random sampling) on their content and submit the result to the redistribution contract. This happens in two phases (commit and reveal), so that the nodes cannot know the results of others’ calculations when committing to their own.
- Round length is estimated around 15 minutes (152 blocks to be precise), though it can be extended.

Amongst the nodes that agree with the correct result, one is chosen — with a probability in proportion to their stake — as the winner. The winner must execute an on-chain transaction claiming their reward, which is the entire pot of storage rent paid since the previous round, or even more, if the previous pot has not been claimed at that time.

## Stake your node with Bee

Bee has builtin endpoints for depositing the stake. Currently the minimum staking requirement is 10 xBZZ, so make sure that there is enough tokens in the node's wallet and you must have some native token as well for paying the gas.

Then you can run the following command to stake 10 xBZZ. The amount is given in PLURs which is the smallest denomination of xBZZ and `1 xBZZ == 1e16 PLUR`.

```bash
curl -XPOST localhost:1635/stake/100000000000000000
```

If the command executed successfully it returns a transaction hash that you can use to verify on a block explorer.

It is possible to deposit more by repeatedly using the command above.

You can also check the amount staked with the following command:

```bash
curl localhost:1635/stake
```

## Check redistribution status

Use the <a href="/debug-api/#tag/RedistributionState" target="_blank" rel="noopener noreferrer">RedistributionState</a> endpoint of the API to get more information about the redistribution status of the node.

```
curl -X GET http://localhost:1635/redistributionstate | jq
```

```json
{ 
  "minimumFunds": "18750000000000000",
  "hasSufficientFunds": true,
  "isFrozen": false,
  "isFullySynced": true,
  "phase": "commit",
  "round": 176319,
  "lastWonRound": 176024,
  "lastPlayedRound": 176182,
  "lastFrozenRound": 0,
  "block": 26800488,
  "reward": "10479124611072000",
  "fees": "30166618102500000"
}
```
* `"minimumFunds": <integer>` - The minimum xDAI needed to play a single round of the redistribution game (the unit is 1e-18 xDAI).
* `"hasSufficientFunds": <bool>` - Shows whether the node has enough xDAI balance to submit at least five storage incentives redistribution related transactions.  If `false` the node will not be permitted to participate in next round.
* `"isFrozen": <bool>` - Shows node frozen status.
* `"isFullySynced": <bool>` - Shows whether node's localstore has completed full historical syncing with all connected peers.
* `"phase": <string>` - Current phase of redistribution game.
* `"round": <integer>` - Current round of redistribution game. The round number is determined by dividing the current Gnosis Chain block height by the number of blocks in one round. One round takes 152 blocks, so using the "block" output from the example above we can confirm that the round number is 176319 (block 26800488 / 152 blocks = round 176319).   
* `"lastWonRound": <integer>` - Number of round last won by this node.
* `"lastPlayedRound": <integer>` - Number of the last round where node's neighborhood was selected to participate in redistribution game.
* `"lastFrozenRound": <integer>` The number the round when node was last frozen. 
* `"block": <integer>` - Gnosis block of the current redistribution game.
* `"reward": <string (BigInt)>` - Record of total reward received in [PLUR](/docs/learn/glossary#plur).
* `"fees": <string (BigInt)>` - Record of total spent in 1E-18 xDAI on all redistribution related transactions.

## Check node performance

One of the most common issues affecting staking is the `sampler` process failing. The sampler is a resource intensive process which is run by nodes which are selected to take part in redistribution. The process may fail or time out if the node's hardware specifications aren't high enough. To check a node's performance the `/rchash/{depth}/{anchor}` endpoint of the API may be used. 

Before proceeding, first check that the node is fully synced, is not frozen, and has sufficient funds to participate in staking. To check node sync status, call the `redistributionstate` endpoint:

```
curl -X GET http://localhost:1635/redistributionstate | jq
```
Response:

```json
{ 
  "minimumFunds": "18750000000000000",
  "hasSufficientFunds": true,
  "isFrozen": false,
  "isFullySynced": true,
  "phase": "commit",
  "round": 176319,
  "lastWonRound": 176024,
  "lastPlayedRound": 176182,
  "lastFrozenRound": 0,
  "block": 26800488,
  "reward": "10479124611072000",
  "fees": "30166618102500000"
}
```
Confirm that `hasSufficientFunds` is `true`, and `isFullySynced` is `true` before moving to the next step. If `hasSufficientFunds` if `false`, make sure to add at least the amount of xDAI shown in `minimumFunds` (unit of 1e-18 xDAI). If the node was recently installed and `isFullySynced` is `false`, wait for the node to fully sync before continuing. After confirming the node's status, continue to the next step.

The `{anchor}` value can be set to any random string. To get the `{depth}` value, first call the `topology` debug API endpoint using [jq](https://stedolan.github.io/jq/manual/#Basicfilters) to filter for the `depth` `value:

```
sudo curl -sX GET http://localhost:1635/topology | jq '.depth'
```

Output:

```
8
```

Call the endpoint using the value returned (8 in our example) like so:

```
sudo curl -X GET http://localhost:1633/rchash/8/randomstring | jq
```

If the sampler runs successfully, you should see output like this:

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   659  100   659    0     0      3      0  0:03:39  0:02:54  0:00:45   161
{
  "Sample": {
    "Items": [
      "000003dac2b2f75842e410474dfa4c1e6e0b9970d81b57b33564c5620667ba96",
      "00000baace30916f7445dbcc44d9b55cb699925acfbe157e4498c63bde834f40",
      "0000126f48fb1e99e471efc683565e4b245703c922b9956f89cbe09e1238e983",
      "000012db04a281b7cc0e6436a49bdc5b06ff85396fcb327330ca307e409d2a04",
      "000014f365b1a381dda85bbeabdd3040fb1395ca9e222e72a597f4cc76ecf6c2",
      "00001869a9216b3da6814a877fdbc31f156fc2e983b52bc68ffc6d3f3cc79af0",
      "0000198c0456230b555d5261091cf9206e75b4ad738495a60640b425ecdf408f",
      "00001a523bd1b688472c6ea5a3c87c697db64d54744829372ac808de8ec1d427"
    ],
    "Hash": "7f7d93c6235855fedc34e32c6b67253e27910ca4e3b8f2d942efcd758a6d8829"
  },
  "Time": "2m54.087909745s"
}
```

If the `Time` value is higher than 6 minutes, then the hardware specifications for the node may need to be upgraded. 

If there is an evictions related error such as the one below, try running the call to the `/rchash/{depth}/{anchor}` endpoint again:

```
error: "level"="error" "logger"="node/storageincentives" "msg"="make sample" "error"="sampler: failed creating sample: sampler stopped due to ongoing evictions"
```

While evictions are a normal part of Bee's standard operation, the event of an eviction will interrupt the sampler process.

If you are still experiencing problems, you can find more help in the [node-operators](https://discord.gg/kHRyMNpw7t) Discord channel (for your safety, do not accept advice from anyone sending a private message on Discord). 