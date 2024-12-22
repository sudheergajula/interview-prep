# Cycle Detection in Directed A Cyclic Graph

## DFS Approach

```java
import java.util.List;

/**
 * no cycle
 * 1 -->  2 --> 3 --> 4 --> 5 --> 6 
 *                    |             \ 
 *                    |             10
 *                    V             /
 *                    7 --> 8 --> 9 
 *                    
 *  cycle
 * 1 -->  2 --> 3 --> 4 --> 5 --> 6 
 *                    /\          |      
 *                    |           |      
 *                    |           V  
 *                    7   <--     8  
 */
class Graph {
    Map<Vertex<T>, List<Edge<T>>> outgoingNodes = new HashMap<>();

    public boolean cycleDetectionUsingDfs(Vertex<T> root, List<Vertex<T>> nodesInPath) {
        if (root == null) return false;

        if (containsInPath(root, nodesInPath)) {
            return true;
        }
        nodesInPath.add(root);
        List<Edge<T>> edges = outgoingNodes.get(root);
        for (Edge e : edges) {
            //If either of them detects cycle in rec stack return true;
            //remember this, its bitwise OR operation.
            result |= cycleDetectionUsingDfs(e.dst, nodesInPath);
            //Backtrack and remove the node, any other node in a path can have different route.
            //No-Cycle
            // 1->2->3->4->5->6->10
            // 1->2->3->4->7->8->9->10
            // 4 is already in path, but doesn't create cycle.
            
            //Cyclic
            //1->2->3->4->5->6->8->7>4
            nodesInPath.removeLast();
        }
        return result;
    }
}

```

## BFS Approach
### Using Khan's algorithm - also does topological sorting as well

```java
import java.util.*;

public class Graph {
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

    public void detectCycle() {
        List<Vertex<T>> startNodes = new ArrayList<>(this.startNodes());
        List<Vertex<T>> result = new LinkedList<>();
        Queue<Vertex<T>> queue = new ArrayDeque<>();
        Map<Vertex<T>, Integer> inDegree = new HashMap<>();

        for (Vertex<T> e : vertices) {
            inDegree.put(e, incomingNodes.get(e) == null ? 0 : incomingNodes.get(e).size());
        }

        queue.add(startNodes);
        while (!queue.isEmpty()) {
            Vertex<T> ele = queue.poll();
            if (ele == null) continue;

            result.add(ele);

            List<Edge<T>> edges = this.outgoingNodes.get(ele);
            for (Edge<T> e : edges) {
                Vertex<T> dst = e.dst;
                Integer updated = inDegree.compute(e, (tVertex, integer) -> integer - 1);
                if (updated == 0) {
                    queue.add(dst);
                }
            }
        }
        if (result.size() != vertices.size()) {
            throw new IllegalStateException("Cycle detected");
        }
    }
}

```

