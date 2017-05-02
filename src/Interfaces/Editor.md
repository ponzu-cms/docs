Ponzu provides a set of interfaces from the `management/editor` package which extend the system's functionality and determine how content editors are rendered within the CMS.

## Interfaces

### [editor.Editable](https://godoc.org/github.com/ponzu-cms/ponzu/management/editor#Editable)
Editable determines what `[]bytes` are rendered inside the editor page. Use Edtiable on anything inside your CMS that you want to provide configuration, editable fields, or any HTML/markup to display to an end-user.

### [editor.Mergeable](https://godoc.org/github.com/ponzu-cms/ponzu/management/editor#Mergeable)
Mergable enables a CMS end-user to merge the "Pending" content from an outside source into the "Public" section, and thus making it visible via the public content API. It also allows the end-user to reject content. "Approve" and "Reject" buttons will be visible on a the edit page for content submitted.