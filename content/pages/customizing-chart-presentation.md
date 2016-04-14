Title: Customizing Chart Presentation
URL:
sortorder: 60

You can use any charting library with the output from Popily's API. However you'll probably prefer to use [our JavaScript library](https://github.com/popily/popily-js). It does useful things like selecting the most appropriate visualization based on the types of data being compared in any given Insight, automatically generates descriptions, and all of our visualizations are interactive and responsive out of the box!

Plus you have an enormous amount of control over how your visualization appears. For starters, here are the types of visualizations Popily can produce (we're adding more all the time!).

* Bar charts/column charts
* Stacked bar charts/stacked column charts
* Grouped bar charts/grouped column charts
* Line charts/time series
* Stacked area/grouped time series
* Scatterplots/grouped scatterplots
* Gauge charts
* Pie charts (we're not snobs)
* Choropleth maps
* Interactive maps (dots on a map)

Typically you'll add visualizations to the DOM using the handy `getAndRender` method, which retrieves an Insight from the API, decides how to visualize it, and draws it on the page. For example:

```javascript
popily.chart.getAndRender('#my-element', {insight:'some-insight-of-yours'});
```

You can pass additional options to this method that give you fine-grained control over how your data is presented.

Option | Description | Example Values
--------- | ----------- | -------------
chartType | The type of visualization | ``bar``, ``barStacked``, ``barGrouped``, ``bubble``, ``line``, ``multiLine``, ``stackedArea``, ``gauge``, ``pie``, ``scatterplot``, ``scatterplotCategory``, ``choropleth``, ``interactiveMap``
colors | A list of hex values for bars, pie slices, lines, etc. | ``['#eee', '#555']``
xLabel | String label for the x axis | My X Axis
yLabel | String label for the y axis | My Y Axis
groupByColumn | Column on the group by (aka z) axis. (The stacks in a stacked bar chart) | Your Column Name
xColumn | Column that should go on the x axis | Your Column Name
height | Chart height | ``500``
width | Chart width | ``500``
rotated | Rotate the chart so x axis is on the left instead the bottom | ``true``
title | Chart title | String of custom title. For auto-generated title, use ``true``
xOrder | Order of the x axis (like bars in a bar chart) | ``a-z``, ``z-a``, ``asc``, ``desc``
barSize | Relative size of bars in a bar chart | ``.1``
lineSize | Size of lines in a line chart |
pointSize | Size of points in a scatterplot |
titleFontFamily | Font of the chart title | ``Arial``
titleFontSize | Size of the chart title | ``26px``
titleFontColor | Color of the chart tytle | ``red``
chartFontFamily | Font of labels and text in the chart |
chartFontSize | Font size of labels and text in the chart |
chartFontColor | Font color of labels and text in the chart |
xGrid | Vertical grid lines in charts |
yGrid | Horizontal grid lines in charts |
time_interval | Display into time groups |

```javascript
var insightOptions = {
   chartType: 'bar',
   colors: ['#444', '#999'],
   xLabel: 'Thing on the Bottom',
   yLabel: 'Thing on the Left',
   groupByColumn: 'Store Location',
   height: 500,
   width: 500,
   rotated: false,
   title: 'This Chart Rules',
   xOrder: 'a-z',
   barSize: .5
};

popily.chart.getAndRender('#my-element', insightOptions);
```
