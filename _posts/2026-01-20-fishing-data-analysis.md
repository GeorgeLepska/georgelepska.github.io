---
layout: post
title: *Predicting Trophy Striped Bass Conditions*
description: *from five years of an expert fisherman’s logbook*
date: 2026-01-20
author: George Lepska
categories: [data-analysis, fishing]
tags: [striped-bass, logistic-regression, fishbrain]
---

![Comparison photo](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/headline-final.jpg?w=961)
*Can I catch larger fish through data analysis? left: me, right: OuttaBait*

Fishing has always been a passionate hobby of mine. I've been fishing for about 10 years. My favorite fish to go after are striped bass - New England's biggest marine species.

![Me holding a striped bass](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/img_2638.jpeg?w=1024)
*Me holding a 24" Striper caught 7/17/2017*

However, there's one problem - my pb is only 29"; clearly there's something I can improve.

OuttaBait - a RI Fishbrain user with 837 striped bass catches - averages a striped bass (striper) catch length of 33.25". Can I learn how to catch bigger fish through his data?

## The Data

The data from this project comes from Fishbrain - The #1 fishing social media app. I collected OuttaBait's fishing data from January 2021 - December 2025 using an excel spreadsheet.

The model incorporates 10 variables: Fish length, fishing method (trolling, casting, and other), air temp, water temp, weather type (clear, overcast, and rainy), wind speed, wind direction, time, moon phase, and air pressure. In total, there were 378 recorded catches within the 5 years.

OuttaBait's average striped bass (striper) is 33.25" with a min of 17" and a max of 49.25". Figure 1 shows the distribution of Striped bass length.

![Striped bass length distribution](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/striper_histogram.png?w=1024)
*Figure 1: striped bass length distribution histogram illustrating the number of catches below and above the 75th percentile (n=290). Fish above the 75% percentile were classified as "trophy" fish*

A vast majority of the fish are between 25 and 40 inches, with approximately 28% being over 37 inches. We are dealing with a fishing pro.

## Model Approach

For this project, I used a logistic regression model. The model's output variable, success, classified fish below the 75th percentile (37 inches) as non-trophy and fish above the 75th percentile as trophy. After cleaning the data, there were 209 non-trophy and 81 trophy fish.

To ensure accurate results, I used an 80/20 train/test split. The model trained on 80% (232 observations) of data and tested the remaining 20% (58 observations) to provide an unbiased evaluation of the final model's performance.

## Finding the Right Variables

To identify optimal variables for my model, I performed both likelihood ratio tests and AUC comparisons across 8 potential predictor variables.

Preliminary exploration indicated that water temperature and method were the strongest predictors of fish length, shown by figures 2 and 3.

![Box plot of fishing methods](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/image-3.png?w=1024)
*Figure 2: Box plot showing the relationship between striped bass length and fishing methods - casting, trolling, and other techniques. Trolling has larger fish on average than casting and other.*

![Scatterplot of water temperature](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/image-4.png?w=1024)
*Figure 3: scatterplot showing the relationship between striper length and water temperature, highlighting seasonal variations. Larger fish are caught during summer, when water is warmer.*

In figure 2, trolling has a far higher average than the other two methods. Figure 3 tells a similar story - the largest fish are caught in the summer when water temps are warmest. Both the fishing method and water temperature play crucial roles in catching trophy fish.

What about the other variables? Likelihood Ratio Tests were then performed in table 1 to determine which additional variables would improve the model.

### Table 1: Likelihood Ratio Tests

| Additional Variable | P-Value |
|---------------------|---------|
| Weather | 0.12 |
| Time of Day | 0.21 |
| Wind Direction | 0.26 |
| Wind Speed | 0.26 |
| Moon Phase | 0.46 |
| Barometric Pressure | 0.72 |

*Results of Likelihood Ratio Tests. All additional variables above the statistically significant threshold (p < 0.05)*

No additional variable reached conventional statistical significance (p-value < 0.05). However, weather showed the strongest marginal contribution and was retained for further evaluation.

Following the likelihood ratio test, AUC (area under the curve) comparisons were done in table 2. The model with the highest AUC will be selected.

### Table 2: AUC Comparisons

| Model | Variables | AUC |
|-------|-----------|-----|
| Model 1 (Baseline) | Water temperature + Method | 0.779 |
| Model 2 | Model 1 + Weather | 0.786 |
| Model 3 | Model 2 + Time of Day | 0.753 |
| Model 4 | Model 3 + Wind Speed | 0.749 |
| Model 5 | Model 4 + Wind Direction | 0.750 |

*AUC Comparisons Chart across 5 models. Model 2 has the highest AUC, therefore making it the best selection for the final model*

Boom! Model 2 is the winner with an AUC of 0.786.

## Model Performance

What does a 0.786 AUC mean? This is best visualized using a ROC (Receiver Operating Characteristic) curve in Figure 4. The ROC curve plots the true positive rate (sensitivity) against the false positive rate (specificity) across all classification thresholds.

![ROC curve](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/image-9.png?w=1024)
*Figure 4: ROC plot. model is above red dotted line (AUC 0.5)*

The model achieved an AUC of 0.786, indicating ability in the acceptable-to-good range (0.7-0.8). This means if I chose one trophy from the test and one non-trophy from the test, the model will correctly rank it 79% of the time.

AUC's range from 0 to 1, where 0.5 is random guessing and 1.0 is perfect discrimination. The model is positioned well above the red dashed line (representing 0.5 AUC), demonstrating it's far better than random guessing.

Our model was then used on the 20% (58 fish) from the test set. The confusion matrix in table 3 shows us how well model's predictions matched actual outcomes.

### Table 3: Confusion Matrix

| Actual / Predicted | Predicted: Not Trophy | Predicted: Trophy |
|--------------------|-----------------------|-------------------|
| **Actual: Not Trophy** | 36 | 6 |
| **Actual: Trophy** | 11 | 5 |

*Confusion matrix - overall accuracy: 70.7%. correctly classified 36/47 non-trophy and 5/11 trophy fish, with a total of 41/58*

The model correctly predicted 36 non-trophy fish and 5 trophy fish, and incorrectly predicted 11 non-trophy fish and 6 trophy fish, giving a score of 41/58 or 70.7%. Not bad.

## Results of the Model

So... what factors affect catching a trophy fish? The final model quantifies the relationship between each predictor and the probability of catching a trophy fish. The results are shown in table 4.

### Table 4: Final Model Coefficients

| Term | Estimate | Odds Ratio | p-value |
|------|----------|------------|---------|
| (Intercept) | -8.19 | 0.00 | 0.000 *** |
| Water Temperature | 0.10 | 1.10 | 0.0001 *** |
| Trolling Method | 1.70 | 5.49 | 0.0001 *** |
| Other Method | 0.94 | 2.57 | 0.172 |
| Overcast Weather | 0.77 | 2.17 | 0.0468 * |
| Rainy Weather | 0.52 | 1.68 | 0.3517 |

*Significance Codes: \*\*\* p<0.001, \* p<0.05. Water temp, trolling and overcast are statistically significant predictors of striper length*

Water temperature, trolling, and overcast weather had statistically significant effects on striper length, but by how much? Figure 5 shows us to what degree each variable affects fish length.

![Effect sizes](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/practical_effects2.png?w=1024)
*Figure 5: Effect sizes of water temp, method and overcast. water temp from 50-75 degrees 12.2x odds of trophy fish, casting -> trolling 5.5x the odds of trophy fish, clear -> overcast 2.2x the odds of trophy fish.*

Since water temp is a quantitative variable and method and weather are categorical, they get interpreted differently. For water temp, each 1°F increase multiplies odds of catching a trophy by 1.1 (1.1 odds ratio), so going from 50°F to 75°F multiplies odds by 12.2x.

For method and weather, switching from casting to trolling multiplies the odds of catching a trophy by 5.5x and going from clear to overcast multiplies the odds of catching a trophy by 2.2x.

## Scenarios

Using the model's odds ratios, we can estimate the probability of catching a trophy fish under each combination of method, weather, and water temp from 50-75°F with Figure 6.

![Scenarios chart](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/image-30.png?w=1024)
*Figure 6: Scenarios chart of each combination of method, weather, and water temp from 50-75°F. high temp, overcast, and trolling creates the best chance of catching a trophy fish.*

The best scenario - 75°F water temp, trolling, and overcast - has an 80.5% success rate, while the worst scenario - 50°F water temp, casting, and clear - has a 3.1% success rate. What a difference!

## Limitations

The study is subject to some limitations:

- The data from one angler was used, which makes the data harder to generalize effects to general population and leads to idiosyncratic effects.
- The sample size was small (n=290) and categorical variables with too many parameters. A larger sample would give us better predictions and make the variables with more parameters more useful.
- OuttaBait did more trips in the summer than other seasons, which may underestimate the effects of the other three seasons.
- Survivorship bias: doesn't post days with no catch. Likely overestimates the odds of success.
- It is a linear model with nonlinear variables, water temp isn't best at the peak in figure 3.
- Only 28% of fish were trophy, creating imbalance in training data. ROC plot has a large initial slope, so it's more reliable at ruling out trophy catches (high specificity) than confidently identifying them (lower sensitivity).
- No geographical data, fish were not caught at one location, does not account for location-based effects.

These limitations provide insight for future work, with an emphasis on increased sampling, multiple anglers, non-linear modeling, and spatial data.

## Lure Data

While the model did not capture lure selection, I created a visualization for the top 5 lures OuttaBait used in 2025 based on method, season, and frequency.

![Lure comparisons](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/image-19.png?w=1024)
*Figure 7: Bar chart of lure comparisons*

The red tube is the most used lure, also, it is the only lure used for trolling in the summer. The tube troller is the best lure for catching trophies.

Here are the lures:

![Top 5 lures](https://glepska-pjtwf.wordpress.com/wp-content/uploads/2026/01/top-5-lures-final-3.png?w=948)

## Conclusion

Here are the main takeaways from the data:

1. Always troll when possible
2. Use the tube troller
3. Target warmer water (65-75°F range)
4. Overcast days provide a meaningful advantage
5. Don't worry too much about wind speed, wind direction, moon phase, barometric pressure, rainy and clear conditions, and time of day

I will be vlogging me fishing based on these results. Also, special thanks to OuttaBait for speaking with me. More to come soon!

[Link to code](https://github.com/GeorgeLepska/George-Lepska-Portfolio/blob/main/README.md)

[Link to OuttaBait](https://www.instagram.com/outta_bait/?hl=en)
