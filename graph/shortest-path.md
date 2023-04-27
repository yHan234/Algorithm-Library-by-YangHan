# Shortest Path

{% code title="Floyd O(V ^ 3)" lineNumbers="true" %}
```cpp
// 邻接矩阵
void floyd(auto &G)
{
    const int N = G.size();

    for (int k = 0; k < N; k++)
    {
        for (int i = 0; i < N; i++)
        {
            for (int j = 0; j < N; j++)
            {
                if (G[i][j] > G[i][k] + G[k][j])
                {
                    G[i][j] = G[i][k] + G[k][j];
                    // pre[i][j] = k;
                }
            }
        }
    }
}
```
{% endcode %}

{% code title="Bellman O(V * E)" lineNumbers="true" %}
```cpp
// 点数，边[u, v, w]，起点，最大经过边数
auto bellman(int N, auto EDGES, int s, int k)
{
    std::vector<int> dist(N, INF);
    dist[s] = 0;
    for (int times = k; times--;)
    {
        auto bak = dist;
        for (auto [u, v, w] : EDGES)
        {
            dist[v] = std::min(dist[v], bak[u] + w);
        }
    }
    return dist;
}
```
{% endcode %}

{% code title="Dijkstra O(E logV)" lineNumbers="true" %}
```cpp
// [dist, pre, cnt]
auto dijkstra(const auto &G, int s)
{
    using pii   = std::pair<int, int>;
    const int N = G.size();

    std::vector<bool> done(N, false);
    std::vector<int>  dist(N, INF);
    std::vector<int>  pre(N, 0); // 前驱节点
    std::vector<int>  cnt(N, 0); // 最短路的数量
    dist[s] = 0;
    cnt[s]  = 1;

    std::priority_queue<pii, std::vector<pii>, std::greater<pii>>
        heap; // first:距离 second:结点
    heap.emplace(dist[s], s);

    while (!heap.empty())
    {
        int u = heap.top().second;
        heap.pop();
        if (done[u])
        {
            continue;
        }
        done[u] = true;

        for (auto [v, w] : G[u])
        {
            if (dist[u] + w < dist[v])
            {
                dist[v] = dist[u] + w;
                cnt[v]  = cnt[u];
                pre[v]  = u;
                heap.emplace(dist[v], v);
            }
            else if (dist[u] + w == dist[v])
            {
                cnt[v] += cnt[u];
            }
        }
    }

    return std::make_tuple(dist, pre, cnt);
};
```
{% endcode %}
