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

Games help you develop skills
<v-click>
<div class="font-size-8">and Go makes game development easy</div>
</v-click>

<!-- 
I want to start with this statement that comes from my personal experience. And this conviction has increased a lot by watching my son playing, during the course of his first year of age. He turned 1 year just a month ago by the way.

Games help you develop skills and for what it matters to us, Go makes game development easy and today we'll see how.
-->

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

---
transition: fade-out
layout: image-right
image: /images/michele.jpeg
backgroundSize: 80%
---

# About myself

whoami (note: change picture)

I am a Gopher since 2018

- but also have experiences in other programming languages

My company is Amadeus

- my team that creates middleware tools in Go that help configure applications deployed in the cloud

I speak at conferences

- mostly about Go and cloud topics

On my free time I enjoy swimming, cooking, learning languages and playing board games

<!-- 
I'm Michele, Italian from Sicily, I am a passionate Gopher since 2018 and before then I used to work in Java, Scala and C++. I always like make side projects and develop new things. Besides programming, I enjoy swimming, cooking and learning languages; currently, I'm learning Japanese: GOのワークショップへようこそ！ 

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

# Today's board game

Ticket to Ride

<v-clicks>

The board represents the United States map

- The dots are cities/railway stations
- The lines are railway links that connect them

The resources are train tokens and colored cards that are spent to occupy railway links

- For example to occupy the line between Miami and Atlanta you'll need to spend 5 trains tokens and 5 blue cards

The objective is to get the highest score

- By occupying railway links
- By connecting cities from objective cards
</v-clicks>

<img v-click="[4, 5]" src="/images/atlantaMiami.png" alt="detail of the atlanta-miami route" class="absolute rounded shadow" style="top: 25%; right: 5%; height: 50%; width: 40%;"/>

<!-- 
Today we will look at this specific game: Ticket to Ride.
Let me have by show of hand: who knows or has played to this game so far?

Nice!

For those who don't know, I'll give you an idea of what it its about
-->

---
transition: fade-out
---

# A first gameplay idea

The let's go crazy approach

For this idea we'll simplify the rules by:

- giving unlimited resources to the players
- giving each player no objectives

By this means we'll only need the list of railway links

Each player will take turns to occupy the links

- Either by selecting the ones with most value (deterministic and predictable)
- Or by selecting a random link (less predictable, funnier results)

Let's see how this looks in the code

<v-click>

````md magic-move {lines: true}
```go {none|all}
package main

func main() {
  // ...
}
```

```go {none|all}
package main

func main() {
  // random selection
}
```
````
</v-click>

<!-- 
The random line selection
-->

---
transition: slide-left
layout: image
image: /images/TTR_USA_map.jpg
backgroundSize: fit
---

<!-- 
If you pay closer attention to the board, you'll notice one interesting property
 -->

---
transition: slide-left
layout: image
image: /images/aGraphToMe.jpeg
backgroundSize: fit
---

---
transition: slide-left
layout: image
image: /images/TTR_USA_map.jpg
backgroundSize: fit
---

---
transition: fade-out
layout: image-right
image: /images/aGraphToMeReallyYeah.jpeg
backgroundSize: 90%
---

# A better gameplay idea

Modelling Ticket to Ride as a Graph

Thanks to this property we can use graph algorithms to have our players make better choices

But if graph algorithms look scary to you I have good news for you

<!-- 
Let's see how, there are two elements that stand out:

1. Go can easily be written line by line from pseudocode
2. Go has generics and interfaces which can help in making data structure adaptable to any kind of data

In other words we can decouple the data structure itself from the kind of data it holds 
-->

---
transition: fade-out
layout: lblue-fact
---

Games help you develop skills
<v-click>
<div class="font-size-8">and Go makes game development easy</div>
</v-click>

---
transition: fade-out
---

# Go and Graphs: basic concepts

Vertex

A vertex is a node that is holding data, for simplicity we will have it comparable

```go
type Vertex[T comparable] struct { 
  E T 
}
```

For Ticket to Ride a vertex is a train station which translates into

```go
type City string
// that will be instantiated in
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}
```

---
transition: fade-out
---

# Go and Graphs: basic concepts

Edge

An edge is a pair of vertices that can hold any property

```go
type Edge[T comparable] struct {
	X, Y *Vertex[T]
	P    EdgeProperty
}
type EdgeProperty any
```

For Ticket to Ride an edge is a railway link between two cities

```go
type City string
// Edge
type TrainLine Edge[City]
// EdgeProperty to attach to the P field in the Edge
type TrainLineProperty struct {
	Distance int
}
// that will be instantiated in
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}
newYorkWashington := Edge[City]{X: &newYork, Y: washington, P: TrainLineProperty{Distance: 2}}
```

---
transition: fade-out
---

# Go and Graphs: basic concepts

Graph

A graph then is a collection of edges and vertices

We can represent a graph in several ways but they share a set of common behaviors

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

One concrete representation of a graph is the list of vertices and edges:

```go
type ArcsList[T comparable] struct {
	v        []*Vertex[T]
	e        []*Edge[T]
}
// that for a ticket to ride board will be will be instantiated in
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}
newYorkWashington := Edge[City]{X: &newYork, Y: washington, P: TrainLineProperty{Distance: 2}}
// ...
board := ArcsList[City]{
  v: []*Vertex[City]{ &newYork ,&washington, ... }
  e: []*Vertex[Edge]{ &newYorkWashington, ... }
}
```

Other representations are 

- Adjacency lists
- Adjacency matrices
- Incidence lists
- Incidence matrices

---
transition: fade-out
---

# Go and Graphs: algorithms

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

# Gameplay

How does a player selects the next move

Let's see what happens in the random case:

Execution of a random game

---
transition: fade-out
---

# Gameplay

How does a player selects the next move

Let's use the knowledge we take from the graph to better select the line to occupy

Execution of a game using the connectivity and shortest path algoritms

---
transition: fade-out
---

# Next steps

How can the gameplay improve with the algorithms we have at our disposal?

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

Go and Games go well together

<v-click>

Games are a good opportunity to practise and learn new aspects of Go

Go makes it easy to have a minimun example of gameplay working

</v-click>

<v-click>Go makes it easy to be able to implement algorithms</v-click>

<v-click>

There are other examples of developing games in Go:

- Daniela Petruzalek's talks [Building an Indie Game in GO](https://www.youtube.com/watch?v=Oce77qCXu7I) and [Pacman from scratch](https://www.youtube.com/watch?v=SM8LTMnB4x0);
- Drishti Jain's talk [Go Beyond the Console: Developing 2D Games in Go](https://www.youtube.com/watch?v=OBKULmYQbuU);
- https://github.com/mcaci/wallrush to check
</v-click>

<v-click>

__Take advantage of the simplicity that Go brings you__
</v-click>

---
layout: fact
transition: fade-out
class: "font-size-7.8"
---

And it will enable us to implement algorithms faster and more reliably!

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
<img src="/images/michelecaciQR.jpeg" class="absolute bottom-5 right-5 text-right" style="width: 20%; height: auto;"/>