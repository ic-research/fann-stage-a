# 6. Data and splits

## Splits

**Rule.** Each workload has a fit split, a risk-control split, and a test stream. The fit split and the risk-control split together are the calibration data. W3 also has a model-training split.

| Split | Used for | Never used for |
|-------|----------|----------------|
| Model-training split (W3 only) | Fine-tuning the two W3 models. | Reported results. Any threshold, cutoff, or selection. Training B3b. |
| Fit split | Fitting the B3 temperature. Training B3b. Computing the quantiles that become threshold candidates for B2, B3, B3b, and the system. | Reported results. Selecting a threshold. |
| Risk-control split | Evaluating every threshold candidate and selecting the threshold. Selecting the best baseline. The hardness cutoff and the familiarity cutoff of the slices. The light-path check in [04-models.md](04-models.md). | Reported results. Refitting `T`. Training B3b. |
| Test stream | The reported results only. | Any fit, any selection, any cutoff. |

**Rule.** Write the split indices to a file before you inspect any model output. Reuse that file for every system.

| Workload | Model-training split | Fit split | Risk-control split | Test stream |
|----------|----------------------|-----------|--------------------|-------------|
| W3 | First 5,000 queries of the shuffled official train set | Next 2,500 queries | Last 2,503 queries | The official test set (3,080 queries) |
| W2 | None | 25% of CCT-20 bursts | 25% of CCT-20 bursts | 50% of CCT-20 bursts |

**Rule.** W3: shuffle the official train set with `numpy.random.default_rng(20261030).permutation(10003)`.

**Rule.** W2: assign whole bursts at random with seed `20261030`. A burst is the dataset's sequence id. All images of one burst go to one split.

**Explanation.** A random assignment of bursts keeps the calibration data and the test stream exchangeable, which the error bound in [07-metrics.md](07-metrics.md) assumes. A split by date would put a distribution shift between calibration and test. Whole bursts keep the near-repeats inside one burst in one split, so a test image does not have a near-twin in calibration that inflates the calibration result.

**Rule.** Do not include calibration data in the reported results.

**Explanation.** The thresholds, the temperature, and B3b see these inputs. A result on the same inputs would measure memory of the setup, not a result on new inputs.

## Test stream

**Rule.** Hold out the test stream.

**Rule.** W3 has no recorded order. Build three test orders with seeds `0`, `1`, and `2`. W2 keeps the recorded order: sort the test images by capture time, then by image id.

**Rule.** Do not use the test stream until the protocol is frozen. The code that reads the test stream must refuse to run unless the frozen configuration file exists and the model hashes match the manifest.

**Explanation.** The order matters because familiarity looks backward in the stream. A near-repeat counts only when the earlier input is already in the stream. A new shuffle after you see the results can change the repetition rate. The freeze locks the stream before you see those results.

## Calibration replay

**Rule.** To evaluate a threshold candidate for the system or for B2, replay the risk-control split as a stream, with an empty store at the start. Use the same store sizes and the same FIFO rule as the test.

**Explanation.** The risk then comes from the same store dynamics as the test, including the warm-up.

## History warm-up

**Rule.** Start the history store empty.

**Rule.** Report the cost with the warm-up included. Report the steady-state cost as a separate number. The steady state starts after the first `N` inputs of the stream, where `N` is the store size. If a store does not fill, report the cost with the warm-up included only.

**Explanation.** An empty store cannot reuse an output. The first inputs take the full route or the light route more often. A report that starts after the store is full hides that cost. A report that includes only the start hides the later saving. You need both numbers.

## Repetition-rate series

**Rule.** Build test streams at the repetition rates 10%, 30%, and the natural rate of the workload. Do not build a rate above the natural rate. Measure the rate with the slice embedding at cutoff 0.85.

**Rule.** Build a lower rate by controlled subsampling: remove near-repeats from the test stream at random until the rate reaches the target. Use the stream seed. Keep the order of the inputs that remain.

**Rule.** Report the cost saving as a curve against the repetition rate. Do not report one selected rate only.

**Explanation.** The repetition rate is the share of inputs that have a near-repeat earlier in the stream. A saving at one high rate does not show the saving at a low rate. The curve is the result.

## Slices

**Rule.** Define the four slices before the test. Use one fixed embedding that is not the gate score, not the B2 embedding, and not a model under test. Do not define a slice from any system's own score.

| Workload | Slice embedding | Revision | Licence |
|----------|-----------------|----------|---------|
| W2 | `google/siglip-base-patch16-224`, image tower, L2-normalized | `7fd15f0689c79d79e38b1c2e2e2370a7bf2761ed` | Apache-2.0 |
| W3 | `sentence-transformers/all-MiniLM-L6-v2`, mean pool, L2-normalized | `1110a243fdf4706b3f48f1d95db1a4f5529b4d41` | Apache-2.0 |

The four slices are:

- familiar-easy
- familiar-hard
- novel-easy
- novel-hard

**Rule.** Familiar means that a near-repeat appears earlier in the test stream at or above the familiarity cutoff. Freeze the familiarity cutoff on calibration data: replay the fit split and then the risk-control split as one stream, try the cutoffs 0.70, 0.80, 0.85, 0.90, and 0.95, and take the highest cutoff at which at least 15% of the risk-control split is familiar.

**Rule.** Hard means that the reference-model negative log-likelihood of the gold label is at or above the 75th percentile of those values on the risk-control split. On W2, the gold probability is the sum of the reference probabilities over the SpeciesNet labels that the mapping table in [04-models.md](04-models.md) assigns to the gold class.

**Rule.** If a slice has fewer than 30 test inputs in a seed, report it and mark it `small`. Do not drop it. Do not redraw the cutoff.

**Explanation.** The familiar-hard slice is the slice that Gate 2 uses. The input is close to an earlier input, and the reference model finds it difficult. A confidence gate tends to send difficult inputs to the full route. If the system wins only on easy inputs, it is another confidence gate. [08-gates.md](08-gates.md) gives that pass rule.

**Explanation.** You must not let the gate define "familiar." If you do, the gate agrees with itself. The slice definition comes from the stream order, an independent embedding, and the reference-model loss.
