# NL2AGBench: Benchmarking LLM Auto-Formalization for AlphaGeometry

![Translation gap between natural-language geometry problems and AlphaGeometry's formal DSL](readme-imgs/graphic_translation_difficulty.png)

*Figure 1: The translation gap between natural-language geometry problems and AlphaGeometry's formal DSL. While AlphaGeometry has demonstrated strong theorem-proving capabilities, the manual formalization step remains a major barrier to usability and large-scale deployment.*

## Benchmark Construction

NL2AGBench is built from geometry problems originally formalized for AlphaGeometry:

- **Source**: The Java Geometry Expert (JGEX) repository, from which the AlphaGeometry project manually formalized 231 problems into its DSL.
- **Selection**: 48 representative problems were selected for translation diversity (circles, cyclic quadrilaterals, angle bisectors, orthocenters, circumcenters, midpoints, reflections, perpendicular constructions) and execution efficiency (excluding problems with excessive AlphaGeometry runtime).
- **Format**: Every problem pairs a natural-language statement with a verified, executable AlphaGeometry translation.

![Sample NL2AGBench problems](readme-imgs/images_benchmark_samples.png)

*Figure 2: Sample NL2AGBench problems spanning a range of geometric concepts, selected for both diversity and runtime efficiency.*

## Evaluation Method

Translation quality is judged with **execution-based evaluation** rather than string/textual similarity, since two syntactically different DSL outputs can be semantically equivalent. Each generated translation is run directly inside AlphaGeometry and the outcome is bucketed into one of three classes:

- **Successful Translation** — the specification parses and executes correctly.
- **Syntax Error** — the DSL violates AlphaGeometry's grammar or construction rules (further split into `AssertionError`, `KeyError`, and `ValueError` cases in the error taxonomy).
- **Logic Error** — the specification is syntactically valid but semantically inconsistent with the source problem (e.g. wrong predicate, omitted constraint, misread geometric relationship). Runs that hang without output for 5+ minutes are also classified as logic errors.

We evaluate **10 state-of-the-art LLMs** (closed- and open-source, spanning multiple parameter scales) using this framework.

### Table 1. Zero-Shot Model Performance

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Success rate</th></tr>
        <tr><td>GPT-5.4</td><td align="right">75.0%</td></tr>
        <tr><td>Gemini 3.1 Pro</td><td align="right">62.5%</td></tr>
        <tr><td>Grok 4.3</td><td align="right">52.1%</td></tr>
        <tr><td>Claude Sonnet 4.6</td><td align="right">37.5%</td></tr>
        <tr><td>GPT-4o mini</td><td align="right">4.2%</td></tr>
      </table>
    </td>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Success rate</th></tr>
        <tr><td>Qwen3:235B</td><td align="right">12.5%</td></tr>
        <tr><td>Llama3.1:70B</td><td align="right">0.0%</td></tr>
        <tr><td>Llama3.2:3B</td><td align="right">0.0%</td></tr>
        <tr><td>Mistral:7B</td><td align="right">0.0%</td></tr>
        <tr><td>Nemotron-3 Nano:4B</td><td align="right">0.0%</td></tr>
      </table>
    </td>
  </tr>
</table>

*All rates are based on 48 problems. Success counts, in the model order shown: closed-source — 36, 30, 25, 18, 2; open-source — 6, 0, 0, 0, 0. Wilson intervals are listed below Table 2.*

## Improving Translation Performance

Three mitigation strategies were tested on top of the zero-shot baseline: few-shot prompting, human-guided hints, and supervised fine-tuning.

### Few-Shot Prompting

Each model was given 54 reference examples pairing natural-language problems with verified AlphaGeometry translations, exposing it to common constructions, DSL syntax patterns, and proof predicates. This intervention requires no retraining and increased the observed success rate for seven of the ten models. Qwen3:235B showed the largest absolute gain, at 33.3 percentage points.

### Table 2. Zero-Shot vs. Few-Shot Performance

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Zero-shot</th><th>Few-shot</th><th>Gain (pp)</th></tr>
        <tr><td>GPT-5.4</td><td align="right">75.0%</td><td align="right">79.2%</td><td align="right">+4.2</td></tr>
        <tr><td>Gemini 3.1 Pro</td><td align="right">62.5%</td><td align="right">83.3%</td><td align="right">+20.8</td></tr>
        <tr><td>Grok 4.3</td><td align="right">52.1%</td><td align="right">62.5%</td><td align="right">+10.4</td></tr>
        <tr><td>Claude Sonnet 4.6</td><td align="right">37.5%</td><td align="right">52.1%</td><td align="right">+14.6</td></tr>
        <tr><td>GPT-4o mini</td><td align="right">4.2%</td><td align="right">12.5%</td><td align="right">+8.3</td></tr>
      </table>
    </td>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Zero-shot</th><th>Few-shot</th><th>Gain (pp)</th></tr>
        <tr><td>Qwen3:235B</td><td align="right">12.5%</td><td align="right">45.8%</td><td align="right">+33.3</td></tr>
        <tr><td>Llama3.1:70B</td><td align="right">0.0%</td><td align="right">22.9%</td><td align="right">+22.9</td></tr>
        <tr><td>Llama3.2:3B</td><td align="right">0.0%</td><td align="right">0.0%</td><td align="right">0.0</td></tr>
        <tr><td>Mistral:7B</td><td align="right">0.0%</td><td align="right">0.0%</td><td align="right">0.0</td></tr>
        <tr><td>Nemotron-3 Nano:4B</td><td align="right">0.0%</td><td align="right">0.0%</td><td align="right">0.0</td></tr>
      </table>
    </td>
  </tr>
</table>

*All rates are based on 48 problems. Gains are percentage points (pp), calculated before rounding. Few-shot success counts, in the model order shown: closed-source — 38, 40, 30, 25, 6; open-source — 22, 11, 0, 0, 0.*

<details>
<summary><strong>95% Wilson confidence intervals</strong></summary>

<table>
  <tr><th>Model</th><th>Zero-shot 95% CI</th><th>Few-shot 95% CI</th></tr>
  <tr><td>GPT-5.4</td><td align="right">61.2–85.1%</td><td align="right">65.7–88.3%</td></tr>
  <tr><td>Gemini 3.1 Pro</td><td align="right">48.4–74.8%</td><td align="right">70.4–91.3%</td></tr>
  <tr><td>Grok 4.3</td><td align="right">38.3–65.5%</td><td align="right">48.4–74.8%</td></tr>
  <tr><td>Claude Sonnet 4.6</td><td align="right">25.2–51.6%</td><td align="right">38.3–65.5%</td></tr>
  <tr><td>GPT-4o mini</td><td align="right">1.2–14.0%</td><td align="right">5.9–24.7%</td></tr>
  <tr><td>Qwen3:235B</td><td align="right">5.9–24.7%</td><td align="right">32.6–59.7%</td></tr>
  <tr><td>Llama3.1:70B</td><td align="right">0.0–7.4%</td><td align="right">13.3–36.5%</td></tr>
  <tr><td>Llama3.2:3B</td><td align="right">0.0–7.4%</td><td align="right">0.0–7.4%</td></tr>
  <tr><td>Mistral:7B</td><td align="right">0.0–7.4%</td><td align="right">0.0–7.4%</td></tr>
  <tr><td>Nemotron-3 Nano:4B</td><td align="right">0.0–7.4%</td><td align="right">0.0–7.4%</td></tr>
</table>

</details>

Wilson intervals describe uncertainty around each success rate, not uncertainty in the improvement. Because the problems were manually curated, these binomial-model intervals do not establish representativeness of all geometry problems or measure variation across repeated model generations.

### Table 3. Paired McNemar Tests: Zero-Shot vs. Few-Shot

We compare the paired success/failure outcomes on the same 48 problems using **two-sided exact McNemar tests**. The values below are **unadjusted p-values**.

<table>
  <tr>
    <th>Closed-source models</th>
    <th>Open-source models</th>
  </tr>
  <tr>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Exact p-value</th></tr>
        <tr><td>GPT-5.4</td><td align="right">0.7539</td></tr>
        <tr><td>Gemini 3.1 Pro</td><td align="right"><strong>0.0414</strong></td></tr>
        <tr><td>Grok 4.3</td><td align="right">0.3018</td></tr>
        <tr><td>Claude Sonnet 4.6</td><td align="right">0.1671</td></tr>
        <tr><td>GPT-4o mini</td><td align="right">0.1250</td></tr>
      </table>
    </td>
    <td valign="top">
      <table>
        <tr><th>Model</th><th>Exact p-value</th></tr>
        <tr><td>Qwen3:235B</td><td align="right"><strong>0.0000305</strong></td></tr>
        <tr><td>Llama3.1:70B</td><td align="right"><strong>0.000977</strong></td></tr>
        <tr><td>Llama3.2:3B</td><td align="right">1.0000</td></tr>
        <tr><td>Mistral:7B</td><td align="right">1.0000</td></tr>
        <tr><td>Nemotron-3 Nano:4B</td><td align="right">1.0000</td></tr>
      </table>
    </td>
  </tr>
</table>

**Bold values meet the p < 0.05 threshold.** At this unadjusted threshold, the tests provide statistically significant evidence of improved success rates for **Gemini 3.1 Pro, Llama3.1:70B, and Qwen3:235B**. The other comparisons do not provide sufficient evidence of a difference; this does not establish that the settings are equivalent.

<details>
<summary><strong>Paired outcome counts used in the tests</strong></summary>

<table>
  <tr><th>Model</th><th>Improved</th><th>Regressed</th></tr>
  <tr><td>GPT-5.4</td><td align="right">6</td><td align="right">4</td></tr>
  <tr><td>Gemini 3.1 Pro</td><td align="right">15</td><td align="right">5</td></tr>
  <tr><td>Grok 4.3</td><td align="right">10</td><td align="right">5</td></tr>
  <tr><td>Claude Sonnet 4.6</td><td align="right">13</td><td align="right">6</td></tr>
  <tr><td>GPT-4o mini</td><td align="right">4</td><td align="right">0</td></tr>
  <tr><td>Qwen3:235B</td><td align="right">16</td><td align="right">0</td></tr>
  <tr><td>Llama3.1:70B</td><td align="right">11</td><td align="right">0</td></tr>
  <tr><td>Llama3.2:3B</td><td align="right">0</td><td align="right">0</td></tr>
  <tr><td>Mistral:7B</td><td align="right">0</td><td align="right">0</td></tr>
  <tr><td>Nemotron-3 Nano:4B</td><td align="right">0</td><td align="right">0</td></tr>
</table>

</details>

*Improved: failure in zero-shot → success in few-shot. Regressed: success in zero-shot → failure in few-shot. Syntax and logic errors both count as failures. Outcomes that remain successful or remain failures do not contribute to the test's changed-pair counts. When no outcomes change, p is reported as 1.0000.*

Improvements were most pronounced on syntax-related failures, suggesting that unfamiliarity with AlphaGeometry's DSL contributes to translation errors, especially for open-source models. A substantial gap to the strongest closed-source models nonetheless remained.

![Few-shot and Zero-shot Performance Comparison](readme-imgs/graph_model_performance.png)

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
- Few-shot prompting increased observed success rates for seven of ten models; the exact McNemar tests identified significant improvements for Gemini 3.1 Pro, Llama3.1:70B, and Qwen3:235B at the unadjusted 0.05 threshold.
- Human-guided hints are highly effective for near-correct translations but don't scale to large benchmark runs.
- Fine-tuning helps with syntax but not with the deeper geometric reasoning needed for semantic correctness.
- The syntax/logic error taxonomy is useful for diagnosing *why* a translation failed, not just whether it did.

## Future Work

Extending NL2AGBench to other theorem-proving systems, incorporating diagrams directly via multimodal formalization, building specialized auto-formalization models, and adding symbolic feedback loops for iterative self-correction.