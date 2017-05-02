Ponzu provides a set of interfaces from the `system/api` package which enable richer interaction with your system from external clients. If you need to allow 3rd-party apps to manage content, use the following interfaces.

## Interfaces

### [api.Createable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Createable)
Externalable enables 3rd-party clients (outside the CMS) to send content via a `multipart/form-data` encoded `POST` request to a specific endpoint: `/api/content/create?type=<Type>`. When `api.Createable` is implemented, content will be saved from the request in a "Pending" section which will is visible only within the CMS.

```go 
type Createable interface {
    Create(http.ResponseWriter, *http.Request) error
}
```

### [api.Updateable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Updateable)
Updateable enables 3rd-party clients (outside the CMS) to update existing content via a `multipart/form-data` encoded `POST` request to a specific endpoint: `/api/content/update?type=<Type>&id=<id>`.

```go 
type Updateable interface {
    Update(http.ResponseWriter, *http.Request) error
}
```

### [api.Deleteable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Deleteable)
Updateable enables 3rd-party clients (outside the CMS) to delete existing content via a `multipart/form-data` encoded `POST` request to a specific endpoint: `/api/content/delete?type=<Type>&id=<id>`. Request validation should be employed otherwise any client could delete data from your database.

```go 
type Deleteable interface {
    Delete(http.ResponseWriter, *http.Request) error
}
```

### [api.Trustable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Trustable)
Trustable provides a way for submitted content (via `api.Createable`) to bypass the `editor.Mergeable` step in which CMS end-users must manually click the "Approve" button in order for content to be put in the "Public" section and access via the content API endpoints. `api.Trustable` has a single method: `AutoApprove` which will automatically approve content, bypassing the "Pending" section altogether.

```go
type Trustable interface {
    AutoApprove(http.ResponseWriter, *http.Request) error
}
```