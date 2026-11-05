# 8. Gates, kill rule, and report

The word "gate" in this file is a pass rule. It is not the routing gate in [02-system.md](02-system.md).

**Rule.** The numbers below are the numbers for the test.

## Pass rules

| Pass rule | The system passes when |
|-----------|------------------------|
| Gate 0, sanity | The system beats B1 on cost at the quality tolerance. |
| Gate 1, the required result | On one workload, all three conditions hold. The lower 95% bound of the cost saving against the best baseline is at least 20%. The aggregate quality loss is at most 1%. The false-familiar rate is at or below `α`. This result holds on both workloads, W2 and W3. |
| Gate 2, the distinction | On the familiar-hard slice, the system beats the stronger of B3 and B3b at the same cost, or matches its quality at a lower cost. The stronger one is the one with the higher label agreement with B0 on that slice at its selected threshold. |

**Explanation.** The 20% cost saving and the 1% aggregate quality loss are confirmed. [07-metrics.md](07-metrics.md) sets `α`.

**Explanation.** Gate 0 is a sanity check. B1 saves cost only on exact copies. A system that cannot beat an exact cache, at the required quality, does not answer the near-repeat question.

**Explanation.** The lower 95% bound is the bottom of the bootstrap interval from [07-metrics.md](07-metrics.md). The point estimate can be above 20% while the lower bound is below 20%. Gate 1 uses the lower bound. A noisy saving does not pass.

**Explanation.** Stage A has two workloads, so Gate 1 needs both. If Gate 1 holds on one workload only, the result is reported for that workload only, and the stated claim narrows to it. If it holds on neither, the kill rule below applies. Removing W1 before the freeze changed this count from "2 of 3".

**Explanation.** Gate 2 uses the familiar-hard slice from [06-data.md](06-data.md). B3 already sends easy inputs to a cheaper path. A win on easy inputs does not show that pass history adds information. The hard familiar inputs are the test of that difference. B3b reads the same signature as the system without a store, so a win against B3b shows that the store adds information that a trained predictor does not.

**Rule.** If Gate 2 does not pass, the report must call the contribution "another cascade." If the system beats B3 but not B3b, the report must also say that a trained competence gate matched it.

**Explanation.** A cascade uses confidence to send an input to a small model or a large model. "Another cascade" means that the system did not beat B3 on the familiar-hard slice. You still report the curves. Do not say that familiarity added a separate signal.

## Kill rule

**Rule.** If Gate 1 fails on both workloads by the decision date, publish the negative result. Then change the bet. If Gate 1 fails on one workload, publish that failure, and narrow the stated result to the workload where it holds.

**Rule.** The decision date is 12 Dec 2026.

**Explanation.** A negative result is a result. The kill rule does not let you replace the pass rule after you see the numbers. You publish the miss. You may then limit the words that you use, or you may change the next experiment. You may not say that Gate 1 passed.

## Report

**Rule.** Report every run. Include failed runs. Include baselines that you removed, with the reason.

**Rule.** Release these items with the paper:

- the code
- the configurations
- the seeds
- the model hashes
- the raw table of every run
- this protocol, with the freeze timestamp

**Rule.** After the freeze, date each amendment. List it in [amendments.md](amendments.md). Say what changed. Say why it changed.

**Explanation.** The raw table is the record before a summary. A summary can drop a failed run. The table must not drop it. The freeze timestamp shows the reader which rules were in force for that table.
