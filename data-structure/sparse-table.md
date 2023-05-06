# Sparse Table

可重复贡献问题（RMQ, GCD, &, |...）

O(n log n) - O(1)

输入输出数据一般很多，建议开启输入输出优化

{% code lineNumbers="true" %}
```cpp
template <typename M>
struct SparseTable
{
    std::vector<std::vector<M>>            st;
    std::function<M(const M &, const M &)> op;
    int                                    baseIndex;

    template <typename Iter>
    SparseTable(Iter first, Iter last, int baseIdx, std::function<M(const M &, const M &)> op)
        : op(op), baseIndex(baseIdx - 1)
    {
        int n = last - first, logn = std::log2(n);
        st.assign(logn + 1, std::vector<M>(n + 1));

        std::copy(first, last, st[0].begin() + 1);
        for (int k = 1; k <= logn; k++)
        {
            int len = (1 << (k - 1));
            for (int i = 1; i + (1 << k) - 1 <= n; i++)
            {
                st[k][i] = op(st[k - 1][i], st[k - 1][i + len]);
            }
        }
    }

    M prod(int l, int r)
    {
        l -= baseIndex;
        r -= baseIndex;

        if (l == r)
        {
            return M();
        }

        int len = std::log2(r - l);
        return op(st[len][l], st[len][r - (1 << len)]);
    }
};
```
{% endcode %}
