---
theme: seriph
background: /images/gopherAndRail.jpg
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
layout: intro
---

# About myself

<img src="/images/meAtCDS23.jpg" class="absolute top-15 right-25 text-right" style="width: 30%; height: auto;"/>

<v-clicks>

I am a proud Gopher since 2018

- with experiences in other programming languages too

I work (in Go) in Amadeus

- a company that creates applications for the travel industry

I like participating at conferences

- But on my free time I enjoy swimming, cooking, learning languages and playing board games
</v-clicks>

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
- The lines are railway lines that connect them

The resources are train tokens and colored cards that are spent to occupy railway lines

- For example to occupy the line between Miami and Atlanta you'll need to spend 5 trains tokens and 5 blue cards

The objective is to get the highest score

- By occupying railway lines
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
layout: lblue-fact
---

Let's see how to play the game in Go

---
transition: fade-out
---

# Idea #1

We Go random

<v-click>

Let's simplify the rules first:
</v-click>

<v-clicks>

- the number of player will be 2
- the railway link chosen by each player will be __random__
- each player has unlimited resources
  - which means that each player will take turns to select a link and occupy it
- each player has no objectives
  - which means that the final score will be determined by which lines they occupy

</v-clicks>

---
transition: fade-out
---

# Idea #1

Let's see the code

````md magic-move {lines: true}
```go {all|4-6|8-9|10-20|21|all}
package main

func main() {
	// Collect all the railway lines
	railwaylines, err := data.RailwayLines()
	if err != nil { /* log and exit */ }

	// create the two players
	p1, p2 := player.NewRandom(1), player.NewRandom(2)
	// use a coin to select the player who takes the turn and play until all lines are occupied
	var coin bool
	for game.FreeRoutesAvailable(railwaylines) {
		playRound := p1.Play()
		if !coin {
			playRound = p2.Play()
		}
		playRound(railwaylines)
		// pass the turn
		coin = !coin
	}
	slog.Info("end game", "Score P1", player.Score(p1), "Score P2", player.Score(p2))
}
```

```go {all|1-6|7|8-15|16-23|all}
package player

type Random struct {
	id    int
	owned []*game.TrainLine
}
func NewRandom(id int) *Random { return &Random{id: id} }
func (p *Random) Play() func(g game.Board) {
	return func(g game.Board) {
		// select and remove a random railway line from the board
		chosenLine := game.PopRandomLine(g)
		// add it to the owned list
		p.owned = append(p.owned, (*game.TrainLine)(chosenLine))
	}
}
// Score sums up the value of each owned railway line
func (p *Random) Score() int {
	var score int
	for i := range p.owned {
		score += p.owned[i].Value()
	}
	return score
}
```
````

<!-- 
And so we have our first gameplay example
-->

---
transition: fade-out
layout: fact
---

Demo time!

---
transition: fade-out
layout: lblue-fact
---

Let's focus on the board for one second

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
image: /images/TTR_USA_map_graph.jpg
backgroundSize: fit
---

---
transition: fade-out
layout: image-right
image: /images/aGraphToMeReallyYeah.jpeg
backgroundSize: 90%
---

# Idea #2

Let's model Ticket to Ride board as a graph

This is where we introduce graphs algorithms

<!-- But if graph algorithms look scary to you I have good news for you -->

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
layout: lblue-fact
---

Graphs Algorithms, Ticket To Ride and Go

---
transition: fade-out
---

# Vertices and Edges

How we can implement them in Go and how they translate in Ticket to Ride

<v-click>

````md magic-move {lines: true}
```go {none|1-4|5-10}
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

```go {1|2-5|7-10|11-12}
// A Ticket to Ride example
// We create city stations as vertices of our Ticket to Ride graph
type City string
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}

// We define a property for the Edge between city station vertices
type TrainLineProperty struct {
  Distance int
}
// We create a train line as an edge between two city station vertices
newYorkWashington := Edge[City]{X: &newYork, Y: &washington, P: TrainLineProperty{Distance: 2}}
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

```go {1-4|5-10|6,12-17|all}
// A Ticket to Ride example
newYork := Vertex[City]{E: "New York"}
washington := Vertex[City]{E: "Washington"}
newYorkWashington := Edge[City]{X: &newYork, Y: washington, P: TrainLineProperty{Distance: 2}}
// Keep adding cities (vertices) and railway lines (edges)
// And add all them to the board
board := ArcsList[City]{
  v: []*Vertex[City]{ &newYork ,&washington /*, ...*/ }
  e: []*Edge[City]{ &newYorkWashington /*, ...*/ }
}

// in other words the job that was done when collecting all the railway lines in the main
func main() {
	// ...
	railwaylines, err := data.RailwayLines()
	// ...
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

There are other graph representations.

The choice of the representation is based on memory and time efficiency with respect to the operations done.

All graph representations share a common behavior that can be captured by creating an interface.

```go
type Graph[T comparable] interface { 
  Vertices() []*Vertex[T]
  Edges() []*Edge[T]
  AddVertex(v *Vertex[T])
  RemoveVertex(v *Vertex[T])
  AddEdge(e *Edge[T])
  RemoveEdge(e *Edge[T])
  // ...
}
```

</v-clicks>

---
transition: fade-out
layout: lblue-fact
---

What algorithms do we need for Ticket to Ride?

<!-- 
Once we have a graph up representing the board of ticket to ride, we can start reasoning on it using the algorithms we have at our disposal

Let's see a few of them.
-->

---
transition: fade-out
layout: image-right
image: /images/TTR_USA_map.jpg
backgroundSize: 100%
---

# Is there a path connecting a city to another one?

Connected vertices in a graph

<v-clicks>

The game starts with all of the cities connected by the edges representing the railway lines

As soon as players occupy railway lines, the correspondent edge is removed from the graph

We are going to see the following algorithms to check if two cities are still connected by railway lines:

- __visit__ of a graph
- __connectivity__ of two vertices in a graph

</v-clicks>

---
transition: fade-out
---

# Is there a path connecting a city to another one?

Let's see the code

<v-clicks>

````md magic-move {lines: true}
```go {all|3|4-7,21|8-10,20|10-13,20|6,7,10,14-15,20,21|5,10,16-19,20,22|all}
// GenericVisit walks the graph from a source node, visiting each node it can visit only once
func GenericVisit[T comparable](g Graph[T], s *Vertex[T]) *Tree[T] {
	if !g.ContainsVertex(s) { return nil }
	s.Visit()
	t := &Tree[T]{element: &s.E}
	queue := []*Vertex[T]{s}
	for len(queue) != 0 {
		var next *Vertex[T]
		next, queue = queue[0], queue[1:]
		for _, v := range g.AdjacentNodes(next) {
			if v.Visited() {
				continue
			}
			v.Visit()
			queue = append(queue, v)
			parentNode := t.Find(&next.E)
			if subtree != nil {
				parentNode.children = append(parentNode.children, &Tree[T]{element: &v.E})
			}
		}
	}
	return t
}
```

```go {all|1-4|6-21|6,10,11|6,14-19|6,15|all}
type Tree[T comparable] struct {
	element  *T
	children []*Tree[T]
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
		i := slices.IndexFunc(t.children, func(t *Tree[T]) bool { return t.Find(e) != nil })
		if i < 0 {
			return nil
		}
		return t.children[i]
	}
}
```

```go
// func GenericVisit[T comparable](g Graph[T], s *Vertex[T]) *Tree[T]

// Connected verifies that the vertices x and y are connected in the graph g
// by visiting g using x as a source and checking that the output tree contains the vertex v
func Connected[T comparable](g Graph[T], x, y *Vertex[T]) bool {
	return GenericVisit(g, x).Find(&y.E) != nil
}
```
````
</v-clicks>

---
transition: fade-out
layout: image-right
image: /images/TTR_USA_map.jpg
backgroundSize: 100%
---

# Of all the routes connecting two cities, which one is the shortest?

Shortest path

<v-clicks>

If two cities are connected, there is at least one route between them

We are going to see the __Bellman-Ford algorithm__ to check which route is the shortest between two cities

</v-clicks>

---
transition: fade-out
---

# Bellman-Ford algorithm for the shortest path

Let's see the code


````md magic-move {lines: true}
```go {all|2-8|9-21|all}
func BellmanFordDistances[T comparable](g Graph[T], s *Vertex[T]) map[*Vertex[T]]*Distance[T] {
	d := make(map[*graph.Vertex[T]]*Distance[T]) // type Distance[T comparable] struct { v, u *Vertex[T]; d int }
	for _, v := range g.Vertices() {
		d[v] = &Distance[T]{v: s, u: v}
		if v != s {
			d[v].d = math.MaxInt
		}
	}
	canRelax := (x, y *graph.Vertex[T], w Weighter) bool { return d[x].d+w.Weight() < d[y].d && d[x].d+w.Weight() > 0 }
	relax    := (x, y *graph.Vertex[T], w Weighter) 	  { d[y].setDistance(w.Weight() + d[x].d)}
	for range g.Vertices() {
		for _, e := range g.Edges() {
			w := e.P.(Weighter) // type Weighter interface{ Weight() int }
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

```go {all|5-7,23|3,4,8-22|all}
func Shortest[T comparable](g graph.Graph[T], d map[*graph.Vertex[T]]*Distance[T], x, y *graph.Vertex[T]) []*graph.Vertex[T] {
	if len(g.Vertices()) < 2 { return nil }
	isShortestDist := func(u, v *graph.Vertex[T], w Weighter) bool { return d[u].d+w.Weight() == d[v].d }
	isConnectingEdge := func(u, v *graph.Vertex[T], e *graph.Edge[T]) bool { return (e.X == u && e.Y == v) || (e.X == v && e.Y == u) }
	path := []*graph.Vertex[T]{y}
	v := y
	for v != x {
	neighbourSearch:
		for _, u := range g.AdjacentNodes(v) {
			for _, edge := range g.Edges() {
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

# Quick note on why using Go for algorithm development

The power of simplicity

<v-clicks>

A common theme in Go is that its simplicity often hides the complexity that makes the language simple

It holds the same with algorithms, despite being complex themselves, few lines of code are necessary to implement them in Go

</v-clicks>

<!-- 
We have seen some of the syntax elements of Go like Generics, Interfaces and Functions as first class citizen have bubbled up quite often in the code snippets.
And the interesting part is that they contribute to make the code look almost like pseudo-code.
Don't get me wrong, I'm not comparing the two of them, but if you look at a textbook and see the pseudo-code for an algorithm,
Go provides all the necessary the tools to translate pseudocode into actual code.
Which leads me to my, probably, unpopular opinion.
-->

---
layout: lblue-fact
transition: fade-out
---

# It is very easy to translate pseudocode to Go

---
transition: fade-out
---

# Back to Idea #2

New rules

<v-clicks>

- the number of player will be 2
- each player has unlimited resources
  - which means that each player will take turns to select a link and occupy it
- each player has __3__ objectives
  - which means the railway link chosen by each player will be made by __looking at the shortest path__ available for the routes on their objective list

</v-clicks>

---
transition: fade-out
---

# Back to Idea #2

Let's see the code

````md magic-move {lines: true}
```go {none|4-6|8-9|10-20|21|all}
package main

func main() {
	// Collect all the railway lines
	railwaylines, err := data.RailwayLines()
	if err != nil { /* log and exit */ }

	// create the two players
	p1, p2 := player.NewRandom(1), player.NewRandom(2)
	// use a coin to select the player who takes the turn and play until all lines are occupied
	var coin bool
	for game.FreeRoutesAvailable(railwaylines) {
		play := p1.Play()
		if !coin {
			play = p2.Play()
		}
		play(railwaylines)
		// pass the turn
		coin = !coin
	}
	slog.Info("end game", "Score P1", player.Score(p1), "Score P2", player.Score(p2))
}
```

```go {none|1-6|7|8-15|16-23|all}
package player

type Random struct {
	id    int
	owned []*game.TrainLine
}
func NewRandom(id int) *Random { return &Random{id: id} }
func (p *Random) Play() func(g game.Board) {
	return func(g game.Board) {
		// select and remove a random railway line from the board
		chosenLine := game.PopRandomLine(g)
		// add it to the owned list
		p.owned = append(p.owned, (*game.TrainLine)(chosenLine))
	}
}
// Score sums up the value of each owned railway line
func (p *Random) Score() int {
	var score int
	for i := range p.owned {
		score += p.owned[i].Value()
	}
	return score
}
```
````

<!-- 
And so we have our first gameplay example
-->

---
transition: fade-out
layout: fact
---

Demo time!

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

<v-clicks>

Games are a good opportunity to practise and learn new aspects of Go

Go makes it easy to have a minimun example of gameplay working

Go makes it easy to be able to implement algorithms
</v-clicks>

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