# Chtholly Tree

{% code lineNumbers="true" %}
```cpp
struct ChthollyTree
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

    std::set<Node> st;

    // [l, r) -> [l, x), [x, r)
    auto split(int x)
    {
        if (st.empty() || x >= std::prev(st.end())->r)
        {
            return st.end();
        }

        auto it = --st.upper_bound({x, 0, 0});
        if (it->l == x)
        {
            return it;
        }

        auto [l, r, v] = *it;
        st.erase(it);

        st.emplace(l, x, v);
        return st.emplace(x, r, v).first;
    }

    // odt[l, r) = v
    void assign(int l, int r, i64 v)
    {
        auto itr = split(r);
        auto itl = split(l);

        st.erase(itl, itr);
        st.emplace(l, r, v);
    }

    void performance(int l, int r)
    {
        for (auto last = split(r), it = split(l);
             it != last; ++it)
        {
            // Perform Operations here
        }
    }
};
```
{% endcode %}
