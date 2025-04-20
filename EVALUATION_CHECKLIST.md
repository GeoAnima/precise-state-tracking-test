# Evaluation Checklist ▸ Precise State Tracking Test

Use this sheet to log and compare outputs from a full-precision LLM (Model A) and its quantized counterpart (Model B).

---

## Test Execution Details

*   **Test Date:** `[YYYY-MM-DD]`
*   **Model A Name/Version:** `[e.g., GPT-4-Turbo FP32, Llama-3-70B-Instruct BF16]`
*   **Model B Name/Version:** `[e.g., GPT-4-Turbo INT8, Llama-3-8B-Instruct GGUF Q4_K_M]`

---

## Oracle (Correct Values)

| Variable           | Expected Value | Expected Rationale Trigger |
|--------------------|----------------|----------------------------|
| Counter            | 0              |                            |
| Threshold          | 9.45           |                            |
| Status             | "Paused"       |                            |
| Accumulator        | 250000.0       |                            |
| Last Status Change |                | Rule 3                     |

---

## Comparison Table

| Criterion                               | Expected            | Model A Output | Model B Output | Model B Correct? (Y/N) | A vs B Match? (Y/N) | Notes (e.g., specific errors, phrasing issues) |
|-----------------------------------------|---------------------|----------------|----------------|------------------------|---------------------|------------------------------------------------|
| **Final Counter**                       | 0                   |                |                |                        |                     |                                                |
| **Final Threshold**                     | 9.45                |                |                |                        |                     |                                                |
| **Final Status**                        | "Paused"            |                |                |                        |                     |                                                |
| **Final Accumulator**                   | 250000.0            |                |                |                        |                     |                                                |
| **Rationale Correctly IDs Rule 3?**     | Yes                 |                |                |                        |                     |                                                |
| **Rationale Sentence ≤ 15 words?**      | Yes                 |                |                |                        |                     |                                                |
| *(Optional) Trace Step 1 correct?*      | *See Manual Calc*   |                |                |                        |                     |                                                |
| *(Optional) Trace Step 2 correct?*      | *See Manual Calc*   |                |                |                        |                     |                                                |
| *(Optional) Trace Step 3 correct?*      | *See Manual Calc*   |                |                |                        |                     |                                                |
| *(Optional) Trace Step 4 correct?*      | *See Manual Calc*   |                |                |                        |                     |                                                |
| *(Optional) Trace Step 5 correct?*      | *See Manual Calc*   |                |                |                        |                     |                                                |
| **Overall Execution Correct?**          | All expected match  |                |                |                        |                     |                                                |

*(Manual calculation reference for trace steps if needed:
Step 1: C=1, T=10.5, S="Active", A=0.0
Step 2: C=1, T=10.5, S="Active", A=250000.0
Step 3: C=0, T=10.5, S="Paused", A=250000.0
Step 4: C=0, T=9.45, S="Paused", A=250000.0
Step 5: C=0, T=9.45, S="Paused", A=250000.0)*

### How to Fill

1.  Fill in the **Test Execution Details** section.
2.  Run the test on both models and copy their outputs into the **Model A Output** and **Model B Output** columns.
3.  For each row, compare Model B's output to the **Expected** value/condition. Mark **Model B Correct?** as "Y" (Yes) or "N" (No).
4.  Compare Model A's output directly to Model B's output for that row. Mark **A vs B Match?** as "Y" or "N". (They match if they give the *same* answer, even if it's wrong).
5.  Use the **Notes** column for observations like calculation drift, logical errors, failure to follow constraints (e.g., sentence length), or interesting phrasing differences.

Consistent "N" marks for Model B, especially when Model A shows "Y" (or when A and B don't match), indicate potential issues introduced by quantization.
