###  Description
今天的数学课上，Crash小朋友学习了最小公倍数(Least Common Multiple)。对于两个正整数a和b，LCM(a, b)表示能同时被a和b整除的最小正整数。例如，LCM(6, 8) = 24。回到家后，Crash还在想着课上学的东西，为了研究最小公倍数，他画了一张N\*M的表格。每个格子里写了一个数字，其中第i行第j列的那个格子里写着数为LCM(i, j)。一个4\*5的表格如下： 1 2 3 4 5 2 2 6 4 10 3 6 3 12 15 4 4 12 4 20 看着这个表格，Crash想到了很多可以思考的问题。不过他最想解决的问题却是一个十分简单的问题：这个表格中所有数的和是多少。当N和M很大时，Crash就束手无策了，因此他找到了聪明的你用程序帮他解决这个问题。由于最终结果可能会很大，Crash只想知道表格里所有数的和mod 20101009的值。
### Http
[BZOJ](http://www.lydsy.com/JudgeOnline/problem.php?id=2154)
[Luogu](https://www.luogu.org/record/show?rid=5368858)
### Tag
莫比乌斯反演，数论分块
## 题目大意
求\\(\sum\_{i}^{n} \sum\_{j}^{m} lcm(i,j)\\)，其中\\(lcm(i,j)\\)表示\\(i,j\\)的最小公倍数
## 解决思路
根据\\(lcm(i,j)=\frac{ij}{gcd(i,j)}\\)，式子可以化为
\\[Ans=\sum\_{i=1}^{n} \sum\_{j=1}^{m} \frac{ij}{gcd(i,j)}\\]
令\\(d=gcd(i,j)\\)，把\\(d\\)提出来，即变成枚举\\(d\\)。
\\[Ans=\sum\_{d=1}^{n} \sum\_{i=1}^{n} \sum\_{j=1}^{m} [gcd(i,j)==d] \* \frac{ij}{d}\\]
`[  ]`里面有\\(d\\)，把它提出来。后面的\\(i,j\\)相当于都提取出一个公因子\\(d\\)，变成\\(\frac{i}{d},\frac{j}{d}\\)
\\[Ans=\sum\_{d=1}^{n} \sum\_{i=1}^{n/d} \sum\_{j=1}^{m/d} [gcd(i,j)==1] \* dij \\\\ Ans=\sum\_{d=1}^{n} d \* \sum\_{i=1}^{n/d} \sum\_{j=1}^{m/d} [gcd(i,j)==1] \* ij\\]
前面先不管它，先看后面这一部分
设\\[f(x)=\sum\_{i=1}^{n/d} \sum\_{j=1}^{m/d} [gcd(i,j)==x] \* ij\\]。设\\[g(x)=\sum\_{x|t} f(t)\\]
\\[g(x)=\sum\_{i=1}^{n/d} \sum\_{j=1}^{m/d} [x|gcd(i,j)]\* ij \\]
提一个\\(x\\)出来
\\[g(x)=\sum\_{i=1}^{n/d/x} \sum\_{j=1}^{m/d/x} [1|gcd(i,j)] i\*x \*j\*x\\]
\\([1|gcd(i,j)]\\)是显然成立的。所以
\\[g(x)=x^2\*\sum\_{i=1}^{n/d/x} \sum\_{j=1}^{m/d/x} ij\\]
那么这就是两个等差数列相乘了，可以\\(O(1)\\)地做
根据莫比乌斯反演
\\[f(x)=\sum\_{x|t}^{n/d} \mu(\frac{t}{x}) g(t) \\\\ = \sum\_{x|t}^{n/d} \mu(\frac{t}{x}) t^2 \sum\_{i=1}^{n/d/t} \sum\_{j=1}^{m/d/t} ij\\]
由于我们要求的是\\(f(1)\\)
所以得到\\[f(1)=\sum\_{k=1}^{n/d} \mu(k) k^2 \sum\_{i=1}^{n/d/k} \sum\_{j=1}^{m/d/k} ij\\]
把\\(f(1)\\)代回到\\(Ans\\)
\\[Ans=\sum\_{d=1}^{n} \sum\_{k=1}^{n/d} \mu(k) \* k^2 \sum\_{i=1}^{n/d/k} \sum\_{j=1}^{m/d/k} ij\\]
内外两层数论分块，可以做到总复杂度\\(O(n)\\)
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

const int maxNum=10000010;
const int inf=2147483647;
const ll Mod=20101009;

int Mu[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];
ll Sum[maxNum];

void GetMu();//计算mu
ll Calc(ll n,ll m);//内层数论分块

int main()
{
	GetMu();
	ll n,m;scanf("%lld%lld",&n,&m);if (n>m) swap(n,m);
	ll ans=0;
	for (ll i=1,last;i<=n;i=last+1)
	{
		last=min(n/(n/i),m/(m/i));//外层数论分块
		ll s=(last+i)*(last-i+1)/2;
		ans=(ans+s%Mod*Calc(n/i,m/i)%Mod)%Mod;
	}
	printf("%lld\n",ans);
	return 0;
}

void GetMu()//线性筛
{
	notprime[1]=1;Mu[1]=1;
	for (int i=2;i<maxNum;i++)
	{
		if (notprime[i]==0) Prime[++pricnt]=i,Mu[i]=-1;
		for (int j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j]<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0) break;
			Mu[i*Prime[j]]=-Mu[i];
		}
	}
	for (int i=1;i<maxNum;i++) Sum[i]=((Sum[i-1]+(ll)Mu[i]*(ll)i*(ll)i)%Mod+Mod)%Mod;
	return;
}

ll Calc(ll n,ll m)
{
	if (n>m) swap(n,m);
	ll ret=0;
	for (ll i=1,last;i<=n;i=last+1)
	{
		last=min(n/(n/i),m/(m/i));
		ll l1=n/i,l2=m/i;
		ll s1=(1+l1)*l1/2%Mod,s2=(1+l2)*l2/2%Mod;
		ret=(ret+((Sum[last]-Sum[i-1])+Mod)%Mod*s1%Mod*s2%Mod)%Mod;
	}
	return (ret%Mod+Mod)%Mod;
}
```
