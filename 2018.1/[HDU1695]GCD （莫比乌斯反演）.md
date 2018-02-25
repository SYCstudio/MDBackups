###  Description
Given 5 integers: a, b, c, d, k, you're to find x in a...b, y in c...d that GCD(x, y) = k. GCD(x, y) means the greatest common divisor of x and y. Since the number of choices may be very large, you're only required to output the total number of different number pairs.
Please notice that, (x=5, y=7) and (x=7, y=5) are considered to be the same.

Yoiu can assume that a = c = 1 in all test cases.
### Http
[HDU](https://vjudge.net/problem/HDU-1695)
### Tag
莫比乌斯反演
## 题目大意
给定\\(a,b,K\\)求\\(\sum\_i^a \sum\_j^b [gcd(i,j)==K]\\)
## 解决思路
设\\[F(x)=\sum\_i^a \sum\_j^b [gcd(i,j)==K]\\] \\[G(x)=\sum\_{x|d} F(d)\\]
\\[G(x)=\sum\_{x|d} \sum\_i^a \sum\_j^b [gcd(i,j)==d]=\lfloor \frac{a}{x} \rfloor \* \lfloor \frac{b}{x} \rfloor \\]
那么根据莫比乌斯反演，可以得到
\\[F(x)=\sum\_{x|d} \mu (\frac{d}{x}) \* \lfloor \frac{a}{d} \rfloor \* \lfloor \frac{b}{d} \rfloor \\]
但是同时要注意到，题目中表明了$$(a,b)$$与$$(b,a)$$是同一种方案，所以接下来考虑如何去重。
我们假定$$a\leq b$$，记包括了重复的答案为$$ans$$。我们知道$$[1,a]$$的方案是被重复算了的，所以我们再算一遍到$$a$$的代价，记为$$sum$$。需要注意的是，这里的$$sum$$中计算的也是计算了两次的，所以最终的答案应该是$$sum-\frac{ans}{2}$$
最后要注意的是，$$K$$可以取到$$0$$，所以要特判
## 代码
```cpp
#include<iostream>
#include<cstdio>
#include<cstdlib>
#include<cstring>
#include<algorithm>
using namespace std;

#define ll long long

const int maxNum=100010;
const int inf=2147483647;

int a,b,c,d,k;
ll Mu[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];

void GetMu();//线性筛求Mu

int main()
{
    GetMu();
    int T;scanf("%d",&T);
    for (int ti=1;ti<=T;ti++)
    {
		scanf("%d%d%d%d%d",&a,&b,&c,&d,&k);
		if (k==0)
		{
			printf("Case %d: 0\n",ti);
			continue;
		}
		if (d<b) swap(b,d);
		ll ans=0,sum=0;
		for (ll i=k;i<=b;i+=k)//先计算出包括重复的
			if (i%k==0) ans=ans+Mu[i/k]*(ll)(b/i)*(ll)(d/i);
		for (ll i=k;i<=b;i+=k)//求出重复的
			if (i%k==0) sum=sum+Mu[i/k]*(ll)(b/i)*(ll)(b/i);
		printf("Case %d: %lld\n",ti,ans-sum/2);//算出最终答案
    }
    return 0;
}

void GetMu()
{
    Mu[1]=1;notprime[1]=1;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Mu[i]=-1,Prime[++pricnt]=i;
		for (int j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j]<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0) break;
			Mu[i*Prime[j]]=-Mu[i];
		}
    }
    return;
}
```
