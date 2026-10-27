# Project notes — 27 October 2026

## Challenge

Huawei Ireland Tech Arena 2026 Phase 1 is a click-through-rate prediction task. For each evaluation row, predict the probability of a click for a user/item candidate. Each event contains 10 candidate rows and at most one clicked row. Phase 1 closes Wednesday 4 November 2026 at 11:59pm Dublin time. The top 15 advance to the onsite phase.

The dataset is synthetic. train.csv contains 6,868,240 rows (686,824 events); evaluation.csv contains 529,390 rows (52,939 events), one event per user. Evaluation is each user's first event at or after the 27 January 2025 cutoff. Histories contain up to 20 previous clicks. rowid and event_id are random identifiers and should not be model features. Parse CSV with a real parser and histories as JSON.

## Official evaluation and submission requirements

- Metric: ROC AUC over all rows in each public/private evaluation split.
- The public and private sets are randomly split from evaluation rows. The final Phase 1 ranking uses private AUC; public AUC is only the live leaderboard.
- AUC measures global clicked-row versus non-clicked-row ranking. It is not a per-event or per-user average; no prediction threshold or calibration is needed.
- Team limit: 10 submissions per day.
- Submission format: UTF-8 CSV with header rowid,predicted_probability, exactly one row for every evaluation rowid, finite values in [0,1].
- Before upload, check the header, number of rows, duplicate/missing/unknown rowids, rowid-to-prediction alignment, and probability range. Check upload status before retrying.
- Use time-based folds with event integrity and fit-only target statistics. Eval consists of each user's first event after the cutoff; a useful local fold mirrors that selection.

## Baseline and team position

As of the context pack dated 27 October, the best public score recorded was 0.825657 (24th of 50 on the 27 October snapshot); the team target was top 15 and at least 0.830 local-validated. These are dated observations, not a live leaderboard reading.

The official starter combines smoothed item/user CTR signals with item-item co-click similarity. The team reported that prior public submissions were around 0.826 while initial local feature-only models were around 0.63. Later results show the gap was not a direct comparison of one model: the stronger GBDT v1 measured 0.7259 / 0.7382 on local folds, and no model yet had both a local and public number in the room record.

## Findings from the room

### Feature engineering (T-03)

The recency/slate/cold-start feature builder produced rec_A, rec_B, and rec_eval, keyed by rowid. It uses fit-side labels/impressions only and includes a leakage check that removes post-cutoff data and nulls validation labels before comparing target features.

At commit 7a2f170, the reported real-data leakage check passed on both folds. First-only/all-fit LightGBM AUC was approximately 0.6332/0.6331 on A and 0.6369/0.6373 on B; the starter was 0.6110/0.6141. A 58-column recommended set was documented. The verifier assignment moved from Atharva-agent to maksim-agent; the transcript does not record a completed board ruling after that reassignment.

The reported validation/evaluation adversarial AUC was 0.61 on A and 0.67 on B, mainly associated with return times after the cutoff. Eval users were reported to return later (median 42.6h since last click; p90 70h) than fold A/B. Window-grid features that perfectly identify the dataset window were flagged for exclusion.

### Validation harness (T-02)

The proposed contract uses Parquet files with text identifiers, Int64 timestamps, Int8 time fields, parsed history arrays, and two time-based folds (pseudo-cutoffs 25 and 26 January 2025). Validation rows are each user's first event at/after the cutoff; event rows remain together. The harness covers conversion, fold rowids, cold/warm parity, global AUC, and submission validation.

T-02 was blocked in the room on data availability and GitHub authentication. A later message says the code was ready locally and a CSV subfolder lookup fix was committed; the real-data parity check and PR push remained outstanding in the transcript.

### Sequence model (T-04)

The first real-data SASRec-lite runs were weak standalone: 0.5145 (per-row BCE) and 0.5158 (listwise) on fold A. The best sequence-derived column was max cosine similarity to history at 0.546 standalone. The proposed pivot was to make sequence features a heavily regularized residual on top of the stronger choice model and judge only its change to stacked global AUC.

### GBDT and choice model (T-05)

GBDT v1 reported local global AUC 0.7259 / 0.7382 on folds A/B. Version 2 reported 0.7724 / 0.7887. A softmax choice model over 10 candidates plus no-click, with user/item factors and history pooling, scored 0.7421 / 0.7600 alone. Stacking its point-in-time logit, rank/share, and P(any click) features into the GBDT reported 0.7610 / 0.7769, above a logit blend. Strong weight decay (0.7) was reported to reduce overfitting. The choice model was trained using only earlier days for each scored day.

The 58 rec_ columns added about 0.001 to v1 but no gain over v2. A v2 candidate file was reported format-checked and aligned with raw eval; the coordinator had not yet posted a go decision for the calibration upload in the room transcript.

### Public-to-private calibration and the AUC gap (T-07)

The hypothesis that no-click events explain the local/public gap was refuted. About 62.9% of train events and 63.3–63.4% of validation events contained a click. For v1, global AUC and clicked-event-only AUC were nearly identical; per-event diagnostics were lower and are not the official competition metric.

The room concluded that no model had both a local and public score yet. One calibration submission, after the coordinator's explicit go decision, was the proposed direct measurement. Do not infer local-to-public performance by comparing different models or metrics.

## Room board snapshot

The final room messages in this archive state:

- T-02 validation harness: blocked on the owner's GitHub authentication/data workflow.
- T-03 feature engineering: awaiting independent verification by maksim-agent.
- T-04 sequence residual: in progress.
- T-05 GBDT ranker/blend: in progress.
- T-07 AUC-gap explanation: concluded; no-click hypothesis refuted.
- T-08 documentation contract section: blocked on T-02 landing.
- T-09 choice-model sweep: approved for phi-agent, verifier maksim-agent.

## Working rules and cautions

The team's room plan says one shared repository, branches named feat/name-area, and PRs into main; no direct pushes to main. Work items use an owner and a different verifier. Do not upload competition submissions without local global-AUC evidence posted to the room and the coordinator's go decision. Preserve private-team discussion and keep the repository private.

This document consolidates room-reported evidence. It does not independently verify model outputs, PR contents, submission status, or the live leaderboard.
