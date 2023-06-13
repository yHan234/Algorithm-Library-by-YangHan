# Binary Search

{% code lineNumbers="true" %}
```cpp
template <typename F>
i64 binarySearch(F check, i64 l, i64 r)
{
    assert(check(l));
    while (r - l > 1)
    {
        auto m         = (r + l) / 2;
        std::tie(l, r) = (check(m) ? std::make_pair(m, r) : std::make_pair(l, m));
    }
    return l;
}

template <typename F>
double realBinarySearch(F check, double l, double r, int iter = 100)
{
    while (iter--)
    {
        double m       = (l + r) / 2;
        std::tie(l, r) = (check(m) ? std::make_pair(m, r) : std::make_pair(l, m));
    }
    return (l + r) / 2;
}
```
{% endcode %}
