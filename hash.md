# 第四部分 字符型数据相关算法
## 第一章 哈希
&#160; &#160; &#160; &#160; Hash，直接音译为“哈希”，还可以翻译为“散列”。通过哈希函数，可以将一个任意长度的信息M映射成为一个长度较短且长度固定的值V=H(M)，V可以称作哈希值或散列值，H就称作哈希函数或散列函数

&#160; &#160; &#160; &#160; 哈希建立了真实值和哈希值的映射关系，因此经常用在字符串匹配等问题上。通过哈希函数，可以把一个字符串转化为一个大小适当的整数，方便之后的比较、查找等操作。在接下来的讲解中，主要围绕字符串哈希展开，当然，哈希的应用范围非常广泛，哈希的对象也不仅仅拘泥于字符串，一张图，一棵树都可以哈希，这些在平时训练及考试中也会遇到，但大致思想是一致的，都是设计合适的哈希函数，建立映射关系。

##### 1.1 哈希的基本原理
&#160; &#160; &#160; &#160; 字符串哈希的基本原理是使用一定范围内的整数来表示字符串，设计哈希函数H，对于字符串S，有整数V=H(S)，那么V就是S的哈希值。如果两个字符串的哈希值V一样，那么就可以认为这两个字符串是一样的。比较的时间复杂度由之前的O(n)下降到O(1)，当然，这是假设哈希值已知的情况。

&#160; &#160; &#160; &#160; 但是，通过这种算法并不能保证所有字符串和整数之间是一一对应的关系。根据鸽巢原理，若字符串集中的字符串个数多于整数集中的整数个数，则必然会有多个字符串对应于一个整数；并且，由于哈希函数的设计，不同字符串也可能计算出同一值。这也就是“哈希冲突”或者叫“哈希碰撞”。这种“冲突”是难以避免的，但在OI考试及训练的大多情况中，只要能够设计出较为合理的哈希函数，可以大大降低这种情况出现的概率,足以应付绝大多数的测试数据。

##### 1.2 常用的哈希函数
&#160; &#160; &#160; &#160; 选择适当的哈希函数是字符串哈希的核心，构造哈希函数主要考虑以下两个因素：速度快和分布均匀。速度快是指计算哈希函数的时间复杂度要低，这个要求是显而易见的，如果复杂度太大，哈希就没有意义，还不如直接用朴素算法解决；分布均匀是指对于字符串集合中的任意一个字符串，哈希函数能够以近似相等的概率将其映射到整数集的任何一个整数上，其意义是尽量减少“哈希冲突”。接下来将介绍一种常用高效的哈希算法：Rabin_Karp算法。

&#160; &#160; &#160; &#160; 选取两个大小合适的互素常数BASE和MOD，假设字符串S=s<sub>1</sub>s<sub>2</sub>…s<sub>n</sub>,定义哈希函数：
```math
H(S)=(s_1BASE^{n-1}+ s_2BASE^{n-2}+ s_3BASE^{n-3}+…+s_nBASE^0) \ mod \ MOD
```
其中BASE是基数，相当于把字符串看作BASE进制数,这一部分的时间复杂度为O(n)。

&#160; &#160; &#160; &#160;
在使用这种哈希算法时，可以把字符串S的所有前缀的哈希值都记录在hash数组中， 如：hash<sub>1</sub>=H(s<sub>1</sub>)，hash<sub>2</sub>=H(s<sub>1</sub>s<sub>2</sub>)，hash<sub>m</sub>=H(s<sub>1</sub>s<sub>2</sub>…s<sub>m</sub>)。这样做的好处是可以O(1)地算出S的任意一个子串的哈希值。若S<sub>lr</sub>=s<sub>l</sub>s<sub>l+1</sub>…s<sub>r</sub>,则其哈希值可以直接由如下公式得到：
```math
H(S_{lr})=hash_r-(hash_{l-1}*BASE^{r-l+1}) \ mod \ MOD
```
&#160; &#160; &#160; &#160; 综上，用Rabin_Karp算法哈希一个字符串的时间复杂度为O(n)，获得子串哈希值的时间复杂度为O(1)。BASE值可以取类似127、233这样小一点的素数，MOD可以根据具体题目取尽量大一点的素数，如1235789、9875321或者把哈希值设成unsigned long long类型来自然溢出。

##### 1.3 例题分析
#####  1.3.1 字符串匹配问题
###### 题目描述
&#160; &#160; &#160; &#160; 有两个字符串T和P，字符串T比字符串P长，求出P在T中第一个出现的位置下标，若不存在，输出“No”。

###### 输入格式
&#160; &#160; &#160; &#160;两行，第一行字符串T，第二行字符串P，都是小写字母。

###### 输出格式
&#160; &#160; &#160; &#160;一个整数，匹配起始位置；或者“No”，表示T中没有P。

###### 输入样例
&#160; &#160; &#160; &#160; abcdefghijklmn

&#160; &#160; &#160; &#160; cdef

###### 输出样例
&#160; &#160; &#160; &#160; 3

###### 题解
&#160; &#160; &#160; &#160;裸的字符串匹配问题，按照1.2中讲解写出即可。

###### 代码
```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long LL;
const LL mod = 912837417;
const LL Base = 127;
char T[5000], P[5000];
LL lenT, lenP;
LL hash[5000];
LL base[5000];
LL hashP;
inline LL query(LL l, LL r) {
	LL ans = (hash[r] - (hash[l - 1] * base[r - l + 1])) % mod;
	if (ans < 0) ans += mod;
	return ans;
}
int main() {
	scanf("%s%s", T + 1, P + 1);
	lenT = strlen(T + 1); lenP = strlen(P + 1);
	base[0] = 1;
	for (int i = 1; i <= lenT; i++) {
		base[i] = (base[i - 1] * Base) % mod;
		hash[i] = (hash[i - 1] * Base + (LL)(T[i] - 'a' + 1)) % mod;
		if (i <= lenP) hashP = (hashP*Base + (LL)(P[i] - 'a' + 1)) % mod;
	}
	for (int i = 1; i <= lenT - lenP + 1; i++) {
		LL now = query(i, i + lenP - 1);
		if (now == hashP) {
			cout << i << endl;
			return 0;
		}
	}
	cout << "No" << endl;
	return 0;
}

```
##### 1.3.2 找最长公共子串
###### 题目描述
&#160; &#160; &#160; &#160; 现在给两个仅包含小写字母的字符串a和b，求a与b的最长公共连续子串的长度。

###### 输入格式
&#160; &#160; &#160; &#160;两行，每行一个字符串。

###### 输出格式
&#160; &#160; &#160; &#160;一个整数，为输入的两个字符串的最长公共连续子串的长度。

###### 输入样例
&#160; &#160; &#160; &#160; qaqaaaq

&#160; &#160; &#160; &#160; qqaqa

###### 输出样例
&#160; &#160; &#160; &#160; 4

###### 样例解释
&#160; &#160; &#160; &#160; 最长连续公共子串为qaqa，长度为4

###### 数据范围
&#160; &#160; &#160; &#160; 20 组测试数据

&#160; &#160; &#160; &#160; 对于20%的数据，a,b长度∈[1, 200]

&#160; &#160; &#160; &#160; 对于50%的数据，a,b长度∈[1, 20000]

&#160; &#160; &#160; &#160; 对于100%的数据, a,b长度∈[1, 200000]

###### 题解
&#160; &#160; &#160; &#160; 按照1.2中的公式哈希两个字符串，时间复杂度为O(n)；二分答案len，对当前的len，求出两个字符串中所有长度为len的子串的哈希值，时间复杂度O(nlogn)；将第二个字符串中所有长度为len的子串的哈希值从小到大排序，枚举第一个字符串中所有长度为len的子串的哈希值，在刚排好序的第二个字符串中二分查找（或者用lower_bound）。若能找到，则这个len是可以达到的，最终答案大于等于此len，反之最终答案一定小于此len，时间复杂度为O(nlogn)。总的时间复杂度为O(nlogn)，可以通过100%的数据。

###### 代码
```C++
#include<cstdio>
#include<cstring>
#include<string>
#include<algorithm>
using namespace std;
typedef unsigned long long ULL;
const int MAXN = 200005;
const ULL BASE = 233;

ULL base[MAXN];
ULL hashA[MAXN];
ULL hashB[MAXN];
ULL ha[MAXN];
ULL hb[MAXN];

char A[MAXN], B[MAXN];
int lenA, lenB;
int maxlen, minlen;
bool check(int len) {//检查len是否可行
	for (int i = 1; i <= lenA - len + 1; i++)
		ha[i] = hashA[i + len - 1] - hashA[i - 1] * base[len];
	for (int i = 1; i <= lenB - len + 1; i++)
		hb[i] = hashB[i + len - 1] - hashB[i - 1] * base[len];
	sort(hb + 1, hb + lenB - len + 2);
	for (int i = 1; i <= lenA - len + 1; i++) {
		int pos = lower_bound(hb + 1, hb + lenB - len + 2, ha[i]) - hb;
		if (pos <= lenB - len + 1 && ha[i] == hb[pos]) return true;
	}
	return false;
}

int main() {
	scanf("%s %s", A + 1, B + 1);
	lenA = strlen(A + 1); lenB = strlen(B + 1);
	maxlen = max(lenA, lenB); minlen = min(lenA, lenB);
	//根据1.2计算A，B串的哈希值
	base[0] = 1;
	for (int i = 1; i <= maxlen; i++) {
		base[i] = base[i - 1] * BASE;
		if (i <= lenA) hashA[i] = hashA[i - 1] * BASE + (ULL)(A[i] - 'a' + 1);
		if (i <= lenB) hashB[i] = hashB[i - 1] * BASE + (ULL)(B[i] - 'a' + 1);
	}
	//二分答案
	int ans = 0;
	int l = 0, r = minlen;
	while (l <= r){
		int mid = (l + r) >> 1;
		if (check(mid) == true) {
			ans = mid;
			l = mid + 1;
		}
		else
			r = mid - 1;
	}

	printf("%d\n", ans);
	return 0;
}

```
