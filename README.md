# FANN stage A

The pre-registered protocol for the first test of FANN (Familiarity-Aware Neural Networks), from [I/C Research](https://www.intelligencepercompute.com/).

**Status: frozen on 5 Nov 2026. There are no results yet.** FANN is a hypothesis, not a finding.

## The test

Stage A puts a familiarity gate in front of existing models. The gate reads the model's own history of past passes. It then sends each input to the full model, a lighter model, or an earlier answer.

The test asks whether that costs less than caches, confidence gates, and a trained competence gate, with the rate of wrong familiar decisions held to a limit set in advance. If the test fails, we publish the failure.

## Read the protocol

Start at [protocol/README.md](protocol/README.md). It gives the reading order.

- The pass rules and the kill rule are in [protocol/08-gates.md](protocol/08-gates.md).
- The values that are still to be filled are in [protocol/10-open-items.md](protocol/10-open-items.md).
- Every change after the freeze is a dated amendment in [protocol/amendments.md](protocol/amendments.md).

The freeze timestamp is the date and time of the commit that froze the protocol.

## Licence

The text of this repository is licensed under [CC BY 4.0](LICENSE). Code added later will be licensed under Apache-2.0.
