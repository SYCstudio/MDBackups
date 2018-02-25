###  Description
Consider a N\*N\*N lattice. One corner is at (0,0,0) and the opposite one is at (N,N,N). How many lattice points are visible from corner at (0,0,0) ? A point X is visible from point Y iff no other lattice point lies on the segment joining X and Y. 
### Http
[http://www.spoj.com/problems/VLATTICE/en/](http://www.spoj.com/problems/VLATTICE/en/ "http://www.spoj.com/problems/VLATTICE/en/")
### Tag
莫比乌斯反演
## 题目大意
求从三维坐标系的原点\\((0,0,0)\\)朝第一卦限看去，在\\(n\*n\*n\\)的范围内能看到多少个不被遮蔽的整点
## 解决思路
把题目化成数学形式就是求\\[\sum\_{i=1}^{n} \sum\_{j=1}^{n} \sum\_{k=1}^{n} [gcd(i,j,k)==1]\\]
考虑莫比乌斯反演。
设\\[F(x)=\sum\_{i=1}^{n} \sum\_{j=1}^{n} \sum\_{k=1}^{n} [gcd(i,j,k)==x]\\] \\[G(x)=\sum\_{x|d} F(d)=\sum\_{x|d} \sum\_{i=1}^{n} \sum\_{j=1}^{n} \sum\_{k=1}^{n} [gcd(i,j,k)==d]\\]
看到第二个式子，由于$$gcd(i,j,k)==d$$，所以有$$d|i,d|j,d|k$$，又因为$$x|d$$，所以计算贡献可以发现$$G(x)={\lfloor \frac{n}{x} \rfloor}^3$$
那么根据莫比乌斯反演，可以得到\\[F(x)=\sum\_{x|d} \mu (\frac{d}{x})\*G(d)\\]
把$$x==1$$带进去，得到最终的式子
\\[F(x)=\sum\_{i=1}^n \mu (i) {\lfloor \frac{n}{i} \rfloor}^3\\]
需要注意的是，这是三维的情况，即$$gcd(i,j,k)==1$$，这要求$$i,j,k!=0$$，即没有计算在三个平面的情况。而由我们上面的分析可以知道，三个平面上的答案就是$$\sum\_{i=1}^n \mu (i) \*3\*{\lfloor \frac{n}{i} \rfloor}^2$$要乘三的原因是有三个平面。
当然，相信你也猜到了，我们计算这三个平面的时候，是不包括三根数轴的，所以最后还要答案要加上这三根数轴的贡献，即$$(1,0,0),(0,1,0),(0,0,1)$$，即加上三
## 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
using namespace std;

#define ll long long

const int maxNum=1000010;
const int inf=2147483647;

int Mu[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];

void GetMu();

int main()
{
    GetMu();
    int T;scanf("%d",&T);
    while (T--)
    {
		int n;scanf("%d",&n);
		ll ans=0;
		for (int i=1;i<=n;i++)
		{
			ll x=n/i;
			ans=ans+(ll)Mu[i]*(ll)x*(ll)x*(ll)(x+3);
		}
		printf("%lld\n",ans+3ll);
    }
    return 0;
}

void GetMu()//线性筛求Mu
{
    Mu[1]=1;notprime[1]=1;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Prime[++pricnt]=i,Mu[i]=-1;
		for (int j=1;(j<=pricnt)&&((ll)Prime[j]*(ll)i<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0) break;
			Mu[i*Prime[j]]=-Mu[i];
		}
    }
    return;
}
```
