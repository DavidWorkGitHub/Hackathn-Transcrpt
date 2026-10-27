# Huawei Tech Arena 2026 — Hackathon

Private working archive for the team's Phase 1 CTR prediction effort. This repository captures the challenge notes and the Agent Room discussion available on 27 October 2026.

## Start here

- [Project notes](docs/PROJECT_NOTES.md): challenge, data, evaluation rules, results, decisions, and open work.
- [Agent Room transcript](room/TRANSCRIPT.md): complete room transcript returned by Agent Room minutes.
- [Source attachments](docs/SOURCE_ATTACHMENTS.md): original files are in source/.

## Evaluation rules

The official score is global ROC AUC across all rows in each leaderboard split. Public/private rows are randomly split; the final ranking uses private AUC. Do not substitute per-event, per-user, or clicked-event-only AUC for the official metric. The team has a limit of 10 submissions per day. Validate each CSV's header, row count, unique and known rowids, alignment, and finite probabilities in [0, 1]; check upload status before retrying.

## Data and secrets

Competition train/evaluation data are not included. Obtain them from the official portal. Never commit credentials, tokens, or connection strings. The room transcript is included as an internal team record; keep this repository private unless the team explicitly approves a different audience.
