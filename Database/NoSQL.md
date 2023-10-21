---
tags: NoSQL
---

# Benefits
- They don’t have **a predefined schema** → supports addition, removal of field dynamically.
- Easily store unstructured data.
- Support Horizontal scaling.

## Collection
In MongoDB, collection is the **same as the tables** in relational databases.

## Document
In MongoDB, a document is the **same** **as a row** in relational databases. A single entry in the collection is called a document.

## Embedded document
In RDBMS, we need to create a separate table to store relational data. Contrary to that, a NoSQL database allows us to store the relational data in the same document. They are called embedded documents. They are also sometimes referred to as child documents.

```jsx
{
  _id: "60f56bd0f3a5ecfb8148fea3",
  name: "Learn MongoDB Topic 1",
  date: "2021-07-21",
  priority: 1,
  status: "pending",
  user: "onkar",
  team: {
    id: "60f56bd0f3a55ecfb8148fea34",
    location: {
      address:"San Francisco",
      lat:37.773972,
      lng:-122.431297
    }
  }
}
Note: The team field contains the embedded document.
```

**A MongoDB database is created** when the first document is **inserted** into a collection.


# The benefit of using Mongodb?
**Why mongodb could be horizontal scaling?**
- <ins>NoSQL are ideal for distributed </ins> network application over SQL database. Because they are horizontal scaling, unlike vertically scaling SQL. That why NoSQL used in BigData,...
- MongoDB is <ins>document oriented</ins>, data often not span accross table, it will be <ins>denormalized</ins> into single document --> make query fetch document very quickly & don't necessary to joining between multiple tables
- MongoDB use <ins>sharding strategy</ins>, which is horizontal scaling, split a large collection into mulitple shards --> we would parallel query and then joining total result and return --> This is keypoint over SQL database, when data is incremented <ins>too large would make SQL query slowly</ins>