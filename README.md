# lab5-db-query

## 1. SQL Query Optimization

 1. Orders Query

```
SELECT Orders.OrderID, SUM(OrderDetails.Quantity * OrderDetails.UnitPrice) AS TotalPrice
FROM Orders
JOIN OrderDetails ON Orders.OrderID = OrderDetails.OrderID
WHERE OrderDetails.Quantity > 10
GROUP BY Orders.OrderID;
```

Since we are not using the table Orders to obtain any data, we can obtain the data directly from OrderDetails without joining
```
SELECT OrderID, SUM(Quantity * UnitPrice) AS TotalPrice
FROM OrderDetails
WHERE Quantity > 10
GROUP BY OrderID;
```

Also we can create an Index into OrderId in both tables since is one of the principal attributes between them.

```
CREATE INDEX idx_order_id ON Orders, OrderDetails (OrderID);
```

2. Customer Query

```
SELECT CustomerName FROM Customers WHERE City = 'London' ORDER BY CustomerName;
```

The only way to improve the effiency of this query is using an index,
```
CREATE INDEX idx_customer_city ON Customers (City);
```


## 2. NoSQL Query Implementation

1. User Posts Query
```
db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 });
```

To improve the effiency of this query we can do 2 things, first is to create an index on active status, second thing is to limit the result that we can obtain from the query

Index
```
db.posts.createIndex({ status: "active" })
```
Query with limit
```
db.posts
  .find({ status: "active" }, { title: 1, likes: 1 })
  .sort({ likes: -1 })
  .limit(10);
```

   
2. User Data Aggregation
```
db.users.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$location", totalUsers: { $sum: 1 } } },
]);
```

In this case is using correctly the aggregation, to improve efficiency we can use indexing on status active
```
db.users.createIndex({ status: "active" })
```
