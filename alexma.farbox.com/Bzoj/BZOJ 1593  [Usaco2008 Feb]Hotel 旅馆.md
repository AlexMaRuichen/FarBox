[题目链接](http://www.lydsy.com/JudgeOnline/problem.php?id=1593)

# Description

奶牛们最近的旅游计划，是到苏必利尔湖畔，享受那里的湖光山色，以及明媚的阳光。作为整个旅游的策划者和负责人，贝茜选择在湖边的一家著名的旅馆住宿。这个巨大的旅馆一共有N (1 <= N <= 50,000)间客房，它们在同一层楼中顺次一字排开，在任何一个房间里，只需要拉开窗帘，就能见到波光粼粼的湖面。 贝茜一行，以及其他慕名而来的旅游者，都是一批批地来到旅馆的服务台，希望能订到D_i (1 <= D_i <= N)间连续的房间。服务台的接待工作也很简单：如果存在r满足编号为r..r+D_i-1的房间均空着，他就将这一批顾客安排到这些房间入住；如果没有满足条件的r，他会道歉说没有足够的空房间，请顾客们另找一家宾馆。如果有多个满足条件的r，服务员会选择其中最小的一个。 旅馆中的退房服务也是批量进行的。每一个退房请求由2个数字X_i、D_i 描述，表示编号为X_i..X_i+D_i-1 (1 <= X_i <= N-D_i+1)房间中的客人全部离开。退房前，请求退掉的房间中的一些，甚至是所有，可能本来就无人入住。 而你的工作，就是写一个程序，帮服务员为旅客安排房间。你的程序一共需要处理M (1 <= M < 50,000)个按输入次序到来的住店或退房的请求。第一个请求到来前，旅店中所有房间都是空闲的。

# Solution

又是一道小白逛公园

唯一不同就是查询左端点

查询顺序请见代码

``` c++
#include <stdio.h>
#include <algorithm>
using namespace std;
struct seg_tree
{
    seg_tree *ls,*rs;
    int lmx,rmx,mx,len;
    int lazy,state;
    seg_tree()
    {
        ls=rs=NULL;
        lmx=rmx=mx=len=0;
        lazy=0;
        state=1;
    }
    void update()
    {
        mx=max(max(ls->mx,rs->mx),ls->rmx+rs->lmx);
        if(ls->lmx==ls->len)
            lmx=ls->len+rs->lmx;
        else
            lmx=ls->lmx;     
        if(rs->rmx==rs->len)
            rmx=rs->len+ls->rmx;
        else
            rmx=rs->rmx;     
         
    }
    void push_down()
    {
        if(lazy)
        {
            ls->state=lazy;
            rs->state=lazy;
            ls->lazy=lazy;
            rs->lazy=lazy;
            if(ls->state<0)
            {
                ls->mx=ls->lmx=ls->rmx=0;
                rs->mx=rs->lmx=rs->rmx=0;
            }
            else
            {
                ls->mx=ls->lmx=ls->rmx=ls->len;
                rs->mx=rs->lmx=rs->rmx=rs->len;
            }
            lazy=0;
        }
    }
}*root;
void build(seg_tree* &x,int l,int r)
{
    x->len=r-l+1;
    if(l==r)
    {
        x->mx=x->lmx=x->rmx=1;
        return;
    }
    int mid=(l+r)>>1;
    x->ls=new seg_tree();
    x->rs=new seg_tree();
    build(x->ls,l,mid);
    build(x->rs,mid+1,r);
    x->update();
}
int query(seg_tree* &x,int len,int l,int r)
{
    if(len==r-l+1)
        return l;
    int mid=(l+r)>>1;
    x->push_down();
    if(x->ls->mx>=len)
        return query(x->ls,len,l,mid);
    else if(x->ls->rmx+x->rs->lmx>=len)
        return  mid-x->ls->rmx+1;
    else
        return query(x->rs,len,mid+1,r);
}
void modify(seg_tree* &x,int tag,int l,int r,int L,int R)
{
    if(l==L&&r==R)
    {
        x->lazy=tag;
        if(tag>0)
            x->mx=x->lmx=x->rmx=x->len;
        else
            x->mx=x->lmx=x->rmx=0;
        return;
    }
    x->push_down();
    int mid=(L+R)>>1;
    if(mid>=r)
        modify(x->ls,tag,l,r,L,mid);
    else if(mid<l)
        modify(x->rs,tag,l,r,mid+1,R);
    else
        modify(x->ls,tag,l,mid,L,mid),modify(x->rs,tag,mid+1,r,mid+1,R);
    x->update();
}
int n,m;
int main()
{
    scanf("%d%d",&n,&m);
    root=new seg_tree();
    build(root,1,n);
    while(m--)
    {
        static int opt,x,y;
        scanf("%d",&opt);
        if(opt==1)
        {
            scanf("%d",&x);
            if(root->mx<x)
                puts("0");
            else
            {
                y=query(root,x,1,n);
                printf("%d\n",y);
                modify(root,-1,y,y+x-1,1,n);
            }
        }
        else
        {
            scanf("%d%d",&x,&y);
            modify(root,1,x,x+y-1,1,n);
        }
    }
}

```

