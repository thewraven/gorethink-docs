---
#date: 2016-03-09T00:11:02+01:00
title: Writing data
weight: 15
---

## Insert

Insert documents into a table. Accepts a single document or an array of documents.

> r.Table(table).Insert(element interface{},options ...r.InsertOptions).RunWrite(session) -> (r.WriteResponse,error)

In this particular case, the driver accepts the **element** that will be inserted either be
a **map[string]interface{}**, a slice of elements **[]interface** , or a single **struct**   


### Insert: Examples

Insert a document into the table _posts_ using a map:

```
post := map[string]interface{}{
  "id":      2,
  "title":   "Lorem ipsum",
  "content": "Dolor sit amet",
}
resp, err := r.DB("examples").Table("posts").Insert(post).RunWrite(session)
if err != nil {
  fmt.Print(err)
  return
}

fmt.Printf("%d row inserted", resp.Inserted)
```

Insert a document into the table _posts_ using a **struct**:

```
type Post struct {
  ID      int    `gorethink:"id"`
  Title   string `gorethink:"title"`
  Content string `gorethink:"content"`
}
post := Post{
  ID:      1,
  Title:   "Lorem ipsum",
  Content: "Dolor sit amet",
}
resp, err := r.DB("examples").Table("posts").Insert(post).RunWrite(session)
if err != nil {
  fmt.Print(err)
  return
}

fmt.Printf("%d row inserted", resp.Inserted)
```

Insert a slice of posts using an **[]interface{}**:

```
post := Post{
  ID:      1,
  Title:   "Lorem ipsum",
  Content: "Dolor sit amet",
}
post1 := Post{
  Title:   "Lorem ipsum",
  Content: "Dolor sit amet",
}
resp, err := r.DB("examples").Table("posts").Insert([]interface{}{
  post, post1,
}).RunWrite(session)
if err != nil {
  fmt.Print(err)
  return
}
```

## Delete

Delete one or more documents from a table. The elements to be deleted can be either a full table, a selection or a single selection.

> r.Table(table).Delete(options ...r.DeleteOpts).RunWrite(session) -> (r.WriteResponse,error)

### Delete: Examples

Delete a single document from a table:

```
resp, err := r.DB("examples").Table("posts").
Get(2).Delete().RunWrite(session)
if err != nil {
    fmt.Print(err)
    return
}

fmt.Printf("%d row deleted", resp.Deleted)
  ```

Delete a selection of a table:

```
filter := map[string]interface{}{
  "status": "published",
}
resp, err := DB("examples").Table("posts").
  Filter(filter).Delete().RunWrite(session)
if err != nil {
  fmt.Print(err)
  return
}

fmt.Printf("%d rows deleted", resp.Deleted)
```

Delete the whole table:

```
resp, err := r.DB("examples").Table("posts").
Delete().RunWrite(session)
if err != nil {
    fmt.Print(err)
    return
}

fmt.Printf("%d rows deleted", resp.Deleted)
```

Deleting a selection with soft duration:

```
filter := map[string]interface{}{
  "status": "published",
}
deleteOpts := r.DeleteOpts{
  Durability: "soft",
}
resp, err := DB("examples").Table("posts").
  Filter(filter).Delete(deleteOpts).RunWrite(session)
if err != nil {
  fmt.Print(err)
  return
}
```
