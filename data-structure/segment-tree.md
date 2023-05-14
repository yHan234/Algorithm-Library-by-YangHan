# Segment Tree

{% code title="普通线段树" lineNumbers="true" %}
```cpp
#if __cplusplus >= 201703L
template <class S, auto op, auto e>
#else
template <class S, S (*op)(S, S), S (*e)()>
#endif
struct SegmentTree
{
    using Size    = int;
    using Index   = int;
    using NodePtr = int;

    const Size     n;
    std::vector<S> data;

    SegmentTree(Size n)
        : SegmentTree(std::vector<S>(n, e()))
    {
    }
    SegmentTree(std::vector<S> init)
        : n(init.size()), data(4 << std::__lg(n))
    {
        std::function<void(NodePtr, Index, Index)> build =
            [&](NodePtr p, Index l, Index r) {
                if (r - l == 1)
                {
                    data[p] = init[l];
                    return;
                }

                Index m = (l + r) / 2;
                build(2 * p, l, m);
                build(2 * p + 1, m, r);
                pull(p);
            };
        build(1, 0, n);
    }

    void set(Index x, const S &d, NodePtr p = 1, Index l = 0, Index r = -1)
    {
        r = (r == -1) ? n : r;

        if (r - l == 1)
        {
            data[p] = d;
            return;
        }

        Index m = (l + r) / 2;
        (x < m) ? set(x, d, 2 * p, l, m)
                : set(x, d, 2 * p + 1, m, r);

        pull(p);
    }
    S prod(Index ql, Index qr, NodePtr p = 1, Index l = 0, Index r = -1)
    {
        r = (r == -1) ? n : r;

        if (qr <= l || r <= ql)
        {
            return e();
        }
        if (ql <= l && r <= qr)
        {
            return data[p];
        }

        Index m = (l + r) / 2;
        return op(prod(ql, qr, 2 * p, l, m),
                  prod(ql, qr, 2 * p + 1, m, r));
    }

  private:
    void pull(NodePtr p)
    {
        data[p] = op(data[2 * p], data[2 * p + 1]);
    }
};
```
{% endcode %}

{% code title="懒标记线段树" lineNumbers="true" %}
```cpp
#if __cplusplus >= 201703L
template <class S, auto op, auto e, class F, auto mp, auto comp, auto id>
#else
template <class S, S (*op)(S, S), S (*e)(), class F, S (*mp)(S, F), F (*comp)(F, F), F (*id)()>
#endif
struct LazySegmentTree
{
    using Size    = int;
    using Index   = int;
    using NodePtr = int;

    const Size     n;
    std::vector<S> data;
    std::vector<F> tag;

    LazySegmentTree(Size n)
        : LazySegmentTree(std::vector<S>(n, e()))
    {
    }
    LazySegmentTree(std::vector<S> init)
        : n(init.size()), data(4 << std::__lg(n)), tag(4 << std::__lg(n), id())
    {
        std::function<void(NodePtr, Index, Index)> build =
            [&](NodePtr p, Index l, Index r) {
                if (r - l == 1)
                {
                    data[p] = init[l];
                    return;
                }

                Index m = (l + r) / 2;
                build(2 * p, l, m);
                build(2 * p + 1, m, r);
                pull(p);
            };
        build(1, 0, n);
    }

    void set(Index x, const S &d, NodePtr p = 1, Index l = 0, Index r = -1)
    {
        r = (r == -1) ? n : r;

        if (r - l == 1)
        {
            data[p] = d;
            return;
        }

        push(p);
        Index m = (l + r) / 2;
        (x < m) ? set(x, d, 2 * p, l, m)
                : set(x, d, 2 * p + 1, m, r);
        pull(p);
    }
    S prod(Index ql, Index qr, NodePtr p = 1, Index l = 0, Index r = -1)
    {
        r = (r == -1) ? n : r;

        if (qr <= l || r <= ql)
        {
            return e();
        }
        if (ql <= l && r <= qr)
        {
            return data[p];
        }

        push(p);
        Index m = (l + r) / 2;
        return op(prod(ql, qr, 2 * p, l, m),
                  prod(ql, qr, 2 * p + 1, m, r));
    }
    void apply(Index ql, Index qr, const F &t, NodePtr p = 1, Index l = 0, Index r = -1)
    {
        r = (r == -1) ? n : r;

        if (qr <= l || r <= ql)
        {
            return;
        }
        if (ql <= l && r <= qr)
        {
            apply(p, t);
            return;
        }

        push(p);
        Index m = (l + r) / 2;
        apply(ql, qr, t, 2 * p, l, m);
        apply(ql, qr, t, 2 * p + 1, m, r);
        pull(p);
    }

  private:
    void pull(NodePtr p)
    {
        data[p] = op(data[2 * p], data[2 * p + 1]);
    }
    void push(NodePtr p)
    {
        apply(2 * p, tag[p]);
        apply(2 * p + 1, tag[p]);
        tag[p] = id();
    }
    void apply(NodePtr p, F t)
    {
        data[p] = mp(data[p], t);
        tag[p]  = comp(tag[p], t);
    }
};
```
{% endcode %}

{% code title="线段树上二分" lineNumbers="true" %}
```cpp
Index maxRight(Index ql, auto f)
{
    S s = e();

    std::function<Index(NodePtr, Index, Index)> maxRight =
        [&](NodePtr p, Index l, Index r) {
            if (r <= ql)
            {
                return ql;
            }
            if (ql <= l)
            {
                S ss = op(s, data[p]);
                if (f(ss))
                {
                    s = ss;
                    return r;
                }
                else if (r - l == 1)
                {
                    return l;
                }
            }

            push(p); // Lazy
            int m    = (l + r) / 2;
            int lRes = maxRight(2 * p, l, m);
            return (lRes < m) ? lRes : maxRight(2 * p + 1, m, r);
        };
    return maxRight(1, 0, n);
}

// TODO: minLeft
```
{% endcode %}

{% code title="动态开点懒标记线段树" lineNumbers="true" %}
```cpp
#if __cplusplus >= 201703L
template <std::size_t Capacity, class S, auto op, auto e, class F, auto mp, auto comp, auto id>
#else
template <std::size_t Capacity, class S, S (*op)(S, S), S (*e)(), class F, S (*mp)(S, F, int, int), F (*comp)(F, F), F (*id)()>
#endif
struct DynamicLazySegmentTree
{
    using Index   = int;
    using NodePtr = int;

    DynamicLazySegmentTree(Index down, Index up)
        : DOWN(down), UP(up), node(), poolPtr(0), root(newNode())
    {
    }
    void set(Index x, const S &d)
    {
        set(x, d, root, DOWN, UP);
    }
    S prod(Index l, Index r)
    {
        return prod(l, r, root, DOWN, UP);
    }
    void apply(Index l, Index r, const F &t)
    {
        apply(l, r, t, root, DOWN, UP);
    }

  private:
    const Index DOWN, UP;

    struct Node
    {
        std::pair<NodePtr, NodePtr> chi;

        S data = e();
        F tag  = id();
    };
    std::array<Node, Capacity> node;
    std::size_t                poolPtr;
    NodePtr                    root;

    NodePtr newNode()
    {
        return poolPtr++;
    }

    void pull(NodePtr p, Index l, Index r)
    {
        auto &[lc, rc] = node[p].chi;
        node[p].data   = op(node[lc].data, node[rc].data);
    }
    void apply(const F &t, NodePtr p, Index l, Index r)
    {
        node[p].data = mp(node[p].data, t, l, r);
        node[p].tag  = comp(node[p].tag, t);
    }
    void push(NodePtr p, Index l, Index r)
    {
        auto &[lc, rc] = node[p].chi;
        if (!lc)
        {
            lc = newNode();
            rc = newNode();
        }

        auto      &tag = node[p].tag;
        const auto m   = (l + r) / 2;
        apply(tag, lc, l, m);
        apply(tag, rc, m, r);
        tag = F();
    }

    void set(Index x, const S &d, NodePtr p, Index l, Index r)
    {
        if (r - l == 1)
        {
            node[p].data = d;
            return;
        }

        push(p, l, r);
        const auto [lc, rc] = node[p].chi;
        const auto m        = (l + r) / 2;

        (x < m) ? set(x, d, lc, l, m)
                : set(x, d, rc, m, r);
        pull(p, l, r);
    }

    S prod(Index ql, Index qr, NodePtr p, Index l, Index r)
    {
        if (qr <= l || r <= ql)
        {
            return e();
        }
        if (ql <= l && r <= qr)
        {
            return node[p].data;
        }

        push(p, l, r);
        const auto [lc, rc] = node[p].chi;
        const auto m        = (l + r) / 2;
        return op(prod(ql, qr, lc, l, m),
                  prod(ql, qr, rc, m, r));
    }

    void apply(Index ql, Index qr, const F &t, NodePtr p, Index l, Index r)
    {
        if (qr <= l || r <= ql)
        {
            return;
        }
        if (ql <= l && r <= qr)
        {
            apply(t, p, l, r);
            return;
        }

        push(p, l, r);
        const auto [lc, rc] = node[p].chi;
        const auto m        = (l + r) / 2;
        apply(ql, qr, t, lc, l, m);
        apply(ql, qr, t, rc, m, r);
        pull(p, l, r);
    }
};
```
{% endcode %}
