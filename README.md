# DSC232R-GroupProject

# Spotify Music Analysis and Playlist Generation

## Project Overview

This project aims to analyze the Spotify Charts dataset and develop a machine learning model to create personalized playlists based on audio features of the tracks. The goal is to cluster songs into different categories and generate playlists that fit specific themes or moods.

## Dataset

The dataset used in this project is the Spotify Charts dataset from Kaggle, which includes various attributes related to Spotify music tracks. The dataset contains 26,174,269 observations with attributes like ID, Title, Rank, Date, Artist, URL, Region, Chart, Trend, Streams, Track ID, Album, Popularity, Duration_ms, Explicit, Release_date, Available_markets, and Audio Features.

## Data Preprocessing

### Initial Preprocessing
- Removed columns with a significant proportion of nulls.
- Checked for and removed entirely empty rows.
- Converted relevant columns to float data types to facilitate numerical operations.

### Major Preprocessing
- Scaled numerical features to ensure uniformity.
- Imputed missing values using appropriate strategies (mean, median, or mode).
- Encoded categorical features as necessary.
- Expanded features to include new audio-related attributes for better clustering.

## Feature Engineering

### Audio Features
We focused on the following audio features for clustering and playlist generation:
- `af_danceability`
- `af_energy`
- `af_key`
- `af_loudness`
- `af_mode`
- `af_speechiness`
- `af_acousticness`
- `af_instrumentalness`
- `af_liveness`
- `af_valence`
- `af_tempo`
- `af_time_signature`

## Machine Learning Model

### KMeans Clustering
We implemented KMeans clustering to group tracks based on their audio features. The optimal number of clusters was determined using the silhouette score. We experimented with different numbers of clusters and selected the best configuration.

#### Steps:
1. Assembled the audio features into a single vector.
2. Scaled the features using `StandardScaler`.
3. Applied KMeans clustering to the scaled features.
4. Generated predictions and assigned cluster labels to each track.

## Results

The KMeans clustering model successfully grouped the tracks into distinct clusters based on their audio characteristics. This allows for the creation of playlists that cater to specific themes or moods.

## Conclusion

The initial model using KMeans clustering provided a good starting point for playlist generation based on audio features. However, there is room for improvement:

### Next Steps:
1. **Model Enhancements**: Experiment with other clustering algorithms like DBSCAN or hierarchical clustering to see if they provide better results.
2. **Feature Selection**: Incorporate additional features such as artist popularity, track release date, and region-specific preferences to refine the clustering.
3. **User Feedback Integration**: Develop a system to incorporate user feedback to dynamically adjust playlists and improve recommendation accuracy.

### Conclusion of the First Model:
The first model demonstrated that clustering based on audio features is a viable approach to generating themed playlists. To improve the model:
- We could enhance feature engineering by including more contextual information about tracks and users.
- Implementing a feedback loop to adapt to user preferences could significantly increase the relevance of the generated playlists.

## Code and Notebooks

The complete code and notebooks for this project are available in the repository. The main files include:
- `milestone3_notebook.py`: The latest implementation with updates for milestone 3.
- `README.md`: This updated README file with the new work and modifications.

## Links to Code and Notebooks
- [Milestone 3 Notebook](Milestone3_Notebook.ipynb)
- [README File]("README.md")
