# Words in this protocol

Use only these meanings. If a file needs a new technical name, that file defines the name once.

| Word | Meaning |
|------|---------|
| **Protocol** | The rules for the stage A test. After the freeze, the rules change only by amendment. |
| **Freeze** | The public commit of this protocol. The commit has a date and a time. |
| **Amendment** | A later change. The amendment has a date. It says what changed and why. |
| **Stage A** | The first test. The test puts a gate in front of models that do not change during the test. |
| **Reference model** | The open-weights model for one workload. Its output is the quality reference. Its cost is the cost reference. |
| **Open weights** | You can download the model weights. |
| **Light path** | The lower-cost model for one workload, for the same task. It comes from the reference model, or it is a smaller distilled, quantized, or pruned model for the same task. |
| **Gate** | The part that reads the history and selects the route. |
| **Route** | The path for one input. The routes are the full route, the light route, and the reuse route. |
| **Full route** | The reference model runs on the input. |
| **Light route** | The light path runs on the input. |
| **Reuse route** | The system returns a stored output from an earlier input. |
| **History store** | The record of earlier inputs for one test stream. |
| **Signature** | The short record of one input. The gate uses the signature to find similar inputs. |
| **Familiarity score** | The distance from a new signature to the nearest signatures in the history store. |
| **Distance `d`** | The familiarity score for one input. A small `d` means that the input is near an earlier input. |
| **Threshold** | A distance limit. The policy compares `d` with the thresholds. |
| **Policy** | The rule that maps `d` to a route. |
| **Workload** | One public dataset plus one task. Stage A has two workloads, W2 and W3. |
| **Near-repeat** | An input that is close to an earlier input in the same test stream. The two inputs are not the same input. |
| **Natural near-repeat rate** | The share of near-repeats in the dataset before you change the dataset. |
| **Test stream** | The ordered inputs that you use for the reported result. You hold this stream out. |
| **Calibration data** | The fit split and the risk-control split together. You do not report results on this data. |
| **Model-training split** | W3 only. The inputs that you use to fine-tune the two W3 models. They are not calibration data. |
| **Model-training split** | W3 only. The inputs that you use to fine-tune the two W3 models. They are not calibration data. |
| **Fit split** | The calibration inputs that you use to fit the temperature, train B3b, and generate threshold candidates. |
| **Risk-control split** | The calibration inputs that you use to select thresholds, select the best baseline, and set the slice cutoffs. |
| **Competence gate** | B3b. A trained network that predicts whether the light path will give the B0 output. It keeps no store of earlier inputs. |
| **Baseline** | A comparison system. You run each baseline on the same hardware, the same software, and the same test stream. |
| **Best baseline** | For one workload, the lowest-cost baseline that meets the quality tolerance on the risk-control split. |
| **Quality tolerance** | The largest permitted difference from the required task output. On W2 and W3 the output must be the B0 label. |
| **Slice** | One of four groups of inputs. You define the groups before the test. |
| **False-familiar rate** | The share of inputs on the light route or the reuse route whose output is outside the quality tolerance. The tolerance is the difference from the full route. |
| **Coverage** | The share of all inputs on the light route or the reuse route. |
| **Cost** | The wall-clock time and the dollars for one request. The cost includes the gate. |
| **Cost saving** | The decrease in cost against the best baseline. |
| **Seed** | A number that makes one random step repeatable. |
| **Tier** | A compute level. Tier 1 is offline simulation. Tier 2 is measurement on a real serving stack. |
| **Open item** | A value that is still empty. You must write it before the freeze. |

**Rule.** Do not use "familiar" for difficulty. Familiar means that a near-repeat is earlier in the test stream.

**Rule.** Do not use "freeze" for the reference model. Use "freeze" for the protocol only.
