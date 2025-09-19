Game Recommendation System
=========================

Short description
-----------------
This repository contains code and data for a game recommendation system built from Steam-like game metadata and user interaction logs. It implements both content-based and collaborative recommendation approaches, plus helper scripts for preprocessing, analysis, and evaluation.

Why this repo
--------------
- Explore different recommender approaches (content-based, implicit ALS, EM/SVD experiments).
- Reproducible pipeline from raw CSVs through preprocessing, recommendation generation, and simple evaluation.
- Educational and experimental: many scripts are notebook-style and designed for research/learning.

Repository layout
-----------------
- Game_rec_sys_Code/
  - content_based/
    - preprocessing_games_data.py      # Cleans `steam_games.csv` and builds combined text features
    - content_based_recommender.py    # Builds CountVectorizer features and generates per-user recommendations
  - collaborative_als/
    - collaborative_recommender_als.py # ALS implicit-feedback recommender (uses `implicit` library)
  - collaborative_em/
    - EM_Rating.py                    # Experimental script: GMM/EM, SVD, gradient-descent MF
  - split_training_testing.py         # Simple random train/test split of user interactions
  - recommender_evaluation.py         # Evaluates recommendation outputs against the test set (per-user counts/ratios)
  - data_analysis/                    # Visualization and analysis scripts

- Game_recsys_data/
  - raw_data/                         # Original CSVs (steam_games.csv, steam_users_purchase_play.csv, steam_users.csv)
  - intermediate_data/                # Preprocessed CSVs
  - model_data/                       # Train/test splits used by recommenders
  - output_data/                      # Recommendation CSVs produced by algorithms
  - evaluation_data/                  # Evaluation CSVs produced by `recommender_evaluation.py`

Quick explanation
---------------------------------------------
We want to suggest games to users based on what they already played and game metadata.

- Content-based approach: we read each game's tags, genres and developer/publisher details, create a simple textual fingerprint for each game, and measure how "similar" two games are. For a user, we look at games they already have and recommend similar games with the best review scores.

- Collaborative approach: we look at the pattern of which users played which games and use matrix factorization (ALS) to find latent patterns — for example, people who like game A often also like games B and C. The model predicts which games a user may like even if metadata is sparse.

- Evaluation: we hold out 20% of user interactions as a test set and measure, for each user, how many of their held-out games were present in the top-20 recommendations. This gives a simple per-user effectiveness ratio.

Getting started (run locally)
-----------------------------
1. Create a Python environment and install dependencies. Example using conda (recommended for the `implicit` package):

```bash
conda create -n recsys python=3.9 -y
conda activate recsys
conda install -c conda-forge pandas numpy scikit-learn scipy matplotlib seaborn plotnine tqdm -y
conda install -c conda-forge implicit -y
```

2. Ensure data is in place. The repository expects these files under `Game_recsys_data/` (already included in this repo snapshot):
- `data/raw_data/steam_games.csv`
- `data/raw_data/steam_users_purchase_play.csv` or equivalent

3. Preprocess game metadata and reviews (content-based inputs):

```bash
python Game_rec_sys_Code/content_based/preprocessing_games_data.py
python Game_rec_sys_Code/data_analysis/analysis_reviews.py
```

4. Create train/test split:

```bash
python Game_rec_sys_Code/split_training_testing.py
```

This writes `Game_recsys_data/model_data/steam_user_train.csv` and `steam_user_test.csv`.

5. Run recommenders:

- Content-based (will create several output CSVs in `Game_recsys_data/output_data/`):

```bash
python Game_rec_sys_Code/content_based/content_based_recommender.py
```

- ALS-based collaborative recommender:

```bash
python Game_rec_sys_Code/collaborative_als/collaborative_recommender_als.py
```

- Collaborative EM / SVD experiment (not production-ready, more experimental):

```bash
python Game_rec_sys_Code/collaborative_em/EM_Rating.py
# This file may contain Windows paths; edit the script paths before running if necessary.
```

6. Evaluate recommendations (for each output file, `recommender_evaluation.py` writes a CSV under `Game_recsys_data/evaluation_data/`):

```bash
python Game_rec_sys_Code/recommender_evaluation.py
```


