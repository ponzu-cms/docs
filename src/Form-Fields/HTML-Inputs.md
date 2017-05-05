title: HTML Input Elements for Ponzu Editor Forms

Ponzu provides a number of helpful HTML Inputs to create forms which CMS admins
use to manage content. The input functions are typically used inside a Content
type's `MarshalEditor()` func from within an `editor.Form()` - for example:

```go
// MarshalEditor writes a buffer of html to edit a Post within the CMS
// and implements editor.Editable
func (p *Post) MarshalEditor() ([]byte, error) {
    view, err := editor.Form(p,
        editor.Field{ // <- editor.Fields contain input-like funcs
            View: editor.Input("Title", p, map[string]string{ // <- makes a text input
                "label":       "Title",
                "type":        "text",
                "placeholder": "Enter the Title here",
            }),
        },
        editor.Field{
            View: editor.Richtext("Body", p, map[string]string{ // <- makes a WYSIWIG editor
                "label":       "Body",
                "placeholder": "Enter the Body here",
            }),
        },
        editor.Field{
            View: editor.Input("Author", p, map[string]string{
                "label":       "Author",
                "type":        "text",
                "placeholder": "Enter the Author here",
            }),
        },
    )

    if err != nil {
        return nil, fmt.Errorf("Failed to render Post editor view: %s", err.Error())
    }

    return view, nil
}
```
---

## Field Input Functions

There are many of these input-like HTML view funcs exported from Ponzu's
`management/editor` package. Below is a list of the built-in options:

### editor.Input

##### Screenshot
![HTML Input](/images/editor-input.png)

##### Function Signature
```go
Input(fieldName string, p interface{}, attrs, options map[string]string) []byte
```

##### Example

```go
...
editor.Field{
    View: editor.Input("Title", s, map[string]string{
        "label":       "Title",
        "type":        "text",
        "placeholder": "Enter the Title here",
    }),
},
...
```

---

### editor.InputRepeater

##### Screenshot
![HTML Input](/images/editor-input-repeater.png)

##### Function Signature
```go
InputRepeater(fieldName string, p interface{}, attrs, options map[string]string) []byte
```

##### Example

```go
...
editor.Field{
    View: editor.InputRepeater("Title", s, map[string]string{
        "label":       "Titles",
        "type":        "text",
        "placeholder": "Enter the Title here",
    }),
},
...
```

---

### editor.Checkbox
The `editor.Checkbox` function returns any number of checkboxes in a collection,
defined by the value:name map of options.

##### Screenshot
![HTML Checkbox](/images/editor-checkbox.png)

##### Function Signature
```go
Checkbox(fieldName string, p interface{}, attrs, options map[string]string) []byte
```

##### Example

```go
...
editor.Field{
    View: editor.Checkbox("Options", s, map[string]string{
        "label": "Options",
    }, map[string]string{
        // "value": "Display Name",
        "1": "First",
        "2": "Second",
        "3": "Third",
    }),
},
...
```

---

### editor.Richtext

##### Screenshot
![HTML Richtext Input](/images/editor-richtext.png)

##### Function Signature
```go
Richtext(fieldName string, p interface{}, attrs map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.Richtext("Opinion", s, map[string]string{
        "label":       "Rich Text Editor",
        "placeholder": "Enter the Opinion here",
    }),
},
...
```

---

### editor.Tags

##### Screenshot
![HTML Tags Input](/images/editor-tags.png)

##### Function Signature
```go
Tags(fieldName string, p interface{}, attrs map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.Tags("Category", s, map[string]string{
        "label":       "Tags",
        "placeholder": "+Category",
    }),
},
...
```

---

### editor.File

##### Screenshot
![HTML File Input](/images/editor-file.png)

##### Function Signature
```go
File(fieldName string, p interface{}, attrs map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.File("Photo", s, map[string]string{
        "label":       "File Upload",
        "placeholder": "Upload the Photo here",
    }),
},
...
```

---

### editor.FileRepeater

##### Screenshot
![HTML File Input](/images/editor-file-repeater.png)

##### Function Signature
```go
FileRepeater(fieldName string, p interface{}, attrs map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.FileRepeater("Photo", s, map[string]string{
        "label":       "File Upload Repeater",
        "placeholder": "Upload the Photo here",
    }),
},
...
```

---

### editor.Select

##### Screenshot
![HTML Select Input](/images/editor-select.png)

##### Function Signature
```go
func Select(fieldName string, p interface{}, attrs, options map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.Select("Rating", s, map[string]string{
        "label": "Select Dropdown",
    }, map[string]string{
        // "value": "Display Name",
        "G":     "G",
        "PG":    "PG",
        "PG-13": "PG-13",
        "R":     "R",
    }),
},
...
```

---

### editor.SelectRepeater

##### Screenshot
![HTML Select Input](/images/editor-select-repeater.png)

##### Function Signature
```go
func SelectRepeater(fieldName string, p interface{}, attrs, options map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.SelectRepeater("Rating", s, map[string]string{
        "label": "Select Dropdown Repeater",
    }, map[string]string{
        // "value": "Display Name",
        "G":     "G",
        "PG":    "PG",
        "PG-13": "PG-13",
        "R":     "R",
    }),
},
...
```

---

### editor.Textarea

##### Screenshot
![HTML Textarea Input](/images/editor-textarea.png)

##### Function Signature
```go
Textarea(fieldName string, p interface{}, attrs map[string]string) []byte
```

##### Example
```go 
...
editor.Field{
    View: editor.Textarea("Readme", s, map[string]string{
        "label":       "Textarea",
        "placeholder": "Enter the Readme here",
    }),
},
...
```

---

## Data References
It is common to want to keep a reference from one Content type to another. To do
this in Ponzu, use the [`bosssauce/reference`](https://github.com/bosssauce/reference) 
package. It comes pre-installed in Ponzu as an ["Addon"](/Ponzu-Addons/Using-Addons).

### reference.Select

##### Screenshot
![HTML Select Input](/images/editor-select.png)

##### Function Signature
```go
func Select(fieldName string, p interface{}, attrs map[string]string, contentType, tmplString string) []byte
```

##### Example
```go 
...
editor.Field{
    View: reference.Select("DirectedBy", s, map[string]string{
        "label": "Select Dropdown",
    }, "Director", `{{.last-name}}, {{.first_name}}`),
},
...
```

---

### reference.SelectRepeater

##### Screenshot
![HTML Select Input](/images/editor-select-repeater.png)

##### Function Signature
```go
func SelectRepeater(fieldName string, p interface{}, attrs map[string]string, contentType, tmplString string) []byte
```

##### Example
```go 
...
editor.Field{
    View: reference.SelectRepeater("PlacesFilmed", s, map[string]string{
        "label": "Select Dropdown Repeater",
    }, "Location", `{{.name}}, {{.region}}`),
},
...
```

---