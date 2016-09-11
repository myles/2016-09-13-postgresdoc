footer: Myles Braithwaite | [mylesb.ca](http://mylesb.ca/) | [me@mylesb.ca](mailto:me@mylesb.ca) | [@mylesb](https://twitter.com/mylesb)
slidenumbers: true

# [fit] PostgresDoc

# [fit] Using Postgres as a Document Database

---

![](media/dr-strangelove-ripper-and-mandrake.png)

# [fit] How I Learned to Stop Worrying and Love the SQL Again

---

![right fit](media/table-diagram-basic.png)

# Relational Database

^ In a relational database, like MySQL or Postgres, you must first define a schema before you are able to add records to your database.

^ The schema is a blueprint for your tables in a database and the relationship between tables of data.

^ Within a table, you need to define constraints in terms of rows and named columns as well as the type of data that is stored in a column.

---

![right fit](media/table-diagram-complicated.png)

# Relational Database

^ Here is a little more complicated diagram of what I'm talking about.

---

# Document Oriented Database

```json
{
    "_id": "b04b9746-77bc-11e6-a75a-34363bd0c9bc",
    "name": "Lot 9 Pilsner",
    "brewer": "Creemore Springs",
    "rating": 4.2
}
```

^ In a document-oriented database, like CouchDB or MongoDB, contains documents, which are records that describe the data in the document, as while as the acutal data.

---

# Document Oriented Database

```json
{
    "_id": "b04b9746-77bc-11e6-a75a-34363bd0c9bc",
    "date": "2016-09-11T16:35:33.773325-0400"
    "customer": {
        "name": "Myles Braithwaite",
        "telephone": "+1 (416) 555-1234",
        "address": "123 Street Ave."
    },
    "order": [{
        "name": "beer",
        "price_per_unit": 12,
        "quantity": 2,
        "total": 24
    }],
    "total": 24,
    "payment": {
        "type": "VISA",
        "number": "12345",
        "expiry": "2001-04"
    }
}
```

---

# Document Oriented Database

![right fit](media/document-diagram.png)

^ Here is a more complicated view of the data.

---

# [fit] Why not both?

![](media/why-not-both.gif)

^ Why can't we have the best of both worlds?

---

# Why not both?

- Avoid complicated JOINs.
- Ability to store complicated JSON API responses in the database.
- Avoid transforming data before returning it via a JSON API.

^ JOINs suck, they require a huge amount of processing power.

^ When working with external JSON APIs, think credit card processsors, social networks, and the goverment, it really helpful to be able to store the entire JSON response in your database.

^ In the world of React and Anglar frontend frameworks have realtime access to your data though a JSON database is the different between millseconds and seconds.

---

# [fit] Postgres JSON/JSONB Type

^ Enter Postgres JSON Type.

^ Postgres has had JSON support for a while, with a TEXT type.

^ But Postgres 9.2 introduced native JSON support.

^ And in 9.3, Postgres improved the JSON type by adding additional constructor and extractor methods.

^ In 9.4 they added the ablility to store JSON as a "Binary JSON", also known as JSONB Type, which strips out insignificate whitespace, added a bit of overhead when inserting data, but provided a huge benefit when quering data though indexes.

---

# [fit] `CREATE TABLE`

---

```sql
CREATE TABLE notes (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    notebook_id UUID REFERENCES notebooks (id),
    note JSONB
);
```

^ Let's create a simple database table for our note taking app.

^ It has a id as a UUID Type, a foreign key to the notebooks table.

^ And it stores it's data in a JSONB Type.

---

# [fit] `INSERT`

---

```sql
INSERT INTO notes
VALUES (
    '675afef8-7863-11e6-8914-34363bd0c9bc',
    '8921ad42-300e-447a-8417-ec92bb18e2df',
    '{
        "name": "PostgresDoc",
        "tags": ["Postgres",
                 "Document Oriented Database"],
        "body": "Hello, World"
    }'
)
```

^ Here we are inserting some data into the database.

---

# [fit] `SELECT`

---

```sql
SELECT note->>'name' AS name FROM notes;
```

```
name
----------------------------------------
PostgresDoc
World Domination Plans
Superman < Batman > Supergirl = Batgirl
(3 rows)
```

^ Here we are selecting some information from the JSONB type.

---

```sql
SELECT
    jsonb_array_elements_text(note->'tags') AS tag
FROM notes
WHERE id = '675afef8-7863-11e6-8914-34363bd0c9bc';
```

```
tag
---------------------------
Postgres
Document Oriented Database
(2 rows)
```

^ Here we are viewing an array of information from the JSONB type.

---

```sql
SELECT id, note
FROM notes
WHERE note->'tags' ? 'Postgres';
```

```
                id                   |        note           |
-------------------------------------+------------------------
675afef8-7863-11e6-8914-34363bd0c9bc | {"name": "PostgresDoc"}
(1 row)
```

^ Here we are filtering for all notes that have the tag Postgres.

---

```sql
SELECT count(*) FROM notes WHERE note ? 'checklist';
```

```
count
-------
   1
(1 row)
```

^ Here we are checking the notes for the existance of the property named checklist.

---

```sql
CREATE INDEX
    idx_checklist
ON
    notes
((note->>'tags'))
```

---

# [fit] ORMs

---

# SQLAlchemy

```python
note_table = Table('notes', metadata,
    Column('id', Integer, primary_key=True),
    Column('note', JSONB)
)

with engine.connect() as conn:
    conn.execute(
        note_table.insert(),
        note = {"name": "Postgres", "tags": ["Postgres"]}
    )
```

---

# Rails

```ruby
create_table :notes do |t|
    t.json 'note'
end

class Note < ApplicationRecord
end

Note.create(note: { name: "Postgres", tags: ["Postgres"]})
```

---

# [fit] <https://github.com/myles/2016-09-13-postgresdoc>