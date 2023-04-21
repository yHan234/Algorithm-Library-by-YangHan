# MedianHeap

{% code title="维护第k小" lineNumbers="true" %}
```cpp
template <typename T>
struct MedianHeap
{
    // 小根堆，维护大的元素
    std::priority_queue<T, std::vector<T>, std::greater<T>> minHeap;
    // 大根堆，维护小的元素
    std::priority_queue<T, std::vector<T>, std::less<T>> maxHeap;

    // 维护第 k 小，使大根堆中有恰好 k 个元素
    void maintain(int k)
    {
        while (maxHeap.size() < k)
            maxHeap.push(minHeap.top()), minHeap.pop();
        while (maxHeap.size() > k)
            minHeap.push(maxHeap.top()), maxHeap.pop();
    }

    void push(T x)
    {
        if (minHeap.empty())
            (maxHeap.empty() || x <= maxHeap.top()) ? maxHeap.push(x) : minHeap.push(x);
        else
            (x >= minHeap.top()) ? minHeap.push(x) : maxHeap.push(x);
    }

    T findkth(int k)
    {
        maintain(k);
        return maxHeap.top();
    }

    void popkth(int k)
    {
        maintain(k);
        maxHeap.pop();
    }

    int size()
    {
        return minHeap.size() + maxHeap.size();
    }
};
```
{% endcode %}

{% code title="维护第k大" lineNumbers="true" %}
```cpp
template <typename T>
struct MedianHeap
{
    // 小根堆，维护大的元素
    std::priority_queue<T, std::vector<T>, std::greater<T>> minHeap;
    // 大根堆，维护小的元素
    std::priority_queue<T, std::vector<T>, std::less<T>> maxHeap;

    // 维护第 k 大，使小根堆中有恰好 k 个元素
    void maintain(int k)
    {
        while (minHeap.size() < k)
            minHeap.push(maxHeap.top()), maxHeap.pop();
        while (minHeap.size() > k)
            maxHeap.push(minHeap.top()), minHeap.pop();
    }

    void push(T x)
    {
        if (minHeap.empty())
            (maxHeap.empty() || x <= maxHeap.top()) ? maxHeap.push(x) : minHeap.push(x);
        else
            (x >= minHeap.top()) ? minHeap.push(x) : maxHeap.push(x);
    }

    T findkth(int k)
    {
        maintain(k);
        return minHeap.top();
    }

    void popkth(int k)
    {
        maintain(k);
        minHeap.pop();
    }

    int size()
    {
        return minHeap.size() + maxHeap.size();
    }
};
```
{% endcode %}
