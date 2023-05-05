# Sparse Table

可重复贡献问题（RMQ, GCD, &, |...）

O(n log n) - O(1)

输入输出数据一般很多，建议开启输入输出优化

{% code lineNumbers="true" %}
```cpp
template <typename T>
struct SparseTable
{
    std::vector<std::vector<T>>            st;
    std::function<T(const T &, const T &)> op;
    int                                    baseIndex;

    template <typename Iter>
    SparseTable(Iter begin, Iter end, int baseIdx, std::function<T(const T &, const T &)> op)
        : op(op), baseIndex(baseIdx - 1)
    {
        int n = end - begin, logn = std::log2(n);
        st.assign(logn + 1, std::vector<T>(n + 1));

        std::copy(begin, end, st[0].begin() + 1);
        for (int k = 1; k <= logn; k++)
        {
            int len = (1 << (k - 1));
            for (int i = 1; i + (1 << k) - 1 <= n; i++)
            {
                st[k][i] = op(st[k - 1][i], st[k - 1][i + len]);
            }
        }
    }

    T query(int l, int r)
    {
        l -= baseIndex;
        r -= baseIndex;

        int len = std::log2(r - l + 1);
        return op(st[len][l], st[len][r - (1 << len) + 1]);
    }
};
```
{% endcode %}
