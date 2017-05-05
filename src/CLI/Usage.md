title: Using the Ponzu Command Line Interface (CLI)

```bash
$ ponzu [flags] command <params>
```

## Commands

### new

Creates a 'ponzu' directory, or one by the name supplied as a parameter 
immediately following the 'new' option in the $GOPATH/src directory. Note: 
'new' depends on the program 'git' and possibly a network connection. If 
there is no local repository to clone from at the local machine's $GOPATH, 
'new' will attempt to clone the 'github.com/ponzu-cms/ponzu' package from 
over the network.

Example:
```bash
$ ponzu new myProject
> New ponzu project created at $GOPATH/src/myProject
```

Errors will be reported, but successful commands return nothing.

---

### generate, gen, g

Generate boilerplate code for various Ponzu components, such as `content`.

Example:
```bash
            generator      struct fields and built-in types...
             |              |
             v              v    
$ ponzu gen content review title:"string" body:"string":richtext rating:"int"
                     ^                                   ^
                     |                                   |
                    struct type                         (optional) input view specifier
```

The command above will generate the file `content/review.go` with boilerplate
methods, as well as struct definition, and corresponding field tags like:

```go
type Review struct {
	Title  string   `json:"title"`
	Body   string   `json:"body"`
	Rating int      `json:"rating"`
	Tags   []string `json:"tags"`
}
```

The generate command will intelligently parse more sophisticated field names
such as 'field_name' and convert it to 'FieldName' and vice versa, only where 
appropriate as per common Go idioms. Errors will be reported, but successful 
generate commands return nothing.

**Input View Specifiers** _(optional)_

The CLI can optionally parse a third parameter on the fields provided to generate 
the type of HTML view an editor field is presented within. If no third parameter
is added, a plain text HTML input will be generated. In the example above, the 
argument shown as `body:string:richtext` would show the Richtext input instead
of a plain text HTML input (as shown in the screenshot). The following input
view specifiers are implmeneted:

| CLI parameter | Generates |
|---------------|-----------| 
| checkbox | `editor.Checkbox()` |
| custom | generates a pre-styled empty div to fill with HTML |
| file | `editor.File()` |
| hidden | `editor.Input()` + uses type=hidden |
| input, text | `editor.Input()` |
| richtext | `editor.Richtext()` |
| select | `editor.Select()` |
| textarea | `editor.Textarea()` |
| tags | `editor.Tags()` |

---

### build

From within your Ponzu project directory, running build will copy and move 
the necessary files from your workspace into the vendored directory, and 
will build/compile the project to then be run. 

Optional flags:

- `-gocmd` sets the binary used when executing `go build` within `ponzu` build step

Example:
```bash
$ ponzu build
(or)
$ ponzu -gocmd=go1.8rc1 build # useful for testing
```

Errors will be reported, but successful build commands return nothing.

---

### run

Starts the HTTP server for the JSON API, Admin System, or both.
The segments, separated by a comma, describe which services to start, either 
'admin' (Admin System / CMS backend) or 'api' (JSON API), and, optionally, 
if the server should utilize TLS encryption - served over HTTPS, which is
automatically managed using Let's Encrypt (https://letsencrypt.org) 

Optional flags:

- `-port` sets the port on which the server listens for HTTP requests [defaults to 8080]
- `-httpsport` sets the port on which the server listens for HTTPS requests [defaults to 443]
- `--https` enables auto HTTPS management via Let's Encrypt (port is always 443)
- `--devhttps` generates self-signed SSL certificates for development-only (port is 10443)

Example: 
```bash
$ ponzu run
(or)
$ ponzu -port=8080 --https run admin,api
(or) 
$ ponzu run admin
(or)
$ ponzu -port=8888 run api
(or)
$ ponzu --devhttps run
```
Defaults to `$ ponzu -port=8080 run admin,api` (running Admin & API on port 8080, without TLS)

*Note:* 
Admin and API cannot run on separate processes unless you use a copy of the
database, since the first process to open it receives a lock. If you intend
to run the Admin and API on separate processes, you must call them with the
'ponzu' command independently.

---

### upgrade

Will backup your own custom project code (like content, addons, uploads, etc) so
we can safely re-clone Ponzu from the latest version you have or from the network 
if necessary. Before running `$ ponzu upgrade`, you should update the `ponzu`
package by running `$ go get -u github.com/ponzu-cms/ponzu/...` 

Example:
```bash
$ ponzu upgrade
```

---

### add, a

Downloads an addon to GOPATH/src and copys it to the Ponzu project's ./addons directory.
Must be called from within a Ponzu project directory.

Example:
```bash
$ ponzu add github.com/bosssauce/fbscheduler
```

Errors will be reported, but successful add commands return nothing.

---

### version, v

Prints the version of Ponzu your project is using. Must be called from within a 
Ponzu project directory. By passing the `--cli` flag, the `version` command will 
print the version of the Ponzu CLI you have installed.

Example:
```bash
$ ponzu version
> Ponzu v0.7.1
(or)
$ ponzu --cli version
> Ponzu v0.7.2
```

---

## Contributing

1. Checkout branch ponzu-dev
2. Make code changes
3. Test changes to ponzu-dev branch
    - make a commit to ponzu-dev
    - to manually test, you will need to use a new copy (ponzu new path/to/code), 
    but pass the `--dev` flag so that ponzu generates a new copy from the `ponzu-dev` 
    branch, not master by default (i.e. `$ponzu --dev new /path/to/code`)
    - build and run with `$ ponzu build` and `$ ponzu run`
4. To add back to master: 
    - first push to origin ponzu-dev
    - create a pull request 
    - will then be merged into master

_A typical contribution workflow might look like:_
```bash
# clone the repository and checkout ponzu-dev
$ git clone https://github.com/ponzu-cms/ponzu path/to/local/ponzu # (or your fork)
$ git checkout ponzu-dev

# install ponzu with go get or from your own local path
$ go get github.com/ponzu-cms/ponzu/...
# or
$ cd /path/to/local/ponzu 
$ go install ./...

# edit files, add features, etc
$ git add -A
$ git commit -m 'edited files, added features, etc'

# now you need to test the feature.. make a new ponzu project, but pass --dev flag
$ ponzu --dev new /path/to/new/project # will create $GOPATH/src/path/to/new/project

# build & run ponzu from the new project directory
$ cd /path/to/new/project
$ ponzu build && ponzu run

# push to your origin:ponzu-dev branch and create a PR at ponzu-cms/ponzu
$ git push origin ponzu-dev
# ... go to https://github.com/ponzu-cms/ponzu and create a PR
```

**Note:** if you intend to work on your own fork and contribute from it, you will
need to also pass `-fork=path/to/your/fork` (using OS-standard filepath structure),
where `path/to/your/fork` _must_ be within `$GOPATH/src`, and you are working from a branch
called `ponzu-dev`. 

For example: 
```bash
# ($GOPATH/src is implied in the fork path, do not add it yourself)
$ ponzu --dev -fork=github.com/nilslice/ponzu new /path/to/new/project
```
