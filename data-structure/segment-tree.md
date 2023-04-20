# Segment Tree

```cpp
// 1-based indexing
template <typename Info>
struct SegmentTree
{
    using Size    = int;
    using Index   = int;
    using NodePtr = int;

    const Size        n;
    std::vector<Info> node;

    SegmentTree(Size n)
        : n(n), node(4 << std::__lg(n))
    {
    }

    SegmentTree(std::vector<Info> init)
        : SegmentTree(init.size() - 1)
    {
        auto build =
            [&](auto build, NodePtr p, Index l, Index r) {
                if (l == r)
                {
                    node[p] = init[l];
                    return;
                }

                Index m = (l + r) / 2;
                build(build, 2 * p, l, m);
                build(build, 2 * p + 1, m + 1, r);
                update(p);
            };
        build(build, 1, 1, n);
    }

    void update(NodePtr p)
    {
        node[p] = Info::merge(node[2 * p], node[2 * p + 1]);
    }

    void modify(Index x, const Info &info)
    {
        auto modify =
            [&](auto modify, NodePtr p, Index l, Index r) {
                if (l == r)
                {
                    node[p] = info;
                    return;
                }

                Index m = (l + r) / 2;
                if (x <= m)
                {
                    modify(modify, 2 * p, l, m);
                }
                else
                {
                    modify(modify, 2 * p + 1, m + 1, r);
                }

                update(p);
            };
        modify(modify, 1, 1, n);
    }

    Info query(Index ql, Index qr)
    {
        auto query =
            [&](auto query, NodePtr p, Index l, Index r) {
                if (r < ql || qr < l)
                {
                    return Info();
                }
                if (ql <= l && r <= qr)
                {
                    return node[p];
                }

                Index m = (l + r) / 2;
                return Info::merge(query(query, 2 * p, l, m),
                                   query(query, 2 * p + 1, m + 1, r));
            };
        return query(query, 1, 1, n);
    }
};

struct Info
{
    Info() //! query会使用默认构造
    {
    }
    Info() // 有参构造
    {
    }
    static Info merge(const Info &lnode, const Info &rnode)
    {
    }
};
```

```cpp
// 1-based indexing
template <typename Info, typename Tag>
struct LazySegmentTree
{
    using Size    = int;
    using Index   = int;
    using NodePtr = int;

    const Size        n;
    std::vector<Info> node;
    std::vector<Tag>  tag;

    LazySegmentTree(Size n)
        : n(n), node(4 << std::__lg(n)), tag(4 << std::__lg(n))
    {
    }

    LazySegmentTree(std::vector<Info> init)
        : LazySegmentTree(init.size() - 1)
    {
        auto build =
            [&](auto build, NodePtr p, Index l, Index r) {
                if (l == r)
                {
                    node[p] = init[l];
                    return;
                }

                Index m = (l + r) / 2;
                build(build, 2 * p, l, m);
                build(build, 2 * p + 1, m + 1, r);
                update(p);
            };
        build(build, 1, 1, n);
    }

    void update(NodePtr p)
    {
        node[p] = Info::merge(node[2 * p], node[2 * p + 1]);
    }

    void apply(NodePtr p, const Tag &t)
    {
        node[p].apply(t);
        tag[p].apply(t);
    }

    void pushdown(NodePtr p)
    {
        apply(2 * p, tag[p]);
        apply(2 * p + 1, tag[p]);
        tag[p] = Tag();
    }

    void apply(Index ql, Index qr, const Tag &t)
    {
        auto apply =
            [&](auto apply, NodePtr p, Index l, Index r) {
                if (r < ql || qr < l)
                {
                    return;
                }
                if (ql <= l && r <= qr)
                {
                    LazySegmentTree::apply(p, t);
                    return;
                }

                pushdown(p);
                Index m = (l + r) / 2;
                apply(apply, 2 * p, l, m);
                apply(apply, 2 * p + 1, m + 1, r);
                update(p);
            };
        return apply(apply, 1, 1, n);
    }

    Info query(Index ql, Index qr)
    {
        auto query =
            [&](auto query, NodePtr p, Index l, Index r) {
                if (r < ql || qr < l)
                {
                    return Info();
                }
                if (ql <= l && r <= qr)
                {
                    return node[p];
                }

                pushdown(p);
                Index m = (l + r) / 2;
                return Info::merge(query(query, 2 * p, l, m),
                                   query(query, 2 * p + 1, m + 1, r));
            };
        return query(query, 1, 1, n);
    }

    void modify(Index x, const Info &info)
    {
        auto modify =
            [&](auto modify, NodePtr p, Index l, Index r) {
                if (l == r)
                {
                    node[p] = info;
                    return;
                }

                pushdown(p);
                Index m = (l + r) / 2;
                if (x <= m)
                {
                    modify(modify, 2 * p, l, m);
                }
                else
                {
                    modify(modify, 2 * p + 1, m + 1, r);
                }
                update(p);
            };
        modify(modify, 1, 1, n);
    }
};

struct Tag
{
    Tag() //! pushdown会使用默认构造
    {
    }
    Tag() // 有参构造
    {
    }
    void apply(const Tag &t)
    {
    }
};
struct Info
{
    Info() //! query会使用默认构造
    {
    }
    Info() // 有参构造
    {
    }
    static Info merge(const Info &lnode, const Info &rnode)
    {
    }
    void apply(const Tag &t)
    {
    }
};
```