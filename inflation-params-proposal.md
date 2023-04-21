# Inflation Params Proposal

We do this via chain upgrade on live network

to view current params - `./cvnd q inflation params`

first we run the following command and select evmos.inflation.v1.MsgUpdateParams

`./cvnd tx gov draft-proposal`

```json
{
 "messages": [
  {
   "@type": "/evmos.inflation.v1.MsgUpdateParams",
   "authority": "cvn10d07y265gmmuvt4z0w9aw880jnsr700jc4lesp",
   "params": {
    "mint_denom": "acvnt",
    "exponential_calculation": {
     "a": "341000000.000000000000000000",
     "r": "0.531000000000000000",
     "c": "9393000.000000000000000000",
     "bonding_target": "0.667700000000000000",
     "max_variance": "0.000000000000000000"
    },
    "inflation_distribution": {
     "staking_rewards": "0.522333346000000000",
     "usage_incentives": "0.333333321000000000",
     "community_pool": "0.144333333000000000"
    },
    "enable_inflation": true
   }
  }
 ],
 "metadata": "ipfs://CID",
 "deposit": "1000000000acvnt"
}
```

then we modify the `draft_proposal.json` file generated to have the appropriate inflation parameters. it should look like this

note: sum of params in `inflation_distribution` must sum up to 1.

`./cvnd tx gov **submit-proposal** draft_proposal.json --from key1 --chain-id cvn_8008-1 --gas-prices 135319343acvnt`