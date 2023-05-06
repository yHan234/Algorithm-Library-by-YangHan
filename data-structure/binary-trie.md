# Binary Trie

{% code lineNumbers="true" %}
```cpp
template <int Capacity>
struct BinaryTrie
{
    using Int     = std::uint32_t;
    using NodePtr = int;

    const int MAXBIT = 30;

    struct Node
    {
        std::array<NodePtr, 2> next;
        // 其它信息
        int num;
        int cnt;

        void init()
        {
            next[0] = 0;
            next[1] = 0;
            // 其它信息的初始化
            cnt = 0;
            num = -1;
        }
    };
    std::array<Node, Capacity> node;
    NodePtr                    poolPtr;

    NodePtr root;

    BinaryTrie()
        : poolPtr(0), root(newNode())
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

    void erase(Int n)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int bit = n >> i & 1;
            if (!node[p].next[bit] ||
                !node[node[p].next[bit]].cnt)
            {
                assert(false);
            }
            p = node[p].next[bit];

            node[p].cnt--;
        }
    }

    // 查找与x异或最大的数
    Int findXorMax(Int x)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int bit = x >> i & 1;

            // 当前位不同的路径异或值更大
            NodePtr bigCh = node[p].next[bit ^ 1];
            NodePtr smlCh = node[p].next[bit];

            p = (bigCh && node[bigCh].cnt)
                    ? bigCh
                    : smlCh;
        }
        return node[p].num;
    }

    // 找到与x异或第k小的数（k从1开始）
    Int findXorKthMin(Int x, int k)
    {
        NodePtr p = root;
        for (int i = MAXBIT; i >= 0; i--)
        {
            Int     bit  = x >> i & 1;
            NodePtr smCh = node[p].next[bit];
            NodePtr bgCh = node[p].next[bit ^ 1];

            if (!smCh || !node[smCh].cnt)
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
