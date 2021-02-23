# Avatar: The Last Airbender episode analysis

Avatar: The Last Airbender is one of my favorite TV shows of all time. It has been since I was a child. In this worksheet I dive deeper in the data behind the show, exploring public perception of various episodes, seeing who wrote/directed/animated which episodes, and how all of those factors influenced the ratings of the show. 

### Before we dive in

I collected the data that'll be analyzed via scraping IMDb, Wikipedia and many _many_ pages of the Avatar Fandom Wiki. I aggregated the basic information regarding each episode into a CSV file, which will be analyzed in the code below. I scraped episode transcripts of each episode as well, but that data will be used in a different analysis. The code I used to scrape everything is accessible in the atla_collection.py file and the collection_functions.py file. Now lets go!


```python
#setup code, importing packages and other data
from atla_collection import *
from analysis_functions import *
import pandas as pd 
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots
import plotly.offline as pyo
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np
import re as re

pyo.init_notebook_mode()
sns.set_theme()
```


        <script type="text/javascript">
        window.PlotlyConfig = {MathJaxConfig: 'local'};
        if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
        if (typeof require !== 'undefined') {
        require.undef("plotly");
        define('plotly', function(require, exports, module) {
            /**
* plotly.js v1.49.4
* Copyright 2012-2019, Plotly, Inc.
* All rights reserved.
* Licensed under the MIT license
*/
        });
        require(['plotly'], function(Plotly) {
            window._Plotly = Plotly;
        });
        }
        </script>




```python
#csv to DataFrame setup
basic_info = pd.read_csv("csv_files/atla_basic_info.csv")
```

## Exploring the number of ratings per episode
The first thing I wanted to do was check how many IMDb ratings people had submitted for each episode. I scraped a few different IMDb webpages in order to get this information, and the rating counts can be seen below, colored by each season. 


```python
#plot to show the number of people who gave ratings for each episode
plt.figure(figsize = (15, 10))
plt.title("Number of People Who Gave Ratings For Each Episode of Avatar The Last Airbender")
plt.yticks(range(0, 8000, 1000))

g = sns.barplot(x = basic_info.episode_number_overall, y = basic_info.total_votes_on_ratings, 
                hue = basic_info.season, dodge = False, palette = ["blue", "green", "red"])
g
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243a8ac8b48>




![png](output_4_1.png)


## The graphs and what they mean
The number of ratings for each episode is fairly variable but it is evident that a bulk of the episodes have in between 2000 and 3000 reviews. The histogram in the first cell below shows the distribution of number of ratings of each episodes and confirms this. 

I believe it likely that the episodes that surpass the 3000 threshold are episodes generally considered the best/most popular episodes by fans. Their titles are listed in the second cell below.

In the third cell below, we see a pie chart showing how the episodes with over 3000 ratings were distributed throughout the seasons. Overall, season 3 holds the highest percentage of those, leading me to believe that it is the most popular season overall

Also, obviously, the graph in the cell above show that the series finale had the highest number of ratings by far and as we will see, it also has the highest average rating too


```python
plt.figure(figsize = (12, 7))
plt.title("Distribution of the Number of Ratings for Each Episode")

basic_info.total_votes_on_ratings.describe()
sns.histplot(data = basic_info, x = "total_votes_on_ratings", color = "#BB0000")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243a8990a48>




![png](output_6_1.png)



```python
basic_info.loc[basic_info.total_votes_on_ratings > 3000].title
```




    0                      The Boy in the Iceberg
    1                          The Avatar Returns
    12                            The Blue Spirit
    18             The Siege of the North, Part 1
    26                                 Zuko Alone
    34                    The Tales of Ba Sing Se
    39                  The Crossroads of Destiny
    45               The Avatar and the Fire Lord
    58     Sozin's Comet, Part 2: The Old Masters
    59    Sozin's Comet, Part 3: Into the Inferno
    60         Sozin's Comet, Part 4: Avatar Aang
    Name: title, dtype: object




```python
#popular_episodes
lots_of_votes = basic_info.loc[basic_info.total_votes_on_ratings > 3000]
fig = go.Figure(go.Pie(labels = lots_of_votes.season, values = lots_of_votes.total_votes_on_ratings, 
                       hole = 0.4))
fig.update_traces(textinfo = 'label+percent', marker = dict(line = dict(color='#000000', width=1)))
fig.update_layout(title = "Season Distribution of the Episodes With Over 3000 IMDb Ratings")
fig
```


<div>


            <div id="05573229-c64d-4ed2-8d49-2332f0beac5d" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("05573229-c64d-4ed2-8d49-2332f0beac5d")) {
                    Plotly.newPlot(
                        '05573229-c64d-4ed2-8d49-2332f0beac5d',
                        [{"hole": 0.4, "labels": [1, 1, 1, 1, 2, 2, 2, 3, 3, 3, 3], "marker": {"line": {"color": "#000000", "width": 1}}, "textinfo": "label+percent", "type": "pie", "values": [3386, 3012, 3042, 3183, 3581, 3683, 3590, 3182, 3240, 4276, 6669]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Season Distribution of the Episodes With Over 3000 IMDb Ratings"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('05573229-c64d-4ed2-8d49-2332f0beac5d');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


## Episode ratings vs the number of votes for each rating
The first two cells below shows the correlation coefficient between ratings and the number of votes for each episode.
The first cell considers outliers, the second one doesn't.

Their is an apparent relationship here. Episodes with higher average ratings tended to have more total reviews. It is also interesting to see the massive spike in total reviews as average ratings approach 10. The scatterplots visualizing these relationships are in the third and fourth cells below. The third cell considers outliers, the fourth cell does not


```python
np.corrcoef(basic_info.ratings, basic_info.total_votes_on_ratings)[0][1] #with outliers
```




    0.4642951237617395




```python
sans_outliers = basic_info.loc[(basic_info.total_votes_on_ratings < 6000) & (basic_info.total_votes_on_ratings > 2200)]
np.corrcoef(sans_outliers.ratings, sans_outliers.total_votes_on_ratings)[0][1] # outliers removed (Season 1 and Season 3 finales)
```




    0.5303849804077386




```python
#scatter plot of ratings versus number of votes
plt.figure(figsize = (12, 8))
plt.title("Ratings vs Total Number of Votes for Each Rating, with Outliers")
sns.scatterplot(data = basic_info, x = "ratings", y = basic_info.total_votes_on_ratings, 
                hue = "season", palette = ["#3377FF", "Green", "Red"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243aa70e948>




![png](output_12_1.png)



```python
plt.figure(figsize = (12, 8))
plt.title("Ratings vs Total Number of Votes for Each Rating, with no Outliers")
sns.scatterplot(data = sans_outliers, x = "ratings", y = "total_votes_on_ratings", 
                hue = "season", palette = ["#3377FF", "Green", "Red"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243aa40f048>




![png](output_13_1.png)


# Exploring the Ratings of the Episodes

After examining total reviews, next thing I wanted to do was analyze the average ratings themselves for each episode. I scraped this data off of IMDb as well. They are what the bulk of the analysis is focused on. I was curious on how people felt about the quality of each episode, and how the various writers, animators, and directors affected those ratings as well.

The first pieces of information below are a basic description of the distribution of the ratings and its corresponding visualization via boxplot. The ratings are firmly centered around the mid-to-upper 8's. It's ever so slightly left skewed and his a relatively small standard deviation.


```python
basic_info.ratings.describe()
```




    count    61.000000
    mean      8.690164
    std       0.612564
    min       7.000000
    25%       8.200000
    50%       8.700000
    75%       9.100000
    max       9.900000
    Name: ratings, dtype: float64




```python
plt.figure(figsize = (15, 2))
plt.title("Distribution of Avatar The Last Airbender Episode Ratings Overall")
plt.yticks(range(0, 11))

g = sns.boxplot(x = basic_info.ratings)
```


![png](output_16_0.png)


# Ratings over the Seasons
In the first cell below, the ratings of each episode are shown as a bar graph, color coded by season. The bar graph mostly just displays the information calculated in the previous cell but this time it shows the variability in the ratings throughout all episodes.

The graph in the second cell down is almost exactly like the bar graph in the first in terms of what's being shown, but and the region of interest on the y-axis is more focused. In this graph it's more apparent that the rating averages increase with each successive season

In the third below cell, the boxplot fleshes out this relationship clearly. 

> __With each passing season, the average ratings go up__




```python
#plot to show show ratings for each episode
plt.figure(figsize = (16, 10))
plt.title("Avatar The Last Airbender Episode Ratings")
plt.yticks([0, 1, 2, 3, 4, 5, 6, 7 ,8, 9, 10])

g = sns.barplot(x = basic_info.episode_number_overall, y = basic_info.ratings, 
            hue = basic_info['season'].astype("str"), palette = ["#3377FF", "Green", "Red"], dodge = False)
g
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243abb4f988>




![png](output_18_1.png)



```python
plt.figure(figsize = (15, 10))
plt.title("Avatar The Last Airbender Episode Ratings")
plt.yticks([0, 1, 2, 3, 4, 5, 6, 7 ,8, 9, 10])
sns.lineplot(data = basic_info, x = "episode_number_overall", y = "ratings", hue = "season", palette = ["#3377FF", "green", "red"])
sns.scatterplot(data = basic_info, x = "episode_number_overall", y = "ratings", hue = "season", palette = ["#3377FF", "green", "red"], legend = False)

```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ac3da108>




![png](output_19_1.png)



```python
plt.figure(figsize = (15, 10))
plt.title("Distribution of Avatar The Last Airbender Episode Ratings Per Season ")
plt.yticks(range(0, 11))
sns.set_theme(style = "darkgrid")

g = sns.boxplot(x = basic_info.season, y = basic_info.ratings, 
            hue = basic_info['season'].astype("str"), palette = ["#3377FF", "green", "red"])

```


![png](output_20_0.png)


## Progression of the ratings of each season
The line graph, below shows the ratings of each episode __of that particular season__. 

Most of the graph is clearly a jumbled mess, but a clear relationship forms at the end. The seasons always end with a ratings increases in the last 3-4 episodes. 

> So in other words, ___they didn't slip up once when closing out season___

There is also that season 1 outlier shown by the blue line. We will get to that later 


```python
plt.figure(figsize = (15, 10))
#plt.yticks([0, 1, 2, 3, 4, 5, 6, 7 ,8, 9, 10])

sns.lineplot(data = basic_info, x = "episode_number_in_season", y = "ratings", hue = "season", palette = ["#3377FF", "green", "red"])
sns.scatterplot(data = basic_info, x = "episode_number_in_season", y = "ratings", hue = "season", palette = ["#3377FF", "green", "red"], legend = False)

```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ac0b2c08>




![png](output_22_1.png)



```python
#a bunch of setup
#we won't use the count df's to graph, but they're useful in their own right

#episodes with a rating less than 7.5
trash_ratings = basic_info.loc[basic_info.ratings < 7.5]
trash_count = trash_ratings.season.value_counts().to_frame().reset_index().rename({"season":"number", "index":"season"}, axis = 1)

#episodes with a ratings < 8
subpar_ratings = basic_info.loc[basic_info.ratings < 8]
subpar_count = subpar_ratings.season.value_counts().to_frame().reset_index().rename({"season":"number", "index":"season"}, axis = 1)

#episodes with ratings > 8.5
good_ratings = basic_info.loc[basic_info.ratings > 8.5]
good_count = good_ratings.season.value_counts().to_frame().reset_index().rename({"season":"number", "index":"season"}, axis = 1)

#episodes with ratings > 9.0
great_ratings = basic_info.loc[basic_info.ratings > 9.0]
great_count = great_ratings.season.value_counts().to_frame().reset_index().rename({"season":"number", "index":"season"}, axis = 1)

#episodes with ratings > 9.5
fantastic_ratings = basic_info.loc[basic_info.ratings > 9.5]
fantastic_count = great_ratings.season.value_counts().to_frame().reset_index().rename({"season":"number", "index":"season"}, axis = 1)
```

# Which episodes are considered good? Bad? Average?

We're gonna go deeper and focus on the episodes themselves, sort out which are good, which are bad, and which are just average. The ratings are sorted as follows

* Fantastic - Greater than 9.5
* Great - Greater than 9
* Good - Greater than 8.5
* Subpar - Less than 8
* Trash - Less than 7.5


## We will focus on the two groups of below average episodes first.
So for episodes that didn't land compared to the others, the plot below shows how many episodes of each season are considered subpar and trash by the criteria listed above. Their ratings are shown as well as their titles and what season the episode is from. Below is the counts for subpar episodes

#### Subpar Episodes
* Season 1: four subpar episodes
* Season 2: two subpar episodes
* Season 3: one subpar episode

For the trash episodes, there is only one: __The Great Divide__, an episode from Season 1, widely regarded as the worst episode in the whole franchise. That was the cause of the massive downward spike in the blue line of the line graph up above

Also on a personal note, I don't believe The Swamp is a subpar episode. It's one of my favorites :(


```python
#need to create titles for all subplots
fig, axes = plt.subplots(2, 2, figsize = (15, 10))
axes[0][0].set_title("Avatar The Last Airbender Episode Bad Episodes Per Season")
axes[0][1].set_title("Number of Subpar Episodes Per Season")
plt.sca(axes[1, 1])
plt.yticks([0, 1, 2])

#subpar ratings subplots
sns.barplot(ax = axes[0][0], data = subpar_ratings, x = "ratings", y = "title", hue = "season", 
            orient = "h", dodge = False, palette = ["#3377FF", "green", "red"])
sns.countplot(ax = axes[0][1], data = subpar_ratings, x = "season", palette = ["#3377FF", "green", "red"])

#trash ratings subplots
sns.barplot(ax = axes[1][0], data = trash_ratings, x = "ratings", y = "title", hue = "season", 
            orient = "h", palette = ["#3377FF", "green", "red"])
sns.countplot(ax = axes[1][1], data = trash_ratings, x = "season", 
              hue = "season", palette = ["#3377FF", "green", "red"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ac988d88>




![png](output_26_1.png)


## Now we look through the rest.

__To be clear before we go further:__ There are twenty episodes in season 1, twenty episodes in season 2 and twenty-one episodes in season 3. The numbers below will not add up to that because certain episodes will fit into multiple categories based on how good their rating is. For instance, Zuko Alone (Season 2 Episode 7) has a rating of would fit into the good and great categories but would not fit into the fantastic category as it isn't greater than 9.5 (it's actually exactly 9.5)

### Good Episodes
* Season 1: six good episodes
* Season 2: fifteen good episodes
* Season 3: seventeen good episodes

### Great Episodes
* Season 1: four great episodes
* Season 2: six great episodes
* Season 3: ten great episodes

### Fantastic Episodes
* Season 1: one fantastic episode
* Season 2: one fantastic episode
* Season 3: two fantastic episodes

These graphs consistently show that as the seasons progress, their quality gets higher. Furthermore all of the episodes with fantastic ratings are season finale episodes, once again showing that the show never once flopped on an ending (RIP Game of Thrones, RIP Dexter).


```python
#comparing the good, great, and fantastic episodes of each season of Avatar the Last Airbender
fig, axes = plt.subplots(3, 2, figsize = (12, 24))
axes[0][0].set_title("Names of Avatar The Last Airbender Good Episodes")
axes[0][1].set_title("Number of Good Avatar The Last Airbender Episodes Per Season")
axes[1][0].set_title("Names of Avatar The Last Airbender Great Episodes")
axes[1][1].set_title("Number of Great Avatar The Last Airbender Episodes Per Season")
axes[2][0].set_title("Names of Avatar The Last Airbender Fantastic Episodes")
axes[2][1].set_title("Number of Fantastic Avatar The Last Airbender Episodes Per Season")
plt.sca(axes[2, 1])
plt.yticks([0, 1, 2])

#good ratings subplots
sns.barplot(ax = axes[0][0], data = good_ratings, x = "ratings", y = "title", hue = "season", 
            orient = "h", dodge = False, palette = ["#3377FF", "green", "red"])
sns.countplot(ax = axes[0][1], data = good_ratings, x = "season", palette = ["#3377FF", "green", "red"])

#great ratings subplots
sns.barplot(ax = axes[1][0], data = great_ratings, x = "ratings", y = "title", hue = "season", 
            orient = "h", dodge = False, palette = ["#3377FF", "green", "red"])
sns.countplot(ax = axes[1][1], data = great_ratings, x = "season", palette = ["#3377FF", "green", "red"])

#fantastic ratings subplots
sns.barplot(ax = axes[2][0], x = fantastic_ratings.ratings, y = fantastic_ratings.title, hue = fantastic_ratings.season, 
            orient = "h", dodge = False, palette = ["#3377FF", "green", "red"])
sns.countplot(ax = axes[2][1], data = fantastic_ratings, x = "season", palette = ["#3377FF", "green", "red"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243acbe1c88>




![png](output_28_1.png)


# Directors and their connection to ratings
Now we're going to start looking at how different directors can impact the ratings of an episode. To do that, we first start off with graphing a plot, showing the different directors and how many episodes they directed.

Immediately it stands out that Giancarlo Volpe dominates the field in terms of quantity of episodes directed, with Ethan Spaulding, Lauren MacMullan, Joaquim Dos Santos and Dave Filoni also directing their fair shares of episodes.


```python
plt.figure(figsize = (15, 10))
plt.yticks(range(0, 20))
plt.title("Avatar The Last Airbender Number of Episodes Each Director Directed")

sns.countplot(data = basic_info, x = "directors", order = basic_info["directors"].value_counts().index)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ace4fe88>




![png](output_30_1.png)


## Directors by Season

The figure below shows the median ratings of each of the directors split up by season. Some only have one or two bars because they only directed episodes for one or two of the seasons. 

While Michael Dante Dimartino seems to be top dog, but he only directed one episode, the finale of season 2 so it isn't exactly a fair comparsion.

On the other end, Dave Filoni and Anthony Lioi both had comparatively lower average ratings with 8.05 and 8.10 respectively


```python
#median ratings of episodes directed by a given director, split up by season

plt.figure(figsize = (14, 8))
plt.title("Avatar The Last Airbender Median Ratings By Season and Director")
plt.yticks(range(0, 11))

directors = basic_info.groupby(["season", "directors"]).ratings.median().to_frame().reset_index()
directors

sns.barplot(data = directors.sort_values(["ratings"]), x = "directors", y = "ratings", 
            hue = "season", palette = ["#3377FF", "green", "red"])
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ad564f48>




![png](output_32_1.png)



```python
directors.loc[(directors.season == 1) & (directors.directors.isin(["Dave Filoni", "Anthony Lioi"]))]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>season</th>
      <th>directors</th>
      <th>ratings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1</td>
      <td>Anthony Lioi</td>
      <td>8.10</td>
    </tr>
    <tr>
      <td>1</td>
      <td>1</td>
      <td>Dave Filoni</td>
      <td>8.05</td>
    </tr>
  </tbody>
</table>
</div>



## When did the directors direct their episodes?

In the figure below, there are three pie charts, one for each season. Each pie chart shows the proportion and number of episodes directed by a given director in that season.

Personally, I was surprised that there was as much of a turnover as their was. Before I started, I had always just kind of thought that there were only one or two directors always running the show, but it turns out there were seven and they passed in and out throughout the seasons. 

For instance
* Dave Filoni only directed episodes in Season 1
* Joaquim Dos Santos only directed episodes in Season 3 (where he did a good job - more on that later)
* Lauren MacMullan never directed anything past Season 2

The directors are responsible for many crucial elements of each episode. Personally I find it incredible that despite the turnover in directors the show still managed to keep a steady vision. 


```python
#plotly pie chart subplot attempt

#first split it all up by season
season_1 = basic_info.loc[basic_info.season == 1]
season_2 = basic_info.loc[basic_info.season == 2]
season_3 = basic_info.loc[basic_info.season == 3]

#season_1.directors.value_counts().index

fig = make_subplots(rows = 1, cols = 3, specs = [[{'type':'domain'}, {'type':'domain'}, {'type':'domain'}]])

fig.append_trace(go.Pie(labels = season_1.directors.value_counts().index, 
                     values = season_1.directors.value_counts(), title = "Season 1"), row = 1, col = 1)
fig.append_trace(go.Pie(labels = season_2.directors.value_counts().index, 
                     values = season_2.directors.value_counts(), title = "Season 2"), row = 1, col = 2)
fig.append_trace(go.Pie(labels = season_3.directors.value_counts().index, 
                     values = season_3.directors.value_counts(), title = "Season 3"), row = 1, col = 3)
fig.update_traces(textinfo = "value+percent", pull = 0.05, marker=dict(line=dict(color='#000000', width=1)))
fig.update_layout(height = 400, title_text = "Avatar The Last Airbender Director Proportions By Season")
```


<div>


            <div id="331ef94b-b29b-4cc9-b302-dc8c42ac54fb" class="plotly-graph-div" style="height:400px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("331ef94b-b29b-4cc9-b302-dc8c42ac54fb")) {
                    Plotly.newPlot(
                        '331ef94b-b29b-4cc9-b302-dc8c42ac54fb',
                        [{"domain": {"x": [0.0, 0.2888888888888889], "y": [0.0, 1.0]}, "labels": ["Dave Filoni", "Lauren MacMullan", "Giancarlo Volpe", "Anthony Lioi"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 1"}, "type": "pie", "values": [8, 5, 5, 2]}, {"domain": {"x": [0.35555555555555557, 0.6444444444444445], "y": [0.0, 1.0]}, "labels": ["Giancarlo Volpe", "Ethan Spaulding", "Lauren MacMullan", "Michael Dante DiMartino"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 2"}, "type": "pie", "values": [7, 6, 6, 1]}, {"domain": {"x": [0.7111111111111111, 1.0], "y": [0.0, 1.0]}, "labels": ["Joaquim Dos Santos", "Giancarlo Volpe", "Ethan Spaulding"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 3"}, "type": "pie", "values": [8, 7, 6]}],
                        {"height": 400, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Avatar The Last Airbender Director Proportions By Season"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('331ef94b-b29b-4cc9-b302-dc8c42ac54fb');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


## Writers

Next up we are going to check out the writers. I got information on writers of each episode from Wikipedia. While they generally only credited one to two people per episode, I assumed the people credited for a given episode were the main writers of that episode. Lets dive on in!

What we in the first cell below the number of episodes each writer is credited with writing. 

__What struck me immediately__ was the comparatively few episodes __Bryan Konietzko__ was credited as main writer on. For more context, Avatar: The Last Airbender was created by Michael Dante DiMartino and Bryan Konietzko (Called "Bryke" by the fandom for reasons I cannot fathom) Michael Dante DiMartino was credited on 14 episodes, but Bryan Konietzko was credited on only 7, or half of that. 

Other people who played a major role in the writing of episodes were:
* John O'Bryan (13)
* Aaron Ehasz (10)
* Tim Hedrick (10)
* Elizabeth Welch Ehasz 8)
* Joshua Hamilton (6)

In the second cell below, there is a massive bar chart showing that information in a more visualy appealing context


```python
unique_writer_tags = basic_info.writers.unique()
real_unique_writers = []
for tag in unique_writer_tags:
    new_tag = re.split(' and |,', tag)
    for name in new_tag:
        if name.strip() not in real_unique_writers:
            real_unique_writers.append(name.strip())

writer_df = pd.DataFrame()

for writer in real_unique_writers:
    writer_df[writer] = basic_info.writers.str.contains(writer)
    
#writer_df['Michael Dante DiMartino']

writer_counts_df = pd.DataFrame(columns = ["writer", "writer_count"])
writer_counts = []
for writer in real_unique_writers:
    writer_counts.append(writer_df[writer].value_counts()[True])
    
writer_counts_df["writer"] = real_unique_writers
writer_counts_df["writer_count"] = writer_counts
writer_counts_df.sort_values("writer_count", ascending = True)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>writer</th>
      <th>writer_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>20</td>
      <td>Josh Hamilton</td>
      <td>1</td>
    </tr>
    <tr>
      <td>18</td>
      <td>Giancarlo Volpe</td>
      <td>1</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Nick Malis</td>
      <td>1</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Justin Ridge</td>
      <td>1</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Matthew Hubbard</td>
      <td>1</td>
    </tr>
    <tr>
      <td>6</td>
      <td>James Eagan</td>
      <td>1</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Ian Wilcox</td>
      <td>1</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Lauren Macmullan</td>
      <td>1</td>
    </tr>
    <tr>
      <td>14</td>
      <td>Gary Scheppke</td>
      <td>1</td>
    </tr>
    <tr>
      <td>19</td>
      <td>May Chan</td>
      <td>1</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Joann Estoesa</td>
      <td>1</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Lisa Wahlander</td>
      <td>1</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Andrew Huebner</td>
      <td>1</td>
    </tr>
    <tr>
      <td>16</td>
      <td>Katie Mattila</td>
      <td>2</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Joshua Hamilton</td>
      <td>6</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Bryan Konietzko</td>
      <td>7</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Elizabeth Welch Ehasz</td>
      <td>8</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Tim Hedrick</td>
      <td>10</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Aaron Ehasz</td>
      <td>10</td>
    </tr>
    <tr>
      <td>3</td>
      <td>John O'Bryan</td>
      <td>13</td>
    </tr>
    <tr>
      <td>0</td>
      <td>Michael Dante DiMartino</td>
      <td>14</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure(figsize = (15, 10))
plt.title("Number of Episodes Each Writer is Credited as Head Writing (According to Wikipeida)")
plt.xticks(rotation=45)

sns.barplot(data = writer_counts_df.sort_values("writer_count", ascending = True), 
            x = "writer", y = "writer_count")
```




    <matplotlib.axes._subplots.AxesSubplot at 0x243ad637508>




![png](output_38_1.png)


## Animation Studios

Now we're on to what I consider the most important part of any good cartoon. I had absolutely no knowledge of the animation studios behind the series when I started and I was curious if they would affect the ratings at all. 

The graphs below reveal that there were three animation studios that worked on the series. They were:

* DR Movie
* JM Animation
* Moi Animation


DR Movie animated episodes in seasons 1 and 2. Moi Animation animated episodes in season 3 when DR Movie didn't come back. JM Animation animated episodes in all three seasons as show in the bar graph in the second cell down. 

The bar graph shows the median ratings of each studio by season. Upon inspection, one can see that in seasons 1 and 2, there wasn't really much of a difference in ratings that could be explained by which studio animated the episodes, however in season 3 the median ratings of episodes animated by JM Animation are noticeably higher than the median ratings of the ones directed by Moi Animation. This can be explained by the fact that JM Animation animated three of the four Sozins Comet finale episodes of season 3. The median ratings of JM animation episodes without the finale lowers to 8.9 as calculated in the first cell below.

JM Animation animated the most episodes overall as one would expect given the information that they were the only studio that animated episodes in all three seasons


The pie chart shows that each season was split roughly in half - one half of the episodes animated by one studio and the other half animated by the other.


```python
np.median(basic_info.loc[(basic_info.animation_studio == "JM Animation") & (basic_info.season == 3) & (basic_info.episode_number_overall < 57)].ratings)
```




    8.9




```python
#Animation studios median ratings by season
plt.figure(figsize = (12, 7))
plt.yticks(np.arange(0, 10))
animators = basic_info.groupby(["animation_studio", "season"]).median().ratings.to_frame().reset_index()

sns.barplot(data = animators.sort_values("ratings"), x = "animation_studio", y = "ratings", 
            hue = "season", palette = ["#3377FF", "green", "red"]).set_title("Animation Studio Median Ratings by Season")
plt.legend(loc = 4)
```




    <matplotlib.legend.Legend at 0x243ae54a908>




![png](output_41_1.png)



```python
#shows how many episodes each animation studio animated
animator_series = basic_info.animation_studio.value_counts()
fig = go.Figure(data = go.Pie(labels = animator_series.index, values = animator_series, 
                              hole = 0.65, textinfo = 'label+value+percent', textfont_size = 15,
                              marker=dict(line=dict(color='#000000', width=1))))
fig.update_layout(title = "Distribution of Which Studios Animated the Avatar Episodes")
fig
```


<div>


            <div id="294a6d7a-e03f-4e36-abfd-65d860535a77" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("294a6d7a-e03f-4e36-abfd-65d860535a77")) {
                    Plotly.newPlot(
                        '294a6d7a-e03f-4e36-abfd-65d860535a77',
                        [{"hole": 0.65, "labels": ["JM Animation", "DR Movie", "Moi Animation"], "marker": {"line": {"color": "#000000", "width": 1}}, "textfont": {"size": 15}, "textinfo": "label+value+percent", "type": "pie", "values": [32, 19, 10]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Distribution of Which Studios Animated the Avatar Episodes"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('294a6d7a-e03f-4e36-abfd-65d860535a77');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>



```python
#pie charts showing distribution by season
#first split em up
animators_season_1 = basic_info.loc[basic_info.season == 1]
animators_season_2 = basic_info.loc[basic_info.season == 2]
animators_season_3 = basic_info.loc[basic_info.season == 3]

#do tha thang
fig = make_subplots(rows = 1, cols = 3, specs = [[{'type':'domain'}, {'type':'domain'}, {'type':'domain'}]])

fig.append_trace(go.Pie(labels = animators_season_1.animation_studio.value_counts().index, 
                     values = animators_season_1.animation_studio.value_counts(), title = "Season 1"), row = 1, col = 1)
fig.append_trace(go.Pie(labels = animators_season_2.animation_studio.value_counts().index, 
                     values = animators_season_2.animation_studio.value_counts(), title = "Season 2"), row = 1, col = 2)
fig.append_trace(go.Pie(labels = animators_season_3.animation_studio.value_counts().index, 
                     values = animators_season_3.animation_studio.value_counts(), title = "Season 3"), row = 1, col = 3)
fig.update_traces(textinfo = "value+percent", pull = 0.05, marker=dict(line=dict(color='#000000', width=1)))
fig.update_layout(height = 400, title_text = "Avatar The Last Airbender Animation Studios Proportions By Season")
fig
```


<div>


            <div id="59fbe9f4-eeb8-40e2-a377-8ae867c4360a" class="plotly-graph-div" style="height:400px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("59fbe9f4-eeb8-40e2-a377-8ae867c4360a")) {
                    Plotly.newPlot(
                        '59fbe9f4-eeb8-40e2-a377-8ae867c4360a',
                        [{"domain": {"x": [0.0, 0.2888888888888889], "y": [0.0, 1.0]}, "labels": ["JM Animation", "DR Movie"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 1"}, "type": "pie", "values": [11, 9]}, {"domain": {"x": [0.35555555555555557, 0.6444444444444445], "y": [0.0, 1.0]}, "labels": ["DR Movie", "JM Animation"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 2"}, "type": "pie", "values": [10, 10]}, {"domain": {"x": [0.7111111111111111, 1.0], "y": [0.0, 1.0]}, "labels": ["JM Animation", "Moi Animation"], "marker": {"line": {"color": "#000000", "width": 1}}, "pull": 0.05, "textinfo": "value+percent", "title": {"text": "Season 3"}, "type": "pie", "values": [11, 10]}],
                        {"height": 400, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Avatar The Last Airbender Animation Studios Proportions By Season"}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('59fbe9f4-eeb8-40e2-a377-8ae867c4360a');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


## Directors and Animation Studios
Below is a heatmap, showing average episode ratings of a given director/animator combo. The plot below gives a convincing argument that the combination of JM Animation and either Joaquim Dos Santos or Ethan Spaulding lead to the best episodes on average


```python
#creating a grouped table to find the optimal director/animator combo

#grouped animator/director/season
grouped_ads = basic_info.groupby(["animation_studio", "directors", "season"]).ratings.agg([min, max, "mean", len]).reset_index()
grouped_ads = grouped_ads.loc[grouped_ads.directors != "Michael Dante DiMartino"]

fig = go.Figure(data = go.Heatmap(x = grouped_ads["animation_studio"], y = grouped_ads["directors"], z = grouped_ads["mean"],
                                 colorscale = "sunset"))
fig
```


<div>


            <div id="d0efe397-f7f7-40bc-aba7-25871c055b09" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("d0efe397-f7f7-40bc-aba7-25871c055b09")) {
                    Plotly.newPlot(
                        'd0efe397-f7f7-40bc-aba7-25871c055b09',
                        [{"colorscale": [[0.0, "rgb(243, 231, 155)"], [0.16666666666666666, "rgb(250, 196, 132)"], [0.3333333333333333, "rgb(248, 160, 126)"], [0.5, "rgb(235, 127, 134)"], [0.6666666666666666, "rgb(206, 102, 147)"], [0.8333333333333334, "rgb(160, 89, 160)"], [1.0, "rgb(92, 83, 165)"]], "type": "heatmap", "x": ["DR Movie", "DR Movie", "DR Movie", "DR Movie", "DR Movie", "DR Movie", "DR Movie", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "JM Animation", "Moi Animation", "Moi Animation", "Moi Animation"], "y": ["Anthony Lioi", "Dave Filoni", "Ethan Spaulding", "Giancarlo Volpe", "Giancarlo Volpe", "Lauren MacMullan", "Lauren MacMullan", "Anthony Lioi", "Dave Filoni", "Ethan Spaulding", "Ethan Spaulding", "Giancarlo Volpe", "Giancarlo Volpe", "Giancarlo Volpe", "Joaquim Dos Santos", "Lauren MacMullan", "Lauren MacMullan", "Ethan Spaulding", "Giancarlo Volpe", "Joaquim Dos Santos"], "z": [8.2, 8.55, 8.766666666666667, 7.933333333333334, 8.95, 8.700000000000001, 8.466666666666667, 8.0, 8.299999999999999, 8.733333333333334, 9.166666666666666, 8.399999999999999, 8.466666666666667, 8.833333333333334, 9.2, 8.8, 8.799999999999999, 8.366666666666667, 8.899999999999999, 8.966666666666667]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('d0efe397-f7f7-40bc-aba7-25871c055b09');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


## I know what you're thinking: Where's the rest of it??

It's coming. This is my little pet project and I can only update it when I have the time. Never fear though. I will get it done