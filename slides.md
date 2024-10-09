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
layout: intro
---

# About myself

<img src="/images/meAtCDS23.jpg" class="absolute top-15 right-25 text-right" style="width: 30%; height: auto;"/>

I am a Gopher since 2018

- but also have experiences in other programming languages

My company is Amadeus

- my team that creates middleware tools in Go that help configure applications deployed in the cloud

I speak at conferences

- mostly about Go and cloud topics

On my free time I enjoy swimming, cooking, learning languages and playing board games

- and today we're going to see one in action

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

Modeling Ticket to Ride as a Graph

Thanks to this property we can use graph algorithms to have our players make better choices

But if graph algorithms look scary to you I have good news for you

---
transition: fade-out
layout: image
image: /images/goodNewsAlgorithmsAreEasy.jpg
backgroundSize: fit
---

<!-- 
Let's see how Go really makes the implementation of graph algorithms easy

There are two elements that stand out:

1. Go can easily be written line by line from pseudocode
2. Go has generics and interfaces which can help in making data structure adaptable to any kind of data

In other words we can decouple the data structure itself from the kind of data it holds 
-->

---
transition: fade-out
layout: section
---

# Graphs in Go: basics blocks

---
transition: fade-out
---

# Vertices and Edges

How we can implement them in Go and how they translate in Ticket to Ride

<v-click>

````md magic-move {lines: true}
```go {none|1-4|5-10|all}
// A vertex is a node that is holding data, for simplicity we will have it comparable
type Vertex[T comparable] struct { 
  E T 
}
// An edge is a pair of vertices that can hold any property
type Edge[T comparable] struct {
	X, Y *Vertex[T]
	P    EdgeProperty
}
type EdgeProperty any
```

```go {1|2-3|2-6|8-11|12-13|all}
// Let's see an example for Ticket to Ride
// We first make an alias of a City from a string (optional)
type City string
// We create city stations as vertices of our Ticket to Ride graph
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}

// We define a property for the Edge between city station vertices
type TrainLineProperty struct {
  Distance int
}
// We create a Train line as an edge between two city station vertices
newYorkWashington := Edge[City]{X: &newYork, Y: washington, P: TrainLineProperty{Distance: 2}}
```
````
</v-click>

---
transition: fade-out
---

# Graphs

How we can implement them in Go and how they translate in Ticket to Ride

````md magic-move {lines: true}
```go
// ArcsList is graph representation of a collection of edges and vertices
type ArcsList[T comparable] struct {
	v        []*Vertex[T]
	e        []*Edge[T]
}
```

```go {1-4|5-10|all}
// Let's see the example for Ticket to Ride
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}
newYorkWashington := Edge[City]{X: &newYork, Y: washington, P: TrainLineProperty{Distance: 2}}
// Keep adding cities (vertices) and railway links (edges)
// And add all them to the board
board := ArcsList[City]{
  v: []*Vertex[City]{ &newYork ,&washington /*, ...*/ }
  e: []*Vertex[Edge]{ &newYorkWashington /*, ...*/ }
}
```

```go
// ArcsList is graph representation of a collection of edges and vertices
type ArcsList[T comparable] struct {
	v        []*Vertex[T]
	e        []*Edge[T]
}
```
````

<v-clicks>

Other graph representations are based on the adjacency or incidence and the choice of which one to use is based on the memory and time to spend on the graph representation and the different algorithms.

However all graph representations have share a common set of behaviors so you can easily change which representation to use

```go
// Graph is interface that defines all the common behavior that any graph representation should have
type Graph[T comparable] interface { 
  Vertices() []*Vertex[T]
	Edges() []*Edge[T]
	AddVertex(v *Vertex[T])
	RemoveVertex(v *Vertex[T])
	ContainsVertex(v *Vertex[T]) bool
	AddEdge(e *Edge[T])
	RemoveEdge(e *Edge[T])
	ContainsEdge(e *Edge[T]) bool
  // ...
}
```

To have the `ArcsList` structure represent a graph it will be appropriate to implement the methods in the interface
</v-clicks>

---
transition: fade-out
layout: section
---

# From basics to algorithms

<!-- 
Once we have a graph up representing the board of ticket to ride, we can start reasoning on it using the algorithms we have at our disposal

Let's see a few of them.
-->

---
transition: fade-out
---

# Connected components in a graph

Is there a path connecting a city station to another one?

<v-clicks>

At the beginning all of the cities are connected
  
As soon as we occupy railway links and remove the correspondent edges from the board this may not be the case anymore

````md magic-move {lines: true}
```go {all}
// Generic is a generic visit of a graph starting from the vertex s
func Generic[T comparable](g Graph[T], s *Vertex[T]) *Tree[T] {
	if !g.ContainsVertex(s) {
		return nil
	}
	s.Visit()
	t := &Tree[T]{element: &s.E}
	f := []*Vertex[T]{s}
	for len(f) != 0 {
		var u *Vertex[T]
		u, f = f[0], f[1:]
		for _, v := range g.AdjacentNodes(u) {
			if v.Visited() {
				continue
			}
			v.Visit()
			f = append(f, v)
			tree := t.Find(&u.E)
			if tree != nil {
				tree.children = append(tree.children, &Tree[T]{element: &v.E})
			}
		}
	}
	return t
}
```

```go {all}
type Tree[T comparable] struct {
	element  *T
	children []*Tree[T]
}

func (t *Tree[T]) Size() int {
	switch {
	case t == nil, t.element == nil:
		return 0
	case t.children == nil:
		return 1
	default:
		size := 1
		for i := range t.children {
			size += t.children[i].Size()
		}
		return size
	}
}

func (t *Tree[T]) Find(e *T) *Tree[T] {
	switch {
	case t == nil, t.element == nil:
		return nil
	case *t.element == *e:
		return t
	case t.children == nil:
		return nil
	default:
		var tree *Tree[T]
		for i := range t.children {
			tree = t.children[i].Find(e)
			if tree == nil {
				continue
			}
			return tree
		}
		return nil
	}
}
```

```go
func Connected[T comparable](g Graph[T]) bool {
	return len(g.Vertices()) == Generic(g, g.Vertices()[0]).Size()
}
```
````
</v-clicks>

---
transition: fade-out
---

# Shortest path

If there are multiple paths connecting a city station to another one, which one is the shortest?

````md magic-move {lines: true}
```go {all}
// Distance between two vertices
type Distance[T comparable] struct {
	v, u *Vertex[T]
	d    int
}

type Weighter interface{ Weight() int }

func BellmanFordDist[T comparable](g Graph[T], s *Vertex[T]) map[*Vertex[T]]*Distance[T] {
	d := make(map[*Vertex[T]]*Distance[T])
	vs := g.Vertices()
	for i := range vs {
		switch v := vs[i]; v {
		case s:
			d[v] = &Distance[T]{v: s, u: v, d: 0}
		default:
			d[v] = &Distance[T]{v: s, u: v, d: math.MaxInt}
		}
	}
	canRelax := func(x, y *Vertex[T], w Weighter) bool {
		return d[x].d+w.Weight() < d[y].d && d[x].d+w.Weight() > 0
	}
	relax := func(x, y *Vertex[T], w Weighter) {
		d[y].setDistance(w.Weight() + d[x].d)
	}
	es := g.Edges()
	for range vs {
		for _, e := range es {
			w := e.P.(Weighter)
			switch {
			case canRelax(e.X, e.Y, w):
				relax(e.X, e.Y, w)
			case canRelax(e.Y, e.X, w):
				relax(e.Y, e.X, w)
			}
		}
	}
	return d
}
```

```go {all}
func Shortest[T comparable](g graph.Graph[T], d map[*graph.Vertex[T]]*Distance[T], x, y *graph.Vertex[T]) []*graph.Vertex[T] {
	if len(g.Vertices()) < 2 {
		return nil
	}
	path := []*graph.Vertex[T]{y}
	v := y
	isShortestDist := func(u, v *graph.Vertex[T], w Weighter) bool { return d[u].d+w.Weight() == d[v].d }
	isConnectingEdge := func(u, v *graph.Vertex[T], e *graph.Edge[T]) bool {
		return (e.X == u && e.Y == v) || (e.X == v && e.Y == u)
	}
	es := g.Edges()
	for v != x {
	neighbourSearch:
		for _, u := range g.AdjacentNodes(v) {
			for _, edge := range es {
				if !isConnectingEdge(u, v, edge) {
					continue
				}
				if !isShortestDist(u, v, edge.P.(Weighter)) {
					continue
				}
				path = append([]*graph.Vertex[T]{u}, path...)
				v = u
				break neighbourSearch
			}
		}
	}
	return path
}
```
````

---
transition: fade-out
---

# Benefits of using Go

While developing algorithms

It takes minimal Go code to translate from pseudocode, making Go an easy choice to implement them quickly

Generics make the introduction of data structures agnostic to the type of data they hold
- it is easy to have a `Graph[string]` or a `Graph[int]` or `Graph[Person]`

"Plug-in" interface help in separating the basic data structure from a similar one with richer information
- For example for the shortest distance algorithm it didn't matter what was inside the EdgeProperty we used as long as we could define a `Weight()` method on it

---
transition: fade-out
layout: section
---

# From algorithms to gameplay

---
transition: fade-out
---

# Gameplay

Let's use the knowledge we gained to better select the line to occupy

Execution of a game using the connectivity and shortest path algoritms

---
transition: fade-out
---

# Next steps

This is just the start of a meaningful game.

How can the gameplay improve with other algorithms we have at our disposal?

Can we tap out to other algorithm fields?

---
transition: fade-out
layout: image-right
image: /images/Gophers1.jpeg
backgroundSize: 80%
---

# Conclusions

Improving in Go with Games

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

And you'll be able to create awesome things in Go!

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