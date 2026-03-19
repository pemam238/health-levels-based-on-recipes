

## Introduction

---

## Data Cleaning and Exploratory Data Analysis



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
# help

## Hypothesis Testing

---

## Framing a Prediction Problem

---

## Baseline Model

---

## Final Model

---

## Fairness Analysis
