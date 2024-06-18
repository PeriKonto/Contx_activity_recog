# Contx_activity_recog
This document presents an overview of the methodology used to detect activties using supervised machine learning approaches. I explored the use of decision trees, random forest, bagged trees and Xgboosting algorithms. These approaches were implemented on sensor data from one day of activity after linking the activity-log (seen in export_7-12-2022_detailed.csv) to the sensor data from the Pocketlab_demonstrator.csv completed in 2022. The later contains position data (x,y,z) from multiple days and for various sensors, including the diamond drill, the jack hammer, the milling drill, the dust extractor and the underarm.

The activities I tried to detect included, Onder schouderhoogte, Boven schouderhoogte, Schouderhoogte from the underarm sensor. Data from the 2022-12-07, were selected in the analysis.

The two databases were linked using their time-stamps via a fuzzy join at one-second interval. Sensor readings were smoothed using different moving windows from 5 to 30 seconds. Accordingly, these smoothed sensor readings were annotated one of the three activities.

Balancing the three groups was also investigated. Class imbalancing was identified on the time-window selected. In machine learning, class imbalance occurs when the number of instances of one class (the majority class) significantly outweighs the number of instances of another class (the minority class). This can lead to biased models that perform poorly at predicting the minority class. It is noted that it was usual that one occupational activity was less frequent than others.

This was solved using a Synthetic Minority Over-sampling Technique: I up-sampled the minority classes while avoiding overfitting until I balanced them. This is accomplished by generating new synthetic data close to the data (belonging to the minority class) in the feature space. I used a a Synthetic Minority Over-sampling Technique (SMOTE). SMOTE works by generating synthetic examples in the feature space of the minority class by interpolating between existing instances.

In addition, after implementing the aforementioned ML approaches, a temporal data leakage was identified. This involved future information inadvertently used to predict the past outcomes which lead to overly optimistic performance estimates!!
I addressed this issue by splitting the data in a time-ordered manner: data were divided into consecutive
 training and test sets. Furthermore, the training set was allowed to grow over time, including data up to a certain point, and the test set consisted of data following a specific point in time!. This process was repeated for several splits, each time moving the cut-off point further along the time series. It is believed that this approach respects the temporal order of the observations.
Performance of the ML methods were computed, after calculating a confusion matrix, and included the sensitivity, the specificity, the accuracy and the ROC.

Tuning of the hyper-parameters was completed by searching the possible parameter combination (grid search) for each fold and evaluating with regard to the best ROC or accuracy value but this can be easily adjusted. The final parameter was selected and included in the final model for prediction using the validation data.

###Future steps [1] The labelled activity is at 1sec, which clearly is unrealistic. I have the code ready and was experimenting with different time windows (5sec - 30sec activity). I haven’t decided the grouping I am going to use (I will look at their distribution) but this needs to be generalized.

[2] I will use a conda environment to set the environement for R and each library I used. For now I suppressed the warnings. Also the coding is simple but not efficient. I will use recipes to make things better and parallelize processes to find the best tuned hyperparameters.

[3] I plan to use un-supervised machine learning approaches (no budget left), to see if I can predict the same classes at specific time points and migrate all this code to a more specialized library to test many additional routines (I could test additional ML and ANN approaches that I believe improve the generality of these methods), without adding any additional specialized libraries

[4] I plan to use a Topological Synthetic Minority Over-sampling Technique (T-SMOTE). T-SMOTE, incorporates topological information into the SMOTE algorithm to create synthetic samples that are more representative of the minority class.

[5] Improve tuned models to a different days and combine different sensors together in the prediction estimates.

[6] I plan to use Kalman filters to the sensor data since I believe this would provide a more reliable estimate of the x,y,z position estimates

[7] I didnt have time to implement a nested cross-validation, using methods of Inner Grid Search and Inner Bayesian Optimization

[8] I plan to improve the Xgboosting algorithm: tune the hyperparameters using Bayesian Optimization using the following snipset

[9] Add a routine to plot one vs rest ROC curve (evaluate multiclass models by comparing each class against all the others at the same time) using the yardstick library
