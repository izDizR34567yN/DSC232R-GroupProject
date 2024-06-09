# DSC232R-GroupProject

# Spotify Music Analysis and Playlist Generation

## Introduction

This project aims to analyze a Spotify Charts dataset and develop a machine learning model to create personalized playlists based on the audio features of the tracks. The goal is to cluster songs into different categories and generate playlists that fit specific themes or moods. The dataset used in this project is the [Spotify Charts (All Audio Data)](https://www.kaggle.com/datasets/sunnykakar/spotify-charts-all-audio-data) dataset from Kaggle, which includes various attributes related to Spotify music tracks. Millions of observations with dozens of attributes render this dataset an unwieldy tangle of unrefined information, largely void of practical value in its raw form. By implementing scalable computational methods and technology, we aim to develop an efficient model that can derive meaningful insights from an otherwise cryptic mass of information.

 ## Methods

### Data Exploration
  
The dataset of interest accords to a simple DataFrame model and is available for [download](https://www.kaggle.com/datasets/sunnykakar/spotify-charts-all-audio-data) as a csv file on Kaggle. To account for its massive scale, we employ Apache Spark to facilitate efficient analysis of the dataset. We begin by rendering the dataset as a Spark DataFrame such that we may derive a preliminary overview of its contents. Thereafter, we are able to carry out a brief exploratory data analysis that includes inspecting and appropriately adjusting the dataset's schema, counting observations, generating summary statistics for numeric attributes, checking for missingness, and exploring select feature interactions through a series of simple scatterplots. The code used to carry out this exploratory data analysis is available in the following [Jupyter notebook](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/Milestone4_Notebook.ipynb), and its most significant are outlined in the "Results" section of this report.
      
### Preprocessing

Having completed a brief exploratory data analysis, we proceed to carry out appropriate preprocessing measures. This includes generic steps of restricting our interest to a relevant subset of select features, scaling numeric features, one-hot encoding categorical features, and dropping or imputing features with missing values.

### Model Selection

Our chief purpose in analyzing this dataset is to develop a means for effectively categorizing its records without having any categorical labels in the dataset, the practical relevance of which may be realized, for example, through a playlist recommender. Clearly, therefore, our purpose fundamentally demands an unsupervised learning approach. With this in mind, we proceed to train and compare signature clustering models provided in the pyspark API on the preprocessed dataset, such that any categories implicitly described by the dataset may be revealed. Namely, we [compare the performances of KMeans, BisectingKMeans, and Gaussian Mixture models](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/model_comparisons.ipynb) through an analysis of the silhouette scores each produces across different numbers of clusters, and we consider the results of this analysis to select an optimal model. Additionally, through [an alternative analysis](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/Milestone4_Notebook.ipynb) involving the elbow method, we hope to confirm the results of this analysis based on silhouette scores. Finally, we aim to exemplify the practical value of our analyses by [building a playlist recommender](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/playlist_recommendation.ipynb) based on the best model prescribed by our analysis. This recommender system selects 5 random songs, allows the user to choose one, and then suggests a playlist of 12 songs from the same cluster, ensuring cohesive and enjoyable listening experiences based on similar audio features. This practical application highlights the effectiveness of our clustering model in real-world scenarios, providing personalized music recommendations. We encourage you to download and play with this notebook.

For further insights into the performance of each model, we randomly sample roughly 40 observations from each cluster generated by a given model (an informal test conducted for the [optimal KMeans model](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/samples.ipynb) and for the [optimal BisectingKMeans model](https://github.com/izDizR34567yN/DSC232R-GroupProject/blob/Milestone4/samples_2.ipynb)). From each sample, we aim to infer the character of its respective cluster. Because manual inspection may be tedious or error-prone, we instead unify the samples (with cluster labels) into a single small-scale dataset on which we train a simple decision tree classifier--in scikit-learn--and plot the resulting decision tree to extract the per-cluster insights we seek. For simplicity, we set the maximum number of leaf nodes in this decision tree classifier equal to the number of clusters used. To estimate the accuracy of the categorization mechanism embodied in a given clustering model, we calculate the proportion of correct predictions the decision tree makes on each cluster.

## Results

### Data Exploration

Our exploratory data analysis revealed that our dataset comprises 26,174,269 observations in a 28-dimensional feature space. In raw form, the dataset enforces a simple, monotonic schema in which all features are nullable strings, including those features that are semantically numerical. To enable statistical summarization of such features, we modify the given schema, casting features that are evidently numerical from string to float data types.

Our preliminary analysis further revealed that the dataset is mostly complete, with relatively few missing values. The percentage of missing values per attribute is well under 2%, and only one feature ("streams") contains a notable proportion (22.37%) of missing values. To avoid sampling bias on this feature--and because we expect the number of streams per record to be comparatively insignificant for our purposes--we simply exclude this feature from our future analyses.

Finally, through select scatterplots, we seek a very generic level of insight concerning any potentially noteworthy relationships among features. While most pairs of features appear to be largely independent, a few pairs do exhibit an obvious degree of association that confirm sensible notions about them. For example, the following plot accurately reflects the physical definition of loudness as a logarithmic function of energy:

<p align="center">
  <img src="https://github.com/izDizR34567yN/DSC232R-GroupProject/assets/169011035/73faf648-9f5a-4bba-85d1-6fe1eecec41a">
  <br>
  <strong>Figure 1 </strong> - Scatter plot illustrating the relationship between Energy and Loudness of Spotify tracks.
</p>


### Model Selection

#### Elbow Method

Upon training a KMeans model on the dataset across nine different choices for number of clusters, we are able to generate the following elbow plot:

<p align="center">
  <img src="https://github.com/izDizR34567yN/DSC232R-GroupProject/assets/169011035/b6765169-39f5-4645-a9e1-eeaa084318d4">
  <br>
  <strong>Figure 2 - Elbow Method for Optimal k:</strong> This plot illustrates the inertia values for different numbers of clusters (k) in a K-means clustering analysis.
</p>


This plot clearly suggests that seven is the optimal number of clusters to choose when training a KMeans model on our dataset.

In contrast, training a BisectingKMeans model on the dataset across the same range for the number of clusters yields the following elbow plot:

<p align="center">
  <img src="https://github.com/izDizR34567yN/DSC232R-GroupProject/assets/169011035/416fed82-68a8-4ac0-b8c3-687662a8e17f">
  <br>
  <strong>Figure 3 - Elbow Method for Optimal k (Bisecting KMeans):</strong> This plot shows the inertia values for different numbers of clusters (k) in a Bisecting KMeans clustering analysis.
</p>


Unlike the KMeans model, the optimal number of clusters is somewhat less dramatically evident in this case. Still, with reasonable judgment, one would very likely select eight as the optimal number of clusters for training a BisectingKMeans model on our dataset. By comparing this elbow plot with the one previously generated for KMeans models, one may further notice that KMeans models appear to be preferred over BisectingKMeans models for this dataset.

#### Silhouette Scores

We have successfully generated the following compact plot of performances in terms of silhouette scores:

<p align="center">
  <img src="https://github.com/izDizR34567yN/DSC232R-GroupProject/assets/169011035/30a0ab05-a50c-4f58-9fc0-e204a70f5323">
  <br>
  <strong>Figure 4 - Silhouette Score Comparison: KMeans vs GMM vs BKM:</strong> This plot compares the silhouette scores for different numbers of clusters (k) across three clustering algorithms: KMeans (red line), Gaussian Mixture Model (GMM, blue line), and Bisecting KMeans (BKM, cyan line).
</p>
   
Here again, we find that seven is the preferred number of clusters for KMeans models; that eight is the preferred number of clusters for BisectingKMeans models; and that, overall, the performance of the KMeans model is generally somewhat better than that of the BisectingKMeans models for our dataset

#### Informal Empirical Test: Decision Tree Classifier on Cluster Samples

For each of the two best models determined, by sampling roughly 40 observations from each cluster generated and training a very simple decision tree classifier on the aggregated sample, we find the following:

***Proportion of correct predictions per cluster for KMeans with seven clusters***
Cluster | 0 | 1 | 2 | 3 | 4 | 5 | 6
--- | --- | --- | --- | --- | --- | --- | ---
Accuracy | 0.0% | 100.0% | *N/A* | 100% | 88.57% | 97.87% | 100.0%

<br />

***Proportion of correct predictions per cluster for BisectingKMeans with eight clusters***
Cluster | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | --- | ---
Accuracy | 86.11% | 97.56% | 0.0% | 56.41% | 51.52% | 12.12% | 59.62% | 65.71%

## Discussion

### Model Selection

Quite fortunately, the results of our elbow method analysis and our silhouette score analysis happened to both confirm each other exactly. Namely, both analyses determined that seven is the preferred number of clusters for KMeans models; that eight is the preferred number of clusters for BisectingKMeans models; and that, overall, the performance of the KMeans model is generally somewhat better than that of the BisectingKMeans models for our dataset. Because of the rather inconvenient computational expense associated with training Gaussian generative models, we have forgone the elbow method on Gaussian generative models, and we have deemed it worthwile to only consider Gaussian generative models in the context of silhouette score analysis. This was an especially reasonable decision because our silhouette score analysis indicated that Gaussian generative models generally perform fairly poorly compared to the other two model types that we have evaluated on our dataset, and as such, Gaussian generative models did not appear to merit further analyses.

While the formal analyses described above offered ample means for deciding on an optimal model, we also sought a more quantitative, empirical perspective of how the best models performed. To this end, we conducted an informal empirical test involving sampling from the clusters generated by a given model of interest, training a small decision tree on the aggregated sample, and recording the per-cluster accuracy of the decision tree. While this is--by no means--a formal, substantive test, the results it yielded were still rather intriguing and, in some respects, even assuring. For example, the results pertaining to the best KMeans model (fit with seven clusters) reflected significantly better accuracies, in general, than that of the best BisectingKMeans model (fit with eight clusters). This was even despite the fact that the decision tree fit for the KMeans model was fit with only six maximum leaf nodes as compared to the eight maximum leaf nodes used for the BisectingKMeans. I.e., the decision tree fit for the KMeans model was somewhat more primitive than that of the BisectingKMeans model, yet it performed substantially better in discerning clusters. Thus, this test not only reaffirms our dataset's preference for KMeans models to BisectingKMeans models, but it also illustrates this preference somewhat more dramatically. Although the results of this auxiliary empirical test are therefore favorable, we do acknowledge that, admittedly, there are obvious weaknesses in how the test was carried out--namely, we largely relied on arbitrary and spontaneous intuitions applied to a single set of samples, rather than establishing more rigorous, formal methods to be applied on multiple sample sets. Nonetheless, we have at least provided a basic template--avoiding too much digression from our main objectives--for how more formal tests might be conducted through repeated sampling and perhaps through trying out other simple classifiers (e.g., decision trees with different settings for maximum leaf nodes or other types of classifiers altogether).

## Conclusion

Our diverse analysis methodologies thus all consistently affirmed that the clustering model with optimal performance for our dataset is a KMeans model fit with seven clusters. By comparison, we found that BisectingKMeans models performed worse thatn KMeans models in general, and that Gaussian mixture models tended to perform worst of all. Of course, to conform to a reasonable scale of work in reaching these conclusion, several interesting analytical prospects were necessarily neglected. Perhaps most notably, it would have been quite interesting to evaluate and compare the performances of a host of different types of available supervised learning models on the dataset after being labeled by a given clustering model. Moreover, by analyzing the decision boundaries produced by such supervised learning models, we might facilitate an intriguing means for detecting outliers or other dubious observations. We therefore trust that our analysis may have not only enabled practical applications such as the development of playlist recommendation systems, but that it may have also provided a flexible basis for further, more detailed analyses which may enhance performance in the context of this or other applications.

## Collaboration

Andre: Dataset Selection, Initial Abstract, Data Exploration, Clustering Methods Comparison Code, Playlist Recommender Code

Indumini: Final Writeup Review

Sami: Data Exploration, Revising/Debugging Elbow Method Code, Informal Empirical Test, Final Writeup

Xing: Datatset Selection, Developing Elbow Method Code
