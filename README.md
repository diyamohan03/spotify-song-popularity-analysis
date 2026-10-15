# Spotify Song Popularity Analysis

A look at whether audio features like danceability and energy explain which songs on Spotify's 2020-2021 Top 200 charts were the most popular.

## The question

Streaming platforms group and recommend songs using audio features. This project asks two things about the songs that made Spotify's weekly global Top 200 in 2020 and 2021:

1. Which audio features account for most of the differences between hit songs, and do the songs fall into natural groups?
2. Can those features predict how popular a song is?

## Approach

- **Data:** 1,556 songs from the [Spotify Top 200 Charts (2020-2021)](https://www.kaggle.com/datasets/sashankpillai/spotify-top-200-charts-20202021) dataset on Kaggle (1,545 after dropping rows with missing values). Only audio features were used as inputs. Other popularity measures (streams, weeks charted, chart position) were dropped so they couldn't stand in for the target.
- **PCA** on nine standardized audio features: danceability, energy, loudness, speechiness, acousticness, liveness, tempo, duration and valence.
- **K-means clustering** on danceability and energy, with the number of clusters picked by the elbow method.
- **Classification:** Spotify's 0-100 popularity score was turned into popular / not popular at cutoffs of 70, 75 and 85. An RBF-kernel SVM, a random forest and gradient boosting were each compared with a baseline that always guesses the larger class.
- **Tools:** Python, pandas, scikit-learn, matplotlib.

## Results

**PCA.** The first two components explain 28.0% and 14.8% of the variance.

| Component | Strongest features (loading) |
|---|---|
| 1 | Energy (-0.53), loudness (-0.52), acousticness (+0.45) |
| 2 | Danceability (-0.52), duration (+0.51), speechiness (-0.45) |

**Clusters.** K-means found three groups:

| Cluster | Songs | Mean danceability | Mean energy | Mean popularity |
|---|---|---|---|---|
| Lower danceability and energy | 323 | 0.51 | 0.46 | 68.5 |
| More danceable | 643 | 0.81 | 0.59 | 70.3 |
| Higher energy | 579 | 0.66 | 0.78 | 70.8 |

The three groups have almost the same average popularity.

**Predicting popularity.** Test-set accuracy compared with always guessing the larger class:

| Popular if score >= | Share popular | Baseline | SVM | Random forest | Gradient boosting |
|---|---|---|---|---|---|
| 70 | 61.8% | 59.2% | 61.2% | 57.9% | 59.5% |
| 75 | 44.4% | 61.2% | 54.4% | 52.4% | 51.5% |
| 85 | 8.3% | 92.2% | 92.2% | 91.3% | 91.6% |

No model meaningfully beats the baseline. At the 85 cutoff the models score above 90%, but only because about 92% of songs are below the cutoff, so guessing "not popular" every time does just as well.

Among songs that already made the charts, audio features don't predict how popular a song is. Artist following, marketing and release timing likely matter more, and they aren't in this dataset.

## How to run it

1. Download `spotify_dataset.csv` from the [Kaggle page](https://www.kaggle.com/datasets/sashankpillai/spotify-top-200-charts-20202021) and save it as `data/spotify_dataset.csv`.
2. Install the packages:
   ```
   pip install pandas numpy scikit-learn matplotlib jupyter
   ```
3. Open and run `spotify_popularity_analysis.ipynb`.

## Background

Spring 2024.
