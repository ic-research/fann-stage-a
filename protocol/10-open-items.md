# 10. Open items before the freeze

**Rule.** Write every item in this file before the freeze, except the items under "Filled after the freeze".

**Explanation.** The freeze date is 5 Nov 2026. This list was updated on 5 Nov 2026. W2 and W3 are selected, with their rules and their fine-tuning or reference choices. The values that need a model run are listed under "Filled after the freeze".

## Workloads

- [x] Write the selected workloads. W2: Caltech Camera Traps, CCT-20. W3: BANKING77 ([03-workloads.md](03-workloads.md)).
- [x] Write each rejection, with the reason. W1 is not in stage A. UA-DETRAC was not selected because its CC BY-NC-SA 3.0 licence forbids commercial use ([03-workloads.md](03-workloads.md)).
- [x] Write the licence and the size of each dataset ([03-workloads.md](03-workloads.md)).
- [x] Write the task and tolerance for each workload. Both are classification, with the B0 label as the tolerance. `ε` is not needed.
- [x] Write the near-repeat rate floor and the method that measures the rate. 30% at cutoff 0.85 of an independent embedding, on a stream of test-stream length ([03-workloads.md](03-workloads.md)).
- [x] Write the measured natural near-repeat rate for W3. 43.4% at cutoff 0.85 over 3,080 queries (train split only).

## Models

- [x] W3 reference model and light path: base models, revisions, and the fine-tuning recipe ([04-models.md](04-models.md)).
- [x] W3 quality bar: accuracy against the gold label of at least 0.85 on the risk-control split.
- [x] W2 reference model: SpeciesNet v4.0.3b ([04-models.md](04-models.md)).
- [x] Write the removal rule for the history store. FIFO ([02-system.md](02-system.md), v0.2).
- [x] Write the signature and its dimension. Native dimension, no reduction ([04-models.md](04-models.md)).

## Baselines

- [x] Write the baseline set. B0, B1, B2, B3, B3b, B4. B5 removed at the freeze, with the reason ([05-baselines.md](05-baselines.md)).
- [x] Write B3b: input, network, training target, training split, and threshold candidates ([05-baselines.md](05-baselines.md)).
- [x] Write the B2 embedding models ([05-baselines.md](05-baselines.md)).
- [x] Write Gate 2 against the stronger of B3 and B3b ([08-gates.md](08-gates.md)).
- [x] The ladder of gates. Stage A tests rungs 1 (B3), 2 (B3b), and 4 (the system). Rung 3, prototypes or compressed memory, is not in stage A.

## Data, error control, and series

- [x] Write every split and its use ([06-data.md](06-data.md)).
- [x] Write the slice embeddings and the slice cutoff procedure ([06-data.md](06-data.md)).
- [x] Write the error-control family and method ([07-metrics.md](07-metrics.md)). One family per workload and system. Bonferroni for one threshold. Fixed-sequence testing inside each cell for the system.
- [x] Write the gate depths `k`. 25%, 50%, and 75% of network depth ([02-system.md](02-system.md), [04-models.md](04-models.md)).
- [x] Write the store sizes. W2: 1,000; 10,000; all records up to 100,000. W3: 1,000; all records ([02-system.md](02-system.md)).
- [x] Write the repetition rates. 10%, 30%, and the natural rate ([06-data.md](06-data.md)).
- [x] Write the second batch size. 32 ([07-metrics.md](07-metrics.md)).
- [x] Write the seeds. `0`, `1`, `2` for streams and subsampling. `20261030` for splits, B3b training, and the bootstrap ([06-data.md](06-data.md), [07-metrics.md](07-metrics.md)).

## Error caps and dates

- [x] Confirm or change `α`. 1% of routed inputs ([07-metrics.md](07-metrics.md)).
- [x] Confirm or change the aggregate quality-loss cap. 1%.
- [x] Confirm or change the cost-saving bar. A lower 95% bound of 20%.
- [x] Confirm or change the decision date. 12 Dec 2026.

## Filled after the freeze

**Rule.** [README.md](README.md) gives the rule for these items: dated amendments, calibration data only, before any code reads the test stream of that workload.

**W3**
- [ ] Fine-tune the two models. Write the weight hashes.
- [ ] Measure the reference accuracy on the risk-control split against the 0.85 bar.
- [ ] Run the always-light check in [04-models.md](04-models.md).

**W2**
- [ ] Write the measured natural near-repeat rate.
- [ ] Write the reference weight hash.
- [ ] Write the light path: method, settings, and hash.
- [ ] Write the quality bar and the label-mapping table from SpeciesNet labels to CCT-20 classes.
- [ ] Write the block indices for the three gate depths.
- [ ] Run the always-light check in [04-models.md](04-models.md).

**Serving measurement (write before experiment E5)**
- [ ] Write the GPU type.
- [ ] Write the price for one hour on that GPU.

## Release

- [x] Create the public repository in the GitHub organization.
- [x] Commit this protocol to that repository at the freeze.

**Explanation.** The repository is `fann-stage-a` in the `ic-research` GitHub organization. The rule is that the repository is public and that the freeze commit contains this protocol.

## How to close an item

Write the value in the file that owns the rule. Then check the box here.
