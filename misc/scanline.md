# Scanline

{% code title="2D Point Counting" lineNumbers="true" %}
```cpp
// Scanline
namespace sl
{
struct Point
{
    int x, y;
};
struct Rectangle
{
    int x1, x2, y1, y2;
};
struct Event
{
    int x, y, sign, idx;
};

// 二维数点 y轴扫描
std::vector<int> pointCount(
    std::vector<Point>     &point, // [x, y]
    std::vector<Rectangle> &query) // [x1, x2, y1, y2]
{
    const int N = int(point.size());
    const int Q = int(query.size());

    // 离散化 x 坐标
    Discretizer<int> dX;
    dX.a.reserve(N + Q * 2);
    for (auto [x, y] : point)
    {
        dX.insert(x);
    }
    for (auto [x1, x2, y1, y2] : query)
    {
        dX.insert(x1 - 1);
        dX.insert(x2);
    }
    dX.initialize();

    // 读取询问，根据容斥原理将询问分为 4 个操作
    std::vector<Event> event; // [x, y, sign, idx]
    event.reserve(Q * 4);
    for (int i = 0; auto [x1, x2, y1, y2] : query)
    {
        event.emplace_back(x2, y2, 1, i);
        event.emplace_back(x1 - 1, y1 - 1, 1, i);
        event.emplace_back(x1 - 1, y2, -1, i);
        event.emplace_back(x2, y1 - 1, -1, i);
        i++;
    }

    // 将点和操作按 y 坐标排序
    std::sort(point.begin(), point.end(),
              [](auto p1, auto p2) {
                  return p1.y < p2.y;
              });
    std::sort(event.begin(), event.end(),
              [](auto p1, auto p2) {
                  return p1.y < p2.y;
              });

    Fenwick<int>     fw(dX.size()); // 树状数组记录前缀和
    std::vector<int> ans(Q, 0);     // 记录答案

    // 根据询问扫描
    for (int point_i = 0; auto [x, y, sign, idx] : event)
    {
        // 将扫描线以下的点全部计入
        while (point_i < N && point[point_i].y <= y)
        {
            fw.add(dX.rank(point[point_i].x) + 1, 1);
            point_i++;
        }
        // 修改该询问操作的答案
        ans[idx] += fw.sum(dX.rank(x) + 1) * sign;
    }

    return ans;
}
} // namespace sl
```
{% endcode %}

{% code title="Rectangle Area Union" lineNumbers="true" %}
```cpp
// LazySegmentTree
struct Tag
{
    int  add;
    void apply(const Tag &t)
    {
        add += t.add;
    }
};
struct Info
{
    int    min;
    double len;
    Info(int min = INT32_MAX, double len = 0)
        : min(min), len(len)
    {
    }
    static Info merge(const Info &lInfo, const Info &rInfo)
    {
        if (lInfo.min < rInfo.min)
        {
            return lInfo;
        }
        else if (lInfo.min > rInfo.min)
        {
            return rInfo;
        }
        else
        {
            return {lInfo.min, lInfo.len + rInfo.len};
        }
    }
    void apply(const Tag &t)
    {
        min += t.add;
    }
};

namespace sl
{
struct Rectangle
{
    double x1, x2, y1, y2;
};
struct Event
{
    double x1, x2, y;
    int    sign;
};

double areaUnion(const std::vector<Rectangle> &rec)
{
    const int N = rec.size();

    // 离散化x
    Discretizer<double> dX;
    dX.reserve(N * 2);
    for (auto [x1, x2, y1, y2] : rec)
    {
        dX.insert(x1);
        dX.insert(x2);
    }
    dX.initialize();

    const int segCnt = dX.size() - 1;

    // 建线段树
    std::vector<Info> init(segCnt + 1);
    for (int i = 1; i <= segCnt; i++)
    {
        init[i] = {0, dX.kth(i) - dX.kth(i - 1)};
    }
    LazySegmentTree<Info, Tag> uncovlen(init);

    // 构建event
    std::vector<Event> event;
    event.reserve(N * 2);
    for (auto [x1, x2, y1, y2] : rec)
    {
        event.push_back({x1, x2, y1, 1});
        event.push_back({x1, x2, y2, -1});
    }
    std::sort(event.begin(), event.end(), [](auto p1, auto p2) { return p1.y < p2.y; });

    // 扫描线
    double ans    = 0;
    double lasty  = 0;
    double totlen = dX.a.back() - dX.a.front();
    for (auto [x1, x2, y, sign] : event)
    {
        auto [min, len] = uncovlen.query(1, segCnt);

        double xx = (min == 0) ? totlen - len : totlen;
        double yy = y - lasty;
        ans += xx * yy;

        uncovlen.apply(dX.rank(x1) + 1, dX.rank(x2), {sign});
        lasty = y;
    }

    return ans;
}
} // namespace sl
```
{% endcode %}
