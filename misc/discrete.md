# Discrete

{% code lineNumbers="true" %}
```cpp
template <typename T>
struct Discretizer
{
    std::vector<T> a;
    bool           inited = false;

    void reserve(std::size_t n)
    {
        a.reserve(n);
    }
    void insert(T e)
    {
        a.push_back(e);
        inited = false;
    }
    void init()
    {
        std::sort(a.begin(), a.end());
        a.erase(std::unique(a.begin(), a.end()), a.end());
        inited = true;
    }

    void checkInit()
    {
        if (!inited)
        {
            init();
        }
        inited = true;
    }

    // order: [0, size)
    std::size_t order(const T &e)
    {
        checkInit();
        return std::lower_bound(a.begin(), a.end(), e) - a.begin();
    }
    // k: [0, size)
    T kth(std::size_t k)
    {
        checkInit();
        return a[k];
    }

    std::size_t size()
    {
        checkInit();
        return a.size();
    }
};
```
{% endcode %}
