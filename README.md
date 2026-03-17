
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

| name                                 |   minutes |   n_steps |   n_ingredients |   average_rating |   calories |   total_fat_PDV |   sugar_PDV |   sodium_PDV |   protein_PDV |   saturated_fat_PDV |   carbs_PDV |   health_indicator | healthy_tag   |
|:-------------------------------------|----------:|----------:|----------------:|-----------------:|-----------:|----------------:|------------:|-------------:|--------------:|--------------------:|------------:|-------------------:|:--------------|
| 1 brownies in the world    best ever |        40 |        10 |               9 |                4 |      138.4 |              10 |          50 |            3 |             3 |                  19 |           6 |         -0.383264  | False         |
| 1 in canada chocolate chip cookies   |        45 |        12 |              11 |                5 |      595.1 |              46 |         211 |           22 |            13 |                  51 |          26 |         -2.24926   | False         |
| 412 broccoli casserole               |        40 |         6 |               9 |                5 |      194.8 |              20 |           6 |           32 |            22 |                  36 |           3 |          0.192644  | False         |
| millionaire pound cake               |       120 |         7 |               7 |                5 |      878.3 |              63 |         326 |           13 |            20 |                 123 |          39 |         -4.61474   | False         |
| 2000 meatloaf                        |        90 |        17 |              13 |                5 |      267   |              30 |          12 |           12 |            29 |                  48 |           2 |          0.0813278 | False         

Bivariate Analysis:


### Interesting Aggregates


| Category | calories | saturated_fat_PDV | sugar_PDV | protein_PDV | health_indicator |
|----------|----------|-------------------|-----------|-------------|------------------|
| no healthy tag | 319.8 | 27 | 21 | 19 | 0.651596 |
| healthy tag | 242.2 | 6 | 30 | 12 | 1.0682 |

---

## Assessment of Missingness


---

## Hypothesis Testing
Our null hypothesis is that recipes with and without the 'healthy' tag have the same average health percentile. The alternative hypothesis is that recipes with the 'healthy' tag have a higher average health percentile than recipes without the tag. To evaluate this, we used the difference in mean health percentiles between two groups as our test statistic, and we set a significance level of .05/ We conducted a permutation test with 10,000 simulations and observed a mean health percentile difference of 0.0569, with a p-value < 0.0001, meaning that none of the simulated permutations produced a difference as large as the one observed in the actual data.

Because the p-value is below our significance level, we reject the null hypothesis. This provides strong statistical evidence suggesting that recipes labeled with the 'healthy' tag tend to have higher health percentile scores on average. We chose to do a permutation test because it does not rely on assumptions about the distribution of the data and directly tests whether the observed difference between the two groups could reasonably occur by chance if the tag were unrelated to healthiness, but this result does not prove that the 'healthy' tag perfectly identifies healthier recipes. It only indicates that there is a statistically significant association between the tag and higher health scores in this dataset



---

## Framing a Prediction Problem

---

## Baseline Model

---

## Final Model

---

## Fairness Analysis
