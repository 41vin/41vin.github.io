---

---



  A network is commonly modeled as a graph, a structure consisting of vertices (or nodes) and edges (or links) between the vertices.

  A graph is denoted $G = (V, E)$, where V is the set of vertices and E the set of edges. In practical applications, we will mostly use the terms nodes and links, since these more closely reflect the physical components of a network.



# Basic Properties

![image-20231221120620122](/Users/mcl/Desktop/Project/Geon/Portfolio/41vin.github.io/assets/img/Network_Modeling_and_Analysis/image-20231221120620122.png)

  The number $n = |V|$ of nodes is called the *order* of the graph, and the number $m = |E|$ of links is its size. The links can be thought of as carrying a flow of some commodity between nodes, or more generally as symbolizing a relationship between nodes.

- Directed: one direction
- Undirected: two directions



For any vertex, the number of edges originating from it is the *degree* of the vertex.

---

# Graph Representation

  There are various ways to represent a graph. A particularly convenient representation is the n x n *adjacency matrix* A, which also can be used in algebraic equations. The adjacency matrix is the most commonly used graph representation in applications.

  Suitable for directed graphs, is the vertex-edge n × m incidence matrix J where an entry (v, e) is a positive one in the position of the origin of the directed edge, a negative one in the position of its termination, and zeros elsewhere.

  This matrix is useful in representing many flow problems since each row represents a vetex's incoming and outgoing edges.

  