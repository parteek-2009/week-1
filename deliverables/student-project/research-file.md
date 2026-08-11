# Research File

## Problem Statement

The agent observes a post before it's published. It must select publish, hold for review, or flag as adversarial-evasion because the poster's intent and full context is not known.

## Project Objective

Design a pre-publish content-moderation agent for user-friendly platforms that intercepts sensitive or inappropriate posts before they become visible to other users, rather than relying on post-publish reporting. The agent must reason under incomplete information (it cannot fully observe poster intent or surrounding context) and must route ambiguous or severe cases to human reviewers rather than acting unilaterally on high-cost decisions like banning.

## Technical Terms

- Content moderation (proactive vs. reactive)
- Toxicity detection / toxicity classifier
- Trust and safety (T&S)
- AutoModerator (Reddit's rule-based automod system)
- Hate speech detection vs. incivility detection (umbrella term: "toxicity detection")
- Adversarial text perturbation / adversarial example
- Character-level obfuscation (homoglyph substitution, leetspeak, letter-swap, word-splitting)
- Evasion attack / Attack Success Rate (ASR)
- Spurious correlation (classifier learning surface patterns instead of true intent)
- Community-specific calibration (different subreddits tolerate different things)
- False positive / false negative in moderation context
- Human-in-the-loop review
- Confidence threshold / decision threshold
- Belief distribution over hidden states
- Posterior probability / Bayesian update
- Precision, recall, calibration (as distinct from raw accuracy)

## Search Queries Used

- "reddit communities content moderation trust and safety discussion"
- "r/moderatorsupport OR r/modhelp trust and safety subreddit active"
- "subreddit machine learning content moderation NLP toxicity classifier community"
- "adversarial text obfuscation evade toxicity classifier character substitution research"

## Verified Reddit Communities (5–10 required)

Each of these was checked for current relevance and activity before inclusion.

1. **r/ModSupport** — Official, admin-run Reddit community where moderators raise issues directly with Reddit admins. Verified active; documented in Reddit's own official help-center list of moderator communities. Directly relevant because it's where real moderators discuss the exact tradeoffs (proactive vs. reactive removal, appeal handling, false-positive costs) my agent is designed around.
2. **r/modhelp** — Unofficial but resource-rich peer-support community for moderators. Verified active. An NSF-funded academic study specifically analyzed 115 threads and 2,740 comments from this subreddit and r/ModSupport on hate/harassment moderation challenges, confirming it's a substantive, research-worthy source of real practitioner discussion, not just casual chat.
3. **r/AutoModerator** — Community focused on Reddit's rule-based automated moderation tool. Verified active via Reddit's official communities-for-moderators list. Relevant because AutoModerator is the closest existing real-world analogue to a pre-publish rule-based interception layer, which is exactly what my agent's first-pass filter resembles.
4. **r/MachineLearning** — Large, active, general ML research community. Relevant for technical discussion of classifier robustness, adversarial examples, and NLP model behavior that underlies the sensitivity-detection layer of the agent.
5. **r/artificial** — Active general AI community. Useful for higher-level discussion of AI moderation systems, agent design, and public perception of automated bans/flags — relevant to the "how do people feel about pre-publish AI intervention" angle.
6. **r/ExperiencedDevs** — Active professional software-engineering community. Useful for practitioner discussion on trust & safety engineering, production ML system design, and the operational tradeoffs of shipping a detection system at scale.
7. **r/dataisbeautiful** — Considered but will only use if a specific results-visualization discussion is relevant; not counted toward the core 5 unless it produces a real completed discussion.

(Core five committed: r/ModSupport, r/modhelp, r/AutoModerator, r/MachineLearning, r/artificial. r/ExperiencedDevs as a sixth if time allows, to reach the 20-contribution target across ten communities.)

## Relevant X Accounts

To be finalized once X search is run directly (X content is not indexable the same way as web search results). Categories to search for once on-platform, based on what the web research surfaced:
- Researchers publishing on toxicity-detection robustness and adversarial text attacks (e.g., authors behind Perspective API robustness work, TextShield, "Toxicity Detection is NOT all you Need")
- Trust & safety practitioners/engineers who post publicly about moderation system design
- Reddit's own official trust & safety / admin accounts, if public-facing ones exist
- Critics and researchers focused on moderation transparency and false-positive harms

This section will be completed and verified directly on X per Section 6's instruction to select 15–25 accounts — pending because it needs live X search, not web search.

## Five Verified Papers, Articles, or Datasets

1. **"Decoding the Rule Book: Extracting Hidden Moderation Criteria from Reddit Communities"** (EMNLP 2025 Main, arxiv.org/html/2509.02926) — Directly supports the hidden-state framing of my agent: shows that moderation classifiers learn implicit, community-specific criteria not stated in official rules, and can learn spurious correlations (e.g., flagging any mention of "moderator" regardless of context) rather than true intent.
2. **"Toxicity Detection is NOT all you Need: Measuring the Gaps to Supporting Volunteer Content Moderators through a User-Centric Method"** (arxiv.org/html/2311.07879v4) — Establishes that toxicity detection alone is insufficient for real moderation needs; subreddits have ~25 distinct rule types beyond toxicity. Directly supports why my agent needs more than a single toxicity score.
3. **"A New Generation of Perspective API: Efficient Multilingual Character-level Transformers"** (arxiv.org/pdf/2202.11176) — Google's Perspective API team's own paper, includes a section specifically on human-readable obfuscation (character substitution like "a" → "4", "@") and reports model robustness under 0–50% obfuscation rates. Directly relevant to the adversarial-evasion action in my agent.
4. **"Evading Toxicity Detection with ASCII-art: A Benchmark of Spatial Attacks on Moderation Systems"** (arxiv.org/html/2409.18708) — Benchmarks multiple evasion strategies (homoglyphs, word-splitting, misspellings, ASCII-art) against real toxicity detectors and LLMs, with F1 scores per attack type. Gives me real, citable data on how much evasion degrades detection.
5. **"Custodians of Online Communities: How Moderator Mutual Support Communities Help Fight Hate & Harassment"** (NSF-funded, par.nsf.gov/servlets/purl/10528248, USENIX) — Qualitative analysis of r/ModSupport and r/modhelp specifically, documenting real moderator challenges, tradeoffs, and admin-moderator relationship friction. Grounds my related-work section in actual practitioner experience rather than speculation.

## Questions I Want to Answer

- What specifically can the agent NOT observe that a human moderator with full context could? (Working answer: poster intent, prior conversational context, in-group vs. out-group language use — needs sharpening.)
- How should the hidden state be structured — as a single "is this bad" belief, or as separate beliefs over intent, context, and evasion-likelihood?
- What's a realistic false-positive rate to expect from a pre-publish sensitivity check, based on published toxicity-classifier benchmarks?
- How much does character-level obfuscation actually degrade detection accuracy in practice (need the specific numbers from the ASCII-art and Perspective API papers)?
- What do real moderators say is the biggest cost of over-aggressive automated flagging (this needs direct evidence from r/ModSupport / r/modhelp discussions, not just my assumption)?
- Is "flag as adversarial-evasion" really a distinct action from "hold for review," or should it just be a reason that routes into the same review queue?

## AI Prompts Used and Important AI Errors Caught

- Used the Section 4 research prompt as the basis for this file's structure.
- Initial AI-drafted problem statement (from Claude) bundled "poster's intent" and "full context" into a single hidden state without justification — flagged as a real modeling gap that needs to be resolved deliberately, not left ambiguous, before Section 8 design work.
- Initial AI-proposed action set included "flag as adversarial-evasion" as a peer action to "publish" / "hold for review" without clear justification for why it's a separate action rather than just a reason that triggers "hold for review" — this is an open question, not yet resolved, recorded above rather than silently accepted.
- All Reddit communities listed above were checked against real search results (Reddit's own official help-center page, an NSF-funded academic paper, and general web results) rather than accepted from an AI-generated list without verification, per Section 4's instruction to remove any community that is inactive or not relevant.
- X accounts were NOT filled in from AI suggestion, because AI cannot reliably verify live X account activity via web search — flagged explicitly above as pending direct X-platform verification rather than presenting an unverified list as fact.
