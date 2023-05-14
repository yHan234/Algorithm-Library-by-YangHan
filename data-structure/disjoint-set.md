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

    Index root(Index x)
    {
        while (x != par[x])
        {
            x = par[x] = par[par[x]];
        }
        return x;
    }

    void merge(Index x, Index y)
    {
        x = root(x);
        y = root(y);

        if (sz[x] < sz[y])
        {
            std::swap(x, y);
        }

        sz[x] += sz[y];
        par[y] = x;
        cnt--;
    }

    bool same(Index x, Index y)
    {
        return root(x) == root(y);
    }
    int size(Index x)
    {
        return sz[root(x)];
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

    Index root(Index x)
    {
        while (x != par[x])
        {
            vec[x] = vec[x] + vec[par[x]];
            x = par[x] = par[par[x]];
        }
        return x;
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
