###  Description
The Farey Sequence Fn for any integer n with n >= 2 is the set of irreducible rational numbers a/b with 0 < a < b <= n and gcd(a,b) = 1 arranged in increasing order. The first few are
F2 = {1/2}
F3 = {1/3, 1/2, 2/3}
F4 = {1/4, 1/3, 1/2, 2/3, 3/4}
F5 = {1/5, 1/4, 1/3, 2/5, 1/2, 3/5, 2/3, 3/4, 4/5}

You task is to calculate the number of terms in the Farey sequence Fn. 
### Http
[POJ](https://vjudge.net/problem/POJ-2478)
### Tag
欧拉函数
## 题目大意
给定正整数\\(N\\)，求小于等于\\(N\\)的互质的数的个数
## 解决思路
求小于等于\\(N\\)的互质的数的个数，就是求\\(\sum \phi(i)\\)，线性筛出$$\phi(i)$$即可，但要注意虽然$$\phi(1)==1$$，但这里不能统计进去
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

ll phi[maxNum];
ll sum[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];

void Phi();

int main()
{
    Phi();
    sum[1]=0;
    for (int i=2;i<maxNum;i++) sum[i]=sum[i-1]+phi[i];//求前缀和
    int n;
    while (scanf("%d",&n)!=EOF)
    {
		if (n==0) break;
		printf("%lld\n",sum[n]);
    }
    return 0;
}

void Phi()
{
    notprime[1]=1;phi[1]=1;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Prime[++pricnt]=i,phi[i]=i-1;
		for (int j=1;(j<=pricnt)&&((ll)i*Prime[j])<maxNum;j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0)
			{
				phi[i*Prime[j]]=phi[i]*Prime[j];
				break;
			}
			phi[i*Prime[j]]=phi[i]*phi[Prime[j]];
		}
    }
    return;
}
```
