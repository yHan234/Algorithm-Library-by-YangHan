# Topological Sorting

{% code title="Topological Sorting O(E + V)" lineNumbers="true" %}
```cpp
// 非拓扑图返回empty
auto toposort(const auto &G)
{
    const int N = G.size();

    std::vector<int> in(N);
    for (auto u : G)
    {
        for (auto v : u)
        {
            in[v] += 1;
        }
    }

    std::queue<int> q;
    for (int i = 0; i < N; i++)
    {
        if (in[i] == 0)
        {
            q.push(i);
        }
    }

    std::vector<int> l;
    while (!q.empty())
    {
        int u = q.front();
        q.pop();
        l.push_back(u);

        for (auto v : G[u])
        {
            if (--in[v] == 0)
            {
                q.push(v);
            }
        }
    }

    if (int(l.size()) != N)
    {
        l.clear();
    }

    return l;
}
```
{% endcode %}

{% code title="DAG Shortest Path" lineNumbers="true" %}
```cpp
// 只有拓扑顶点能被作为最短路源点
auto toposort(const auto &G, int s)
{
    const int N = G.size();

    std::vector<int> in(N);
    for (auto u : G)
    {
        for (auto [v, w] : u)
        {
            in[v] += 1;
        }
    }

    std::queue<int> q;
    q.push(s);

    std::vector<int> dist(N, INF);
    dist[s] = 0;

    while (!q.empty())
    {
        int u = q.front();
        q.pop();

        for (auto [v, w] : G[u])
        {
            dist[v] = std::min(dist[v], dist[u] + w);
            if (--in[v] == 0)
            {
                q.push(v);
            }
        }
    }

    return dist;
}
```
{% endcode %}
