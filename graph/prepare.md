# Prepare

```cpp
constexpr int INF = 0x3f3f3f3f;
```

{% code title="邻接矩阵" %}
```cpp
std::vector G(N, std::vector<int>(N, INF));
// 记得初始化
for (int i = 0; i < N; i++)
{
    G[i][i] = 0;
}
```
{% endcode %}
