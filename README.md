::: {.titlepage}
:::

# 数学

## 数论

### 扩展欧几里得

计算 $ax+by=gcd(a,b)$

    void exgcd(ll a,ll b,ll &x,ll &y){
        if(b==0){
            x=1,y=0;return;
        }
        exgcd(b,a%b,x,y);
        ll t=x;x=y;y=t-a/b*y;
    }

### 快速幂

非递归实现

        ll qpow(ll a,ll p,ll mod){
            ll ans=1;
            a%=mod;
            while(p){
                if(p&1){
                    ans=(ans*a)%mod;
                }
                a=(a*a)%mod;
                p>>=1;
            }
            return ans%mod;
        }

### 乘法逆元

$ax\equiv1(mod\ m)$ 则x是a在模m意义下的逆元

a与m互质时$ax+my=gcd(a,m)=1$，求得的最小的正整数x是逆元

        ll inv(ll a,ll p){
            ll x,y;
            exgcd(a,p,x,y);
            return (x+p)%p;
        } 

费马小定理$a^{p-1}=1\ (mod\ p)$($p$是质数)

故$a\cdot a^{p-2}=1$

    ll inv(ll a,ll p){
        return qpow(a,p-2);
    }

对于同一个p，求1 n中所有整数在模p意义下的逆元

$O(n)$

    int main(){
        scanf("%lld%lld",&n,&p);
        inv[1]=1;
        puts("1");
        for(int i=2;i<=n;++i){
            printf("%lld\n",inv[i]=p-p/i*inv[p%i]%p);
        }
        return 0;
    }

### 中国剩余定理

方程组$x\equiv a_i(mod\ m_i) \quad 1<=i<=n$其中$a_i$, $m_i$互质

$ans\equiv\sum\limits_i^na_i\cdot M_i\cdot inv(M_i,m_i)\ (mod\ M)$

$M=\prod\limits_i^nm_i\quad M_i=M/m_i$

        int main(){
            int n;
            ll Mpai=1;
            scanf("%d",&n);
            for(int i=1;i<=n;++i){
                scanf("%lld%lld",&mod[i],&a[i]);
                Mpai*=mod[i];
            }
            ll ans=0;
            for(int i=1;i<=n;++i){
                ll Mi=Mpai/mod[i];
                ans+=a[i]*Mi*inv(Mi,mod[i]);
            }
            printf("%lld",ans%Mpai);
            return 0;
        }

### 扩展中国剩余定理

    const int N=1e5+5;
    typedef long long ll;
    ll b[N],a[N],n;
    ll exgcd(ll a,ll b,ll &x,ll &y){
        if(b==0){
            x=1,y=0;return a;
        }
        ll tmp=exgcd(b,a%b,x,y);
        ll t=x;x=y;y=t-a/b*y;
        return tmp;
    }
    ll slowmul(ll a,ll b,ll mod){
        ll ans=0;
        while(b){
            if(b&1){
                ans=(ans+a)%mod;
            }
            a=(a+a)%mod;
            b>>=1;
        }
        return ans;
    }
    int main(){
        scanf("%d",&n);
        for(int i=1;i<=n;++i){
            scanf("%lld%lld",&b[i],&a[i]);
        }
        ll ans=a[1],M=b[1],x,y;
        for(int i=2;i<=n;++i){
            ll rs=((a[i]-ans)%b[i]+b[i])%b[i];
            ll g=exgcd(M,b[i],x,y);
            x=slowmul(x,rs/g,b[i]);
            ans+=M*x;
            M*=b[i]/g;
            ans=(ans+M)%M;
        }
        printf("%lld\n",ans);
    }   

### 卢卡斯定理

    ll C(int n,int m){ 
        if(m>n){ 
            return 0; 
        } 
        return fac[n]*inv(fac[m]*fac[n−m],p)%p; 
    } 
    ll lucas(int n,int m){ 
        if(m==0) 
            return 1;   
        return C(n%p,m%p)*lucas(n/p,m/p)%p; 
    }

### 欧拉筛

    const ll N=1e9,M=1e8; 
    ll prime[M],cnt;
    bool nisp[N];

    void findp(ll n){
        for(ll i=2;i<=n;++i){
            if(!nisp[i]){
                prime[++cnt]=i;
            }
            for(ll j=1;j<=cnt&&i*prime[j]<=n;++j){
                nisp[i*prime[j]]=1;
                if(i%prime[j]==0){
                    break;
                }
            }
        }
    }

### BSGS

    typedef long long ll;
    unordered_map<ll,ll> mp;
    int main(){
        ll p,a,b;
        scanf("%lld%lld%lld",&p,&a,&b);
        ll sqp=ceil(sqrt(p));//square root of p
        ll rs=b,ap=1,ls=1;//rs/ls: right/left side
        //ap: sqp-th power of a
        //mp[sqp]=0;
        for(int i=1;i<=sqp;++i){
            rs=(rs*a)%p;
            ap=(ap*a)%p;
            mp[rs]=i;
        }
        for(int i=1;i<=sqp;++i){
            ls=(ls*ap)%p;// ls=a^{i*\sqrt{p}}
            if(mp[ls]){
                printf("%lld\n",((i*sqp-mp[ls])%p+p)%p);
                return 0;
            }
        }
        puts("no solution");
        return 0;
    }

### 欧拉函数

由唯一分解定理，设 $n = \prod_{i=1}^{n}p_i^{k_i}$，其中 $p_i$ 是质数，有
$\varphi(n) = n \times \prod_{i = 1}^s{\dfrac{p_i - 1}{p_i}}$

        int phi(int x)
        {
            int ans=1;
            for(int i=1;prime[i]*prime[i]<=x;++i)
            {
                if(x%prime[i]==0)
                {
                    ans*=prime[i]-1;x/=prime[i];
                    while(x%prime[i]==0)
                        ans*=prime[i],x/=prime[i];
                }
            }
            if(x>1) ans*=x-1;
            return ans;
        }

欧拉定理：若 $\gcd(a, m) = 1$，则 $a^{\varphi(m)} \equiv 1 \pmod{m}$。

### 数论分块

$\sum_i^n k\space mod \space i=\sum_i^n k-i\lfloor\frac{k}{i} \rfloor =nk-\sum_i^n i\lfloor\frac{k}{i}\rfloor$(luoguP2261)

        int main(){
        ll n,k;
        scanf("%lld%lld",&n,&k);
        ll ans=n*k;
        //计算 \sum_i^n [k/i] * f(i) 
        //这里是计算  \sum_i^n [k/i] * i  
        for(ll l=1,r;l<=n;l=r+1){
            if(k/l!=0) r=min((k/(k/l)),n);
            else r=n;
            //do something 
            ans-=(k/l)*(l+r)*(r-l+1)>>1;
        }
        printf("%lld\n",ans);
        return 0;
    }

### 二次剩余

求解$x^2\equiv n(mod p)$，$p$是奇素数。

Cipolla算法

    typedef long long ll;
    ll w,mod;
    struct cpx{
        ll real,imag;
        cpx(ll _r=0,ll _i=0):real(_r),imag(_i){}
    };
    inline bool operator == (const cpx &x,const cpx &y){
        return x.real==y.real&&x.imag==y.imag;
    }
    inline cpx operator * (const cpx &x,const cpx &y){
        return cpx((x.real*y.real+w*x.imag%mod*y.imag)%mod,
                    (x.imag*y.real+x.real*y.imag)%mod);
    }
    cpx power(cpx x,ll k){
        cpx res=1;//real=1,imag=0 
        while(k){
            if(k&1) res=res*x;
            x=x*x;
            k>>=1;
        }
        return res;
    }
    bool isresidue(ll x){
        return power(x,(mod-1)>>1)==1;
    }
    pair<ll,ll> solve(ll n){
        ll a=rand()%mod;
        while(a==0||isresidue((a*a+mod-n)%mod)) a=rand()%mod;
        w=(a*a+mod-n)%mod;
        ll x=power(cpx(a,1),(mod+1)>>1).real;
        return make_pair(x,mod-x);
    }
    int main(){
        srand(time(0));
        int T;scanf("%d",&T);
        while(T--){
            ll n;scanf("%lld%lld",&n,&mod);
            if(n==0){
                puts("0");continue;
            }
            auto ans=solve(n);
            if(power(n,(mod-1)>>1).real==mod-1) puts("Hola!");//无解
            else{
                if(ans.first==ans.second){
                    printf("%lld\n",ans.first);continue;
                }
                if(ans.first>ans.second) swap(ans.first,ans.second);
                printf("%lld %lld\n",ans.first,ans.second);
            } 
        }
        return 0;
    }

## 组合数学

### 康托展开

    ll n,tr[N],a[N],fac[N];
    #define lowbit(x) ((x)&-(x))
    void add(ll x,ll v){
        while(x<=n){
            tr[x]+=v;
            x+=lowbit(x);
        }
    }
    ll ask(int x){
        ll ret=0;
        while(x>0){
            ret+=tr[x];
            x-=lowbit(x);
        }
        return ret;
    }
    int main(){
        scanf("%lld",&n);
        fac[0]=1;
        for(int i=1;i<=n;++i){
            scanf("%lld",&a[i]);
            add(i,1);
            fac[i]=fac[i-1]*i%M;
        }
        ll ans=1;
        for(int i=1;i<n;++i){
            ans=(ans+ask(a[i]-1)*fac[n-i]%M)%M;
            add(a[i],-1);
        }
        printf("%lld",ans);
        return 0;
    } 

### LGV引理

Lindström--Gessel--Viennot
lemma，可以用来处理**有向无环图**上不相交路径计数等问题。

$\omega(P)$ 表示 $P$
这条路径上所有边的边权之积。（路径计数时，可以将边权都设为
$1$）（事实上，边权可以为生成函数）

$e(u, v)$ 表示 $u$ 到 $v$ 的**每一条路径**$P$ 的 $\omega(P)$ 之和，即
$e(u, v)=\sum\limits_{P:u\rightarrow v}\omega(P)$。

起点集合 $A$，是有向无环图点集的一个子集，大小为 $n$。

终点集合 $B$，也是有向无环图点集的一个子集，大小也为 $n$。

一组 $A\rightarrow B$ 的不相交路径 $S$：$S_i$ 是一条从 $A_i$ 到
$B_{\sigma(S)_i}$ 的路径（$\sigma(S)$ 是一个排列），对于任何
$i\ne j$，$S_i$ 和 $S_j$ 没有公共顶点。

$N(\sigma)$ 表示排列 $\sigma$ 的逆序对个数。

$$M = \begin{bmatrix}e(A_1,B_1)&e(A_1,B_2)&\cdots&e(A_1,B_n)\\
e(A_2,B_1)&e(A_2,B_2)&\cdots&e(A_2,B_n)\\
\vdots&\vdots&\ddots&\vdots\\
e(A_n,B_1)&e(A_n,B_2)&\cdots&e(A_n,B_n)\end{bmatrix}$$

$$\det(M)=\sum\limits_{S:A\rightarrow B}(-1)^{N(\sigma(S))}\prod\limits_{i=1}^n \omega(S_i)$$

其中 $\sum\limits_{S:A\rightarrow B}$ 表示满足上文要求的
$A\rightarrow B$ 的每一组不相交路径 $S$。

路径计数时，$\omega(S_i)=1$,故$\prod\limits_{i=1}^n \omega(S_i)=1$。

例题题意：有一个 $n\times n$ 的棋盘，棋子只能向下向右走，有 $m$
个棋子，一开始第 $i$ 个棋子放在 $(1, a_i)$，最终要到
$(n, b_i)$，路径要两两不相交，求方案数。

观察到如果路径不相交就一定是 $a_i$ 到 $b_i$，因此 LGV 引理中一定有
$\sigma(S)_i=i$，不需要考虑符号问题。边权设为 $1$，直接套用引理即可。

从 $(1, a_i)$ 到 $(n, b_j)$ 的路径条数相当于从 $n-1+b_j-a_i$ 步中选
$n-1$ 步向下走，所以 $e(A_i, B_j)=\binom{n-1+b_j-a_i}{n-1}$。

    const int M=105,N=2e6+6;
    ll fact[N],mt[M][M],a[M],b[M];
    const ll mod=998244353;
    void exgcd(ll a,ll b,ll &x,ll &y){
        if(b==0){
            x=1,y=0;return;
        }
        exgcd(b,a%b,x,y);
        ll t=x;x=y;y=t-a/b*y;
    }
    ll inv(ll a){
        ll x,y;
        exgcd(a,mod,x,y);
        return (x+mod)%mod;
    }
    ll C(ll n,ll m){
        return fact[n]*inv(fact[m])%mod*inv(fact[n-m])%mod;
    }
    int main(){
        int T;scanf("%d",&T);
        fact[0]=1;
        for(int i=1;i<N;++i){
            fact[i]=(fact[i-1]*i)%mod;
        }
        while(T--){
            ll n,m;
            scanf("%lld%lld",&n,&m);
            for(int i=1;i<=m;++i){
                scanf("%lld%lld",&a[i],&b[i]);//(ai,1)->(bi,n)
            }
            for(int i=1;i<=m;++i){
                for(int j=1;j<=m;++j){
                    if(a[i]<=b[j]){
                        mt[i][j]=C(n-1+b[j]-a[i],n-1);
                    }else{
                        mt[i][j]=0;
                    }
                }
            }
            ll ans=1;
            for(int i=1;i<=m;++i){
                for(int j=i+1;j<=m;++j){
                    while(mt[j][i]){
                        ll t=mt[i][i]/mt[j][i];
                        for(int k=i;k<=m;++k){
                            mt[i][k]=(mt[i][k]-t*mt[j][k]%mod+mod)%mod;
                            swap(mt[i][k],mt[j][k]);
                        }
                        ans=mod-ans;
                    }
                }
                ans=ans*mt[i][i]%mod;
            }
            printf("%lld\n",(ans+mod)%mod);
        }
        return 0;
    }

## 数值分析

### 牛顿迭代

    #define eps 0.0001
    double a,b,c,d,a1,b1;
    double f(double x){
        return a*x*x*x+b*x*x+c*x+d;
    }
    double df(double x){
        return a1*x*x+b1*x+c;
    }
    double newton(double l,double r){
        double x=(l+r)/2;
        while(fabs(f(x))>eps){
            x-=f(x)/df(x);
        }
        return x;
    }

### 自适应Simpson积分

    inline double f(double x){
        return (c*x+d)/(a*x+b);//被积函数 
    }
    inline double simpson(double l,double r,double fl,double fr,double fm){
        return (r-l)*(fl+4*fm+fr)/6;
    }
    double asr(double l,double r,double eps,double fl,double fr,double fm,double ans){
        double mid=(l+r)/2;
        double flm=f((l+mid)/2),frm=f((r+mid)/2);
        double sl=simpson(l,mid,fl,fm,flm),sr=simpson(mid,r,fm,fr,frm);
        if(fabs(sl+sr-ans)<=15*eps) return sl+sr+(sl+sr-ans)/15;
        return asr(l,mid,eps/2,fl,fm,flm,sl)+asr(mid,r,eps/2,fm,fr,frm,sr);
    }
    double solve(double al,double ar){     
        double fal=f(al),far=f(ar),fam=f((al+ar)/2);
        return asr(al,ar,1e-6,fal,far,fam,simpson(al,ar,far,far,fam));
    }

### 牛顿插值

设$f[x_0,x_i,\dots,x_k]$为$f(x)$ 的$k$阶差商，那么
$$f[x_0,x_i,\dots,x_k]=\frac{f[x_1,x_2,\dots,x_k]-f[x_0,x_1,\dots,x_{k-1}]}{x_k-x_0}$$

$f(x)$在$x_i$处的0阶差商为$f(x_i)$, 即$f[x_0]=f(x_0)$

给定包含$k+1$个数据点的集合$(x_{0},y_{0}),\ldots ,(x_{k},y_{k})$
如果对于$\forall i,j\in \left\{0,...,k\right\},i\neq j$满足$x_i \neq x_j$，那么应用牛顿插值公式所得到的牛顿插值多项式为

$$N(x):=\sum_{j=0}^{k}f[x_0,\dots,x_j]\cdot n_j(x)$$

其中每个$n_{j}(x)$为牛顿基本多项式（或称插值基函数），其表达式为

$$n_{j}(x):=
\left\{
    \begin{array}{lr}
    \prod\limits_{i=0}^{j-1} (x-x_i)&j>0\\
    1&j=0 \\
    \end{array}
\right.$$

数据点为实数代码

    const int N=1e7+5;
    int n,now;
    double dt[2][N],coe[N],ploy[N],x[N],y[N];
    //dt : x_n 结尾的n个差商
    //ploy[t]: 多项式 \prod_{i=0}^{n-1} (x-x_i) 中 x^t 的x^t系数 (0<=t<n)
    //coe[t](coefficient，系数) : 插值出的多项式的x^t的系数 (0<=t<n) 
    void insert(double xn,double yn)//插入点(xn,yn) 
    {
        x[++n]=xn;
        y[n]=yn,now^=1,dt[now][0]=yn;
        for (int i=1;i<n;i++) 
            dt[now][i]=(dt[now][i-1]-dt[now^1][i-1])/(x[n]-x[n-i]);//维护差商 
        for (int i=n-1;i>=1;i--) ploy[i]=ploy[i-1]-x[n-1]*ploy[i];
        //维护多项式 （多项式要乘以二次项 (x-x_{n-1})，
        //故i次项系数变为 原来i-1次项系数 加上 -x_{n-1}倍的原来i次项的系数） 
        ploy[0]*=-x[n-1];//常数项特殊处理 
        for(register int i=0;i<n;i++) coe[i]+=ploy[i]*dt[now][n-1];//累计答案 
    }
    double fx(double x){
        double res=coe[n-1];
        for(int i=n-2;i>=0;--i){//秦九韶算法 
            res=res*x+coe[i];
        }
        return res;
    }
    int main(){
        x[0]=-1;//初始化，仅仅是为了适应第一次计算 
        ploy[0]=1;
        return 0;
    }

整数带取模

    const int N=2e3+5,mod=998244353;
    typedef long long ll;
    int n,now;
    ll dt[2][N],coe[N],ploy[N],x[N],y[N];
    void exgcd(ll a,ll b,ll &x,ll &y){
        if(b==0){
            x=1,y=0;return;
        }
        exgcd(b,a%b,x,y);
        ll t=x;x=y;y=t-a/b*y;
    }
    ll inv(ll a,ll p){
        ll x,y;
        exgcd(a,p,x,y);
        return (x+p)%p;
    }
    void insert(ll xn,ll yn)
    {
        x[++n]=xn;
        y[n]=yn,now^=1,dt[now][0]=yn;
        for (int i=1;i<n;i++) 
            dt[now][i]=(dt[now][i-1]-dt[now^1][i-1]+mod)%mod*inv((x[n]-x[n-i]+mod)%mod,mod)%mod;
        for (int i=n-1;i>=1;i--)
            ploy[i]=(ploy[i-1]-x[n-1]*ploy[i]%mod+mod)%mod;
        ploy[0]*=-x[n-1];
        ploy[0]=(ploy[0]%mod+mod)%mod;
        for(register int i=0;i<n;i++) coe[i]=(coe[i]+ploy[i]*dt[now][n-1]%mod)%mod;
    }
    ll fx(ll x){
        ll res=coe[n-1];
        for(int i=n-2;i>=0;--i){//秦九韶算法 
            res=(res*x%mod+coe[i])%mod;
        }
        return res;
    }
    int main(){
        x[0]=-1;//初始化，仅仅是为了适应第一次计算 
        ploy[0]=1;
        int n,k,a,b;
        scanf("%d%d",&n,&k);
        for(int i=1;i<=n;++i){
            scanf("%d%d",&a,&b);
            insert(a,b);
        }
        printf("%lld",fx(k));
        return 0;
    }

## 线性代数

### 高斯消元

处理浮点数时

    const double eps=1e-6;
    const int N=100+5;
    double a[N][N];
    int n;
    int gauss(){
        int c,r;
        for(c=1,r=1;c<=n;++c){
            int t=r;
            for(int i=r;i<=n;++i){
                if(fabs(a[i][c])>fabs(a[t][c])){
                    t=i;
                }
            }//选最大的（提高精度+将0交换到下面）
            if(fabs(a[t][c])<eps) continue;
            for(int i=c;i<=n+1;++i) swap(a[t][i],a[r][i]);//交换到上部
            for(int i=n+1;i>=c;--i) a[r][i]/=a[r][c];//行首行列式数值变成1
            for(int i=r+1;i<=n;++i){
                if(fabs(a[i][c])>eps){
                    double d=a[i][c];
                    for(int j=n+1;j>=c;--j){
                        a[i][j]-=a[r][j]*d;
                    }
                }
            }
            ++r;
        }
        if(r<=n){
            for(int i=r;i<=n;++i){
                if(fabs(a[i][n+1])>eps){
                    return 2;//无解
                }
            }
            return 0;//没有唯一解
        }
        for(int i=n;i>0;--i){
            for(int j=i+1;j<=n;++j){
                a[i][n+1]-=a[j][n+1]*a[i][j];
            }
        }
        return 1;
    }

### 矩阵树定理

无向图情况

设 $G$ 是一个有 $n$ 个顶点的无向图。定义度数矩阵 $D(G)$ 为：

$$D_{ii}(G) = \mathrm{deg}(i), D_{ij} = 0, i\neq j$$

设 $\#e(i,j)$ 为点 $i$ 与点 $j$ 相连的边数，并定义邻接矩阵 $A$ 为：

$$A_{ij}(G)=A_{ji}(G)=\#e(i,j), i\neq j$$

定义 Laplace 矩阵（亦称 Kirchhoff 矩阵）$L$ 为：

$$L(G) = D(G) - A(G)$$

记图 $G$ 的所有生成树个数为 $t(G)$。

有向图情况

设 $G$ 是一个有 $n$ 个顶点的有向图。定义出度矩阵 $D^{out}(G)$ 为：

$$D^{out}_{ii}(G) = \mathrm{deg^{out}}(i), D^{out}_{ij} = 0, i\neq j$$

类似地定义入度矩阵 $D^{in}(G)$

设 $\#e(i,j)$ 为点 $i$ 指向点 $j$ 的有向边数，并定义邻接矩阵 $A$ 为：

$$A_{ij}(G)=\#e(i,j), i\neq j$$

定义出度 Laplace 矩阵 $L^{out}$ 为：

$$L^{out}(G) = D^{out}(G) - A(G)$$

定义入度 Laplace 矩阵 $L^{in}$ 为：

$$L^{in}(G) = D^{in}(G) - A(G)$$

记图 $G$ 的以 $r$ 为根的所有根向树形图个数为
$t^{root}(G,r)$。所谓根向树形图，是说这张图的基图是一棵树，所有的边全部指向父亲。

记图 $G$ 的以 $r$ 为根的所有叶向树形图个数为
$t^{leaf}(G,r)$。所谓叶向树形图，是说这张图的基图是一棵树，所有的边全部指向儿子。

定理叙述

矩阵树定理具有多种形式。其中用得较多的是定理 1、定理 3 与定理 4。

定理 1（矩阵树定理，无向图行列式形式） 对于任意的 $i$，都有

$$t(G) = \det L(G)\binom{1,2,\cdots,i-1,i+1,\cdots,n}{1,2,\cdots,i-1,i+1,\cdots,n}$$

其中记号
$L(G)\binom{1,2,\cdots,i-1,i+1,\cdots,n}{1,2,\cdots,i-1,i+1,\cdots,n}$
表示矩阵 $L(G)$ 的第 $1,\cdots,i-1,i+1,\cdots,n$ 行与第
$1,\cdots,i-1,i+1,\cdots,n$
列构成的子矩阵(\*原矩阵去掉第$i$行同时去掉第$i$列（$1\leq i\leq n$），即矩阵的主子式\*)。也就是说，无向图的
Laplace 矩阵具有这样的性质，它的所有 $n-1$ 阶主子式都相等。

定理 2（矩阵树定理，无向图特征值形式）设
$\lambda_1, \lambda_2, \cdots, \lambda_{n-1}$ 为 $L(G)$ 的 $n - 1$
个非零特征值，那么有

$t(G) = \frac{1}{n}\lambda_1\lambda_2\cdots\lambda_{n-1}$

定理 3（矩阵树定理，有向图根向形式） 对于任意的 $k$，都有

$$t^{root}(G,k) = \det L^{out}(G)\binom{1,2,\cdots,k-1,k+1,\cdots,n}{1,2,\cdots,k-1,k+1,\cdots,n}$$

因此如果要统计一张图所有的根向树形图，只要枚举所有的根 $k$ 并对
$t^{root}(G,k)$ 求和即可。

定理 4（矩阵树定理，有向图叶向形式）对于任意的 $k$，都有

$$t^{leaf}(G,k) = \det L^{in}(G)\binom{1,2,\cdots,k-1,k+1,\cdots,n}{1,2,\cdots,k-1,k+1,\cdots,n}$$

在模k整环上的的高斯消元,用辗转相除:

    const int N=105;
    char mp[N][N];
    int n,m;
    typedef long long ll;
    ll L[N][N];
    int id[N][N],cnt;
    const ll mod=1e9;
    int main(){
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i){
            scanf("%s",mp[i]+1);
        }
        for(int i=1;i<=n;++i){
            for(int j=1;j<=m;++j){
                if(mp[i][j]=='.'){
                    id[i][j]=++cnt;
                    if(mp[i-1][j]=='.'){
                        L[id[i][j]][id[i-1][j]]--;
                        L[id[i-1][j]][id[i][j]]--;
                        L[id[i][j]][id[i][j]]++;
                        L[id[i-1][j]][id[i-1][j]]++;
                        
                    }
                    if(mp[i][j-1]=='.'){
                        L[id[i][j]][id[i][j-1]]--;
                        L[id[i][j-1]][id[i][j]]--;
                        L[id[i][j]][id[i][j]]++;
                        L[id[i][j-1]][id[i][j-1]]++;
                    }
                    
                }
            }
        }
        int r=cnt-1;
        for(int i=1;i<=r;++i){
            for(int j=1;j<=r;++j){
                L[i][j]=(L[i][j]+mod)%mod;
            }
        }
        ll ans=1;
        for(int i=1;i<=r;++i){
            for(int j=i+1;j<=r;++j){
                while(L[j][i]){
                    ll t=L[i][i]/L[j][i];
                    for(int k=i;k<=r;++k){
                        L[i][k]=(L[i][k]-t*L[j][k]%mod+mod)%mod;
                        swap(L[i][k],L[j][k]);
                    }
                    ans*=-1;
                }
            }
            ans=ans*L[i][i]%mod;
        }
        printf("%lld",(ans+mod)%mod);
        return 0;
    }    

### 线性基

**向量空间**:给定域$F$，$F$上的向量空间$V$是一个集合，其上定义加法和数乘运算且这两个运算满足八个公理。

**线性无关**:对于向量空间中$V$上$n$个元素的向量组$(\mathbf{v}_1, \ldots, \mathbf{v}_n)$,若存在不全为$0$的数$a_i \in F$满足
$$a_{1}\mathbf{v}_{1}+a_{2}\mathbf {v}_{2}+\ldots +a_{n}\mathbf{v}_{n} = 0$$

则称这$n$个向量线性相关（linearly
dependent），否则称为线性无关（linearly independent）。

**线性组合**:对于向量空间中 V 上 nn
个元素的向量组$(\mathbf{v}_1, \ldots, \mathbf{v}_n)$，其线性组合（linear
combination）是如下形式的向量

$$a_{1}\mathbf{v}_{1} + a_{2}\mathbf {v} _{2}+\ldots +a_{n}\mathbf {v} _{n}$$
​​ 其中$a_1, \ldots, a_n \in F$

一组向量线性无关
$\Leftrightarrow$没有向量可用有限个其他向量的线性组合所表示

**张成**:对于向量空间中 $V$ 上 $n$ 个元素的向量组
$(\mathbf{v}_1, \ldots, \mathbf{v}_n)$，其所有线性组合所构成的集合称为
$(\mathbf{v}_1, \ldots, \mathbf{v}_n)$的张成（span），记为
$\mathrm{span}(\mathbf{v}_1, \ldots, \mathbf{v}_n)$

**基**:若向量空间 $V$ 中向量组 $\mathfrak{B}$ 既是线性无关的又可以张成
$V$，则称其为$V$ 的基（basis）。

$\mathfrak{B}$中的元素称为基向量。如果基中元素个数有限，就称向量空间为有限维向量空间，将元素的个数称作向量空间的维数。

设 $\mathfrak {B}$ 是向量空间 $V$ 的基。则$\mathfrak {B}$具有以下性质：

1.$V$ 是
$\mathfrak {B}$的极小生成集，就是说只有$\mathfrak {B}$能张成$V$，而它的任何真子集都不张成全部的向量空间。

2.$\mathfrak {B}$ 是 $V$ 中线性无关向量的极大集合，就是说
$\mathfrak {B}$ 在$V$ 中是线性无关集合，而且$V$
中没有其他线性无关集合包含它作为真子集。

3.$V$ 中所有的向量都可以按唯一的方式表达为$\mathfrak {B}$
中向量的线性组合。

**异或运算下的基**

对于数 $a_0, a_1, \ldots, a_n$，将
$a_i$的二进制表示$(b_{m}\ldots b_0)_2$​ 看作一个向量。向量组
$\mathbf{a}_1, \ldots, \mathbf{a}_n$可以张成一个向量集合
$\mathrm{span}(\mathbf{a}_1, \ldots, \mathbf{a}_n)$，加上我们的异或运算和乘法运算（显然满足
8 条公理），即可形成一个向量空间
$V = (\{0, 1\}, \mathrm{span}(\mathbf{a}_1, \ldots, \mathbf{a}_n), \oplus, \cdot)$

设集合$S$中最大的数在二进制意义下有$L$位，我们使用一个
$[0\dots L]$的数组$a$来储存线性基。

这种线性基的构造方法保证了一个特殊性质，对于每一个$i$,$a_i$有以下两种可能：

-   $a_i=0$，并且**只有**满足$j<i$的$a_j$（即$a_i$前面所有数）的第$i$个二进制位一定为$0$。

-   $a_i\neq0$,并且

    -   整个$a$数组只有$a_i$的第$i$个二进制位为$1$；

    -   $a_i$更高的二进制位一定为1

（如果排成矩阵，形成一个下三角矩阵）

    typedef long long ll;
    const int MAXL=60;
    ll a[MAXL+1];int lbsiz;
    void insert(ll t){//高斯消元解出的对角矩阵的非零行构成的向量组 
        for(int j=MAXL;j>=0;--j){// 逆序枚举二进制位
            if(!((t>>j)&1)) continue;// 如果 t 的第 j 位为 0，则跳过
            if(a[j]) t^=a[j];
            else{// 找到可以插入 a[j] 的位置
                // 用 a[0...j - 1] 消去 t 的第 [0, j) 位上的 1
    // 如果某一个a[k]=0也无须担心，因为这时候第k位不存在于线性基中，不需要保证t的第k位为 0  
                for(int k=0;k<j;++k) if((t>>k)&1) t^=a[k];
                // 用 t 消去 a[j + 1...L] 的第 j 位上的 1
                for(int k=j+1;k<=MAXL;++k) if((a[k]>>j)&1) a[k]^=t;
                    // 插入到 a[j] 的位置上
                a[j]=t;++lbsiz;
                return;// 不要忘记，结束插入过程
            }
        }
    }

最大异或和：将线性基中所有向量异或起来得到的向量所对应的数。

    ll querymax(){
        ll res=0;
        for(int i=0;i<=MAXL;++i) res^=a[i];
        return res;
    }

集合中选任意个数，和给定数异或的最大值

    ll query(ll x){//query(0)即最大异或和
        for(int i=MAXL;i>=0;--i){
            if((x^a[i])>x){
                x^=a[i];
            }
        }
        return x;
    }

集合任选大于1个元素异或出的第k大数

    vector<ll> v;
    bool zero;int n;//zero: 是否能异或出0
    void init(){
        lbsiz=0;
        v.clear();
        memset(a,0,sizeof a);
    }
    void prepare(){
        zero=(lbsiz!=n);
        for(int i=0;i<=MAXL;++i){
            if(a[i]) v.push_back(a[i]);
        }
    }
    ll query(ll k){
        if(zero) --k;
        if(k>=(1ll<<v.size())) return -1;
        ll res=0;
        for(int i=MAXL;i>=0;--i) if((k>>i)&1) res^=v[i];
        return res;
    }
    int main(){
        int T;
        scanf("%d",&T);
        for(int ttt=1;ttt<=T;++ttt){
            init();
            scanf("%d",&n);ll t;
            for(int i=1;i<=n;++i){
                scanf("%lld",&t);
                insert(t);
            }
            prepare();
            printf("Case #%d:\n",ttt);
            int q;scanf("%d",&q);
            while(q--){
                scanf("%lld",&t);
                printf("%lld\n",query(t));
            }
        }
        return 0;
    }

线性基RMQ

    typedef long long ll;
    const int N=20000+5,MAXL=61;
    struct Edge{
        int t,n;
    }e[N<<1];
    int hd[N],cnt;
    inline void build(int f,int t){
        e[++cnt]=(Edge){t,hd[f]};
        hd[f]=cnt;
    }
    struct Lbasis{//Linear Basis 
        ll a[MAXL+1];
        Lbasis(){memset(a,0,sizeof a);}
        Lbasis(const Lbasis& b){memcpy(a,b.a,sizeof a);} 
        void insert(ll t){
            //...
        }
        void merge(const Lbasis &b){
            for(int i=MAXL;i>=0;--i){
                if(b.a[i]){
                    insert(b.a[i]);
                }
            }
        }
        ll max(){
            ll res=0;
            for(int i=0;i<=MAXL;++i){
                res^=a[i];
            }
            return res;
        }
    }b[20][N];
    int dep[N],anc[20][N];
    ll val[N];
    void dfs(int u,int f){
        dep[u]=dep[f]+1;
        anc[0][u]=f;
        b[0][u].insert(val[u]);
        for(int i=1;anc[i-1][u];++i){
            anc[i][u]=anc[i-1][anc[i-1][u]];
            b[i][u]=b[i-1][u];
            b[i][u].merge(b[i-1][anc[i-1][u]]);
        }
        for(int i=hd[u];i;i=e[i].n){
            int v=e[i].t;
            if(v==f) continue;
            dfs(v,u);
        }
    }
    ll ask(int x,int y){
        if(dep[x]<dep[y]) swap(x,y);
        int dd=dep[x]-dep[y];
        Lbasis res;
        for(int i=19;i>=0;--i){
            if(dd&(1<<i)){
                res.merge(b[i][x]);
                x=anc[i][x];
            }
        }
        if(x==y){
            res.insert(val[x]);
            return res.max();
        }
        for(int i=19;i>=0;--i){
            if(anc[i][x]!=anc[i][y]){
                res.merge(b[i][x]);res.merge(b[i][y]);
                x=anc[i][x];y=anc[i][y];
            }
        }
        res.insert(val[x]);res.insert(val[y]);res.insert(val[anc[0][x]]);
        return res.max();
    } 

## 多项式算法

### FFT

    const int N=4e6+5;//多项式长度之和的两倍 
    typedef complex<double> cp;
    cp a[N],b[N];
    int rev[N];
    const double PI=acos(-1.0);

    void fft(cp *a,int nn){
        for(int i=1;i<nn;++i){
            if(rev[i]>i){
                swap(a[i],a[rev[i]]);
            }
        }
        for(int m=1;m<nn;m<<=1){//m为待合并区间长度的一半 
            cp Wn(cos(PI/m),sin(PI/m));//主n次单位根(n=2m)，Wn=exp(2*PI*i/n) 
            for(int n=m<<1,k=0;k<nn;k+=n){//k为区间开头 
                cp w(1,0);//旋转因子 
                for(int j=0;j<m;++j,w=w*Wn){//枚举左半区间 
                    cp t=w*a[k+j+m],u=a[k+j];
                    a[k+j]=u+t;
                    a[k+j+m]=u-t;
                }
            }
        }
    }
    int main(){
        int n,m,t;
        scanf("%d%d",&n,&m);//两个多项式的最高次数 
        for(int i=0;i<=n;++i){
            scanf("%d",&t);a[i]=t;  
        }
        for(int i=0;i<=m;++i){
            scanf("%d",&t);b[i]=t;
        }
        int lim=1,l=0;
        while(lim<=n+m) lim<<=1,++l;
        for(int i=0;i<lim;++i){
            rev[i]=(rev[i>>1]>>1)|((i&1)<<(l-1));
        }
        //在原序列中i与i/2的关系是：i可以看做是i/2的二进制上的每一位左移一位得来
        //那么在反转后的数组中就需要右移一位，同时特殊处理最高位 
        fft(a,lim);
        fft(b,lim);
        for(int i=0;i<lim;++i){
            a[i]*=b[i];
        }
        fft(a,lim);
        reverse(a+1,a+lim);
        for(int i=0;i<=n+m;i++){
            printf("%d ",(int)(a[i].real()/lim+0.5));
        }
        return 0;
    }

# 字符串算法

## Hash

    #define ctoi(x) ((x)-'a'+1)
    typedef long long ull;
    const int MAXN=10000+5;
    const ull e=29;
    ull hs[MAXN],pw[MAXN];
    char str[MAXN];
    void calhash(ull* a,char* c){
        for(int i=1;c[i];++i)
            a[i]=a[i-1]*e+ctoi(c[i]);
    }
    ull geths(ull* a,int x,int y){//[x,y]
        return (a[y]-a[x-1]*pw[y-x+1]);
    }
    ull connect(ull a,ull b,int lenb){
        return a*pw[lenb]+b;
    }
    void init(int n){
        pw[0]=1;
        for(int i=1;i<=n;++i)
            pw[i]=pw[i-1]*e;
    }    

## 回文串

### Manachar

        scanf("%s",rs+1);
        int len=strlen(rs+1);
        s[0]='#';
        for(int i=1;i<=len;++i){
            s[(i<<1)-1]='#';
            s[i<<1]=rs[i];
        }
        s[len=len*2+1]='#';
        int c=1,r=1;
        for(int i=1;i<=len;++i){
            if(i<r){
                p[i]=min(p[c*2-i],r-i);
            }
            while(s[i+1+p[i]]==s[i-1-p[i]]){
                ++p[i];
            }
            if(i+p[i]>r){
                r=i+p[i];
                c=i;
            }
        }

## 字符串匹配

### KMP

        nxt[1]=0;
        for(int i=2;ch[i];++i){
            for(j=nxt[i-1];j!=0&&ch[j+1]!=ch[i];j=nxt[j]);
            if(ch[j+1]==ch[i]) ++j;
            nxt[i]=j;
        }
        for(int i=1;str[i];++i){
            for(j=f[i-1];j!=0&&str[i]!=ch[j+1];j=nxt[j]);
            if(str[i]==ch[j+1]) ++j;
            f[i]=j;
        }

### Z函数ExKMP

字符串下标以 $0$ 为起点。 对于个长度为 $n$ 的字符串 $s$。定义函数 $z[i]$
表示 $s$ 和 $s[i,n-1]$（即以 $s[i]$
开头的后缀）的最长公共前缀（LCP）的长度。$z$ 被称为 $s$ 的 \*\*Z
函数\*\*。特别地，$z[0] = 0$。

        z[1]=n;
        for(int i=2,l=0,r=0;i<=n;++i){
            if(i<=r) z[i]=min(r-i+1,z[i-l+1]);
            while(i+z[i]<=n&&pt[z[i]+1]==pt[i+z[i]]) ++z[i];
            if(i+z[i]-1>r) l=i,r=i+z[i]-1;
        }
        int m=strlen(s+1);
        for(int i=1,l=0,r=0;i<=m;++i){
            if(i<=r) f[i]=min(r-i+1,z[i-l+1]);
            while(i+f[i]<=m&&pt[f[i]+1]==s[i+f[i]]) ++f[i];
            if(i+f[i]-1>r) l=i,r=i+f[i]-1;
        }

### AC自动机

    #define ctoi(x) ((x)-'a')
    const int N=1e6+5;
    struct Node{
        int s[26];
        int fail,num;
    }t[N];
    int cnt=1;
    char ts[N];
    string s[151];
    int cs[151];
    queue<int> q;
    void insert(char *s,int num){
        int len=strlen(s),u=1;
        for(int i=0;i<len;++i){
            int v=ctoi(s[i]);
            if(!t[u].s[v]){
                t[u].s[v]=++cnt;
            }
            u=t[u].s[v];
        }
        t[u].num=num;
    }
    void initfail(){
        for(int i=0;i<26;++i){
            t[0].s[i]=1;
        }
        q.push(1);
        t[1].fail=0;
        while(!q.empty()){
            int u=q.front();
            q.pop();
            for(int i=0;i<26;++i){
                int v=t[u].s[i];
                if(v){
                    t[v].fail=t[t[u].fail].s[i];
                    q.push(v);
                }
                else{
                    t[u].s[i]=t[t[u].fail].s[i];
                }
            }
        }
    }
    void ask(char *s){
        int u=1,len=strlen(s);
        for(int i=0;i<len;++i){
            int v=t[u].s[ctoi(s[i])];
            u=v;
            while(v){
                if(t[v].num!=0){
                    ++cs[t[v].num];
                }
                v=t[v].fail;
            }
        }
    }
    int main(){
        while(1){
            cnt=1;
            memset(t,0,sizeof t);
            memset(cs,0,sizeof cs);
            int n;
            scanf("%d",&n);
            if(n==0){
                break;
            }
            for(int i=1;i<=n;++i){
                scanf("%s",ts);
                s[i]=ts;
                insert(ts,i);
            }
            initfail();
            scanf("%s",ts);
            ask(ts);
            int mx=0;
            for(int i=1;i<=n;++i){
                mx=max(mx,cs[i]);
            }
            printf("%d\n",mx);
            for(int i=1;i<=n;++i){
                if(mx==cs[i]){
                    cout<<s[i]<<'\n';
                }
            }
        }
        return 0;
    }

    #define ctoi(x) ((x)-'a')
    const int L=2e6+5,N=2e5+5;
    struct Node{
        int s[26];
        int fail;
        vector<int> num;
    }t[N<<2];
    int cnt=1;
    char ts[L];
    int cs[N];
    queue<int> q;
    void insert(char *s,int num){
        int len=strlen(s),u=1;
        for(int i=0;i<len;++i){
            int v=ctoi(s[i]);
            if(!t[u].s[v]){
                t[u].s[v]=++cnt;
            }
            u=t[u].s[v];
        }
        t[u].num.push_back(num);
    }
    void initfail(){
        for(int i=0;i<26;++i){
            t[0].s[i]=1;
        }
        q.push(1);
        t[1].fail=0;
        while(!q.empty()){
            int u=q.front();
            q.pop();
            for(int i=0;i<26;++i){
                int v=t[u].s[i];
                if(v){
                    t[v].fail=t[t[u].fail].s[i];
                    q.push(v);
                }
                else{
                    t[u].s[i]=t[t[u].fail].s[i];
                }
            }
        }
    }
    void ask(char *s){
        int u=1,len=strlen(s);
        for(int i=0;i<len;++i){
            int v=t[u].s[ctoi(s[i])];
            u=v;
            while(v){
                if(t[v].num.size()!=0){
                    for(int i=0;i<t[v].num.size();++i){
                        ++cs[t[v].num[i]];
                    }
                }
                v=t[v].fail;
            }
        }
    }
    int main(){
        cnt=1;
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;++i){
            scanf("%s",ts);
            insert(ts,i);
        }
        initfail();
        scanf("%s",ts);
        ask(ts);
        int mx=0;
        for(int i=1;i<=n;++i){
            printf("%d\n",cs[i]);
        }
        return 0;
    }

# 数据结构

## 左偏树

    const int MAXN=1000000+5;//luogu P3377
    typedef int Arr[MAXN];
    Arr fa,ch[2],siz;
    pair<int,int> dat[MAXN];
    bool del[MAXN];
    int find(int x){
        return fa[x]==x?x:fa[x]=find(fa[x]);
    }
    int merge(int x,int y){
        if(!x||!y)
            return x+y;
        if(dat[x]>dat[y])
            swap(x,y);
        ch[1][x]=merge(ch[1][x],y);
        fa[ch[1][x]]=x;
        if(siz[ch[1][x]]>siz[ch[0][x]]) 
            swap(ch[1][x],ch[0][x]);    
        siz[x]=siz[ch[0][x]]+siz[ch[1][x]]+1;
        return x;   
    }
    void pop(int x){
        fa[ch[0][x]]=ch[0][x],fa[ch[1][x]]=ch[1][x];
        fa[x]=merge(ch[0][x],ch[1][x]);
    }
    int main(){
        int n,m,t,x,y;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i){
            scanf("%d",&t);
            dat[i]=make_pair(t,i);
            siz[i]=1;
            fa[i]=i;
        }
        while(m--){
            scanf("%d%d",&t,&x);
            if(t==1){
                scanf("%d",&y);
                if(del[x]||del[y])
                    continue;
                int fx=find(x),fy=find(y);
                if(fx!=fy)
                    merge(fx,fy);
            }
            else{
                if(del[x])
                    puts("-1");
                else{
                    int fx=find(x);
                    printf("%d\n",dat[fx].first);
                    del[dat[fx].second]=1;
                    pop(fx);
                }
            }
        }
        return 0;
    } 

## 稀疏表（ST表）

    const int MAXN=100000+5;
    int ST[20][MAXN],lg[MAXN];
    inline int ask(int l,int r){
        int k=lg[r-l+1];
        return max(ST[k][l],ST[k][r-(1<<k)+1]);
    }
    int main(){
        int n,q,x,y;
        scanf("%d%d",&n,&q);
        for(int i=1;i<=n;++i)
            lg[i]=(int)(log2((double)i));
        for(int i=1;i<=n;++i)
            scanf("%d",&ST[0][i]);
        for(int i=1;i<20;++i)
            for(int j=1;j+(1<<i)-1<=n;++j)
                ST[i][j]=max(ST[i-1][j],ST[i-1][j+(1<<i-1)]);
        while(q--){
            scanf("%d%d",&x,&y);
            printf("%d\n",ask(x,y));
        }
        return 0;
    }

## 树状数组

    #include<iostream>
    #include<cstdio>
    #include<cstring>
    using namespace std;
    typedef long long ll;
    const int MAXN=200000+5;
    ll X[MAXN],A[MAXN];
    int n;
    #define lowbit(x) (x&-x)
    void add(ll *a,int x,ll v){
        while(x<=n){
            a[x]+=v;
            x+=lowbit(x);
        }
    }
    ll ask(ll *a,int x){
        ll ret=0;
        while(x>0){
            ret+=a[x];
            x-=lowbit(x);
        }
        return ret;
    }
    void change(int l,int r,ll v){
        add(X,l,v);
        add(X,r+1,-v);
        add(A,l,l*v);
        add(A,r+1,-(r+1)*v);
    }
    ll asksum(int l,int r){
        return ((r+1)*ask(X,r)-ask(A,r))-(l*ask(X,l-1)-ask(A,l-1));
    }
    int main(){
        int q,opt,l,r;
        ll x,prv=0;
        scanf("%d",&n);
        for(int i=1;i<=n;++i){
            scanf("%lld",&x);
            add(X,i,x-prv);
            add(A,i,i*(x-prv));
            prv=x;
        }
        scanf("%d",&q);
        for(int i=1;i<=q;++i){
            scanf("%d",&opt);
            if(opt==1){
                scanf("%d%d%lld",&l,&r,&x);
                change(l,r,x);
            }
            else{
                scanf("%d%d",&l,&r);
                printf("%lld\n",asksum(l,r));
            }
        }
        return 0;
    }    

## 线段树

### 区间加、乘

    #define L(x) (x<<1)
    #define R(x) (x<<1|1)
    #define sz(x) (t[x].r-t[x].l+1)
    #define mid(x) (t[x].l+t[x].r>>1)
    inline void upd(int o){
        t[o].v=(t[L(o)].v+t[R(o)].v)%mod;
    }
    void buildt(int o,int L,int R){
        t[o].l=L,t[o].r=R;
        t[o].mul=1;
        if(L==R){
            scanf("%lld",&t[o].v);
            t[o].v%=mod;
            return;
        }
        int M=L+R>>1;
        buildt(L(o),L,M);
        buildt(R(o),M+1,R);
        upd(o);
    }
    void spread(int o){
        ll &tm=t[o].mul,&ta=t[o].add;
        if(tm!=1){
            t[L(o)].mul=(t[L(o)].mul*tm)%mod;
            t[L(o)].add=(t[L(o)].add*tm)%mod;
            t[L(o)].v=(t[L(o)].v*tm)%mod;
            t[R(o)].mul=(t[R(o)].mul*tm)%mod;
            t[R(o)].add=(t[R(o)].add*tm)%mod;
            t[R(o)].v=(t[R(o)].v*tm)%mod;
            tm=1;
        }
        if(ta){
            t[L(o)].add=(t[L(o)].add+ta)%mod;
            t[L(o)].v=(t[L(o)].v+ta*sz(L(o))%mod)%mod;
            t[R(o)].add=(t[R(o)].add+ta)%mod;
            t[R(o)].v=(t[R(o)].v+ta*sz(R(o))%mod)%mod;
            ta=0;
        }
    }
    void optmul(int o,int L,int R,ll val){
        if(L<=t[o].l&&t[o].r<=R){
            t[o].add=(t[o].add*val)%mod;
            t[o].mul=(t[o].mul*val)%mod;
            t[o].v=(t[o].v*val)%mod;
            return;
        }
        int M=mid(o);
        spread(o);
        if(L<=M)
            optmul(L(o),L,R,val);
        if(M<R)
            optmul(R(o),L,R,val);
        upd(o);
    }
    void optadd(int o,int L,int R,ll val){
        if(L<=t[o].l&&t[o].r<=R){
            t[o].v=(t[o].v+val*sz(o)%mod)%mod;
            t[o].add=(t[o].add+val)%mod;
            return;
        }
        spread(o);
        int M=mid(o);
        if(L<=M)
            optadd(L(o),L,R,val);
        if(M<R)
            optadd(R(o),L,R,val);
        upd(o);
    }
    ll ask(int o,int L,int R){
        if(L<=t[o].l&&t[o].r<=R){
            return t[o].v;
        }
        spread(o);
        int M=mid(o);
        ll ret=0;
        if(L<=M)
            ret=(ret+ask(L(o),L,R))%mod;
        if(M<R)
            ret=(ret+ask(R(o),L,R))%mod;
        return ret;
    }
    int main(){
        int T,n,opt,x,y;
        ll val;
        scanf("%d%d%lld",&n,&T,&mod);
        buildt(1,1,n);
        while(T--){
            scanf("%d%d%d",&opt,&x,&y);
            if(opt==2){
                scanf("%lld",&val);
                optadd(1,x,y,val%mod);
            }
            else if(opt==1){
                scanf("%lld",&val);
                optmul(1,x,y,val%mod);
            }
            else{
                printf("%lld\n",ask(1,x,y)%mod);
            }
        }
        return 0;
    }

## 主席树

    const int MAXN=4000000+5,MAXM=500000+5;
    struct node{
        int l,r,c;
    }t[MAXN];
    int a[MAXM],lsh[MAXM],rt[MAXN];
    int n,tcnt,rng;
    #define fnd(x) (lower_bound(lsh+1,lsh+rng+1,x)-lsh)
    void insert(int l,int r,int x,int& y,int v){
        y=++tcnt;
        t[y]=t[x];
        ++t[y].c;
        if(l==r) return;
        int mid=l+r>>1;
        if(v<=mid) 
            insert(l,mid,t[x].l,t[y].l,v);
        else 
            insert(mid+1,r,t[x].r,t[y].r,v);
    }
    int ask(int l,int r,int x,int y,int k){
        if(l==r) return l;
        int mid=l+r>>1;
        if(t[t[y].l].c-t[t[x].l].c>=k)
            return ask(l,mid,t[x].l,t[y].l,k);
        else 
            return ask(mid+1,r,t[x].r,t[y].r,k-(t[t[y].l].c-t[t[x].l].c));
    }
    int main(){
        int q;
        scanf("%d%d",&n,&q);
        for(int i=1;i<=n;++i)
            scanf("%d",&a[i]),lsh[i]=a[i];
        sort(lsh+1,lsh+n+1);
        rng=unique(lsh+1,lsh+n+1)-lsh-1;
        for(int i=1;i<=n;++i)
            insert(1,rng,rt[i-1],rt[i],fnd(a[i]));
        int x,y,z;
        while(q--){
            scanf("%d%d%d",&x,&y,&z);
            printf("%d\n",lsh[ask(1,rng,rt[x-1],rt[y],z)]);
        }
        return 0;
    } 

## 树链剖分

    const int MAXN=30000+5;
    typedef int Arr[MAXN];
    Arr fa,son,top,sz,dep,inseg,intr,val,hd;
    int cnt,totp,n;
    struct Edge{
        int t,n;
    }e[MAXN<<1];
    struct node{
        int l,r,max,sum;
    }t[MAXN<<2];
    #define L(x) (x<<1)
    #define R(x) (x<<1|1)
    #define mid(x) (t[o].l+t[o].r>>1)
    #define size(x) (t[o].r-t[o].l+1)
    inline void build(int f,int t){
        e[++cnt]=(Edge){t,hd[f]};
        hd[f]=cnt;
    }
    void dfs1(int u,int f){
        sz[u]=1;
        dep[u]=dep[f]+1;
        fa[u]=f;
        for(int i=hd[u];i;i=e[i].n){
            int& v=e[i].t;
            if(v==f) continue;
            dfs1(v,u);
            sz[u]+=sz[v];
            if(!son[u]||sz[son[u]]<sz[v])
                son[u]=v;
        }
    }
    void dfs2(int u,int tp){
        top[u]=tp;
        inseg[u]=++totp;
        intr[totp]=u;
        if(!son[u]) return;
        dfs2(son[u],tp);
        for(int i=hd[u];i;i=e[i].n){
            int& v=e[i].t;
            if(v==son[u]||v==fa[u]) continue;
            dfs2(v,v);
        }
    }
    inline void update(int o){
        t[o].sum=t[L(o)].sum+t[R(o)].sum;
        t[o].max=max(t[L(o)].max,t[R(o)].max);
    }
    void buildtree(int o,int L,int R){
        t[o].l=L,t[o].r=R;
        if(L==R){
            t[o].sum=t[o].max=val[intr[L]];
            return;
        }
        int M=L+R>>1;
        buildtree(L(o),L,M);
        buildtree(R(o),M+1,R);
        update(o);
    }
    int asksum(int o,int L,int R){
        if(L<=t[o].l&&t[o].r<=R)
            return t[o].sum;
        int M=mid(o),ret=0;
        if(L<=M) ret+=asksum(L(o),L,R);
        if(M<R) ret+=asksum(R(o),L,R);
        return ret;
    }
    int askmax(int o,int L,int R){
        if(L<=t[o].l&&t[o].r<=R)
            return t[o].max;
        int M=mid(o),ret=-1e9;
        if(L<=M) ret=max(ret,askmax(L(o),L,R));
        if(M<R) ret=max(ret,askmax(R(o),L,R));
        return ret;
    }
    void change(int o,int p,int v){
        if(t[o].l==t[o].r){
            t[o].sum=t[o].max=v;
            return;
        }
        int M=mid(o);
        if(p<=M) change(L(o),p,v);
        else change(R(o),p,v);
        update(o);
    }
    int askmax(int x,int y){
        int fx=top[x],fy=top[y],ans=-2e9;
        while(fx!=fy){
            if(dep[fx]<dep[fy]) swap(x,y),swap(fx,fy);
            ans=max(ans,askmax(1,inseg[fx],inseg[x]));
            x=fa[fx],fx=top[x];
        }
        if(dep[x]<dep[y]) swap(x,y);
        ans=max(ans,askmax(1,inseg[y],inseg[x]));
        return ans;
    }
    int asksum(int x,int y){
        int fx=top[x],fy=top[y],ans=0;
        while(fx!=fy){
            if(dep[fx]<dep[fy]) swap(x,y),swap(fx,fy);
            ans+=asksum(1,inseg[fx],inseg[x]);
            x=fa[fx],fx=top[x];
        }
        if(dep[x]<dep[y]) swap(x,y);
        ans+=asksum(1,inseg[y],inseg[x]);
        return ans;
    }
    int main(){
        int x,y;
        scanf("%d",&n);
        for(int i=1;i<n;++i){
            scanf("%d%d",&x,&y);
            build(x,y);
            build(y,x);
        }
        dfs1(1,0);
        dfs2(1,1);
        for(int i=1;i<=n;++i)
            scanf("%d",val+i);
        buildtree(1,1,totp);
        int q;
        char opt[10];
        scanf("%d",&q);
        while(q--){
            scanf("%s%d%d",opt,&x,&y);
            if(opt[1]=='M')
                printf("%d\n",askmax(x,y));
            else if(opt[1]=='S')
                printf("%d\n",asksum(x,y));
            else change(1,inseg[x],y);
        }
        return 0;
    }    

## 珂朵莉树

用作处理**随机数据**，具有区间赋值操作的序列操作问题
把值相同的区间合并成一个结点保存在 set 里面。 对于add，assign 和 sum
操作，用 set
实现的珂朵莉树的复杂度为$O(nloglogn)$，而用链表实现的复杂度为$O(nlog)$。

    typedef long long ll;
    struct Node{
      ll l, r;//闭区间！
      mutable ll v;//mutable让我们可以在后面的操作中修改 v 的值
      //可以直接修改已经插入 set 的元素的 v 值，而不用将该元素取出后重新加入 set
      Node(const ll &il, const ll &ir, const ll &iv):l(il),r(ir),v(iv){}
      bool operator < (const Node &o)const{return l<o.l;}
    };
    set<Node> odt;
    //包含点 x的区间（设为 [l,r)）分裂为两个区间[l,x)和[x,r)并返回指向后者的迭代器
    ll n;
    auto split(ll x) {
        if (x>n) return odt.end();
        auto it=--odt.upper_bound((Node){x, 0, 0});
        if(it->l==x) return it;
        ll l=it->l,r=it->r,v=it->v;
        odt.erase(it);
        odt.insert(Node(l,x-1,v));
        return odt.insert(Node(x,r,v)).first;
    } 
    void assign(ll l, ll r, ll v){//区间赋值，作为时间复杂度保证
        auto itr=split(r+1),itl=split(l);//进行求取区间左右端点操作时，必须先 split 右端点，再 split 左端点。若先 split 左端点，返回的迭代器可能在 split 右端点的时候失效，可能会导致 RE。
        odt.erase(itl,itr);
        odt.insert(Node(l,r,v));
    }
    //其他的操作，每块都操作（暴力）
    void do_sth(ll l,ll r,ll v){
        auto itr=split(r+1),itl=split(l);
        for(;itl!=itr;++itl) {
            //do something
        }
    }
    //例如，区间加
    void add(ll l,ll r,ll v){
        auto itr=split(r+1),itl=split(l);
        for(;itl!=itr;++itl) {
            itl->v+=v;  
        }
    }
    //区间第k小的是几
    ll kth(ll l,ll r,ll k){
        vector<pair<ll,ll> > v;
        auto itr=split(r+1),itl=split(l);
        for(;itl!=itr;++itl) {
            v.push_back(make_pair(itl->v,itl->r-itl->l+1));
        }
        sort(v.begin(),v.end());
        for(int i=0;i<v.size();++i){
            k-=v[i].second;
            if(k<=0) return v[i].first;
        }
    }
    int main(){
        for(ll i=1;i<=n;++i){//区间初始化
            ll x;scanf("%lld",&x);
            odt.insert(Node(i,i,x));
        }
        return 0;
    }

## 01-Trie

    typedef long long ll;
    const int MAXN=10000000;
    ll t[2][MAXN];
    int siz[MAXN];
    int cnt,root=++cnt;
    void insert(ll x,int v){
        bool b;
        x+=2e9;
        siz[root]+=v;
        for(int i=63,u=root;~i;--i){
            b=x>>i&1ll;
            if(!t[b][u]) 
                t[b][u]=++cnt;
            u=t[b][u];
            siz[u]+=v;
        }
    }
    ll askrnk(ll x){
        bool b;
        x+=2e9;
        ll ret=0;
        for(int i=63,u=root;~i;--i){
            b=x>>i&1ll;
            if(b) ret+=siz[t[0][u]];
            u=t[b][u];
        }
        return ret+1;
    }
    ll askkth(ll k){
        bool b;
        ll ret=0;
        for(int i=63,u=root;~i;--i){
            if(k>siz[t[0][u]]){
                ret|=1ll<<i;
                k-=siz[t[0][u]];
                u=t[1][u];
            }
            else u=t[0][u]; 
        }
        return ret-2e9;
    }
    int main(){
        int q,x;
        ll y;
        scanf("%d",&q);
        while(q--){
            scanf("%d%lld",&x,&y);
            switch(x){
                case 1:insert(y,1);break;
                case 2:insert(y,-1);break;
                case 3:printf("%lld\n",askrnk(y));break;
                case 4:printf("%lld\n",askkth(y));break;
                case 5:printf("%lld\n",askkth(askrnk(y)-1));break;
                case 6:printf("%lld\n",askkth(askrnk(y+1)));
            }
        }
        return 0;
    }        

# 图论

## 最短路

### SPFA

    const int N=1e4+5,M=5e5+5;
    const ll inf=(1ll<<31)-1;
    struct Edge{
        int t,n;
        ll v;
    }e[M<<1];
    ll dis[N];
    int hd[N];
    bool inq[N];
    queue<int> q;
    int cnt,n,m,S;
    void build(int f,int t,ll v){
        e[++cnt]=(Edge){t,hd[f],v};
        hd[f]=cnt;
    }
    void spfa(){
        for(int i=1;i<=n;++i){
            dis[i]=inf;
        }
        q.push(S);
        dis[S]=0;
        inq[S]=1;
        while(!q.empty()){
            int u=q.front();
            q.pop();
            inq[u]=0;
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(dis[v]>dis[u]+e[i].v){
                    dis[v]=dis[u]+e[i].v;
                    if(!inq[v]){
                        q.push(v);
                        inq[v]=1;
                    }
                }
            }
        }
    }    

### Dijkstra

    typedef long long ll;
    const int N=1e5+5,M=2e5+5;
    struct Edge{
        int t,n,v;
    }e[M<<1];
    int hd[N],dis[N],cnt,n,m,S;
    bool cho[N];
    typedef pair<int,int> pii;
    priority_queue<pii,vector<pii>,greater<pii> > q;
    void build(int f,int t,int v){
        e[++cnt]=(Edge){t,hd[f],v};
        hd[f]=cnt;
    }
    void dij(){
        for(int i=1;i<=n;++i){
            dis[i]=2147483647;
        }
        dis[S]=0;
        q.push(pii(0,S));
        while(!q.empty()){
            pii t=q.top();
            q.pop();
            int u=t.second;
            if(cho[u]){
                continue;
            }
            cho[u]=1;
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(dis[v]>dis[u]+e[i].v){
                    dis[v]=dis[u]+e[i].v;
                    q.push(pii(dis[v],v));
                }
            }
        }
    }

    int main(){
        scanf("%d%d%d",&n,&m,&S);
        int a,b,val;
        for(int i=1;i<=m;++i){
            scanf("%d%d%d",&a,&b,&val);
            build(a,b,val);
        }
        dij();
        for(int i=1;i<=n;++i){
            printf("%d ",dis[i]);
        }
        return 0;
    }

### Johnson全源最短路

用于处理**有负边权稀疏图**的多源最短路问题。套用斐波那契堆优化的Dijkstra时间复杂度为$O(V^2logV+VE)$,套用二叉堆优化的Dijkstra时间复杂度$O(VElogE)$。

（稠密图$V^2\approx E$，使用Floyd算法即可，无负边权直接做$V$次Dijkstra。）

设虚拟结点，与其他点建边权为0的边，从虚拟结点跑spfa得$h_i$，重建图赋值$(u, v)$边权$w(u, v)$为$w(u, v)+h_u-h_v$，这保证了最短路径不变而且所有权重均非负，跑n次Dijkstra求最短路，算法求得距离为$dis(s, t)$，非不可达点的实际距离为$dis(s, t)-(h_s-h_t)$。

    const int M=6e3+5,N=3e3+5,inf=1e9;
    struct Edge{
        int t,n,v;
    }e[M+N];
    int hd[N];
    int cnt,n,m;
    inline void build(int f,int t,int v){
        e[++cnt]=(Edge){t,hd[f],v};
        hd[f]=cnt;
    }
    bool inq[N],vis[N];
    int h[N],t[N];
    bool spfa(){
        static queue<int> q;
        q.push(0);//虚拟结点定为0 
        h[0]=0;inq[0]=1;
        while(!q.empty()){
            int u=q.front();
            q.pop();
            inq[u]=0;
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(h[v]>h[u]+e[i].v){
                    h[v]=h[u]+e[i].v;
                    if(!inq[v]){
                        ++t[v];
                        if(t[v]==n+1) return 0;//入队n+1次，有负环 
                        q.push(v);
                        inq[v]=1;
                    }
                }
            }
        }
        return 1;
    }
    int dis[N][N];
    bool cho[N];
    typedef pair<int,int> pii;
    priority_queue<pii,vector<pii>,greater<pii> > q;
    void dij(int S){
        for(int i=1;i<=n;++i){
            dis[S][i]=inf;
            cho[i]=0;
        }
        dis[S][S]=0;
        q.push(pii(0,S));
        while(!q.empty()){
            pii t=q.top();
            q.pop();
            int u=t.second;
            if(cho[u]) continue;
            cho[u]=1;
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(dis[S][v]>dis[S][u]+e[i].v){
                    dis[S][v]=dis[S][u]+e[i].v;
                    q.push(pii(dis[S][v],v));
                }
            }
        }
    }
    int main(){
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;++i){
            int a,b,v;
            scanf("%d%d%d",&a,&b,&v);
            build(a,b,v);
        }
        for(int i=1;i<=n;++i){
            h[i]=inf;
            build(0,i,0);//虚拟结点到其他点建边权为0的边 
        }
        if(!spfa()){
            puts("-1");
            return 0;
        }
        for(int u=1;u<=n;++u){
            for(int i=hd[u];i;i=e[i].n){
                e[i].v+=h[u]-h[e[i].t];
            }
        }
        for(int i=1;i<=n;++i){
            dij(i);
        }
        return 0;
    }

### 同余最短路

有多少整数$b\in [0,h)$,使得$\sum\limits_{i=1}^{n}a_ix_i=b$有非负整数解。

令$dis_i$表示最小的符合$(\sum\limits_{i=1}^{n}a_ix_i)\space mod\space a_k=i(\forall k  \in[1.n])$的$\sum\limits_{i=1}^{n}a_ix_i$,
则$i+t\cdot a_k,\forall t \in \mathbb{N}$都有解。

$\forall i \in [0,a_k)$,$\forall j\in[1,n],j\neq k$建边$(i,(i+a_j)mod\space a_k)$,边权为$a_j$,
从0开始跑最短路可求$dis_i$,$a_k$取$a_1...a_n$中最小的可保证建边最少运行最快。

$$ans=\sum\limits_{i=1}^{n}(\lfloor\frac{h-dis_i}{a_k}\rfloor+1)$$

        const int N=5e5+5;
        int cnt,hd[N];
        struct Edge{
            int t,v,n;
        }e[N*24];
        typedef long long ll;
        void build(int f,int t,int v){
            e[++cnt]=(Edge){t,v,hd[f]};
            hd[f]=cnt;
        }
        queue<int> q;
        bool inq[N];
        ll dis[N],a[20];
        int main(){
            ll n,l,r;
            scanf("%lld%lld%lld",&n,&l,&r);//题目为求[l,r]之间的解
            l--;
            ll mn=1e18,k=-1;
            for(int i=1;i<=n;++i){
                scanf("%lld",&a[i]);
                if(a[i]<mn){
                    k=i;
                    mn=a[i];
                }
            }
            for(int i=0;i<mn;++i){
                for(int j=1;j<=n;++j){
                    if(j!=k){
                        build(i,(i+a[j])%mn,a[j]);
                    }
                }
            }
            for(int i=0;i<N;++i){dis[i]=2e18;}
            q.push(0);
            dis[0]=0;
            inq[0]=1;
            while(!q.empty()){
                int u=q.front();
                q.pop();
                inq[u]=0;
                for(int i=hd[u];i;i=e[i].n){
                    int v=e[i].t;
                    if(dis[v]>dis[u]+e[i].v){
                        dis[v]=dis[u]+e[i].v;
                        if(!inq[v]){
                            inq[v]=1;
                            q.push(v);
                        }
                    }
                }
            }
            unsigned long long al=0,ar=0;
            for(int i=0;i<mn;++i){
                if(dis[i]<=r){
                    ar+=(r-dis[i])/mn+1;
                    if(dis[i]<=l){
                        al+=(l-dis[i])/mn+1;
                    }
                }
            }
            printf("%llu\n",ar-al);
            return 0;
        }   

## 最小生成树

Minimum Spanning Tree

### Prim

    const int N=5005,M=2e5+5;
    struct Edge{
        int t,n,v;
    }e[M<<1];
    int dis[N],hd[N];
    bool cho[N];
    int cnt;
    void build(int f,int t,int v){
        e[++cnt]=(Edge){t,hd[f],v};
        hd[f]=cnt;
    }
    typedef pair<int,int> pii;
    priority_queue<pii,vector<pii>,greater<pii> > q;
    int main(){
        int n,m;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;++i){
            int a,b,c;
            scanf("%d%d%d",&a,&b,&c);
            build(a,b,c);build(b,a,c);
        }
        memset(dis,0x3f,sizeof dis);
        dis[1]=0;
        q.push(pii(0,1));
        int now=0,ans=0;
        while(!q.empty()){
            int u=q.top().second;q.pop();
            if(cho[u]) continue;
            cho[u]=1;
            ++now;
            ans+=dis[u];
            if(now==n) break;
            for(int i=hd[u];i;i=e[i].n){
                
                int v=e[i].t;
                if(dis[v]>e[i].v){
                    dis[v]=e[i].v;
                    q.push(pii(dis[v],v));
                }
            }
        }
        printf("%d\n",ans);
        return 0;
    }

### Kruskal

    struct Edge{
        int f,t,v;
        bool operator < (const Edge &b)const{
            return v<b.v;
        }
    }e[M];
    int fa[N];
    int n,m;
    int find(int x){
        return fa[x]==x?x:fa[x]=find(fa[x]); 
    }
    int main(){
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i){ 
            fa[i]=i;
        } 
        for(int i=1;i<=m;++i){
            scanf("%d%d%d",&e[i].f,&e[i].t,&e[i].v);
        }
        int cnt=0;
        long long ans=0;
        sort(e+1,e+m+1);
        for(int i=1;i<=m;++i){
            int x=find(e[i].f),y=find(e[i].t);
            if(x!=y){
                ++cnt;
                fa[x]=y;
                ans+=e[i].v;
            }
            if(cnt==n-1){
                break;
            }
        }
        printf("%lld",ans);
        return 0;
    } 

### Borůvka

最初每个点是孤立点。从所有当前的连通块向其他连通块扩展出最小边，直到只剩一个连通块。

时间复杂度$O(E\log V)$

    const int N=5005,M=2e5+5;
    struct Edge{
        int f,t,v;
    }e[M];
    int fa[N],lk[N],cpt[N],n,m; //cpt cheapest 子树中最小的边权 
    int find(int x){return fa[x]==x?x:fa[x]=find(fa[x]);};

    int Boruvka(){
        int ans=0;
        for(int i=1;i<=n;++i) fa[i]=i;
        int t=n;
        while(t>1){
            for(int i=1;i<=n;++i) lk[i]=0;
            int cnt=0;
            for(int i=1;i<=m;++i){
                int x=find(e[i].f),y=find(e[i].t);
                if(x!=y){
                    ++cnt;
                    if(!lk[x]||cpt[x]>e[i].v) lk[x]=y,cpt[x]=e[i].v;
                    if(!lk[y]||cpt[y]>e[i].v) lk[y]=x,cpt[y]=e[i].v;
                    //lk[i]: 在本轮连边中 i子树连向哪棵子树 
                }
            }
            if(!cnt) return -1;//图不联通 
            for(int i=1;i<=n;++i){
                if(fa[i]==i){
                    fa[find(lk[i])]=i;
                    ans+=cpt[i];
                    --t;
                }
            }
        }
        return ans;
    } 

### 异或最小生成树

给每一个点一个点权，连接两个点$i,j$的边的边权为$a_i$ xor
$a_j$，求这张图上的最小生成树。

我们怎么快速找到连通块外的最近点（异或值最小的点）呢，这时我们就需要0/1字典树了。

我们可以建立一个全局字典树包含所有的点权，然后对每个连通块都维护一个单独的字典树，每个字典树中包含了这个连通块中的所有点权。

每次查询某个点$a_i$的最近点的时候，我们从高位到低位比较，假设当前在第$i$位，且$a_i$的第$i$位上的值为$b$，如果连通块外有$i$位也为$b$的数字（说明$b$方向上，全局字典树的size大于当前连通块字典树的size），我们必然选择同为$b$的数字，我们在两棵字典树的位置都往$b$方向转移。如果没有，我们便在字典树上的位置便要向$b$
$xor$ $1$的方向转移，结果值就要加上$2^i$。

    const int N=2e5+5;
    int t[2][N*60],siz[N*60],val[N*60],root[N];
    int cnd;
    typedef pair<int,int> pii;
    void insert(int u,int x,int i){
        bool b;
        ++siz[u];
        for(int i=30;~i;--i){
            b=x>>i&1;
            if(!t[b][u]) t[b][u]=++cnd;
            u=t[b][u];
            ++siz[u];
        }
        val[u]=i;
    }
    pii query(int rt,int lt,int x){
        int ans=0;
        for(int i=30;i>=0;--i){//从高位到低位贪心 
            bool b=x>>i&1;
            int sr=t[b][rt]?siz[t[b][rt]]:0;
            int sl=t[b][lt]?siz[t[b][lt]]:0;
            if(sr>sl){//全局的字典树大于当前的字典树 
                rt=t[b][rt];////可以走与x的第i位相同的 
                lt=t[b][lt];
            }else{//不得不走与x的第i位相异的 
                ans+=(1<<i);//当前位对答案贡献 
                rt=t[b^1][rt];
                lt=t[b^1][lt];
            }
        }
        return pii(ans,val[rt]);
    }
    void merge(int &x,int y){
        if(x==0||y==0){
            x=x+y;
            return;
        }
        merge(t[0][x],t[0][y]);
        merge(t[1][x],t[1][y]);
        siz[x]+=siz[y];
        val[x]=max(val[x],val[y]);
    }
    int fa[N],lk[N],cpt[N],n,m,a[N]; //cpt cheapest 子树中最小的边权 
    int find(int x){return fa[x]==x?x:fa[x]=find(fa[x]);};
    int main(){
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;++i){
            scanf("%d",&a[i]);
            fa[i]=i;
            root[i]=i;
        }
        root[0]=n+1;
        cnd=n+1;
        sort(a+1,a+n+1);
        n=unique(a+1,a+n+1)-a-1;//相同的数可以事先用边权为0的边连起来 
        for(int i=1;i<=n;++i){
            insert(root[0],a[i],i);
            insert(root[i],a[i],i);
        }
        int t=n;
        long long ans=0;
        while(t>1){
            for(int i=1;i<=n;++i) lk[i]=0;
            for(int i=1;i<=n;++i){
                int x=find(i);
                pii tmp=query(root[0],root[x],a[i]);
                int w=tmp.first,y=find(tmp.second);
                if(x!=y){
                    if(!lk[x]||cpt[x]>w) lk[x]=y,cpt[x]=w;
                    if(!lk[y]||cpt[y]>w) lk[y]=x,cpt[y]=w;
                }
            }
            for(int i=1;i<=n;++i){
                if(fa[i]==i){
                    int fi=find(lk[i]);
                    fa[fi]=i;
                    merge(root[i],root[fi]);
                    ans+=cpt[i];
                    --t;
                }
            }
        }
        printf("%lld",ans); 
        return 0;
    }

### 严格次小生成树

在最小生成树上，倍增维护最大边权和严格次大边权。

枚举尝试添加非树边，如果这条边边权大于最大值，在环上替换最大值的边权，若等于则替换严格次大边权的边，得到严格最小生成树。

    const int M=6e5+5,N=2e5+5;
    typedef long long ll;
    struct E1{
        int f,t,v;
        bool operator < (const E1 &b)const{
            return v<b.v;
        }
    }edg[M];
    int fa[N],hd[N],cnt;
    int find(int x){
        return fa[x]==x?x:fa[x]=find(fa[x]);
    }
    struct E2{
        int t,v,n;
    }e[M*4];
    void build(int f,int t,int v){
        e[++cnt]=(E2){t,v,hd[f]};
        hd[f]=cnt;
    }
    int dep[N],anc[21][N],la[21][N],sub[21][N];
    bool used[N];
    void dfs(int u,int f){
        for(int i=hd[u];i;i=e[i].n){
            int v=e[i].t;
            if(v==f) continue;
            dep[v]=dep[u]+1;
            anc[0][v]=u;
            la[0][v]=e[i].v;
            sub[0][v]=-2e9;
            for(int i=1;anc[i-1][v];++i){
                anc[i][v]=anc[i-1][anc[i-1][v]];
                la[i][v]=max(la[i-1][v],la[i-1][anc[i-1][v]]);
                if(la[i-1][v]>la[i-1][anc[i-1][v]])
                    sub[i][v]=max(sub[i-1][v],la[i-1][anc[i-1][v]]);
                else if(la[i-1][v]<la[i-1][anc[i-1][v]])
                    sub[i][v]=max(la[i-1][v],sub[i-1][anc[i-1][v]]);
            }
            dfs(v,u);
        }
    }
    int lca(int x,int y){
        if(dep[x]<dep[y]) swap(x,y);
        int dd=dep[x]-dep[y];
        for(int i=19;i>=0;--i){
            if(dd&(1<<i)){
                x=anc[i][x];
            }
        }
        if(x==y) return x;
        for(int i=19;i>=0;--i){
            if(anc[i][x]!=anc[i][y]){
                x=anc[i][x],y=anc[i][y];
            }
        }
        return anc[0][x];
    }
    int query(int x,int y,int v){
        int ans=-2e9;
        for(int i=19;i>=0;--i){
            if(dep[anc[i][y]]>=dep[x]){
                if(v!=la[i][y]) ans=max(ans,la[i][y]);
                else ans=max(ans,sub[i][y]);
                y=anc[i][y];
            }
        }
        return ans;
    }
    int main(){
        int n,m;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;++i){
            scanf("%d%d%d",&edg[i].f,&edg[i].t,&edg[i].v);
        }
        for(int i=1;i<=n;++i){
            fa[i]=i;
        }
        sort(edg+1,edg+m+1);
        int cne=0;
        ll ans=0;
        for(int i=1;i<=m;++i){
            int x=find(edg[i].f),y=find(edg[i].t);
            if(x!=y){
                used[i]=1;
                fa[x]=y;
                ans+=edg[i].v;
                build(edg[i].f,edg[i].t,edg[i].v);
                build(edg[i].t,edg[i].f,edg[i].v);
                ++cne;
                if(cne==n-1){
                    break;
                }
            }
        }
        dep[1]=1;
        dfs(1,0);
        ll ta=9e18;
        for(int i=1;i<=m;++i){
            if(used[i]||edg[i].f==edg[i].t) continue;//题目中有重变，去除 
            int p=lca(edg[i].f,edg[i].t);
            int al=query(p,edg[i].f,edg[i].v);
            int ar=query(p,edg[i].t,edg[i].v);
            ta=min(ta,ans-max(al,ar)+edg[i].v);
        }
        printf("%lld",ta);
        return 0;
    } 

## 最小树形图

### 朱刘算法EdmondAlgorithm

找到除了root以为外其他点的权值最小的入边。用inw\[i\]记录。

如果出现除了root以为存在其他孤立的点，则不存在最小树形图。

找到图中所有的环，并对环进行缩点，重新编号。

更新其他点到环上的点的距离
环中的点有（$V_{k_1},V_{k_2}...V_{k_i}$）总共i个，用缩成的点叫$V_k$替代。

则在压缩后的图中，其他所有不在环中点v到$V_k$的距离定义如下：$e_{v,V_k}=min\{e_{v,V_{k_j}}-inw_{V_{k_j}}\}$
而Vk到v的距离为:$e_{v,V_k}=min\{e_{v,V_{k_j}}\}$

重复3，4直到没有环为止。

    const int N=105,M=1005;
    int hd[N],cnt,n,m,ort;
    int inw[N]/*最小入边*/,id[N]/*当前图到重构图的映射*/,pre[N],vis[N];
    struct Edge{
        int f,t,v;
    }e[M];
    int dmst(int rt,int nv,int ne){
        int ret=0;
        while(1){//1.确定最小边集 
            for(int i=1;i<=nv;++i) inw[i]=INT_MAX,vis[i]=0,id[i]=0;
            for(int i=1;i<=ne;++i){
                int frm=e[i].f,to=e[i].t;
                if(frm!=to&&e[i].v<inw[to]){//忽略自环 
                    inw[to]=e[i].v;pre[to]=frm;
                }
            }
            for(int i=1;i<=nv;++i){//有顶点不可达 
                if(i!=rt&&inw[i]==INT_MAX) return -1;
            }
            int idx=0;inw[rt]=0;//2.找环 
            for(int i=1;i<=nv;++i){//计算最小入边集的权值和；检查是否有环，如果有，重新对点进行编号
                ret+=inw[i];
                int v=i;
                while(v!=rt&&vis[v]!=i&&!id[v]){//由v回溯。能回到根，即最后v==root，那么肯定不在环里；回不到根，v!=root，v有可能在环里，也有可能不在（回溯到一个环然后出不去了，同样也到不了根）。
                //若v在环里，则环上所有点的id[]值会被重新标号，不再是o；若是后一种情况，它前驱的环上的点的id[]已被修改为非0，不能通过“!id[v]”这个条件的检查。
                    vis[v]=i;
                    v=pre[v];
                }
                if(v!=rt&&!id[v]){////两个条件保证了：1.在环上2.这环没被处理过
                    id[v]=++idx;
                    for(int u=pre[v];u!=v;u=pre[u]){
                        id[u]=idx;
                    }
                }
            }
            if(!idx) break;//无环，退出
            for(int i=1;i<=nv;++i){
                if(!id[i]) id[i]=++idx;
            }
            for(int i=1;i<=ne;++i){//3.重新构图，准备下一次迭代 
                int to=e[i].t;
                e[i].f=id[e[i].f];
                e[i].t=id[e[i].t];
                if(e[i].f!=e[i].t){
                    e[i].v-=inw[to];
                } 
            }
            nv=idx;
            rt=id[rt];
        }
        return ret;
    }
    int main(){
        scanf("%d%d%d",&n,&m,&ort);
        for(int i=1;i<=m;++i) scanf("%d%d%d",&e[i].f,&e[i].t,&e[i].v),++e[i].f,++e[i].t;
        printf("%d\n",dmst(ort+1,n,m));
        return 0;
    }

## 强连通/双连通

### SCC

求一条路径，使路径经过的点权值之和最大。允许多次经过一条边或者一个点，但是，重复经过的点，权值只计算一次。

Tarjan算法缩点，然后拓扑排序。

    const int N=2e5+5;
    struct Edge{
        int t,n;
    }e1[N],e2[N];
    typedef int Arr[N];
    Arr sccno,dfn,low,hd1;
    vector<int> scc[N];
    stack<int> s;
    int cnt1,ccnt,clck;
    void build1(int f,int t){
        e1[++cnt1]=(Edge){t,hd1[f]};
        hd1[f]=cnt1;
    }
    void dfs(int u){
        dfn[u]=low[u]=++clck;
        s.push(u);
        for(int i=hd1[u];i;i=e1[i].n){
            int v=e1[i].t;
            if(!dfn[v]){
                dfs(v);
                low[u]=min(low[u],low[v]);
            }else if(!sccno[v]){
                low[u]=min(low[u],dfn[v]);
            }
        }
        if(low[u]==dfn[u]){
            ++ccnt;
            while(1){
                int t=s.top();
                s.pop();
                sccno[t]=ccnt;
                scc[ccnt].push_back(t);
                if(t==u) break;
            }
        }
    }
    Arr cv,val,ind,dp,hd2;
    int cnt2;
    queue<int> q;
    void build2(int f,int t){
        e2[++cnt2]=(Edge){t,hd2[f]};
        hd2[f]=cnt2;
        ++ind[t];
    }
    void topo(){
        for(int i=1;i<=ccnt;++i){
            if(!ind[i]){
                q.push(i);
                dp[i]=cv[i];
            }
        }
        while(!q.empty()){
            int u=q.front();
            q.pop();
            for(int i=hd2[u];i;i=e2[i].n){
                int v=e2[i].t;
                --ind[v];
                dp[v]=max(dp[v],dp[u]+cv[v]);
                if(ind[v]==0) q.push(v);
            }
        }
    }
    int main(){
        int n,m,x,y;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i){
            scanf("%d",&val[i]);
        }
        for(int i=1;i<=m;++i){
            scanf("%d%d",&x,&y);build1(x,y);
        }
        for(int i=1;i<=n;++i){
            if(!dfn[i]) dfs(i);
        }
        for(int i=1;i<=n;++i){
            cv[sccno[i]]+=val[i];
            for(int j=hd1[i];j;j=e1[j].n){
                if(sccno[i]!=sccno[e1[j].t]) build2(sccno[i],sccno[e1[j].t]);
            }
        }
        topo();
        int ans=0;
        for(int i=1;i<=ccnt;++i) ans=max(ans,dp[i]);
        printf("%d",ans);
        return 0;
    }

### BCC和切点

    const int N=2e4+5,M=1e5+5;;
    struct Edge{
        int t,n;
    }e[M<<1];
    int hd[N],bccno[N],dfn[N],low[N];
    int cnt,ccnt,clck;
    struct E2{
        int f,t;
    };
    stack<E2> s;
    bool cut[N];
    vector<int> bcc[N];
    inline void build(int f,int t){
        e[++cnt]=(Edge){t,hd[f]};
        hd[f]=cnt;
    }
    void dfs(int u,int fa){
        dfn[u]=low[u]=++clck;
        int child=0;
        for(int i=hd[u];i;i=e[i].n){
            int v=e[i].t;
            if(!dfn[v]){
                s.push((E2){u,v});
                ++child;
                dfs(v,u);
                low[u]=min(low[u],low[v]);
                if(low[v]>=dfn[u]){
                    cut[u]=1;
                    bcc[++ccnt].clear();
                    for(;;){
                        E2 e=s.top();s.pop();
                        if(bccno[e.f]!=ccnt){
                            bcc[ccnt].push_back(e.f);bccno[e.f]=ccnt;
                        }
                        if(bccno[e.t]!=ccnt){
                            bcc[ccnt].push_back(e.t);bccno[e.t]=ccnt;
                        }
                        if(e.f==u&&e.t==v) break;
                    }
                }
            }else if(dfn[v]<dfn[u]&&v!=fa){
                s.push((E2){u,v});
                low[u]=min(low[u],dfn[v]);
            }
        }
        if(fa==0&&child==1) cut[u]=0;
    }
    int main(){
        int n,m,a,b;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=m;++i){
            scanf("%d%d",&a,&b);
            build(a,b);build(b,a);
        }
        int ans=0;
        for(int i=1;i<=n;++i){
            if(!dfn[i]){
                dfs(i,0);
            }
            if(cut[i]){
                ++ans;
            }
        }
        printf("%d\n",ans);
        for(int i=1;i<=n;++i){
            if(cut[i]){
                printf("%d ",i);
            }
        }
        return 0;
    }    

## 欧拉图

### 欧拉回路

Hierholzer算法，也称逐步插入回路法。算法流程为从一条回路开始，每次任取一条目前回路中的点，将其替换为一条简单回路，以此寻找到一条欧拉回路。如果从路开始的话，就可以寻找到一条欧拉路。

任取一个起点,开始下面的步骤

如果该点没有相连的点，就将该点加进路径中然后返回。

如果该点有相连的点，就列一张相连点的表然后遍历它们直到该点没有相连的点。(遍历一个点，删除一个点）处理当前的点,删除和这个点相连的边,
在它相邻的点上重复上面的步骤,把当前这个点加入路径中。

    const int N=1e5+5,M=2e5+5;
    struct Edge{
        int t,n,id;
    }e[M*2];
    int ans[M];
    int ind[N],outd[N],hd[N];
    bool vis[M];
    int cnt,tot;
    void build(int f,int t,int id){
        e[++cnt]=(Edge){t,hd[f],id};
        hd[f]=cnt;
        ++ind[t],++outd[f];
    }
    void dfs(int u){
        for(int i=hd[u];i;i=hd[u]/*hd[u]会被修改成e[i].n*/){
            while(i&&vis[abs(e[i].id)]){
                i=e[i].n;
            }
            hd[u]=i;//已经访问过的边不再遍历,故修改第一条边 
            if(!i) break;
            vis[abs(e[i].id)]=1;
            dfs(e[i].t);
            ans[++tot]=e[i].id;
        }
    }
    int main(){
        int cs,n,m;
        scanf("%d%d%d",&cs,&n,&m);//cs 1：无向图 2：有向图 
        for(int i=1;i<=m;++i){
            int x,y;
            scanf("%d%d",&x,&y);
            build(x,y,i);
            if(cs==1) build(y,x,-i);
        }
        if(cs==1){
            for(int i=1;i<=n;++i){
                if(ind[i]&1){
                    puts("NO");return 0;
                }
            }    
        }else{
            for(int i=1;i<=n;++i){
                if(ind[i]!=outd[i]){
                    puts("NO");return 0;
                }
            }
        }
        dfs(e[1].t);
        if(tot<m){
            puts("NO");
        }
        else{
            puts("YES");
            for(int i=m;i>=1;--i){
                printf("%d ",ans[i]);
            }
        }
        return 0;
    } 

## 网络流

### Dinic

$O(n^2m)$,但是一般算法运行很快不会达到该上界。二分图时$O(m\sqrt n)。所以边权都为1时，O(min(n^{\frac{2}{3}},\sqrt{m})m)$

    const int N=1200+5,M=120000+5;
    typedef long long ll;
    struct Edge{
        int t,v,n;
    }e[M<<1];
    queue<int> q;
    int cnt=1,S,T;
    int hd[N],h[N];
    void adde(int f,int t,int v){
        e[++cnt]=(Edge){t,v,hd[f]};hd[f]=cnt;
    }
    void build(int f,int t,int v){
        adde(f,t,v);adde(t,f,0);
    }
    bool bfs(){
        memset(h,-1,sizeof h);
        while(!q.empty()) q.pop();
        h[S]=0;q.push(S);
        while(!q.empty()){
            int u=q.front();
            q.pop();
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(h[v]==-1&&e[i].v){
                    h[v]=h[u]+1;
                    q.push(v);
                }
            }
        }
        return h[T]!=-1;
    }
    ll dfs(int u,ll fl){
        if(u==T) return fl;
        ll w,used=0;
        for(int i=hd[u];i;i=e[i].n){
            int &v=e[i].t;
            if(h[v]==h[u]+1){
                w=dfs(v,min(fl-used,(ll)e[i].v));
                e[i].v-=w;
                e[i^1].v+=w;used+=w;
                if(used==fl) return fl;
            }
        }
        if(!used) h[u]=-1;
        return used;
    }
    ll dinic(){
        ll ans=0;
        while(bfs())
            ans+=dfs(S,2e18);
        return ans;
    }

## 树上问题

### 倍增LCA

    const int N=500000+5;
    struct Edge{
        int t,n;
    }e[N<<1];
    int hd[N],dep[N],anc[20][N];
    int cnt,n,root;
    void build(int f,int t){
        e[++cnt]=(Edge){t,hd[f]};
        hd[f]=cnt;
    }
    void dfs(int u,int f){
        for(int i=hd[u];i;i=e[i].n){
            int v=e[i].t;
            if(v==f){
                continue;
            }
            dep[v]=dep[u]+1;
            anc[0][v]=u;
            for(int i=1;anc[i-1][v];++i){
                anc[i][v]=anc[i-1][anc[i-1][v]];
            }
            dfs(v,u);
        }
    }
    int lca(int x,int y){
        if(dep[x]<dep[y]){
            swap(x,y);
        }
        int dd=dep[x]-dep[y];
        for(int i=19;i>=0;--i){
            if(dd&(1<<i)){
                x=anc[i][x];
            }
        }
        if(x==y){
            return x;
        }
        for(int i=19;i>=0;--i){
            if(anc[i][x]!=anc[i][y]){
                x=anc[i][x],y=anc[i][y];
            }
        }
        return anc[0][x];
    }
    int main(){
        int T;
        scanf("%d%d%d",&n,&T,&root);
        int a,b;
        for(int i=1;i<n;++i){
            scanf("%d%d",&a,&b);
            build(a,b);
            build(b,a);
        }
        dfs(root,0);
        while(T--){
            scanf("%d%d",&a,&b);
            printf("%d\n",lca(a,b));
        }
        return 0;
    } 

# 动态规划

## 数位DP

    typedef long long ll;
    int a[20];
    ll dp[20][state];//不同题目状态不同
    ll dfs(int pos,/*state变量*/,bool lead/*前导零*/,bool limit/*数位上界变量*/)//不是每个题都要判断前导零
    {
        //递归边界，既然是按位枚举，最低位是0，那么pos==-1说明这个数我枚举完了
        if(pos==-1) return 1;/*这里一般返回1，表示你枚举的这个数是合法的，那么这里就需要你在枚举时必须每一位都要满足题目条件，也就是说当前枚举到pos位，一定要保证前面已经枚举的数位是合法的。不过具体题目不同或者写法不同的话不一定要返回1 */
        //第二个就是记忆化(在此前可能不同题目还能有一些剪枝)
        if(!limit && !lead && dp[pos][state]!=-1) return dp[pos][state];
        /*常规写法都是在没有限制的条件记忆化，这里与下面记录状态是对应，具体为什么是有条件的记忆化后面会讲*/
        int up=limit?a[pos]:9;//根据limit判断枚举的上界up;这个的例子前面用213讲过了
        ll ans=0;
        //开始计数
        for(int i=0;i<=up;i++)//枚举，然后把不同情况的个数加到ans就可以了
        {
            if() ...
            else if()...
            ans+=dfs(pos-1,/*状态转移*/,lead && i==0,limit && i==a[pos]) //最后两个变量传参都是这样写的
            /*这里还算比较灵活，不过做几个题就觉得这里也是套路了
            大概就是说，我当前数位枚举的数是i，然后根据题目的约束条件分类讨论
            去计算不同情况下的个数，还有要根据state变量来保证i的合法性，比如题目
            要求数位上不能有62连续出现,那么就是state就是要保存前一位pre,然后分类，
            前一位如果是6那么这意味就不能是2，这里一定要保存枚举的这个数是合法*/
        }
        //计算完，记录状态
        if(!limit && !lead) dp[pos][state]=ans;
        /*这里对应上面的记忆化，在一定条件下时记录，保证一致性，当然如果约束条件不需要考虑lead，这里就是lead就完全不用考虑了*/
        return ans;
    }
    ll solve(ll x)
    {
        int pos=0;
        while(x)//把数位都分解出来
        {
            a[pos++]=x%10;//个人老是喜欢编号为[0,pos),看不惯的就按自己习惯来，反正注意数位边界就行
            x/=10;
        }
        return dfs(pos-1/*从最高位开始枚举*/,/*一系列状态 */,true,true);//刚开始最高位都是有限制并且有前导零的，显然比最高位还要高的一位视为0嘛
    }
    int main()
    {
        ll le,ri;
        while(~scanf("%lld%lld",&le,&ri))
        {
            //初始化dp数组为-1,这里还有更加优美的优化,后面讲
            printf("%lld\n",solve(ri)-solve(le-1));
        }
    }  

## 期望DP

### 高次期望

一个01串中每个长度为$X$的全1子串可贡献$X^3$的分数。

给出n次操作的成功率（在01串后附加1的概率）$p[i]$，求分数的期望。

设$a[i]$表示前i位中第i位为1的长度的期望：

$a[i]=(a[i−1]+1)×p[i]$

即为在i-1的末尾加一个概率为$p[i]$出现的1

设$b[i]$表示前i位中第i位为1的长度的平方的期望，$(x+1)^2=x^2+2x+1$,故

$b[i]=(b[i−1]+2×a[i−1]+1)×p[i]$

同理，设$c[i]$表示前i位中第i位为1的长度的立方的期望：($(x+1)^3=x^3+3x^2+3x+1$)

$c[i]=(c[i−1]+3×b[i−1]+3×a[i−1]+1)×p[i]$

但本题求的是前n位（而不是第n）的得分期望，故

$f[i]=(f[i−1]+3×b[i−1]+3×a[i−1]+1)×p[i]+f[i-1]×(1-p[i])$

$=f[i-1]+(3×b[i−1]+3×a[i−1]+1)×p[i])$

        for(int i=1;i<=n;++i){
            double p;scanf("%lf",&p);
            a[i]=(a[i-1]+1)*p;
            b[i]=(b[i-1]+a[i-1]*2+1)*p;
            f[i]=f[i-1]+(3*b[i-1]+3*a[i-1]+1)*p;
        }  

# 计算几何

    const double eps=1e-8;
    const double PI=acos(-1.0);
    int sgn(double x){
        if(fabs(x)<eps) return 0;
        else return x<0?-1:1;
    }
    struct Point{
        double x,y;
        Point(){}
        Point(double xx,double yy){x=xx;y=yy;}
        void input(){
            scanf("%lf%lf",&x,&y);
        }
        void pt(){
            printf("%.10f %.10f\n",x,y);
        }
        Point operator + (const Point &b)const{
            return Point(x+b.x,y+b.y);
        }
        Point operator - (const Point &b)const{
            return Point(x-b.x,y-b.y);
        }
        double operator ^ (const Point &b)const{
            return x*b.y-y*b.x;
        }
        double operator * (const Point &b)const{
            return x*b.x+y*b.y;
        }
        Point operator * (const double &k)const{
            return Point(x*k,y*k); 
        }
        Point operator / (const double &k)const{
            return Point(x/k,y/k); 
        }
        bool operator == (const Point &b)const{
            return sgn(x-b.x)==0&&sgn(y-b.y)==0;
        }
        bool operator < (const Point &b)const{
            return sgn(x-b.x)==0?y<b.y:x<b.x;//Andrew算法中升序排序 
        }
    };
    typedef Point Vector;
    double norm(Vector a){
        return a.x*a.x+a.y*a.y;
    }
    double len(Vector a){
        return sqrt(a.x*a.x+a.y*a.y);
    }
    Point proj(Point p,Point a,Point b){//p在ab上的投影(Projection)
        Vector v=b-a;
        return a+v*(v*(p-a)/norm(v));
    }
    Point refl(Point p,Point a,Point b){//p关于ab的映像(Reflection)
        return p+(proj(p,a,b)-p)*2;
    }
    double dist(Point p,Point a,Point b){//点到直线的距离 
        Vector v1=b-a,v2=p-a;
        return fabs((v1^v2)/len(v1));
    }
    double distPS(Point p,Point a,Point b){//点（Point）到线段（Segment）的距离 
        if(a==b) return len(p-a);
        Vector v=b-a,v1=p-a,v2=p-b;
        if(sgn(v*v1)<0) return len(v1);
        if(sgn(v*v2)>0) return len(v2);
        return dist(p,a,b);
    }
    int ccw(Point p,Point a,Point b){//判断a,b相对于p点的关系 
        Vector v1=a-p,v2=b-p;
        if((v1^v2)>eps) return 1;//逆时针 counter clockwise
        if((v1^v2)<-eps) return -1;//顺时针 clockwise
        if(v1*v2<-eps) return 2;//b在pa的反方向（180 degrees)
        if(norm(v1)<norm(v2)) return -2;//b在pa的前方（0 degrees） 
        return 0;//b在pa上  On segment 
    }
    bool intersect(Point a,Point b,Point x,Point y){//线段相交判断 
    //  if(a==x||a==y||b==x||b==y) return 1;//误差小时，端点重合时ccw会判成On segment，不必单独判 
        return (ccw(a,b,x)*ccw(a,b,y)<=0&&ccw(x,y,a)*ccw(x,y,b)<=0);
    }
    double distSS(Point a,Point b,Point x,Point y){//线段（Segment）间的距离 
        if(intersect(a,b,x,y)) return 0.0;
        return min(min(distPS(a,x,y),distPS(b,x,y)),
            min(distPS(x,a,b),distPS(y,a,b)));
    }
    Point crosspoint(Point a,Point b,Point x,Point y){//直线交点 
        Vector v=y-x;
        double d1=fabs(v^(a-x)),d2=fabs(v^(b-x));
        return a+(b-a)*(d1/(d1+d2));
    }
    double polygon_area(Point p[],int n){//n边形面积，从0开始存点 
        double area=0;
        for(int i=1;i<n-1;++i){
            area+=(p[i]-p[0])^(p[i+1]-p[0]);
        }
        return area/2;
    }
    typedef vector<Point> Polygon;
    Polygon andrewScan(Polygon &s){
        Polygon u,l;
        if(s.size()<3) return s;
        sort(s.begin(),s.end());
        u.push_back(s[0]);u.push_back(s[1]);
        for(int i=2;i<s.size();++i){
            for(int n=u.size();n>=2&&ccw(u[n-2],u[n-1],s[i])>0;--n){
                //u[n-2].pt();u[n-1].pt();s[i].pt();
                //cout<<ccw(u[n-2],u[n-1],s[i])<<endl;
                u.pop_back();//ccw!=-1弹出，（仅顺时针时保留）:选尽可能少的点，共线的不计入 
            }                //ccw>0弹出，ccw取-1，-2时保留:共线的时候计入 
            u.push_back(s[i]);
        }
        l.push_back(s[s.size()-1]);l.push_back(s[s.size()-2]);
        for(int i=s.size()-3;i>=0;--i){
            for(int n=l.size();n>=2&&ccw(l[n-2],l[n-1],s[i])>0;--n){
                l.pop_back();
            }
            l.push_back(s[i]);
        }
        reverse(l.begin(),l.end());
        for(int i=u.size()-2;i>=1;--i){//逆时针存储 
            l.push_back(u[i]);
        }
        //求凸包周长 
        int ans=0;
        for(int i=1;i<l.size();++i){
            double xx=l[i-1].x-l[i].x,yy=l[i-1].y-l[i].y;
            ans+=sqrt(xx*xx+yy*yy);
        }
        double xx=l[0].x-l[l.size()-1].x,yy=l[0].y-l[l.size()-1].y;
        ans+=sqrt(xx*xx+yy*yy);
        
        return l; 
    } 

# 杂项

## 莫队

### 普通莫队

小Z的袜子

题意：求区间的$\frac{\sum C_{cnt_i}^{2}}{C_{len}^{2}}=\frac{(\sum cnt_i^2) -len}{len(len-1)}$

用莫队算法维护区间内的各种颜色数量的平方和

    const int N=50000+5;
    typedef long long ll;
    struct Qy{
        int l,r,id;
        ll a,b;
    }qu[N];
    int pos[N],c[N],n,m,l=1,r;
    ll cnc[N];//count of color
    ll ans;

    inline ll sqr(ll x){
        return x*x;
    }

    inline void upd(ll p,ll dlt){
        ans-=sqr(cnc[c[p]]);
        cnc[c[p]]+=dlt;
        ans+=sqr(cnc[c[p]]);
    }

    int main(){
        scanf("%d%d",&n,&m);
        ll bsz=sqrt(n);
        for(int i=1;i<=n;++i){
            scanf("%d",&c[i]);
            pos[i]=(i-1)/bsz+1;
        }
        for(int i=1;i<=m;++i){
            scanf("%d%d",&qu[i].l,&qu[i].r);
            qu[i].id=i;
        }
        sort(qu+1,qu+m+1,[](Qy x,Qy y){return (pos[x.l]==pos[y.l]?x.r<y.r:pos[x.l]<pos[y.l]);});
        for(int i=1;i<=m;++i){
            for(;l<qu[i].l;++l){
                upd(l,-1);
            }
            for(;l>qu[i].l;--l){
                upd(l-1,1);
            }
            for(;r<qu[i].r;++r){
                upd(r+1,1);
            }
            for(;r>qu[i].r;--r){
                upd(r,-1);
            }
            if(qu[i].l==qu[i].r){
                qu[i].a=0,qu[i].b=1;
                continue;
            }
            ll len=qu[i].r-qu[i].l+1;
            qu[i].a=ans-len;
            qu[i].b=len*(len-1);
            ll g=__gcd(qu[i].a,qu[i].b);
            qu[i].a/=g,qu[i].b/=g;
        }
        sort(qu+1,qu+m+1,[](Qy x,Qy y){return x.id<y.id;});
        for(int i=1;i<=m;++i){
            printf("%lld/%lld\n",qu[i].a,qu[i].b);
        }
        return 0;
    } 

### 回滚莫队

对原序列进行分块，对询问按以左端点所属块编号升序为第一关键字，右端点升序为第二关键字的方式排序

如果询问左端点所属块B和上一个询问左端点所属块的不同，那么将莫队区间的左端点初始化为B的右端点加1,
将莫队区间的右端点初始化为B的右端点

1\. 如果询问的左右端点所属的块相同，那么直接扫描区间回答询问 2.
如果询问的左右端点所属的块不同
(1)如果询问的右端点大于莫队区间的右端点，那么不断扩展右端点直至莫队区间的右端点等于询问的右端点
(2)不断扩展莫队区间的左端点直至莫队区间的左端点等于询问的左端点
(3)回答询问
(4)撤销莫队区间左端点的改动，使莫队区间的左端点回滚到B的右端点加1

    typedef long long ll;
    const int N=1e5+5;
    struct Qy{
        int l,r,id;
    }qu[N];
    int a[N],lsh[N],n,m,nn,val[N],belo[N],ct[N],cnt[N],L[N],R[N];
    ll ans[N],mx;
    void discrete(){
        sort(lsh+1,lsh+n+1);
        nn=unique(lsh+1,lsh+n+1)-lsh-1;
        for(int i=1;i<=n;++i){
            val[i]=lower_bound(lsh+1,lsh+nn+1,a[i])-lsh;
        }
    }
    inline void del(int x){
        --cnt[val[x]];
    }
    inline void add(int x,ll& v){
        ++cnt[val[x]];
        v=max(v,1ll*cnt[val[x]]*a[x]);
    }
    int main(){
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;++i){
            scanf("%d",&a[i]);
            lsh[i]=a[i];
        }
        for(int i=1;i<=m;++i){
            scanf("%d%d",&qu[i].l,&qu[i].r);
            qu[i].id=i;
        }
        discrete();//离散化 
        int bsz=sqrt(n),bc=n/bsz;//bsz:block size bc:block count
        for(int i=1;i<=bc;++i){
            if(i*bsz>n) break;
            L[i]=(i-1)*bsz+1;
            R[i]=i*bsz;
        }
        if(R[bc]<n){
            ++bc;L[bc]=R[bc-1]+1;R[bc]=n;
        }
        for(int i=1;i<=bc;++i){
            for(int j=L[i];j<=R[i];++j){
                belo[j]=i;
            }
        }
        sort(qu+1,qu+m+1,[](Qy a,Qy b){
            return belo[a.l]==belo[b.l]?a.r<b.r:belo[a.l]<belo[b.l];
        });
        int l=1,r=0,lb=0;//lb:last block
        for(int i=1;i<=m;++i){
            if(belo[qu[i].l]==belo[qu[i].r]){
                for(int j=qu[i].l;j<=qu[i].r;++j) ++ct[val[j]];
                ll tmp=0;
                for(int j=qu[i].l;j<=qu[i].r;++j) tmp=max(tmp,1ll*ct[val[j]]*a[j]);
                ans[qu[i].id]=tmp;
                for(int j=qu[i].l;j<=qu[i].r;++j) --ct[val[j]];
                continue;
            }
            if(lb!=belo[qu[i].l]){
                int p=R[belo[qu[i].l]];
                while(r>p) del(r--);
                while(l<p+1) del(l++);
                mx=0,lb=belo[qu[i].l];
            }
            while(r<qu[i].r) add(++r,mx);
            ll temp=mx;
            int tl=l;
            while(tl>qu[i].l) add(--tl,temp);
            while(tl<l) del(tl++);//回滚！
            ans[qu[i].id]=temp; 
        }
        for(int i=1;i<=m;++i){
            printf("%lld\n",ans[i]);
        }
        return 0;
    }

## 随机化

### 模拟退火

    //Simulate Anneal
    const double eps=1e-9;
    bool accept(double delta,double temper){
        if(delta>=0) return 1;
        return rand()<exp(delta/temper)*RAND_MAX;
    }
    //F(x)=6*x^7+8*x^6+7*x^3+5*x^2-y*x
    long long y;
    double f(double x){
        return x*(-y+x*(5+x*(7+x*x*x*(8+6*x))));
        //return 6*x*x*x*x*x*x*x+8*x*x*x*x*x*x+7*x*x*x+5*x*x-y*x;
    }
    double solve(){
        double t=50,del=0.98;
        double plan=50;
        while(t>eps){
            double newplan=plan+((double)rand()/RAND_MAX*2-1)*t;
            if(newplan<=100&&newplan>=0&&accept(f(plan)-f(newplan),t)) plan=newplan;
            t*=del;
        }
        return f(plan);
    }
    double do_main(){
        srand(time(0));
        double res=9e18;
        int T=10;
        while(T--){
            res=min(res,solve());
        }
        return res;
    }

## PBDS

### 可并堆

        #include<ext/pb_ds/priority_queue.hpp>
        #include<iostream>
        #include<cstdio>
        using namespace std;
        typedef __gnu_pbds::priority_queue<pair<int,int>,greater<pair<int,int> > > heap;
        const int MAXN=100000+5;
        heap h[MAXN];
        int fa[MAXN];
        bool del[MAXN];
        int find(int x){
            return fa[x]==x?x:fa[x]=find(fa[x]);
        }
        int main(){
            int n,m,t,x,y;
            scanf("%d%d",&n,&m);
            for(int i=1;i<=n;++i){
                scanf("%d",&t);
                h[i].push(make_pair(t,i));
                fa[i]=i;
            }
            while(m--){
                scanf("%d%d",&t,&x);
                if(t==1){
                    scanf("%d",&y);
                    if(del[x]||del[y])
                        continue;
                    int fx=find(x),fy=find(y);
                    if(fx!=fy){
                        fa[fy]=fx;
                        h[fx].join(h[fy]);
                    }
                }
                else{
                    if(del[x])
                        puts("-1");
                    else{
                        int fx=find(x);
                        printf("%d\n",h[fx].top().first);
                        del[h[fx].top().second]=1;
                        h[fx].pop();
                    }
                }
            }
            return 0;
        }

### 斐波那契堆Dijkstra

    #include<ext/pb_ds/priority_queue.hpp>
    #include<iostream>
    #include<cstdio>
    #include<cstring>
    using namespace std;
    const int N=1e5+5,M=4e5+5;
    typedef int ll;
    typedef pair<ll,ll> pii;
    typedef __gnu_pbds::priority_queue<pii,greater<pii> > heap;
    heap q;
    heap::point_iterator id[N];
    struct Edge{
        ll t,v,n;
    }e[M];
    ll hd[N],cho[N],dis[N],cnt;
    void build(int f,int t,int v){
        e[++cnt]=(Edge){t,v,hd[f]};
        hd[f]=cnt;
    }
    int main(){
        int n,m,S;
        scanf("%d%d%d",&n,&m,&S);
        memset(dis,0x3f,sizeof dis);
        for(int i=1;i<=m;++i){
            int a,b,c;
            scanf("%d%d%d",&a,&b,&c);
            build(a,b,c);
        }
        dis[S]=0;
        id[S]=q.push(pii(0,S));
        while(!q.empty()){
            pii pu=q.top();
            q.pop();
            int u=pu.second;
            if(cho[u]) continue;
            cho[u]=1;
            for(int i=hd[u];i;i=e[i].n){
                int v=e[i].t;
                if(dis[v]>dis[u]+e[i].v){
                    dis[v]=dis[u]+e[i].v;
                    if(id[v]!=0){
                        q.modify(id[v],make_pair(dis[v],v));
                    }
                    else{
                        id[v]=q.push(pii(dis[v],v));
                    }
                }
            }
        }
        for(int i=1;i<=n;++i){
            printf("%d ",dis[i]);
        }
        return 0;
    }

### 平衡树

    #include<cstdio>
    #include<iostream>
    #include<ext/pb_ds/assoc_container.hpp>
    #include<ext/pb_ds/tree_policy.hpp>
    using namespace std;
    using namespace __gnu_cxx;
    using namespace __gnu_pbds;
    struct Node{
        int v,clock;
        Node(int v=0,int clock=0):v(v),clock(clock){}
    };
    struct cmp{
        bool operator () (Node A,Node B) const{
            if(A.v==B.v)
                return A.clock<B.clock;
            return A.v<B.v;
        }
    };
    #define null_mapped_type null_type
    //较早版本使用 null_type
    tree<Node,null_mapped_type,cmp,rb_tree_tag,tree_order_statistics_node_update> T;
    tree<Node,null_mapped_type,cmp,rb_tree_tag,tree_order_statistics_node_update>::iterator it;
    int main(){
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;i++){
            int opt,x;
            scanf("%d%d",&opt,&x);
            switch(opt){
                case 1:
                    T.insert(Node(x,i));
                    break;
                case 2:
                    T.erase(T.upper_bound(Node(x,0)));
                    break;
                case 3:
                    it=T.upper_bound(Node(x,0));
                    printf("%lu\n",T.order_of_key(*it)+1);
                    break;
                case 4:
                    it=T.find_by_order(x-1);
                    printf("%d\n",it->v);
                    break;
                case 5:
                    it=T.lower_bound(Node(x,0));
                    printf("%d\n",(--it)->v);
                    break;
                case 6:
                    it=T.upper_bound(Node(x,n+1));
                    printf("%d\n",it->v);
                    break;
            }
        }
        return 0;
    } 

## 归并排序求逆序对

    #define mid(x,y) x+y>>1
    typedef long long ll;
    const int MAXN=100000+5,INF=1e9+7;
    int L[MAXN>>1],R[MAXN>>1];
    int a[MAXN];
    ll merge(int* a,int l,int r){
        ll cnt=0;
        int M=mid(l,r),n1=M-l+1,n2=r-M;
        for(int i=1;i<=n1;++i) L[i]=a[l+i-1];
        for(int i=1;i<=n2;++i) R[i]=a[M+i];
        L[n1+1]=R[n2+1]=INF;
        int m=1,n=1;
        for(int k=l;k<=r;++k){
            if(L[m]<=R[n])
                a[k]=L[m++];
            else a[k]=R[n++],cnt+=n1-m+1;
        }
        return cnt;
    }
    ll mergesort(int *a,int l,int r){
        int M=mid(l,r);
        ll cnt=0;
        if(l==r) return 0;
        cnt+=mergesort(a,l,M);
        cnt+=mergesort(a,M+1,r);
        cnt+=merge(a,l,r);
        return cnt;
    }
    int main(){
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;++i)
            scanf("%d",&a[i]);
        printf("%lld",mergesort(a,1,n));
        return 0;
    }    

## 编译优化

    #define __AVX__ 1
    #define __AVX2__ 1
    #define __SSE__ 1
    #define __SSE2__ 1
    #define __SSE2_MATH__ 1
    #define __SSE3__ 1
    #define __SSE4_1__ 1
    #define __SSE4_2__ 1
    #define __SSE_MATH__ 1
    #define __SSSE3__ 1
    //C++11后，以上须定义在stdc++.h前
    #pragma GCC optimize("Ofast,no-stack-protector,unroll-loops,fast-math")
    #pragma GCC target("sse,sse2,sse3,ssse3,sse4,popcnt,abm,mmx,avx,avx2,tune=native")
