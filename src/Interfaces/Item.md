title: Item Package Interfaces

Ponzu provides a set of interfaces from the `system/item` package which extend the functionality of the content in your system and how it interacts with other components inside and outside of Ponzu. 

## Interfaces

### [item.Pushable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Pushable)
Pushable, if [HTTP/2 Server Push](https://http2.github.io/http2-spec/#PushResources) is supported by the client, can tell a handler which resources it would like to have "pushed" preemptively to the client. This saves follow-on roundtrip requests for other items which are referenced by the Pushable item. 
The `Push` method, the only method in Pushable, must return a `[]string` containing the `json` field tags of the referenced items within the type.


### [item.Hideable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Hideable)
Hideable tells an API handler that data of this type shouldn’t be exposed outside the system. Hideable types cannot be used as references (relations in Content types).
The `Hide` method, the only method in Hideable, takes an `http.ResponseWriter, *http.Request` and returns an `error`. A special error in the `items` package, `ErrAllowHiddenItem` can be returned as the error from Hide to instruct handlers to show hidden content in specific cases.


### [item.Omittable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Omittable)
Omittable tells a content API handler to keep certain fields from being exposed through the JSON response. It's single method, `Omit` takes no arguments and returns a `[]string` which must be made up of the JSON struct tags for the type containing fields to be omitted.


### [item.Hookable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Hookable)
Hookable rovides lifecycle hooks into the http handlers which manage Save, Delete, Approve, and Reject routines 
All methods in its set take an `http.ResponseWriter, *http.Request` and return an `error`.

```go
type Hookable interface {
    BeforeAPICreate(http.ResponseWriter, *http.Request) error
    AfterAPICreate(http.ResponseWriter, *http.Request) error

    BeforeAPIUpdate(http.ResponseWriter, *http.Request) error
    AfterAPIUpdate(http.ResponseWriter, *http.Request) error

    BeforeAPIDelete(http.ResponseWriter, *http.Request) error
    AfterAPIDelete(http.ResponseWriter, *http.Request) error

    BeforeAdminCreate(http.ResponseWriter, *http.Request) error
    AfterAdminCreate(http.ResponseWriter, *http.Request) error

    BeforeAdminUpdate(http.ResponseWriter, *http.Request) error
    AfterAdminUpdate(http.ResponseWriter, *http.Request) error

    BeforeAdminDelete(http.ResponseWriter, *http.Request) error
    AfterAdminDelete(http.ResponseWriter, *http.Request) error

    BeforeSave(http.ResponseWriter, *http.Request) error
    AfterSave(http.ResponseWriter, *http.Request) error

    BeforeDelete(http.ResponseWriter, *http.Request) error
    AfterDelete(http.ResponseWriter, *http.Request) error

    BeforeApprove(http.ResponseWriter, *http.Request) error
    AfterApprove(http.ResponseWriter, *http.Request) error

    BeforeReject(http.ResponseWriter, *http.Request) error
    AfterReject(http.ResponseWriter, *http.Request) error

    // Enable/Disable used exclusively for addons
    BeforeEnable(http.ResponseWriter, *http.Request) error
    AfterEnable(http.ResponseWriter, *http.Request) error

    BeforeDisable(http.ResponseWriter, *http.Request) error
    AfterDisable(http.ResponseWriter, *http.Request) error
}
```

#### BeforeAPICreate
```go
BeforeAPICreate(res http.ResponseWriter, req *http.Request) error
```

#### AfterAPICreate
```go
AfterAPICreate(res http.ResponseWriter, req *http.Request) error
```

#### BeforeApprove
```go
BeforeApprove(res http.ResponseWriter, req *http.Request) error
```

#### AfterApprove
```go
AfterApprove(res http.ResponseWriter, req *http.Request) error
```

#### BeforeReject
```go
BeforeReject(res http.ResponseWriter, req *http.Request) error
```

#### AfterReject
```go
AfterReject(res http.ResponseWriter, req *http.Request) error
```

#### BeforeSave
```go
BeforeSave(res http.ResponseWriter, req *http.Request) error
```

#### AfterSave
```go
AfterSave(res http.ResponseWriter, req *http.Request) error
```

#### BeforeDelete
```go
BeforeDelete(res http.ResponseWriter, req *http.Request) error
```

#### AfterDelete
```go
AfterDelete(res http.ResponseWriter, req *http.Request) error
```

#### BeforeAPIDelete
```go
BeforeAPIDelete(res http.ResponseWriter, req *http.Request) error
```

#### AfterAPIDelete
```go
AfterAPIDelete(res http.ResponseWriter, req *http.Request) error
```

#### BeforeAPIUpdate
```go
BeforeAPIUpdate(res http.ResponseWriter, req *http.Request) error
```

#### AfterAPIUpdate
```go
AfterAPIUpdate(res http.ResponseWriter, req *http.Request) error
```

#### BeforeAdminCreate
```go
BeforeAdminCreate(res http.ResponseWriter, req *http.Request) error
```

#### AfterAdminCreate
```go
AfterAdminCreate(res http.ResponseWriter, req *http.Request) error
```

#### BeforeAdminUpdate
```go
BeforeAdminUpdate(res http.ResponseWriter, req *http.Request) error
```

#### AfterAdminUpdate
```go
AfterAdminUpdate(res http.ResponseWriter, req *http.Request) error
```

#### BeforeAdminDelete
```go
BeforeAdminDelete(res http.ResponseWriter, req *http.Request) error
```

#### AfterAdminDelete
```go
AfterAdminDelete(res http.ResponseWriter, req *http.Request) error
```

Hookable is implemented by Item by default as no-ops which are expected to be overridden. 

!!! note "Note" 
    returning an error from any of these `Hookable` methods will end the request, causing it to halt immediately after the hook. For example, returning an `error` from `BeforeDelete` will result in the content being kept in the database. The same logic applies to all of these interface methods that return an error - **the error defines the behavior**.


### [item.Identifiable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Identifiable)
Identifiable enables a struct to have its ID set/get. Typically this is done to set an ID to -1 indicating it is new for DB inserts, since by default a newly initialized struct would have an ID of 0, the int zero-value, and BoltDB's starting key per bucket is 0, thus overwriting the first record.
Most notable, Identifiable’s `String` method is used to set a meaningful display name for an Item. `String` is called by default in the Admin dashboard to show the Items of certain types, and in the default creation of an Item’s slug.
Identifiable is implemented by Item by default.


### [item.Sluggable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Sluggable)
Sluggable makes a struct locatable by URL with it's own path. As an Item implementing Sluggable, slugs may overlap. If this is an issue, make your content struct (or one which embeds Item) implement Sluggable and it will override the slug created by Item's `SetSlug` method with your own.
It is not recommended to override `SetSlug`, but rather the `String` method on your content struct, which will have a similar, more predictable effect.
Sluggable is implemented by Item by default.


### [item.Sortable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Sortable)
Sortable enables items to be sorted by time, as per the sort.Interface interface. Sortable is implemented by Item by default.

