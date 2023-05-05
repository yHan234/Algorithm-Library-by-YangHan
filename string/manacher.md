# Manacher

{% code lineNumbers="true" %}
```cpp
// s[i]            -> r[i * 2 + 1]
// s[i] | s[i + 1] -> r[i * 2 + 2]
// r[i_odd]        -> s[i / 2]
// r[i_even]       -> s[i / 2 - 1] | s[i / 2]
std::vector<int> manacher(std::string ss)
{
    std::string s = "#";
    for (auto c : ss)
    {
        s += c;
        s += '#';
    }

    int n = int(s.size());

    std::vector<int> r(n);
    for (int i = 0, l = 0, r = -1; i < n; i++)
    {
        int k = (i > r) ? 1 : std::min(r[l + r - i], r - i + 1);
        while (0 <= i - k && i + k < n && s[i - k] == s[i + k])
        {
            k++;
        }
        r[i] = k--;
        if (i + k > r)
        {
            l = i - k;
            r = i + k;
        }
    }

    for (int i = 0; i < n; i++)
    {
        r[i] = r[i] / 2 * 2 - (i & 1);
    }

    return r;
}
```
{% endcode %}
