# Binary Trie

{% code lineNumbers="true" %}
```cpp
template <int Capacity>
struct BinaryTrie
{
    using Int     = std::uint32_t;
    using NodePtr = int;

    const int MAXBIT = 31;

    struct Node
    {
        std::array<NodePtr, 2> next;
        // 其它信息
        int num;
        int cnt;

        void init()
        {
            std::fill(next.begin(), next.end(), 0);
            // 其它信息的初始化
            cnt = 0;
            num = -1;
        }
    };
    std::array<Node, Capacity> node;
    NodePtr                    poolPtr = 0;

    NodePtr root;

    BinaryTrie()
        : root(newNode())
    {
    }

    NodePtr newNode()
    {
        node[poolPtr].init();
        return poolPtr++;
    }

    void insert(Int n)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int bit = n >> i & 1;
            if (!node[p].next[bit])
            {
                node[p].next[bit] = newNode();
            }
            p = node[p].next[bit];

            node[p].cnt++;
        }
        node[p].num = n;
    }

    // 找到与x异或最大的数
    Int xor_max(Int x)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int bit = x >> i & 1;
            // 优先走与当前位不同的路径
            p = (node[p].next[bit ^ 1])
                    ? node[p].next[bit ^ 1]
                    : node[p].next[bit];
        }
        return node[p].num;
    }

    // 找到与x异或第k小的数
    Int xor_kth_min(Int x, int k)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int     bit  = x >> i & 1;
            NodePtr smCh = node[p].next[bit];
            NodePtr bgCh = node[p].next[bit ^ 1];

            if (!smCh)
            {
                p = bgCh;
            }
            else if (node[smCh].cnt < k)
            {
                k -= node[smCh].cnt;
                p = bgCh;
            }
            else
            {
                p = smCh;
            }
        }
        return node[p].num;
    }
};
```
{% endcode %}
