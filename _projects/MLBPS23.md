---
title: "MLB Hitting Philosophy for PostSeason Success"
excerpt: "One or two sentence summary of the problem this project solves."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/MLBPS23"
classes: compact-text
---

## Overview

What fueled the 2023 Arizona Diamondbacks' cinderella run to the World Series? More generally, what hitting philosophy was most effective in the MLB PostSeason that year? We answered these questions and more, built a full random forest regressor ML pipeline to predict runs, and we deployed our model publically.  


## Approach

- Cleaned and validated data using google sheets, explored data by building visualizations using matlotlib, seaborn, correlation heatmaps, and built an interactive dashboard on Tableau. Built a full data processing pipeline for a Random Forest Regressor model. Evaluated model using mean squared error.
- Target variable was runs. Chose to use mean squared error as the loss function because runs are usually small nonnegative integers less than 15 so it is important to be accurate and penalize outlying predictions. My training RMSE was about 1.38. Validated model on 2025 postseason data resulting in RMSE of 1.73.

- Tools/libraries: Python, scikit-learn, Pandas, Seaborn, Numpy, matplotlib, Docker, FastAPI, Google Cloud Run

## Summary of Results
The most important feature in predicting runs was extra base hits, and by a large margin. That was a correlation seein in the data visualzation, but we quantified it by extracting model weights. To my surpise, the second most important feature was not home runs, it was at-bats. This is something we started to see in the Tableau scoring percentage heatmap. This gives evidence to the theory that hitting for extra bases more consistenly in a more disciplined way is a good approach for runs. It's also important to note that both teams that made it to the world series outpreformed their predicted runs (on average).

## Try It Live

This model is deployed as a REST API on Google Cloud Run. You can explore the interactive Swagger docs and test predictions directly:

[Try the live model API](https://mlb-predictor-240528909633.us-central1.run.app/docs#/default/predict_predict_post)


## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).