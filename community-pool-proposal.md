# Community Pool Proposals

`./cvnd q distribution community-pool` - balance of pool

`./cvnd tx gov draft-proposal` - to draft a proposal

`./cvnd tx distribution fund-community-pool 1000acvnt --from key1 --chain-id cvn_8008-1` for giving the community pool some cash

`./cvnd q gov votes <proposal_id>`This gives the votes on a proposal at the moment

`./cvnd tx gov submit-proposal new_prop.json --from key1 --chain-id evmos_1100-1` to submit a proposal. this should also send the deposit automatically

`./cvnd tx gov vote <vote-id> yes --from key1 --chain-id cvn_8008-1 --gas-prices 7acvnt` To vote on a proposal

`./cvnd q gov proposals` to see all proposals

`./cvnd q gov votes 1`to see the votes on a proposal

json for proposal to send some coins to an address - 

```json
{
"title": "Governance Proposal to Fund Rewards for Canto Online Hackathon Chapter 1, Season 4",
"description": "If successful, this proposal will send 260,000 Canto to the COH rewards distribution contract in order to pay the winners of COH Chapter 1, Season 4.",
"recipient": "cvn1yjnzmrrr5cqr5a4xe6wscl2mvw56wj8tptws4l",
"amount": "351acvnt",
"deposit": "1000000000acvnt"
}
```

Use this with the following command to submit a community spend proposal

`./cvnd tx gov submit-legacy-proposal community-pool-spend ./draft_proposal.json --from key1 --chain-id cvn_8008-1 --gas-prices 7acvnt`