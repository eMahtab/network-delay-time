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
