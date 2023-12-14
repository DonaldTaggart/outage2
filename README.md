# Model Building for Outage Data
DSC 80 Project 5 | Donald Taggart

---
### Framing the Problem

   The objective of these model will be to predict the cause a major outage. They will conduct multiclass classification with the response variable they will be used to predict being the "CAUSE.CATEGORY" column. I chose this particular column because having accurate predictions for this specific data helps those in charge know why outages typically occur and can be used to help them remedy any issues that may be leading the power to go out in the first place.  My models will be evaluated based on their accuracy as it's the most common metric for classification models and will not be misleading in this particular use case given class imbalance is not a concern here. The prediction would hypothetically be formed once the energy has been restored so that all of the major feature variables are known and can therefore be utilized (such as the outage's duration).

---
### Baseline Model

   This simple model considers only two features when forming predictions (the climate category and the gross state product per capita). The climate category column is initially a categorical (nominal) column that a one hot encoder transformer can be applied to in order to turn it into multiple quantitative columns. The first column formed by said transformer was dropped because it would be redundant to include it with the others. The gross state product data was left untouched given it was already quantitative. The model ended up having accuracies of about 0.82 for the training data and 0.59 for the testing data which seems to be pretty good for a multiclass classifier that has seven different options (as compared to a binary classifier that only needs to decide between two options). For example, if the model only guessed the outage at random, the accuracy would be about 0.14 because there are seven possibilities for the cause.
   
---
### Final Model

   I included the "CLIMATE.REGION" and "OUTAGE.DURATION" columns as two new features for this final model on top of the two other columns that were utilized in the baseline model. I thought the climate region the outage took place in would be helpful because it could influence the odds of severe weather being the cause. I am also assuming the outage duration could help indicate the chances of the cause being one that would presumably take longer to repair (such as damage being inflicted by causes such as severe weather or an intentional attack). Due to there being some pretty extreme outliars in the "OUTAGE.DURATION" column, I transformed the column's data into quantiles so that the outages were still ordered by how long they lasted, but the ones that lasted extremely long would not skew the results too heavily.
   
   I will use GridSearchCV() to tune the RandomForestClassifier() hyperparameters of criterion, min_samples_split, and max_depth. The max_depth value is important because if it is too high, the model can become too complicated and overfit based on the training data, meaning that its variance can become unreasonably high when it comes to the test data. However, if max_depth is too low, bias can become too high, meaning it's important to tune properly. The min_samples_split value is also important too given it has the opposite effect of max_depth on variance and bias. The criterion value is also valuable to choose optimaly given it's the hyperparameter that decides how to measure the quality of each split. Using a cross-validation generator of 5 GridSearchCV determined that the best hyperparameter combination was a criterion value of "gini", a max_depth value of 16, and a min_samples_split value of 8. These hyperparameters led to a final model with accuracies of about 0.85 for the training data and 0.69 for the testing data, a noticeable jump from the baseline model's 0.82 and 0.59.
   
---
### Fairness Analysis

   Group X only consists of outages that occured within the WECC region and Group Y consists of any outage that occured outside of the WECC region. The evaluation metric is still accuracy given their is no class imbalance and the model is still a classifier. The null hypothesis is that the model is fair in that its accuracy is similar for the WECC region and as it is for other regions. The alternative hypothesis is that the model is not fair because its accuracy for the WECC region is not similar to that of regions that are not the WECC (in either direction). The test statistic is the absolute difference between the model's accuracy on outages within the WECC and the model's accuracy on outages outside of the WECC. I decided to use a significance level of 0.05 due to it being common within the industry and by comparing it to the smaller p-value of just under 0.01 that resulted from the permutation test, the null hypothesis can be rejected.

<iframe src="assets/test_stat_empirical_distribution.html" width=800 height=600 frameBorder=0></iframe>
