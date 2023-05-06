# Mo's algorithm

[https://www.cnblogs.com/WAMonster/p/10118934.html](https://www.cnblogs.com/WAMonster/p/10118934.html)

{% code lineNumbers="true" %}
```cpp
// O(N * Q^0.5)
namespace mo
{
int N, Q;
int BlockSize;

struct Query
{
    int l, r;
    int i;

    friend bool operator<(const Query &q1, const Query &q2)
    {
        if (q1.l / BlockSize != q2.l / BlockSize)
        {
            return q1.l < q2.l;
        }
        else
        {
            return ((q1.l / BlockSize) & 1)
                       ? q1.r < q2.r
                       : q1.r > q2.r;
        }
    }
};

std::vector<int>   A;
std::vector<Query> QUE;

int res;

void add(int i)
{
    res = {};
}
void del(int i)
{
    res = {};
}

void read()
{
    std::cin >> N;
    A.assign(N + 1, {});
    for (int i = 1; i <= N; i++)
    {
        std::cin >> A[i];
    }

    std::cin >> Q;
    QUE.assign(Q, {});
    for (int i = 0; i < Q; i++)
    {
        std::cin >> QUE[i].l >> QUE[i].r >> QUE[i].k;
        QUE[i].i = i;
    }
}

auto work()
{
    read();

    BlockSize = N / std::sqrt(1.0 * Q * 2 / 3);
    std::sort(QUE.begin(), QUE.end());
    res = 0;

    std::vector<int> ans(Q);

    for (int l = 1, r = 0;
         auto [ql, qr, k, qi] : QUE)
    {
        while (l > ql)
            add(--l);
        while (r < qr)
            add(++r);
        while (l < ql)
            del(l++);
        while (r > qr)
            del(r--);

        ans[qi] = res;
    }

    return ans;
}
} // namespace mo
```
{% endcode %}
