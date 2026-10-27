# Submission log (portal team: SignalRank – Lean Sequential Recommender)

Every upload gets a row, **added the same day**. Public AUC comes from the portal's "Your submissions" table. Local AUC comes from the T-02 harness (fold C1 = 2025-01-25, fold C2 = 2025-01-26).

## Slot rules (10 uploads per team per day, the counter resets daily)
1. **Never upload without a local AUC posted in the Agent Room first.** It must beat the current best on **both** folds.
2. Keep **2 slots per day** for the end-of-day best candidates.
3. Blend-weight micro-tweaks (Δ < 0.0002 local) don't get a slot: that's noise against the private split.
4. The final private ranking uses the hidden split. Pick the final submission by **local fold AUC**, not public AUC.
5. Pre-upload check: run the T-02 validator (header, 529,390 unique rowids, values in [0,1]). Upload a `.csv.zip` to save bandwidth.
6. After every upload: add a row here and post `[RESULT] <file> public=<auc>` in the room.

## Current best
**0.825657** public: `submission_champ_dominant_free_lunch_95_5.csv.zip` (26 Oct). Leaderboard 24th/50 on 27 Oct, top-15 cut 0.827904.

## Log
| Date | File | Local C1 | Local C2 | Public AUC | Notes |
|---|---|---|---|---|---|
| 24 Oct 1:43pm | submission_apex_72_super | – | – | 0.817989 | pre-room (SignalRank) |
| 24 Oct 1:59pm | submission_super_blend_65_35 | – | – | 0.818039 | |
| 24 Oct 2:40pm | submission_super_blend_60_40 | – | – | 0.818008 | |
| 25 Oct 3:29pm | submission_apex_sas_85_15 | – | – | 0.824352 | big jump: "sas" component (SASRec?) |
| 25 Oct 5:31pm | submission_dual_engine_4fold_pure | – | – | 0.815686 | |
| 25 Oct 6:11pm | submission_apex_r4_v5_w19 | – | – | 0.825587 | |
| 25 Oct 6:21pm | submission_apex_r4_v5_w21 | – | – | 0.825627 | |
| 25 Oct 10:49pm | submission_apex_w21_slatenet_99_1 | – | – | 0.825618 | |
| 25 Oct 10:54pm | submission_apex_r4_v5_w2065 | – | – | 0.825609 | |
| 25 Oct 10:57pm | submission_apex_AB_w215 | – | – | 0.825629 | |
| 25 Oct 11:02pm | submission_apex_r4_v5_w22 | – | – | 0.825629 | |
| 25 Oct 11:04pm | submission_apex_AB_w2175 | – | – | 0.825630 | |
| 25 Oct 11:05pm | submission_apex_AB_w2175_micro65 | – | – | 0.825628 | |
| 26 Oct 1:42am | submission_csdt_aligned_pure | – | – | 0.814773 | |
| 26 Oct 1:57am | submission_free_lunch_champ_90_10 | – | – | 0.821562 | |
| 26 Oct 2:01am | **submission_champ_dominant_free_lunch_95_5** | – | – | **0.825657** | **best** |
| 26 Oct 2:04am | submission_champ_dominant_free_lunch_90_10 | – | – | 0.825652 | |
| 26 Oct 12:00pm | submission_event_propensity_blend_45_55 | – | – | 0.823919 | |
| 26 Oct 12:09pm | submission_tri_40_30_30 | – | – | 0.820638 | |
| 26 Oct 1:08pm | submission_grand_dual_stacker_catboost_95_5 | – | – | 0.825490 | |

Note: the code and local validation behind these 20 uploads aren't in this repo. If anyone has the "apex" / "champ" pipeline, push it to `legacy/` so T-05 can blend against it.
