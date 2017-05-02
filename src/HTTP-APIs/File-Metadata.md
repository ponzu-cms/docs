title: File Metadata HTTP API

Ponzu provides a read-only HTTP API to get metadata about the files that have been uploaded to your system. As a security and bandwidth abuse precaution, the API is only queryable by "slug" which is the normalized filename of the uploaded file. 

### Endpoints
#### Get File by Slug (single item)
 - `/api/uploads?slug=<Slug>`
 - Must be a `GET` request