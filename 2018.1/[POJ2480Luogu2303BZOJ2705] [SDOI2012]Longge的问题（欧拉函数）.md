###  Description
Longge的数学成绩非常好，并且他非常乐于挑战高难度的数学问题。现在问题来了：给定一个整数N，你需要求出∑gcd(i, N)(1<=i <=N)。
### Http
[POJ](https://vjudge.net/problem/POJ-2480)
[BZOJ](http://www.lydsy.com/JudgeOnline/problem.php?id=2705)
[Luogu](https://www.luogu.org/problemnew/show/P2303)
### Tag
欧拉函数
## 题目大意
求\\[\sum\_{i=1}^{n} gcd(i,n)\\]
## 解决思路
与[这一题](http://sycstudio.com/archives/110)类似，设\\(n=a\*b\\)，那么若有\\(gcd(a\*b,a\*c)==a\\)，则\\(gcd(b,c)==1\\)，那么题目变成求\\(\phi(n/a)\\)。
## 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
using namespace std;

#define ll long long

ll Phi(ll x);

int main()
{
	ll n;
	while (scanf("%lld",&n)!=EOF)
	{
		ll ans=0;
		for (ll i=1;(ll)i*(ll)i<=n;i++)
			if (n%i==0)
			{
				ans=ans+(ll)i*(ll)Phi(n/i);
				if (i*i!=n) ans=ans+(ll)(n/i)*(ll)Phi(i);
			}
		printf("%lld\n",ans);
	}
	return 0;
}

ll Phi(ll x)
{
	//cout<<"Phi:"<<x<<" ";
	ll ret=x;
	for (ll i=2;(ll)i*(ll)i<=x;i++)
		if (x%i==0)
		{
			ret=ret/i*(i-1);
			while (x%i==0) x=x/i;
		}
	if (x!=1) ret=ret/x*(x-1);
	//cout<<ret<<endl;
	return ret;
}
```
