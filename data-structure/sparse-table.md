# Sparse Table

可重复贡献问题（RMQ, GCD, &, |...）

O(n log n) - O(1)

输入输出数据一般很多，建议开启输入输出优化

{% code lineNumbers="true" %}
```cpp
#if __cplusplus >= 201703L
template <class S, auto op, auto e>
#else
template <class S, S (*op)(S, S), S (*e)()>
#endif
struct SparseTable
{
    std::vector<std::vector<S>> data;

    static int topbit(int x)
    {
        return (x == 0 ? -1 : 31 - __builtin_clz(x));
    }

    SparseTable(std::vector<S> init)
    {
        int n   = init.size();
        int log = topbit(n);

        data.resize(log + 1);
        data[0].resize(n);
        std::copy(init.begin(), init.end(), data[0].begin());

        for (int i = 0; i < log; i++)
        {
            data[i + 1].resize(data[i].size() - (1 << i));
            for (int j = 0; j < data[i].size() - (1 << i); j++)
            {
                data[i + 1][j] = op(data[i][j], data[i][j + (1 << i)]);
            }
        }
    }

    S prod(int l, int r)
    {
        if (l == r)
        {
            return e();
        }
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
