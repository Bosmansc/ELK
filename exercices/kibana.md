# Kibana

We will use the shakespeare, account and logs data stored in elasticsearch.

### Index pattern

In order to use an elasticsearch index, we need to create some index pattern in Kibana which will define the structure of our data to visualize.
It could appear a bit repetitive as we normally have already done that in elasticsearch.

- On the Kibana index management, create an index pattern corresponding to logstash-\*, ba\*, shakes\*.


### Discover data

- In the discover session, play with your data. Use the ba\* index pattern and search for the account in the first 100 account number with a balance inferior to 50 000$.

### Create Visualization

#### Pie chart

We will try to represent the number of accounts splitted by their balance in a Pie Chart

Let's define 7 different ranges of balance:

0-999, 1000-2999,3000-5999,6000-9999,10000-19999,20000-39999,40000-100000

```

1- Open Visualize
2- Click Create new visualization
3- Click Pie.
4- In Choose a source, select the ba* index pattern.
5- In the Buckets pane, click Add > Split slices.

6- In the Aggregation dropdown, select Range.
7- In the Field dropdown, select balance.
8- Add your range.
```

- Now, add a second split slices in order to divide each previous range accordingly to the age of the owner. (Hint: use the Terms age of your data)


#### Bar chart

We will try to represent the number of times per

Let's define 7 different ranges of balance:

0-999, 1000-2999,3000-5999,6000-9999,10000-19999,20000-39999,40000-100000

```

1- Open Visualize
2- Click Create new visualization
3- Click Pie.
4- In Choose a source, select the ba* index pattern.
5- In the Buckets pane, click Add > Split slices.

6- In the Aggregation dropdown, select Range.
7- In the Field dropdown, select balance.
8- Add your range.
```

- Now add a second split slices in order to divide each previous range accordingly to the age of the owner. (Hint: use the Terms age of your data)
