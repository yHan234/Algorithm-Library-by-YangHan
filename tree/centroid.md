# Centroid

对于树上的每一个点，计算其所有子树中最大的子树节点数，这个值最小的点就是这棵树的重心。这里子树指无根树的子树，即包括“向上”的那棵子树，并且不包括整棵树自身。

* 以树的重心为根时，所有子树的大小都不超过整棵树大小的一半。
* 树中所有点到某个点的距离和中，到重心的距离和是最小的；如果有两个重心，那么到它们的距离和一样。
* 把两棵树通过一条边相连得到一棵新的树，那么新的树的重心在连接原来两棵树的重心的路径上。
* 在一棵树上添加或删除一个叶子，那么它的重心最多只移动一条边的距离。

{% code lineNumbers="true" %}
```cpp
auto getCentroid(auto &tree, int root, int n)
{
    std::array<int, 2> centroid{-1, -1}; // 树的重心
    std::vector<int>   weight;           // 这个节点的重量(最大的子树节点数)
    std::vector<int>   size;             // 以这个节点为根的子树大小（用于计算“向上”的那棵子树）

    std::function<void(int, int)> dfs =
        [&](int u, int par) {
            size[u] = 1, weight[u] = 0;
            for (auto v : tree[u])
            {
                if (v == par)
                {
                    continue;
                }

                getCentroid(v, u);
                size[u] += size[v];
                weight[u] = std::max(weight[u], size[v]);
            }
            weight[u] = std::max(weight[u], n - size[u]);
            if (weight[u] <= n / 2)
            {
                centroid[centroid[0] != -1] = u;
            }
        };

    return centroid;
}
```
{% endcode %}
