# Day 1 · 2026-09-19

## 今日概览

- 学习模块：BFS（广度优先搜索）
- 完成题目：1 道（迷宫可达性判断）
- 今日重点：用结构体存 BFS 状态（不用套嵌套 pair），配合标记数组和队列判重

## 题目 1：迷宫可达性判断（BFS）

**标签**：BFS、搜索

**题目描述**

读入一个 n×m 的网格，`.` 表示可以走，从 (1,1) 出发，判断能否走到 (n,m)：能走到输出 `Yes`，否则输出 `No`。

**思路**

1. 先建立一个结构体存 x、y（以后如果要记录步数也方便加），一开始想的是用 pair，但如果还要记步数就得再套一层 pair，不如结构体简单直接。
2. 先标记再入队，每次取出队头、弹出队头，如果到达终点就输出。

**代码**

```cpp
#include<bits/stdc++.h>
using namespace std;

char grid[110][110];
int n,m;
bool vis[110][110];

int dx[4]={-1,1,0,0};
int dy[4]={0,0,1,-1};

struct node{
    int x;
    int y;
};
queue<node> q;

int main(){
    cin>>n>>m;
    int flag=0;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            cin>>grid[i][j];

    vis[1][1]=true;
    q.push({1,1});

    while(!q.empty()){
        node pre=q.front();
        q.pop();
        if(pre.x==n&&pre.y==m){
            cout<<"Yes";
            flag=1;
            break;
        }
        for(int i=0;i<4;i++){
            int nx=pre.x+dx[i];
            int ny=pre.y+dy[i];

            if(nx>=1&&ny>=1&&nx<=n&&ny<=m&&grid[nx][ny]=='.'&&!vis[nx][ny]){
                vis[nx][ny]=true;
                q.push({nx,ny});
            }
        }
    }
    if(!flag)
        cout<<"No";
    return 0;
}
```

**易错点 / 注意点**

- 忘记定义一个 flag 来标记是否到达终点：第一次的写法不管有没有到终点都会输出 No
- 单字符判断不能用字符串 `""`，要用 `''`
- push 的时候只能 push 一个元素，所以是 `q.push({x,y})`

## 今日总结

- 掌握了：BFS 的标准写法——结构体存状态、vis 数组标记、队列逐层扩展
- 还需加强：（后续再补充）
