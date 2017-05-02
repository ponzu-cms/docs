title: Content HTTP API

### Endpoints
#### Get Contents by Type (multiple items)
 - `/api/contents?type=<Type>`
 - optional params:
  - order (string: ASC / DESC, default: DESC)
  - count (int: -1 - N, default: 10, -1 returns all)
  - offset (int: 0 - N, default: 0)
 - Must be a `GET` request

#### Get Content by Type (single item)
 - `/api/content?type=<Type>&id=<ID>`
 - Must be a `GET` request

#### Get Content by Slug (single item)
 - `/api/content?slug=<Slug>`
 - Must be a `GET` request

#### New Content (single item)
 - `/api/content/create?type=<Type>`
 - Type must implement [`api.Createable`](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Createable) interface
 - Must be a multipart/form-data encoded `POST` request
 - Returns `JSON` response containing the ID of the item in the database (if approved), the status ("public" or "pending"), and the type (`<Type>`):
```javascript
{
  "data": [
    {
        "id": "6", // will be omitted if status is pending
        "type": "Review",
        "status": "public"
    }
  ]
}
```

#### Update Content (single item)
 - `/api/content/update?type=<Type>&id=<id>`
 - Type must implement [`api.Updateable`](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Updateable) interface
 - Must be a multipart/form-data encoded `POST` request
 - Returns `JSON` response containing the ID of the item in the database (if approved), the status ("public"), and the type (`<Type>`):
```javascript
{
  "data": [
    {
        "id": "6",
        "type": "Review",
        "status": "public"
    }
  ]
}
```

#### Delete Content (single item)
 - `/api/content/delete?type=<Type>&id=<id>`
 - Type must implement [`api.Deleteable`](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Deleteable) interface
 - Must be a multipart/form-data encoded `POST` request
 - Returns `JSON` response containing the ID of the item in the database (if approved), the status ("deleted"), and the type (`<Type>`):
```javascript
{
  "data": [
    {
        "id": "6",
        "type": "Review",
        "status": "deleted"
    }
  ]
}
```


All API endpoints are CORS-enabled (can be disabled in configuration at run-time) and API requests are recorded by your system to generate graphs of total requests and unique client requests within the Admin dashboard.

### Helpful links
[Typewriter](https://github.com/natdm/typewriter)
Generate & sync front-end data structures from Ponzu content types. ([Ponzu example](https://github.com/natdm/typewriter/blob/master/EXAMPLES.md#example-use-in-a-package-like-ponzu))
