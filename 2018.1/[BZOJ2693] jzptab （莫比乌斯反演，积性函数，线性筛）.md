###  Description
![BZOJ2693](http://sycstudio.com/bzojch/file/2693_0.jpg)
### Http
[BZOJ](http://www.lydsy.com/JudgeOnline/problem.php?id=2693)
权限题，提供本站离线版本：[bzojch](http://sycstudio.com/bzojch/p/2693.html)
### Tag
莫比乌斯反演，线性筛，积性函数
## 题目大意
与[这道题](http://sycstudio.com/archives/201)一样，但变成了多组数据。
## 解决思路
前面的式子到[这道题](http://sycstudio.com/archives/201)查看，我们直接从那一道题的结尾开始推。
在那道题的最后，我们得到
\\[Ans=\sum\_{d=1}^{n} d \* \sum\_{x=1}^{n/d} \mu(x) \* x^2 \* \sum\_{i=1}^{n/d/x} \sum\_{j=1}^{m/d/x} ij\\]
由于后面是两个等差数列求和，所以我们设\\(S(i)=\frac{(i+1)\*i}{2}\\)，那么就有
\\[Ans=\sum\_{d=1}^{n} d\* \sum\_{x=1}^{n/d} \mu(x) \* x^2 \* S(\frac{n}{dx}) \* S(\frac{m}{dx})\\]
设\\(T=x\* d\\)，则
\\[Ans=\sum\_{d=1}^{n} d\* \sum\_{x=1}^{n/d} \mu(x) \* x^2 \* S(\frac{n}{T}) \* S(\frac{m}{T})\\]
现在我们考虑枚举\\(T\\)，那么因为\\(T=x\*d\\)，所以对于每一个\\(d|T\\)，\\(d\\)都有\\(\mu(x)\*d\*x^2\\)的贡献。又因为\\(x=\frac{T}{d}\\)，所以，式子就化为
\\[Ans=\sum\_{T=1}^{n} S(\frac{n}{T}) \* S(\frac{m}{T}) \* \sum\_{d|T} \mu(d) Td\\]
后面这一部分是积性函数，可以线性筛出来的，具体讨论一下。
设\\(p\\)为质数，设\\[f(x)=\sum\_{d|T} \mu(d) T\*d=T\*\sum\_{d|T} \mu(d) \* d\\]
那么就有
\\[\begin{cases} f(p)=p\*(\mu(1)\*1+\mu(p)\*p)=p-p^2 \\\\ f(p\*i)=f(p)\*f(i) \quad \quad \quad \quad \quad \quad \quad gcd(i,p)==1 \\\\ f(p\*i)=p\*f(i) \quad \quad \quad \quad \quad \quad \quad \quad gcd(i,p)==p \end{cases}\\]
线性筛出\\(f(i)\\)，求前缀和数论分块，就可以把单次询问的复杂度降到\\(O(\sqrt{n})\\)
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

const int maxNum=10000100;
const ll Mod=100000009;
const int inf=2147483647;

ll Sum[maxNum];
ll pricnt=0,Prime[maxNum];
bool notprime[maxNum];

void Sieve();//线性筛求f(i)

int main()
{
	Sieve();
	int T;scanf("%d",&T);
	while (T--)
	{
		ll n,m;scanf("%lld%lld",&n,&m);if (n>m) swap(n,m);
		ll ans=0;
		for (ll i=1,last;i<=n;i=last+1)
		{
			last=min(n/(n/i),m/(m/i));//数论分块
			ll l1=n/i,l2=m/i;
			ll s1=l1*(1ll+l1)/2%Mod,s2=l2*(1ll+l2)/2%Mod;//这里计算出来的是两个等差数列的和
			ans=((ans+(((Sum[last]-Sum[i-1])%Mod+Mod)%Mod)*s1%Mod*s2%Mod)%Mod+Mod)%Mod;
		}
		printf("%lld\n",ans);
	}
	return 0;
}

void Sieve()//线性筛
{
	notprime[1]=1;Sum[1]=1;
	for (ll i=2;i<maxNum;i++)
	{
		if (notprime[i]==0) Prime[++pricnt]=i,Sum[i]=(i-i*i+Mod)%Mod;
		for (ll j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j]<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0)
			{
				Sum[i*Prime[j]]=Sum[i]*Prime[j]%Mod;break;
			}
			Sum[i*Prime[j]]=Sum[i]*Sum[Prime[j]]%Mod;
		}
	}
	for (int i=1;i<maxNum;i++) Sum[i]=((Sum[i]+Sum[i-1])%Mod+Mod)%Mod;//计算前缀和
	return;
}
```
