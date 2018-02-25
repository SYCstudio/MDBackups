# HDU 3501 Calculation 2

###  Description
Given a positive integer N, your task is to calculate the sum of the positive integers less than N which are not coprime to N. A is said to be coprime to B if A, B share no common positive divisors except 1. 
### Http
https://vjudge.net/problem/HDU-3501
### Tag
欧拉函数
## 题目大意
求\\(\sum\_{i=1}^{n} [gcd(i,n)!=1]\*i\\)
## 解决思路
考虑若有\\(gcd(i,n)==1\\)，那一定会有$$gcd(n-i,n)==1$$，那么可以知道，与一个数互质的数一定是一组一组出现的，所以可以知道与$$N$$互质的数的和就是$$N\*\phi(N)/2$$。那么根据补集原理，用$$1~N$$的和减去与$$N$$互质的数之和就是与$$N$$不互质的数之和啦。
注意特判1。
## 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
using namespace std;

#define ll long long

const ll Mod=1000000007;

ll Phi(ll x);
ll Inv(ll a,ll b);
void Exgcd(ll a,ll b,ll &x,ll &y);

int main()
{
    ll inv2=Inv(2,Mod);
    ll n;
    while (scanf("%lld",&n))
    {
		if (n==0) break;
		if (n==1) {printf("0\n");continue;}
		printf("%lld\n",(ll)n*(ll)(n-1ll-Phi(n))%Mod*inv2%Mod);
    }
    return 0;
}

ll Phi(ll x)
{
    if (x==1) return 1;
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

ll Inv(ll a,ll b)
{
    ll x,y;
    Exgcd(a,b,x,y);
    return (x%b+b)%b;
}

void Exgcd(ll a,ll b,ll &x,ll &y)
{
    if (b==0)
    {
		x=1;y=0;
		return;
    }
    Exgcd(b,a%b,x,y);
    ll tmp=x;
    x=y;y=tmp-a/b*y;
    return;
}
```
