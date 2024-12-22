# Eulerian Path and Eulerian Graph/Curcuit

What is Eulerian Path?
Path in a graph that visits all vertices exactly and there exists a path between every pair of vertices(u,v) and (v,u)

What is Eulerian Cirucit/Graph?
An Eulerian Graph/Circuit is an Eluerian path that starts and ends at the same vertex.

## Eulerian Path for Undirected Graphs

- All the nodes have equal in-degree and out-degree
- Start and end with the same vertex

```java
/*
 *    (A) --- (B)
 *    |        |
 *    |        |
 *    (C) --- (D)
 * has in-degree
 A = 2
 D = 2
 C = 2
 D = 2
 */

public class EulerianUndirected {
    private final Map<Node<T>, List<Edge<T>>> map = new HashMap<>();

    public void addEdge(Node<T> src, Node<T> dst, int cost) {
        Edge<T> e1 = new Edge<>(src, dst, cost);
        Edge<T> e2 = new Edge<>(dst, src, cost);
        lookup.add(src);
        lookup.add(dst);
        map.computeIfAbsent(src, (k) -> new LinkedList<>()).add(e1);
        map.computeIfAbsent(dst, (k) -> new LinkedList<>()).add(e2);
    }

    public int isEulerian() {

        Map<Node, Integer> degreeMap = new HashMap<>();
        int oddNodes = 0;
        for (Map.Entry<Node<T>, List<Edge<T>>> entry : this.map.entrySet()) {
            degreeMap.put(entry.getKey(), entry.getValue().size());
            if (entry.getValue().size() % 2 != 0) {
                oddNodes++;
                System.out.printf("odd node %s:%s%n", entry.getKey(), entry.getValue().size());
            }
        }
        return oddNodes > 0 && oddNodes <= 2 ? 0 : oddNodes > 2 ? -1 : 1;
    }
}
```

### Semi Eulerian

- Start vertex is not equal to end vertex. Once we start traversal, we may not end in the same vertex.
- At most 2 nodes have odd in-degree

```java
/*
 *  (A) -- (B) -- (C) -- (D)
 * has in-degree
 A = 1
 D = 1
 C = 2
 D = 2
 */
```

## Eulerian Path for Directed Graphs

#### Eulerian circuit 
Every vertex has equal In-degree and Out-degree. And all of them have to be **strongly connected**.

#### Eulerian path 
At most one vertex has (outdegree) - (indegree) = 1 and at most one vertex has (indegree) - (outdegree) = 1, and all other vertices have equal in and outdegrees.

```java
class EulerianDirected {
    private final Map<Vertex<T>, List<Edge<T>>> incomingNodes = new HashMap<>();
    private final Map<Vertex<T>, List<Edge<T>>> outgoingNodes = new HashMap<>();

    public int isEulerian() {

        Map<Node, Integer> degreeMap = new HashMap<>();
        int in = 0;
        int out = 0;
        for (Map.Entry<Node<T>, List<Edge<T>>> entry : this.incomingNodes.entrySet()) {
            int diff = entry.getValue().size() - this.outgoingNodes.get(entry.getkey());
            if (diff > 0) in++;
            if (diff < 0) out++;
        }
        return in <= 1 && out <= 1;
    }
}
```

