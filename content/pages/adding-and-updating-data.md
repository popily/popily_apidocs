Title: Adding Data
URL: 
sortorder: 30

In Popily, the data you put together for analysis is called a **Data Source**. This could be the result of a database query, a request to an API, or a CSV file. Sending data to Popily for analysis is a two-step process:

## Step 1: Define the data

Soon Popily will be able to predict what types of information are in your data. For now, just tell Popily what type of data it should expect, and it will handle the rest of the analysis. 

When we say "type," this is more advanced than simply an integer or string. Popily understands concepts like categories, or US states.

Type | Description | Example
--------- | ------- | -----------
category | A column containing categorical values. | Apple, Orange, Pear
numeric | A column of numeric values. | 320, 2, 23.10
currency | A column of numeric values as currencies. | $320, $2, $23.10
datetime | A column of datetime or timestamps. | 12/12/2015, 2/3/15, January 6, 2015
country | A column of countries. | Spain, France, England
state | A column of US states. | Arizona, Maine, Oregon
coordinate | A column of latitude and longitude coordinate pairs. | [43.522, 23.521]
zipcode | A column of US Zip codes. | 42351
rowlabel | A column of non-categorical uniquely or semi-uniquely identifying each individual row | Steve Jobs, Dunder Muffin, Wendy Jones

Coming soon we'll also identify these types of information:

Type | Description | Example
--------- | ------- | -----------
time | Time of day without a date attached | 12:52pm, 13:23
text | A long text | An example won't fit here, but anything over 200 characters counts
email | An email address | sergey@google.com, tim@apple.com, jonathon@popily.com
URL | A URL | https://popily.com, https://google.com, twitter.com/PopilyTeam
address | A full street address | 100 Congress Ave, Austin, TX 78745
street | Just the street portion of an address | 100 Congress Ave
city | A major world city | Austin, Manchester, Beijing
phone | A phone number | 440-867-5309, +44 20 345 567
latitude | Latitude part of a coordinate | 34.75, -87.13
longitude | Longitude part of a coordinate | -179.34, 134.12

Popily's API expects a list of columns, and each item in the list should have a `column_header` property that exactly matches the column in your data and a `data_type` property that is one of the values described above.

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

## Step 2: Send the data

Now tell Popily where the data can be accessed.  We can work with data from your database, or from flat files like CSVs -- whatever makes more sense in your application. Here's an example where Popily connects directly to a database.

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

You can check the `status` of a Data Source at any time using its slug or id. 

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
reinspecting | Reprocessing the Data Source after a change.
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