# Huawei Tech Arena 2026: FULL CONTEXT (single file, 27 Oct 2026)

Everything an agent with zero context needs. Sections: START HERE, official instructions, FAQ, slides, starter code, platform chats + webinar Q&A, team record + other teams, all 94 teams, full webinar transcript.

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

---

# TechArena 2026 – Phase 1: Click Prediction

## Task

Phase 1 is a click-probability prediction task on synthetic recommendation data. No real users or products are involved. Your task is to predict a click probability for every row in `evaluation.csv`.

- An **event** is one recommendation opportunity: one user is shown **10 candidate items** at one moment.
- A **row** in the CSV files is one candidate (an *impression*): one item shown to one user in one event.
- In the training data the target `clicked` is 1 if that candidate was clicked and 0 otherwise.
- An event has **at most one clicked candidate**. Events with no click are also possible: all 10 of their rows have `clicked = 0`.

For **every row of `evaluation.csv`**, predict the probability that the candidate was clicked. Do not submit an extra row or
value for the "no click" outcome.

## Files

| File | Content | Data rows (header excluded) |
|---|---|---|
| `train.csv` | Labelled candidates (includes `clicked`), 686,824 events | 6,868,240 |
| `evaluation.csv` | Candidates to predict, no labels, 52,939 events (one per user) | 529,390 |
| `example_submission.csv` | Format example: one line per evaluation row, all with the same constant value | 529,390 |
| `README.txt` | Short description shipped with the data | – |

`train.csv` is about 7.3 GB. Consider streaming or chunked processing. Labels for the evaluation rows are not provided.

## Columns

`evaluation.csv` has the same columns as `train.csv` except `clicked`.

| Column | Meaning |
|---|---|
| `rowid` | Identifier of the row (one candidate in one event). The key of your submission. |
| `event_id` | Identifier of the event. All 10 rows of an event share user, timestamp and history. |
| `user_id` | The user. |
| `item_id` | The candidate item shown in this row. |
| `timestamp` | Event time, Unix seconds, UTC (whole minutes). |
| `time_bucket` | Six-hour block of the UTC day: 0 = 00:00–05:59, 1 = 06:00–11:59, 2 = 12:00–17:59, 3 = 18:00–23:59. |
| `day_type` | 0 = Monday–Friday, 1 = Saturday or Sunday (UTC date). |
| `history_item_ids` | JSON array of items this user clicked before the event. |
| `history_timestamps` | JSON array of the Unix-second times of those clicks. |
| `clicked` | **Training only.** 1 = clicked, 0 = not clicked. |

- `rowid`, `event_id`, `user_id` and `item_id` are **opaque identifiers**. Treat them as text, not numbers, and do not try to
  interpret them. `rowid` and `event_id` are random and carry no information; use them as keys only.
- The history columns hold JSON arrays inside quoted CSV fields. Read the files with a CSV parser, then parse these fields
  with a JSON parser; do not split lines on commas.

## History and time split

- A history contains previous **clicks** only (not all earlier impressions): the latest **20 at most**, strictly before the
  current event, ordered **oldest to newest**.
- `history_item_ids` and `history_timestamps` are aligned element by element and have the same length. An empty history is `[]`.
  The same item can appear several times.
- The simulated timeline starts before the training period (a *warm-up* period, before 2025-01-20 UTC). Its impression rows are
  **not** included, but its clicks can appear in histories. A user can have a non-empty history without any row in `train.csv`.
- **Training** covers events from 2025-01-20 00:00 UTC up to, not including, 2025-01-27 00:00 UTC (the cutoff).
- **Evaluation** contains, for each user, the **first event at or after the cutoff**, with all its 10 candidates. Later events of
  these users are not included.
- Some evaluation users do not appear in `train.csv`, and some evaluation events have an empty history. Make sure your
  predictions cover these cases too.

**Local validation.** You have no evaluation labels, so validate on a later part of the training period: fit on earlier events,
test on later ones, and keep all rows of an event on the same side of the split. Compute features and aggregate statistics (for
example click rates per user or item) using labels from the fitting part only, never from the events you are testing on.
A later training window does not exactly reproduce the official setup, which uses one first event per user.

## Submission format

A UTF-8 CSV file with exactly this header (case-sensitive, this order):

```
rowid,predicted_probability
```

Format illustration only – a real submission has one line for **every** evaluation row:

```
rowid,predicted_probability
row_aaaa0000,0.0629
row_bbbb1111,0.1200
row_cccc2222,0.0003
```

Requirements:

- Every `rowid` of `evaluation.csv` appears **exactly once**: no missing, duplicate or unknown rowids.
- Exactly the two columns above and no extra index column. Keep each `rowid` exactly as given (no trimming or case changes).
- Each prediction is a finite number in **[0, 1]**. Plain decimals (`0.25`, `.5`, `1`) and scientific notation (`2e-3`) are accepted.
  Empty values, text, `nan` and `inf` are rejected.
- Predictions are matched to rows **by `rowid`**, so row order does not matter. Keeping the order of `example_submission.csv`
  is still convenient.
- The probabilities of an event do **not** have to sum to one.

Upload the finished file through the competition's submission page, then check its processing status in your submission
history. If an upload is interrupted, check the history before uploading again.

## Evaluation

Submissions are ranked by **ROC AUC**; higher is better. AUC measures how well your predictions rank clicked rows above
non-clicked rows: it is the probability that a randomly chosen clicked row gets a higher prediction than a randomly chosen
non-clicked row (ties count as one half). Random ranking has an expected AUC of 0.5 and perfect separation gives 1.0. No
threshold is needed, and only the ranking matters, not calibration. The example submission is constant and therefore scores ~0.5.

The evaluation rows are split between a public leaderboard and private leaderboard set randomly.
Submission scoring computes the ROC AUC for both, but only the public leaderboard score will get updated during Phase 1 of the competition.
The final ranking will be determined by the private AUC, to be revealed only after the end of Phase 1.
ROC AUC is computed over all rows in the public/private set rows, and is not a per-user or per-event average.

**Each team is limited to 10 submissions per day!**

## Starter solution

A simple starter solution is provided in starter_solution.py.

To run it yourself, use the command
```
python3 starter_solution.py --train train.csv --evaluation evaluation.csv --out submission.csv
```

## Checklist

- [ ] Read the CSV files with a CSV parser and parse the history columns as JSON.
- [ ] Keep identifiers as text and keep each prediction attached to its `rowid`.
- [ ] Produce one prediction for every evaluation row.
- [ ] Check the header, the number of rows, duplicates, missing or unknown `rowid`s, and that all values are in [0, 1].
- [ ] Check your submission status before retrying an upload.



---

# Official FAQ (Tech Arena Ireland - FAQ 2026 PDF, downloaded 27 Oct 2026; organisers update it often)

                                        FAQ

   -​ Is this real user data?

No, everything is synthetic. User IDs, item IDs, timestamps, and click events are
generated by a simulator; nothing here is tied to real people or real products.
Treat it purely as a statistical modeling problem.

   -​ Are AI coding tools (Copilot, ChatGPT, Claude Code, etc.) allowed?

Yes, of course. Use whatever tools help you build and debug your model.

   -​ What am I actually predicting, and how is it scored?

 For each row of evaluation.csv (one candidate item shown to a user in one
event), predict the probability it was clicked. The primary metric is global ROC
AUC across all evaluation rows. A constant prediction scores ~0.5 AUC by
construction.

   -​ Is the leaderboard score I see during the competition final?

No, it's a provisional score on a subset of the evaluation set. The final ranking is
computed on the full evaluation set (including rows held back from the live
leaderboard) after the competition closes, so don't over-tune to the live number.

   -​ What format does my submission need to be in?

A CSV with header rowid,predicted_probability, exactly one row per rowid in
evaluation.csv, each exactly once, probabilities as finite numbers in [0, 1]. Run
validate_submission.py before submitting — malformed, missing, duplicate, or
out-of-range submissions are rejected, not auto-repaired.

   -​ Can I use rowid or event_id as a feature?

Not recommended. Both are random identifiers assigned at export time and carry
no information. Don't waste time trying to decode them.

   -​ How many submissions am I allowed?

10 submissions per day, per team.


# Opening webinar slides (Tech Arena Ireland - Challenge 2026 PDF, Gonzalo Fiz Pontiveros, Huawei IRC, 14 Oct 2026), text extract

 HUAWEI IRELAND TECH ARENA 2026
From user histories
to recommendations
Generative sequential recommendation under constraints
Gonzalo Fiz Pontiveros
AIRecS, Huawei Ireland Research Centre
Opening webinar · 14 October 2026
 Recommendation turns abundance into discovery
 01 / WHY RECOMMENDATION MATTERS
Which item is useful to this person, right now?
 A large catalogue
 Jazz mix Dance hits
 A listener A relevant
 Acoustic live Podcasts History, current intent recommendation
 and context Acoustic live
 Classical New releases
 Learn what matters Help someone discover it
Users Platforms Creators and sellers
Find useful content, products Make each interaction more Reach people who may value
and services. relevant. their work.
The same problem appears in shopping, video, news and many other digital services.
Tech Arena 2026 | Opening webinar 1/8
 A shared idea: predict what comes next
 02 / FROM LANGUAGE TO INTERACTION HISTORIES
Language
 On my way home
A listener’s interaction history
 Jazz mix Dance hits Acoustic set Next
 played skipped replayed interaction?
 T
 ∏︂
 pθ (x1 , . . . , xT ) = pθ (xt | x<t )
 t=1
 xt : a text token or an encoded interaction; x<t : the preceding sequence.
 Learn a conditional distribution from the history.
Tech Arena 2026 | Opening webinar 2/8
 LLMs motivate a scalable recipe for recommendation
 02 / WHY THIS DIRECTION IS EXCITING
 Scale and measure
 Represent events Learn from histories
 Data, context and
 Items, actions and context Conditional prediction
 model capacity
SASRec TIGER HSTU
2018 2023 2024
Attention over item histories Generate structured item Scale sequence models
for next-item prediction. identifiers from interaction adapted to recommendation
 histories. data.
 How far can the sequence modelling recipe take us in recommendation?
Tech Arena 2026 | Opening webinar 3/8
 What changes when the sequence records behaviour?
 03 / REPRESENTATION, TIME AND MEANING
 Typical text Interaction histories
Vocabulary A fixed vocabulary of reusable subword A changing catalogue of items,
 tokens including new items
Time Position within the text Order, elapsed time, sessions
Meaning Tokens in a linguistic context Views, clicks, skips and purchases carry
 different signals
Input/Output Short input sequences; long output Long input sequences; short output
Geometry sequences sequences
 The representation must preserve which event happened, and when.
Tech Arena 2026 | Opening webinar 4/8
 Recommendations help create tomorrow’s training data
 03 / OBSERVATION, FEEDBACK AND OBJECTIVES
 History
 Offer Response New data
 What we have
 What the user sees What the user does What we learn from
 observed
 • A logged click reflects both preference and exposure.
 • For an item that was never shown, the response is unknown.
 • Choosing recommendations changes the interactions we will observe.
 Predicting a response and choosing an action are connected problems.
Schnabel et al., Recommendations as Treatments (ICML 2016).
Tech Arena 2026 | Opening webinar 5/8
 Two phases: predict a response, then choose an offer
 04 / THE TECH ARENA CHALLENGE
 Phase 1 Predict clicks Phase 2 Compete and adapt
How likely is this user to click this Which item should your agent offer?
candidate?
 Given
Given A simulated marketplace and user histories.
Interaction history and candidate items.
 Build
Build A recommendation agent competing for user
A model of the user’s response. choice.
Submit Submit
A file of predicted click probabilities. Models for evaluation at round boundaries.
 Use what you learn about user behaviour to make better decisions.
Tech Arena 2026 | Opening webinar 6/8
 Phase 1: estimate the response to a candidate
 04 / FROM HISTORY AND CANDIDATE TO A CLICK PROBABILITY
Our listener’s history Illustrative predictions
 Jazz mix: played
 Candidate Click probability
 Dance hits: skipped
 Acoustic live 0.74
 Acoustic set: replayed Jazz classics 0.50
 Dance hits 0.12
 pθ (click | H, i)
H: observed history i: candidate item
 Learn from Score against
 Submit aligned predictions
 labelled examples hidden labels
Tech Arena 2026 | Opening webinar 7/8
 Phase 2: recommend, observe, improve
 04 / COMPETITION IN THE MARKETPLACE
 Your agent: Acoustic live
 Same listener Simulated user
 Agent A: Jazz classics
 Interaction history Responds to the offers
 Agent B: Dance hits
Four rounds of development and feedback
 Develop Release responses
 Submit Evaluate
 Initial data Learn for the
 Models collected Run on new data
 + feedback next round
 Start with a baseline. Improve quality and efficiency with each round.
Teams update their models between evaluation rounds.
Tech Arena 2026 | Opening webinar 8/8
 Selected reading
 REFERENCES / FOR PARTICIPANTS WHO WANT TO EXPLORE
 • Kang and McAuley (2018).
 Self-Attentive Sequential Recommendation.
 SASRec: attention over interaction histories.
 • Rajput et al. (2023).
 Recommender Systems with Generative Retrieval.
 TIGER: autoregressive generation of semantic item identifiers.
 • Zhai et al. (2024).
 Actions Speak Louder than Words: Trillion-Parameter Sequential
 Transducers for Generative Recommendations.
 HSTU: sequence modelling adapted to recommendation at scale.
 • Schnabel et al. (ICML 2016).
 Recommendations as Treatments:
 Debiasing Learning and Evaluation.
 Selection bias and propensity-weighted learning and evaluation.
Tech Arena 2026 | Opening webinar 8/8



---

# Official starter solution: starter_solution.py (from the portal, 27 Oct 2026)

Run: python3 starter_solution.py --train train.csv --evaluation evaluation.csv --out submission.csv

```python
#!/usr/bin/env python3
"""
TechArena CTR -- simple starter solution.

Uses only the Python standard library (csv, json, math, collections) -- no numpy,
pandas, or scikit-learn required. Intentionally simple: it should get you onto the
leaderboard with a clearly-beatable score, not win the competition.

Idea (three ingredients, combined by hand):
  1. Item popularity:  smoothed empirical click-through rate of each item.
  2. User propensity:  smoothed empirical click-through rate of each user.
  3. "People who clicked A also clicked B": a simple item-item co-click similarity,
     built from what users clicked/saw in their history, used to score each candidate
     against the user's own click history (this is a basic collaborative-filtering
     signal -- it is what gives this script most of its lift over popularity alone).

(1) and (2) are combined in log-odds ("naive Bayes") space, and (3) is added as a
bonus term. None of this uses the structure of the actual data-generating model --
a good participant solution should do meaningfully better by modeling user/item
interactions directly (e.g. matrix factorization) instead of hand-combining
independent signals like this.

Usage:
    python3 starter_solution.py --train train.csv --evaluation evaluation.csv --out submission.csv
"""
import argparse
import csv
import json
import math
import sys
from collections import defaultdict

SMOOTHING_K = 50          # pseudo-count for item/user CTR shrinkage toward the global rate
CF_WEIGHT = 25.0          # how much the co-click similarity bonus counts vs. the CTR priors


def logit(p, eps=1e-6):
    p = min(max(p, eps), 1 - eps)
    return math.log(p / (1 - p))


def read_rows(path, has_label):
    """Yields dicts for every row of a train/evaluation CSV."""
    with open(path, newline="", encoding="utf-8") as f:
        r = csv.DictReader(f)
        for row in r:
            row["history_item_ids"] = json.loads(row["history_item_ids"])
            if has_label:
                row["clicked"] = int(row["clicked"])
            yield row


def main():
    ap = argparse.ArgumentParser()
    ap.add_argument("--train", required=True)
    ap.add_argument("--evaluation", required=True)
    ap.add_argument("--out", default="submission.csv")
    a = ap.parse_args()

    # ---- pass 1 over train.csv: item/user click + impression counts, and an
    #      implicit "what did this user interact with" set (clicks + history) for
    #      the co-click similarity table -------------------------------------
    item_clicks, item_impr = defaultdict(int), defaultdict(int)
    user_clicks, user_impr = defaultdict(int), defaultdict(int)
    user_interacted = defaultdict(set)
    total_clicks, total_rows = 0, 0

    print("pass 1/2: reading train.csv ...", file=sys.stderr)
    for i, row in enumerate(read_rows(a.train, has_label=True)):
        uid, iid, y = row["user_id"], row["item_id"], row["clicked"]
        item_clicks[iid] += y
        item_impr[iid] += 1
        user_clicks[uid] += y
        user_impr[uid] += 1
        total_clicks += y
        total_rows += 1
        if row["history_item_ids"]:
            user_interacted[uid].update(row["history_item_ids"])
        if y:
            user_interacted[uid].add(iid)
        if (i + 1) % 2_000_000 == 0:
            print(f"  {i+1:,} rows", file=sys.stderr)

    global_ctr = total_clicks / total_rows
    item_ctr = {iid: (item_clicks[iid] + SMOOTHING_K * global_ctr) / (item_impr[iid] + SMOOTHING_K)
                for iid in item_impr}
    user_ctr = {uid: (user_clicks[uid] + SMOOTHING_K * global_ctr) / (user_impr[uid] + SMOOTHING_K)
                for uid in user_impr}
    g_logit = logit(global_ctr)

    # ---- item-item co-click table: co[a][b] = how many users interacted with both
    #      a and b. This is the "people who liked A also liked B" signal. --------
    print("building item-item co-click table ...", file=sys.stderr)
    item_pop = defaultdict(int)           # how many users interacted with each item
    co = defaultdict(lambda: defaultdict(int))
    for uid, items in user_interacted.items():
        items = list(items)
        for it in items:
            item_pop[it] += 1
        for x in range(len(items)):
            for yix in range(x + 1, len(items)):
                a_, b_ = items[x], items[yix]
                co[a_][b_] += 1
                co[b_][a_] += 1
    item_pop_sqrt = {k: math.sqrt(v) for k, v in item_pop.items()}

    def cf_score(candidate_item, history_items):
        """Cosine-normalized co-click similarity between the candidate and the
        user's own history items (0 if the candidate has no measured co-click
        overlap with anything in the history)."""
        if candidate_item not in co or not history_items:
            return 0.0
        neighbors = co[candidate_item]
        total = 0.0
        denom_a = item_pop_sqrt.get(candidate_item, 0.0)
        for h in history_items:
            c = neighbors.get(h)
            if c:
                denom_b = item_pop_sqrt.get(h, 0.0)
                if denom_a and denom_b:
                    total += c / (denom_a * denom_b)
        return total

    def score_row(uid, iid, history_items):
        i_ctr = item_ctr.get(iid, global_ctr)
        u_ctr = user_ctr.get(uid, global_ctr)
        combo_logit = logit(i_ctr) + logit(u_ctr) - g_logit
        return combo_logit + CF_WEIGHT * cf_score(iid, history_items)

    # ---- pass 2: score evaluation.csv and write the submission ------------------
    print("pass 2/2: scoring evaluation.csv ...", file=sys.stderr)
    with open(a.out, "w", newline="", encoding="utf-8") as out_f:
        w = csv.writer(out_f)
        w.writerow(["rowid", "predicted_probability"])
        for i, row in enumerate(read_rows(a.evaluation, has_label=False)):
            z = score_row(row["user_id"], row["item_id"], row["history_item_ids"])
            p = 1.0 / (1.0 + math.exp(-z))   # back to a probability for the submission format
            w.writerow([row["rowid"], f"{p:.6g}"])
            if (i + 1) % 2_000_000 == 0:
                print(f"  {i+1:,} rows scored", file=sys.stderr)

    print(f"done. wrote {a.out}", file=sys.stderr)


if __name__ == "__main__":
    main()
```



---

# Platform chats (scraped 27 Oct 2026, times are Dublin)

Rooms: Forum/Announcements (7) · Team Matchmaking (54) · Need help? Ask the Admin (36) · our team room (3) · DM with David Omozokpia (5). Messages marked [deleted] had no text left.

## Forum: Announcements (7)

**Adé BeMyApp** · 29 Sept, 11:48am
Welcome to the Huawei Ireland Tech Arena 2026 forum! This Announcements channel is where you'll find all the latest news, important updates and reminders throughout the challenge.
Your first mission: 1) Explore the platform. 2) Create your project and register your teammates. 3) Is your team complete? You're all set! Get ready for the Kick-off on 14 October, when we'll reveal the challenge and explain the next steps. 4) Looking for a team or additional teammates? Head to the Team Matchmaking channel and introduce yourself, including your university, area of expertise and level of study.

**Adé BeMyApp** · 29 Sept, 11:49am
Don't forget to subscribe to the channels to receive all the latest notifications!

**Adé BeMyApp** · 7 Oct, 2:39pm
A quick reminder about team formation: 1) Is your team complete? Keep your project in Draft mode so no one else can access or apply to join it. 2) Looking for more teammates? Publish your project so others can view it and apply to join. Once your team is complete, switch it back to Draft mode. 3) Looking to join a team? Check the published projects and click Apply.

**Adé BeMyApp** · 12 Oct, 1:21pm
Opening Webinar: 14 October at 1:00 PM Irish time. Join us to discover the challenge, key milestones and everything you need to know to get started. Link: #/talks/6ac66979c112f0bccdfe1455

**Adé BeMyApp** · 14 Oct, 4:04pm
The opening webinar replay is now available on the platform! I've also uploaded the slides. You can find both in the Talks tab (#/talks/6ac66979c112f0bccdfe1455). If you have any questions, email adelaide@bemyapp.com. I usually respond faster by email than via the platform chat.

**Adé BeMyApp** · 16 Oct, 10:40am
Several teams currently have only one or two members. Please remember that teams must have three members to be eligible for the challenge. I'll add participants who are still looking for a team to help complete yours.

**Adé BeMyApp** · 21 Oct, 9:06am (👍 reaction)
Phase 1 is now open! You can access the challenge through your team space on the platform. You have until 11:59 p.m. Dublin time on November 4 to give it your best and try to be among the 15 teams selected for Phase 2. If you have any questions, post them in the Lobby or email adelaide@bemyapp.com. You'll also find the FAQs in the platform's Resources section.

## Networking: Team Matchmaking (54)

- **Aayush Rokade** · 5 Oct 11:23am: "Hello Myself Aayush Rokade," (11:24am message deleted)
- **Dhruva Chaklasiya** · 5 Oct 11:26am: looking to join a team.
- **Aayush Rokade** · 5 Oct 11:29am: UCD, MSc Information Systems, BE Computer Engineering. RAG, data analysis, some recommender-systems knowledge. Looking for a team.
- **Harshita Dinesh Kumar** · 5 Oct 11:32am: CS (AI) master's, University of Galway. Python, ML, LLMs, agentic AI. Looking for a team.
- **Vanessa John Lobo** · 5 Oct 11:44am: CS PhD, UCD. AI/ML, LLMs, RAG, agentic systems, HCI. Looking for a team.
- **Gaurav Boob** · 5 Oct 12:47pm: CS Masters, Trinity. Has a team and wants one more, preferably with RAG experience. Proof of work: gaurav.life.
- **Divya Sunil Raj** · 5 Oct 9:11pm: MSc Cybersecurity, ATU Donegal, maths background, HDip Agile Software Eng. Data analysis/modelling. Looking for a team.
- **Adé BeMyApp** · 7 Oct 2:41pm: "go and check the teams tab some are hiring! just click on apply" (2:42pm message deleted)
- **Adé BeMyApp** · 7 Oct 2:44pm: teams looking to hire: #/projects/6ac4e0d9c112f0bccdfcc95c, 6ac39b50c112f0bccdfc3d73, 6ac39865c112f0bccdfc3b09, 6ac37ec5c112f0bccdfc1fe0. Paste your project link in the stream for visibility.
- **Govind Verma** · 7 Oct 4:27pm: "Hello"
- **Gaurav Boob** · 7 Oct 8:44pm (reply): "if anyone is interested, plz dm"
- **Vaitheeshwar Badrinarayanan** · 10 Oct 4:51pm: UL students looking for teammates, all universities welcome.
- **Viktoriia Bielhrad** · 12 Oct 4:07pm: computing student at TU Dublin, looking for teammates.
- **Kartik Gupta** · 12 Oct 4:55pm: Comp Eng master's, TCD, looking for 2 more teammates. 11:12pm: chat inbox issue, acknowledge here if sending a join invite.
- **Adé BeMyApp** · 13 Oct 10:55–10:58am: if your team is looking for people, paste the link. Hiring teams: #/projects/6ac7547a852019b558eeebe6, 6acce6d2852019b558f4edfd, 6acbf1d8852019b558f2e1a1, 6acba96f852019b558f2943a, 6acabdf5852019b558f263aa, 6ac4e0d9c112f0bccdfcc95c, 6ac39b50c112f0bccdfc3d73, 6ac39865c112f0bccdfc3b09, 6ac37ec5c112f0bccdfc1fe0. "DM them, click on apply, respond to this thread, ask the admin to add you to one."
- **Adé BeMyApp** · 14 Oct 11:23–11:34am: more links: 6ac817aa852019b558f057a0, 6ace07b0852019b558f7e6de, 6ac7547a852019b558eeebe6, 6ac64420c112f0bccdfd83e9, 6ac39b50c112f0bccdfc3d73, 6ac37ec5c112f0bccdfc1fe0.
- **Adé BeMyApp** · 14 Oct 2:27pm / 2:28pm (replies): "do you want me to add you teammates? DM me" / "what is you team?"
- **Gaurav Boob** · 14 Oct 3:24pm: team complete. Team name: Latent Space.
- **Adé BeMyApp** · 16 Oct 10:40am: 3-member minimum reminder (same as the Announcements post). 10:46am: team #/projects/6acff8a1b8e7dd902b99e4a6 is missing 1 person.
- **RHEAN XAVIER** · 16 Oct 11:14am: "hi". **Divya Sunil Raj** · 11:15am: "hi"
- **Adé BeMyApp** · 16 Oct 2:46pm: create your team and invite teammates, or check the Teams tab for who's hiring.
- **Nathan Alvares** · 18 Oct 2:32pm: MSc AI (NCI), Dublin. Strengths: strong baselines, clean offline evaluation, keeping models inside a compute/runtime budget. AI lead on FinPal (2nd, Citi upStart Dragons' Den). Backend agent for The Coaching Engine (TechIreland AI Challenge, Vertex AI/GCP). Ex-FlyRank AI intern. New to recommenders. github.com/IronNathanAlvares
- **Radu Lecca** · 18 Oct 5:28pm (two earlier messages deleted): Masters in Cyber Security, UL. No team yet.
- **Darshil Parikh** · 19 Oct 11:48pm: MSc Data Science & Analytics (UCC), looking for a team. 11:49pm: suggests he, Radu and Nathan team up.
- **Nathan Alvares** · 20 Oct 11:37am: agrees.
- **Adé BeMyApp** · 20 Oct 1:23pm: "Nathan, Radu, and Darshil, your project is created!" (became **EirHack**)
- **Leonarda Vuković** · 20 Oct 3:10pm: MSc Software Development (UL), mostly games projects so far, looking for a team.
- **Kandarp Joshi** · 20 Oct 11:32pm: Master's in Data Science, Maynooth, looking for a team.
- **Vishranth Vijayakumar** · 22 Oct 8:58pm (reposted 27 Oct 11:01am): **SignalRank is hiring 2–3 teammates.** Wants Python/pandas and comfort with large CSVs. Bonus: LightGBM/XGBoost tuning, PyTorch, sequence recommenders (SASRec, GRU4Rec). Students in Ireland. Process: shared repo, one results log (**local AUC vs public AUC**), "**We plan the 10 daily submissions together**", no blind uploads. Registration closes 30 Oct.

## Networking: Need help? Ask the Admin (36)

- **Jianqiao Xu** · 7 Oct 11:10am: teammate accidentally created a team, how to delete? → **Adé** 2:35pm: give the team name to delete.
- **Kartik Gupta** · 7 Oct 4:29pm: how to add a participant? → **Adé** 4:48pm: paste the link, or add the friend's username on your project page. The friend must be registered.
- **Kartik Gupta** · 11 Oct 6:15pm: join applications expire and the messages vanish, can the time limit be extended? → **Adé** 12 Oct 1:23pm: extended, "now you have 12h to respond to the applicant."
- **Joshan John** · 11 Oct 10:23pm: has a team, anyone want to join?
- **Vaitheeshwar Badrinarayanan** · 12 Oct 11:57am: UL students looking for teammates.
- **Chidozie Onyejelem** · 13 Oct 1:15pm: is there a team deadline? → **Adé** 14 Oct 11:22am: "The deadline will be the 21st at the start of phase 1."
- **Rahul Raj** · 15 Oct 12:11pm: trouble downloading "Tech Arena Ireland - Challenge 2026.pdf"? → **Adé** 16 Oct 10:37am: "I will create a new tab for you to access it!" Then 10:53am: "you can download the PDF in the resources it's on the front page."
- **Aswinkumar Varathakumaran** · 15 Oct 4:25pm: please delete team TCD Top K → **Adé** 16 Oct 10:37am: deleted.
- **Adé BeMyApp** · 16 Oct 10:40am: 3-member minimum reminder.
- **IQRAM HOSSAIN FAHAD** · 16 Oct 11:06am: has 6 other members but got an email saying not enough members.
- **Brown M** · 16 Oct 11:08am: MPhil Engineering, TCD. CV, deep learning, AI infra, generative recommender systems. Looking for a team.
- **Darshil Parikh** · 20 Oct 8:17am: no team yet, can't see anything in the lobby. → **Adé** 9:28am: "I am going to create a team for you."
- **Nathan Alvares** · 20 Oct 11:44am: no teams show on the Teams page. → **Adé** 1:19pm: offers to create a team with Darshil and Bu.
- **Vaibhav Prakash** · 21 Oct 9:40am: [deleted]
- **RHEAN XAVIER** · 21 Oct 11:48pm: "Hi where do i find the data sets for phase 1?" → **Adé** 23 Oct 12:42pm: "**you need to be registered on the platform and part of a team to have access to the dataset.**"
- **Vishranth Vijayakumar** · 22 Oct 9:11pm: can't add members, it says full but he's the only one. → **Adé** 23 Oct 12:41pm: emailed him, make sure teammates are registered.
- **Euan Smith** · 23 Oct 2:28pm: accidentally made a team called "Test" while exploring, please delete. → **Adé** 5:22pm: "it's done!"
- **Vaibhav Prakash** · 27 Oct 12:07pm: certificate issue on the platform-techarena* domain, ERR_CERT_COMMON_NAME_INVALID, cert is only *.virtualconference.com.
- **Maksim Viktorov** · 27 Oct 2:02pm: "could you please remove me from the team I currently in. I think I was accidentally put in there."
- **Euan Smith** · 27 Oct 2:03pm: "could you register two teammates for me: [two TUD student emails, redacted], and if possible add them to my team, Technological University Dublin?"
- **Euan Smith** · 27 Oct 2:24pm: "and also: [a TCD student email, redacted]" (this became Dilan Uthan, now on the team)
- **David Omozokpia** · 27 Oct 3:09pm: "may you please delete my team as I made it preemptively Vincables please"

## Our team room: Technological University Dublin (3)
- 27 Oct 1:55pm: system message "Welcome to your project chatroom! New team members will be automatically added to this room."
- 27 Oct 1:56pm: "Jai Depuru has been added to the project"
- 27 Oct 1:56pm: system welcome message

## DM: David Omozokpia (5)
- **David** · 23 Oct 2:36pm: Hi
- **Euan** · 2:37pm: Hey David / Wanna make the team? / Then drop the invites?
- **David** · 2:39pm: Yeah


# Opening webinar: live Q&A and chat (Talks tab, scraped 27 Oct 2026)

From the talk page `#/talks/6ac66979c112f0bccdfe1455` ("Huawei Tech Arena Ireland 2026 - Opening Webinar", 4 speakers: Daniel Dunne, Adé (BeMyApp), Gabor Fodor, Gonzalo Fiz Pontiveros, 45 viewers). Slides attached on that page: *Tech Arena Ireland - Challenge 2026.pdf*.

## Questions tab (with upvotes and written answers)
Most questions were answered out loud in the stream; Adé marked them "hope it's clear for you :)". The spoken answers are in [../webinar/transcript.md](../webinar/transcript.md).

| Asked by | Question | Votes | Written answer |
|---|---|---|---|
| Sadman Araf | Can we keep submitting multiple times like Kaggle, to improve our leaderboard score? | 1 | **Gabor Fodor: "yep! 10x/team/day"** |
| Sofiia Khomiak | What kind of review will there be? Will we have to defend the work for phase 2? | 1 | (spoken) |
| Joshan John | Where can I find the briefing or any helpful documents for phase 1? | 4 | (spoken) |
| Oluwafarayolami Olanbiwoninu | What are the evaluation metrics for judging the top 15 teams? | 0 | (spoken) |
| Sadman Araf | If evaluation is only CSV submissions, are we judged only on the metric score, not the approach? | 0 | (spoken) |
| Smit Savaliya | How are the top 15 selected for phase 2: results only, or does submitting first count? | 1 | (spoken) |
| Minh-Khoi Pham | Is the phase 2 submission a CSV as well? | 1 | (spoken; transcript: no, phase 2 is not CSV) |
| Gregory Korchagin | How will latency be benchmarked: per request or across batched evaluation? Is there a hard millisecond limit? | 0 | marked answered |
| Oluwafarayolami Olanbiwoninu | Must the project topic come from a specific area, or is any area fine? | 0 | marked answered |
| Zicheng Liang | Will you provide compute (VMs, cloud credits), or do we use our own devices? | 1 | marked answered |
| James Penrose | What is the attitude to using AI coding agents? | 0 | marked answered |
| Gregory Korchagin | Will the dataset include item metadata (titles, text, visual features, categories), or only interaction logs (user_id, item_id, timestamp)? | 0 | marked answered |
| Viet Pham | Simulated users give feedback to the recommender. How do you build simulated user behaviour that matches real scenarios? | 0 | marked answered |

**Confirmed in writing:** 10 submissions per team per day (Gabor Fodor, Huawei mentor). This upgrades the "10/day" figure from unofficial to official.

## Chat tab (visible part)
- Adé: "Oct 21st!" (Phase 1 start date)
- Asif Ur Rehman Shaik, twice: on what basis / parameters is our score evaluated?
- Shay Power: will this stream be available for later viewing?
- Then thank-yous from attendees (Hector Valle, Zicheng Liang, Puneet Warathe, Vladyslav Yaroshovets, Rachel Keaveney, Chidozie Onyejelem, Alfia Naaz, Alfred Folashade, Xu Chen, Divya Sunil Raj, Vladyslav Brykaylo, Maria Fernanda Alvarado Rivera)
- Adé: "replay is available 🚀"



---

# Our team + event info (scraped 27 Oct 2026)

## Team: Technological University Dublin
- Project id: `6ae0ab2f95692e10fb826ab8` (Draft, not published; this keeps outsiders from applying)
- Tagline: "TU Dublin team tackling Huawei's challenge"
- Links on the team page: euansmith.net, tudublin.ie
- Free section text: "test" (placeholder, replace it)
- Hiring tags: none
- **Members (4 as of 27 Oct 4pm, marked complete):**
  - Euan Smith (@Professor_Larp): creator
  - Samuel Oni (@Samuel)
  - Jai Depuru (@Jai Depuru): added 27 Oct 1:56pm
  - Dilan Uthan: added by the admin 27 Oct afternoon (registered from the TCD email Euan sent at 2:24pm)
- Team chat room id: `6ae0ad3395692e10fb82713b` (only system messages so far)
- **Still pending (27 Oct 4pm):** at 2:03pm on 27 Oct Euan asked the admin to register [two TUD student emails, redacted] and add them to the team.

## Where the rest of the WhatsApp group is
- **David Omozokpia:** on his own team **"Vincible's"** (1 member). The platform needs 3, so that team can't compete as it stands.
- **Maksim Viktorov:** listed in **"[Team 1]"** with Aayush Rokade, David Zhai, Dhruva Chaklasiya and Sakshi Chitnis. At 2:02pm on 27 Oct he asked the admin to remove him ("accidentally put in there").

## Mentors (4)
- Daniel Dunne: Orga Team
- Cici Wang: Orga Team
- Gabor Fodor: IT Mentor
- Gonzalo Fiz Pontiveros: IT Mentor

## Talks (1)
- **Huawei Tech Arena Ireland 2026 - Opening Webinar** (14 Oct, 1pm). Speakers: Daniel Dunne, Adé (BeMyApp), Gabor Fodor, Gonzalo Fiz Pontiveros. The replay and slides are in the Talks tab: `#/talks/6ac66979c112f0bccdfe1455`

## Event → Resources
- *Tech Arena Ireland - Challenge 2026* (slides from the opening webinar): PDF
- *Tech Arena Ireland - FAQ 2026* ("will be updated frequently"): PDF. **Re-check this before the deadline.**

## Platform quirks
- A team can't be deleted by its members. Ask the admin by team name.
- Join applications expire after 12h (the admin extended this on 12 Oct).
- A cert warning (`ERR_CERT_COMMON_NAME_INVALID`, cert is for `*.virtualconference.com`) was reported 27 Oct. curl hits the same mismatch.


# Other teams' project pages (scraped 27 Oct 2026, ~4pm Dublin)

Three published team pages, scraped in full. Each of these teams has a **"Go to the Challenge"** button on its page. It points to `portal-techarena-ireland.hackathon.com/auth/<per-team token>`. Our team page does **not** have this button yet (checked the same afternoon), so the admin still has to give us portal access. The per-team tokens are left out of this repo on purpose: they are other teams' logins.

## HACKBOTS (`#/projects/6acd2057852019b558f5eef6`)
- **Tagline:** "Smarter recommendations, leaner compute."
- **Challenge portal button:** yes
- **Hiring:** yes ("Apply now")
- **Members (4):** Kushal Madappa, Maksudur Rahman, Manas Sharma, Atreya Amrita
- **Page text (summary):** they frame recommender systems as what shapes app stores, browsers and feeds, and say Huawei's **CBG Cloud Service** team works on these problems. Their four team rules:
  1. Baseline first, cleverness second.
  2. If it doesn't fit the compute budget, it doesn't ship.
  3. Every idea gets an experiment, and every experiment gets a number.
  4. Divide the work, but review each other's code.

## WHAT?! (`#/projects/6ada05c6262396ec8270b20c`)
- **Tagline:** "TechArena 2026"
- **Challenge portal button:** no
- **Hiring:** yes
- **Members (2):** Abdullah Khan, Wasif Qazi (below the 3-member minimum)

## SIGNALRANK – Lean Sequential Recommender (`#/projects/6ada32091528549c78789e30`)
- **Tagline:** "Fast, compute-aware recommender predicting next clicks from user histories under runtime limits."
- **Challenge portal button:** yes
- **Hiring:** yes
- **Members (1):** Vishranth Vijayakumar. He told the admin on 22 Oct the platform says his team is full although he's the only member (see chats.md). This is the team whose recruiting post gave the unofficial AUC / 10 submissions a day / large CSV details.

## What this tells us
- Having the portal button doesn't depend on team size: SignalRank has it with 1 member, WHAT?! doesn't with 2. It looks like the admin enables it per team.
- **Action:** email adelaide@bemyapp.com asking her to enable the challenge portal ("Go to the Challenge") for team Technological University Dublin.


<!-- Sent to the Winners WhatsApp group by Euan, 27 Oct 2026 3:00pm. Team status here was correct at that time; Dilan Uthan joined the TUD team later that afternoon. -->

*Huawei Tech Arena: key info (platform + opening webinar)*
Full scrape + webinar transcript in the repo: platform-scrape/ and webinar/transcript.md

*Team status on the platform*
- Team "Technological University Dublin": Euan, Samuel, Jai (3 members = eligible)
- David: on a solo team "Vincible's" (1 member, ineligible). Needs deleting by the admin, then join TUD team
- Maks: listed in "[Team 1]", removal requested 27 Oct. Join TUD team after
- Atharva: admin asked 27 Oct to register (TUD email) + add to TUD team
- d: not on the platform yet. Register at platform-techarena-ireland.hackathon.com
- Registration closes *Fri 30 Oct*. Team size 3–8
- Admin: Adé, adelaide@bemyapp.com (email is faster than platform chat). Teams can only be deleted by the admin

*Dates*
- Phase 1: 21 Oct – *Wed 4 Nov 11:59pm* (Dublin)
- Selection 5–6 Nov, top 15 announced 9 Nov
- Onsite final: Sat 14 – Sun 15 Nov, Clayton Hotel Cardiff Lane. Top 8 present on day 2
- Prizes: 1st €6,000 · 2nd €2,500 (2 teams) · 3rd €1,500 (3 teams), split per member. Finalists get Huawei intern interviews

*Challenge*
- "Competing in the Intelligent Marketplace: Generative Sequential Recommendation Under Constraints"
- Learn from user interaction histories and predict user responses. Multiple agents compete for attention. Quality balanced against compute/runtime limits
- Phase 1: submit a CSV via the submission portal in the team space
- Metric: a form of AUC
- Public + private leaderboard split, so the public score can't be gamed
- No evaluation time limit in Phase 1
- Phase 2: not CSV. Wall-time limit on the whole test set (not per batch), run on GPU machines
- How the dataset was generated won't be revealed. Any technical approach allowed
- Unofficial (another team's post): 10 submissions/day, large CSVs. Common approaches: LightGBM/XGBoost, SASRec, GRU4Rec

*Blockers*
- Dataset only visible once registered + on a team. Not showing in the TUD team space yet
- Challenge 2026 slides + FAQ 2026 PDFs: Event page → Resources. Not in the repo yet (FAQ is updated regularly)

*Mentors:* Daniel Dunne, Cici Wang, Gonzalo Fiz Pontiveros, Gabor Fodor



---

# All teams on the platform (94, scraped 27 Oct 2026)

Format: **Team** [size, complete/published flags]: members. "Complete" is the platform's own flag. The minimum team size is 3.

- **P3 Labs** [3]: Leah Weldon, Faith Olopade, Martha Ryan
- **CSDT** [4, complete]: Danyal Aftab, Maruf Hassan, Assadig Abakr, Shushanta Pudasaini
- **LocalPulse** [5]: Haoshan Tian, Rui Xu, Sixu Li, Chuan Zhao, Zeyu Shan
- **Latent Space** [4]: Gaurav Boob, Himanshu Baheti, Gregory Korchagin, Charly Martin-Avila
- **AlphaSeq: Reading Behaviour Like Language** [8, complete]: Vaitheeshwar Badrinarayanan, Nimra Zia, Krithikaa Jothi Prakash, Kavyashree S L, Vanessa John Lobo, Tushar Toteja, Sai Rahul Guggilam, Feifan He
- **NextToken** [4, complete]: Kartik Gupta, Govind Verma, Yash Chaudhary, Aayushi Jain
- **prjkt-avengers** [4]: Likun Fang, Zijing Zhang, Yan Liu, Kai Yang
- **BotProof Rec** [3]: Oluwafarayolami Olanbiwoninu, Sharukkhan Mubarrak, Unubold Enkhsaikhan
- **Cybots** [4, complete]: Rahul Raj, Saiskanda Devatha Ramkumar, Thuso Sehloho, Arya Rana
- **404 Found** [7, complete]: Xu Chen, Iqram Hossain Fahad, Phuong Nguyen, Mohit Pradhan, Liang Wenbo, Vishak Siva Narayanan Priya, Anas Abdelhadi
- **Six Six Six** [3, complete]: Jianqiao Xu, Kaiwen Yao, Junyang Zhang
- **ChaiScript** [3, complete]: Kevin Sabu, Edwin Shibu Mathew, Surya Kiran
- **SAFA** [4]: Frida Gurn, Aoife Guanzon, Stephanie Dias Carvalho, Ami Yadav
- **Hot Potters** [4, complete]: Leon Andorfi, Hong Shen, Pardis Yavari, Hailan Jin
- **Elevated** [4]: Tarannum Ahmed Nowshin, Md Asif Rahman, Sadman Araf, Mohammad Fahim Haque
- **PAHASHI** [2]: Hara Bhuvan Choula, Pavan Kolasani
- **NextClick AI** [5, complete]: Smit Savaliya, Cariappa MK, Nilesh Barge, Jenil Parmar, Chetan Panchal
- **DCU_IT** [5, complete]: Yevhen Hrynenko, Sofiia Khomiak, Roman Luhovskyi, Vladyslav Yaroshovets, Nazarii Heiko
- **cvg** [1]: Vinit
- **Conversion** [4, complete]: Milo Dennehy, Erwan Brennan, Kilian Corcoran, Danila Macijauskas
- **EVA-1** [4, complete]: Hector Valle, Maria Fernanda Alvarado Rivera, Vijay Sharmith Palani, Jeremiah Jabavu
- **Rustaceans** [3]: Ilie Gabuja, Artem Lytvyn, Dmytro Hryshchenko
- **3 coders** [2]: Radha Kumar, Annie John-Edwin
- **Rock On** [4]: Anuja Jayakumar, Sharif Al Musfique, Manthuri Hrithikesh, Kartikeya Sharma
- **Ping Panthers** [6]: Adam Nedic, Sergiu Manea, Ayomide Kukoyi, Edmund Akinfaye, Alfred Folashade, Somto Onwubuilo
- **G2 Larpers** [5, complete]: Denis Maftei, Mateusz Kietrys, Alexander Chiper, Bernard Uzo, Vladyslav Brykaylo
- **Team Signal** [6, complete]: Vaibhav Prakash, Tom Kinane, Suhas Kalyan Kumar, Menaal Nadeem, Lama Mesmeh, Anthony Coughlan
- **Slopify67** [3]: Ibraim Abduramanov, Ilias Kubedinov, Tahir Djian
- **Rererererecommendation** [4, complete]: Zheng Liu, Jiawei Zhou, Jiajun Xiao, Derek Pun
- **MYS** [3, complete]: Yu Xuan Low, Chong Yao Ong, Yu Hung Leong
- **Orbit** [2, complete]: Imtiaz Ahmed Butt, Muhammad Turab
- **HalluciNation** [5, complete]: Joshan John, Mert F. (Matt) Gunes, Xiang Yu Oon, Abdul Rafay Khurram, Puneet Warathe
- **Alpha** [3]: Anush Serrao, Rupam Misra, Harshal Rajendra Ahire
- **TCD Top-K** [5]: Stefan Dsouza, Michael Regan, Aswinkumar Varathakumaran, Liam Coveney, Alexander Høyskel
- **SmartRec** [5, complete]: Viktoriia Bielhrad, Yasashwi Srinivasa Gowda, Jade Louarrhi, Sajjad Hussain, Dhurgham Alsaadi
- **free lunch** [3, complete]: Thang-Long Nguyen-Ho, Minh-Khoi Pham, Thi Thuy Nga Le
- **HackBots** [4, complete, published]: Kushal Madappa, Maksudur Rahman, Manas Sharma, Atreya Amrita
- **Algorithmic Frontier: Generative Sequential Recommendation** [5, complete]: Rhean Xavier, Sujay Tasgaonkar, Divya Sunil Raj, Saurav Sarkar, Ishaan Shanavas
- **Ctrl + Predict** [6, complete]: Monish Obaid, Abdul Gaffar Nagori, Arman Pathan, Daksh Jadhav, Maksym Chechotkin, Rajesh Yadav
- **VNAI** [6, complete]: Viet Pham, Hoang Tu Bui, Tung Tran, Van Nguyen, Duc-Hai Nguyen, Tien Duc Anh Vu
- **tud builders** [6, complete]: Yusuph Darbo, Andres Arturo Lopez Lozada, Jake O'Reilly, Sandijs Buls, Denys Lazorenko, Haseeb Lateef
- **Hackathon project** [3]: James Penrose, Oisín Foy Long, Kyle Joyce
- **the recommenders** [8, complete]: Ivan Kenneth Bibat, Xiang Le Lam, Noveno Andrew Martin, Vaibhav Revankar, Pawan Venugopal, Laurin Burge, Mohamed Elmofti, Jana Stachova
- **The Clanker Program** [4, complete]: Matas Zostautas, Paul Adelabu, Pushkar Konasirasgi, Chidozie Onyejelem
- **FeedForward** [2, complete]: Elif Gul Abdul Halim, Kamil Abdullah
- **Triple T** [3]: Fedoriv Makar, Oleksandr Yarovyi, Vlastimil Finger
- **No Ideas** [6]: Aoibh Griffin, Alexander TippMcKnight, Gianna Estrada, Kenneth Okafor, Tolu Eniola, Killian Hewson
- **[Team 1]** [5]: Aayush Rokade, **Maksim Viktorov**, David Zhai, Dhruva Chaklasiya, Sakshi Chitnis
- **[Team 2]** [5]: Yong Huang, Alfia Naaz, Gabriel Fortini, Harshita Dinesh Kumar, Hetha Putane
- **Learn2Rec** [5]: Mohammad Amaan, Shengxin Chen, Huan Chen, Sourav Mahajan, Sankalp Jain
- **[Team 3]** [7]: Meliena Jane, Muhammad Yameen, Ritisha Sethi, Jyothi Kakarla, Aisosa Edobor Yemi, Jerome Tigi, Sri Pujitha Mittapally
- **this is our awesome project** [7, complete]: Ali Assylkhan, Daria Osypova, Daniil Oliinyk, Roman Bochuliak, Sofiia Diachenko, Oleksiy Tabachyn, Luka Paranyak
- **Overfitters** [6]: Asif Ur Rehman Shaik, Abhinav Karforma, Aditya Bhavsar, Naveen Painuly, Gaurang Rajendra Kumbhar, Sahil Ichake
- **Success** [3, complete]: Rachel Keaveney, Elizabeth Schmidt, Megan Fondja
- **This is our really awesome project** [1]: Filips Talums
- **Learn2RecSmartly** [7]: Adarsh Arun, Luka Cvetkovic, Nihal Mala, Tanmai Hanumanthu, Anish Achar, Shaurya Kapoor, Mithil Mudholkar
- **Match AI** [3]: Vaishnavi Kannan, Swastik Bhattacharya, Ramakrishnan Veerasamy
- **MUnemployed** [4, complete]: Andrei Seleznev, Ernest Warzuchowski, Mario Andrus, Rudransh Arora
- **THUNDERSTORM** [3, complete]: Nawazish Ali, Omar Farooq Khan Suri, Xiaowei Hou
- **GCDstud** [3]: Shakibul Hasan, Abhilash Kaim, Rushyendra Kankanala
- **Huawei TUDers** [7]: Tan Winnie, Abraham Tukai, Rishi Prasanthi Shabil, Marcela Poli, Elvita Paskauskaite, Eoin Flores Leslie, Ife Ojo
- **Bubly** [6, complete]: Vedant Ambre, Sairaj Bandre, Sakshi Dhamane, Prachiti Palande, Priyansh Koli, Gautami Sachidanand Bhat
- **KFC Operations** [5]: Favour O Efe, Vince Kirk Ramilo, Atalanta Madzikwa, Andile Ngwenya, Chiara McGuire
- **SSS** [3]: Semyon Fox, Erifeoluwa Jamgbadi, Aquline Benny
- **TCD Rec.Team** [8, complete]: Agastya Kataria, Andrew Rotaru, Parsia Jahanzad, Tianfang Zhou, Mark Kelly, Fionn Gavin, Rory Nolan, Aaron Murphy
- **TCD Team 2** [4]: Julian Young, Bobbi Beattie, Maxim Melnyk, Amogh Gaikwad
- **Team DCU** [4]: Antoine Iglesias-Tallon, Steeve Sunny, Arya Rana, Eben John Valson
- **UnevenGradient** [3, complete]: Nikhil Verma, Saurabh Yadav, Kandarp Joshi
- **SPB** [3, complete]: Aditya Sharma, Harshwardhan Dongare, Pushkar Upadhyay
- **Nova** [3]: Davit Gamjashvili, Susan Ogozi, Chioma Ugwunze
- **Epsilon** [7]: Hemanth Thandayamparambil Sunil Kumar, Giljohn Preeju, Prarthana Manju Pradeep, Henok Jackson, George Daniel Thomas, Aswin Kuruvath Jayan, Tharunkrishna Nair
- **Trinners** [7, complete]: Sinclair Philandrianos, Tadhagath Marepalli, Vlad Manea, Colin Marshall, Muskaan Siddiqui, Lucas Perez, Callum Hughes
- **UCD Connect** [4]: Diana Shustrova, Steven Mascarenhas, Bhavya Lanka, Shruti Mane
- **Code?** [4]: Maria-Cristina Mandila, Mitesh Uttekar, Vaishnav Malvankar, Mandar Sawant
- **M-IRE** [3, complete]: Alexander Mutua, Dev Dharsandiya, Hasmina Juthy
- **TUD_Hackat** [3]: Armand Meijers, Allen Lapitan, Aaron Sequeira
- **UL4** [4, complete]: Manu Vashisth, Hersh Singh, Shay Power, Dhiraj Kumar Singh
- **The Dark Triad** [6, complete]: Stepan Tretiakov, Vsevolod Shestopalov, Roman Oleinikov, Mykyta Doroshenko, Pavlo Panchenko, Vladyslav Shkurina
- **PROMETHEUS** [3, complete]: Kyrylo Hadetskiy, Vladyslav Yaskovets, Mykola Karamyshev
- **Git happens** [6]: Rahman Syed, Aparna Sivasailam, Safiya Syed, Raja Gopal Bhaviri, Ridhika Sahni, Dongbo Wang
- **EirHack** [4, complete]: Nathan Alvares, Darshil Parikh, Radu Lecca, Leonarda Vuković
- **RO's** [3]: Amos Farcau, Alexandru Belecci, Andrei Scarevnea
- **UOG** [6]: Shubhankan Sharma, Aniket Birle, Mohammed Maaz, Bhumika Garigela, Anurag Patidar, Kavya Mallikarjunappa
- **YLZ** [3, complete]: Luo Yang, Keyu Zhu, Yuhang Lian
- **Beyond Baseline** [3]: Kouider Chadli, Kavach Dheer, Damian Wlodarczyk
- **Vitality** [3, complete]: Iulian Ursu, Emil Maritoi, Khabiba Saikenova
- **Him-An** [3, complete]: Yash Sarda, Vedant Rathi, Bhavishya Khanchandani
- **What?!** [2, complete, published]: Abdullah Khan, Wasif Qazi
- **SignalRank – Lean Sequential Recommender** [1, complete, published]: Vishranth Vijayakumar
- **Yeepee** [2, complete]: Chenming Tao, Brown M
- **Vincible's** [1]: **David Omozokpia**
- **limerick_legends** [3]: Shardul Singh, Arvinder Singh Dhoul, Ritika Saini
- **Legion** [3]: Surya Prakash Vellingiri, Lohith Kumar Prem Kumar, Meghna Rathish
- **Technological University Dublin** [3, complete]: Euan Smith, Samuel Oni, Jai Depuru ← us

## Quick read
- 94 teams total. 10 have fewer than 3 members, so they can't compete as they stand.
- TU Dublin teams besides ours: **tud builders** (6), **Huawei TUDers** (7), **TUD_Hackat** (3). Viktoriia Bielhrad (TU Dublin) is on **SmartRec**.
- The names suggest most teams are aiming at sequence models: NextToken, AlphaSeq, Learn2Rec, Algorithmic Frontier: Generative Sequential Recommendation.



---

# Opening webinar transcript (14 Oct 2026, 43 min)

Source: Talks tab #/talks/6ac66979c112f0bccdfe1455. Speakers: Daniel Dunne, Adé (BeMyApp), Gabor Fodor, Gonzalo Fiz Pontiveros. Auto-transcribed with whisper-large-v3-turbo, so names and numbers can be misheard; check against the slides.

**[00:00]** Welcome everyone to TechArena Ireland 2026. Happy to see you all connected during your lunch break. So I hope everything is smooth. Without further ado, I would like to welcome Daniel Thun, Senior Technical Procuretor from Huawei Ireland Research Center to give us a few words. Thank you Addy. Good day everyone and welcome to Huawei Ireland TechArena 2026 webinar. It's great to have you all here today and especially to see so many students across Ireland interested in technology, innovation and of course a bit of competition. Before we get into the challenge itself, I'd like to just take a few moments to tell you a little bit about Huawei, our presence in Ireland and why initiatives like TechArena are important to us. So Huawei was founded in like 1987 and today is a global technology company with more than 194,000

**[01:01]** employees operating in more than 170 countries and regions and serving billions of people across the world. But Huawei is not just a global tech company operating in Ireland. We've been part of the Irish technology ecosystem for over 20 years and Ireland has become an important location for our research and innovation activities. Today Huawei has three R&D centers in Ireland, in Dublin, Cork and Athlone, where our teams work on cutting edge areas such as AI, cloud computing, big data and ML. We also collaborate closely with Irish universities and research institutions helping to strengthen the connection between academic research and real world technology development. And that's where today's event becomes particularly exciting for you. At Huawei we believe that innovation happens when talented people are given the opportunity to solve meaningful challenges. That's why we invest in initiatives like

**[02:05]** TechArena to bring together students, researchers and industry experts and to give you the opportunity to demonstrate your creativity, problem solving skills and technical ability. TechArena is also an important part of how we identify and hire interns and engineers. So every year we look for talented students who could become the next generation of engineers or researchers at Huawei. Many of our teams are built by people who started their journey through university projects, hackathons, internships and research collaborations. So for everyone participating today, this is not only a competition, it's also an opportunity. It's a chance to showcase your skills, challenge yourself, work as part of a team and create something that you can be very proud of. It's also a great opportunity to strengthen your CV, gain experience working on a real industry challenge and connect with Huawei experts and researchers who may become part of your future career. So my advice is simple,

**[03:10]** like don't just focus on winning, focus on learning, collaborating and making the most of the experience. Bring your ideas, support your teammates and don't be afraid to experiment. These are exactly the qualities we look for in future hires. So in a few moments, our senior expert will talk you through the challenge itself and everything you need to know from a technical perspective. So with that, welcome once again to Huawei Ireland TechArena 2026. We're really looking forward to seeing your ideas, your creativity and your innovation. Good luck and enjoy the experience. Adi, you're on mute. Yeah, it's the story of my life, I think, always on mute. So yeah, I was about to say thank you, Daniel,

**[04:11]** for the introduction. And now I should have plenty of info for you before we jump into the challenge. So we're going to talk about the timeline, the challenge, the competition phases, the prize pool, and the next step, because we have some next step after this webinar. So I'll go right in. So the timeline, I think it's a tiny, tiny, but I'll share it again. It's on the platform, you have access to it. So we kick off phase one, upon October 21st, and it will last until November 4. November 4, not midday, midnight sharp, we stop, we cut the portal access. So you can't upload a last, a last submission. So they will get to it afterwards, they will let you know everything. But yeah, phase one, if from October 21st and until November 4, we'll have a few days buffer to review all the submissions,

**[05:16]** even though you will have a little board, we want to review and make sure that there's no cheat in any case. And then we will have them announce the top 15 teams that will make it to phase two in Dublin, upon November 9, so that you can prepare your trip, whatever, your journey on the on-site hackathon that will be on November 14th and 15th. And the award ceremony right after November 15th, on the same day. So that's for the timeline. And the challenge, competing in the intelligent marketplace, a generative sequential recommendation and the constraints. I'm not going to be the one to explain to you the challenge clearly. But I will let the place to Gonzalo and Gabor, team lean and data scientists in the Huawei Ireland Research Centre to take the lead and explain the challenge in all its tiny details. Welcome in Gonzalo and Gabor. Thank you very much, Jade. Let me just do the sharing screen thing.

**[06:22]** There we go. All right, you should all be seeing my slide now. One second. Good. So, hi, well, good everyone. So I'm Gonzalo. I'm a team leader at the AIRX in the Huawei Ireland Research Centre. So our team works on search and recommendation and our mission is to advance the research and turn new ideas into technologies that can create value in Huawei products and services. So helping people discover useful things is basically very much central to our work. Is that, am I being heard? Everybody's frozen for me. Yeah, we can hear you fine and we can see the slides. Don't forget to. Very good. Very good. Okay. All right. So in this presentation, basically the idea is to try to give a bit of a motivation why recommendation systems are important, what the challenge is about, and specifically we're looking at something that I call sequence models and basically how these,

**[07:26]** how the two competition phases will basically put these ideas into practice. Okay. So let me, one. So, so we're going to have kind of try to set a specific example. So imagine that you have, you know, you're opening some kind of music service like Deezer or Spotify and you have an enormous catalog of music, right? So this means that it's impossible for a user to actually browse the entire catalog and decide what to listen to. It's just too big. So the idea is that you have a system that somehow learns from the history, the user's history, listening history, and basically retrieves a relevant item, which he recommends to the user. So this is very useful for everyone. So the users find what they want. The platforms are basically make sure they offer a good experience for the, for the use themselves. And the creators basically find their audience. So this problem, by the way, is not just

**[08:29]** about, I mean, we're going to center around music here because I think it's, it anchors quite well, the problem, but you know, it's a, it's a similar problem that happens in e-shopping, in video, like TikTok and so on, news and many, many digital services. So it's actually very much ubiquitous technology. Okay. So there's one, one, basically one share idea between kind of interpreting the history of the user and actually language models, right? So these are all can be interpreted as basically sequential models. So the idea here is that given the, the words on my way, you try to predict what the next word is. In this case, you know, home might be a likely example. And this, you can do kind of the same thing, but with that, with the user's history. So you can look at, you know, the jazz, he's his history. So he played a jazz, a jazz track. He then skipped a dance track, and then he replayed an acoustic set. And then the idea is, okay, what would you offer then next? Right?

**[09:32]** So the way we think about this problems is through this basically factorization, this conditional probability. And this is, by the way, this is essentially what, what we're trying to model with a sequential model. This is something that if any of you have looked at transformers or language models, you'd be extremely familiar. I think at this point, this is probably the most written down equation in the history of mankind. And okay, so how do this LLM, so again, this language model, sequence models, motivate this scalable receptor recommendation. So there are kind of three main components to sequential recommendation. The first one is representing the events, right? So we need to construct what these events here, you know, what are steps in the process. And this is actually kind of a very important, very, very important aspect, right? So we decide what counts as an event, what counts as a token. Then there's the modeling part, so basically learning

**[10:35]** from the histories, understanding how, you know, the conditional prediction behaves. And then a third part, which is kind of more relevant to, I guess, to industry, but it's particular to us, is basically how to scale and measure. So you have to imagine now that, you know, you're scaling this to millions and millions of different potential items. History lengths are very, very long. And all of this has to be done in basically, so that the inference that needs to happen in milliseconds, right? Now, I mentioned here, this kind of three milestone kind of papers. So this SASREC, this is one of the kind of quintessential baselines for sequential accommodation systems. And so it was like the first, I think, well, I don't know, but among the first to bring this attention over writing histories, so having an intentional transformer type model in recommendation. Then this tiger, this from Google back in 2023. So here, they actually worked on this event

**[11:38]** representation. So the key here is that instead of having items as kind of a single item ID in your system, you want to use something I call semantic IDs, which are basically a way to relate one item to the next in a kind of semantic way. And finally, we have this HSTU, which is a paper from Meta. And in this paper, they really focus on this very, very large, very long sequencing. So we're making this analogy between language and sequential recommendation. And I kind of wanted to just pause a little bit to think about what actually changes from this. I think in some sense, the sequential recommendation problem is much harder than the LLX. So one of the, when you compare this, and the situation, one of the things that happens is, for instance, the vocabulary is, is, is, it's in a very different way. So language has the advantage that your vocabulary is essentially fixed, right? Your set of tokens, language doesn't really, language does change over time, but it changes very slowly


---

**[12:41]** over time. So the set of tokens that you use are basically the word, right? The set of words do not change or stop words doesn't change. However, when you, when you do recommendation, especially things like online advertising, you have lots of items that basically leave your catalog. So items that no longer exist there, and new items that enter constantly, right? So basically, we have a dynamic vocabulary, and this is a difficulty that we need to deal with. Then we have the aspect of time. So there's kind of a very clear positioning in tokens, where, you know, essentially time is discrete, it's basically given by positions. Whereas in recommendation, there's a, there's a, there's this kind of a continual process, right? So, I mean, for instance, events that happened. So for, if you liked a playlist, but you know, three months ago, it's not the same thing as liking it just yesterday, right? It's not the same information. And so we need to stack into that as into account. Also, you know, tokens in, in, in language that kind of have some semantic meaning, whereas in,

**[13:48]** in our setting, you know, tokens can be things like actions, like you clicks on different items. So they, and these things all expose very different kind of signal, right? So, so there's a more variety in, let's say, in the token, what tokens represent, let's say. And finally, there's this, this is again, more, more related to, to like the industry, this noticeable difference in the input output geometry. So the problem for, for text generation, typically you start with a, with a tiny prompt, then you want to generate a long text from that, right? However, when you, when you do recommendation, you want to do kind of the opposite. So we have a very long input, which is basically the, you know, the history, whatever the history events, and then you have a relatively short output, right? So that's the next item, right? We're not interested in, in generally, well, typically we're not interested in, in, in, predicting the 20th items since, since we last saw you. Okay. So, and this brings me to, to yet another

**[14:55]** difficulty or another change that says, when you look at the sequential recommendation from, from language, which is that we have this aspect that you, when you recommend things, you're basically generating tomorrow's data, right? So basically the way it goes is you have some history for your, for your user, you make an offer, you make him say, you offer some tracks to play to, then you get some response and that creates the need, the data for the, for, for tomorrow, basically. So there's this feedback loop where, you know, what you predict becomes the basis for the data that you get afterwards. And this is important to, to take into account. So this is known as the exposure balance. Okay. So let me go back to the, to the challenge. So like, you know, there's, there's basically two phases. So in phase one, we have what we call the predict clicks phase, or, and so the idea is that, you know, given an interacting history and, and a candidate item, your, your, your objective is to build a model that basically gives you the probability that, that that item will be clicked.

**[15:58]** Right. And then, so you, and you simply submit a file, which basically predicts this click probabilities. Now in phase two, there's this kind of compete and adapt. So the idea here is that we have a, we simulate a marketplace and user histories, and now you'll, you'll be the recommendation agent that essentially offers items to, or items to this, to this, to these users. And then you'll submit the models for evaluation, which in, during our evaluation will essentially simulate these users. And then you'll get some answers back, which you can then do basically update and improve your model. Okay. So let me, let me go a little bit into more detail. So for instance, so again, this is an example, the, the actual data won't be about music. And it'd be actually be anonymized as well. So we, we have some, for example, this, this user that has this, you know, this history, I think from, from top to bottom, you know, he, he played some jazz, then he skipped the dance and then he replayed

**[17:02]** the acoustic press, the same sort of form. And then your task will be basically to predict it. So given, given a candidate item, I, you want to predict the probability of click, given this history here, the item I. So for instance, just, we can look at some, some just made up these numbers, but you might, your model might predict that acoustic live might get a 0.74 probability. This is motivated by the fact that the, the, you know, the user already replayed an acoustic set and therefore, you know, is quite likely to, you know, there's a, there's a strong chance that I know he's on some kind of acoustic vendor and he wants to keep on playing acoustics. He's also played jazz in the, in the past. So you, you might give a nice, relatively big probability, but you know that, the dance it has skipped. So you probably, you know, your prediction for that should be quite low. Right. So the, the, the pipeline is quite simple. You, you'll basically learn, we give you some dates, some label data, you learn from that label data, and then you, you submit the line predictions and then we score it against our hidden data.

**[18:04]** So, um, okay. So for the, for the second round, uh, now basically the idea is that you have to do the whole thing. So you don't, you don't just, we don't give you a candidate items that you need to evaluate that thing, the probability of a click, but rather you need to actually tell them, give me an item, right? Give me an item so that we can then offer them to the user. Uh, so the idea is that, uh, we, you know, every, everybody's team has an agent, each of these agents propose different, uh, different items. Uh, and then we have some, uh, user simulation that we'll choose among these items and we'll choose some kind of rewards. And this will produce a new set of data that will then release to you. Uh, and we'll have this, we'll do this to like four rounds basically of this process, right? So the idea is in between each round, you, you get to, uh, to include the new data, maybe, you know, improve the models, see, uh, see what could be, you know, maybe identify things that were improved, et cetera. Uh, so in some sense, you know, this is nice because it really emulates the, the kind of the actual

**[19:07]** real life setting that we have in the very controlled environment. Um, okay. And finally, um, I have some kind of selected reading. Uh, so this, I've kind of briefly mentioned all of these before. Um, so if, yeah, if you want to kind of explore, I mean, if you completely need to, uh, uh, to, uh, yeah, to sequential recommendations, this might be, this might be nice papers to, to read about and, and get to know. Um, and that, that's it. Thank you so much, Gonzalo. Uh, I'll take back the lead. I've got a few more things to share before the Q&A session. Uh, I stopped sharing, right? Yeah. Yes, you did. And now we should be back. So thank you so much, Gonzalo and, and Gabor. So you, you didn't spoke there, but I'm sure you'll be answering questions afterwards. Um, so yeah, to get back to it. Yes, we have a prize pool because it's a challenge and we need to have winners. So first prize would be, uh, 6,000 euros, uh, for the team split, uh, equally among

**[20:16]** the team. So reminder teams are for three, uh, to eight participants. Um, for the second prize, we will have two teams winning. So two second, uh, places, uh, with the sum of 2,500, uh, euros. And for the set team, uh, we will have, uh, the surprise, sorry, we'll have three teams, uh, um, winning, uh, the amount of, uh, uh, uh, 1,500, uh, euros, uh, to split equally among the participants. So there you have it for the prize pool, but to gain that, you need to do the challenge, uh, be, uh, in among the 15, um, selected, uh, after phase one and to, uh, uh, uh, be, uh, uh, to be a super, super, uh, uh, challenger, uh, during the, uh, uh, November, uh, 14th and 15th, to be on the podium, uh, for the next steps, it's all about the platform and creating teams. So as I

**[21:17]** said, uh, you need to create your profile on the platform, uh, go to the team tab and create your team. If it's not yet, uh, done, if you are looking for a team, um, just go scroll on the, uh, hiring section to make sure, uh, you see, uh, the different, uh, teams are looking for teammates. Uh, if you have a team and you are already, um, um, uh, within team, sorry, I'm losing my words. Uh, so let me get back to it. So you have a team, you create your team on the team tab, name it, invite your teammates and, uh, you stay put because it's on the 21st. Um, you, uh, uh, are looking to complete your team because you are not yet complete, uh, you can publish your, uh, uh, your team, uh, to make it, um, visible for everyone who is looking to join a team and then go back, uh, to draft mode, uh, once your

**[22:19]** team is completed. And if you don't have a team, you haven't created a team and you're looking to join a team, you can go, uh, on the team tab and look on the left, uh, for the hiring, um, the, the hiring button. So you can see all the teams that are looking to, uh, complete their teams. So you can, uh, join one of them. Otherwise, what I would do also, um, is to go onto the lobby and scroll in the matchmaking. I, uh, copy paste all the links of the teams that are looking to complete that team. So you can, uh, go and apply, uh, to join their teams. And in last, uh, manner, what I would do is just complete your teams, uh, with people that are looking to join teams. And if you are not, uh, creating a team or looking to join your team, I will just put you into one. So, uh, um, there you have it. It's, uh, starts on October 21st. So, uh, reach out to me if you have any questions and you want to be put

**[23:24]** into one teams or, uh, do the mismatch. Uh, I'll do the matchmaking for you. So you have it go back into draft mode, be here. Otherwise everyone can have access to your portal and your username and everything. So we don't want, uh, people, uh, picking on each other. So go back to draft. So it's your place, uh, and it's your, uh, it's, it's more secure if I may say. Uh, so how to find CMA. I just explained it, but send me a post or, uh, or, uh, send me a mail. It's always, um, easier. So don't hesitate to, to do that. So I think I gave all the info and now, uh, you have the Q and S section. If you have any questions for Gonzalo and Gabor about the technical, uh, spec of the challenge, or for me, if you have any questions, uh, concerning the, the timeline or, uh, the team formation. There you go.

**[24:28]** So, ah, yeah, I see we have questions. Uh, will we be provided with the dataset for the first phase? Oh, that's, I think it's a question for Gazzo. Yeah. For you. Go ahead. Okay. Uh, yeah. Yeah. Yeah. So the first one that there will be a, a trained dataset, right? Yeah. Cool. Thank you. Is it possible for me, uh, to form a two member team? Nope. Uh, you need to be, uh, from three members to eight member max. So that's the rule for this year. So yeah, no, no, no two member team. So yes. Uh, also, will we be getting the dataset and most submission as instructions? Uh, uh, yeah. So I think the, I mean, I think the platform will offer basically a platform and then some kind of read me or instructions on how to make submissions and sort of like the, the rules of the submissions and all that. So that'd be,


---

**[25:30]** I think that we'll have that in writing. Uh, would that PowerPoint be available for us on the replay? Yes. I will, uh, add, uh, my slides. I don't know, Gonzalo, if you'll be able to share with me your slides so that we can, uh, I can put it on the platform with the replay. Yeah. Yeah. Yeah. Uh, yeah. I'll, I'll double check. There's nothing, but yeah, no, there should be a problem. Okay. Uh, what is the attitude to using AI coding agents? I guess my process of elimination, I guess I will answer it. Um, right. So I think for phase one, it's kind of difficult to stop something like that. So just to be clear phase one, I think the submission is going to be a CSV file. So yeah, I guess feel free to explore it, use AI coding agents stuff to you. Phase two, on the other hand, we'll have, let's say more stricter guidelines regarding that, but we will put it in writing. So we will also put it up on the website to cover these questions

**[26:34]** in more details, especially since these AI coding tools are a bit, uh, that's a difficult to address. So we don't put any restrictions at least for the phase one part. And for phase two, there will be very specific instructions, which is the live round and how will that look like? Thank you. And the other one coming up, will you provide any computing resources like virtual machines or cloud computing credits, or we need to use our own devices? Once again, for the second part of phase two, this is a live round and we will provide, uh, cloud computing, uh, resources. So basically we would set up each qualifying team with a virtual machine with some decent GPUs. Um, so there's some serious firepower there. And for phase one, again, it's each team is free to use whatever resources they feel comfortable with. We don't want to restrict them less in terms of environment or what kind of solutions they can go for. Uh, but for phase two, which would be again, live round, they will be supplied with

**[27:35]** virtual machines and, um, all the resources needed for, um, for the live competition. It also means that all teams will have a very level playing field in the final phase. Thank you. Will the dataset include item metadata, titles, text description, visual features, categories, or will it strictly consist of user interaction, log sequences, user ID, item ID, timestamps. Yeah. So we very specifically want to focus on user item interactions. So in that sense, this dataset is a bit unusual or different from other public recommender system datasets you might see. Again, there will be no explicit metadata mentioned. And so the idea would be to nudge, uh, teams to explore this whole idea of what can you derive from user items, sequence interactions, and maybe timestamps. So very little metadata primarily use item interactions. Thank you. Um, I see simulated users will give feedback to improve recommendation system.

**[28:44]** So it plays a vital role in the flow. The closer simulated user to real scenario, the better recommendation is. My question is how do you build simulated users behaviors that could match real scenarios as most as possible? In general, I mean, that's a very open-ended question. How we got our dataset? I guess we can't really reveal that because again, the whole point is we're going to a bit, let's say, hide the details on what is behind each user and each item. Again, the whole point would be to leave this off to the contest and to the teams to explore this. Um, but I think we also have, uh, a separate chat area. If somebody wants to discuss the, how do you generate synthetic datasets that mirror online recommender systems, we can have a separate conversation on that. It's, yeah, this is a very good question. It's a nice question in general. It's a nice research question, just not within the context of the competition, but I think we would actually be happy to answer it in general. But, um, regarding the competition, what's behind our specific dataset, I think we cannot reveal it.

**[29:47]** Um, but we try to mirror actual, let's say real life production or production like real life recommender system behavior and datasets, but we won't tell how. Okay. Thank you. Could you please clarify which specific area the project topic should be drawn from, or can it be drawn from any area? I don't know why they voted him down. Um, so again, it's going to, I'm not sure if I understand the question correctly regarding what is behind users and items. I think it's not, uh, clearly defined. What I mean by that is from the team's perspective is just user and item interactions. What's behind it could be anything. Again, there's no real difference whether it's like a music recommendation system or about online shopping or whether it's advertising. And so don't try to narrowly focus on what kind of area or topic is behind it. I think you're free to explore on the technical side, any solution you want and regarding what's behind the dataset. It also

**[30:51]** shouldn't matter too much. Like the problem is presented as a universal, uh, representation of all recommender systems. I'm not sure if I made that clear, but if it's not clear, please ask follow-up question. And I will try to clarify. Okay. How will latency be benchmarked? Is it measured per individual user request or across batched evaluation requests? Is there a hard millisecond restriction? Um, right. So phase one, again, submission will be in the form of a CSV file. Uh, I think we will have like a couple of minutes. Actually, no, it shouldn't be a problem. So what I mean by that is phase one submission is in the form of a CSV file. There is no evaluation time limit. Phase two will have an actual time limit for the evaluation. We will give more detailed instructions once we get closer to the phase two organization, but generally speaking is going to be the submitted models will be evaluated on, um, test dataset, and there will be a max time limit allocated to the test dataset. And essentially

**[31:54]** all teams will be executed on the same hardware. There's, um, wall time limit for the whole execution. So it's not per batch, uh, but rather for the whole test dataset. And it also accepts partial answers. What I mean by that, if you only completed 90% of the test data sets, you will get scored based on that 90%. Uh, again, this is very space two specific. So more details can be given, uh, for phase one, you can submit a CSV file and there is no time limit. Okay. I don't have any more questions. Please don't hesitate to rest them now. So, uh, that could be good. Um, if so, if you have more questions popping in your mind, uh, afterwards, uh, you can always send me an email or, uh, on the platform directly because both of, uh, Gabor and Gonzalo are both on the platform and, uh, registered as mentors. So don't hesitate to ask questions there and reply to you. Plus, uh, be sure to, um, subscribe on the

**[33:01]** channels in the lobby to get all the practical information and ask your questions, uh, over there. If you still have, um, other, um, other questions and yeah, Gabor, go ahead. Uh, I, I see some questions regarding face. I'm not sure if face two was really covered well, but I wanted to add that. Yes. So face two is going to be only for the top, I believe, max 15 qualifying teams and it's a live round. So face two will not be with CSV files. It's going to be a lot more serious in that regard. So it's no longer about just submitting a CSV file, but rather actually submitting an executable code and model that is being trained by the participants during the live round. Um, so, um, I'll just read the question. Uh, so it's in the replay afterwards. So how top 15 teams will be selected for face two based on results only or time is errors to the part of consideration means which team is submitting first. Um, well, the phase one, I believe is

**[34:06]** open for many, many, many days. So the idea isn't to put time pressure on people, but rather for them to think deeply about the problem and come up with good solutions. And so it's going to be based on score. So the idea would be of course, is the best scoring teams, the top 15 teams will be selected for that face. There is no time constraint. So as long as it's within the submission timelines, I guess. Plus you will have a leaderboard, uh, that will be, uh, uh, live so that you will be able to see after your submission, uh, what place you're ranked. And, uh, yeah, I think, and, and for the time you have, uh, it starts on the 21st of October and it ends on November 4th midnight. So that's the thing. Indeed. We have another question. Uh, Gonzalo, you wanted to add something? Oh, no, no, no. Sorry. Okay. Sorry. Uh, so if the evaluation is only based on CSV submissions,

**[35:08]** does that mean we won't be evaluated on the basis of our approach just on the metric score? Right. So just to be clear, there's going to be a public and private leaderboard split. So we will basically try to make sure that it's not so easy to, to game the leaderboard metrics. Uh, I think we would also look at the submission history, but most double check that let's say, um, there's nothing, uh, exploitative happening, or for example, if teams are not trying to bypass their submission limits by collaborating or other things, but in general, just to keep it really, really simple, we do believe that if we just score based on the top scores of the leaderboard, we can select 15 very good teams. And so what approach is being used? The reason why that would matter is because phase two will cover or have a lot of similarities with phase one. So teams who selected a more reliable, well, well, let's say researched approach for this phase one can also carry that knowledge onto phase two. So we

**[36:09]** think that essentially it gets rewarded indirectly, even though for the actual CSU scores, we're not checking essentially what was the approach taken by the teams. Okay. Maybe, uh, you answered it, but what are the evaluation metrics in judging the top 15 teams? Um, we will put everything out. I think once the data set goes online and that is maybe the, um, that will clarify everything up. I basically is going to be a single leaderboard metric that will, uh, cover, let's say the, it's going to be a form of, uh, AUC basically. I'm not sure if, uh, this really answers the question, but it's going to be based on the leaderboard score that doesn't have a time evaluation criteria, only focuses on accuracy. Okay. So, uh, we have another question say, where can I find the briefing or any helpful documents for phase one? I think it will be in the data set release on October. The data set release, also the slides I think Gonzalo presented will also be shared. So, and maybe we can

**[37:11]** put some more resources into, to set candidates on the right direction, but I think we'll also provide, let's say, uh, starting sample model solution that can already be executed. So we'll try to give also some resources on how to get started with the competition. Great. Again, another question. What kind of view will there be? Uh, will we have to defend the work for phase two? Um, so normally I think that the winning teams, uh, traditionally, uh, each year, they do a small presentation on their approach. So this could be thought of as a public defense. And I think depending on what we see regarding how the teams perform, we might also have a, uh, that's an informal questioning on how did they come up with a certain solution and what was their approach taken? I think this is a bit maybe relevant today, given that again, people can use AI tools. Very often we don't understand what are we running. So we do want to make sure that let's say the winning teams in phase two have a good understanding, at least of what is the approach taken. Even if of course,


---

**[38:14]** the, of course, nowadays people sometimes don't even write their own codes. So, but again, this is something that we will try to address ahead of time and be very specific about it. Um, I think it's also new to us, right? It's the first time, uh, that we are having these questions regarding, um, how to handle AI tools combined with explainability, but just to be clear, winner winning teams have a live presentation about their phase two approach taken like a small, let's say, technical overview of it. And also we might ask more pointed questions because we ourselves are quite curious about what the technical solutions were and what approach they take. But we will be more specific in, um, later stages about these instructions and how we will approach it. So don't worry about it. Okay. Um, I have other questions, uh, on the chat, on the chat. Yes. Um, how exactly do we need to submit for phase one? I'm not sure I get the question, but maybe you do. Uh, I think so basically

**[39:19]** once teams are registered and once the competition opens, I believe there's a new platform that opens up and available and that's where there's a submission button and that's the, and that's when it opens up. So right now it's not visible, I think two teams, but once the competition opens, there will be a submission portal for uploading your CSV file. And to complete with you, uh, on your team, when you create your team, you have a space for you and you have a button that is, uh, bring me to the platform or something like this. I don't know if I put it this way, but, uh, let me check it out for you. Uh, I'll let you know, let, let you know exactly. So, uh, you have the button that is go to the challenge. So if you click on it, uh, you will see that your team, uh, at TikTok builders, I don't know if you're connected. The challenge will open in six days on Wednesday, 21st October at 9, 9, at 9am Irish time. So you can see you have, uh, your link on your, uh, on your, uh, team, um, uh, in your team,

**[40:24]** uh, with the, with your team, uh, on your team project, uh, your, your page. So there you go. And yes, this replay would be available. Uh, um, uh, this webinar would be available as a replay, uh, right after, uh, we closed, uh, the, the line and we have one last question that is, can we keep submitting multiple times like Kaggle competition to improve our leadable score? Of course. Of course. So I think we will impose a max 10 submissions per team per day, but of course you're free to submit as many times as capacity allows. Uh, I see some other questions. I will maybe just quickly rush through. Sorry if I'm speed. Yeah. No, no, no worries. Um, there's a question about AI compliance towards your models. I'm don't think that's an issue. Let's say, I'm not sure what you mean by AI compliance. If there's something ambiguous, please let me know for phase one. Again, feel free to use any AI tools for phase two. There will be very specific instructions on where do we draw the line regarding AI tools and what can be used and

**[41:27]** cannot be used. But other than that, there are no restrictions. So there is no, let's say ethics considerations or I'm not sure what other interpretations of AI compliance is there. Uh, personal computers for the onsite stage. Yes, of course, because you want to remote connect to these machines and, um, for further instructions regarding, um, phase one and the dates and everything, I think that will get published as well. So we will reiterate, I think these instructions over and over. And essentially I think everybody gets notified automatically when the competition opens, I guess. Yes.  Yes. And, uh, yeah, well, I guess we have no more questions and we answered the whole, I guess. So, um, um, good luck, uh, you still need to create for finalize your teams. Uh, you'll see that in your team environment, uh, that you have, uh, uh, a link to go to the challenge. It's not open yet,

**[42:29]** but we'll remind you, uh, when, uh, it's open and, uh, good luck everyone and, and see you soon. Thank you. Thank you. Thank you. Thank you. Bye.                                                    Thank you.