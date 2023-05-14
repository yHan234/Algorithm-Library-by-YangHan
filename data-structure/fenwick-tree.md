# Fenwick Tree

$$
c_i = \sum_{i - lowbit(i) + 1}^i a_i
$$

<figure><img src="../.gitbook/assets/fenwick.svg" alt=""><figcaption><p>Fenwick Tree</p></figcaption></figure>

{% code title="树状数组" lineNumbers="true" %}
```cpp
template <typename T>
struct Fenwick
{
    using Size  = int;
    using Index = int;

    Size           n;
    std::vector<T> c;

    Fenwick(Size n)
        : n(n), c(n + 1)
    {
    }

    void add(Index x, T v)
    {
        assert(0 <= x && x < n);
        for (Index i = x + 1; i <= n; i += i & -i)
        {
            c[i - 1] += v;
        }
    }

    T psum(Index x)
    {
        assert(0 <= x && x <= n);
        auto res = T();
        for (Index i = x; i; i -= i & -i)
        {
            res += c[i - 1];
        }
        return res;
    }
    T sum(Index l, Index r)
    {
        return psum(r) - psum(l);
    }

    // return max k: psum(k) <= s
    int maxRight(T s)
    {
        int k = 0;
        for (int bit = 1 << std::__lg(n); bit; bit >>= 1)
        {
            if (k + bit - 1 < n && c[k + bit - 1] <= s)
            {
                s -= c[k + bit - 1];
                k += bit;
            }
        }
        return k;
    }
};
```
{% endcode %}

{% code title="差分树状数组" lineNumbers="true" %}
```cpp
template <typename T>
struct DiffFenwick
{
    using Size  = int;
    using Index = int;

    Size       n;
    Fenwick<T> b, bi;

    DiffFenwick(Size n)
        : n(n), b(n + 1), bi(n + 1)
    {
    }

    void add(Index l, Index r, T v)
    {
        b.add(l, v);
        b.add(r, -v);
        bi.add(l, v * l);
        bi.add(r, -v * r);
    }
    T psum(Index p)
    {
        return p * b.psum(p) - bi.psum(p);
    }
    T sum(Index l, Index r)
    {
        return psum(r) - psum(l);
    }
    T get(Index p)
    {
        return b.psum(p + 1);
    }
};
```
{% endcode %}

{% code title="二维树状数组" lineNumbers="true" %}
```cpp
template <typename T>
struct TwoDFenwick
{
    using Size  = int;
    using Index = int;

    Size                        n, m;
    std::vector<std::vector<T>> a;

    TwoDFenwick(Size n, Size m)
        : n(n), m(m), a(n, std::vector<T>(m))
    {
    }

    void add(Index r, Index c, T v)
    {
        for (Index i = r + 1; i <= n; i += i & -i)
        {
            for (Index j = c + 1; j <= m; j += j & -j)
            {
                a[i - 1][j - 1] += v;
            }
        }
    }

    T psum(Index r, Index c)
    {
        T res = T();
        for (Index i = r; i; i -= i & -i)
        {
            for (Index j = c; j; j -= j & -j)
            {
                res += a[i - 1][j - 1];
            }
        }
        return res;
    }
};
```
{% endcode %}
