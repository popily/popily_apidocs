Title: Data Analysis 101
URL: 
sortorder: 20

Popily automates analytics so you can get your customers what they want as quickly as possible. Even though it's automatic, we find it's sitll helpful to understand a little about best practices in data analysis in order to get the most out of Popily.

## Data Analysis: How it Works

### Table of Contents
<a name="getting-data"></a>
**Part 1**: [Getting the Data](#getting-data)

**Part 2**: [Analyzing the Data](#analyzing-data)

**Part 3**: [Presenting the Data](#presenting-data)

----

### <a name="getting-data"></a>1. Getting the data

This probably sounds obvious, but it's so important that it's worth pointing out. Any analysis is only as good as the information it's analyzing. For example, let's say you have a database with two tables: `employees` and `salaries`. If you want to answer a question about employee salaries, you'll need information from both these tables. That example is pretty trivial, but now imagine a more complex scenario when you wanted to understand user behavior relative to a social media campaign. You might have user information in your application database, details about user behavior in Google Analytics, and records of social media activity in some third-party service that tracks posts to Twitter, Facebook, etc. 

Continuing with the social media example, imagine you created a single CSV file that pulled user names from your database, and joined those with click events from Google Analytics and social media mentions from your third-party service. You might end up with something like this:

| username | clickedButtonID | tweetText                                  | pageURL                   | eventDate |
|----------|-----------------|--------------------------------------------|---------------------------|-----------|
| johndoe  | #tweetThis      | I love @yourCompany, they make great stuff | yourcompany.com/promotion | 1-1-2016  |
| janedoe  | #tweetThis      | .@yourCompany is great                     | yourcompany.com/promotion | 1-1-2016  |


In Popily, the data you put together for analysis is called a **Data Source**.

Fortunately, generally speaking creating the Data Source you need will be more straightforward. In fact, Popily is pretty good at building data models on the fly, so usually all you need to do is write a basic query that gets everything you could possibly want, like:

`SELECT * FROM employees JOIN salaries ON salaries.employee_id = employees.id;`

Then send that data to Popily.

```javascript
var sourceOptions = {
    connection_string: 'mysql://username:password@host:port/database',
    query: 'SELECT * FROM employees JOIN salaries ON salaries.employee_id = employees.id;'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```
<a name="analyzing-data"></a>
Of course, sometimes even with the best intentions databases get a little messy over time. If you're not sure how to get the data you need for analysis, [talk to us on Slack](https://gentle-shore-82359.herokuapp.com/), or email us at [awesome@popily.com](mailto:awesome@popily.com).

----

### 2. Analyzing the data

The goal of an analysis is to figure out something about one column, like "the average employee salary is $50,000," or, more commonly, about a relationship between the columns, like "the average salary of employees based on their favorite color." Usually this involves doing a little relational algebra and performing basic calculations like finding a total or average.

In Popily, these relationships are somewhat pretentiously called **Insights**.

Fortunately Popily makes Insights automatically. We break up your data into a bunch of possible relationships, do the math, and store the calculations so they're ready whenever you need them. Then you just ask for the relationship you want. For example, to get the relationship between employees' favorite color and their salaries:

```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    source: 'my-employee-salary-source-slug', 
    columns: ['employees.favorite_color', 'salaries.amount']
};

popily.chart.getAndRender('#some-element', insightOptions);
```

Popily does a little data science magic in the background to try and figure out which way of thinking about this relationship is the most "interesting." (Seriously, the score we give to each Insight is called "interestingness.") Sometimes, however, Popily's opinion will differ from yours. Let's say, for instance, that when you asked for an Insight about `employees.favorite_color` and `salaries.amount`, it returned the total salary for each favorite color, but you want the average. No problem! You can specifiy the calculation you want like this:

```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    source: 'my-employee-salary-source-slug', 
    columns: ['employees.favorite_color', 'salaries.amount'],
    calculation: 'average'
};

popily.chart.getAndRender('#some-element', insightOptions);
``` 

Now that you have the calculated relationship you want, you can transform it to show exactly what you want. Maybe you only want to see salary information for employees who like primary colors:

```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    source: 'my-employee-salary-source-slug', 
    columns: ['employees.favorite_color', 'salaries.amount'],
    calculation: 'average',
    transformations: [
        {
            column: 'employees.favorite_color',
            values: ['Red', 'Green', 'Blue'],
            op: 'eq'
        }
    ]
};

popily.chart.getAndRender('#some-element', insightOptions);
``` 

You can ask for complex relationships and perform many transformations in sequence. These features combined are powerful tools for quickly analyzing your data.

<a name="presenting-data"></a>
If you're interested, this approach of splitting up a dataset, performing some calculations, and then transforming the output, is loosely based on a strategy for analysis called "[split-apply-combine](http://seananderson.ca/2013/12/01/plyr.html)" (which is similar [MapReduce](https://en.wikipedia.org/wiki/MapReduce)). Happy reading!

----

### 3. Presenting the data

What's the best way to represent the relationship of employee salary grouped by favorite color? What about employee salary for each favorite color on average over time? Why does everyone [hate pie charts](http://www.gilliganondata.com/index.php/2009/12/02/how-succinctly-can-i-explain-why-pie-charts-are-evil/) so much? 

Given the complexity of data science and analytics, it might be surprising that [one of the biggest problems is communication](https://hbr.org/2013/06/data-is-worthless-if-you-dont). This is particularly important in dashboards and other analytics that you embed in your application because people need to understand them at a glance. 

At Popily we believe in the [Grammar of Graphics](http://www.amazon.com/The-Grammar-Graphics-Statistics-Computing/dp/0387245448), and determine which visualization is most appropriate based on the type of information in any given relationship. Need to show the average of a number over time? We'll pick a line chart. Need to show the total sales volume by state? We'll pick a [choropleth map](https://en.wikipedia.org/wiki/Choropleth_map). This is why you don't see any mention of chart types in our examples.  

When you do something like this:

```javascript
popily.chart.getAndRender('#some-element', insightOptions);
```

Popily looks at the Insight returned from our API, inspects the information type of each column in that relationship, looks for any transformations you might have applied, understands the density of the information that needs to be visualized, and determines which visualization will most clearly communicate the story of that relationship. Phew!

Of course sometimes your judgement will be better than our algorithms, so you can override the chart type (within reason).

```javascript

var insightOptions = {
    ...
    chartType: 'bar'
}

popily.chart.getAndRender('#some-element', insightOptions);
```

We say "within reason" because some chart types just wouldn't make sense. For example if you want to show your users the relationship between `employees.favorite_color` (a category) and `salaries.amount` (a number), you wouldn't use a scatterplot, because that visualization type is for comparing one list of numbers with another list of numbers. 

Also, every once in awhile someone asks for a chart that seems like a good idea, but would be super confusing in practice. Usually these are visualizations that compare more than three dimensions -- like `salaries.amount` grouped by `employees.favorite_color` grouped by `employees.hire_date` grouped by `employees.name`. Try to say that as a sentence: "I want to see the average salary for each employee name broken down by favorite color and grouped monthly." Whoa. This is the kind of thing that'd be better as two charts. Trust us.

Our list of visualization types is always growing, and if there's one you *really* want and just can't wait, our [JavaScript charting library](https://github.com/popily/popily-js) is open source -- contributors welcome! Currently we produce:

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

To all the statisticians out there, we love box plots, bar charts with error bars, violin plots, tree maps, and every other artisanal visualization that may or may not have appeared in Chris' PhD thesis. We've yet to find a use for any other these outside of research projects, so we don't offer them by default. That said, if you ask really nicely, we're game to try it.  