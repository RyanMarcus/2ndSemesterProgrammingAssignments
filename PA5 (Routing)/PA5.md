# Brandeis University COSI 12B, Spring 2016 PA5

Due date: Feb 29th, 2016

##Overview
This assignment will give you practice with graphs. Turn in an Eclipse export of your working code. The assignment involves implementing code to compute the shortest route between two points.

##Background information
### Maps and sets

Two data structures you will find extremely useful for this assignment are [`Map`s](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html) and [`Set`s](https://docs.oracle.com/javase/8/docs/api/java/util/Set.html).

A `Map` represents a mapping between keys of type K to values of type V. The mapping is [surjective](https://en.wikipedia.org/wiki/Surjective_function), meaning that every entry of type K has exactly one entry of type V. However, the converse is not true: a mapping may map two different keys to the same value.

Maps can be thought of as a basic "key/value" data structure, meaning that they maintain a list of `(key, value)` pairs, and it allows you to quickly look up the `value` associated with any given `key`.

Here's an example usage of `Map`:

```
Map<String, Integer> m = new HashMap<String, Integer>();

m.put("One", 1);
m.put("Two", 2);
m.put("Too", 2);

System.out.println(m.get("One"));
System.out.println(m.get("Two") + " or " + m.get("Too"));

```

You can [run the example yourself](https://repl.it/BoMD/0) if you'd like. We create an instance of a `HashMap` class (which implements the `Map`) interface, and then we associate the key "One" with the value 1, the key "Two" with the value 2, and the key "Too" with the value 2 as well. We then retrieve elements of the `Map` via `Map::get`, which retrieves the value corresponding to the passed key.

Because a map is surjective, we can only map a key to one value. Consider the code below:


```
Map<String, Integer> m = new HashMap<String, Integer>();

m.put("alpha", 1);
System.out.println(m.get("alpha"));
m.put("alpha", 2);
System.out.println(m.get("alpha"));

```

This code will print a `1` followed by a `2`. The second `Map::put` call replaces the previous value associated with the key `alpha`.

Java also comes with an implementation of `Set`s, which are data structures that store elements in an unordered fashion. You can insert elements into sets, remove elements from sets, iterate over all the elements in a set, or check if a particular object is inside of a set. [You can read more about sets in Java here](https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html). 



### Graphs
In computer science, we often represent problems as [graphs](https://en.wikipedia.org/wiki/Graph_(discrete_mathematics)).

![graph](http://cs.brandeis.edu/~rcmarcus/cs12b/graph6.png)
\ 

In general, when a computer scientist talks about a graph, they aren't talking about a bar chart or a line graph. Instead, a graph `G` is a set of vertices `V` and edges `E` which compose a connected structure like the one above. Graphs can be used to represent problems like package routing, traffic planning, and simple shortest-path navigation.

Representing a graph in the computer's memory can be done in a few different ways. The most popular way, and the way we recommend using for the assignment, is an [adjacency list](https://en.wikipedia.org/wiki/Adjacency_list). For each vertex, we store a list of a connected edges. For the graph depicted above, such a list might look like:
 
  V     Connected To
-----  --------------
  1          2, 4
  2         1,3,4,5
  3           2, 5
  4         1, 2, 5
  5         2, 3, 4

We can use the adjacency list to answer questions like "what is vertex 2 connected to?" and "are vertex 3 and vertex 4 connected?" This representation also tends to be fairly compact, although some data is replicated. Since we know our graph is *undirected*, if we know there is an edge between `1` and `2`, then we know there is also an edge between `2` and `1`. We store this edge twice, which isn't the worst thing in the world, but is an opportunity for optimization.

There are a lot of things we can do with graphs. One of the most obvious, **routing**, is the task of finding a route or path between two points on the graph. Often, we want to find the **shortest path** between two points, meaning a list of vertices connected to each other with the shortest sum of edge lengths.

Another task, **minimum spanning trees**, involves finding the fewest number of edges that can be selected to connect all the nodes in a graph. For example, if Brandeis University wanted to minimize their snow plowing so that everyone could still get to every point, they could compute a minimum spanning tree of campus, and only plow the edges in that tree. One possible minimum spanning tree for the graph above would be the edges `(1, 2)`, `(2, 4)`, `(2, 5)`, `(2, 3`). With just these edges, you can get from any node to every other node, so those set of edges *span* the graph. The sum of their weights is also minimal (there is no other tree with a smaller edge sum), so the set of edges is *minimal*. When a set of edges is both minimal and spanning, it is a minimum spanning tree.

Let's use some math terms to express this simple concept in a confusing but compact notation. Say that a graph $G$ is made up of a set of vertices $V$ and a set of edges $E$, so we have $G = (V, E)$. The edge connecting `2` and `4` would be expressed as $(2, 4) \in E$. Notice that on an undirected graph we have the following axiom:

$$(v, u) \in E \to (u, v) \in E$$

This simply states that if `v` is connected to `u`, then `u` is connected to `v`.

We can use this notation to formalize the concept of a shortest route as well. Let a *route* $R$ be a list of vertices such that for any $v_i$ and $v_{i+1}$, $(v_i, v_{i+1}) \in E$. In other words, each vertex in the list is connected to the next vertex by an edge. Formally, a *route* $R$ from $a$ to $b$ is a list such that:

$$\forall v_i \in R (v_i = b \lor (v_i, v_{i+1}) \in E) \land \exists v_i \in R (i = 0 \land v_i = a)$$

In English, this says that (1) all vertices in the route must be the endpoint of the route or must be connected by an edge to the next vertex in the route, and (2) there exists a vertex in the route that is the first vertex in the route and is equal to the start vertex. Seems pretty obtuse, right? But there it is.


If we let **R(a, b)** represent the set of all possible routes from $a$ to $b$, then we can define the *shortest route* $R$ from $a$ to $b$ as:

$$\{R \in \mbox{\bf R(a, b)} \mid \lnot \exists \rho \in \mbox{\bf R(a, b)}(\rho \neq R \land \sigma(rho) < \sigma(R)) \}$$

... where $\sigma$ is defined as the path length:

$$\sigma(R) = \sum_{v_i \in R} weight(v_i, v_{i+1}) \mbox{(except for the end)}$$

... where $weight(a, b)$ is the weight of the edge connecting $a$ and $b$.


In other words, the shortest route $R$ from $a$ to $b$ is a route between $a$ and $b$ such that there is no other route between $a$ and $b$ with a length shorter than $R$.

So you might be thinking: yikes! Why would I ever want to express an idea like this? And the answer is that the notation used above provides a very *concise* and a very *exact* formulation. They leave very little open for interpretation, but at the cost of making any interpretation quite difficult. For this assignment, you won't need to know the mathematical notation, you'll just need to understand the concepts. In life, however, and in your career at Brandeis, you'll likely become comfortable with this notation. It might even appear on an exam.

I won't bother formalizing a minimum spanning tree, but I think you can imagine what it might look like: a set of edges such that there exists a route between each edge and such that the total sum of the weight of the edges is minimized.

## Your task

### The `Graph` class

The first thing you need to do is implement the `Graph` class. It will be constructed using a string that you will need to parse into some internal representation. Then, based on this internal representation, you will need to implement the following methods:

  * `getEdgeWeight(String a, String b)`: returns the weight of the edge between vertices `a` and `b`, if one exists. Otherwise, returns -1.
  * `getConnectedEdges(String s)`: returns an iterable over the vertices connected to the passed-in vertex.
  * `getPathLength(Iterable<String> i)`: returns the length of the path in the graph of going through the vertices specified in the iterable.
  * `getVertices()`: returns an iterable over all the vertices in the graph.


The constructor of the `Graph` object should take in a string representation of a graph that looks like this:

    graph g {
        "a" -- "b" [ label = "10" ];
        "b" -- "c" [ label = "20" ];
    }

You should parse it in a way that is *agnostic to whitespace*, i.e. the graph above should be parsed to the same thing as this graph:


    graph g    {
        "a" --       "b" [ label = "10" ];
	  "b" -- "c" [  label = "20"];
      }

See the test cases for more examples.

### The `Router` class

The second thing you'll need to implement is the `Router` class. This class has two methods:

  * `Router::getRoute(String start, String end)`: returns an `Iterable` over vertexs representing a valid route between the given start vertex and the given ending vertex. For example, if `getRoute("1", "5")` was called with the graph above, a valid Iterable would return `["1", "4", "5"]` or `["1", "2", "5"]`, or some other valid route. You may not visit the same node twice on your route.
  * `Router::getShortestRoute(String start, String end)`: returns an `Iterable` over the vertexes representing the shortest valid route (the route with the lowest sum of edge weights) between the start and the end. For example, `getShortestRoute("1", "5")` should return an Iterable over `["1", "2", "5"]`. Clearly, if you implement this method first, the `getRoute` method will be trivial.


For `getShortestRoute`, you'll probably want to implement [Dijkstra's algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm), an extremely important algorithm in computer science. If you implement `getShortestRoute` first, you can just call it from `getRoute`.

### The `SpanningTree` class

Finally, you'll need to implement some methods in the `SpanningTree` class. This class will find minimum spanning trees of a graph. There is only one method you need to implement:

  * `getMST(Graph g)`: returns an Iterable over `Edge`s making up the minimum spanning tree of the graph.

The `Edge` class is a [nested class](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html) inside of the `SpanningTree` graph that has three properties that are publicly exposed. Such classes are occassionally called [POJOs (plain old Java objects)](https://en.wikipedia.org/wiki/Plain_Old_Java_Object). You can add methods, constructors, or anything else you'd like to the `Edge` class as long as you leave `vertex1`, `vertex2`, and `weight` public and set them properly. Generally, we would create getters and setters (`getWeight`, `setWeight`) and keep the class fields private. You can read [more about when you should do this on StackOverflow](http://programmers.stackexchange.com/questions/21802/when-are-getters-and-setters-justified).

To implement the minimum spanning tree, you'll probably want to use [Kruskal's algorithm](https://en.wikipedia.org/wiki/Kruskal%27s_algorithm), another classic CS algorithm. In order to use it, you might need to implement a [Union-find data structure](https://en.wikipedia.org/wiki/Disjoint-set_data_structure) as a seperate class. The details of the implementation are left up to you. Here's a YouTube video that shows you how to use [Kruskal's algorithm in two minutes](https://www.youtube.com/watch?v=71UQH7Pr9kU), and here's [a more in-depth video](https://www.youtube.com/watch?v=5xosHRdxqHA). Here's [another](https://www.youtube.com/watch?v=fAuF0EuZVCk) recommend by one of your TAs.

## A note on tests...
Your graph, router, and `SpanningTree` can be tested with a provided set of **non-exhaustive** unit tests. That means that if your code fails the tests, it is definitely wrong. The converse is not true: code that can pass the tests is not necessarily correct code.  **Passing the unit tests is not enough to ensure a good score**. You should test your code significantly more on your own. Do not attempt to hard code, corner-case, or otherwise overfit your solution to the tests. We have provided you a subset of the tests that we will use for grading.

**In this assignment, the tests are *far* from exhaustive. I highly recommend you investigate edge cases like empty graphs, graphs without routes between two points, etc.**



##Style guidelines
Follow stylistic guidelines about removing redundancy, using proper data types, indentation, whitespace, identifier names, and commenting at the beginning of your program, on each method, and on complex sections of code.


##Submission
Your exported Eclipse project should be submitted on LATTE. For late policy check the syllabus.

##Grading
You will be graded on:

  * External Correctness: The output of your program should match exactly what is expected. Programs that do not compile will not receive points for external correctness.
  * Internal Correctness:  Your source code should follow the stylistic guidelines shown in class. Also, remember to include the comment header at the beginning of your program.
  * One-on-one interactive grading: By the end of the day that the assignment is due, please make an appointment with your TA for an interactive 10-15 minute grading session. You will receive an email notifying you of the name of the TA who has been assigned to you for this assignment with further instructions on setting up the appointment. (You will be meeting with a different TA for each assignment). One-on-one interactive grading will help you improve your programming skills and avoid repeating mistakes from one assignment to the next. 



# Terms and Conditions

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Cosi12b Assignments</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="http://rmarcus.info" property="cc:attributionName" rel="cc:attributionURL">Ryan Marcus</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
