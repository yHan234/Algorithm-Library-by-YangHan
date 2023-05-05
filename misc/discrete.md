# Discrete

{% code lineNumbers="true" %}
```cpp
template <typename T>
struct Discretizer
{
    std::vector<T> a;

    Discretizer()
        : a()
    {
    }

    template <typename Iter>
    Discretizer(Iter first, Iter last)
        : a(first, last)
    {
        initialize();
    }

    void insert(T e)
    {
        a.push_back(e);
    }

    void initialize()
    {
        std::sort(a.begin(), a.end());
        a.erase(std::unique(a.begin(), a.end()), a.end());
    }

    // rank: 0 ~ size - 1
    std::size_t rank(const T &e)
    {
        return std::lower_bound(a.begin(), a.end(), e) - a.begin();
    }

    // rank: 0 ~ size - 1
    T kth(std::size_t k)
    {
        return a[k];
    }

    std::size_t size()
    {
        return a.size();
    }
};
```
{% endcode %}
