# START HERE: Huawei Ireland Tech Arena 2026 (read this first if you have no context)

Snapshot taken 27 Oct 2026, ~4pm Dublin. Sources: the competition portal, the BeMyApp platform, the official PDFs and the opening webinar. The other docs in this knowledge base are the full primary sources.

## 1. What this is
- **Huawei Ireland Tech Arena 2026**, run by the Huawei Ireland Research Centre with BeMyApp. The theme is "Competing in the Intelligent Marketplace: Generative Sequential Recommendation Under Constraints".
- **Phase 1 (now, online): click-through-rate prediction.** It's a scored Kaggle-style leaderboard, not a pitch. Each event shows one user 10 candidate items, and at most one gets clicked. Predict P(click) for every candidate row in `evaluation.csv`.
- **Metric: global ROC AUC** across all evaluation rows. Only the ranking matters, not calibration. It isn't a per-user or per-event average.
- **Public/private split:** the leaderboard shows the public AUC, but the **final Phase 1 ranking uses the private AUC**, which is revealed after the phase closes. Don't overfit to the public number.
- **Phase 2 (onsite final, top 15):** a simulated marketplace where our recommendation agent competes against other agents for user clicks. 4 rounds of develop → submit → evaluate → feedback. Not CSV: they run our models under a **wall-time limit on GPU machines**. The top 8 present, and judging covers storytelling, cost/scalability, flexibility and technical implementation (that's how 2025 was judged).

## 2. Dates (Dublin time)
| When | What |
|---|---|
| **Wed 4 Nov, 11:59pm** | **Phase 1 closes** |
| Fri 30 Oct | Registration closes |
| 5–6 Nov | Selection |
| 9 Nov | Top 15 announced |
| **Sat 14 – Sun 15 Nov** | Onsite final, Clayton Hotel Cardiff Lane, Dublin 2. Top 8 announced 3:55pm day 2, presentations 5pm, awards 7pm |

Prizes: 1st €6,000 (1 team) · 2nd €2,500 (2 teams) · 3rd €1,500 (3 teams), split equally per member. Finalists also get Huawei internship interviews.

## 3. Rules that matter
- **10 submissions per team per day** (confirmed in writing by Gabor Fodor, Huawei). Plan them together, no blind uploads.
- **AI coding tools are allowed** (official FAQ).
- The data is **synthetic** (simulator). How it was generated won't be revealed. Any modelling approach is allowed.
- `rowid` and `event_id` are random. Don't use them as features.
- Submission: UTF-8 CSV, header `rowid,predicted_probability`, exactly one row per evaluation rowid (529,390 rows), values finite in [0,1]. A `.zip` of the CSV is accepted. Bad files are rejected, not repaired.
- Teams need 3–8 members.

## 4. The data
| File | Size | Rows |
|---|---|---|
| `train.csv` (in `train.zip`, 650 MB zipped) | ~7.3 GB | 6,868,240 rows = 686,824 events |
| `evaluation.csv` (in `evaluation.zip`, 45 MB zipped) | 459 MB | 529,390 rows = 52,939 events, one per user |
| `example_submission.csv` | 25 MB | 529,390, constant value (AUC ≈ 0.5) |

Columns: `rowid, event_id, user_id, item_id, timestamp, time_bucket (0–3, 6-hour UTC blocks), day_type (0 weekday / 1 weekend), history_item_ids (JSON array), history_timestamps (JSON array), clicked (train only)`.
- The history holds **previous clicks only** (not impressions): up to the latest 20, oldest → newest. It can include warm-up clicks from before 20 Jan 2025.
- Train covers 20 Jan 2025 00:00 UTC up to (not including) 27 Jan 2025. **Evaluation = each user's first event at or after the cutoff.**
- Some eval users aren't in train, and some have an empty history. Predictions have to cover these cold-start rows.
- **Local validation:** use a time-based split (fit on earlier events, test on later ones), keep each event on one side, and compute target stats from the fit part only. A later window doesn't perfectly mimic "first event per user", so a better validation set is each user's first event after a pseudo-cutoff.
- IDs are opaque strings. Parse the history columns as JSON with a real CSV parser (don't split on commas). Stream or chunk `train.csv` (7.3 GB).

## 5. Where to get things
- **Competition portal** (data, starter, submissions, leaderboard): `https://portal-techarena-ireland.hackathon.com/submit.html`. Downloads: Instructions, Training data, Evaluation data, Example submission, Starter solution. Leaderboard at `/leaderboard.html`.
- **BeMyApp platform** (team, chats, talks, resources PDFs): `https://platform-techarena-ireland.hackathon.com`. It currently throws a cert warning (the cert is for `*.virtualconference.com`). Organisers know.
- **Admin:** Adé (BeMyApp), adelaide@bemyapp.com. Email gets a faster reply than platform chat.
- **Mentors:** Daniel Dunne, Cici Wang, Gonzalo Fiz Pontiveros, Gabor Fodor (Huawei IRC).
- **Team repo:** `github.com/EuanSmith2/Huawei-Hackathon`. The data files are NOT in git (too big): each person downloads them from the portal.

## 6. Our team on the portal
- Portal team name: **"SignalRank – Lean Sequential Recommender"** (Euan's group joined it, 27 Oct).
- **Our best public AUC: 0.825657 → 24th of 50** on the leaderboard (27 Oct).
- Submission history (public AUC). The file names show the approaches tried:

| Date | File | AUC |
|---|---|---|
| 24 Oct | submission_apex_72_super | 0.817989 |
| 24 Oct | submission_super_blend_65_35 | 0.818039 |
| 24 Oct | submission_super_blend_60_40 | 0.818008 |
| 25 Oct | submission_apex_sas_85_15 | 0.824352 |
| 25 Oct | submission_dual_engine_4fold_pure | 0.815686 |
| 25 Oct | submission_apex_r4_v5_w19 | 0.825587 |
| 25 Oct | submission_apex_r4_v5_w21 | 0.825627 |
| 25 Oct | submission_apex_w21_slatenet_99_1 | 0.825618 |
| 25 Oct | submission_apex_r4_v5_w2065 | 0.825609 |
| 25 Oct | submission_apex_AB_w215 | 0.825629 |
| 25 Oct | submission_apex_r4_v5_w22 | 0.825629 |
| 25 Oct | submission_apex_AB_w2175 | 0.825630 |
| 25 Oct | submission_apex_AB_w2175_micro65 | 0.825628 |
| 26 Oct | submission_csdt_aligned_pure | 0.814773 |
| 26 Oct | submission_free_lunch_champ_90_10 | 0.821562 |
| 26 Oct | **submission_champ_dominant_free_lunch_95_5** | **0.825657 (best)** |
| 26 Oct | submission_champ_dominant_free_lunch_90_10 | 0.825652 |
| 26 Oct | submission_event_propensity_blend_45_55 | 0.823919 |
| 26 Oct | submission_tri_40_30_30 | 0.820638 |
| 26 Oct | submission_grand_dual_stacker_catboost_95_5 | 0.825490 |

Read on this: blend-weight tweaks since 25 Oct have moved AUC by about 0.00005, which is noise against a public/private split. The gap to the top (0.833) needs new signal or a new model, not more re-weighting.

## 7. Leaderboard (public AUC, 27 Oct ~4pm)
1 free lunch 0.832966 · 2 CSDT 0.832692 · 3 P3 Labs 0.832391 · 4 YLZ 0.832089 · 5 Slopify67 0.831985 · 6 Nova 0.831635 · 7 The Dark Triad 0.830559 · 8 Epsilon 0.830372 · 9 Team Signal 0.829602 · 10 prjkt-avengers 0.829430 · 11 THUNDERSTORM 0.828452 · 12 PROMETHEUS 0.828392 · 13 What?! 0.828242 · 14 Vitality 0.827915 · **15 MUnemployed 0.827904 (cut line for Phase 2)** · 16 SAFA 0.827783 · 17 HalluciNation 0.827426 · 18 Triple T 0.827162 · 19 Conversion 0.827030 · 20 M-IRE 0.826861 · 21 The Clanker Program 0.826628 · 22 Rererererecommendation 0.826323 · 23 HackBots 0.825658 · **24 SignalRank (us) 0.825657** · 25 Learn2RecSmartly 0.825154 · … 50 DCU_IT 0.755668. 50 teams have submitted.

**To make the top 15 we need about +0.0023 public AUC (~0.8280+), and more margin to be safe against the private split.** The whole top 10 sits within 0.0035.

## 8. Modelling pointers (from the organisers' own material)
- The starter (doc 05) combines item CTR + user CTR in log-odds, plus an item-item co-click cosine bonus. Its own docstring says a good solution should "model user/item interactions directly (e.g. matrix factorization)".
- The organisers' reading list: **SASRec** (attention over histories), **TIGER** (generative retrieval), **HSTU** (sequence modelling at scale), and **Schnabel et al. 2016, "Recommendations as Treatments"** (exposure bias / propensity). The slides stress that "a logged click reflects both preference and exposure".
- Signals worth trying: history recency (time since last click, time since the candidate was last clicked), whether the candidate is in the history and how often, within-event features (rank of the candidate's score among its 10 slate-mates, slate-level normalisation), user activity/cold-start flags, time_bucket/day_type interactions, item popularity trends, and sequence-model embeddings (SASRec/GRU4Rec) fed into a GBDT (LightGBM/CatBoost) ranker.
- Huawei's advice (Derek Collins, Oct 2026): "understand the data and constraints, establish a strong baseline, and then iterate. Test assumptions rather than simply adding complexity." For Phase 2, be ready to explain why you designed it that way and what trade-offs you made.
- Phase 2 runs under a wall-time limit, so keep an eye on inference cost now. A model that's 0.0005 better but 20× slower may lose later.

## 9. How we work (Agent Room rules)
- One shared repo. Each agent owns a folder/module and works on its own branch, `feat/<name>-<area>`. Nobody pushes to main: PRs only, and one human merges.
- Live coordination goes in the room. Durable decisions go in the repo's `CLAUDE.md` + `docs/contracts.md`.
- Every piece of work gets a room task with a done-when line and a verifier who isn't the owner.
- **Submissions are scarce (10/day).** Post the local validation AUC in the room before using a submission slot, and log every upload (file, local AUC, public AUC) in the repo.

## 10. Index of this knowledge base
1. 01-START-HERE (this file)
2. 02-official-instructions: the full official task spec
3. 03-official-faq: the official FAQ
4. 04-webinar-slides: the opening webinar slide text
5. 05-starter-solution: the official baseline code
6. 06-webinar-transcript: the full opening webinar transcript (14 Oct), including Q&A
7. 07-platform-chats-and-webinar-qna: announcements, admin answers, matchmaking posts, written webinar Q&A
