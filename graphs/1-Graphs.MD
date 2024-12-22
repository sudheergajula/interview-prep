# Graphs

Graph contains set of vertices (V) and edges representing the relationship between these vertices.

```java
public static class Graph<T> {
    public static class Vertex<T> {
        T data;

        Vertex(T data) {
            this.data = data;
        }
    }

    public static class Edge {
        Vertex<T> src;
        Vertex<T> dst;
        int cost;

        Edge(Vertex<T> src, Vertex<T> dst, int cost) {
            this.src = src;
            this.dst = dst;
            this.cost = cost;
        }
        Edge(Vertex<T> src, Vertex<T> dst) {
            this.src = src;
            this.dst = dst;
            this.cost = 0;
        }
    }

    public static <T> Vertex<T> createVertex(T data) {
        return new Vertex<>(data);
    }

}

```
## Undirected Graphs
A graph in which edges do not have any direction.

```java
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;

public static class Graph<T> {

    Map<Vertex<T>, List<Edge<T>>> adjList = new HashMap();

    public void addEdge(Vertex<T> src, Vertex<T> dst) {
        Edge fromTo = new Edge(src, dst);
        Edge toFrom = new Edge(dst, src);
        //This is to create a bidirectional connection between both the vertices
        adjList.computeIfAbsent(src, new LinkedList<>()).add(fromTo);
        adjList.computeIfAbsent(dst, new LinkedList<>()).add(toFrom);
    }

}
```

## Directed Graphs
Directed graph has an directed edge for vertices. This will be uni-directional.

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.List;

public static class Graph<T> {

    Map<Vertex<T>, List<Edge<T>>> incomingNodes = new HashMap<>();
    Map<Vertex<T>, List<Edge<T>>> outgoingNodes = new HashMap<>();
    Set<Vertex<T>> vertices = new HashSet<>();
    Set<Vertex<T>> startNodes = new HashSet<>();

    public static class Vertex<T> {
        T data;

        Vertex(T data) {
            this.data = data;
        }
    }

    public static class Edge<T> {
        Vertex<T> src;
        Vertex<T> dst;
        int cost;

        Edge(Vertex<T> src, Vertex<T> dst, int cost) {
            this.src = src;
            this.dst = dst;
            this.cost = cost;
        }

        Edge(Vertex<T> src, Vertex<T> dst) {
            this.src = src;
            this.dst = dst;
            this.cost = 0;
        }
    }

    public static <T> Vertex<T> createVertex(T data) {
        return new Vertex<>(data);
    }

    public void addEdge(Vertex<T> src, Vertex<T> dst) {
        Edge<T> fromTo = new Edge<>(src, dst);
        Edge<T> toFrom = new Edge<>(dst, src);
        // This is to create a bidirectional connection between both the vertices
        outgoingNodes.computeIfAbsent(src, v -> new LinkedList<>()).add(fromTo);
        incomingNodes.computeIfAbsent(dst, v -> new LinkedList<>()).add(toFrom);
        vertices.add(src);
        vertices.add(dst);
    }

    public Set<Vertex<T>> startNodes() {
        for (Map.Entry<Vertex<T>, List<Edge<T>>> m : outgoingNodes.entrySet()) {
            if (m.getValue().isEmpty()) {
                startNodes.add(m.getKey());
            }
        }
        return startNodes;
    }

}
```

## Graph Traversals

### <ins>BFS</ins>

```java


import java.util.ArrayDeque;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;

class BFS {
    Map<Vertex<T>, List<Edge<T>>> outgoingNodes = new HashMap<>();

    public void bfs(Vertex<T> root) {

        if (root == null) return;

        Set<Vertex<T>> seen = new HashSet<>();
        Queue<Vertex<T>> queue = new ArrayDeque<>();
        List<Vertex<T>> traversal = new LinkedList<>();

        queue.add(root);
        seen.add(element);

        while (!queue.isEmpty()) {
            Vertex<T> element = queue.poll();
            if (element == null) continue;

            traversal.add(element);

            List<Edge<T>> edges = this.outgoingNodes.get(element);
            for (Edge e : edges) {
                if (!seen.contains(e.dst)) {
                    queue.add(e.dst);
                    seen.add(e.dst);
                }
            }
        }

    }
}

```

### <ins>DFS</ins>

```java


import java.util.ArrayDeque;
import java.util.HashSet;
import java.util.Queue;

class DFS {
    Map<Vertex<T>, List<Edge<T>>> outgoingNodes = new HashMap<>();

    public void dfs(Vertex<T> root, Set<Vertex<T>> visited) {

        if (root == null) return;

        // This is very important
        if (visited.contains(root)) return;

        visited.add(root);
        List<Edge<T>> edges = this.outgoingNodes.get(root);
        if (null==edges || edges.isEmpty()) return;

        for (int i = 0; i < edges.size(); i++) {
            dfs(edges.get(i), visited);
        }

    }
}

```
