# Disjoint Set

{% code lineNumbers="true" %}
```cpp
struct DisjointSet
{
    using Index = int;
    using Size  = int;

    std::vector<Index> par;
    std::vector<Size>  sz;
    Size               cnt; // count of connected components

    DisjointSet(Size n)
        : par(n), sz(n, 1), cnt(n)
    {
        std::iota(par.begin(), par.end(), 0);
    }

    Index root(Index u)
    {
        while (u != par[u])
        {
            u = par[u] = par[par[u]];
        }
        return u;
    }

    void merge(Index u, Index v)
    {
        u = root(u);
        v = root(v);

        if (sz[u] < sz[v])
        {
            std::swap(u, v);
        }

        sz[u] += sz[v];
        par[v] = u;
        cnt--;
    }

    bool same(Index u, Index v)
    {
        return root(u) == root(v);
    }
    int size(Index u)
    {
        return sz[root(u)];
    }
    int count()
    {
        return cnt;
    }

    std::vector<std::vector<int>> belong()
    {
        std::vector<std::vector<int>> res(par.size());
        for (int i = 0; i < par.size(); i++)
        {
            res[root(i)].push_back(i);
        }
        return res;
    }
    friend std::ostream &operator<<(std::ostream &os, DisjointSet &u)
    {
        for (int t = 0; auto i : u.belong())
        {
            os << t++ << ": ";
            for (auto j : i)
            {
                os << j << " ";
            }
            os << '\n';
        }
        return os;
    }
};
```
{% endcode %}

{% code title="带权并查集" lineNumbers="true" %}
```cpp
template <typename Vector>
struct WeightedDisjointSet
{
    using Size  = int;
    using Index = int;

    std::vector<Index>  par;
    std::vector<Vector> vec;
    std::vector<Size>   sz;

    WeightedDisjointSet(Size n)
        : par(n), vec(n), sz(n, 1)
    {
        std::iota(par.begin(), par.end(), 0);
    }

    Index root(Index u)
    {
        while (u != par[u])
        {
            vec[u] = vec[u] + vec[par[u]];
            u = par[u] = par[par[u]];
        }
        return u;
    };

    void merge(Index c, Index p, Vector v)
    {
        Index rtC = root(c);
        Index rtP = root(p);
        assert(rtC != rtP);

        if (sz[rtC] > sz[rtP])
        {
            std::swap(c, p);
            std::swap(rtC, rtP);
            v = -v;
        }

        vec[rtC] = -vec[c] + v + vec[p];
        sz[rtP] += sz[rtC];
        par[rtC] = rtP;
    }

    Vector query(Index from, Index to)
    {
        Index rtF = root(from);
        Index rtT = root(to);
        assert(rtF == rtT);

        return vec[from] + -vec[to];
    }
};

struct Vector
{
    // 零向量
    Vector()
    {
    }
    // 反向量
    Vector operator-() const
    {
    }
    // 向量加法
    friend Vector operator+(const Vector &lhs, const Vector &rhs)
    {
    }
};
```
{% endcode %}

{% code title="可撤销并查集" lineNumbers="true" %}
```cpp
struct UndoableDisjointSet
{
    using Index = int;
    using Size  = int;

    std::vector<Index> par;
    std::vector<Size>  sz;

    std::stack<std::pair<int &, int>> parHis;
    std::stack<std::pair<int &, int>> szHis;

    UndoableDisjointSet(Size n)
        : par(n), sz(n, 1)
    {
        std::iota(par.begin(), par.end(), 0);
    }

    Index root(Index u)
    {
        while (u != par[u])
        {
            u = par[u];
        }
        return u;
    }

    bool same(Index u, Index v)
    {
        return root(u) == root(v);
    }

    void merge(int u, int v)
    {
        u = root(u);
        v = root(v);

        if (u == v)
        {
            return;
        }
        if (sz[u] < sz[v])
        {
            std::swap(u, v);
        }

        szHis.push({sz[u], sz[u]});
        parHis.push({par[v], par[v]});

        sz[u]  = sz[u] + sz[v];
        par[v] = u;
    }

    void undo()
    {
        if (parHis.empty())
        {
            return;
        }

        parHis.top().first = parHis.top().second;
        szHis.top().first  = szHis.top().second;

        parHis.pop();
        szHis.pop();
    }

    int version()
    {
        return parHis.size();
    }

    void rollback(int version)
    {
        while (parHis.size() > version)
        {
            undo();
        }
    }
};
```
{% endcode %}

### 删除与移动

要删除一个叶子节点，我们可以将其父亲设为自己。为了保证要删除的元素都是叶子，我们可以预先为每个节点制作副本，并将其副本作为父亲。

移动与删除类似，通过以副本作为父亲，保证要移动的元素都是叶子。

```cpp
struct dsu
{
    vector<int> par, size;

    explicit dsu(int n)
        : par(n * 2), size(n * 2, 1)
    {
        iota(par.begin(), par.begin() + n, n);
        iota(par.begin() + n, par.end(), n);
    }

    void erase(int x)
    {
        --size[root(x)];
        par[x] = x;
    }

    void move(int x, int y)
    {
        auto px = root(x), py = root(y);
        if (px == py)
            return;
        par[x] = py;
        --size[px], ++size[py];
    }
};
```
