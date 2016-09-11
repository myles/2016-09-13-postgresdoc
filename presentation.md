footer: Myles Braithwaite | [mylesb.ca](http://mylesb.ca/) | [me@mylesb.ca](mailto:me@mylesb.ca) | [@mylesb](https://twitter.com/mylesb)
slidenumbers: true

# [fit] PostgresDoc

# [fit] Using Postgres as a Document Database

---

![](media/dr-strangelove-ripper-and-mandrake.png)

# [fit] How I Learned to Stop Worrying and Love the SQL Again

---

![right fit](media/table-diagram.png)

# Relational Database

^ In a relational database, like MySQL or Postgres, you must first define a schema before you are able to add records to your database.

^ The schema is a blueprint for your tables in a database and the relationship between tables of data.

^ Within a table, you need to define constraints in terms of rows and named columns as well as the type of data that is stored in a column.

---

# Document Oriented Database

```json
{
    "_id": "b04b9746-77bc-11e6-a75a-34363bd0c9bc",
    "_rev": "77bd11e6b4a6",
    "name": "Lot 9 Pilsner",
    "brewer": "Creemore Springs",
    "rating": 4.2
}
```

^ In a document-oriented database, like CouchDB or MongoDB, contains documents, which are records that describe the data in the document, as while as the acutal data.

---

# Document Oriented Database

![right fit](media/document-diagram.png)

^ This is a better example of the beifits of a document oriented database.

---

![inline](media/why-not-both.gif)