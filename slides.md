---
theme: seriph
# background: /images/Gophers6.jpeg
background: https://cover.sli.dev
title: Graphs, games and Go
info: |
  ## Graphs and Games: can Go take a ticket to ride?
  Slides for GoLab 2024
class: text-center
layout: cover
highlighter: shiki
drawings:
  persist: false
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Graphs and Games: can Go take a ticket to ride?

<div class="absolute bottom-10 text-left">
    <div>Michele Caci</div>
    <div>Senior Software Engineer at Amadeus</div>
    <div class="flex m-0 gap-1">
      <a href="https://github.com/mcaci" target="_blank" alt="Michele's GitHub" title="Michele's GitHub"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-github />
      </a>
      <a href="https://x.com/goMicheleCaci" target="_blank" alt="Michele's X" title="Michele's X"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-x />
      </a>
      <a href="https://www.linkedin.com/in/michele-caci-47770132/" target="_blank" alt="Michele's Linkedin" title="Michele's Linkedin"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-linkedin />
      </a>
    </div>
</div>

---
transition: fade-out
layout: lblue-fact
---

Games help you develop skills!
<v-click>
<div class="font-size-8">and they work perfectly with Go</div>
</v-click>

---
transition: fade-out
---

# Notes

GRAPHS AND GAMES: CAN GO TAKE A TICKET TO RIDE?

November 13 2024 - 12:00 (25 min)

The outline of the talk should go this way

- Introduce myself and my passion for board games
- Explain the game Ticket to Ride and why I decided to implement it in Go
- Show the creation of a Ticket to Ride board in Go
- Explore some Graph algorithms that can give us some insights on
- If time permits also show some gameplay examples

LEVEL: Introductory and Overview

Add references to Daniela Petruzalek talk on Pacman and Drishti Jain for how to create a game in Go (and wallrush too)

---
transition: fade-out
layout: image-right
image: /images/michele.jpeg
backgroundSize: 80%
---

# Whoami

About myself (note: change picture)

I’m a Gopher since 2018
- Practised via the [Tour of Go](https://go.dev/tour/) and [exercism](https://exercism.org/)
- I have previously worked in Java, Scala and C++

I work in Amadeus in a team that creates tooling in Go to assist applications deploying in the cloud

I speak at conferences, about Go and cloud topics

Besides programming I enjoy swimming, cooking, learning languages and board games

<!-- 
Bio: I'm Michele, Italian from Sicily, I am a passionate Gopher since 2018 and before then I used to work in Java, Scala and C++. I always like make side projects and develop new things when time let's me :D. Besides programming, I enjoy swimming, cooking and learning languages; currently, I'm learning Japanese: GOのワークショップへようこそ！ 

You can find me in:
- github: [mcaci](https://github.com/mcaci)
- linkedin: [Michele Caci](https://www.linkedin.com/in/michele-caci-47770132/)
- X/Twitter: [@goMicheleCaci](https://x.com/goMicheleCaci)
-->

---
transition: fade-out
layout: image-right
image: /images/TTR_USA_map.jpg
backgroundSize: 97%
---

# The board game of the day

Ticket to Ride

<v-click>

In this game you have:
</v-click>

<v-clicks>

- A board representing the map of the United States with cities and railway lines
- A set of trains and cards to spend to occupy railway lines
- A set of objectives to give you more points
</v-clicks>

<v-click>

The player with most points wins
</v-click>

---
transition: fade-out
layout: image-right
image: /images/TTR_USA_map.jpg
backgroundSize: 97%
---

# That really looks like a graph to me

Ticket to Ride

The cities are vertices

The railway links are the edges

The game can be analyzed using graph algorithms which can be difficult but Go will make the much easier

Let's see how

---
transition: fade-out
---

# Go and Graphs

Introduction

We have two elements for Go and Graphs to go together

1. Go can easily be written line by line from pseudocode
2. Go has generics and interfaces which can help in making data structure adaptable to any kind of data

In other words we can decouple the data structure itself from the kind of data it holds

---
transition: fade-out
---

# Go and Graphs

Some concepts on the graphs

A vertex is a node that is holding data, for simplicity we will have it comparable

```go
type Vertex[T comparable] struct { 
  E T 
}
```

An edge is a pair of vertices that can hold some properties

```go
type Edge[T comparable] struct {
	X, Y *Vertex[T]
	P    EdgeProperty
}
type EdgeProperty any
```

---
transition: fade-out
---

# Go and Graphs

Some concepts on the graphs

A graph then is a collection of edges and vertices

We can represent a graph in several ways but they all share in common a certain set of behaviors (aka. an interface)

```go
type Graph[T comparable] interface { 
  Vertices() []*Vertex[T]
	Edges() []*Edge[T]
	AddVertex(v *Vertex[T])
	RemoveVertex(v *Vertex[T])
	ContainsVertex(v *Vertex[T]) bool
	AddEdge(e *Edge[T])
	RemoveEdge(e *Edge[T])
	ContainsEdge(e *Edge[T]) bool
}
```

One common representation of a graph is the list of vertices:

```go
type ArcsList[T comparable] struct {
	v        []*Vertex[T]
	e        []*Edge[T]
}
```

---
transition: fade-out
---

# Go and Graphs

Using ticket to ride as an example

Once we have a graph up, we can start reasoning on it using the algorithms we have at our disposal

- Is there a path connecting a city to another one
  - At the beginning all of the cities are connected
  - As soon as we occupy railway links and remove them from the initial graph this may not be the case any more
- In graph algorithm terms
  - Are the two nodes in the same connected component

---
transition: fade-out
---

# Go and Graphs

Using ticket to ride as an example

Once we have a graph up, we can start reasoning on it using the algorithms we have at our disposal

- What is the shortest path between the two cities
  - This evolves when railway links are occupied
- In graph algorithm terms
  - Looking for the shortest path between two nodes


---
transition: fade-out
---

# Go and Graphs

Using ticket to ride as an example

Once we have a graph up, we can start reasoning on it using the algorithms we have at our disposal

- What is the shortest path between the two cities
  - This evolves when railway links are occupied
- In graph algorithm terms
  - Looking for the shortest path between two nodes

---
transition: fade-out
---

# Go and Graphs

Some concepts on the graphs

<v-click>

````md magic-move {lines: true}
```go {all|2|2,6}
func forwardContentToTarget(source io.Reader, target io.Writer) error {
  bytes, err := io.ReadAll(source) // read the source
  if err != nil {
    return err
  }
  _, err = fmt.Fprint(target, bytes) // and transfer it
  return err
}
```

```go {2|all}
func forwardContentToTarget(source io.Reader, target io.Writer) error {
  _, err := io.Copy(target, source) // read the source and transfer it
  if err != nil {
    return err
  }
}
```
````
</v-click>

<v-click>
How about transferring the same content to multiple targets?
</v-click>

<v-click>
````md magic-move {lines: true}
```go {all|2|2,6-9}
func forwardContentToTargets(source io.Reader, targets ...io.Writer) error {
  bytes, err := io.ReadAll(source) // read the source
  if err != nil {
    return err
  }
  for _, target := range targets {
    _, err = fmt.Fprint(target, bytes) // and transfer it
    return err
  }
  return nil
}
```

```go {3,5|all}
func forwardContentToTargets(source io.Reader, targets ...io.Writer) error {
  // create a writer that includes all targets
  dsts := io.MultiWriter(targets)
  // read the source and transfer it
  _, err := io.Copy(dsts, source)
  if err != nil {
    return err
  }
}
```
````
</v-click>

---
transition: fade-out
layout: lblue-fact
---

Gem #1: delegate the complexity of the code
<div class="font-size-10">to the standard library</div>

---
transition: fade-out
---

# Example #2

Creating new errors

<v-click>
````md magic-move {lines: true}
```go
// A new error
err := errors.New("an error occurred")
```

```go
// A new error with fmt.Errorf
err := fmt.Errorf("an error occurred: %s", msg)
```

```go
// A new error with fmt.Errorf referencing another error
err := fmt.Errorf("an error occurred: %s", oErr.Error())
```
````
</v-click>

<v-clicks>

Making a reference to an error via its value (%\[s|q|v]) makes it more difficult to handle
- Unit tests broken because the error message was not matching the one expected

To our help, error wrapping was released with Go [v1.13](https://tip.golang.org/doc/go1.13#error_wrapping) and multi-error wrapping with Go [v1.20](https://tip.golang.org/doc/go1.20#errors)

We can replace %\[s|q|v] with %w or use __errors.Join__ to delegate to the standard library the aspect of formatting of the error and concentrate only on its creation
</v-clicks>

<!-- 
Go [v1.13](https://tip.golang.org/doc/go1.13#error_wrapping) (Sep 2019) 
Go [v1.20](https://tip.golang.org/doc/go1.20#errors) (Feb 2023) 
-->

---
transition: fade-out
layout: lblue-fact
---

Gem #2: A human cares about the error message
<v-click>
<div class="font-size-8">a program cares about what kind of error it is</div>
</v-click>


<!-- and if you see fmt.Errorf still using %s to refer to error messages translate them to wrapped errors with no harm -->

---
transition: fade-out
---

# Example #3

Slices and maps packages (manipulating an http.Header)

<v-clicks>

````md magic-move {lines: true}
```go
// http.Header is an alias for map[string][]string
// update updates the header h with some headers to add and some keys to delete
func update(h http.Header, toAdd http.Header, toDelete []string) {
  for key, values := range toAdd {
    for _, value := range values {
      h.Add(key, value)
    }
  }
  for _, key := range toDelete {
    delete(h[key])
  }
}
```

```go
// http.Header is an alias for map[string][]string
// update updates the header h with some headers to add and some keys to delete
func update(h http.Header, toAdd http.Header, toDelete []string) {
  // Copy all key/value pairs from a source map (toAdd) to the destination one (h)
  maps.Copy(h, toAdd)
  // Deletes all k,v pairs where the function yields true
  maps.DeleteFunc(h, func(k, v string)) bool { return slices.Contains(toDelete, k)}
}
```
````

__Maps__ and __slices__ packages were released with Go [v1.21](https://tip.golang.org/doc/go1.21#library) (Aug 2023) together with generics

These packages use generics to simplify maps and slices operations
</v-clicks>

---
transition: fade-out
layout: lblue-fact
---

Gem #3: check new Go features as they released
<v-click>
<div class="font-size-8">and see if you can use them to simplify your code</div>
</v-click>

---
transition: fade-out
layout: image-right
image: /images/Gophers10.jpeg
backgroundSize: 80%
---

# Conclusions

Striving for simplicity

<v-click>

The topic of simplicity is not new

- Rob Pike's [talk](https://www.youtube.com/watch?v=rFejpH_tAHM&t=1s): Simplicity is Complicated;
- Go Time's episode [#296](https://changelog.com/gotime/296): Principles of simplicity;

</v-click>

<v-click>Today we saw simplicity in action with few gems</v-click>

<v-click>

__What you can do__ is from time to time have a look at:

- the [standard library](https://pkg.go.dev/std) packages
- the [release notes](https://tip.golang.org/doc/devel/release)
</v-click>

<v-click>

__Because these are the places where you'll find the most valuable gems to make your Go code simpler__
</v-click>

---
layout: fact
transition: fade-out
class: "font-size-7.8"
---

And making our code simpler is how we step up our Go game!

---
layout: lblue-end
transition: fade-out
---

<div class="text-white font-size-10">
Thank you very much!
</div>

<div class="absolute bottom-10">
  <div  class="text-white">Michele Caci</div>
  <div class="flex m-0 gap-1">
    <a href="https://github.com/mcaci" target="_blank" alt="Michele's GitHub" title="Michele's GitHub"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-github />
    </a>
    <a href="https://x.com/goMicheleCaci" target="_blank" alt="Michele's X" title="Michele's X"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-x />
    </a>
    <a href="https://www.linkedin.com/in/michele-caci-47770132/" target="_blank" alt="Michele's Linkedin" title="Michele's Linkedin"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-linkedin />
    </a>
  </div>
</div>