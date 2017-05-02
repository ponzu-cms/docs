title: Extending Content through built-in Interfaces

Extending your content types with more features and functionality within the system
is done by implementing the various built-in interfaces provided by Ponzu. To learn 
more about interfaces, see [A Tour of Go - Interfaces](https://tour.golang.org/methods/10).

## Interfaces

All Content types which embed an `item.Item` will implicitly implement its many
interfaces. In Ponzu, the following interfaces are exported from the `system/item`
package and have a default implementation which can be overridden to change your
content types' functionality within the system.

- [item.Hideable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Hideable)
- [item.Pushable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Pushable)
- [item.Omittable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Omittable)
- [item.Hookable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Hookable)
- [item.Identifiable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Identifiable)
- [item.Sortable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Sortable)
- [item.Sluggable](https://godoc.org/github.com/ponzu-cms/ponzu/system/item#Sluggable)

## API Interfaces

To enable 3rd-party clients to interact with your Content types, you can extend your types with the API interfaces:

- [api.Createable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Createable)
- [api.Updateable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Updateable)
- [api.Deleteable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Deleteable)
- [api.Trustable](https://godoc.org/github.com/ponzu-cms/ponzu/system/api#Trustable)

