Title: Overview
URL: 
sortorder: 30

Popily automates analytics so you can get your customers what they want as quickly as possible. Even though it's automatic, we find it's sitll helpful to understand a little about best practices in data analysis in order to get the most out of Popily.

## Data Analysis: How it Works

### 1. **Getting the data**

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
columns = [
    {
        column_header: 'employees.employee_name',
        data_type: 'rowlabel'
    },
    {
        column_header: 'employees.hire_date',
        data_type: 'datetime'
    },
    {
        column_header: 'employees.favorite_color',
        data_type: 'category'
    },
    {
        column_header: 'salaries.amount',
        data_type: 'number'
    }
]

var sourceOptions = {
    columns: columns, 
    connection_string: 'mysql://username:password@host:port/database',
    query: 'SELECT * FROM employees JOIN salaries ON salaries.employee_id = employees.id;'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```

Of course, sometimes even with the best intentions databases get a little messy over time. If you're not sure how to get the data you need for analysis, [talk to us on Slack](https://gentle-shore-82359.herokuapp.com/), or email us at [awesome@popily.com](mailto:awesome@popily.com).

### 2. **Analyzing the data**

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

If you're interested, this approach of splitting up a dataset, performing some calculations, and then transforming the output, is loosely based on a strategy for analysis called "[split-apply-combine](http://seananderson.ca/2013/12/01/plyr.html)" (which is similar [MapReduce](https://en.wikipedia.org/wiki/MapReduce)). Happy reading!

### 3. **Presenting the data**  

Coming soon.