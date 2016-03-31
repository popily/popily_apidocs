Title: Getting Charts
URL: 
sortorder: 40

Once you've added data to Popily, visualizing it is easy. Each visualization represents the relationship between two or more columns. We call these relationships **Insights**. So you ask the for the relationship you want, and you get Insights back. 

### Table of Contents
<a name="getting-one-chart"></a>
**Part 1**: [Getting one chart](#getting-one-chart)

**Part 2**: [Getting a list of charts](#getting-list-charts)

## Getting just one chart

Most often you'll want a single Insight, which looks like this:

```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    source: 'my-employee-salary-source-slug', 
    columns: ['employees.favorite_color', 'salaries.amount']
};

popily.chart.getAndRender('#some-element', insightOptions);
```

In the above example, we're looking for the relationship between `employees.favorite_color` and `salaries.amount`, two columns in the Data Source identified by the slug `my-employee-salary-source-slug`. 

By default Popily returns the chart that it thinks will be most interesting, but this won't always be exactly what you want. Let's say, for instance, that when you asked for an Insight about `employees.favorite_color` and `salaries.amount`, it returned the _total_ salary for each favorite color, but you want the _average_. No problem! You can specifiy the calculation you want like this:

```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    source: 'my-employee-salary-source-slug', 
    columns: ['employees.favorite_color', 'salaries.amount'],
    calculation: 'average'
};

popily.chart.getAndRender('#some-element', insightOptions);
``` 

The types of calculations Popily performs are: 

Calculation | Description
--------- | -----------
average | Average of a number
sum | Total of a number
count | Number of rows, used for categories and dates
ratio | Percentage of rows, used for categories and dates
comparison | Compares two numbers, produces scatterplots
geo | Geospatial information, produces maps

If you already know the slug or id of an individual Insight, for example the Insight that represents the average employee salary grouped by favorite color, you can request that Insight directly. For example:

<a name="getting-list-charts"></a>
```javascript
// Get the relationship between favorite color and salary
var insightOptions = {
    insight: 'employees-favorite-color-salaries-amount' 
};

popily.chart.getAndRender('#some-element', insightOptions);
``` 

## Getting a list of charts

Throughout the documentation we make liberal use of the `getAndRender` method of the JavaScript browser client. This method always returns just one Insight, because that's what people normally want. However you can also return a filtered list of Insights. This can be useful if, like in Popily's user interface, you're helping a user to explore his or her data. 

To do this we'll directly access the `getInsights` method of `popily.api` in the JavaScript browser client. This functionality is also exposed in the Node.js and Python clients.

The simplest way to return a list of Insights is by returning all of the Insights for a given Data Source. 

```javascript
popily.api.getInsights('some-source-slug', {}, function(err, insights) {
    // insights
});
```

This could return a lot of Insights, so you'll need to paginate through the results. Here's an example response with the `count`, `next` and `previous` properties you'd use to work your way through the list of Insights.

```json
{
    "count": 30,
    "previous": null,
    "results": [{
        "refresh_rate": 0,
        "title": "Number of number_3 Grouped by Value",
        "calculation": "count",
        "slug": "number-3-21",
        "source": 336,
        "insight_metadata": null,
        "id": 246367,
        "columns": [{
            "values": ["5.207 to 6.0749", "6.943 to 7.811", "0.868 to 1.736", "0.000227 to 0.868", "6.0749 to 6.943", "4.339 to 5.207", "1.736 to 2.604", "3.471 to 4.339", "2.604 to 3.471"],
            "data_type": "category",
            "column_header": "number_3"
        }, {
            "values": ["102", "51", "204", "254541", "0", "0", "51", "0", "0"],
            "data_type": "numeric",
            "column_header": "count_0"
        }]
    }],
    "next": "https://popily.com/api/insights/?source=336&page=2"
}
```

You can pass each item in the `results` array to the `render` method of Popily's browser client, which accepts all the same customization options as `getAndRender`.

```javascript
for(var i; i < results.length; i++) {
    popily.chart.render('#chart-' + i.toString(), results[i]);
}
```

If you're only looking for Insights about specific columns in a given Data Source, that's cool too. 

```javascript
var columns = ['salaries.amount', 'employees.favorite_color'];
popily.api.getInsights('my-employee-salary-source-slug', 
                        {columns: columns}, 
                        function(err, apiResponse) {
                            console.log(apiResponse);
                        });
```