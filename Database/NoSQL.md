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