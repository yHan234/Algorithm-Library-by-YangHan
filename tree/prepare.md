# Prepare

{% code lineNumbers="true" %}
```cpp
namespace Tree
{
int                           n, root;
std::vector<std::vector<int>> adj;

void init(int n, int root)
{
    Tree::n    = n;
    Tree::root = root;
    adj.assign(n, {});
}
void addEdge(int u, int v)
{
    adj[u].push_back(v);
    adj[v].push_back(u);
}
} // namespace Tree
```
{% endcode %}
