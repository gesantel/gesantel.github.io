---
title: "Example Project Title"
excerpt: "One or two sentence summary of the problem this project solves."
header:
  teaser: /assets/images/example-project-thumbnail.jpg
github: "https://github.com/gesantel/example-repo"
---

## Overview

What fueled the 2023 Arizona Diamondbacks' cinderella run to the World Series? More generally, what hitting philosophy was most effective in the MLB PostSeason that year?

We answered these questions and more, built a full random forest regressor ML pipeline to predict runs, and we deployed our model publically.  


## Approach

- We cleaned and validated our data using google sheets, explored our data by building visualizations using matlotlib, seaborn, correlation heatmaps, and built an interactive dashboard on Tableau. We built a full data processing pipeline for a Random Forest Regressor model. We evaluated our model using mean squared error.
  
- Tools/libraries: Python, scikit-learn, Pandas, Seaborn, Numpy, matplotlib, Docker, FastAPI, Google Cloud Run

## Results

Summarize the outcome — a metric, a chart, a takeaway. Include an image if you have one:

![Result chart](/assets/images/example-project-result.png)
---
title: "2023 MLB Postseason Offense"
excerpt: "Interactive Tableau dashboard analyzing postseason offensive performance."
github: "https://github.com/gesantel/MLBPS23"
---

## Overview

Brief description of what the dashboard explores and why.

## Dashboard

<div class='tableauPlaceholder' id='viz1789255704323' style='position: relative'><noscript><a href='#'><img alt='Dashboard 1 ' src='https://public.tableau.com/static/images/20/2023MLBPostseasonOffenseDashboard/Dashboard1/1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='2023MLBPostseasonOffenseDashboard/Dashboard1' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https://public.tableau.com/static/images/20/2023MLBPostseasonOffenseDashboard/Dashboard1/1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /></object></div>

<script type='text/javascript'>
  var divElement = document.getElementById('viz1789255704323');
  var vizElement = divElement.getElementsByTagName('object')[0];
  if ( divElement.offsetWidth > 800 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';}
  else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';}
  else { vizElement.style.width='100%';vizElement.style.height='1127px';}
  var scriptElement = document.createElement('script');
  scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';
  vizElement.parentNode.insertBefore(scriptElement, vizElement);
</script>

## Key Findings

Summarize what the dashboard shows.




## Code

Full code, setup instructions, and technical details are in the [GitHub repository]({{ page.github }}).
