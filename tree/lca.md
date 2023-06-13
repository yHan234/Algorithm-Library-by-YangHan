# LCA

1. $$\text{LCA}({u})=u$$
2. $$u$$是$$v$$的祖先，当且仅当$$\text{LCA}(u,v)=u$$
3. 如果$$u$$不为$$v$$的祖先并且$$v$$不为$$u$$的祖先，那么$$u,v$$分别处于$$\text{LCA}(u,v)$$的两棵不同子树中
4. 前序遍历中，$$\text{LCA}(S)$$出现在所有$$S$$中元素之前，后序遍历中$$\text{LCA}(S)$$则出现在所有$$S$$中元素之后
5. 两点集并的最近公共祖先为两点集分别的最近公共祖先的最近公共祖先，即$$\text{LCA}(A\cup B)=\text{LCA}(\text{LCA}(A), \text{LCA}(B))$$
6. 两点的最近公共祖先必定处在树上两点间的最短路上
7. $$d(u,v)=h(u)+h(v)-2h(\text{LCA}(u,v))$$，其中$$d$$是树上两点间的距离，$$h$$代表某点到树根的距离

{% code title="倍增" lineNumbers="true" %}
```cpp
namespace tree
{
int                           n, logn;
std::vector<int>              dep;
std::vector<std::vector<int>> par;
std::vector<std::vector<int>> cost;

int calCost(int x, int y)
{
    // 求路径最小值
    return std::min(x, y);
}

void init(auto &tree, int root = 0)
{
    n    = tree.size();
    logn = std::__lg(n);

    dep.resize(n);
    par.resize(n, std::vector<int>(logn + 1));
    cost.resize(n, std::vector<int>(logn + 1));

    std::function<void(int, int)> dfs =
        [&](int u, int p) {
            dep[u]    = (u == root ? 0 : dep[p] + 1);
            par[u][0] = p;

            for (int i = 1; i <= logn; i++)
            {
                par[u][i]  = par[par[u][i - 1]][i - 1];
                cost[u][i] = calCost(cost[u][i - 1], cost[par[u][i - 1]][i - 1]);
            }

            for (auto [v, w] : tree[u])
            {
                if (v == p)
                {
                    continue;
                }
                cost[v][0] = w;
                dfs(v, u);
            }
        };
    dfs(root, root);
}

int findLCA(int u, int v)
{
    if (dep[u] < dep[v])
    {
        std::swap(u, v);
    }

    int ans = INT32_MAX;

    for (int i = 0, dif = dep[u] - dep[v]; dif; i++, dif >>= 1)
    {
        if (dif & 1)
        {
            ans = calCost(ans, cost[u][i]);
            u   = par[u][i];
        }
    }

    if (u == v)
    {
        return ans;
    }

    for (int i = logn; i >= 0 && v != u; --i)
    {
        if (par[u][i] != par[v][i])
        {
            ans = calCost(ans, calCost(cost[u][i], cost[v][i]));
            u = par[u][i], v = par[v][i];
        }
    }

    return calCost(ans, calCost(cost[u][0], cost[v][0]));
}
}; // namespace tree
```
{% endcode %}

{% code title="Euler Order + Sparse Table" lineNumbers="true" %}
```cpp
namespace tree
{
int              n;
std::vector<int> dep;
std::vector<int> eu, euin;

int mindep(int u, int v)
{
    return (dep[u] < dep[v] ? u : v);
}
SparseTable<int, mindep> st;

void init(auto &tree, int root = 0)
{
    n = tree.size();

    dep.resize(n);
    euin.resize(n);
    eu.clear();

    int idx = 0;

    std::function<void(int, int)> dfs =
        [&](int u, int p) {
            dep[u]  = (u == root ? 0 : dep[p] + 1);
            euin[u] = eu.size();
            eu.push_back(u);

            for (auto v : tree[u])
            {
                if (v == p)
                {
                    continue;
                }
                dfs(v, u);
                eu.push_back(u);
            }
        };
    dfs(root, root);

    st.init(eu);
}

int LCA(int u, int v)
{
    if (euin[u] > euin[v])
    {
        std::swap(u, v);
    }
    return st.prod(euin[u], euin[v] + 1);
}
}; // namespace tree
```
{% endcode %}
