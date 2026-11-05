# 9. Compute tiers

**Rule.** Run stage A in two tiers.

| Tier | What you run | Where |
|------|--------------|-------|
| Tier 1, now | Select the workloads. Measure the near-repeat rates. Fine-tune the two W3 models. Cache model outputs and signatures. Train B3b. Run every baseline and the system by offline simulation. | Your machine, and short sessions on low-cost rented GPUs. |
| Tier 2, real serving | Measure wall-clock time on a real serving stack, at the real batch sizes. Build the W2 light path if it needs a GPU. Run the full series of gate depths, store sizes, and repetition rates. | Rented GPUs. |

**Explanation.** Tier 1 can answer the signal question and the error question from cached outputs. Tier 1 cannot answer the serving-cost question. Q2 needs tier 2, because only tier 2 measures time and dollars on a serving stack.

**Explanation.** Tier 1 reports a simulated cost from measured per-part latencies. It is not the result that you use for Gate 1. The Gate 1 cost decision uses the measured time and dollars from tier 2. [07-metrics.md](07-metrics.md) gives that rule.

**Rule.** Do not shorten the freeze rule or the report rules to save time. If you cut a workload or a baseline after the freeze, write the cut as an amendment.
