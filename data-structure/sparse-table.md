# Sparse Table

{% code lineNumbers="true" %}
```cpp
#if __cplusplus >= 201703L
template <class S, auto op>
#else
template <class S, S (*op)(S, S)>
#endif
struct SparseTable
{
    std::vector<std::vector<S>> data;

    static int topbit(int x)
    {
        return (x == 0 ? -1 : 31 - __builtin_clz(x));
    }

    SparseTable()
    {
    }
    SparseTable(std::vector<S> a)
    {
        init(a);
    }

    void init(std::vector<S> v)
    {
        int n   = v.size();
        int log = topbit(n);

        data.resize(log + 1);
        data[0].resize(n);
        std::copy(v.begin(), v.end(), data[0].begin());

        for (int i = 0; i < log; i++)
        {
            data[i + 1].resize(data[i].size() - (1 << i));
            for (int j = 0; j < int(data[i].size()) - (1 << i); j++)
            {
                data[i + 1][j] = op(data[i][j], data[i][j + (1 << i)]);
            }
        }
    }

    S prod(int l, int r)
    {
        assert(l < r);
        if (l + 1 == r)
        {
            return data[0][l];
        }
        int k = topbit(r - l - 1);
        return op(data[k][l], data[k][r - (1 << k)]);
    }
};
```
{% endcode %}
