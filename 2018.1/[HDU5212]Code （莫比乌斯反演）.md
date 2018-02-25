###  Description
WLD likes playing with codes.One day he is writing a function.Howerver,his computer breaks down because the function is too powerful.He is very sad.Can you help him?

The function:

```cpp
int calc
{
  int res=0;
  for(int i=1;i<=n;i++)
    for(int j=1;j<=n;j++)
    {
      res+=gcd(a[i],a[j])*(gcd(a[i],a[j])-1);
      res%=10007;
    }
  return res;
} 
```
### Http
[HDU](https://vjudge.net/problem/HDU-5212)
### Tag
莫比乌斯反演
## 题目大意
求\\[\sum\_{i}^{n} \sum\_{j}^{n} gcd(A[i],A[j])\*(gcd(A[i],A[j])-1)\\]
## 解决思路
换一个思路，考虑到值域直到\\(10000\\)，所以可以开桶统计每一个数出现了多少次，记\\(Cnt[i]\\)表示数\\(i\\)出现了多少次。在值域里枚举，那么答案就是\\[Ans=\sum\_{i}^{10000} \sum\_{j}^{10000} Cnt[i]\*Cnt[j]\*gcd(i,j)\*(gcd(i,j)-1)\\]
令\\(f(x)=x\*(x-1)\\)，构造$$g(x)$$，使$$f(x)=\sum\_{d|x} g(d)$$，则根据莫比乌斯反演，可以得到$$g(x)=\sum\_{d|x}\mu(\frac{x}{d}) f(d)$$。
这样有什么用呢？根据我们得到的$$g(x)$$，我们可以把对答案的求解式化成这个形式
\\[Ans=\sum\_{i}^{10000} \sum\_{j}^{10000} Cnt[i]\*Cnt[j]\*\sum\_{d|i,d|j} g(d)\\]
为什么可以这样化呢？因为$$d|i,d|j$$保证了$$d$$一定是$$i,j$$的因子，而$$\sum\_{d|x} g(d)=f(x)$$，所以就可以直接替换原来的$$gcd$$
那么接着我们把原来枚举$$i,j$$换成枚举$$d$$，即把后面的$$\sum$$换到前面来。考虑每一对$$i,j$$对$$d$$的贡献，那么式子可以这样列
\\[Ans=\sum\_{d}^{10000} \sum\_{d|i}^{10000} \sum\_{d|j}^{10000} Cnt[i]\*Cnt[j] \*g(d)\\]
重新观察一下这个式子，既然我们把$$d$$已经提到外面了，那么里面枚举的$$i$$和$$j$$其实是一样的，那么我们可以把两次枚举合二为一
\\[Ans=\sum\_{d}^{10000} g(d) \* {(\sum\_{d|i} Cnt[i])}^2\\]
这样，计算的复杂度就降到$$O(n\sqrt{n})$$
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

const int maxNum=10010;
const int Mod=10007;
const int inf=2147483647;

int Mu[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];
int n,Cnt[maxNum];
ll G[maxNum];

void GetMu();//线性筛莫比乌斯函数

int main()
{
    GetMu();
    for (int i=1;i<maxNum;i++)//得到我们构造的函数G
    {
		int j;
		for (j=1;j*j<i;j++)
			if (i%j==0)
				G[i]=((G[i]+Mu[i/j]*j%Mod*(j-1)%Mod+Mu[i/(i/j)]*(i/j)%Mod*(i/j-1)%Mod)%Mod+Mod)%Mod;
		if (j*j==i) G[i]=((G[i]+Mu[i/j]*j%Mod*(j-1)%Mod)%Mod+Mod)%Mod;
    }
    while (scanf("%d",&n)!=EOF)
    {
		mem(Cnt,0);//桶
		for (int i=1;i<=n;i++)
		{
			int x;scanf("%d",&x);
			Cnt[x]++;//桶计数
		}
		ll ans=0;
		for (int i=1;i<maxNum;i++)//这里就是按照那个式子求解了
		{
			ll sum=0;
			for (int j=i;j<maxNum;j=j+i) sum=(sum+Cnt[j])%Mod;
			sum=sum*sum%Mod;
			ans=((ans+sum*G[i]%Mod)%Mod+Mod)%Mod;
		}
		printf("%lld\n",ans);
    }
    return 0;
}

void GetMu()//线性筛
{
    notprime[1]=1;Mu[1]=1;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Prime[++pricnt]=i,Mu[i]=-1;
		for (int j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j])<maxNum;j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]==0) break;
			Mu[i*Prime[j]]=-Mu[i];
		}
    }
    return;
}

```
