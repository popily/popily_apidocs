Title: Transforming Chart Data
URL: 
sortorder: 50

Every Popily visualization represents the relationship between two or more columns. We call these relationships **Insights**. By default Popily paints the entire picture of a relationship, but you can be selective about which aspects of that relationship you share with your customers. In short: you can limit what shows up in the chart.

For example let's say you have a dataset of purchases, and each purchase has a Purchase Price and a Location. To get the relationship between these columns you'd make this request:

```javascript
var insightOptions = {
    columns: ['Purchase Price', 'Location'],
    calculation: 'average',
    single: true  
};

popily.api.getInsights('my-purchases-dataset', insightOptions, 
                        function(err, insight) {
                            console.log(insight);
                        });
```

When you check out the console log, you'll see something like this:

```json
{
    "refresh_rate": 0,
    "title": "Average Purchase Price by Location",
    "calculation": "count",
    "slug": "purchase-price-location",
    "source": 336,
    "insight_metadata": null,
    "id": 246367,
    "columns": [
        {
            "values": [
                "27.50", "35.27", "99.12", "100.00", "12.34"
            ],
            "data_type": "number",
            "column_header": "Purchase Price"
        }, 
        {
            "values": [
                "Lowes", "Home Depot", "Sears", "Ace Hardware", "True Value"
            ],
            "data_type": "category",
            "column_header": "Location"
        }
    ]
}
```

This will produce a bar chart with Location along the bottom and bars representing the average purchase price of each location. You can limit what is displayed in the chart using `transformations`. For example let's only show bars where the value is over $20.

```javascript
var insightOptions = {
    columns: ['Purchase Price', 'Location'],
    calculation: 'average',
    transformations: [
        {
            column: 'Purchase Price',
            op: 'gt',
            value: 20
        }
    ]
};

popily.chart.getAndRender('#some-element', insightOptions);
```

A `transformation` has three properties: 

Property | Description
--------- | -----------
column | The column you'd like to act on
value(s) | `value` or a list of `values` are accepted
op | Stands for "operation" -- like equals, less than, greater than, etc

We're always expanding the available operations to make Popily more flexible. Currently we have:

Op | Description
--------- | -----------
lt | Less than
lte | Less than or equal to
gt | Greater than
gte | Greater than or equal to
eq | Equals
countUnique | Counts the unique values for a given column

Multiple transformations can be applied to the same Insight. For instance we could limit the chart to display only to average Purchase Prices more than $20 at Lowes or Home Depot.

```javascript
var insightOptions = {
    columns: ['Purchase Price', 'Location'],
    calculation: 'average',
    transformations: [
        {
            column: 'Purchase Price',
            op: 'gt',
            value: 20
        },
        {
            column: 'Location',
            op: 'eq',
            values: ['Lowes', 'Home Depot']
        }
    ]
};

popily.chart.getAndRender('#some-element', insightOptions);
```

Note: each transformation will be performed in the order it is received. This becomes particularly important when applying transformations like `countUnique`. For example if our purchases dataset also had Product Type and State columns, we could really go to town:

```javascript
var insightOptions = {
    columns: ['Product Type', 'Location', 'State'],
    calculation: 'count',
    transformations: [
        {
            column: 'Location',
            op: 'eq',
            values: ['Lowes', 'Home Depot']
        },
        {
            column: 'State',
            op: 'eq',
            values: ['Texas', 'California']
        },
        {
            column: 'Product Type',
            op: 'countUnique'
        }
    ]
};

popily.chart.getAndRender('#some-element', insightOptions);
```

That list of transformations will give us the count of each Product Type where the State is Texas or California and the Location is Lowes or Home Depot. 

