# Mod Int

{% code lineNumbers="true" %}
```cpp
template <typename T>
T binpow(T a, i64 k)
{
    T res = 1;
    for (; k; k >>= 1, a = a * a)
    {
        if (k & 1)
        {
            res = res * a;
        }
    }
    return res;
}

struct mint
{
    static int  P;
    static void setMod(int mod)
    {
        P = mod;
    }

    int n;
    mint()
        : n{}
    {
    }
    mint(i64 n)
        : n{norm(n % P)}
    {
    }

    int norm(int n)
    {
        if (n < 0)
        {
            n += P;
        }
        if (n >= P)
        {
            n -= P;
        }
        return n;
    }

    explicit operator int()
    {
        return n;
    }

    mint operator-()
    {
        return mint(P - n);
    }
    mint inv()
    {
        assert(n != 0);
        return binpow(*this, P - 2);
    }

    friend mint operator+(mint lhs, mint rhs)
    {
        return mint(1LL * lhs.n + rhs.n);
    }
    friend mint operator-(mint lhs, mint rhs)
    {
        return lhs + -rhs;
    }
    friend mint operator*(mint lhs, mint rhs)
    {
        return mint(1LL * lhs.n * rhs.n);
    }
    friend mint operator/(mint lhs, mint rhs)
    {
        return lhs * rhs.inv();
    }
};
int mint::P = 1e9 + 7;

struct Comb
{
    std::vector<mint> fac, facinv;
    Comb(int n)
        : fac(n), facinv(n)
    {
        fac[0] = 1;
        for (int i = 1; i < n; i++)
        {
            fac[i] = fac[i - 1] * i;
        }

        facinv[n - 1] = fac[n - 1].inv();
        for (int i = n - 2; i >= 0; i--)
        {
            facinv[i] = facinv[i + 1] * (i + 1);
        }
    }

    mint nPr(int n, int r)
    {
        return (r > n || r < 0) ? 0 : fac[n] * facinv[n - r];
    }
    mint nCr(int n, int r)
    {
        return (r > n || r < 0) ? 0 : fac[n] * facinv[r] * facinv[n - r];
    }
};
// Comb comb(int(1e7));
```
{% endcode %}
