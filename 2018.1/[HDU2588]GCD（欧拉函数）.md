###  Description
The greatest common divisor GCD(a,b) of two positive integers a and b,sometimes written (a,b),is the largest divisor common to a and b,For example,(1,2)=1,(12,18)=6.
(a,b) can be easily found by the Euclidean algorithm. Now Carp is considering a little more difficult problem:
Given integers N and M, how many integer X satisfies 1<=X<=N and (X,N)>=M. 
### Http
[HDU](https://vjudge.net/problem/HDU-2588)
### Tag
欧拉函数
## 题目大意
给定数\\(N\\)，求满足\\(gcd(x,N)>=M\\)的\\(X\\)的数量
## 解决思路
考虑\\(N=a\*b\\)，若\\(gcd(N=a\*b,a\*c)==a\\)，则$$b$$与$$c$$互质，而可能的$$c$$的数量就是与$$b$$互质的数的个数，即为$$\phi(b)$$，所以可以考虑枚举$$N$$的因子$$a$$，求$$\phi(a)$$之和。
但是考虑到$$N$$的范围，直接枚举会超时。因为对于$$N$$的因数$$a$$，一定存在一个对应的因数$$b$$使得$$N=a\*b$$，所以我们只需要枚举到$$\sqrt{n}$$即可，注意判断$$n$$为完全平方数的情况，此时$$\sqrt{n}$$只能算一遍
## 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
using namespace std;

#define ll long long
#define mem(Arr,x) memset(Arr,x,sizeof(Arr))

ll Phi(ll x);

int main()
{
    int T;scanf("%d",&T);
    while(T--)
    {
		ll n,m;scanf("%lld%lld",&n,&m);
		ll ans=0;
		for (ll i=1;i*i<=n;i++)
			if (n%i==0)
			{
				if (i>=m) ans=ans+Phi(n/i);
				if ((n/i>=m)&&(i*i!=n)) ans=ans+Phi(i);//注意这里不能重复计算完全平方数
			}
		printf("%lld\n",ans);
    }
    return 0;
}

ll Phi(ll x)//求φ
{
    ll ret=x;
    for (ll i=2;i*i<=x;i++)
		if (x%i==0)
		{
			ret=ret/i*(i-1);
			while (x%i==0) x=x/i;
		}
    if (x>1) ret=ret/x*(x-1);
    return ret;
}
```
