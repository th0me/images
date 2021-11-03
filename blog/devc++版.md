#include <stdio.h>
#include <malloc.h>
typedef int ElementType;
struct LNode{
    ElementType data;
    struct LNode *next;
};
typedef LNode *Linklist;
void InsertAtHead(Linklist &L,int n,ElementType TempData[])
{
    L=(Linklist)malloc(sizeof(LNode));
    L->next=NULL;
    LNode *p,*pre=L;
    for(int i =0;i<n;i++)
    {
        p=(LNode*) malloc(sizeof(LNode));
        p->data=TempData[i];
        p->next=pre->next;
        pre->next=p;
    }
}
void InsertAtTail(Linklist &L,int n,ElementType TempData[]) //尾插法
{
    L=(Linklist)malloc(sizeof(LNode));
    L->next=NULL;
    LNode *p,*pre=L;
    for(int i =0;i<n;i++)
    {
        p=(LNode*)malloc(sizeof(LNode));
        p->data=TempData[i];
        pre->next=p;
        pre=p;
    }
    p->next=NULL;
}
void PrintList(Linklist L)
{
    LNode *p=L;
    printf("Head");
    while(p)
    {
        printf("->%d",p->data);
        p=p->next;
    }
    printf("\n");
}
void ReverseList(Linklist L)
{
    LNode *p,*pre;
    p=L->next;
    L->next=NULL;
    while(p)
    {
        pre=p->next;
        p->next=L->next;
        L->next=p;
        p=pre;
    }
}
LNode* MergeList(Linklist La,Linklist Lb)
{
    // 判断其中一个链表为空，返回另外一个
    if(La==NULL || Lb==NULL)
        return La==NULL ? Lb : La;
    struct LNode* p1,*p2,*LcHead,*LcTail;
    // 头结点没有数据
    LcHead=LcTail=(struct LNode*)malloc(sizeof(struct LNode));
    p1=La->next;
    p2=Lb->next;
    while(p1 && p2)
    {
        if(p1->data<=p2->data)
        {
            LcTail->next=p1;
            LcTail=p1;
            p1=p1->next;
        }
        else
        {
            LcTail->next=p2;
            LcTail=p2;
            p2=p2->next;
        }
    }
    // 判断两个链表是否还有剩余

if(p1)
    LcTail->next=p1;
if(p2)
    LcTail->next=p2;
return LcHead;

}
void DelOdd(Linklist L){
    LNode *p,*pre;
    pre=L;
    p=L->next;
    while(p)
    {
        if(p->data%2==1)
        {
            pre->next=p->next;
            p=pre->next;
        } else
        {
            pre=p;
            p=p->next;
        }
    }

}
int main()
{
    Linklist La,Lb,Lc=NULL;
    ElementType TempData[1000];
    ElementType TempData2[1000];
    int n;
    printf("Please input n:\n");
    scanf("%d",&n);
    printf("Please enter n ascending numbers for La\n");
    for(int i =0;i<n;i++)
        scanf("%d",&TempData[i]);
    printf("La:\n");
    InsertAtHead(La,n, TempData);
    PrintList(La->next);
    printf("Please input n: \n");
    scanf("%d",&n);
    printf("Please enter n ascending numbers for Lb\n");
    for(int i =0;i<n;i++)
        scanf("%d",&TempData2[i]);
    InsertAtTail(Lb,n,TempData2);
    PrintList(Lb->next);
    printf("Reverse La:\n");
    ReverseList(La);
    PrintList(La->next);
    printf("Merge La and Lb:\n");
    Lc=MergeList(La,Lb);
    PrintList(Lc->next);
    printf("Delete odd:\n");
    DelOdd(Lc);
    PrintList(Lc->next);
}