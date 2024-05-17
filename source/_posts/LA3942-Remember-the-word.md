---
title: LA3942 Remember the word
date: 2015-12-05 
categories: Learning
tags: [Algorithm, Trie]
---
给出一个由 S 个不同单词组成的字典和一个长字符串 Str。把这个字符串分解成若干个单词的连接（单词可以重复使用），有多少种方法？比如，有 4 个单词 a, b, cd, ab，则 abcd 有两种分解方法：a + b + cd 和 ab + cd。

输入包含多组数据，每组数据第一行为待分解的字符串，其长度 L 不超过 300, 000。第二行为单词个数 S，$1\leq S\leq4000$。 以下 S 行每行为一个单词，由不超过 100 个小写字母组成。对每组数据输出方案数模 20, 071, 027。

<!--more-->
递推：令 d(i) 表示字符串 Str[i…(L-1)] 的分解方案数，d(i)=sum{d(i+len(x) | 单词 x 是 Str[i…(L-1)] 的前缀}，若 Str[i…(L-1)] 在字典中，则 d(i) 应加 1。

用 Trie 来保存字符串集合，单词的长度最大为 100，求解 d(i) 时最多只需要判断 100 次。

## Code
{% codeblock lang:C %}
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
const int maxnode=4000*100+10,maxsize=26;
int ch[maxnode][maxsize],val[maxnode],sz;
int idx(char c) { return c-'a'; 	}	
void insert(char *s){
	int u=0,n=strlen(s);
	for(int i=0;i<n;i++){
		int c=idx(s[i]);
		if(!ch[u][c]){
			memset(ch[sz],0,sizeof(ch[sz]));
			val[sz]=0;
			ch[u][c]=sz++;
			}
		u=ch[u][c];
		}
	val[u]=1;	
}
int d[300000+10],kase;
char words[4000+10][100+5],str[300000+10];
int main(){
	int n;
	while(~scanf("%s",str)){
    memset(val,0,sizeof(val)),memset(d,0,sizeof(d));
	scanf("%d",&n);
	sz=1;memset(ch[0],0,sizeof(ch[0]));	
	for(int i=0;i<n;i++) {  scanf("%s",words[i]);insert(words[i]);	}
	
	
	int len=strlen(str);
	d[len-1]=ch[0][str[len-1]-'a']?1:0;
	
	for(int i=len-2;i>=0;i--){
		int u=0,j=i;
		while(j<=len-1&&ch[u][str[j]-'a']){  
			if(val[ch[u][str[j]-'a']]) d[i]+=d[j+1],d[i]%=20071027;
			u=ch[u][str[j]-'a'];
			j++;	
		}
		if(j==len&&val[u]) d[i]++;
	}

	printf("Case %d: %d\n",++kase,d[0]%20071027);
	}
	return 0;
}
{% endcodeblock %}
