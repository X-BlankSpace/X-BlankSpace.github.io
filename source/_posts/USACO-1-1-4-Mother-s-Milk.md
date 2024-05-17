---
title: USACO 1.1.4 Mother's Milk
date: 2015-11-16 21:50:36
categories: Learning
tags: [Algorithm, BFS]
---
农民约翰有三个容量分别是 A,B,C 升的桶，A,B,C 分别是三个从 1 到 20 的整数。最初，A 和 B 桶都是空的，而 C 桶是装满牛奶的。有时，约翰把牛奶从一个桶倒到另一个桶中，直到被灌桶装满或原桶空了。当然每一次灌注都是完全的。由于节约，牛奶不会有丢失。写一个程序去帮助约翰找出当 A 桶是空的时候，C 桶中牛奶所剩量的所有可能性。

<!--more-->
BFS 广搜，状态数一定不超过 21 × 21 × 21
## code
``` CPP
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;

int a,b,c,vl[21],vis[21][21][21],q[9621+100];

void bfs() {
    int front=0,rear=1;
    vis[0][0][c]=1,vl[c]=1,q[0]=c;

    while(front<rear) {
        int ta=q[front]/441;
        int tb=(q[front]-ta*441)/21;
        int tc=q[front]%21;
        if(ta>0) {
            if(tb<b) {
                if(b-tb>ta&&!vis[0][tb+ta][tc]) {
                    tb+=ta;
                    ta=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                    vl[tc]=1;
                }
                else if(b-tb<=ta&&!vis[ta-(b-tb)][b][tc]) {
                    ta-=(b-tb);
                    tb=b;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                if(ta==0) {
                    vl[tc]=1;
                }
            }

            //重新初始化
            ta=q[front]/441;
            tb=(q[front]-ta*441)/21;
            tc=q[front]%21;

            if(tc<c) {
                if(c-tc>ta&&!vis[0][tb][tc+ta]) {
                    tc+=ta;
                    ta=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                    vl[tc]=1;
                }
                else if(c-tc<=ta&&!vis[ta-(c-tc)][tb][c])  {
                    ta-=(c-tc);
                    tc=c;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                if(ta==0) {
                    vl[tc]=1;
                }
            }
        }

        ta=q[front]/441;
        tb=(q[front]-ta*441)/21;
        tc=q[front]%21;

        if(tb>0) {
            if(ta<a){
                if(a-ta>tb&&!vis[ta+tb][0][tc]) {
                    ta+=tb;
                    tb=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                else if(a-ta<=tb&&!vis[a][tb-(a-ta)][tc]) {
                    tb-=(a-ta);
                    ta=a;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                if(ta==0) {
                    vl[tc]=1;
                }
            }

            ta=q[front]/441,tb=(q[front]-ta*441)/21,tc=q[front]%21;

            if(tc<c){
                if(c-tc>tb&&!vis[ta][0][tc+tb]) {
                    tc+=tb;
                    tb=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                else if(c-tc<=tb&&!vis[ta][tb-(c-tc)][c]) {
                    tb-=(c-tc);
                    tc=c;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                //虽然ta的值没有变化，但是可能一开始ta==0，之后tb,tc变化又产生了新的解
                if(ta==0) {
                    vl[tc]=1;
                }
            }
        }

        ta=q[front]/441;
        tb=(q[front]-ta*441)/21;
        tc=q[front]%21;

        if(tc>0) {
            if(ta<a){
                if(a-ta>tc&&!vis[ta+tc][tb][0]) {
                    ta+=tc;
                    tc=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                else if(a-ta<=tc&&!vis[a][tb][tc-(a-ta)]) {
                    tc-=(a-ta);
                    ta=a;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                if(ta==0) {
                    vl[tc]=1;
                }
            }

            ta=q[front]/441;
            tb=(q[front]-ta*441)/21;
            tc=q[front]%21;

            if(tb<b){
                if(b-tb>tc&&!vis[ta][tb+tc][0]) {
                    tb+=tc;
                    tc=0;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                else if(b-tb<=tc&&!vis[ta][b][tc-(b-tb)]) {
                    tc-=(b-tb);
                    tb=b;
                    vis[ta][tb][tc]=1;
                    q[rear++]=ta*441+tb*21+tc;
                }
                if(ta==0) {
                    vl[tc]=1;
                }
            }
        }
        front++;
    }
}

int main(){
    memset(vl,0,sizeof(vl)),memset(vis,0,sizeof(vis));

    freopen("milk3.in","r",stdin);
    freopen("milk3.out","w",stdout);
    scanf("%d%d%d",&a,&b,&c);

    bfs();

    int flag=0;
    for(int i=0;i<=c;i++) {
        if(vl[i]){
            if(!flag) {
                flag=1;
                printf("%d",i);
            }
            else {
                printf(" %d",i);
            }
        }
    }
    puts("");
    return 0;
}
```
