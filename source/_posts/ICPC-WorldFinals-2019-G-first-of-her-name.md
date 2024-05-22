---
title: ICPC WorldFinals 2019 G 「First of Her Name」
date: 2019-05-01 20:24:51
toc: true
categories: Learning
tags: [Algorithm, AC Automaton]
---
## Problem Description
> 题目链接：
[Kattis / First of Her Name](https://open.kattis.com/problems/firstofhername)
[洛谷 / P6257 First of Her Name](https://www.luogu.com.cn/problem/P6257)
> 难度：省选 / NOI−

众所周知，皇室家族的名字非常有讲究。而作为研究皇室的历史学家的你，最近接到了一个艰巨的任务——分析王国历史中所有皇室夫人的名字。

王国历史上有 n 位皇室夫人，方便起见，我们将其从 1 至 n 编号。除了 1 号夫人外，其余夫人的名字均为一个大写字母连接着她母亲的名字。而 1 号夫人作为王国的首任王后，她的名字只有一个大写字母。

例如，由于 AENERYS 由 A 与 ENERYS 组成，因此 ENERYS 是 AENERYS 的母亲。相似地，AENERYS 是 DAENERYS 与 YAENERYS 的母亲。

<!--more-->
你知道王国历史上所有皇室夫人的姓名与关系，而你需要完成的任务是，对于其他历史学家感兴趣的名字串 s，总共有多少位夫人的名字是以 s 起始的。

例如在样例的皇室族谱中，S 至 AENERYS 的这一支（包含 YS、RYS、ERYS、NERYS 与 ENERYS 这几位夫人）均只有一位女儿。接下来 AENERYS 有两位女儿，分别是 DAENERYS，以及女儿是 RYAENERYS 的 YAENERYS。

在这个皇室家族内，有两位夫人的名字以 RY 起始，她们是 RYS 与 RYAENERYS。而 ERYS 与 ENERYS 均以 E 起始。名字以 N 起始的仅有一位夫人 NERYS。同样地，以 S 起始的仅有首位王后 S。而没有任何一位夫人的名字以 AY 起始。

### Input
第一行有两个整数 $n, k$，$n$ $(1 \leq n \leq 10^6)$ 代表王国历史上皇室夫人总数，$k$ $(1 \leq k \leq 10^6)$ 代表其他历史学家感兴趣的名字串的个数。

接下来 $n$ 行描述所有皇室夫人的姓名与关系。第 $i+1$ 行描述第 $i$ 位夫人的资料 $c_i$ 与 $p_i$，其中字符 $c_i$ 表示她名字的首位字母，$p_i$ 为她母亲的编号 $(p_1=0\ 且对于\ 1\lt i\leq n，保证有\ 1\leq p_i\lt i)$。所有夫人的名字均不重复。

接下来 $k$ 行，每行为一个大写字母构成的非空串，代表一个其他历史学家感兴趣的名字串。所有询问串长度之和不超过 $10^6$。

### Output
输出 $k$ 行，第 $i$ 行为一个整数，代表总共有多少位夫人的名字是以第 $i$ 个感兴趣的名字串起始的。

## Thought
所有夫人名字的反串可以构成一棵前缀树，而且每个节点都代表一个唯一的夫人名字。

题目问的是以询问串为前缀的夫人名字有多少，等价于问以询问反串为后缀的夫人名字反串有多少。

那么我们只需要把询问反串也加入前缀树中，接着构造 AC 自动机，问题就转化为求询问反串所在节点的 fail 树的子树中有多少个夫人，从下往上递推累加即可。

<style>
table th:first-of-type {
    width: 35%;
}
table th:nth-of-type(2) {
    width: 35%;
}
table th:nth-of-type(3) {
    width: 15%;
}
table th:nth-of-type(4) {
    width: 15%;
}
</style>

| 名字        | 名字反串   | 母亲节点 | 本节点 |
|-----------:|----------:|:----:|:---:|
| S          | S         | 0    | 1   |
| YS         | SY        | 1    | 2   |
| RYS        | SYR       | 2 | 3 |
| ERYS       | SYRE      | 3 | 4 |
| NERYS      | SYREN     | 4 | 5 |
| ENERYS     | SYRENE    | 5 | 6 |
| AENERYS    | SYRENEA   | 6 | 7 |
| DAENERYS   | SYRENEAD  | 7 | 8 |
| YAENERYS   | SYRENEAY  | 8 | 9 |
| RYAENERYS  | SYRENEAYR | 9 | 10 |

| 询问串 | 询问反串 | 序号 | 答案 |
|:---:|:----:|:--:|:--:|
| RY  | YR   | 0  | 2  |
| E   | E    | 1  | 2  |
| N   | N    | 2  | 1  |
| S   | S    | 3  | 1  |
| AY  | YA   | 4  | 0  |

## Code
```CPP
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;

const int MAXNUM = 1e6 + 10;
const int MAXNODE = 2 * MAXNUM;
const int MAXCHILD = 26;

int globalNode;
int trie[MAXNODE][MAXCHILD], fail[MAXNODE], queue[MAXNODE], cnt[MAXNODE];
int queryNode[MAXNUM];
char query[MAXNUM];

int idx(char ch){
	return ch - 'A';
}

void insert(char c, int mother){
	trie[mother][idx(c)] = ++globalNode;
    cnt[globalNode] = 1;
}

int insert(char *str){
    int node = 0;
    for(int i=strlen(str)-1; i>=0; i--){
        int child=idx(str[i]);
        if(!trie[node][child]) trie[node][child] = ++globalNode;
        node = trie[node][child];
    }
    return node;
}

void build(){
    int front = 0, rear = 0;
    
    for(int i=0; i<MAXCHILD; i++)
        if(trie[0][i]) queue[rear++] = trie[0][i];

    while(front != rear){
        int cur = queue[front++];
        for(int i=0; i<MAXCHILD; i++){
            int node = trie[cur][i];
            if(node){
                fail[node] = trie[fail[cur]][i];
                queue[rear++] = node;
            } else
            	trie[cur][i] = trie[fail[cur]][i];
        }
    }
}

void count(){
	for(int i = globalNode-1; i>=0; i--) cnt[fail[queue[i]]] += cnt[queue[i]];
}

int main(){
	// freopen("find_her_name.in", "r", stdin);
	int n, m;
    scanf("%d %d\n", &n, &m);

    for(int i=0; i<n; i++){
        char c;
        int mother;
        scanf("%c %d\n", &c, &mother);
        insert(c, mother);
    }

    for(int i=1; i<=m; i++){
        scanf("%s", query);
        queryNode[i]=insert(query);
    }

    build();
    count();

    for(int i=1; i<=m; i++) printf("%d\n", cnt[queryNode[i]]);

    return 0;
}
```