# Robust De-anonymization of Large Sparse Datasets

An academic replication and experimental study of the Scoreboard-RH de-anonymization attack described by Narayanan and Shmatikov in [*Robust De-anonymization of Large Sparse Datasets*](https://arxiv.org/abs/cs/0610105).

The project investigates whether sparse movie-rating histories can act as identifying fingerprints. It compares anonymized records from the Netflix Prize dataset with auxiliary information derived from MovieLens 20M, using ratings and timestamps to rank possible matches.

> This project was completed collaboratively as a group assignment for the *Privacy Preserving Information Access* course at the University of Padua.

## Research question

Removing direct identifiers is often insufficient when a dataset contains high-dimensional behavioural data. A small and approximate set of ratings may still be distinctive enough to link an anonymous user to a known profile.

This project explores:

- how Netflix and MovieLens records can be aligned across different identifiers and rating formats;
- how rare ratings and temporal proximity affect candidate matching;
- how eccentricity separates a strong match from competing candidates;
- how the amount and accuracy of auxiliary knowledge influence attack success.

## Methodology

1. **Data alignment** - Movie titles are normalized across Netflix Prize and MovieLens 20M. Ratings, dates, and release years are converted to compatible formats.
2. **Similarity scoring** - Candidate records are scored using common movies, rating distance, timestamp distance and a weighting term that gives more importance to statistically rare ratings.
3. **Match selection** - The Scoreboard-RH eccentricity measure compares the best score with the remaining candidates. A match is accepted when eccentricity reaches the `1.5` threshold used in the study.
4. **Temporal-tolerance experiments** - Candidate distributions are evaluated with time windows of 1, 7, and 30 days.
5. **Controlled validation** - A known Netflix user is transformed into synthetic auxiliary information and hidden among distractors, allowing the predicted match to be checked against ground truth.
6. **Robustness simulation** - For each trial, one target is compared with 999 randomly selected distractors. The experiments vary the number of known movies and introduce temporal errors of 1, 3, or 14 days.

## Key results

The saved outputs report maximum eccentricity values of:

| Time tolerance | Maximum eccentricity |
| ---: | ---: |
| 1 day | 9.0 |
| 7 days | 11.0 |
| 30 days | 11.5 |

In the controlled single-user validation, the correct Netflix record was selected with every tested auxiliary-history size from 5 to 35 movies. Eccentricity increased from approximately `1.51` with 5 known movies to `3.02` with 35 known movies.

The robustness simulation used 50 target users per configuration:

| Known movies | 1-day error | 3-day error | 14-day error |
| ---: | ---: | ---: | ---: |
| 1 | 0.32 | 0.32 | 0.12 |
| 3 | 0.98 | 0.92 | 0.84 |
| 5 | 1.00 | 1.00 | 0.98 |
| 8 | 1.00 | 1.00 | 0.98 |
| 10 | 1.00 | 0.98 | 1.00 |

These results illustrate the privacy risk of releasing precise, sparse behavioural records: in this experimental setting, a small number of approximate ratings was often sufficient to recover the known target from a large candidate set.

## Repository structure

```text
.
├── README.md
└── De-anonymization of Large Sparse Datasets/
    ├── De-anonymization of Large Sparse Datasets.ipynb
    └── results/
```

- [`De-anonymization of Large Sparse Datasets.ipynb`](<De-anonymization of Large Sparse Datasets/De-anonymization of Large Sparse Datasets.ipynb>) contains the preprocessing, scoring, matching, and validation code.
- [`results/`](<De-anonymization of Large Sparse Datasets/results/>) contains the saved experiment outputs.

## Data sources

- [Netflix Prize dataset](https://www.kaggle.com/datasets/netflix-inc/netflix-prize-data)
- [MovieLens 20M](https://grouplens.org/datasets/movielens/20m/)

The source datasets are not included. MovieLens data is subject to the [GroupLens usage terms](https://files.grouplens.org/datasets/movielens/ml-20m-README.html), which restrict redistribution and require acknowledgement.

## Requirements

- Python 3
- pandas
- NumPy
- Matplotlib
- KaggleHub
- access to the two datasets through a configured Kaggle account

The preprocessing section downloads the datasets and creates the intermediate `df_nf_ratings.csv` and `df_ml_ratings.csv` files used by the later experiments. Processing the complete source data is computationally expensive; the matching experiments therefore operate on subsets and batches.

## Limitations

- The cross-dataset analysis is limited to subsets because exhaustive comparison is computationally expensive.
- Movie-title normalization can produce missed or ambiguous alignments.
- The validation results are based on a sample of 50 targets rather than the full population.
- Distractors are sampled randomly, so a new execution may not reproduce every saved probability exactly.
- High eccentricity identifies a statistically distinctive candidate; it is not, by itself, proof of a real-world identity.
