###  Description
Have you ever played Puzzle Bobble, a very famous PC game? In this game, as a very cute bobble dragon, you must keep shooting powerful bubbles to crush all the colorful bubbles upwards. Victory comes when all the bubbles upwards are crushed.

Little Tom is crazy about this game. One day, he finds that all kinds of Puzzle Bobble are 2D Games. To be more excited when playing this game, he comes up with a new idea to design a 3D Puzzle Bobble game! In this game, the bobble dragon is standing in a cubic room with L in length, W in width and H in height. Around him are so many colorful bubbles. We can use 3D Cartesian coordinates (x, y, z) to represent the locations of the bobble dragon and those bubbles. All these coordinates (x, y, z) are triple positive integers ranged from (1, 1, 1) to (L, W, H).

To simplify the problem, let's assume the bobble dragon is standing at (1, 1, 1) in the room. And there is one colorful bubble at every (x, y, z) in the room except (1, 1, 1). The dragon is so strong that he can shoot out a magical bubble to crush all the colorful bubbles in the straight line which the magical bubble flies every single time. Note that bubbles are significantly small with respect to the distances between each two bubbles. Our question remains, how many magical bubbles will the cute dragon shoot before crushing all the colorful bubbles around him?
![](https://odzkskevi.qnssl.com/6efa53acbde993696100f15dc519b0e5?v=1515590581)
### Http
[zoj](https://vjudge.net/problem/22239/origin)
### Tag
莫比乌斯反演，数论分块
## 题目大意
在空间坐标系的第一卦限给定$$L\*H\*W$$的范围，问从$$(1,1,1)$$这个点看去，能看到多少个不被遮住的整数点？
## 解决思路
与[这一题](http://sycstudio.com/archives/119)有些类似，都是求$$\sum\_{i}^{L} \sum\_{j}^{H} \sum\_{k}^{W} [gcd(i,j,k)==1] $$，但本题不同的地方是，这里是从$$(1,1,1)$$看的，所以可以考虑把整个立体空间平移到$$(0,0,0)$$，即把$$L,H,W$$都减一。
那么我们把整个立体空间分为三个部分考虑。
第一部分是三根正轴，我们定义为$$L$$轴,$$H$$轴和$$W$$轴，很容易知道这三根数轴上面的贡献分别是$$1$$。
第二部分是三个平面:$$LoH,HoW,LoR$$，三个平面分别的贡献是$$\sum\_{i} \sum\_{j} [gcd(i,j)==1]$$。
第三部分就是除去三个平面和三根数轴剩下的$$L\*H\*W$$的空间，根据上面的式子，我们知道是求$$\sum\_{i}^{L} \sum\_{j}^{H} \sum\_{k}^{W} [gcd(i,j,k)==1] $$
然后我们考虑如何将上面的式子化简，先来看三维的情况
设\\[F(x)=\sum\_{i}^{L} \sum\_{j}^{H} \sum\_{k}^{W} [gcd(i,j,k)==x] \\] \\[G(x)=\sum\_{x|d} F(d)=\sum\_{x|d} \sum\_{i}^{L} \sum\_{j}^{H} \sum\_{k}^{W} [gcd(i,j,k)==d] \\]可以发现，与上面那一题类似的，\\[G(x)=\lfloor \frac{L}{x} \rfloor \* \lfloor \frac{H}{x} \rfloor \* \lfloor \frac{W}{x} \rfloor \\]
好，那么根据莫比乌斯反演，可以得到\\[F(x)=\sum\_{x|d} \mu (\frac{d}{x}) G(d)\\] 最终得到\\[F(1)=\sum\_{i=1}^n \mu (i) \lfloor \frac{L}{i} \rfloor \* \lfloor \frac{H}{i} \rfloor \* \lfloor \frac{W}{i} \rfloor\\]
那么相应的，二维的情况就是\\[\sum\_{i=1}^n \mu (i) \lfloor \frac{L}{i} \rfloor \* \lfloor \frac{H}{i} \rfloor \\]
虽然说我们把$$O(n^3)$$的复杂度降到了$$O(n)$$，但是这还不够，因为题目有多组数据。怎么办呢？我们来看到这组例子。
\\[\lfloor \frac{100}{20} \rfloor =5 \\\\ \lfloor \frac{100}{21} \rfloor =\lfloor \frac{100}{22} \rfloor =\lfloor \frac{100}{23} \rfloor =\lfloor \frac{100}{24} \rfloor =\lfloor \frac{100}{25} \rfloor =4 \lfloor \frac{100}{26} \rfloor =3 \\\\ ……\\]
我们发现，对于$$n==100$$的时候，$$i=21,22,23,24,25$$时的代价是一样的，并且这个相同的区间在$$i$$变大的时候，区间也会越来越大。
这就是**数论分块**的原理：考虑把一样的东西一起做。$$\lfloor \frac{n}{\frac{n}{i}} \rfloor$$这样就可以找到与$$i$$在同一区间（同一个块）的最后一个位置，利用提前计算好的$$\mu(i)$$的前缀和，就可以一个区间一个区间地算了
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

const int maxNum=1000010;
const int inf=2147483647;

int n;
ll Mu[maxNum],SumMu[maxNum];
int pricnt=0,Prime[maxNum];
bool notprime[maxNum];
int L,W,H;

void GetMu();

int main()
{
    GetMu();
    while (scanf("%d%d%d",&L,&W,&H)!=EOF)
    {
		L--;W--;H--;
		ll ans=3;
		int limit=min(L,min(W,H));
		for (int i=1,last=1;i<=limit;i=last+1)
		{
			ll k1=L/i,k2=W/i,k3=H/i;
			last=min(L/k1,min(W/k2,H/k3));
			ans=ans+(SumMu[last]-SumMu[i-1])*k1*k2*k3;
		}
		limit=min(L,W);
		for (int i=1,last=1;i<=limit;i=last+1)
		{
			ll k1=L/i,k2=W/i;
			last=min(L/k1,W/k2);
			ans=ans+(SumMu[last]-SumMu[i-1])*k1*k2;
		}
		limit=min(L,H);
		for (int i=1,last=1;i<=limit;i=last+1)
		{
			ll k1=L/i,k2=H/i;
			last=min(L/k1,H/k2);
			ans=ans+(SumMu[last]-SumMu[i-1])*k1*k2;
		}
		limit=min(W,H);
		for (int i=1,last=1;i<=limit;i=last+1)
		{
			ll k1=W/i,k2=H/i;
			last=min(W/k1,H/k2);
			ans=ans+(SumMu[last]-SumMu[i-1])*k1*k2;
		}
		printf("%lld\n",ans);
    }
    return 0;
}

void GetMu()
{
    notprime[1]=1;Mu[1]=1;
    for (int i=2;i<maxNum;i++)
    {
		if (notprime[i]==0) Prime[++pricnt]=i,Mu[i]=-1;
		for (int j=1;(j<=pricnt)&&((ll)i*(ll)Prime[j]<maxNum);j++)
		{
			notprime[i*Prime[j]]=1;
			if (i%Prime[j]!=0) Mu[i*Prime[j]]=-Mu[i];
			else break;
		}
    }
    for (int i=1;i<maxNum;i++) SumMu[i]=SumMu[i-1]+Mu[i];
    return;
}
```
