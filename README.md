
# Health Tag Prediction
Parisa Emam, Kate Alvarez

## Introduction
Our dataset comprised of two datasets merged together, one containing recipes from the cooking website Food.com and another containing user reviews and ratings for those recipes. After merging, the dataset contains 82,093 rows and 13 columns, with each row representing a reviewed recipe entry. This project focuses on the question, can we predict whether a recipe will be labeled with the 'healthy' tag using characteristics of the recipe such as the number of ingredients, cooking time, and nutritional information. 


| Column            | Description                                                                                    |
|:------------------|:-----------------------------------------------------------------------------------------------|
| Name              | Name of dish (str)                                                                             |
| minutes           | Average time it takes to prepare (int)                                                         |
| # of Ingredients  | Number of ingredients (int)                                                                    |
| Average Rating    | Average rating given by users (float)                                                          |
| Calories          | Calories in dish (float)                                                                       |
| Total Fat PDV     | Total fat (% daily value)                                                                      |
| Sugar PDV         | Sugar (% daily value)                                                                          |
| Sodium PDV        | Sodium (% daily value)                                                                         |
| Protein PDV       | Protein (% daily value)                                                                        |
| Saturated Fat PDV | Saturated fat (% daily value)                                                                  |
| Carbs PDV         | Carbohydrates (% daily value)                                                                  |
| Health Indicator  | Health score: z(protein) - z(sugar) - z(sat fat). Simplified verson of the Nutri-Score (float) |
| healthy_tag       | True if tagged healthy, else False (boolean)                                                   |


Readers of this website should care about this question because many people rely on online recipes but do not always have the time or knowledge to evaluate the nutritional information. By building a predictive model this project explores whether simple recipe features, such as preparation time and nutritional content, can help predict whether a recipe is tagged as healthy. This model would make it easier for users to quickly identify healthier recipes when browsing cooking website. 


---

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning

    The dataset was first merged on recipe id. All the 0.0's in the rating column were replaced with NaN so when performing any aggregate functions, such as averages, including 0 would not artificially deflate it. All the ratings for each recipe were averaged so there is only one overall rating per recipe. The date recipes were submitted was converted to timestamps. Nan values found in the 'description' column were replaced with empty strings. Any calorie or minute values less than 0 were removed as well as calorie and minute counts in the top 1% for being unreasonable outliers. The protein, saturated fats, and sugar columns were isolated and turned into z-scores so that the health indicator can later be calculated. Columns separating the nutritional info were also added, such as Calories, Protein PDV, Total Fat PDV, etc. 

| name                                 |   Min |   Calories |   Total Fat PDV | ...   |   Carbs PDV |   Health Indicator |
|:-------------------------------------|----------:|-----------:|----------------:|:------|------------:|-------------------:|
| Brownies  |        40 |      138.4 |              10 | ...   |           6 |         -0.383  |
| Chocolate Chip Cookies   |        45 |      595.1 |              46 | ...   |          26 |         -2.249   |
| Broccoli Casserole               |        40 |      194.8 |              20 | ...   |           3 |          0.192  |
| Millionaire Pound Cake               |       120 |      878.3 |              63 | ...   |          39 |         -4.614   |
| Meatloaf                        |        90 |      267   |              30 | ...   |           2 |          0.081 |



### Univariate Analysis
We first looked at the distribution of calories in the dishes with a histogram, revealing that the calories are skewed to the right.  

<iframe
  src="assets/calories_histogram.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>


We used a boxplot to examine the distribution of health indicator scores, which showed most values clustered between −2 and 2 with several outliers. Because of these extremes, the plot was zoomed in to clearly display the central distribution.

<iframe
  src="assets/health_boxplot.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>


### Bivariate Analysis
This scatter plot compares the number of recipe steps to sugar % daily value. Most recipes cluster under 20 steps and below 1000 sugar PDV, and the overall pattern suggests a weak negative relationship, with higher-step recipes generally having lower sugar values.

<iframe
  src="assets/StepsVSsugar.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>


This scatter plot shows the relationship between the health indicator and saturated fat % daily value. The plot reveals a clear negative relationship, where higher health indicator scores are associated with lower saturated fat levels, while less healthy recipes (lower scores) tend to have higher saturated fat.

<iframe
  src="assets/StepsVSsatfat.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>


---

### Interesting Aggregates
Here we aimed to see what factors differed in the recipes with the `”healthy”` tag and without the `”healthy”` tag and see how the median values of several features chosen would differ between the two groups. We observed many differences in several features. For example, the median calories for the recipes tagged `healthy` was 242.2 calories which is  77.6 calories less, or about a quarter, of what recipes not under the tag contained. Similarly, the median saturated_fat_PDV for healthy recipes was 6, compared to 27 for non-healthy recipes which is nearly 5 times more.These results align with the common health and fitness perceptions that lower calorie and lower saturated fat foods are healthier.

However, not all results aligned with expectations. Interestingly, recipes tagged as `"healthy"` had a higher median `”sugar_PDV`” (30 vs. 21) and a lower median protein_PDV (12 vs. 19) compared to those not tagged as `”healthy”`. Instead of reflecting solely healthiness, the `”healthy”` tag might reflect the relative healthiness a recipe has, i.e. the recipe might be a “healthier” alternative to an unhealthy food, so it might still contain a high amount of sugar, but not as much sugar as the “unhealthy” alternative.

Despite these inconsistencies in ideas, the `”health_indicator”` feature we made still aligns well with the labels as recipes tagged as `”healthy”` had a median score of ~0.93 compared to ~0.59 for the recipes not tagged, which is about 1.5x greater. 


|                        |   Num of Ingredients |   Min |   calories |   Saturated Fat PDV |   Sugar PDV |   Protein PDV |   Health Indicator |
|:-----------------------|----------:|----------:|-----------:|--------------------:|------------:|--------------:|-------------------:|
| tagged "healthy"       |      8    |     30    |     242.2  |                6    |       30    |         12    |           0.928936 |
| not tagged "healthy"   |      9    |     35    |     319.8  |               27    |       21    |         19    |           0.588704 |
| difference  (tagged - not tagged)   |     -1    |     -5    |     -77.6  |              -21    |        9    |         -7    |           0.340233 |
| ratio                  |      0.89 |      0.86 |       0.76 |                0.22 |        1.43 |          0.63 |           1.58     |

---

## Assessment of Missingness



### MNAR Analysis 

The `"average_rating"` column is likely MNAR because the missingness is tied to the user's satisfaction level, or experience they had with the recipe instructions or food. Users feel more compelled to rate recipes that evoke strongly positive or negative responses. Therefore, more neutral experiences, which would result in more average ratings would not be recorded, which would point to the missingness depending on the value of the missing rating.

### Missing Dependency 
Continuing analyzing the missingness of the `”average_rating”` column, we investigate if it’s missingness depends on the column `”Sodium_PVD”` which is the percentage of the recommended daily value intake of sodium is in a recipe, and we also investigate the `”Calories”` column which is the number of calories in each recipe. 

### Dependency Investigation – `average_rating` and `calories`: 
**Null Hypothesis:** The missingness of average_rating **does not depend** on the distribution of calories. 
**Alternative Hypothesis:** The missingness of average_rating ** does **on the distribution of calories. 

**Test Statistic:** Kolmogorov-Smirnov (K-S) Statistic
**Significance Level:** .05

<iframe
  src="assets/calories-ks-permutation-v4.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>


Because the p-value is less than 0.05, we reject the null hypothesis. The distribution of calories differs significantly depending on whether average_rating is missing. This indicates that the missingness of average_rating depends on calories, suggesting the missingness mechanism is Missing At Random 


**Observed K-S statistic** ≈ 0.0564
**P-Value** < .001 < .05 

__**Independency Investigation:**__ "average_rating" and "sodium_PDV"
**Null Hypothesis:** The missingness of average_rating **does not depend** on the distribution of the percent daily value of sodium. 
**Null Hypothesis:** The missingness of average_rating **does** on the distribution of the percent daily value of sodium. 

**Test Statistic:** Kolmogorov-Smirnov (K-S) Statistic
**Significance Level:** .05

<iframe
  src="assets/sodium_PDV-ks-permutation-v4.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>

**Observed KS statistic:**  0.0225
**P-Value** = 0.12

Because the p-value is 0.12 > 0.05, we fail to reject the null hypothesis. The missingness of ratings does not appear to have a statistically significant relationship with sodium levels in this dataset. This suggests the missingness is MCAR with respect to sodium.

---

## Hypothesis Testing
Our null hypothesis is that recipes with and without the 'healthy' tag have the same average health percentile. The alternative hypothesis is that recipes with the 'healthy' tag have a higher average health percentile than recipes without the tag. To evaluate this, we used the difference in mean health percentiles between two groups as our test statistic, and we set a significance level of .05/ We conducted a permutation test with 10,000 simulations and observed a mean health percentile difference of 0.0569, with a p-value < 0.0001, meaning that none of the simulated permutations produced a difference as large as the one observed in the actual data.

Because the p-value is below our significance level, we reject the null hypothesis. This provides strong statistical evidence suggesting that recipes labeled with the 'healthy' tag tend to have higher health percentile scores on average. We chose to do a permutation test because it does not rely on assumptions about the distribution of the data and directly tests whether the observed difference between the two groups could reasonably occur by chance if the tag were unrelated to healthiness, but this result does not prove that the 'healthy' tag perfectly identifies healthier recipes. It only indicates that there is a statistically significant association between the tag and higher health scores in this dataset

<iframe
  src="assets/diffOfMeans.html"
  width="600"
  height="400"
  frameborder="0"
></iframe>




---

## Framing a Prediction Problem
We aim to predict if the recipe was tagged `“healthy”`. This is a **binary classification** problem, which would predict **False** if there is no `“healthy”` tag in the tags of the recipe and **True**  if `“healthy”` does appear in the tags. We chose our response variable because it is easily interpretable, and doesn’t rely on us naming what is and what isn't healthy, but the perceived healthiness by the recipe makers. 

To evaluate our model we chose to use the **accuracy** metric which is the proportion of correct predictions over total predictions. We chose to use this metric because we accounted for imbalance in our model building by using `class_weight = “balanced”`, and it is more straightforward than the other metrics and shows performance. 

The features we used in our model was `“minutes”`,` “n_ingredients”`,` “calories”`, `“saturated_fat_PDV”`, `“protein_PDV”`, and `“sugar_PDV”` which are all known at the time of prediction because these features are all are properties the food contains (` “calories”`, `“saturated_fat_PDV”`, `“protein_PDV”`, `“sugar_PDV”`) or properties of the recipe (“minutes”,”n_ingredients”), which are known before any labeling or determination of the health level of the recipe. 

---

## Baseline Model
For our baseline model we are using a **logistic regression model**. We selected the `”calories”` and `”protein_PDV”` features for this model which are both continuous, quantitative features. Since both of the features are quantitative we did not need to encode. We split the data into training and test sets using an 80/20 split, where we use the test set to evaluate the model’s ability to generalize to unseen data. We instantiated the logistic regression model with  `class_weight = balanced`, because the number of recipes tagged `“healthy”` was low (around 20%). This parameter assigns higher weight to the minority class when training, so it helps prevent the model from only predicting the majority class and instead better learn the patterns associated with the minority class, which in our case are the recipes tagged `”healthy”`

 Our resulting **train accuracy is 0.501** and the **test accuracy is 0.496**. We don’t believe our model is necessarily “good” as the resulting train and test accuracies are about the same as binary guessing, which would be around 0.5 accurate. The train and test accuracy are nearly identical which is good as it suggests that the model is not overfitting to the training set. Instead, our model is actually underfitting to the data, which points to our model being too simple. 


---

## Final Model
For the final model we included 6 features:

`“protein_PDV”`, `“saturated_fat_PDV”`, `“sugar_PDV”`, `“calories”`
---

These are features that describe the nutritional makeup of the recipes, which is fundamentally linked to how healthy food is. Excluding the `”calories”` feature, these are the features we used when making our `"health_indicator"` earlier, to see if there was any correlation with these values and health. As seen in our aggregate table, the features tagged healthy had a median score of ~0.93, while those without the tag had a score of ~0.59, suggesting that these have something to do with whether or not a recipe is tagged healthy. 

We also considered how people perceive what level of which nutrients are healthy or unhealthy. For `“protein_PDV”`, higher protein is commonly associated with fitness and health.

High levels of consumption of saturated fats can raise LDL cholesterol (“bad” cholesterol), increasing risk of heart disease as said by the [American Heart Association](https://www.heart.org/en/healthy-living/healthy-eating/eat-smart/fats/saturated-fats). So, we chose `“saturated_fat_PDV”` as a feature because lower levels of saturated fats are considered healthier. This aversion to saturated fats is also reflected in our aggregate table, where the median percent daily value of recipes not tagged `”healthy”` was 27, compared to the recipes tagged healthy which had a median of 6. Sugar is another macronutrient that, when not natural and is in high quantities, is associated with unhealthiness, because of this we included `"sugar_PDV"`.

Lower calorie recipes tend to be labeled as healthy. As seen in our aggregate table, this was reflected in our aggregate table where the food tagged `“healthy”` had a median of 242.2 calories while those not tagged had a median of 319.8 calories. 

`”minutes”`
--- 

This feature measures the time it takes to complete a recipe. 

`"n_ingredients"`
---

Recipes that take longer to prepare may involve cooking from scratch which is often associated with healthier recipes. The recipes tagged `”healthy”` have a median that is one ingredient less than the number of ingredients recipes not tagged healthy have. 


From the perspective of the data generating process, we believe these features improved our models performance because with them we believe that the model can better infer the healthiness criteria recipe authors have for food when creating recipes and putting said recipe under the `”healthy”` tag. Healthiness is ultimately subjective, so the tag is likely influenced by mainstream perceptions of health, which is why we included features such as `”calories”` and `”protein_PDV,”` the former which is deemed healthy in small quantities, and the latter is deemed healthy in larger quantities. We also use features such as `”sugar_PDV”` which may capture another trend where recipes might be tagged as healthy if they are healthier relative to other recipes (such as a relatively healthier version of a dessert).

We preprocessed our data by applying a log transformation to `”minutes”` and `”calories”` because these values are highly right skewed and to make sure the model considers the spread of the data with relatively lower-values (like 200 vs 300 calories compared to 2000 vs 2200 calories). We also applied standard scaling to the nutritional percent daily value features to make sure they were on comparable scales. `”n_ingredients”` was left to pass through the ColumnTransformer since it’s not very skewed. These transformations were included in the`”preprocessor”` step in our pipeline, and for our `“model”` step we added the `max_iter = 1000` parameter to our Logistic Regression instance (which is usually at a default of `max_iter = 100`) to increase the number of iterations the model has to converge.

Our resulting model has a train accuracy of **0.682** and a test accuracy of **0.676**. This is definitely an improvement from our base model that had a test accuracy of about 0.496. Our model is now predicting better than randomly guessing between two categories, and this improvement shows that it is capturing patterns in the data. Since the test and train accuracy are really similar, the model is not overfitting and is generalizing well to unseen data. 

---

## Fairness Analysis

For our fairness model we decided to see if our model performs worse on high calorie recipes versus low calorie recipes. We defined high calories recipes as recipes with calories above the median which is 301.9 calories and low calorie recipes as recipes less than or equal to the median. We chose our evaluation metric to be precision because our model is a classification model and our goal is to see how accurately our final model can identify recipes as tagged `”healthy”`. 

**Null Hypothesis**:  Our model is fair. Its precision for high calorie and low calorie recipes are roughly the same, and any differences are due to random chance. 

**Alternative Hypothesis**:  The model is unfair. Its precision for high calorie recipes is lower than its precision for low calorie recipes

For the test statistic we measured the difference in precision between the high calorie group and the low calorie group. Our observed statistic was 0.0301 which is pretty small. We then performed a permutation test with a significance level of α = 0.05. We got a resulting p-value of 0.996 which is significantly greater than our 0.05 significance level, therefore we fail to reject the null hypothesis. There is insufficient evidence to conclude that our final model performs worse on high calorie recipes. 