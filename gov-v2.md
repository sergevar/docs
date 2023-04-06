## Proposal Types:
- Param updates: updates chain params like inflation rates
- Chain upgrades: updates node software
- Community Spend: send CVNT from CommunityPool(common pool for CVN controlled only by governance proposal) for all different incentives

## Proposal Flow:
Proposals reach quorum only when >50% are voted to Approve excluding users who Abstained
<img width="379" alt="image" src="https://user-images.githubusercontent.com/130031333/230348247-fdcbd902-deea-47f7-9d16-249840ca128d.png">


## Proposal Status:
- Init (I)
  - Proposal is submitted
- CVNT Period (CP)
  - MinDeposit is reached
  - $CVNT holders can vote to Approve or Reject
- soulT Period (SP)
  - $CVNT holders passed quorum to Approve
  - $soulT holders can object
  - If no objections from the $soulT holders, the $CVNT Proposal is executed
- Admin Period (AP)
  - $soulT holder has objected to the $CVNT Proposal
  - CVN Admin can Approve or Reject
- Rejected (R)
  - Proposal has been rejected
- Passed (P)
  - Proposal passed and successfully executed
- Failed (F)
  - Proposal passed but failed execution
