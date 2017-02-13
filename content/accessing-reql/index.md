---
#date: 2016-03-09T00:11:02+01:00
title: Accessing ReQL
weight: 5
---

## r

In order to reduce verbosity, you should use an **import alias** to import the Go driver as follows:

```go
r "gopkg.in/gorethink/gorethink.v3"
```
We will use the **r** alias on this whole guide, that
must be understood as the **gorethink** package.


## Connect

Create a new connection to the database server.

> r.Connect(ConnectOps) -> (*r.Session, error)

The main attributes of **ConnectOpts** :

- `Address` **string**  : Holds the address of the server initially used when creating the session. Only used if Addresses is empty.
- `Addresses` **[]string**  : holds the addresses of the servers initially used when creating the session.
- `Database` **string** : Database is the default database name used when executing queries, this
 value is only used if the query does not contain any DB term.
- `Username` **string** : Username holds the username used for authentication.
- `Password` **string** : Password holds the password used for authentication.

### Connect:  Examples

Basic connection, unauthenticated, with default database:

```
session, err := r.Connect(r.ConnectOpts{
  Host: "localhost:28015",
	Database: "users",
})
```

Authenticated connection to a cluster, using an AuthKey:

```
session, err := r.Connect(r.ConnectOpts{
	Hosts: []string{"localhost:28015", "localhost:28016"},
	Database: "test",
	AuthKey:  "14daak1cad13dj",
})
```

Setting up the maximum number of connections held in the connection pool.
```
session, err := r.Connect(r.ConnectOpts{
    Address:    "localhost:28015",
    MaxOpen:    10,
})
```

{{< note title="The Session struct" >}}
The connection established between the client and the RethinkDB server is hidden
behind the **r.Session** struct, that handles the active connections using a pool,
being goroutine-safe. Hence, connection-related operations like Close() and
Reconnect() are handled as methods of the session struct.
{{< /note >}}


## Close

Close closes the session active.

> err := session.Close(...r.CloseOpts) -> error

Attributes of CloseOpts:

- `NoReplyWait` **bool** : Defines if the client-side should wait for the response
of the cluster.

### Close:  Examples

Closing the active session:
```
err := session.Close()
```
Closing the active session without waiting for the server reply:
```
err := session.Close(r.CloseOpts{
  NoReplyWait: true,
})
```

## Reconnect

Reconnect closes and re-opens a session.

> session.Reconnect(...r.CloseOpts) -> error

### Reconnect: Examples

Reconnecting a session:

```
err := session.Reconnect(r.CloseOpts{
  NoReplyWait: true,
})
```

## Server

The server function of the *r.Session struct returns
the server name and identifier.

> session.Server() -> (r.ServerResponse, error)

### Example: Server

```
serverInfo, err := session.Server()
fmt.Println(serverInfo.Name, serverInfo.ID)
```
