---
title: "MLB Hitting Philosophy for PostSeason Success"
excerpt: "One or two sentence summary of the problem this project solves."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/MLBPS23"
---

## Overview

What fueled the 2023 Arizona Diamondbacks' cinderella run to the World Series? More generally, what hitting philosophy was most effective in the MLB PostSeason that year?

We answered these questions and more, built a full random forest regressor ML pipeline to predict runs, and we deployed our model publically.  


## Approach

- We cleaned and validated our data using google sheets, explored our data by building visualizations using matlotlib, seaborn, correlation heatmaps, and built an interactive dashboard on Tableau. We built a full data processing pipeline for a Random Forest Regressor model. We evaluated our model using mean squared error.
  
- Tools/libraries: Python, scikit-learn, Pandas, Seaborn, Numpy, matplotlib, Docker, FastAPI, Google Cloud Run

## Try It Live

This model is deployed as a REST API on Google Cloud Run. You can explore the interactive Swagger docs and test predictions directly:

[Try the live model API](https://mlb-predictor-240528909633.us-central1.run.app/docs#/default/predict_predict_post)



## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).
