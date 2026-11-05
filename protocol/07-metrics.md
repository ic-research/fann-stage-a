# 7. Metrics

Report each metric for the test stream. Do not report these metrics for the calibration data.

## Quality

**Rule.** Report the task metric for each slice and for the full test stream. Compare the result with B0.

**Explanation.** B0 is the reference model on every input. Quality is the difference from B0, not a score with no reference. On W2 and W3 the task metric is label agreement with B0: the share of inputs whose returned label is the B0 label. The aggregate quality loss is 1 minus that agreement. Accuracy against the gold label is a secondary metric, reported for each system and for B0.

## False-familiar rate

**Rule.** The false-familiar rate is the primary error metric.

**Rule.** Count inputs that the gate sent to the light route or the reuse route. The false-familiar rate is the share of those inputs whose output differs from B0 by more than the task tolerance.

**Rule.** Report the rate for the full test stream and for each slice. Report the coverage with it: the share of all inputs that the gate sent to the light route or the reuse route.

**Rule.** Use the same definition for every system that can route an input away from the full route: B2, B3, B3b, B4, and the system.

**Rule.** Overall accuracy is a secondary metric.

**Explanation.** Overall accuracy can stay high when the gate is wrong on the inputs that carry the new information. A query that looks like an earlier query and needs a different intent label can hide inside a high accuracy number. The false-familiar rate counts that input. It does not count inputs that the gate sent to the full route, because those inputs did not receive a familiar treatment.

## Cost

**Rule.** Cost is the wall-clock time for one request and the dollars for one request. Include the gate. The gate cost includes the signature, the lookup, and the store upkeep.

**Rule.** Charge the gate cost on every input, including the inputs that go to the full route. Charge the reference prefix up to depth `k` on every input that the system or B3b routes. The full route continues the reference model from depth `k`. It does not run the prefix again.

**Rule.** Charge each baseline the work it does: B2 pays its embedding model and its lookup on every input; B3 pays the light path on every input and the reference on each deferred input; B3b pays as [05-baselines.md](05-baselines.md) says.

**Rule.** Gate 1 uses the cost measured on a real serving stack (experiment E5), at batch size 1 and at batch size 32. Use GPU type `[GPU type]`.

**Rule.** Before E5, report a simulated cost. Measure the wall-clock median of each part at batch size 1 on calibration inputs, after a 10-input warm-up that is not timed: the reference model, the light path, the reference prefix to each depth `k`, the rest of the reference from each `k`, the lookup at each store size, the B2 embedding, and the B3b network. Sum the parts by route. Label every such number "simulated cost". A simulated cost does not decide Gate 1.

**Open item.** Write the GPU type and the price for one hour on it. [10-open-items.md](10-open-items.md) lists these under "Filled after the freeze".

**Rule.** You may list FLOPs next to the time and the dollars. The pass decision uses the time and the dollars. The pass decision does not use FLOPs.

**Explanation.** FLOPs do not include the gate, the memory traffic, or the batch behaviour. A method can reduce FLOPs and increase wall-clock time. Q2 asks for the serving cost, so the decision uses time and dollars.

## Error control

**Rule.** Set the target false-familiar rate to `α = 1%` of routed inputs.

**Rule.** The error-control family is one workload and one system. Each system gets its own guarantee on each workload, at 95% confidence over all the candidates that its selection considers.

**Rule.** Declare every candidate before calibration, from the fit split only. Evaluate the candidates on the risk-control split. For each candidate, `n` is the number of routed inputs and `e` is the number of false-familiar inputs among them. The bound is the one-sided Clopper–Pearson upper bound on `e` in `n`. A candidate with `n = 0` routes nothing, has a saving of zero, and is always eligible. Among the eligible candidates, select the one with the greatest simulated cost saving.

**Rule.** B2, B3, and B3b each have one threshold for each configuration. Use Bonferroni over the declared list.

| System | Declared candidates | Per-candidate significance |
|--------|---------------------|----------------------------|
| B2, W2 | Cosine thresholds 0.85, 0.90, 0.93, 0.95, 0.97, 0.98, 0.99, and "never reuse", at each of 3 store sizes: 24 candidates | `0.05 / 24` |
| B2, W3 | The same 8 candidates, at each of 2 store sizes: 16 candidates | `0.05 / 16` |
| B3 | The nine candidates in [05-baselines.md](05-baselines.md) | `0.05 / 9` |
| B3b | The nine candidates in [05-baselines.md](05-baselines.md), at each of 3 depths: 27 candidates | `0.05 / 27` |

**Rule.** The system has two thresholds. Use fixed-sequence testing (Learn then Test) inside each cell of depth and store size, and Bonferroni across the cells. W2 has 9 cells, so use significance `0.05 / 9`. W3 has 6 cells, so use significance `0.05 / 6`. In each cell, test the 12 pairs below in this order. Stop at the first pair whose bound exceeds `α`. The pairs before it are eligible.

| Order | `τ_reuse` quantile | `τ_light` quantile |
|-------|--------------------|--------------------|
| 1 | 0 (no reuse) | 0.05 |
| 2 | 0 | 0.10 |
| 3 | 0 | 0.20 |
| 4 | 0.01 | 0.20 |
| 5 | 0.01 | 0.30 |
| 6 | 0.02 | 0.30 |
| 7 | 0.02 | 0.40 |
| 8 | 0.05 | 0.40 |
| 9 | 0.05 | 0.50 |
| 10 | 0.05 | 0.60 |
| 11 | 0.10 | 0.60 |
| 12 | 0.10 | 0.70 |

The quantiles are quantiles of the familiarity distance `d` on a replay of the fit split, for that cell. Compute them once, then freeze them.

**Explanation.** Each step of the sequence makes both thresholds looser or keeps one fixed, so the risk tends to grow along the sequence. Fixed-sequence testing then needs no correction inside the cell, and the guarantee holds even if the risk is not exactly monotone. Bonferroni across the cells pays for the choice of depth and store size. A correction across workloads is not needed, because each workload reports its own result.

**Rule.** If no candidate of a system routes inputs within the bound, select "route nothing" for that system and workload. Report that the calibration sample did not support a safe threshold. Do not relax `α`, merge the two calibration splits, or tune on the test stream.

**Explanation.** With zero observed errors, a 1% upper bound needs about 480 routed calibration inputs at significance `0.05 / 6`, about 520 at `0.05 / 9`, and about 630 at `0.05 / 27`. W3 has 2,503 risk-control queries, so each system must route a fair share of them with very few errors. A low error count is not enough when the routed sample is small. Reporting the conservative fallback is part of the result.

**Rule.** On the test stream, report the achieved false-familiar rate with a 95% bootstrap interval.

**Rule.** The guarantee assumes that the calibration data and the test data are exchangeable. If the test stream drifts, say that in the report. The assumption then does not hold.

**Explanation.** The system's routing depends on its store, so the routed inputs of one stream are not independent of each other. The risk-control replay measures the risk of the same store process, but the bound treats that replay as one sample of it. The report states this limit next to every bound for the system and for B2.

**Explanation.** Conformal risk control and Learn then Test are procedures that set a threshold from calibration data. They give a bound on a risk when the calibration inputs and the test inputs come from the same distribution. Exchangeable means that the joint distribution does not depend on order. Drift means that later inputs come from a different distribution. The bound then does not apply. You do not hide that break. You write it in the report.

**Explanation.** A bootstrap interval resamples the test results many times. The 95% interval is the interval that covers 95% of those resamples. Report the interval. Do not report the point value only.

## Statistics

**Rule.** Use 3 seeds: `0`, `1`, and `2`. On W3 a seed sets the test-stream order and the subsampling. On W2 a seed sets the subsampling, because the order is the recorded order. B3b training uses the fixed seed `20261030`. Report each seed and the aggregate. Do not treat the three seeds as independent samples.

**Rule.** Put a 95% bootstrap interval on every cost saving and on every rate. Use 1,000 resamples over test inputs, with seed `20261030`.

