###  Description
As we know, any positive integer C ( C >= 2 ) can be written as the multiply of some prime numbers:
\\(C = p\_1\*p\_2\*p\_3\*...\*p_k\\)
which \\(p\_1, p\_2 ... p\_k\\)are all prime numbers.For example, if \\(C = 24\\), then:
\\(24 = 2\*2\*2\*3\\)
here, \\(p\_1 = p\_2 = p\_3 = 2, p\_4 = 3, k = 4\\)

Given two integers P and C. if k<=P( k is the number of C's prime factors), we call C a lucky number of P.

Now, XXX needs to count the number of pairs (a, b), which 1<=a<=n , 1<=b<=m, and gcd(a,b) is a lucky number of a given P ( "gcd" means "greatest common divisor").

Please note that we define 1 as lucky number of any non-negative integers because 1 has no prime factor. 
### Http
[HDU](https://vjudge.net/problem/HDU-4746)
### Tag
莫比乌斯反演，数论分块
## 题目大意
设$$Fact(x)$$表示把$$x$$唯一分解后，分解出来的质因子的个数（注意，相同的质因子不算同一个），求\\[\sum\_{i}^{n} \sum\_{j}^{m} [Fact(gcd(i,j))<=P] \\]
## 解决思路
首先考虑$$Fact(x)$$这个函数，我们发现它是可以线性筛出来的，具体来说，根据其性质可以这样递推出来
设$$p$$为质数
\\[\begin{cases} Fact(p)=1 \\\\ Fact(p^k)=k \\\\ Fact(p\*i)=Fact(i)+1 \end{cases}\\]
由于对于每一个$$x=gcd(i,j)$$，其$$Fact(x)$$是一定的，所以我们先不考虑这个质因子个数的限制。
设\\[f(x)=\sum\_{i}^{n} \sum\_{j}^{m} [gcd(i,j)==x]\\] \\[g(x)=\sum\_{x|d} f(d)\\]
根据我们之前做过的题目（[比如这里](http://sycstudio.com/archives/119)），可以知道$$g(x)=\lfloor \frac{n}{x} \rfloor \* \lfloor \frac{m}{x} \rfloor$$，根据莫比乌斯反演，可以得到$$f(x)=\sum\_{x|d} g(d)$$。
假定我们最后要求的是$$\sum\_{i} f(i)$$（也就是不考虑$$P$$的限制时的方案数），那么我们现在得到了一种$$O(n\*\sqrt{n})$$的办法，就是枚举$$i$$算出每一个$$f(i)$$求和，而算$$f(i)$$的过程可以通过数论分块降到$$O(\sqrt{n})$$的复杂度。
很明显，对于题目的数据范围，这样的复杂度是不行的。考虑到是求和操作，我们来看一看每一个$$g(x)$$最后给答案贡献了多少次。
\\[f(1)=\mu(\frac{1}{1})\*g(1)+\mu(\frac{2}{1})\*g(2)+\mu(\frac{3}{1})\*g(3)...... \\\\ f(2)=\mu(\frac{2}{2})\*g(2)+\mu(\frac{4}{2})\*g(4)+\mu(\frac{6}{2})\*g(6)...... \\\\ f(3)=\mu(\frac{3}{3})\*g(3)+\mu(\frac{6}{3})\*g(6)+\mu(\frac{9}{3})\*g(9)...... \\\\ ...... \\\\ f(x)=\mu(\frac{1\*x}{x})\*g(1\*x)+\mu(\frac{2\*x}{x})\*g(2\*x)+\mu(\frac{3\*x}{x})\*g(3\*x)......\\\\ =\mu(1)\*g(1\*x)+\mu(2)\*g(2\*x)+\mu(3)\*g(3\*x).....\\]
可以发现，每一个数$$x$$给它的倍数$$d=kx$$的$$g(d)$$贡献了$$\mu(j)$$的莫比乌斯系数，所以我们可以考虑线性推出这个系数的和。设$$F[d]$$表示$$g(d)$$前面莫比乌斯系数的和，那么枚举$$x$$的倍数$$d=kx$$，$$F[d]+=\mu (\frac{d}{x})$$。
最后我们把对质因子个数的条件放进去。先考虑要求质因子个数一定等于$$P$$的。这时候也就是说对于一个数$$x$$，它只能在给定的$$P$$与它的质因子个数相同的时候才能贡献，所以考虑把$$F[d]$$加一维变成$$F\[d\]\[p\]$$，表示$$x$$分解质因子个数为$$p$$时对$$x$$的倍数$$d=kx$$的$$g(d)$$的贡献，那么就是$$F\[d\]\[Fact(x)]+=\mu(\frac{d}{x})$$。
最后把条件再放宽，即质因子个数小于等于$$P$$的，那么求$$[p]$$这一维的前缀和即可。由于我们需要数论分块将复杂度，需要求$$[d]$$这一维的前缀和，所以我们求二维前缀和即可。
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

const int maxNum=500010;
const int maxP=20;
const int inf=2147483647;

int n,m,P,Q;
ll Mu[maxNum];
ll Fact[maxNum];//每一个数分解后有多少个质因子
ll pricnt=0,Prime[maxNum];
bool notprime[maxNum];
ll F[maxNum][maxP];

void GetMu();

int main()
{
    GetMu();
    for (int i=1;i<maxNum;i++)//求F[d][p]
	    for (int j=i;j<maxNum;j+=i)
	        F[j][Fact[i]]+=Mu[j/i];
    for (int i=0;i<maxNum;i++)//求前缀和，注意因为要累加0的前缀和，所以把行和列分开考虑
	    for (int j=1;j<maxP;j++)
	        F[i][j]+=F[i][j-1];
    for (int i=1;i<maxNum;i++)
 	    for (int j=0;j<maxP;j++)
	    	F[i][j]+=F[i-1][j];
    scanf("%d",&Q);
    while (Q--)
    {
		scanf("%d%d%d",&n,&m,&P);
		if (P>=maxP)
		{
	    	printf("%lld\n",(ll)n*(ll)m);
	    	continue;
		}
		ll ans=0;
		if (m<n) swap(n,m);
		for (int i=1,last;i<=n;i=last+1)//数论分块求解
		{
	    	last=min(n/(int)(n/i),m/(int)(m/i));
	    	ans=ans+(F[last][P]-F[i-1][P])*(ll)(n/i)*(ll)(m/i);
		}
		printf("%lld\n",ans);
    }
    return 0;
}

void GetMu()//线性筛
{
    notprime[1]=1;Mu[1]=1;Fact[1]=0;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Prime[++pricnt]=i,Mu[i]=-1,Fact[i]=1;
		for (int j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j]<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			Fact[i*Prime[j]]=Fact[i]+1;
			if (i%Prime[j]==0) break;
			Mu[i*Prime[j]]=-Mu[i];
		}
    }
    return;
}
```
