# 1. Question

**Rule.** The test answers two questions.

Does a gate that reads pass history make the cost lower than a cache or a confidence gate? The comparison is on near-repeat workloads.

Does that gate keep out inputs that are not safe to treat as familiar?

**Explanation.** Pass history is the record of earlier passes through the model. The gate does not ask whether the input is easy. The gate asks whether an earlier input is close to this input.

**Explanation.** A cache can return a stored output for the same input. A cache can also return a stored output for a similar input. A confidence gate can stop early. A confidence gate can also send an easy input to a smaller model. The test asks whether pass history adds a saving that those methods do not already give.

**Explanation.** The failure that matters is a wrong familiar decision. The input looked known. The output was not the output of the full route. The test must count that failure. The name of that count is the false-familiar rate. [07-metrics.md](07-metrics.md) defines it.

## The research questions

The research has four questions. This protocol answers three of them.

| Question | What the test must show | Where |
|----------|-------------------------|-------|
| Q1, signal | Pass history separates routes better than confidence, above all on the familiar-hard slice. | Gates 1 and 2 |
| Q2, serving cost | The saving remains when you measure time and dollars on a real serving stack. The gate cost is included. | Cost in [07-metrics.md](07-metrics.md), tier 2 in [09-compute.md](09-compute.md) |
| Q3, bounded error | The false-familiar rate stays at or below a cap that you set before the first run. | Error control in [07-metrics.md](07-metrics.md) |

**Rule.** This protocol does not answer Q4. Q4 asks whether the same behaviour can be trained into a model. That question is later than stage A.

**Explanation.** Stage A does not change a model during the test. W2 uses its reference model as published. W3 fine-tunes its two models once, before calibration, as [04-models.md](04-models.md) says. A pass on stage A does not show that the model learned the behaviour. It shows that a gate in front of the model can select the route.

**Explanation.** Dynamic networks can be sorted by what they change: depth, width, input coverage, parameters, or topology. The gate changes the route for each input. The nearest family for the full and light routes is early exit, which B3 represents. The nearest family for the reuse route is dynamic computation that skips parts of the input that have not changed. The training-based families, such as mixture-of-experts, adaptive computation time, and learned block skipping, change the trained network. They belong to Q4, not to stage A.
