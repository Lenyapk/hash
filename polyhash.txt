struct Resid {
    ll v, M;
    Resid() : v(0), M(1) {};
    Resid(ll val, ll m) : M(m), v(val % m) {
    };
};
Resid operator+(const Resid& a, const Resid& b) {
    int res = a.v + b.v;
    return { res < a.M ? res : res - a.M , a.M};
}
Resid operator-(const Resid& a, const Resid& b) {
    int res = a.v - b.v;
    return { res >= 0 ? res : res + a.M, a.M };
}
Resid operator*(const Resid& a, const Resid& b) {
    return { ((ll)a.v * b.v % a.M), a.M };
}
bool operator==(const Resid& a, const Resid& b) {
    return a.v == b.v;
}
bool operator<(const Resid& a, const Resid& b) {
    return a.v < b.v;
}
struct PoliHash {
    ll n, p, m;
    vc<Resid> prefHash;
    vc<Resid> powP;
    PoliHash(int sz, vc<ll> a, ll M, ll P) {
        n = sz, p = P, m = M;
        prefHash.resize(sz+1);
        prefHash[0] = { 0, M };
        for (int i = 0; i < n; i++) {
            prefHash[i + 1] = prefHash[i] * Resid(P, M) + Resid(a[i], M);
        }
        powP.resize(sz + 1);
        powP[0] = Resid({ 1, M });
        for (int i = 0; i < n; i++)
            powP[i + 1] = powP[i] * Resid(P,M);
    }
    PoliHash(int sz, str a, ll M, ll P) {
        n = sz, p = P, m = M;
        prefHash.resize(sz + 1);
        prefHash[0] = { 0, M };
        for (int i = 0; i < n; i++) {
            prefHash[i + 1] = prefHash[i] * Resid(P, M) + Resid(a[i], M);
        }
        powP.resize(sz + 1);
        powP[0] = Resid({ 1, M });
        for (int i = 0; i < n; i++)
            powP[i + 1] = powP[i] * Resid(P, M);
    }
    Resid Get(int l, int len) {
        return Resid(prefHash[l + len] - prefHash[l] * powP[len]);
    }
};
struct PoliHash2D {
    ll n, m, p, q, mod;
    vc<vc<Resid>> Hash;
    vc<Resid> powP;
    vc<Resid> powQ;
    PoliHash2D(int a, int b, vc<vc<ll>> mat, ll P, ll Q, ll M) {
        n = a, m = b, p = P, q = Q, mod = M;
        Hash.assign(n + 1, vc<Resid>(m + 1, { 0,mod }));
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= m; j++) {
                Hash[i][j] = Hash[i][j - 1] * Resid(Q, M) + Hash[i - 1][j] * Resid(P, M) - Hash[i - 1][j - 1] * Resid(Q, M) * Resid(P, M) + Resid(mat[i - 1][j - 1], M);
            }
        }
        powP.resize(n + 1);
        powQ.resize(m + 1);
        powP[0] = { 1, M };
        powQ[0] = { 1, M };
        for (int i = 1; i <= n; i++) powP[i] = powP[i - 1] * Resid(P, M);
        for (int i = 1; i <= m; i++) powQ[i] = powQ[i - 1] * Resid(Q, M);
    }
    PoliHash2D(int a, int b, vc<str> mat, ll P, ll Q, ll M) {
        n = a, m = b, p = P, q = Q, mod = M;
        Hash.assign(n + 1, vc<Resid>(m + 1, { 0,mod }));
        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= m; j++) {
                Hash[i][j] = Hash[i][j - 1] * Resid(Q, M) + Hash[i - 1][j] * Resid(P, M) - Hash[i - 1][j - 1] * Resid(Q, M) * Resid(P, M) + Resid(mat[i - 1][j - 1], M);
            }
        }
        powP.resize(n + 1);
        powQ.resize(m + 1);
        powP[0] = { 1, M };
        powQ[0] = { 1, M };
        for (int i = 1; i <= n; i++) powP[i] = powP[i - 1] * Resid(P, M);
        for (int i = 1; i <= m; i++) powQ[i] = powQ[i - 1] * Resid(Q, M);
    }
    Resid get(ll x, ll y, ll k, ll d) {
        return Hash[x + k][y + d] - Hash[x][y + d] * powP[k] - Hash[x + k][y] * powQ[d] + Hash[x][y] * powP[k] * powQ[d];
    }
};
