Before you can start writing Golang code. You first need to set up a module. A module is just a folder that will store all your Golang project's source code. In my case I'll create a module called 'my_go_app1':


Create a new empty git repo and clone it. In my case I created and cloned:

```bash
git clone git@github.com:Sher-Chowdhury/gsg_hello_world.git
cd gsg_hello_world/
ls
```

At this stage this repo is empty. Next we run the "Go module initialisation" command inside this repo:


```bash
$ go mod init github.com/Sher-Chowdhury/gsg_hello_world.git
go: creating new go.mod: module github.com:Sher-Chowdhury/gsg_hello_world
```

Here I created a module, using the github repo's url as it's name. This ends up creating a file called "go.mod":

```bash
$ ls -l
total 8
-rw-------  1 schowdhury  staff  62  2 Sep 21:10 go.mod
$ cat go.mod 
module github.com/Sher-Chowdhury/gsg_hello_world

go 1.12
```

Then I created a file called "main.go":


```go
package main

func main() {
    println("hello world")
}
```

You can [run this here](https://play.golang.org/p/EGuGDgEM9Ex), or just run:

```bash
$ go run main.go
hello world
```

the [go run](https://golang.org/pkg/cmd/go/internal/run/) command specificially loads and runs all *.go files that have the first line set to 'package main'. Once all the 'package main' files are collated, `go run` then looks for the 'func main()' function which is the starting point of your code. 


You can compile your code into an executable binary and then run that:

```bash
$ go build main.go
$ ll -lh main
-rwxr-xr-x  1 schowdhury  staff   2.0M  9 Oct 20:36 main
$ file main
main: Mach-O 64-bit executable x86_64
$ ./main
hello world
```

The binary's name is taken from main.go, but without the .go extension. 

That means, you can give the main.go a different name, e.g.:

```
$ mv main.go hello.go
$ go run hello.go
hello world
╰➤ go build hello.go
╰➤ ./hello
hello world
```

Basically, 'main.go' is juste a naming convention. 



The binary is also quite big, at 2MB. That's because it's designed to run as a standalone without needing to depend on any external libraries. 

This build command will only work as long as:

- your project defines a package called 'main', i.e. you have one or more .go files that starts with the line "package main". This helps to tell golang which files makes up the core application of this project, rather than secondary library *.go files.
- your project has exactly one 'func main(){...}' definition, and the file containing this function needs to start with the line "package main".

the above binary by default will run on macOS based OS (Darwin). But's [easy to build for other distros/architecture](https://golang.org/doc/install/source#environment). E.g. if you want to build for Linux with 64 architecture, then you specify those setting as command line variables:

```bash
$ GOOS=linux GOARCH=amd64 go build .
$ file gsg_hello_world
gsg_hello_world: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped
```


## Packages  Overview

There are 2 types of packages

1. main package
2. library package

### library packages
A library package is essentially a bunch of go source code that get's called by other main/library packages. The 2 main characteristics of a library package:

1. If the packages root folder is called 'users', then the *.go files directly under that folder, start with the line `package users`
2. they don't have the 'func main()' function, i.e no entry point. Therefore you can't create an executable from a library package. 

However for now we will be looking at main packages.  

### main packages
You can write all your go code into a single *.go file. However that single file can get quite big and difficult to read, which is why it's best practice to break your code into several smaller *.go files.

All the *.go files that forms part of your main go application, must have it's first line set as `package main`. This is the [package declaration](https://golang.org/doc/code.html#PackageNames) and this tells golang which *.go forms part of your main go application. Among these main files, exactly one file must contain the 'func main()' function. This file is that starting point of your go app. By convention this starting-point file is named as one of following:

- *main.go*
- *you-golang-package-name.go* - e.g. if your project creates a executable file called 'terraform', then this file is called terraform.go. In other word's it needs to match the repo name, e.g. if you're repo is https://github.com/Sher-Chowdhury/gsg_hello_world, then you should call it gsg_hello_world.go. 



A golang project is made up of [packages](https://golang.org/ref/spec#Packages), and each package in turn can make use of other packages. [println](https://golang.org/pkg/builtin/#println) is one of a [small handful of golang functions that comes builtin](https://golang.org/pkg/builtin) with golang. println is quite a primitive function, and that's why it's more common to use the more feature-rich alternative, Println, which comes as part of the `fmt` package:


```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("hello world")
}
```

## The standard library

Golang comes include with a [Standard Library](https://golang.org/pkg/#stdlib) of packages, of which the [fmt](https://golang.org/pkg/fmt/) package is one of them. You can get help info for fmt like this:

```bash
$ go doc fmt
package fmt // import "fmt"

Package fmt implements formatted I/O with functions analogous to C's printf
and scanf. The format 'verbs' are derived from C's but are simpler.
...

$ go doc fmt.Println
func Println(a ...interface{}) (n int, err error)
    Println formats using the default formats for its operands and writes to
    standard output. Spaces are always added between operands and a newline is
...

```

There's a html formatted docs that you can access by running:

```
$ godoc -http=:8001
```
or you can omit the = sign:
```
$ godoc -http :8001
```

then open up web browser to http://localhost:8001




## downloading and running modules

Now if you want to run this module on another workstation, then you first need to commit and push this to github, and on another workstation, you just have to run:

```bash
go get github.com/Sher-Chowdhury/gsg_hello_world
go run github.com/Sher-Chowdhury/gsg_hello_world
> hello world
```

As explained above, you can also build the code from source:

```bash
go build .
file gsg_hello_world
> gsg_hello_world: Mach-O 64-bit executable x86_64
./gsg_hello_world
> hello world
```

This time the binary's name is taken from the go.mod file. 


That's why it's important to name your module after your github repo's url. 

The `go run` command actually runs the `go build` command to temporary build a binary, then execute that binary, then deletes the binary. 



## Using external packages

Let's say you have:

```
package main

// need to first run: go get github.com/fatih/color 
import (
    "github.com/fatih/color"
)
 
func main() {
    color.Red("Roses are red")
    color.Blue("Violets are blue"
}
```

If we try to build/run this, the external package gets downloaded and placed in our common library folder:

```
$ go run .
go: finding github.com/fatih/color v1.7.0
go: finding github.com/mattn/go-isatty v0.0.10
go: finding github.com/mattn/go-colorable v0.1.4
go: finding github.com/mattn/go-isatty v0.0.8
go: finding golang.org/x/sys v0.0.0-20191008105621-543471e840be
go: finding golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
go: downloading github.com/fatih/color v1.7.0
go: extracting github.com/fatih/color v1.7.0
go: downloading github.com/mattn/go-isatty v0.0.10
go: downloading github.com/mattn/go-colorable v0.1.4
go: extracting github.com/mattn/go-isatty v0.0.10
go: extracting github.com/mattn/go-colorable v0.1.4
Roses are red
Violets are blue
```

Where our dependencies are stored somewhere in:

```
go env GOPATH
> /Users/schowdhury/go
```

You can empty out this cache:

```
$ go clean --modcache
```

## Useful links

https://medium.com/@ckeyes88/go-modules-in-real-life-87a21fb4d8aa