###  Description
 Given the value of N, you will have to find the value of G. The definition of G is given below:

Here GCD(i,j) means the greatest common divisor of integer i and integer j.

For those who have trouble understanding summation notation, the meaning of G is given in the following code:
```cpp
G=0;
for(i=1;i<N;i++)
	for(j=i+1;j<=N;j++)
	{
    	G+=gcd(i,j);
	}
/*Here gcd() is a function that finds the greatest common divisor of the two input numbers*/
```
### Http
[UVA](https://vjudge.net/problem/UVA-11426)
### Tag
莫比乌斯反演，数论分块
## 题目大意
给定\\(N\\)，求\\(\sum\_{i=1}^{N} \sum\_{j=i+1}^{N} gcd(i,j)\\)
## 解决思路
先不考虑\\(j=i+1\\)的限制，假设\\(j\\)也是\\([1,n]\\)，则有
\\[Ans=\sum\_{i=1}^{N} \sum\_{j=1}^{N} gcd(i,j) \\]
我们设\\(gcd(i,j)==d\\)，把\\(d\\)提出来
\\[Ans=\sum\_{d}^{N} d \* \sum\_{i}^{N} \sum\_{j}^{N} [gcd(i,j)==d]=\sum\_{d}^{N} d \* \sum\_{i}^{N/d} \sum\_{j}^{N/d} [gcd(i,j)==1]\\]
观察里面这一部分，就是莫比乌斯反演的经典例子。根据前面的题，可以得到
\\[\sum\_{i}^{N/d} \sum\_{j}^{N/d} [gcd(i,j)==1]=\sum\_{i}^{N/d} \mu(i) \*\lfloor \frac{\lfloor \frac{N}{d} \rfloor }{i} \rfloor \* \lfloor \frac{\lfloor \frac{N}{d} \rfloor }{i} \rfloor\\]
把这个等式代回去，可以得到
\\[Ans=\sum\_{d=1}^{N} d\* \sum\_{i=1}^{n/d} \mu(i) \* \lfloor \frac{\lfloor \frac{N}{d} \rfloor }{i} \rfloor \* \lfloor \frac{\lfloor \frac{N}{d} \rfloor }{i} \rfloor\\]
这样直接算的复杂度是$$O(n\*n)=O(n^2)$$的。考虑数论分块降低复杂度。由于对于外面的\\(d\\)，\\(n/d\\)对于多个连续的\\(d\\)的取值是一样的，外面可以数论分块。同理，\\(\lfloor \frac{\lfloor \frac{N}{d} \rfloor }{i} \rfloor\\)也是可以分块做的。所以复杂度就降为\\(O(\sqrt{n}\*\sqrt{n})=O(n)\\)。
最后我们把\\(j=i+1\\)的条件加上，其实意思就是不能重复计算\\(gcd(i,j)\\)和\\(gcd(j,i)\\)。考虑我们上面的\\(Ans\\)计算出来的，它是把每一个\\(gcd(i,i)\\)都计算了一遍，再加上对于\\(i &lt; j\\)，\\(gcd(i,j)\\)计算了两边，即既计算了\\(gcd(i,j)\\)，又计算了\\(gcd(j,i)\\)。所以我们可以在\\(Ans\\)中先减去\\(gcd(i,i)\\)这一类的贡献（其实就是\\(1..n\\)的等差数列），再除以二。
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

const int maxNum=4000010;
const int inf=2147483647;

ll Mu[maxNum];
ll Musum[maxNum];
ll pricnt=0,Prime[maxNum];
bool notprime[maxNum];

void GetMu();//线性筛求mu
ll Calc(ll n);//内层线性筛

int main()
{
	GetMu();
	ll n;
	while (scanf("%lld",&n)!=EOF)
	{
		if (n==0) break;
		ll ans=0;
		for (ll i=1,last;i<=n;i=last+1)//外层线性筛
		{
			last=n/(n/i);
			ans=ans+(last+i)*(last-i+1)/2*Calc(n/i);
		}
		printf("%lld\n",(ans-n*(n+1)/2)/2);
	}
	return 0;
}

void GetMu()
{
	Mu[1]=1;notprime[1]=1;
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
	for (int i=1;i<maxNum;i++) Musum[i]=Musum[i-1]+Mu[i];
	return;
}

ll Calc(ll n)//内层线性筛
{
	ll ret=0;
	for (ll i=1,last;i<=n;i=last+1)
	{
		last=n/(n/i);
		ret=ret+(Musum[last]-Musum[i-1])*(ll)(n/i)*(ll)(n/i);
	}
	return ret;
}
```
