# Chtholly Tree

{% code lineNumbers="true" %}
```cpp
namespace odt
{
struct Node
{
    int         l, r;
    mutable i64 v; // Info

    Node(const int &il, const int &ir, const i64 &iv)
        : l(il), r(ir), v(iv)
    {
    }

    inline bool operator<(const Node &o) const
    {
        return l < o.l;
    }
};

std::set<Node> tr;

// [l, r] -> [l, x), [x, r]
auto split(int x)
{
    if (tr.empty() || x > std::prev(tr.end())->r)
    {
        return tr.end();
    }

    auto it = --tr.upper_bound({x, 0, 0});
    if (it->l == x)
    {
        return it;
    }

    auto [l, r, v] = *it;
    tr.erase(it);

    tr.emplace(l, x - 1, v);
    return tr.emplace(x, r, v).first;
}

// odt[l, r] = v
void assign(int l, int r, i64 v)
{
    auto itr = split(r + 1);
    auto itl = split(l);

    tr.erase(itl, itr);
    tr.emplace(l, r, v);
}

void performance(int l, int r)
{
    for (auto last = split(r + 1), it = split(l);
         it != last; ++it)
    {
        // Perform Operations here
    }
}
} // namespace odt
```
{% endcode %}
