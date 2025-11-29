# Creative Generator Prompt

## Purpose
Generate new creative ideas for **low-CTR or underperforming campaigns**, grounded strictly in the creative messaging already present inside the dataset summary.  
Each output must be usable by marketers: short headlines, clear benefit-driven messages, a CTA, rationale, and a confidence score.

---

## Think
Use ONLY:
- `low_ctr_campaigns`
- `campaign_summaries[].top_creative_messages`
- `creative_type` (if available)
- Any anchor phrases present in existing messages (≤4 words)

Your job:
- Identify what current creatives are emphasizing.
- Extract safe, generic, benefit-driven terms (e.g., “soft fabric”, “breathable”, “all-day comfort”) based ONLY on real anchor examples.
- Generate new creative variations that:
  - stay aligned with product tone,
  - respect the original message style,
  - do NOT hallucinate new product categories/features.

If a campaign has **no anchor messages**, fallback to neutral, benefit-first templates.

---

## Analyze
For each low-CTR campaign:

1. Extract up to 5 anchor messages:  
   Keep them **short** (≤120 chars).

2. Extract benefit-based phrases:  
   - Look for 2–4 word phrases  
   - NOT inventing features  
   - Must occur in anchor messages

3. Generate 3–6 creative ideas per campaign:

Each candidate must follow **this schema**:

```json
{
  "headline": "Short headline (≤ 8 words)",
  "message": "Benefit-driven sentence (≤ 140 chars)",
  "cta": "Shop Now | Learn More | Try Now | Order Today",
  "rationale": "1-line explanation referencing anchor terms",
  "anchor_examples": ["short phrase 1", "short phrase 2"],
  "confidence": 0.0
}
```
### Rules:

* CTA allowed set: ["Shop Now","Learn More","Try Now","Order Today"]

* Never mention:

 - % discounts

 - prices

 - “medical”, “health claims”, “guarantees”

 - urgency not present in anchor messages

* Candidates must be completely grounded in existing anchor phrases.

### Optional LLM Rewrite (if config.use_llm==true)

After generating template-based candidates:

* Send them to the LLM with a strict JSON schema request.

* Let LLM refine style, tone, clarity.

* MUST validate output:

 - Must match schema 100%

 - On invalid output:

    * Retry up to 3 times

    * If still invalid → fall back to template candidate

* Save raw LLM responses to:

```json
logs/llm_traces/<campaign_id>.json
```

This ensures traceability and safety.

### Conclude
SUCCESS FORMAT
```json
{
  "status": "ok",
  "payload": {
    "creatives": [
      {
        "campaign": "men comfortmax launch",
        "campaign_norm": "men comfortmax launch",
        "generated": [
          {
            "headline": "Comfort All Day",
            "message": "Experience soft, breathable comfort inspired by our top sellers.",
            "cta": "Shop Now",
            "rationale": "Derived from 'soft feel' and 'breathable fit' in anchors.",
            "anchor_examples": ["soft feel", "breathable fit"],
            "confidence": 0.85
          }
        ]
      }
    ]
  }
}
```

#### NO LOW-CTR CAMPAIGNS
```json
{
  "status": "ok",
  "payload": {
    "creatives": [],
    "note": "no low-ctr campaigns detected"
  }
}
```

#### ABORT
```json
{
  "status": "abort",
  "reason": "summary missing required fields"
}
```
### Reflection & Retry Logic

* If anchor messages lack clarity:

  - Use safe fallback templates.

 - Record "assumption":"fallback_no_anchors" in rationale.

* If a generated idea appears too similar to an existing anchor message:

 - Slightly vary tone / structure.

 - Keep meaning intact.

* If confidence < 0.50:

 - Add note inside rationale.

 - Consider adding a second variation.

### Safety & Constraints

* NO hallucinated product features.

* NO mention of discounts or availability unless explicitly present in anchors.

* NO sensitive or medical claims.

* Must preserve original tone (benefit-first, comfort-first, or lifestyle-driven).

* Output size must remain small — max 6 creatives per campaign.