# DFS Order

* 子树每个节点的DFS序是连续的。将子树点权问题转换为序列区间问题。

{% code lineNumbers="true" %}
```cpp
auto getDFSOrder(auto &tree, int root)
{
    std::vector<int> in(tree.size()), out(tree.size()), seq(tree.size());

    int idx = 0;

    std::function<void(int, int)> dfs =
        [&](int u, int p) {
            in[u]      = idx++;
            seq[in[u]] = u;
            for (auto v : tree[u])
            {
                if (v == p)
                {
                    continue;
                }
                dfs(v, u);
                chi[u].push_back({in[v], out[v]});
            }
            out[u] = idx;
        };
    dfs(root, root);

    return std::make_tuple(in, out, seq);
}
```
{% endcode %}

### 换根

当已有从$$Root$$开始的DFS序，基于此计算从$$NewRoot$$开始的DFS序中，$$X$$的DFS区间（$$in$$和$$out$$）。

1. 若$$X=NewRoot$$，则$$X$$的DFS区间为$$[0,N)$$。
2. 若在原树（以$$Root$$为根）中，$$NewRoot$$是$$X$$的后代。则设$$Y$$为在所有$$X$$的子树中（以$$Root$$为根），$$NewRoot$$存在的那棵子树的根节点。则$$X$$的DFS区间为$$[0,in_Y)\cup[out_Y,N)$$。
3. 否则，$$X$$的DFS区间为$$[in_X,out_X)$$（与以$$Root$$为根时相同）。

{% code lineNumbers="true" %}
```cpp
std::vector<std::vector<int>> chi; // 记录所有节点的子节点的dfs序

auto getDFSOrder(auto &tree, int root)
{
    // ...
    std::function<void(int, int)> dfs = [&](int u, int p) {
        // ...
        for (auto v : tree[u])
        {
            // ...
            chi[u].push_back(in[v]);
        }
    };
    // ...
}

if (X == root)
{
    // [0, N)
}
else if (in[X] < in[root] && out[root] <= out[X])
{
    int y = seq[*std::prev(std::upper_bound(chi[X].begin(), chi[X].end(), in[root]))];
    // [0, in[y]), [out[y], N)
}
else
{
    // [in[X], out[X])
}
```
{% endcode %}
