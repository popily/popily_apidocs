Title: Get Started
URL: 
sortorder: 10
save_as: index.html

Popily makes it easy to turn your application data into analytics for your customers. We have client libraries in [Python](https://github.com/popily/popily-api) and [JavaScript](https://github.com/popily/popily-js) (more coming soon, let us know which langauges you'd like to see next!), support multi-tenancy out of the box, and let you customize interactive visualizations to your heart's content! 

Just add data, retrieve the relationships your customers care about, and repeat until they're happy.

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

## Step 2: Send your data

Then tell Popily where the data can be accessed.  We can work with data from your database, or from flat files like CSVs -- whatever makes more sense in your application. We call this a **Data Source**.

```javascript
var sourceOptions = {
    connection_string: 'mysql://username:password@host:port/database',
    query: 'SELECT * FROM my_table'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```

## Step 3: Get some charts

Now you can pick a column (or group of columns) to visualize. We call the relationships between columns **Insights**.  

```html
<div id="my-chart"></div>
```

```javascript
// Get the relationship between 'Sale Amount' and 'Sale Date'
var insightOptions = {
    source: source.slug, 
    columns: ['Sale Amount', 'Sale Date'],
    poll: true
};
popily.chart.getAndRender('#my-chart', insightOptions);
```

Note the `poll: true` property. Popily Data Sources are created asynchronously, so even though your request to `addSource` returned a response right away, it may take a few seconds to a few minutes (for datasets with lots of columns) for all of your Insights to be generated. The `poll` property tells the JavaScript client to keep requesting this Insight until it has been created, and update the DOM as soon as it's available. Note that this processing time is only required the first time Popily encounters a Data Source. Updates to the data happen much more quickly. 

## Step 4: Tweak

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