# NL2AGBench: Benchmarking LLM Auto-Formalization for AlphaGeometry

![Translation gap between natural-language geometry problems and AlphaGeometry's formal DSL](README/graphic_translation_difficulty.png)

*Figure 1: The translation gap between natural-language geometry problems and AlphaGeometry's formal DSL. While AlphaGeometry has demonstrated strong theorem-proving capabilities, the manual formalization step remains a major barrier to usability and large-scale deployment.*

## Benchmark Construction

NL2AGBench is built from geometry problems originally formalized for AlphaGeometry:

- **Source**: The Java Geometry Expert (JGEX) repository, from which the AlphaGeometry project manually formalized 231 problems into its DSL.
- **Selection**: 48 representative problems were selected for translation diversity (circles, cyclic quadrilaterals, angle bisectors, orthocenters, circumcenters, midpoints, reflections, perpendicular constructions) and execution efficiency (excluding problems with excessive AlphaGeometry runtime).
- **Format**: Every problem pairs a natural-language statement with a verified, executable AlphaGeometry translation.

![Sample NL2AGBench problems](README/images_benchmark_samples.png)

*Figure 2: Sample NL2AGBench problems spanning a range of geometric concepts, selected for both diversity and runtime efficiency.*

## Evaluation Method

Translation quality is judged with **execution-based evaluation** rather than string/textual similarity, since two syntactically different DSL outputs can be semantically equivalent. Each generated translation is run directly inside AlphaGeometry and the outcome is bucketed into one of three classes:

- **Successful Translation** — the specification parses and executes correctly.
- **Syntax Error** — the DSL violates AlphaGeometry's grammar or construction rules (further split into `AssertionError`, `KeyError`, and `ValueError` cases in the error taxonomy).
- **Logic Error** — the specification is syntactically valid but semantically inconsistent with the source problem (e.g. wrong predicate, omitted constraint, misread geometric relationship). Runs that hang without output for 5+ minutes are also classified as logic errors.

We evaluate **10 state-of-the-art LLMs** (closed- and open-source, spanning multiple parameter scales) using this framework.

### Table 1. Zero-Shot Model Performance

Success counts and rates are shown with **95% Wilson confidence intervals**.

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Zero-shot success</th><th>95% Wilson CI</th></tr>
      <tr><td>GPT-5.4</td><td>36/48 (75.0%)</td><td>[61.2%, 85.1%]</td></tr>
      <tr><td>Gemini 3.1 Pro</td><td>30/48 (62.5%)</td><td>[48.4%, 74.8%]</td></tr>
      <tr><td>Grok 4.3</td><td>25/48 (52.1%)</td><td>[38.3%, 65.5%]</td></tr>
      <tr><td>Claude Sonnet 4.6</td><td>18/48 (37.5%)</td><td>[25.2%, 51.6%]</td></tr>
      <tr><td>GPT-4o mini</td><td>2/48 (4.2%)</td><td>[1.2%, 14.0%]</td></tr>
    </table>
    </td>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Zero-shot success</th><th>95% Wilson CI</th></tr>
      <tr><td>Qwen3:235B</td><td>6/48 (12.5%)</td><td>[5.9%, 24.7%]</td></tr>
      <tr><td>Llama3.1:70B</td><td>0/48 (0.0%)</td><td>[0.0%, 7.4%]</td></tr>
      <tr><td>Llama3.2:3B</td><td>0/48 (0.0%)</td><td>[0.0%, 7.4%]</td></tr>
      <tr><td>Mistral:7B</td><td>0/48 (0.0%)</td><td>[0.0%, 7.4%]</td></tr>
      <tr><td>Nemotron-3 Nano:4B</td><td>0/48 (0.0%)</td><td>[0.0%, 7.4%]</td></tr>
    </table>
    </td>
  </tr>
</table>

## Improving Translation Performance

Three mitigation strategies were tested on top of the zero-shot baseline: few-shot prompting, human-guided hints, and supervised fine-tuning.

### Few-Shot Prompting

Each model was given 54 reference examples pairing natural-language problems with verified AlphaGeometry translations, exposing it to common constructions, DSL syntax patterns, and proof predicates. This intervention requires no retraining and increased the observed success rate for seven of the ten models. Qwen3:235B showed the largest absolute gain, at 33.3 percentage points.

### Table 2. Zero-Shot vs. Few-Shot Performance

Each result shows **successes/48 (success rate)**, followed by its **95% Wilson confidence interval**. Changes are expressed in **percentage points (pp)**.

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Zero-shot</th><th>Few-shot</th><th>Change (pp)</th></tr>
      <tr><td>GPT-5.4</td><td>36/48 (75.0%)<br><sub>[61.2%, 85.1%]</sub></td><td>38/48 (79.2%)<br><sub>[65.7%, 88.3%]</sub></td><td>+4.2</td></tr>
      <tr><td>Gemini 3.1 Pro</td><td>30/48 (62.5%)<br><sub>[48.4%, 74.8%]</sub></td><td>40/48 (83.3%)<br><sub>[70.4%, 91.3%]</sub></td><td>+20.8</td></tr>
      <tr><td>Grok 4.3</td><td>25/48 (52.1%)<br><sub>[38.3%, 65.5%]</sub></td><td>30/48 (62.5%)<br><sub>[48.4%, 74.8%]</sub></td><td>+10.4</td></tr>
      <tr><td>Claude Sonnet 4.6</td><td>18/48 (37.5%)<br><sub>[25.2%, 51.6%]</sub></td><td>25/48 (52.1%)<br><sub>[38.3%, 65.5%]</sub></td><td>+14.6</td></tr>
      <tr><td>GPT-4o mini</td><td>2/48 (4.2%)<br><sub>[1.2%, 14.0%]</sub></td><td>6/48 (12.5%)<br><sub>[5.9%, 24.7%]</sub></td><td>+8.3</td></tr>
    </table>
    </td>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Zero-shot</th><th>Few-shot</th><th>Change (pp)</th></tr>
      <tr><td>Qwen3:235B</td><td>6/48 (12.5%)<br><sub>[5.9%, 24.7%]</sub></td><td>22/48 (45.8%)<br><sub>[32.6%, 59.7%]</sub></td><td>+33.3</td></tr>
      <tr><td>Llama3.1:70B</td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>11/48 (22.9%)<br><sub>[13.3%, 36.5%]</sub></td><td>+22.9</td></tr>
      <tr><td>Llama3.2:3B</td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>+0.0</td></tr>
      <tr><td>Mistral:7B</td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>+0.0</td></tr>
      <tr><td>Nemotron-3 Nano:4B</td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>0/48 (0.0%)<br><sub>[0.0%, 7.4%]</sub></td><td>+0.0</td></tr>
    </table>
    </td>
  </tr>
</table>

Wilson intervals describe uncertainty around each individual success rate; they are not confidence intervals for the improvement. Because the 48 problems were manually curated, these intervals should be interpreted under a binomial model rather than as evidence that the benchmark represents all geometry problems. They also do not measure variation across repeated model generations.

### Table 3. Paired McNemar Tests: Zero-Shot vs. Few-Shot

We compare the two settings on the **same 48 problems** using **two-sided exact McNemar tests**. An **improvement** is a failure in zero-shot that becomes a success in few-shot; a **regression** is a success that becomes a failure. Syntax and logic errors are both treated as failures. Problems that succeed in both settings or fail in both settings do not contribute to the test's discordant counts.

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Improved</th><th>Regressed</th><th>Exact p</th><th>Holm-adjusted p</th></tr>
      <tr><td>GPT-5.4</td><td>6</td><td>4</td><td>0.7539</td><td>1.0000</td></tr>
      <tr><td>Gemini 3.1 Pro</td><td>15</td><td>5</td><td><strong>0.0414</strong></td><td>0.3311</td></tr>
      <tr><td>Grok 4.3</td><td>10</td><td>5</td><td>0.3018</td><td>1.0000</td></tr>
      <tr><td>Claude Sonnet 4.6</td><td>13</td><td>6</td><td>0.1671</td><td>1.0000</td></tr>
      <tr><td>GPT-4o mini</td><td>4</td><td>0</td><td>0.1250</td><td>0.8750</td></tr>
    </table>
    </td>
    <td valign="top">
    <table>
      <tr><th>Model</th><th>Improved</th><th>Regressed</th><th>Exact p</th><th>Holm-adjusted p</th></tr>
      <tr><td>Qwen3:235B</td><td>16</td><td>0</td><td><strong>0.0000305</strong></td><td><strong>0.000305</strong></td></tr>
      <tr><td>Llama3.1:70B</td><td>11</td><td>0</td><td><strong>0.000977</strong></td><td><strong>0.008789</strong></td></tr>
      <tr><td>Llama3.2:3B</td><td>0</td><td>0</td><td>1.0000</td><td>1.0000</td></tr>
      <tr><td>Mistral:7B</td><td>0</td><td>0</td><td>1.0000</td><td>1.0000</td></tr>
      <tr><td>Nemotron-3 Nano:4B</td><td>0</td><td>0</td><td>1.0000</td><td>1.0000</td></tr>
    </table>
    </td>
  </tr>
</table>

**Bold p-values are below 0.05.** The raw exact p-values indicate significant improvements for Gemini 3.1 Pro, Llama3.1:70B, and Qwen3:235B at the unadjusted 0.05 level. After **Holm correction across all ten model comparisons**, only Llama3.1:70B and Qwen3:235B remain significant. Non-significant results do not establish that the prompting strategies are equivalent. For models with no changed success/failure outcomes, the exact p-value is reported as 1.0000.

Improvements were most pronounced on syntax-related failures, suggesting that unfamiliarity with AlphaGeometry's DSL contributes to translation errors, especially for open-source models. A substantial gap to the strongest closed-source models nonetheless remained.

![Few-shot and Zero-shot Performance Comparison](README/graph_model_performance.png)

### Human-Guided Hints

A two-stage correction framework was applied after the initial translation attempt:

1. **Stage 1 — Diagram-Based Hints**: the model is shown a geometric diagram of the target problem and asked to re-evaluate its translation, supplying spatial context (collinearity, circle membership, auxiliary constructions) that's often implicit in the figure but underspecified in text.
2. **Stage 2 — Error-Specific Hints**: if needed, a targeted hint identifies the specific issue (e.g. wrong argument count, misused predicate, invalid construction) and the model revises just that part of its translation.

This staged approach improved translation accuracy for the four models reported below:

| Model | Improvement |
|---|---:|
| Grok 4.3 | +32.14% |
| Claude Sonnet 4.6 | +26.47% |
| Gemini 3.1 Pro | +14.63% |
| GPT-5.4 | +14.29% |

Hints were most effective for strong frontier models whose initial translations were already close to correct — narrowing the search space let them recover rather than requiring them to re-derive the whole formalization. The approach is effective as a diagnostic/correction tool but doesn't scale well on its own, since it requires supplying diagrams and manually inspecting model outputs.

### Supervised Fine-Tuning

Smaller open-source models were fine-tuned on **HAGeo-409**, a human-verified set of geometry problems paired with AlphaGeometry formalizations. Fine-tuning improved adherence to DSL grammar and reduced several classes of syntax errors, but gains in overall executable-translation accuracy were limited. Qualitative review showed fine-tuned models often learned surface-level DSL patterns without reliably capturing the underlying geometric semantics — producing syntactically plausible but semantically wrong constructions. This suggests the bottleneck for smaller models isn't just DSL familiarity but also limited reasoning capacity.

## Takeaways

- A substantial performance gap persists between frontier closed-source models and open-source alternatives on geometry auto-formalization.
- Few-shot prompting increased observed success rates for seven of ten models; improvements for Llama3.1:70B and Qwen3:235B remained statistically significant after Holm correction.
- Human-guided hints are highly effective for near-correct translations but don't scale to large benchmark runs.
- Fine-tuning helps with syntax but not with the deeper geometric reasoning needed for semantic correctness.
- The syntax/logic error taxonomy is useful for diagnosing *why* a translation failed, not just whether it did.

## Future Work

Extending NL2AGBench to other theorem-proving systems, incorporating diagrams directly via multimodal formalization, building specialized auto-formalization models, and adding symbolic feedback loops for iterative self-correction.
