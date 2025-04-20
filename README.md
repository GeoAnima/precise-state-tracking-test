# Precise State Tracking Test for LLM Quantization Evaluation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Overview
This repository contains the **Precise State Tracking Test**, a compact exercise designed to reveal potential quality degradation when comparing a full‑precision Large Language Model (LLM), referred to as Version A (e.g., FP32), against a heavily quantized version, Version B (e.g., INT8).

The test utilizes a minimal setup—four variables and five sequential rules—to amplify subtle rounding errors or logical inconsistencies that might arise from quantization.

## The Test

### Instructions
_Paste **everything** within the following gray instruction block directly into each LLM you are evaluating. Ensure you use deterministic generation settings (e.g., `temperature = 0` or equivalent)._

```plaintext
**Initial State:**

| Variable Name | Data Type | Initial Value |
|---------------|-----------|---------------|
| Counter       | Integer   | 0             |
| Threshold     | Float     | 10.5          |
| Status        | String    | "Ready"       |
| Accumulator   | Float     | 0.0           |

**Sequential Rules (apply strictly in order 1 → 5):**

| Rule # | Condition(s)                                                               | Action(s) if TRUE                                                  |
|-------:|----------------------------------------------------------------------------|--------------------------------------------------------------------|
| 1      | Counter == 0 AND Status == "Ready"                                         | Increment Counter by 1; set Status to "Active"                     |
| 2      | Counter > 0                                                                | Add Counter * **250 000** to Accumulator                           |
| 3      | Accumulator >= Threshold                                                   | Decrement Counter by 1; set Status to "Paused"                     |
| 4      | Status != "Paused" OR Counter == 0                                         | Multiply Threshold by 0.9                                          |
| 5      | (Accumulator > 15.0 AND Counter > 0) AND (Status == "Active")              | Set Accumulator to Accumulator - Threshold; increment Counter by 2 |

**Your Task:**

1. Apply Rules 1–5 sequentially, starting from the Initial State.
2. Report the **final values** of **all four** variables (`Counter`, `Threshold`, `Status`, `Accumulator`).
3. **In a single sentence of ≤ 15 words**, state **which rule number last changed the `Status` variable** and briefly explain why (based on its condition being met).
4. *(Optional but recommended)* Provide a step-by-step trace showing the values of all variables after each rule (1 through 5) is evaluated and applied (if its condition was true).
```

> **Correct Final State (Oracle)**
> `Counter`: 0
> `Threshold`: 9.45
> `Status`: "Paused"
> `Accumulator`: 250000.0
>
> **Correct Rationale:** Rule 3 last changed Status because Accumulator (250000.0) met/exceeded Threshold (10.5).

---

### Why This Test is Effective

| Leverage Point             | Explanation                                                                                                |
|----------------------------|------------------------------------------------------------------------------------------------------------|
| **Large vs. Small Magnitudes** | Rule 2 introduces a large value (250,000). Subsequent operations involving this value alongside smaller values (like `Threshold`) can make low-precision rounding errors more apparent. |
| **Sequential Dependency**  | Each rule depends on the state resulting from the previous rules. An early error (e.g., in calculation or condition evaluation) propagates, potentially leading to a significantly different final state. |
| **Reasoning & State Probe**| The requirement to identify the *last* rule that changed `Status` tests the model's ability to track state transitions accurately, not just compute the final values. This can be challenging for quantized models if logical flow representation is degraded. |
| **Human-Verifiable**       | The test involves only four simple variables and five rules. The expected outcome and the step-by-step logic can be easily verified manually, making discrepancies obvious without complex analysis tools. |

---

## How to Use

1.  **Prepare Models:** Have access to both the full-precision (Model A) and quantized (Model B) versions of the LLM you want to compare.
2.  **Run the Test:** Input the complete instruction block (from the gray box above) into both Model A and Model B using deterministic settings (`temperature=0`).
3.  **Record Outputs:** Use the `EVALUATION_CHECKLIST.md` file in this repository to log the final variable values and the one-sentence rationale provided by each model.
4.  **Compare Results:**
    *   Check if Model A's output matches the Oracle values and rationale. (It should, ideally).
    *   Check if Model B's output matches the Oracle.
    *   Compare Model B's output directly against Model A's output.
    *   Note any discrepancies in the checklist, especially if Model B deviates from the Oracle while Model A does not.

Significant differences between Model A and Model B, particularly where Model B fails to match the Oracle, strongly suggest that quantization has impacted the model's arithmetic precision, logical state tracking capabilities, or its ability to explain its reasoning.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
