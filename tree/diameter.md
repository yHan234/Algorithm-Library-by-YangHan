# Diameter

树上任意两节点之间最长的简单路径即为树的「直径」。

* 若树上所有边边权均为正，则树的所有直径中点重合。

{% code lineNumbers="true" %}
```cpp
i64 getDiameter(auto &tree, int root)
{
    // 有负权边需要初始化为-INF
    std::vector<i64> d1(tree.size());
    std::vector<i64> d2(tree.size());
    i64              d = 0;

    std::function<void(int, int)> dfs =
        [&](int u, int par) {
            d1[u] = d2[u] = 0;
            for (auto [v, w] : tree[u])
            {
                if (v == par)
                {
                    continue;
                }
                dfs(v, u);

                i64 t = d1[v] + w;
                if (t > d1[u])
                {
                    d2[u] = d1[u];
                    d1[u] = t;
                }
                else if (t > d2[u])
                {
                    d2[u] = t;
                }
            }
            d = std::max(d, d1[u] + d2[u]);
        };
    dfs(root, -1);

    return d;
}
```
{% endcode %}
