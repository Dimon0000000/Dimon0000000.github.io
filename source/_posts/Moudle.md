---
title: 算竞模板
date: 2022-10-26 08:45:21
tags: 算竞模板
categories: 算竞模板
index_img: https://s2.loli.net/2022/10/26/ynHm4gTl317wvBa.png
---

> 前言:
> 所有模板仅仅为笔者自己在算竞中用到的模板
> 由于笔者竞赛分都很低，高级模板还请前往查看[old_yan的算竞模板](https://github.com/old-yan/)

### 数学

#### 最大公约数

```cpp
int gcd(int a, int b) {
    while (b ^= a ^= b ^= a %= b);
    return a;
}
```

----------

#### 最小公倍数

```cpp
long long lcm(long long a, long long b) {
    return a * b / gcd(a, b);
}
```

---------


####  快速幂

```cpp
//快速幂
template<typename T>
T power(T a, ll b) {
    T res = 1;
    for (; b; b /= 2, a *= a)
        if (b % 2)
            res *= a;
    return res;
}
```

-------------

#### 快速判断质数

```cpp
template<typename typC>
bool isPrime(typC num) {
    if (num == 1 || num == 4)return 0;
    if (num == 2 || num == 3)return 1;
    if (num % 6 != 1 && num % 6 != 5)return 0;
    typC tmp = sqrt(num);
    for (int i = 5; i <= tmp; i += 6)
        if (num % i == 0 || num % (i + 2) == 0)return 0;
    return 1;
}
```

----------

#### 根据三点获取角度

```cpp
double get_angle(double x1, double y1, double x2, double y2, double x3, double y3) {
    double theta = atan2(x1 - x3, y1 - y3) - atan2(x2 - x3, y2 - y3);
    if (theta > M_PI)
        theta -= 2 * M_PI;
    if (theta < -M_PI)
        theta += 2 * M_PI;

    theta = abs(theta * 180.0 / M_PI);
    return theta;
}
```

### 图论

#### 并查集

```cpp
class UnionFind {
    vector<int> root;
    vector<int> rank;
public:
    UnionFind(int size) {
        root.resize(size);
        rank.resize(size);
        for (int i = 0; i < size; ++i) {
            root[i] = rank[i] = i;
        }
    }

    int find(int x) {
        if (x == root[x]) return x;
        return root[x] = find(root[x]);
    }

    void connect(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] > rank[rootY]) {
                root[rootY] = rootX;
            } else if (rank[rootX] < rank[rootY]) {
                root[rootX] = rootY;
            } else {
                root[rootY] = rootX;
                rank[rootX] += 1;
            }
        }
    }

    bool isConnected(int x, int y) {
        return find(x) == find(y);
    }
};
```

-----

#### BellmanFord

```cpp
namespace BellmanFord {
    template <typename Tp>
    struct BellmanFord {
        struct Edge {
            uint32_t from, to;
            Tp distance;
        };
        std::vector<Edge> m_edges;
        std::vector<Tp> m_distances;
        std::vector<uint32_t> m_from;
        uint32_t m_vertexNum;
        Tp m_infiniteDistance;
        BellmanFord(uint32_t _vertexNum, uint32_t _edgeNum, Tp _infiniteDistance = std::numeric_limits<Tp>::max() / 2) : m_distances(_vertexNum, _infiniteDistance), m_vertexNum(_vertexNum), m_infiniteDistance(_infiniteDistance) { m_edges.reserve(_edgeNum); }
        void addEdge(uint32_t _a, uint32_t _b, Tp _distance) { m_edges.push_back({_a, _b, _distance}); }
        void setDistance(uint32_t _i, Tp _distance = 0) { m_distances[_i] = _distance; }
        template <bool GetPath = false>
        bool calc() {
            if constexpr (GetPath) m_from.resize(m_vertexNum, -1);
            uint32_t lastUpdate = -1;
            for (uint32_t i = 0; i < m_vertexNum && lastUpdate == i - 1; i++)
                for (uint32_t index = 0; index < m_edges.size(); index++)
                    if (auto &[from, to, distance] = m_edges[index]; m_distances[from] != m_infiniteDistance && chmin(m_distances[to], m_distances[from] + distance)) {
                        lastUpdate = i;
                        if constexpr (GetPath) m_from[to] = index;
                    }
            return lastUpdate != m_vertexNum - 1;
        }
        std::vector<uint32_t> getPath_edge(uint32_t _target) const {
            std::vector<uint32_t> path;
            for (uint32_t cur = _target; ~m_from[cur]; cur = m_edges[m_from[cur]].from) path.push_back(m_from[cur]);
            std::reverse(path.begin(), path.end());
            return path;
        }
        std::vector<uint32_t> getPath_vertex(uint32_t _target) const {
            std::vector<uint32_t> path;
            path.push_back(_target);
            for (uint32_t cur = _target; ~m_from[cur];) path.push_back(cur = m_edges[m_from[cur]].from);
            std::reverse(path.begin(), path.end());
            return path;
        }
    };
}
```

----------------

#### 最大流

```cpp
namespace Dinic {
    template<typename Tp>
    struct Dinic {
        struct RawEdge {
            uint32_t from, to;
            Tp cap;
        };

        struct Edge {
            uint32_t to, rev;
            Tp cap;

            bool operator>(const Edge &other) const { return cap > other.cap; }
        };

        std::vector<RawEdge> m_rawEdges;
        std::vector<Edge> m_edges;
        std::vector<uint32_t> m_starts;
        uint32_t m_vertexNum;

        Dinic(uint32_t _vertexNum, uint32_t __edgeNum);

        void addEdge(uint32_t __a, uint32_t __b, Tp __cap) { m_rawEdges.push_back({__a, __b, __cap}); }

        void prepare() {
            for (auto &[from, to, cap]: m_rawEdges)
                if (from != to) {
                    m_starts[from + 1]++;
                    m_starts[to + 1]++;
                }
            std::partial_sum(m_starts.begin(), m_starts.end(), m_starts.begin());
            m_edges.resize(m_starts.back());
            uint32_t cursor[m_vertexNum];
            std::copy(m_starts.begin(), m_starts.begin() + m_vertexNum, cursor);
            for (auto &[from, to, cap]: m_rawEdges)
                if (from != to) {
                    m_edges[cursor[from]] = Edge{to, cursor[to], cap};
                    m_edges[cursor[to]++] = Edge{from, cursor[from]++, 0};
                }
        }

        template<typename _Compare = std::greater<Edge>>
        void prepareSorted(_Compare __comp = _Compare()) {
            prepare();
            for (uint32_t i = 0; i < m_vertexNum; i++) {
                uint32_t start = m_starts[i], end = m_starts[i + 1];
                std::sort(m_edges.begin() + start, m_edges.begin() + end, __comp);
                for (uint32_t j = start; j < end; j++) m_edges[m_edges[j].rev].rev = j;
            }
        }

        Tp calc(uint32_t _source, uint32_t _target, Tp _infiniteCap = std::numeric_limits<Tp>::max() / 2) {
            uint32_t queue[m_vertexNum], depth[m_vertexNum], it[m_vertexNum], end[m_vertexNum];
            Tp res = 0;
            for (uint32_t i = 0; i < m_vertexNum; i++) end[i] = m_starts[i + 1];
            auto dfs = [&](auto self, uint32_t i, Tp _cap) {
                if (i == _target || !_cap) return _cap;
                Tp flow = 0, f;
                for (uint32_t &cur = it[i]; cur != end[i]; cur++)
                    if (auto &[to, rev, cap] = m_edges[cur]; depth[i] + 1 == depth[to] &&
                                                             (f = self(self, to, std::min(_cap, cap))))
                        if (flow += f, _cap -= f, cap -= f, m_edges[rev].cap += f; !_cap) break;
                return flow;
            };
            while (true) {
                std::fill(depth, depth + m_vertexNum, -1);
                uint32_t head = 0, tail = 0;
                depth[_source] = 0;
                queue[tail++] = _source;
                while (head < tail)
                    for (uint32_t from = queue[head++], cur = m_starts[from], end = m_starts[from + 1];
                         cur < end; cur++)
                        if (auto &[to, rev, cap] = m_edges[cur]; cap && chmin(depth[to], depth[from] + 1))
                            queue[tail++] = to;
                if (!~depth[_target]) break;
                for (uint32_t i = 0; i < m_vertexNum; i++) it[i] = m_starts[i];
                while (Tp flow = dfs(dfs, _source, _infiniteCap)) res += flow;
            }
            return res;
        }
    };

    template<typename Tp>
    Dinic<Tp>::Dinic(uint32_t _vertexNum, uint32_t _edgeNum) : m_starts(_vertexNum + 1, 0),
                                                               m_vertexNum(_vertexNum) {
        m_rawEdges.reserve(_edgeNum);
    }
}
```

### 树

#### 线段树

```cpp
class segmentTree {
    int mod = 0x3f3f3f3f;
    int a[100010];

    struct Segment_Tree {
        ll sum, add, mul;
        int l, r;
    } s[100010 * 4];

    void update(int pos) {
        s[pos].sum = (s[pos << 1].sum + s[pos << 1 | 1].sum) % mod;
        return;
    }

    void pushdown(int pos) { //pushdown的维护
        s[pos << 1].sum = (s[pos << 1].sum * s[pos].mul + s[pos].add * (s[pos << 1].r - s[pos << 1].l + 1)) % mod;
        s[pos << 1 | 1].sum =
                (s[pos << 1 | 1].sum * s[pos].mul + s[pos].add * (s[pos << 1 | 1].r - s[pos << 1 | 1].l + 1)) % mod;

        s[pos << 1].mul = (s[pos << 1].mul * s[pos].mul) % mod;
        s[pos << 1 | 1].mul = (s[pos << 1 | 1].mul * s[pos].mul) % mod;

        s[pos << 1].add = (s[pos << 1].add * s[pos].mul + s[pos].add) % mod;
        s[pos << 1 | 1].add = (s[pos << 1 | 1].add * s[pos].mul + s[pos].add) % mod;

        s[pos].add = 0;
        s[pos].mul = 1;
        return;
    }

    void build_tree(int pos, int l, int r) { //建树
        s[pos].l = l;
        s[pos].r = r;
        s[pos].mul = 1;

        if (l == r) {
            s[pos].sum = a[l] % mod;
            return;
        }

        int mid = (l + r) >> 1;
        build_tree(pos << 1, l, mid);
        build_tree(pos << 1 | 1, mid + 1, r);
        update(pos);
        return;
    }

    void mul(int pos, int x, int y, int k) { //区间乘法
        if (x <= s[pos].l && s[pos].r <= y) {
            s[pos].add = (s[pos].add * k) % mod;
            s[pos].mul = (s[pos].mul * k) % mod;
            s[pos].sum = (s[pos].sum * k) % mod;
            return;
        }

        pushdown(pos);
        int mid = (s[pos].l + s[pos].r) >> 1;
        if (x <= mid) mul(pos << 1, x, y, k);
        if (y > mid) mul(pos << 1 | 1, x, y, k);
        update(pos);
        return;
    }

    void add(int pos, int x, int y, int k) { //区间加法
        if (x <= s[pos].l && s[pos].r <= y) {
            s[pos].add = (s[pos].add + k) % mod;
            s[pos].sum = (s[pos].sum + k * (s[pos].r - s[pos].l + 1)) % mod;
            return;
        }

        pushdown(pos);
        int mid = (s[pos].l + s[pos].r) >> 1;
        if (x <= mid) add(pos << 1, x, y, k);
        if (y > mid) add(pos << 1 | 1, x, y, k);
        update(pos);
        return;
    }

    ll AskRange(int pos, int x, int y) { //区间询问
        if (x <= s[pos].l && s[pos].r <= y) {
            return s[pos].sum;
        }
        pushdown(pos);
        ll val = 0;
        int mid = (s[pos].l + s[pos].r) >> 1;
        if (x <= mid) val = (val + AskRange(pos << 1, x, y)) % mod;
        if (y > mid) val = (val + AskRange(pos << 1 | 1, x, y)) % mod;
        return val;
    }
};
```

---------

#### 字典树

```cpp
class Trie {
public:
    int nCnt;
    vector<vector<int>> ch;
    vector<int> f;

    int newNode() {
        ch.emplace_back(26, -1);
        f.push_back(0);
        return nCnt++;
    }

    void add(string &s) {
        int now = 0;
        for (char i: s) {
            f[now]++;
            int c = i - 'a';
            if (ch[now][c] == -1) ch[now][c] = newNode();
            now = ch[now][c];
        }
        f[now]++;
    }

    int query(string &s) {
        int now = 0, ret = 0;
        for (char i: s) {
            if (now > 0) ret += f[now];
            int c = i - 'a';
            now = ch[now][c];
        }
        ret += f[now];
        return ret;
    }
};
```

------

#### 树状数组

```cpp
template<typename T>
struct FenWick {
    int N;
    vector<T> arr;
    FenWick(int sz): N(sz), arr(sz + 1, 0) {}
    void update(int pos, T val) {
        for (; pos <= N;pos |= (pos + 1)) {
            arr[pos] += val;
        }
    }
    // 获取 [1, pos] 的和
    T get(int pos) {
        T ret = 0;
        for (; pos > 0; --pos) {
            ret += arr[pos];
            pos &= (pos + 1);
        }
        return ret;
    }
    // 获取 [l, r] 的和
    T query(int l, int r) {
        return get(r) - get(l - 1);
    }
};
```

-------

#### 珂朵莉树

```cpp
namespace Chtholly {
    struct Node {
        int l, r;
        mutable int v;

        Node(int il, int ir, int iv) : l(il), r(ir), v(iv) {}

        bool operator<(const Node &arg) const {
            return l < arg.l;
        }
    };

    class Tree {
    protected:
        auto split(int pos) {
            if (pos > _sz) return odt.end();
            auto it = --odt.upper_bound(Node{pos, 0, 0});
            if (it->l == pos) return it;
            auto tmp = *it;
            odt.erase(it);
            odt.insert({tmp.l, pos - 1, tmp.v});
            return odt.insert({pos, tmp.r, tmp.v}).first;
        }

    public:
        Tree(int sz, int ini = 1) : _sz(sz), odt({Node{1, sz, ini}}) {}

        virtual void assign(int l, int r, int v) {
            auto itr = split(r + 1), itl = split(l);
            // operations here
            odt.erase(itl, itr);
            odt.insert({l, r, v});
        }

    protected:
        int _sz;
        set<Node> odt;
    };
}
```

------------

#### ST表

```cpp
template<typename iter, typename BinOp>
class SparseTable {
    using T = typename remove_reference<decltype(*declval<iter>())>::type;
    vector<vector<T>> arr;
    BinOp binOp;
public:
    SparseTable(iter begin, iter end, BinOp binOp) : arr(1), binOp(binOp) {
        int n = distance(begin, end);
        arr.assign(32 - __builtin_clz(n), vector<T>(n));
        arr[0].assign(begin, end);
        for (int i = 1; i < arr.size(); ++i) {
            for (int j = 0; j < n - (1 << i) + 1; ++j) {
                arr[i][j] = binOp(arr[i - 1][j], arr[i - 1][j + (1 << (i - 1))]);
            }
        }
    }

    T query(int lPos, int rPos) {
        int h = floor(log2(rPos - lPos + 1));
        return binOp(arr[h][lPos], arr[h][rPos - (1 << h) + 1]);
    }
};
```

------------

#### 后缀数组

```cpp
class SuffixArray {
private:
    void radixSort(int n, int m, int w, vector<int> &sa, vector<int> &rk, vector<int> &bucket, vector<int> &idx) {
        fill(all(bucket), 0);
        for (int i = 0; i < n; ++i) idx[i] = sa[i];
        for (int i = 0; i < n; ++i) ++bucket[rk[idx[i] + w]];
        for (int i = 1; i < m; ++i) bucket[i] += bucket[i - 1];

        for (int i = n - 1; i >= 0; --i) sa[--bucket[rk[idx[i] + w]]] = idx[i];
        fill(all(bucket), 0);
        for (int i = 0; i < n; ++i) idx[i] = sa[i];
        for (int i = 0; i < n; ++i) ++bucket[rk[idx[i]]];
        for (int i = 1; i < m; ++i) bucket[i] += bucket[i - 1];
        for (int i = n - 1; i >= 0; --i) sa[--bucket[rk[idx[i]]]] = idx[i];
    }

public:
    SuffixArray(const string &s) :
            n(s.length() + 1),
            m(max((int) s.length() + 1, 300)),
            rk(2, vector<int>((s.length() + 1) << 1)),
            bucket(max((int) s.length() + 1, 300)),
            idx(s.length() + 1),
            sa(s.length() + 1),
            ht(s.length()) {

        for (int i = 0; i < n; ++i) ++bucket[rk[0][i] = s[i]];
        for (int i = 1; i < m; ++i) bucket[i] += bucket[i - 1];
        for (int i = n - 1; i >= 0; --i) sa[--bucket[rk[0][i]]] = i;
        int pre = 1;
        int cur = 0;
        for (int w = 1; w < n; w <<= 1) {
            swap(cur, pre);
            radixSort(n, m, w, sa, rk[pre], bucket, idx);
            for (int i = 1; i < n; ++i) {
                if (rk[pre][sa[i]] == rk[pre][sa[i - 1]] and rk[pre][sa[i] + w] == rk[pre][sa[i - 1] + w]) {
                    rk[cur][sa[i]] = rk[cur][sa[i - 1]];
                } else {
                    rk[cur][sa[i]] = rk[cur][sa[i - 1]] + 1;
                }
            }
        }
        for (int i = 0, k = 0; i < n - 1; ++i) {
            if (k) --k;
            while (s[i + k] == s[sa[rk[cur][i] - 1] + k]) ++k;
            ht[rk[cur][i] - 1] = k;
        }
    }

    vector<int> sa;
    vector<int> ht;
private:
    int n, m;
    vector<vector<int>> rk;
    vector<int> bucket, idx;
};
```

### 字符串

#### KMP

```cpp
class KMP {
public:
    /**
     * @brief 统计目标串中有多少个模式串
     * @param target 目标字符串
     * @param pattern 模式字符串
     * */
    static int solve(string &target, string &pattern) {
        int ans = 0;
        int idxTarget = 0, idxPattern = 0;
        vector<int> next(std::move(_prefix(pattern)));
        while (idxTarget < target.length()) {
            while (idxPattern != -1 and pattern[idxPattern] != target[idxTarget]) {
                idxPattern = next[idxPattern];
            }
            ++idxTarget;
            ++idxPattern;
            if (idxPattern >= pattern.length()) {
                ++ans;
                idxPattern = next[idxPattern];
            }
        }
        return ans;
    }

private:
    static vector<int> _prefix(const string &pattern) {
        int i = 0, j = -1;
        vector<int> ret(pattern.length() + 1, -1);
        while (i < pattern.length()) {
            while (j != -1 and pattern[i] != pattern[j]) j = ret[j];
            if (pattern[++i] == pattern[++j]) {
                ret[i] = ret[j];
            } else {
                ret[i] = j;
            }
        }
        return ret;
    }
};
```

----------

#### 字符串哈希

```cpp
class StringHash {
public:
    static unsigned BKDR(const std::string &str) {
        unsigned seed = 131; // 31 131 1313 13131 131313 etc..
        unsigned hash = 0;
        for (auto c: str) {
            hash = hash * seed + c;
        }
        return (hash & 0x7FFFFFFF);
    }

    static unsigned AP(const std::string &str) {
        unsigned hash = 0;
        for (int i = 0; i < str.length(); ++i) {
            if (i & 1) {
                hash ^= (~((hash << 11) ^ str[i] ^ (hash >> 5)));
            } else {
                hash ^= ((hash << 7) ^ str[i] ^ (hash >> 3));
            }
        }
        return (hash & 0x7FFFFFFF);
    }

    static unsigned DJB(const std::string &str) {
        unsigned hash = 5381;
        for (auto c: str) {
            hash += (hash << 5) + c;
        }
        return (hash & 0x7FFFFFFF);
    }

    static unsigned JS(const std::string &str) {
        unsigned hash = 1315423911;
        for (auto c: str) hash ^= ((hash << 5) + c + (hash >> 2));
        return (hash & 0x7FFFFFFF);
    }

    static unsigned SDBM(const std::string &str) {
        unsigned hash = 0;
        for (auto c: str) hash = c + (hash << 6) + (hash << 16) - hash;
        return (hash & 0x7FFFFFFF);
    }

    static unsigned PJW(const std::string &str) {
        auto bits_in_unsigned_int = (unsigned) (sizeof(unsigned) * 8);
        auto three_quarters = (unsigned) (bits_in_unsigned_int * 3 / 4);
        auto one_eighth = (unsigned) (bits_in_unsigned_int / 8);
        unsigned high_bits = (unsigned) (0xFFFFFFFF) << (bits_in_unsigned_int - one_eighth);
        unsigned hash = 0;
        unsigned test = 0;
        for (auto c: str) {
            hash = (hash << one_eighth) + c;
            if ((test = hash & high_bits) != 0) {
                hash = (hash ^ (test >> three_quarters)) & (~high_bits);
            }
        }
        return (hash & 0x7FFFFFFF);
    }

    static unsigned ELF(const std::string &str) {
        unsigned hash = 0, x = 0;
        for (auto c: str) {
            hash = (hash << 4) + c;
            if ((x = hash & 0xF0000000ll) != 0) {
                hash ^= (x >> 24);
                hash &= (~x);
            }
        }
        return (hash & 0x7FFFFFFF);
    }
};
```

----------

#### AC自动机

```cpp
namespace Automaton {
    struct ACNode {
        vector<int> nex;
        int fail;
        int cnt;

        ACNode() : nex(26, 0), cnt(0), fail(0) {}
    };

    class AC {
    public:
        AC() : nodes(1) {}

        void insert(const string &arg) {
            int cur = 0;
            for (auto &c: arg) {
                int to = c - 'a';
                if (!nodes[cur].nex[to]) {
                    nodes[cur].nex[to] = (int) nodes.size();
                    nodes.emplace_back();
                }
                cur = nodes[cur].nex[to];
            }
            nodes[cur].cnt++;
        }

        void build() {
            queue<int> Q;
            for (int i = 0; i < 26; ++i) {
                if (nodes[0].nex[i]) {
                    Q.push(nodes[0].nex[i]);
                }
            }
            while (!Q.empty()) {
                int cur = Q.front();
                Q.pop();
                for (int i = 0; i < 26; ++i) {
                    if (nodes[cur].nex[i]) {
                        nodes[nodes[cur].nex[i]].fail = nodes[nodes[cur].fail].nex[i];
                        Q.push(nodes[cur].nex[i]);
                    } else {
                        nodes[cur].nex[i] = nodes[nodes[cur].fail].nex[i];
                    }
                }
            }
        }

        int query(const string &arg) {
            int cur = 0, ans = 0;
            for (auto &c: arg) {
                cur = nodes[cur].nex[c - 'a'];
                for (int j = cur; j and nodes[j].cnt != -1; j = nodes[j].fail) {
                    ans += nodes[j].cnt;
                    nodes[j].cnt = -1;
                }
            }
            return ans;
        }

    private:
        vector<ACNode> nodes;
    };
}
```

### Tricks

#### fastIO

```cpp
#define fastIO() ios::sync_with_stdio(false),cin.tie(nullptr),cout.tie(nullptr)
```

---------------

#### least power of 2 and greater power of 2

```cpp
//摘自dianhsu大佬
int leastPowerOfTwo(int val){
    return 32 - __builtin_clz(val - 1);
}
int greaterPowerOfTwo(int val){
    return 32 - __builtin_clz(val);
}
```

----------

#### 一些方便IO的重构

```cpp
template<typename typC, typename typD>
istream &operator>>(istream &cin, pair<typC, typD> &a) { return cin >> a.first >> a.second; }

template<typename typC>
istream &operator>>(istream &cin, vector<typC> &a) {
    for (auto &x: a) cin >> x;
    return cin;
}

template<typename typC, typename typD>
ostream &operator<<(ostream &cout, const pair<typC, typD> &a) { return cout << a.first << ' ' << a.second; }

template<typename typC, typename typD>
ostream &operator<<(ostream &cout, const vector<pair<typC, typD>> &a) {
    for (auto &x: a) cout << x << '\n';
    return cout;
}

template<typename typC>
ostream &operator<<(ostream &cout, const vector<typC> &a) {
    int n = a.size();
    if (!n) return cout;
    cout << a[0];
    for (int i = 1; i < n; i++) cout << ' ' << a[i];
    return cout;
}
```

