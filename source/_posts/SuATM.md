---
title: 浅谈序列自动机
tags: 字符串算法
mathjax: true
---
# 浅谈序列自动机
<!---more--->
## 简介
序列自动机是一个可以快速判断字符串$t$是否是字符串$s$的子串的一个算法。

## 构造
对$s$构造序列自动机，使用$Nxt_{i,j}$代表从第$i$个位置开始，字符$j$出现的第一个位置。我们倒着遍历更新即可。
```
	int nxt[N][27];
	void init(char *s){
		int l=strlen(s);
		for(int i=0;i<26;i++) nxt[l][i]=INF;
		for(int i=l-1;i>=0;i--){
			for(int j=0;j<26;j++){
				nxt[i][j]=nxt[i+1][j];
			}
			nxt[i][s[i]-'a']=i;
		}
	}
```

## 查询
设置初始指针$p$为-1，每次让$p$跳到$Nxt_{p+1,j}$上面，$j$为当前查询的字符，如果$p$为$INF$,则说明找不到下一个字符，即$t$不是$s$的子串。
```cpp
	bool find(char *t){
		int pos=-1;
		int l=strlen(t);
		for(int i=0;i<l;i++){
			pos=nxt[pos+1][t[i]-'a'];
			if(pos==INF) return 0;
		}
		return 1;
	}
```

## 模板
```cpp
struct sub_AM{
	int nxt[N][27];
	void init(char *s){
		int l=strlen(s);
		for(int i=0;i<26;i++) nxt[l][i]=INF;
		for(int i=l-1;i>=0;i--){
			for(int j=0;j<26;j++){
				nxt[i][j]=nxt[i+1][j];
			}
			nxt[i][s[i]-'a']=i;
		}
	}
	bool find(char *t){
		int pos=-1;
		int l=strlen(t);
		for(int i=0;i<l;i++){
			pos=nxt[pos+1][t[i]-'a'];
			if(pos==INF) return 0;
		}
		return 1;
	}
}solve;
```

## Problems

[计蒜客 Subsquenece](https://nanti.jisuanke.com/t/38232)

```cpp
#include<bits/stdc++.h>

using namespace std;
const int N = 1e5+100;
const int INF = 0x3f3f3f3f;
char s[N];
char t[N];
struct sub_AM{
	int nxt[N][27];
	void init(char *s){
		int l=strlen(s);
		for(int i=0;i<26;i++) nxt[l][i]=INF;
		for(int i=l-1;i>=0;i--){
			for(int j=0;j<26;j++){
				nxt[i][j]=nxt[i+1][j];
			}
			nxt[i][s[i]-'a']=i;
		}
	}
	bool find(char *t){
		int pos=-1;
		int l=strlen(t);
		for(int i=0;i<l;i++){
			pos=nxt[pos+1][t[i]-'a'];
			if(pos==INF) return 0;
		}
		return 1;
	}
}solve;
int main(){
	scanf("%s",s);
	solve.init(s);
	int q;
	scanf("%d",&q);
	while(q--){
		scanf("%s",t);
		if(solve.find(t)){
			puts("YES");
		}
		else puts("NO");
	}		
	return 0;
}
```