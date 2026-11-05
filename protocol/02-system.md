# 2. System under test

**Rule.** The system is a gate in front of a reference model. The gate sends each input to one route.

| Route | What the system does |
|-------|----------------------|
| Full route | The reference model runs. |
| Light route | The light path runs. [04-models.md](04-models.md) gives the light-path rule. |
| Reuse route | The system returns the stored output of an earlier matching input. |

**Explanation.** The full route is the slow route. It is also the quality reference. The light route does less work, so its output can differ. The reuse route does the least work. It does no new model pass. It can be wrong when the new input is not a true match.

**Explanation.** The light path is a tool. The result of the test is how the gate uses the light path. The result is not the light path itself. [04-models.md](04-models.md) gives the model rules.

## Parts of the gate

The gate has three parts.

### History store

**Rule (v0.2).** For each test stream, the history store keeps three records only for past inputs processed by the full reference route:

- the signature of the input
- the route that the gate selected
- the output

**Rule (v0.2).** Light-route inputs do not enter the history store. Thus every stored signature has a full-reference output, and an earlier approximation can never become the source of a later reuse result.

**Rule (v0.2).** Reuse is enabled only for workload/task pairs where the output is defined to be reusable under the frozen workload specification. For W1, reuse is exact-duplicate only; near-duplicate documents must not reuse because changed field values may carry the answer. [03-workloads.md](03-workloads.md) gives the reuse eligibility rules for W2 and W3. A failed eligibility check routes to light or full according to the calibrated policy; it never returns a cached output.

**Rule.** W2: sweep store sizes `N ∈ {1,000, 10,000, all records up to 100,000}`. W3: sweep store sizes `N ∈ {1,000, all records}`. Use FIFO eviction when a store is full. Report all sizes; do not choose a size using test results.

**Explanation.** The W3 test stream has 3,080 inputs, so a W3 store cannot reach 10,000 records.

**Explanation.** FIFO removes the oldest record when the store is full. The same removal rule applies throughout each run.

### Familiarity score

**Rule (v0.2).** The signature is the pooled activation at gate depth `k`, L2-normalized. The familiarity distance is cosine distance, `d = 1 - max_j cosine(s(x), s_j)`, over eligible historical signatures. Smaller `d` means more familiar. Search is exact nearest-neighbour search.

**Rule (v0.2).** Test `k ∈ {25%, 50%, 75%}` of network depth, using the corresponding block index for non-transformers. Report every point. Do not choose `k` from test results.

**Explanation.** The gate depth `k` is the layer where you read the signature. Compute that you spend before the decision is compute that you cannot save. A late `k` can make a good decision and still save little cost. An early `k` can save more cost and can make a worse decision. You must report the curve. Do not report only the best `k`.

### Policy

**Rule.** Set the thresholds before the test. Take the thresholds from the error-control procedure in [07-metrics.md](07-metrics.md).

**Rule (v0.2).** Apply the thresholds only after checking workload-specific reuse eligibility. For an eligible input, `d < τ_reuse` selects reuse, `τ_reuse ≤ d < τ_light` selects light, and `d ≥ τ_light` selects full. For an ineligible input, skip reuse and use the light/full boundary `τ_light`.

**Explanation.** `τ_reuse` and `τ_light` are the two thresholds. A smaller distance means a closer match. The closest matches can use the reuse route. The middle matches use the light route. The far matches use the full route.

**Explanation.** Early-exit systems show the same trade-off between latency and accuracy. A looser threshold saves more compute and makes more errors. A stricter threshold keeps accuracy and saves less. This is why the thresholds come from the calibration data, and why the cost and error curves are reported in full.

**Rule.** Test each store size in the set `N` above. Report every point.

**Explanation.** A larger store can find older matches. A larger store also costs more to keep and to search. The reported point must include that cost. [07-metrics.md](07-metrics.md) requires the gate cost in the request cost.

**Explanation.** Exact search means that the gate has no index error. An approximate index would add its own errors. Stage A does not use an approximate index.

**Explanation.** [04-models.md](04-models.md) gives the signature for each workload. The signature keeps the native dimension of the model. It is not reduced.
