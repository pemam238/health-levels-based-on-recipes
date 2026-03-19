
# Health Tag Prediction
Parisa Emam, Kate Alvarez

## Introduction
Our dataset comprised of two datasets merged together, one containing recipes from the cooking website Food.com and another containing user reviews and ratings for those recipes. After merging, the dataset contains 82,093 rows and 24 columns, with each row representing a reviewed recipe entry. This project focuses on the question, can we predict whether a recipe will be labeled with the 'healthy' tag using characteristics of the recipe such as the number of steps, cooking time, and nutritional information? 

Readers of this website should care about this question because many people rely on online recipes but do not always have the time or knowledge to evaluate the nutritional information. By building a predictive model this project explores whether simple recipe features, such as preparation time, number of steps, or recipe tags, can help predict whether a recipe is tagged as healthy. This model would make it easier for users to quickly identify healthier recipes when browsing cooking website. 

The columns most relevant to our question include minutes (minutes to prepare the recipe), n_steps (number of steps in recipe), tags (Food.com tags for recipe), nutrition (nutrition information containing calories (number), total fat, sugar, sodium, protein, saturated fat, and carboydrates, measured in percentage of daily value). Using information from the nutrition column, we created a column with a health percentile that uses the equation, standardized protein amount - (standardized saturated fat amount) + (standardized sugar amount). Protein contributes positively to the score because higher protein content is generally associeated with healthier meals, while saturated fat and sugar contribute negatively because higher amounts are typically linked to less healthy nutritional profiles. This column is used to quickly compare the overall healthiness of a recipe. 

---

## Data Cleaning and Exploratory Data Analysis
Data Cleaning:
    The dataset was first merged on recipe id. All the 0.0's in the rating column were replaced with NaN so that when performing any aggregate functions, such as averages, including 0 would artificially deflate it. All the ratings per recipe were averaged so there is only one overall rating per recipe. The date recipes were submitted was converted to timestamps. Nan values found in the 'description' column were replaced with empty strings. Any calorie or minute values less than 0 were removed as well as calorie and minute counts in the top 1% for being unreasonable outliers. The protein, saturated fats, and sugar columns were isolated and turned into z-scores so that the health percentile can later be calculated. 

| name                                 |   min |   calories |   Total Fat PDV | ...   |   Carbs PDV |   Health Indicator |
|:-------------------------------------|----------:|-----------:|----------------:|:------|------------:|-------------------:|
| Brownies  |        40 |      138.4 |              10 | ...   |           6 |         -0.383  |
| Chocolate Chip Cookies   |        45 |      595.1 |              46 | ...   |          26 |         -2.249   |
| Broccoli Casserole               |        40 |      194.8 |              20 | ...   |           3 |          0.192  |
| Millionaire Pound Cake               |       120 |      878.3 |              63 | ...   |          39 |         -4.614   |
| Meatloaf                        |        90 |      267   |              30 | ...   |           2 |          0.081 |



Bivariate Analysis:




### Interesting Aggregates
Here we aimed to see what factors differed in the recipes with the healthy tag and without the healthy tag and see how the median of the columns chosen would differ signficantly. For some columns within our dataframe this was true. For example, the median calories for the recipes tagged `healthy` contained 77.6 calories less, or about a quarter, of what recipes not under the tag contained. The tagged recipes also contained a significantly higher amount of saturated_fats, about 1/5 of the amount. Interestingly, the sugar and protein content of the tagged recipes seemed to be the opposite of what we expected, where the median PDV of sugar was higher in the tagged recipes, and the median PDV of protein was lower in the tagged recipes as compared to the untagged recipes. Though, it seems our `"health_indicator"` column does seem to score the `"healthy"` recipes higher. The median `"health_indicator"` score of the tagged recipes being 1.5x higher than the median of the recipes not tagged. 

|                        |   n_steps |   minutes |   calories |   saturated_fat_PDV |   sugar_PDV |   protein_PDV |   health_indicator |
|:-----------------------|----------:|----------:|-----------:|--------------------:|------------:|--------------:|-------------------:|
| tagged "healthy"       |      8    |     30    |     242.2  |                6    |       30    |         12    |           0.928936 |
| not tagged "healthy"   |      9    |     35    |     319.8  |               27    |       21    |         19    |           0.588704 |
| difference             |     -1    |     -5    |     -77.6  |              -21    |        9    |         -7    |           0.340233 |
|  (tagged - not tagged) |           |           |            |                     |             |               |                    |
| ratio                  |      0.89 |      0.86 |       0.76 |                0.22 |        1.43 |          0.63 |           1.58     |

---

### Assessment of Missingness



## MNAR Analysis 

The `"average_rating"` column is likely MNAR because the missingness is tied to the user's satisfaction level, or experience they had with the recipe instructions or food. Users feel more compelled to rate recipes that evoke strongly positive or negative responses. Therefore, more neutral experiences, which would result in more average ratings would not be recorded, which would point to the missingness depending on the value of the missing rating.

## Missing Dependency 
Continuing analyzing the missingness of the `”average_rating”` column, we investigate if it’s missingness depends on the column `”Sodium_PVD”` which is the percentage of the recommended daily value intake of sodium is in a recipe, and we also investigate the `”Calories”` column which is the number of calories in each recipe. 

# Dependency Investigation – `average_rating` and `calories`: 
**Null Hypothesis:** The missingness of average_rating **does not depend** on the distribution of calories. 
**Alternative Hypothesis:** The missingness of average_rating ** does **on the distribution of calories. 

**Test Statistic:** Kolmogorov-Smirnov (K-S) Statistic
**Significance Level:** .05

<iframe
  src="assets/calories-ks-permuation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Because the p-value is less than 0.05, we reject the null hypothesis. The distribution of calories differs significantly depending on whether average_rating is missing. This indicates that the missingness of average_rating depends on calories, suggesting the missingness mechanism is Missing At Random 


**Observed K-S statistic** ≈ 0.0564
**P-Value** < .001 < .05 

# Independency Investigation: `average_rating` and `Sodium_PDV`
**Null Hypothesis:** The missingness of average_rating **does not depend** on the distribution of the percent daily value of sodium. 
**Null Hypothesis:** The missingness of average_rating **does** on the distribution of the percent daily value of sodium. 

**Test Statistic:** Kolmogorov-Smirnov (K-S) Statistic
**Significance Level:** .05

<iframe
  src="assets/sodium_PDV-ks-permuation.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Observed KS statistic:**  0.0225
**P-Value** = 0.12

Because the p-value is 0.12 > 0.05, we fail to reject the null hypothesis. The missingness of ratings does not appear to have a statistically significant relationship with sodium levels in this dataset. This suggests the missingness is MCAR with respect to sodium.

---

## parisaaaaaaaaaaaaaaaa
---

## Hypothesis Testing
Our null hypothesis is that recipes with and without the 'healthy' tag have the same average health percentile. The alternative hypothesis is that recipes with the 'healthy' tag have a higher average health percentile than recipes without the tag. To evaluate this, we used the difference in mean health percentiles between two groups as our test statistic, and we set a significance level of .05/ We conducted a permutation test with 10,000 simulations and observed a mean health percentile difference of 0.0569, with a p-value < 0.0001, meaning that none of the simulated permutations produced a difference as large as the one observed in the actual data.

Because the p-value is below our significance level, we reject the null hypothesis. This provides strong statistical evidence suggesting that recipes labeled with the 'healthy' tag tend to have higher health percentile scores on average. We chose to do a permutation test because it does not rely on assumptions about the distribution of the data and directly tests whether the observed difference between the two groups could reasonably occur by chance if the tag were unrelated to healthiness, but this result does not prove that the 'healthy' tag perfectly identifies healthier recipes. It only indicates that there is a statistically significant association between the tag and higher health scores in this dataset



---

## Framing a Prediction Problem
We aim to predict if the recipe was tagged `“healthy”`. This is a ** binary classification ** problem, which would predict ** False ** if there is no “healthy” tag in the tags and ** True **  if `“healthy”` does appear in the tags. We chose our response variable because it is easily interpretable, and doesn’t rely on us naming what is and what isn't healthy, but the perceived healthiness by the recipe makers. 

To evaluate our model we chose to use the accuracy metric which is the proportion of correct predictions over total predictions. We chose to use this metric because we accounted for imbalance in our model building by using `class_weight = “balanced”`, and it is more straightforward than the other metrics and shows performance. 

The features we used in our model was `“minutes”`,` “n_ingredients”`,` “calories”`, `“saturated_fat_PDV”`, `“protein_PDV”`, and `“sugar_PDV”` which are all known at the time of prediction because these features are all are properties the food contains (` “calories”`, `“saturated_fat_PDV”`, `“protein_PDV”`, `“sugar_PDV”`) or properties of the recipe (“minutes”,”n_ingredients”), which are known before any labeling or determination of the health level of the recipe. 

---

## Baseline Model

---

## Final Model

---

## Fairness Analysis
