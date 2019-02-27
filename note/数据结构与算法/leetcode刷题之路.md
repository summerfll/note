# leetcode刷题之路

## 一.链表相关

### 1.1反转链表(easy)

![241](F:\学习专用\note\pic\241.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
//1.备份head->next
//2.更新head->next
//3.移动head和new_head遍历链表
ListNode *reservelist(ListNode *head)
{
	if (head == NULL)
		return NULL;
	ListNode *new_head = NULL;//指向新链表头结点
	while (head)
	{
		ListNode *next = head->next;//备份head->next
		head->next = new_head;//更新head->next
		new_head = head;//移动new_head
		head = next;//遍历链表
	}
	return new_head;
}
~~~

### 1.2 链表反转（Medium）

已知链表头结点指针head，将链表从位置m到n逆序（不申请额外空间）

要求：

![242](F:\学习专用\note\pic\242.png)

思路：

![243](F:\学习专用\note\pic\243.png)

![244](F:\学习专用\note\pic\244.png)

~~~c
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
/*
1.找到反转开始节点以及反转的前置节点
2.反转（先求反转后的尾部）
3.链接反转的前置节点和后置节点，此时尾部为head注意：反转从头开始的特殊情况
*/
ListNode *reservelist2(ListNode *head,int m,int n)
{
	ListNode *pre_head = NULL;//初始化开始逆置的节点的前驱
	ListNode *result = head;//转换后的头结点
	int len = n - m + 1;//反转的长度
	while (head&&--m)//求取反转的开始点,后移m-1个位置
	{
		pre_head = head;//记录前驱
		head = head->next;
	}
	ListNode *modify_tail = head;//反转后的尾部
	ListNode *new_head = NULL;//反转后的头部
	while (head&&len)//反转
	{
		ListNode *next = head->next;
		head->next = new_head;
		new_head = head;
		head = next;
		len--;
	}
	modify_tail->next = head;//反转后的尾部链接下一个节点
	if (pre_head)//头结点链接反转后的节点
		pre_head->next = new_head;
	else
		result = new_head;
	return result;
}
~~~

2.求两个链表的交点（E）

已知链表A的头结点指针headA，链表B的头结点指针headB，两个链表相交，求两链表交点对应的节点

**要求：**

![245](F:\学习专用\note\pic\245.png)

**法1：时间复杂度O(nlogn),空间复杂度O(n)**

![246](F:\学习专用\note\pic\246.png)

~~~c
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
/*
1.将A节点的所有节点对应的地址插入进set中
2.遍历B链表，查看是否有节点地址相同，第一个相同的即为交点
*/
ListNode* findnode(ListNode*headA,ListNode*headB)
{
    set<ListNode*> res;
    while(headA)
    {
        res.insert(headA);
        headA=headA->next;
    }
    while(headB)
    {
        if(res.find(headB)!=res.end())//！！！查找set中是否有headB中节点地址
            return headB;
        headB=headB->next;
    }
    return NULL;
}
~~~

**法2：时间复杂度O( n),空间复杂度O(1)**

![247](F:\学习专用\note\pic\247.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
/*
1.计算A,B链表长度，较长链表多出的长度
2.长链表向后移动至两个链表对齐
3.两个链表同时向后移动，比较指针是否指向同一节点
*/
class Solution
{
public:
	int find_len(ListNode*head)
	{
		int len = 0;
		while (head)
		{
			len++;
			head = head->next;
		}
		return len;
	}
	ListNode *forward_list(ListNode*head, int len)
	{
		while (head&&len)
		{
			head = head->next;
			len--;
		}
		return head;
	}
	ListNode* findnode(ListNode*headA, ListNode*headB)
	{
		int lenA = find_len(headA);//计算A,B链表长度
		int lenB = find_len(headB);
		if (lenA>lenB)//长链表向后移动至两个链表对齐
		{
			int length = lenA - lenB;
			headA = forward_list(headA, length);
		}
		else
		{
			int length = lenB - lenA;
			headB = forward_list(headB, length);
		}
		while (headA&&headB)//两个链表同时向后移动，比较指针是否指向同一节点
		{
			if (headA == headB)
				return headA;
			headA = headA->next;
			headB = headB->next;
		}
		return NULL;
	}
};

~~~

### 3.链表求环（E）

已知链表可能有环，若有环则返回环起始节点，否则返回NULL

要求：

![248](F:\学习专用\note\pic\248.png)

思路：

![249](F:\学习专用\note\pic\249.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};

class Solution
{
public:
    ListNode *detectCycle(ListNode *head)
    {
        set<ListNode*> ret;
        while(head)
        {
            //注意先查询set中是否有相同地址，没有查到再添加节点到set中
            if(ret.find(head)!=ret.end())
                return head;
            ret.insert(head);
            head=head->next;
        }
        return NULL;
    }
}；
~~~

### 4.链表划分（M）

已知链表指针head与数值x，将所有小于x的节点放在大于或等于x的节点前，且保持这些节点的原来的相对位置

分析：

![250](F:\学习专用\note\pic\250.png)

思路：

![251](F:\学习专用\note\pic\251.png)

![252](F:\学习专用\note\pic\252.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
/*
1.设置两个临时头结点，small_head,big_head,同时设置两个指针，small_ptr和big_ptr，分别指向small_head,big_head
2.遍历链表
将小于x的节点放在small_head后，small_pre后移
将大于或等于x的节点放在big_head后，big_ptr后移
3.链接链表，将big_ptr指向NULL，small_ptr指向big_head.next
*/
class Solution
{
public:
    ListNode *partition(ListNode *head,int x)
    {
        ListNode small_head(0);//设置两个临时头结点，small_head,big_head
        ListNode big_head(0);
        ListNode *small_ptr=&small_head;//设置两个指针，small_ptr和big_ptr，分别指向small_head,big_head
         ListNode *big_ptr=&big_head;
        while(head)//遍历链表
        {
            if(head->val<x)//将小于x的节点放在small_head后，small_pre后移
            {
                small_ptr->next=head;
                small_ptr=head;
            }
            else//将大于或等于x的节点放在big_head后，big_ptr后移
            {
                big_ptr->next=head;
                big_ptr=head;
            }
            head=head->next;
        }
        small_ptr->next=big_head.next;//链接小链表和大链表
        big_ptr->next=NULL;//链接链表尾部
        return small_head.next;
    }
};
~~~

### 5.复杂的链表的深度拷贝（H）

已知一个复杂的链表，节点中有一个指向本链表任意某个节点的随机指针（也可以为空），求这个链表的深度拷贝

~~~C++
struct RandomListNode
{
	int label;
	RandomListNode *next;
    RandomListNode *random;
	RandomListNode(int x) :val(x), next(NULL),random(NULL) {}
};
/*
1.复制每个节点，插到原链表每个节点位置之后
2.拷贝随机指针
3.拆分链表
*/
class Solution
{
public:
    RandomListNode *copyRandomList(RandomListNode *head)
    {
        if(!head)
            return NULL;
        RandomListNode *begin=head;
        RandomListNode *new_head=head;
        while(head)//复制每个节点，插到原链表每个节点位置之后
        {
            RandomListNode *tmp=new RandomListNode(head->label);
            tmp->next=head->next;
            head->next=tmp;
            head=tmp->next;
        }
        head=begin;//返回链表头
        while(head)//设置新节点random指针
        {
            new_head=head->next;
            RandomListNode *node=head->random;
            if(node!=NULL)
            	new_head->random=node->next;
            else
                new_head->random=NULL;
            head=new_head->next;      
        }
        //拆分
        head=begin;//返回链表头
        new_head=begin;//返回链表头
        RandomListNode *result=begin->next;
        while(head)
        {
            new_head=head->next;
            head->next=new_head->next;
            head=new_head->next;
            if(new_head->next)
            	new_head->next=head->next; 
            else
                new_head->next=NULL;
        }
        return result;
    }
};
~~~

### 6.1排序链表的合并（E）

已知两个已排序链表头节点指针l1和l2，将这两个链表合并，合并后仍为有序的，并返回合并后的头结点

分析：

![254](F:\学习专用\note\pic\254.png)

思路：

![255](F:\学习专用\note\pic\255.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
class Solution {
public:
    ListNode *mergeTwoLists(ListNode *l1, ListNode *l2) {
        ListNode new_head(0);//设置临时头节点
        ListNode *ptr=&new_head;
        while(l1&&l2)//遍历l1和l2，将较小节点依次插入临时头节点
        {
            if(l1->val<=l2->val)
            {
                ptr->next=l1;
                l1=l1->next;
                ptr=ptr->next;
            }
            else
            {
                ptr->next=l2;
                l2=l2->next;
                ptr=ptr->next;
            }
        }
        if(l1)//若l1还有剩余节点
            ptr->next=l1;
        if(l2)//若l2还有剩余节点
            ptr->next=l2;
        return new_head.next;
    }
};
~~~

### 6.2排序链表的合并（多个）（H）

已知k个已排序链表头结点指针，将这k个链表合并，合并后仍为有序的，返回合并后的头结点

分析：

![256](F:\学习专用\note\pic\256.png)

思路：

![257](F:\学习专用\note\pic\257.png)

~~~c++
struct ListNode
{
	int val;
	ListNode *next;
	ListNode(int x) :val(x), next(NULL) {}
};
bool cmp(ListNode* a,ListNode*b)
{
    return a->val<b->val;
}
class Solution
{
public:
    ListNode *mergeLists(vector<ListNode*>&lists)
    {
        vector<ListNode*> ret;
        for(int i=0;i<lists.size();i++)//将各个链表中的所有节点放入容器中
        {
            ListNode*head=lists[i];
            while(head)
            {
                ret.push_back(head);
                head=head->next;
            }
        }
        if(ret.size()==0)
            return NULL;
        sort(ret.begin(),ret.end(),cmp);//排序
        for(int i=0;i<ret.size()-1;i++)//链接节点，注意循环次数为ret.size()-1
        {
            ret[i]->next=ret[i+1];
        }
        ret[ret.size()-1]->next=NULL;//尾部节点指向NULL
        return ret[0];
    }   
};
~~~

## 二.栈、队列、堆

### 1.使用队列实现栈

设计一个栈，支持基本的栈操作，这个栈的内部存储数据的结构为队列

~~~c++
class MyStack
{
public:
    MyStack(){   
    }
    void push(int x)
    {
        queue<int> tmp;//定义一个临时队列，存储新加入的数
        tmp.push(x);//入队，此时新加入的数在队头，实现后进先出
        while(data.empty())
        {
            tmp.push(data.front());
            data.pop();
        }
        while(tmp.empty())//将临时队列中的数，复制到data队列
        {
            data.push(tmp.front());
            tmp.pop();
        }
    }
    int pop()
    {
        int tmp=data.front();
        data.pop();
        return tmp;
    }
    int top()
    {
        return data.front();
    }
    bool empty()
    {
        return data.empty();
    }
private:
    queue<int> data;
};
~~~

### 2.使用栈实现队列

设计一个队列，支持基本队列操作，这个队列的内部存储数据的结构为栈

~~~c++
//先进先出
class MyQueue
{
public:
    MyQueue{}
    void push(int x)
    {
        stack<int> tmp;
        while(data.empty())
        {
            tmp.push(data.top());
            data.pop();
        }
        tmp.push(x);
        while(tmp.empty())
        {
            data.push(tmp.top());
            tmp.pop();
        }
    }
    int pop()
        
    {
        int tmp=data.top();
        data.pop();
        return tmp;
    }
    int front()
    {
        return data.top();
    }
    bool empty()
    {
        return data.empty();
    }
private:
    stack<int> data;
}
~~~

### 3.包含min函数的栈

设计一个栈，支持如下操作，这些操作的算法复杂度需要是常数级O(1)

~~~c++
class Solution {
public:
    void push(int value) {//入栈，_min栈，只有小于_min栈的栈顶元素，才入栈
        data.push(value);
        if(_min.empty())
        {
            _min.push(value);
        }
        else
        {
            if(value<_min.top())
                _min.push(value);
        }
    }
    void pop() {//出栈，出栈元素等于min栈的栈顶元素时，min栈的栈顶元素弹出
        int tmp=data.top();
        data.pop();
        if(tmp==_min.top())
            _min.pop();
    }
    int top() {
        return data.top();
    }
    int min() {
        return _min.top();
    }
private:
    stack<int> data;
    stack<int> _min;
};
~~~

