# Network delay time
## https://leetcode.com/problems/network-delay-time

You are given a network of n nodes, labeled from 1 to n. You are also given times, a list of travel times as directed edges times[i] = (ui, vi, wi), where ui is the source node, vi is the target node, and wi is the time it takes for a signal to travel from source to target.

We will send a signal from a given node k. Return the time it takes for all the n nodes to receive the signal. If it is impossible for all the n nodes to receive the signal, return -1.

![Network delay time](example.JPG?raw=true)

## Constraints :
```
1. 1 <= k <= n <= 100
2. 1 <= times.length <= 6000
3. times[i].length == 3
4. 1 <= ui, vi <= n
5. ui != vi
6. 0 <= wi <= 100
7. All the pairs (ui, vi) are unique. (i.e., no multiple edges.)
```


# Implementation 1 : Dijkstra's Algorithm
```java
class Solution {
    Map<Integer, Integer> dist;
    public int networkDelayTime(int[][] times, int N, int K) {
        Map<Integer, List<int[]>> graph = new HashMap();
        for (int[] edge: times) {
            int u = edge[0] - 1;
            int v = edge[1] - 1;
            int time = edge[2];
            graph.putIfAbsent(u, new ArrayList<int[]>());
            graph.get(u).add(new int[]{v, time});
        }
        int[] distances = new int[N];
        Arrays.fill(distances, Integer.MAX_VALUE);
        distances[K-1] = 0;

        boolean[] visited = new boolean[N];
        
        PriorityQueue<int[]> pq = new PriorityQueue<>((n1 , n2) -> n1[1] - n2[1]);
        pq.offer(new int[]{K-1, 0});
        
        int count = 0;
        int result = 0;
        while(!pq.isEmpty()) {
            int[] node = pq.poll();
            int u = node[0];
            if(!visited[u]) {
                visited[u] = true;
                count++;
                // Examine and relax all neighboring vertices if possible
                if(graph.containsKey(u)) {
                    List<int[]> neighbors = graph.get(u);
                    for(int[] neighbor : neighbors) {
                        int v = neighbor[0];
                        int dU = distances[u];
                        int dUV = neighbor[1];
                        int dV = distances[v];
                        if(dU + dUV < dV) {
                            pq.add(new int[]{v, dU+dUV});
                            distances[v] = dU + dUV;
                        }
                    }
                }
            }
        }
        for(int i = 0; i < N; i++) {
            result = Math.max(result, distances[i]);
        }
        
        return count == N ? result : -1;
    }
}
```

# Implementation2 : Bellman Ford Algorithm
```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        int[][] edges = new int[times.length][3];
        for(int i = 0; i < times.length; i++) {
            edges[i][0] = times[i][0] - 1;
            edges[i][1] = times[i][1] - 1;
            edges[i][2] = times[i][2];
        }
        Arrays.sort(edges, (e1,e2) -> e1[2] - e2[2]);
        int[] distances = new int[N];
        Arrays.fill(distances, Integer.MAX_VALUE);
        distances[K-1] = 0;
        int result = 0;
        for(int i = 0; i < N-1; i++) {
            for(int j = 0; j < times.length; j++) {
                int u = edges[j][0];
                int v = edges[j][1];
                int time = edges[j][2];
                if(distances[u] != Integer.MAX_VALUE) {
                    int dU = distances[u];
                    int dV = distances[v];
                    if(dU+time < dV) {
                        distances[v] = dU + time;
                    }
                }
            }            
        }
        
        for(int i = 0; i < N; i++) {
            if(distances[i] == Integer.MAX_VALUE)
                return -1;
            result = Math.max(result, distances[i]);
        }
        
        return result;
    }
}
```

# Implementation 3 : Floyd Warshall Algorithm
```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        int[][] matrix = new int[N][N];
        for(int i = 0; i < N; i++) {
            Arrays.fill(matrix[i], 100000000);
            matrix[i][i] = 0;
        }
        for(int[] time : times) {
            matrix[time[0] - 1][time[1] - 1] = time[2];
        }
        
        for(int k = 0; k < N; k++)
            for(int i = 0; i < N; i++)
                for(int j = 0; j < N; j++)
                    matrix[i][j] = Math.min(matrix[i][j], matrix[i][k] + matrix[k][j]);
    
        int result = -1;
        for(int i = 0; i < N; i++) {
            if(matrix[K-1][i] == 100000000)
                return -1;
            result = Math.max(result, matrix[K-1][i]);
        }
        return result;
    }
}
```

# References :
1. Dijkstra's Algorithm https://www.youtube.com/watch?v=sD0lLYlGCJE (Hindi, good explanation)
2. Bellman Ford's Algorithm https://www.youtube.com/watch?v=IjEX4rgxsvI (Hindi, good explanation)
