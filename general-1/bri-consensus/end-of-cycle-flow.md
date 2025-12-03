---
description: >-
  This is a description of the End-of-Cycle flow that completes the Cycle and
  handles rewards and enforces the consensus
---

# End-of-Cycle Flow

1. BlockReward.reward is called every block and when the cycle ends calls the Consensus.cycle
2. Consensus.cycle is responsible for several functions. Eventually does the following two actions:
   1. Sets the boolean Consensus.ShouldEmitInitiateChange to true
   2. Calls BlockReward.onCycleEnd which sets the boolean BlockReward.shouldEmitRewardedOnCycle to true as well
3. The bri-validator-app (briapp container on validator vms) checks the value of the above two booleans and when true calls the following two functions (two separate transactions):
   1. Consensus.emitInitiateChange
   2. BlockReward.emitRewardedOnCycle

Note that only one validator can make this call successfully so the 1st one succeeds and all others fail. But it’s ok because those are 0-gas transactions. So actually the validator who is the next one to validate a block is the one who is successful in making those calls.

1. The above calls emit the following events:
   1. Consensus.InitiateChange
   2. BlockReward.RewardedOnCycle
2. The bridge oracles brioracle-initiate-change and brioracle-rewarded-on-cycle are responsible for listening to above events.
3. Those oracles should run on all validators and call submitSignature on the HomeBridgeNativeToErc contract in bri network - each validator should submit the signature for each of the oracles (events).
4. Once enough validators have submitted their signatures (majority of the current validators), an event CollectedSignatures is emitted by the home bridge (again one event for each one of the previous events in section 4).
5. The bridge oracle brioracle-collected-sigantures is responsible for listening to the CollectedSignature events and all validators should get it. The validator responsible for transmitting the transaction to mainnet is actually the last one to submit the signature in section 7 and its address is part of the event details so other validator oracles “know” it’s not their turn and skip the event. If a validator is down or out of money or infura is deaf - the next one in line (in the ValidatorSet) is responsible for transmitting to mainnet.
6. Eventually on mainnet we are supposed to see two transactions to the ForeignBridgeNativeToErc each cycle - one updating the new validators and one minting the bri tokens which were created during this cycle on bri.

Note that if the new validator set transactions fail on mainnet there’s a chance the minting will fails as well, because before transmitting it checks if all signatures are valid and there can be a situation where new validators were added on a cycle and were fast enough to submit their signatures on bri end-of-cycle transactions but weren’t updated on mainnet due to failure of the 1st transactions so the 2nd one will actually contain “invalid” signatures from the mainnet perspective.

Example for a successful flow (from 06/11/2025)

1. Consensus.emitInitiateChange transaction on bri - [https://briscan.io/tx/0xea75448cf8f6c45da60d78d85a8a733b3192eaffa1ace7dc171fc2fea0df64bf](https://briscan.io/tx/0xea75448cf8f6c45da60d78d85a8a733b3192eaffa1ace7dc171fc2fea0df64bf)
2. BlockReward.emitRewardedOnCycle transaction on bri - [https://briscan.io/tx/0x7866bf65f303adefbc82403bf31c01c37d3540d8335969d09e053afe820d7393](https://briscan.io/tx/0x7866bf65f303adefbc82403bf31c01c37d3540d8335969d09e053afe820d7393)
