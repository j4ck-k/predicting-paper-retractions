# Predicting Paper Retractions

Peer reviewed articles are a foundational pillar of academia. In theory, the process of peer review ensures that high quality, credible, and trustworthy research that advances our current knowledge can be disseminated to the community at large. However, it is not a foolproof process and some papers do fall through the cracks and end up being retracted. Retractions indicate seriously flawed and unreliable research, errors, fraud, ethical issues, or other serious concerns. Our aim was to build a classifier that identifies papers that have a high risk of retraction. We hope that our model could be helpful in the peer-review and publication process but want to emphasize that it cannot and should not replace rigorous scrutiny from an expert.

## Data

We will use the Retraction Watch database of retracted papers and the OpenAlex database of published papers. We used the Retraction Watch database to identify PLOS One as a reputable journal that has a large number of retractions. Our dataset consists of 424,223 papers published in the journal PLOS One from 2010-2020. Data was collected from OpenAlex using the PyAlex API. We used the raw data available from OpenAlex to create features of interest such as the proportion of a paper’s authors that have been retracted previously, and various measures of how many retractions institutions associated with a paper have received. See the table below for a full list of features and their descriptions

| Feature | Description|
|:------|:---------|
|`id`                                   | Unique ID of the paper on OpenAlex |
|`title`                                | Title of paper |
|`publication_year`                     | Year the paper was published |
|`countries_distinct_count`             | Number of distinct countries associated with authors of the paper |
|`institutions_distinct_count`          | Number of institutions associated with the paper |
|`referenced_works_count`               | Number of references in the paper |
|`cited_by_count`                       | Number of citations a paper has received |
|`authors_distinct_count`               | Number of authors |
|`any_author_has_retraction`            | Boolean for whether any author on the paper has been retracted previously |
|`min_retracted_author_rank`            | Minimum rank of any author on the list of all authors with retractions, `-inf` if no author has been retracted previously. |
|`has_1pct_retracted_author`            | Boolean for whether any author is in the top 1% of most retracted authors |
|`has_5pct_retracted_author`            | Boolean for whether any author is in the top 5% of most retracted authors |
|`has_10pct_retracted_author`           | Boolean for whether any author is in the top 10% of most retracted authors |
|`top_percentile_retracted_author`      | Top percentile of any author on the list of all authors with retractions. |
|`frac_author_repeat_offenders`         | Fraction of authors that have been retracted previously. |
|`any_institution_has_retraction`       | Boolean for whether any institution has been retracted previously. |
|`min_retracted_institution_rank`       | Minimum rank of any institution on the list of all institutions with retractions, `-inf` if no institutions has been retracted previously.
|`has_1pct_retracted_institution`       | Boolean for whether any author is in the top 1% of most retracted authors
|`has_5pct_retracted_institution`       | Boolean for whether any author is in the top 5% of most retracted authors
|`has_10pct_retracted_institution`      | Boolean for whether any author is in the top 10% of most retracted authors
|`top_percentile_retracted_institution` | Top percentile of any institution on the list of all institutions with retractions. |
|`is_retracted`                         | Boolean indicating whether a paper has been retracted.

Our full dataset is too large to host on GitHub - but there is a much smaller dataset of papers published in 2021-2022 in the Data folder that you may use. To get our full datasets, follow the notebook [`openAlex_data_train_dev.ipynb`](https://github.com/j4ck-k/predicting-paper-retractions/blob/main/openAlex_data_train_dev.ipynb). We also have a notebook with examples of how to source data using the OpenAlex API, PyAlex: [`example_openAlex_data.ipynb`](https://github.com/j4ck-k/predicting-paper-retractions/blob/main/Data/OpenAlex/example_openAlex_data.ipynb).


## Modelling

We built a baseline logistic regression model and used forward stepwise subset selection to choose a small subset of features to focus on. We then compared this baseline model to nearest neighbor, random forest, and support vector classification methods. Throughout this process, we used stratified 10-fold cross validation to choose hyperparameters. The stratification here is important since our two data classes are hugely imbalanced.

A walkthrough of our modeling approach can be found in the [`classifiers.ipynb`](https://github.com/j4ck-k/predicting-paper-retractions/blob/main/classifiers.ipynb) notebook.

## Results

Our final model is a random forest classifier consisting of 500 estimators with a maximum depth of 20. In testing, this method has a F1-score of 0.289, precision of 0.691, and recall of 0.182. In particular, the high precision and low recall suggests that much of our inaccuracy stems from false negatives, rather than false positives. The overall accuracy of our classifier in training is 99.8%.
