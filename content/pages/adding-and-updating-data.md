Title: Adding Data
URL: 
sortorder: 30

In Popily, the data you put together for analysis is called a **Data Source**. This could be the result of a database query, a request to an API, or a CSV file. Sending data to Popily for analysis is a two-step process:

## Sending the data

Popily does a lot of work behind the scenes to figure out what type of information is in your data, and the relationhips between variables in your data could be visualized. So you just need to tell Popily where the data can be accessed, and we'll do the rest. We can work with data from your database, or from flat files like CSVs -- whatever makes more sense in your application. Here's an example where Popily connects directly to a database.

```javascript
var sourceOptions = {
    connection_string: 'mysql://username:password@host:port/database',
    query: 'SELECT * FROM my_table'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```

When adding a Data Source from a URL (either an API endpoint or the location of a CSV file), you use the `url` property. 

```javascript
var sourceOptions = {
    columns:columns, 
    url: 'http://mysite.com/data/some-csv.csv'
};

popily.api.addSource(sourceOptions, function(err, source) {
    console.log(source); 
});
```

Lastly, when using the Python, Node.js or any other server-side clients, you can also upload files. In Python:

```python
import popily

popily_api = popily.Popily('YOUR API TOKEN')

your_file = open('/path/to/your/file.csv')
source = popily_api.add_source(file_obj=your_file)
```
In Node.js:

```javascript
var popily = require('popily')('YOUR API TOKEN');

filePath = '/path/to/your/file.csv'
var sourceData = {
  data: filePath
};

popily.addSource(sourceData, function(err, source) {
    // do something with the source
});
```

**Note**: Data Sources are processed asynchronously. So you'll get an immediate response from the API, but it may take a few seconds to a few minutes (depending on the size and shape of your data) for Popily to start producing Insights. This is only true when creating a new Data Source. Updates to a Data Source are performed in seconds.

We'll get into more detail about how to retrieve data and render charts in the [Getting Charts](/getting-charts/), but just FYI, the easiest workaround for this is to poll for the Insight you want, like this:

```javascript
// Get the relationship between 'Sale Amount' and 'Sale Date'
var insightOptions = {
    source: source.slug, 
    columns: ['Sale Amount', 'Sale Date'],
    poll: true
};
popily.chart.getAndRender('#my-chart', insightOptions);
```

You can also check the `status` of a Data Source at any time using its slug or id. 

```javascript
popily.api.getSource('your-source-slug', function(err, source) {
    console.log(source.status); 
});
```

These are the possible statuses of a Data Source. 

Status | Description
--------- | -------
inspecting | Popily is looking for relationships in the data.
calculating | Popily is creating Insights, but still working.
supplementing | Reprocessing the Data Source after a change.
augmenting | Popily is adding additional columns for things like sentiment analysis
error | Something went wrong.
finished | All Insights have been created.

If you ever need to access a list of Data Sources -- either for your account, or associated with a User you have created -- you can retrieve those from the `/sources/` API endpoint, which is exposed in each of our client libraries. 

For example, in our browser client:

```javascript
popily.api.setToken('YOUR API TOKEN || USER API TOKEN');
popily.api.getSources(function(err, sources) {
    console.log(sources); 
});
```