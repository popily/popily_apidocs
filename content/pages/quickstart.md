Title: Get Started
URL: 
sortorder: 10
save_as: index.html

Popily makes it easy to turn your application data into analytics for your customers. We have client libraries in Python and JavaScript (more coming soon!), support multi-tenancy out of the box, and let you customize interactive visualizations to your heart's content! 

Just authenticate, add data, retrieve the relationships your customers care about, tweak the output, and repeat until your customers are happy.

The easiest way to interact with the Popily API is by using one of our client libraries. Right now we have libraries for [Python](https://github.com/popily/popily-api) and JavaScript (both for [Node.js](https://github.com/popily/popily-node) and [in the browser](https://github.com/popily/popily-js)), and more are on the way. If you'd like to help out with a client in your favorite language, just let us know!

We'll use the JavaScript browser client in the examples below. Just make sure to add the library to your HTML document. 

```html
<body>
    <!-- lots of stuff -->
    <script src="popily.min.js"></script>
</body>
```

## Step 1: Authenticate

Use your API key to let Popily know who you are when you make requests.

```javascript
popily.api.setToken('YOUR API KEY');
```

## Step 2: Define your data

Just tell Popily what type of information is in your data, and we'll analyze it. These are more detailed than basic types like "integer" or "string." You can identify your column as a number, a category, a state (like a US state), a city, a zipcode, an email, a URL, and more! 


```javascript
columns = [
    {
        column_header: 'Sale Amount',
        data_type: 'number'
    },
    {
        column_header: 'Region',
        data_type: 'category'
    },
    {
        column_header: 'Sale Date',
        data_type: 'datetime'
    }
]
```
## Step 3: Send your data

Then tell Popily where the data can be accessed.  We can work with data from your database, or from flat files like CSVs -- whatever makes more sense in your application. We call this a **Data Source**.

```javascript
var sourceOptions = {
    columns:columns, 
    connection_string: 'mysql://username:password@host:port/database',
    query: 'SELECT * FROM my_table'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```

## Step 4: Get some charts

Now you can pick a column (or group of columns) to visualize. We call the relationships between columns **Insights**.  

```html
<div id="my-chart"></div>
```

```javascript
// Get the relationship between 'Sale Amount' and 'Sale Date'
var insightOptions = {
    source: source.slug, 
    columns: ['Sale Amount', 'Sale Date']
};
popily.chart.getAndRender('#my-chart', insightOptions);
```

## Step 5: Tweak

Popily will return what it thinks is the most interesting representation of the relationship between the columns you ask for ("Sale Amount" grouped by "Sale Date" in our example). Now you can modify that output to get only the information your customers want to see. We call these modifications **Transformations**. 

Let's tweak our code from Step 4 with a transformation.

```javascript
// Get the relationship between 'Sale Amount' and 'Sale Date' and attach the 
var insightOptions = {
    source: source.slug, 
    columns: ['Sale Amount', 'Sale Date'],

    // Get the average Sale Amount by Sale Date
    calculation: 'average',
    
    // Limit to the output to Sale Dates where average Sale Amount 
    // is more than $100
    transformations: [
        {
            column: 'Sale Amount',
            op: 'gt',
            value: 100
        }
    ]
};
popily.chart.getAndRender('#my-chart', insightOptions);
```

That's it! With a few lines of code we've gone from the raw data in your database, to a responive, interactive visualization showing a filtered representation of the average Sale Amount for each Sale Date. Popily did a few cool things for you along the way. 

1. Popily automatically grouped Sale Date by intervals that made sense, based on your data (like day, week, month and year), and calculated the average Sale Amount for each interval.

2. Popily decided which chart type should be used to represent the relationship between Sale Amount and Sale Date baesd on best practices.

3. Popily packaged the transformed data for presentation and rendered a responsive, interactive visualization inside the DOM element you specified. 

Cool!