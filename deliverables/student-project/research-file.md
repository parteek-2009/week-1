Markdown
# Agent Architecture: Bayesian Bot & Fake Review Analyzer

## 1. Problem Statement & Solution

* **The Problem:** Bot accounts and hired fake reviewers manipulate product ratings—either inflating their own products with fake positive reviews or smearing competitors with fake negative reviews.
* **The Solution:** Instead of relying on expensive, unpredictable LLM prompts to "guess" if a review is fake, this agent uses **Bayesian probability theory**. It combines cheap metadata rules with micro-LLM text classification to continuously update a mathematical risk score.

---

## 2. The 4 Hidden States

The agent tracks 4 mutually exclusive states representing every possible combination of how a review looks vs. what it actually is:

1. **State 1 ($S_1$):** Review looks genuine, actually genuine (Standard authentic user).
2. **State 2 ($S_2$):** Review looks fake, actually fake (Short/spammy bot review).
3. **State 3 ($S_3$):** Review looks genuine, actually fake (High-effort paid fake review).
4. **State 4 ($S_4$):** Review looks fake, actually genuine (Short review by a real user with a specific use-case).

**Total Genuine Probability:**
$$P(\text{Genuine}) = P(S_1) + P(S_4)$$

---

## 3. How the Agent Works (2-Level Pipeline)

[ Incoming Review ]
│
▼
[ Level 1: Primary Evidence Extraction ]
├── 1. Micro-LLM Structure Check (Very Short, Short, Moderate, Long)
├── 2. Verified Purchase Flag (True/False)
├── 3. Account Age (New, Moderate, Old)
└── 4. Review Frequency (Low, High, Very High)
│
▼
[ Primary Bayes Calculation ]
│
├── P(Genuine) > 60%  ──► [ Auto-Approve ]
├── P(Genuine) < 20%  ──► [ High-Priority HITL Ban Queue ]
└── 20% ≤ P ≤ 60%    ──► [ Trigger Level 2 Deep Check ]
│
▼
[ Level 2: Secondary Evidence ]
├── 1. Cross-Review Text Similarity (Embedding Cosine Check)
└── 2. Brand Concentration Ratio (% reviews on 1 seller)
│
▼
[ Secondary Bayes Update ]
│
┌───────┴───────┐
P > 60% │               │ P < 20%
(Approve) ▼               ▼ (High-Priority HITL)
Still 20%-60%
│
▼
[ Watchlist / 0.20x Weight Dampened ]


---

## 4. Pipeline Details

### Level 1: Primary Evidence Check
When a review comes in, the agent runs 4 quick, low-cost checks:
1. **Text Classification:** A micro-LLM categorizes the review structure (`VERY_SHORT`, `SHORT`, `MODERATE`, `LONG`).
2. **Verified Purchase:** Python check for confirmed transaction.
3. **Account Age:** Python rule (`NEW`: 0-3m, `MODERATE`: 3m-2y, `OLD`: >2y).
4. **Review Frequency:** Python rule (`LOW`: 0-2/wk, `HIGH`: 2-5/wk, `VERY_HIGH`: >5/wk).

The agent looks up historical base rates and calculates $P(\text{Genuine})$ using Bayes' Rule.

---

### Level 2: Secondary Evidence Check (The Gray Zone)
If $P(\text{Genuine})$ falls into the **20% to 60% gray zone**, primary evidence isn't enough. The agent triggers 2 deeper user-level behavioral checks:

1. **Cross-Review Text Similarity:** Checks vector cosine similarity across the user's last 5–10 reviews to catch copied scripts/templates.
2. **Brand Concentration Ratio:** Checks what percentage of the user's reviews target this single seller/brand (detects targeted bias/smear accounts).

The agent runs a second Bayesian update using Level 1's output as the new prior probability.

---

## 5. Decision Thresholds & Human-in-the-Loop (HITL)

Banning a genuine user causes far more business damage than letting a bot slip through. To solve this, the agent uses human moderation as a safeguard:

* **$P(\text{Genuine}) > 60\%$:** Auto-Approve and publish.
* **$P(\text{Genuine}) < 20\%$:** Flagged for Ban $\rightarrow$ Sent to **High-Priority HITL Queue** for human confirmation.
* **Still between $20\%$ and $60\%$ (Post Level 2):** Published with **Dampened Rating Weight** (e.g., counts as only 0.20x in seller star rating) $\rightarrow$ Sent to **Low-Priority HITL Watchlist Queue**.

Human decisions (Approve/Ban) are fed back into the system to continuously recalibrate historical probability table