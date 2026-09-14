---
title: "MLB Hitting Philosophy for PostSeason Success"
excerpt: "What fueled the 2023 Arizona Diamondbacks' cinderella run to the World Series? More generally, what hitting philosophy was most effective in the MLB PostSeason that year? Answers and a publically deployed model."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/MLBPS23"
classes: compact-text
---

## Overview

What fueled the 2023 Arizona Diamondbacks' cinderella run to the World Series? More generally, what hitting philosophy was most effective in the MLB PostSeason that year? 

Answered these questions and more, built a full random forest regressor ML pipeline to predict runs, and deployed model publically.  


## Approach

- Cleaned and validated data using google sheets, explored data by building visualizations using matlotlib, seaborn, correlation heatmaps, and built an interactive dashboard on Tableau. Built a full data processing pipeline for a Random Forest Regressor model. Evaluated model using mean squared error.
- Target variable was runs. Chose to use mean squared error as the loss function because runs are usually small nonnegative integers less than 15 so it is important to be accurate and penalize outlying predictions. My training RMSE was about 1.38. Validated model on 2025 postseason data resulting in RMSE of 1.73.

- Tools/libraries: Python, scikit-learn, Pandas, Seaborn, Numpy, matplotlib, Docker, FastAPI, Google Cloud Run

## Summary of Results
The most important feature in predicting runs was extra base hits, and by a large margin. That was a correlation seen during data visualzation, but is now quantified by extracting model weights. From model weights we also see  the second-most important feature was not home runs, it was at-bats. This was seen earlier in the Tableau scoring percentage heatmap. This supports the theory that hitting for extra bases in a more disciplined way is a better approach for runs (this was explored more in the data visualization section). It's also important to note that both teams that made it to the world series outpreformed their predicted runs on average).

## Try It Live

This model is deployed as a REST API on Google Cloud Run. You can explore the interactive Swagger docs and test predictions directly:

[Try the live model API](https://mlb-predictor-240528909633.us-central1.run.app/docs#/default/predict_predict_post)


## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).