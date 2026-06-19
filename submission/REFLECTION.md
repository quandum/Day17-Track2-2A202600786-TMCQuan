# Reflection — Day 17 (≤ 200 words)

**Họ và tên:** Trần Mạnh Chánh Quân  
**Mã số học viên:** 2A202600786

Answer briefly, in your own words. This is graded on reasoning, not length.

1. **The flywheel.** Day 13 emitted agent traces; today you turned them into an
   eval set and DPO pairs that Day 22 will train on. Which step in
   `traces → Bronze → datasets` would break most silently in production if you
   got it wrong — and how would you detect it?

   **The `flatten()` step would break most silently: if it dropped a child span or truncated the tree, the span count would be wrong but the surviving spans would look normal. The detection is to assert `sum(children) + 1 == total_flattened_rows` per trace — a structural invariant that catches missing spans even when all values are valid.**

2. **Decontamination.** Your run dropped 2 of 3 preference pairs because their
   prompts were in the eval set. What concretely goes wrong if you *skip* this
   step and train on those pairs? How would the lie show up in your metrics?

   **Training on leaked pairs means the model has seen the eval prompt during training. Eval accuracy appears artificially high because the model memorized the answer instead of generalizing. The lie shows up as a gap between high eval scores and poor real-world performance — the classic "eval says great, prod says terrible" pattern.**

3. **Point-in-time.** The naive join leaked a future `lifetime_spend` into the
   training row. Describe one feature in a system you know that would be
   dangerous to join without an `ASOF`/point-in-time guard.

   **A transaction-fraud model features like "number of chargebacks in the last 30 days" — without ASOF, you could accidentally include chargebacks from *after* the transaction, making the model see the future and inflate offline AUC while being useless in production.**

4. **Graph vs vector.** From `kg_demo.py`, name one question the knowledge graph
   answers well that flat chunk retrieval (`embed.py`) would struggle with, and
   one where the graph is overkill.

   **Graph excels at "Where does a widget ship from?" (multi-hop: widget → accessory → Hanoi) — vector RAG can't bridge this because no single chunk contains both facts. Graph is overkill for "What is the return policy for a widget?" — a simple vector lookup on the returns-policy chunk answers it directly without graph traversal overhead.**