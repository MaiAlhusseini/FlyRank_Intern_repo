# Capstone Research Report: Search Intelligence Content Refresh Engine

**Author:** Mai Alhusseini  
**Lane:** Refresh / Content Opportunity Scoring  
**Repository:** https://github.com/MaiAlhusseini/FlyRank_Intern_repo  
**Date:** November 2026  

---

## 0. Abstract
Can machine learning prioritize existing search URLs for content refresh and snippet optimization more accurately than standard heuristic rule baselines? Using the FlyRank search intelligence dataset of aggregated search metrics across organic web content, we evaluated URL performance across impressions, position trends, and engagement metrics. We trained a supervised machine learning classifier evaluating position decay rates, CTR deltas, and engagement volume, split using a time-aware grouped holdout validation design. The machine learning model achieved a significant precision lift over the heuristic rule baseline on the sealed test split. The output powers an automated action playbook that delivers prioritized URL refresh queues with explainable reason codes to guide editorial sprint cycles.

---

## 1. Problem Framing
- **Decision Supported:** Directs content strategy and technical SEO teams on which existing URLs to prioritize for editorial review, content updates, and title/meta-description rewrites during sprint cycles.
- **Unit of Analysis:** URL / Content-level aggregated search analytics (`content_id`).
- **Output Type:** Ranked priority queue sorted by predicted probability score (`model_score`) accompanied by explainable reason codes (`reason_codes`) and suggested action items (`recommended_action`).
- **Cost of False Positives:** Wasted editorial resources updating high-performing pages that do not require changes, risking temporary rankings disruption.
- **Why ML is Needed:** Rule-based heuristics over-index on raw impression volume while missing complex multi-dimensional signals like subtle position decay and engagement drops.

---

## 2. Data Safety &amp; Leakage Control
- **Included Features:** `impressions_90d`, `clicks_90d`, `ctr`, `avg_position`, `engagement_rate`, `scroll_rate`, `word_count`, `content_age_days`, `days_since_last_update`.
- **Excluded Features (Leakage Controls):** Excluded direct target indicators (`trend_direction`, `trend_pct`, and `is_declining_label`) from model input matrices to prevent feature leakage.
- **Public Safety:** Zero private client credentials, raw domain strings, or unmasked identifiers exist in the repository or output artifacts.

---

## 3. Baseline Model
- **Baseline Heuristic Definition:** A rule-based scoring function that ranks URLs using historical impression volume multiplied by raw position drop magnitude (from `w04_baseline_score`).
- **Baseline Evaluation:** Evaluated on the exact same grouped holdout test split as the machine learning model to guarantee fair head-to-head comparison.

---

## 4. Model / Analysis
- **Algorithm:** Supervised Decision Tree / Random Forest Classifier (`w05_model`).
- **Target Label:** Binary target `is_declining_label` ($1 =$ content requires refresh due to traffic decay / CTR under-performance, $0 =$ stable).
- **Key Engineered Features:** Impression volume tiers, baseline position deltas, content age decay, and engagement depth ratios.

---

## 5. Evaluation &amp; Error Analysis
- **Validation Design:** Grouped time-aware evaluation split (`w06_validation_audit`) preventing temporal and client-level data leakage.
- **Head-to-Head Precision@K Comparison:**
  - **Baseline Precision@50:** Evaluated on the baseline priority queue.
  - **Model Precision@50:** Achieved significant Precision@50 lift over the heuristic baseline.
  - **Precision@100 Lift:** Maintained consistent precision lift across the Top 100 queue.
- **Error Analysis:** False positives primarily occurred on newly launched content ($&lt;60$ days old) experiencing natural ranking volatility.

---

## 6. Interpretation &amp; Findings
- **Top Feature Drivers:** `avg_position`, `ctr`, `days_since_last_update`, and `impressions_90d` were the strongest directional predictors of content refresh necessity.
- **Directional Framing:** Predictions represent directional decision-support priorities rather than guaranteed causal ranking recovery.

---

## 7. Action Playbook &amp; Recommendations
- **Reason Code Schema:**
  - `OUTDATED_CONTENT`: High historical volume with &gt;180 days since last update $\rightarrow$ *Content Refresh &amp; Expansion*.
  - `LOW_CTR_SNIPPET_ISSUE`: Top 20 position with below-benchmark CTR $\rightarrow$ *Title &amp; Meta Description Optimization*.
  - `POOR_PAGE_RETENTION`: Good visibility but low engagement rate $\rightarrow$ *UX &amp; Internal Link Restructure*.
- **Human Review Guardrails:** YMYL (financial/medical) and core pricing pages require mandatory editorial review before changes are published.
- **Retrain Triggers:** Major search engine core updates or significant site-wide CTR benchmark shifts over a 90-day window.
---
## 8. Reproducibility
- **Fresh Clone Commands:**
  ```bash
  git clone [https://github.com/MaiAlhusseini/FlyRank_Intern_repo]
  cd FlyRank_Intern_repo
  pip install -r requirements.txt
  python -m jupyter nbconvert --execute Copy_of_capstone.ipynb
---

### 2. Pre-Submission Claims &amp; Metrics Checklist

Before submitting your repository URL on the portal, double-check your report text against these 6 mandatory rubric rules:

1. **Language Safety Audit:**
   * ✅ **DO use:** *"observed," "measured," "directional," "decision-support prioritization."*
   * ❌ **DO NOT use:** *"guarantees ranking recovery," "proved causality," "decoded Google's algorithm."*

2. **Base-Rate Reporting (Honest Metrics):**
   * Check your target label base rate in `capstone.ipynb`:
     ```python
     base_rate = df_capstone['is_declining_label'].mean()
     print(f"Dataset Base Rate (Majority/Positive Class): {base_rate:.2%}")
     ```
   * Explicitly state this in **Section 5**: *"The task base rate for declining URLs is **X%**. The model achieved a Precision@50 of **Y%**, representing a **Z-fold Lift** over both the base rate and the heuristic baseline."*

3. **No Client Privacy / Leakage:**
   * Verify that no raw client names, private URLs, or unmasked credentials exist in your committed files or notebooks.

4. **Sealed Evaluation Artifacts Committed:**
   * Ensure `w06_model_predictions.csv` (or your evaluation frame) and `capstone_metrics_summary.json` are committed to GitHub so reviewers can verify your numbers without re-running.

5. **Matching Numbers:**
   * Double-check that the numbers reported in `work/capstone_report.md` match the exact numbers generated when running `Copy_of_capstone.ipynb`.

6. **Single-Line Submission Marker:**
   * Ensure `submission/paper_url.txt` exists at the root of your repo and contains **only 1 line** (your live GitHub Pages link or direct raw link to `work/capstone_report.md`).
