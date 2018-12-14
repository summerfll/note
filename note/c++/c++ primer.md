一.变量和基本类型

## 1.字符和字符串区别

单引号括起来的一个字符称为char型，如‘abs’

双引号括起来的零个或多个字符构成字符串,如“hello world”

编译器会在每个字符串的结尾处添加一个空字符‘\0’，所以字符串的实际长度比它的内容多1

## 2.类型转换

当一个算数表达式中既有无符号数又有int值时，那个int值就会转换成无符号数。

例：

~~~c
unsigned u = 10;

int i = -42;

std::cout << i + i<<std:endl;//输出-84

std::cout << u + i<<std::endl;//输出4294967264，如果int占32位

-1=4294967296-1  
~~~

### **2.1 四个显式强制转换**

static_cast:只要不包含底层const，都可使用

dynamic_cast：支持运行时的类型识别

const_cast：只能改变运算对象的底层const

reinterpret_cast：为运算对象的位模式提供较低层次上的重新解释

## 3.初始化和赋值的区别

初始化和赋值是两个完全不同的操作，初始化是创建变量时赋予其一个初始值。

赋值：把对象的当前值擦除，以一个新值来替代

## 4.指针数组和数组指针

指针数组：数组元素全为指针char *pl[5]={"你","好",'"世","界"}

数组指针：指向[数组](https://baike.baidu.com/item/%E6%95%B0%E7%BB%84)首元素的地址的[指针](https://baike.baidu.com/item/%E6%8C%87%E9%92%88)，其本质为指针（这个指针存放的是数组首地址的地址，相当于2级指针，这个指针不可移动） char num[][3]={"你","好"}

## 5.指针

~~~c
int num = 10;

int *p = &num;//p是指向num的指针，或p中存放num的地址

cout <<p<<endl;//打印num的地址

cout<<*p<<endl;//打印num的值
~~~

**指向指针的指针**

int **p2= &p;//p2指向一个int型的指针

## 6.引用

只存在c++中，引用标识符以符号&开头

作用：

- 引用作为其他变量的**别名**而存在，在一些场合可以代替指针
- 引用相对于指针来说具有更好的可读性和实用性

~~~c++
int i = 10;

int &r=i;//r是一个引用，与i绑定在一起

r=20;//则i=20

cout<<&r<<&i;//输出i和r的地址相同

cout<<r<<i<<endl;//输出r和i的值均为20
i=40;
cout<<r<<i<<endl;//输出r和i的值均为40
~~~

### 6.1 引用本质探究

- 单独定义的引用时，必须初始化：说明很像一个常量
- 普通引用有自己的内存空间，很像指针所占的内存大小

~~~c++
#include<iostream> 
using namespace std;
struct t
{
	double &a;//占用4
	int &b;//占用4
};

int main() {
   cout << sizeof(t) << endl;
	system("pause");
	return 0;	
}
~~~

输出：8

**结论：**

1）引用在C++中的内部实现是一个**常指针**

​          **Type& name == Type* const name**

2）C++编译器在编译过程中使用常指针作为引用的内部实现，因此引用所占用的空间大小与指针相同。

3）从使用的角度，引用会让人误会其只是一个别名，没有自己的存储空间。这是C++为了实用性而做出的细节隐藏

~~~c++
void fun(int &a)
{
    a=90;
}
void fun(int *const a)//实现效果相同
{
    *a=90;
}
~~~

### 6.2 对指针的引用

语法：类型 &引用名=指针名;//可以理解为：（类型） &引用名=指针名，即将指针的类型当成类型*

~~~c
#include<iostream> 
using namespace std; 
int main(){
    int a=10; 
    int *ptr=&a; 
    int *&new_ptr=ptr; 
    *new_ptr=20;
    cout<<&ptr<<" "<<&new_ptr<<endl;
    cout<<*ptr<<" "<<a<<" "<<*new_ptr<<endl;
    return 0;  
}
~~~

结果：

![17](F:\学习专用\note\pic\17.png)



#### 指针引用作函数参数

~~~c++
#include<iostream>
using namespace std;

struct teacher
{
	char name[32];
	int age;
};
int fun(teacher **p)
{
	teacher *tmp = NULL;//定义tmp为指向teacher类型的指针
	if (p == NULL)
	{
		return -1;
	}
	tmp = (teacher *)malloc(sizeof(teacher));
	if (tmp == NULL)
	{
		return -2;
	}
	strcpy(tmp->name,"myteacher");
	//p是实参的地址，*p用间接方式改变实参的值
	*p = tmp;
}

int fun2(teacher* &p1)
{
	//给p1赋值相当于给main函数中的t1赋值
	p1 = (teacher *)malloc(sizeof(teacher));
	if (p1 == NULL)
	{
		return -1;
	}
	strcpy(p1->name, "myteacher");
}

void freeT(teacher *p)
{
	if (p == NULL)
	{
		return;
	}
	free(p);
}
int main() {
	//c中的二级指针
	teacher *t1 = NULL;
	fun(&t1);
	cout << t1->name << endl;
	freeT(t1);

	//c++中的引用（指针引用）
	teacher *t2 = NULL;
	fun2(t2);
	cout << t2->name << endl;
	freeT(t2);

	system("pause");
	return 0;
	
}
~~~

### 6.3 引用作函数参数

~~~c++
void swap(int &a,int &b)
{
    int tmp =a;
    a=b;
    b=tmp;
}
void main()
{
    int x=10,y=90;
    swap(x,y);
    cout<<x<<y<<endl;
}
~~~

### 6.4 函数的返回值是引用

~~~c++
//例1
#include<iostream> 
using namespace std;

int fun()
{
	int a = 10;
	return a;
}
//返回a的内存空间 返回一个a的副本10
int& fun1()
{
	int a1 = 10;
	return a1;
}

int main() {
	int a1 = fun();
	int a2 = fun1();//10
	int &a3 = fun1();//左值为引用，则将a1地址赋给a3
	cout << a1 << " " << a2 << " " << a3;//打印a3时，由于a3为引用，将打印*a3，此时栈中无a1的值（已被清理），所以打印的是地址
	system("pause");
	return 0;
}
~~~

结果：

![19](F:\学习专用\note\pic\19.png)

~~~c++
//例2
#include<iostream> 
using namespace std;

int fun()
{
	int a = 10;
	return a;
}
//返回a的内存空间 返回一个a的副本10
int& fun1()
{
    static int a1 = 10;//静态变量
    a1++;
	return a1;
}

int main() {
	int a = fun();
	int a2 = fun1();//11
	int &a3 = fun1();//左值为引用，则将a1地址赋给a3
	cout << a << " " << a2 << " " << a3<<endl;//打印a3时，由于a3为引用，将打印*a3，此时栈中含有a1的值
	fun1() = 100;//函数返回值是一个引用，并且当左值，此时左值为a1的内存地址
	cout << fun1() << endl;//打印a1的值，效果与上a3相同
	system("pause");
	return 0;	
}
~~~

结果：

![20](F:\学习专用\note\pic\21.png)

**结论：**

当函数返回值为引用时：

- 若返回栈变量，不能成为其它引用的初始值，不能作为左值使用（例1）
- 若返回静态变量或全局变量可以成为其他引用的初始值即可作为右值使用，也可作为左值使用（例2）

### 6.5 常量引用

```c++
//常引用 初始化 分为2种情况
//1）用变量 初始化 常引用
int a = 90;
const int &b = a;//用a初始化常引用
//b=20;//错，常引用，是让变量引用只读属性，不能通过b去修改a

//2）用字面量 初始化 常引用
//int &c = 20;//普通引用 引用一个字面量，字面量没有内存地址，会出错，因为引用就是给内存取多个别名
const int &c = 20;//c++编译器 会分配内存空间
```
结论：

- const int &e 相当于const int * const e
- 普通引用 相当于int *const e
- 当使用常量（字面值）对const引用进行初始化时，c++编译器会为常量分配空间，并将引用名作为这段空间的别名
- 使用字面值对const引用初始化后，将生成一个只读变量

## 7.const限定符

const对象创建后值就不能改变，c++中会将定义的const变量存入**符号表**中

**好处：**

- 指针作函数参数，可以有效的提高代码可读性，减少bug
- 清楚的分清参数的输入和输出特性

### 7.1 顶层const

指针本身为常量

~~~c
int i=0;
int *const pi = &i;//顶层const，不能改变pi的值，即指针变量本身不能被修改
pi=10;//错，不能改变pi的值
*pi=20;//对，可以改变i的值
~~~

### 7.2 底层const

指针所指的对象为常量

~~~c
const int r=32;
const int *p = &r;//底层const，允许改变p的值，即指针所指向的内存空间不能被修改
p=12;//对，改变p的值
*p = 20;//错，不能改变r的值
~~~

### 7.3 特定const

~~~c++
const int r1=32;
const int *const p1 = &r1;//指针所指向的内存空间不能被修改，且指针变量本身不能被修改
p1=12;//错，不能改变p1的值
*p1 = 20;//错，不能改变r1的值
~~~

### 7.4 一般const

~~~c++
const int i=90;
int const s=80;//与上效果相同，即i和s的值不能被修改
~~~

### 7.5 const分配内存时机

可能分配存储空间,也可能不分配存储空间  

- 当const常量为全局，并且需要在其它文件中使用，会分配存储空间
- 当使用&操作符，取const常量的地址时，会分配存储空间
- 当const int &a = 10; const修饰引用时，也会分配存储空间
- **const在编译器编译时就分配内存，而不是在运行时**

~~~c++
#include<iostream>
using namespace std;

void main()
{
	int a;
	const int b=0;
	int c;
	cout << &a << " "<<&c <<"  "<< &b;
	cout << "hello" << endl;
	system("pause");
}
~~~

结果：

![16](F:\学习专用\note\pic\16.png)

### 7.6 const与#define的相同与不同

相同：

~~~c++
const int i=8;
#define i 8  //效果与上相同
~~~

不同：

- const常量是有编译器处理的，宏定义是由预处理器处理，单纯的文本替换
- const常量提供类型检查和作用域检查，即有作用域限制，而宏定义没有，宏定义在一个作用域定义后可在另一个作用域中使用

## 8.constexpr和常量表达式

### 8.1 常量表达式

值不会改变并且在编译过程就能得到计算结果的表达式。

例：字面值，用常量表达式初始化的const对象

~~~c
const int i=20;//i是一个常量表达式，字面值为常量表达式
const int j=i+1;//j是一个常量表达式
const int s=get_size();//s不是常量表达式
~~~

### 8.2 constexpr

c++新标准规定，允许将变量声明为constexpr类型对象以便编译器来验证变量的值是否是一个常量表达式。声明为constexpr变量一定是一个常量，而且必须用常量表达式初始化

~~~c
constexpr int sa=20;//20是常量表达式，初始化正确
constexpr int la=sa+19;//sa+19是常量表达式，初始化正确
constexpr int s=size();//只有当size是一个constexpr函数时，才是一条正确的语句
~~~

## 9.decltype类型指示符

c++新标准中引入decltype，作用是选择并返回操作数的数据类型。

~~~c
decltype(f()) sum = x;//sum的类型为函数f的返回类型
const int ci=0,&cj = ci;
declytpe(ci) x=0;//x的类型是const int
decltype(cj) y=x;//y的类型是const int &,y绑定到变量x
~~~

## 10.size_t类型

size_t是一些C/C++标准在stddef.h中定义的。这个类型足以用来表示对象的大小。size_t的真实类型与操作系统有关。


在**32位**架构中被普遍定义为：

typedef   unsigned int size_t;

而在**64位**架构中被定义为：

typedef  unsigned long size_t;  

**与int的不同点：**

- size_t在32位架构上是4字节，在64位架构上是8字节，在不同架构上进行编译时需要注意这个问题。而int在不同架构下都是4字节，与size_t不同；
- 且int为带符号数，size_t为无符号数。

# 二.字符串、向量和数组

## 1.string标准库

**string对象和字面值相加**

string对象可以和一个字面值相加

string s1= "hello";

string s3 = s1+"world";

两个字面值不能直接相加：如 string s4= "hello"+"world";---错误

## 2.范围for语句!!!!!

用于遍历给定序列中的每个元素并对序列中的每个元素执行某种操作

表示格式：

~~~c
for(declaration : expression)

     statement
~~~

例：

~~~c
string str("some string");

for(auto c : str)  //遍历str中的每个字符

   cout<< c << endl;//输出当前字符
~~~

例：将字符串中的所有s字符改为a---------试用于对一个字符串中的字符进行修改

~~~c
string str("some string");
for(auto &c : str)//c为引用，依次绑定str中的每一个字符（即c为啥str中的那个字符便是啥），如果字符为s则将s改为a
{ 
    if(c=='s')
        c='a';
}
cout << str <<endl;
~~~

结果：

![img](file:///E:/%E6%9C%89%E9%81%93%E7%AC%94%E8%AE%B0/zhangxiafll@163.com/e5d25f8e4f9a409998b4aa24d435c691/clipboard.png)

## 3.处理string对象中的单个字符的方法

（1.使用下标的方式：s[0]为第一个字符，s[1]为第二个字符，类推，直到s[s.size()-1]

（2.使用迭代器

## 4.vector使用

头文件：#include<vector>

**（1）定义和初始化对象--注意区别括号和大括号**

~~~c
vector<int> num;//初始状态为空

vector<vector<int>> fi;//元素为vector的vector对象

vector<string> str(3,"hello");//初始化3个string元素，每个均为字符串hello

vector<string> str2{"hello","my","world"};
等同于vector<string> str2={"hello","my","world"}；//初始化vector对象str2包含三个元素，即3个字符串
vector<int> num1(10);//num1中有10个0

vector<int> num2{10};//num2中有1个10
~~~

**（2）向vector添加元素**

使用push_back向尾端添加元素

~~~c
vector<int> nb;

for(int i=0;i<10;i++)
    nb.push_back(i);
~~~

**（3）其他操作**

vector<string> v;

v.empty;//如果v为空，则为真，否则假 v.size();//v的大小 v[n];//返回第n个元素的引用

例：	

~~~c
  vector<int> num{ 1,2,5,4,7 };

	for (auto &i : num)//引用num中的每个元素

		i *= i;

	for (auto c : num)

		cout << c << endl;
~~~



## 5.迭代器

**（1）迭代器和指针的区别：**

迭代器：

​      （1）迭代器不是指针，是类模板，表现的像指针。他只是模拟了指针的一些功能，通过重载了指针的一些操作符，->,*,++ --等封装了指针，是一个“可遍历STL（ Standard Template Library）容器内全部或部分元素”的对象， 本质是封装了原生指针，是指针概念的一种提升（lift），提供了比指针更高级的行为，相当于一种智能指针，他可以根据不同类型的数据结构来实现不同的++，--等操作；

​      （2）迭代器返回的是对象引用而不是对象的值，所以cout只能输出迭代器使用*解引用后的值而不能直接输出其自身。

​      （3）在设计模式中有一种模式叫迭代器模式，简单来说就是提供一种方法，在不需要暴露某个容器的内部表现形式情况下，使之能依次访问该容器中的各个元素，这种设计思维在STL中得到了广泛的应用，是STL的关键所在，通过迭代器，容器和算法可以有机的粘合在一起，只要对算法给予不同的迭代器，就可以对不同容器进行相同的操作。

指针：

​        指针能指向函数而迭代器不行，迭代器只能指向容器；指针是迭代器的一种。指针只能用于某些特定的容器；迭代器是指针的抽象和泛化。所以，指针满足迭代器的一切要求。

​        总之，指针和迭代器是有很大差别的，虽然他们表现的行为相似，但是本质是不一样的！一个是类模板，一个是存放一个家伙的地址的指针变量。

**（2）迭代器使用**

定义迭代器：vector<int>::iterator it;//能读写vector<int>中的元素

vector<int>::const_iterator it;//只能读元素，不能写

或使用auto方式，自动声明格式

begin（）指向第一个元素的迭代器，end（）指向最后一个元素的下一个元素

若v.begin()==v.end(),则容器为空

迭代器使用（++）运算符：指向后一个位置

cbegin()，cend()指向容器位置与begin()和end()同，但返回值为const_iterator

auto it2 = v.cbegin();//it2类型为const_iterator 

### 5.1 迭代器运算

iter + n ;//指向位置向后移动n个位置

iter - n ;//指向位置向前移动n个位置

例:使用迭代器完成二分搜索

~~~c
	vector<int> number1 = { 12,45,89,120,230,450 };
	int find_number =45;
	vector<int>::iterator start, end,mid;
	start = number1.begin();
	
	end = number1.end();
	mid = number1.begin()+(end - start) / 2;
	while (mid != end && *mid != find_number)
	{
		if (find_number < *mid)
			end = mid;
		else
			start = mid + 1;
		mid = start + (end - start) / 2;
	}
	if (mid == end)
		cout << "not find" << endl;
	else
		cout << " find" <<  find_number<<endl;
~~~

## 6.数组

字符数组：

const char a1[5]="hello";//错--没有空间存放空字符

char a2[]="hello";

char a3[]={'h','e','l','l','o','\n'}；与上相等

数组声明：

~~~c
int *ptr1[10];//ptr1是一个含有10个整型指针的数组，即指针数组

int (*ptr)[10] = &arry;//ptr是一个指向含有10个整数的数组

int (&arr)[10]=arry;//arr是一个引用含有10个整数的数组

int *(&arr)[10]=arry;//arr是数组的引用，该数组含有10个指针
~~~

利用范围for，遍历数组中的所有元素：

~~~c
int array[10]={12,45,12,56,9,9845,656};
for(auto c : array)
    cout<<c<<endl;
~~~



## 7.多维数组

int ia[3][4];第一维为行，第二维为列

**多维数组初始化**

~~~c
int id[3][4] = {
{1,2,3,4},
{5,6,7,8},
{9,10,11,12}
};
int ia[3][4]={1,2,3,4,5,6,7,8,9,10,11,12};//与上表达式相同
~~~

**多维数组下标引用**

int (&row)[4]=ia[2];//把row定义成一个含有4个整数的数组的引用，绑定到ia二维数组的第3行

# 三.表达式

## 1.条件运算符

**条件运算符**

cond ? expr1 : expr2 判断cond条件是否为真，真执行expr1，假执行expr2

**嵌套条件运算符**

例：

fial_grade = (grade > 90) ? "high":(grade > 60)? "pass":"false"

## 2.break语句

break语句负责终止离他最近的while, do while , for或swich语句

## 3.continue语句

终止最近的循环中的当前迭代并立即开始下一次迭代。continue只能出现在for,  while 和 do while循环的内部

# 四.函数

## 1.形参和实参

实参是形参的初始值，与形参存在对应的关系，必须与对应形参类型匹配。

### 1. 1 形参

形参是一种自动对象（只存在于块执行期间的对象），函数开始时为形参申请存储空间，函数终止，形参也被销毁

**无形参函数**

~~~c
void func1();
void func2(void);
~~~

**有参函数**

需写出每个形参的声明符

~~~c
int fun(int a1,int a2,char a3)
~~~

## 2.局部静态对象

局部变量定义为static类型的对象，第一次定义对象后，直到程序终止才销毁。

~~~c
int func1()
{
	static int num = 0;
	return ++num;
}
int main()
{	
    for (int i=0;i < 10;i++)
		cout << func1() << endl;
    return 0;
}
~~~

结果：

![4](F:\学习专用\note\pic\4.png)

## 3.函数声明

函数只能定义一次，但可以多次声明，即重构函数

函数三要素：返回类型，函数名，形参类型

## 4.参数传递

### 4.1 传值

对形参的所有操作不影响实参，实参值不变

~~~c
void fun(int a,int b)
{
    int tmp;
    tmp=a;
    a=b;
    b=tmp;
}
int main()
{
    int a=10,b=20;
    fun(a,b);
    cout<<"a="<<a<<"b="<<b<<endl;
}
~~~

结果：

![5](F:\学习专用\note\pic\5.png)

### 4.2 传地址

指针形参指向实参的地址，可以改变实参的值

~~~c
void fun(int *a,int *b)
{
    int tmp;
    tmp=*a;
    *a=*b;
    *b=tmp;
}
int main()
{
    int x=10,y=20;
    fun(&x,&y);
    cout<<"x="<<x<<"y="<<y<<endl;
}
~~~

结果：

![6](F:\学习专用\note\pic\6.png)

### 4.3 传引用

通过形参通过引用方式绑定实参，改变实参的值

~~~c
void fun(int &a,int &b)
{
    int tmp;
    tmp=a;
    a=b;
    b=tmp;
}
int main()
{
    int x=10,y=20;
    fun(x,y);
    cout<<"x="<<x<<"y="<<y<<endl;
}
~~~

结果：

![7](F:\学习专用\note\pic\7.png)

## 5.指针或引用形参与const

可以用非常量初始化一个底层const对象，但是反过来不行；一个普通引用必须用同一类型的对象初始化

~~~c
const int &r = 43;//使用非常量初始化一个底层const
int &r1=24;//错误，不能用字面值初始化一个非常量引用
~~~

## 6.main:处理命令行选项

~~~c
int main(int argc,char *argv[]);
~~~

argr是一个整数，argv是指向char型指针的指针，即指向字符串的指针（指针数组）

脚本命令：prog -d  -o file

即argc=4，argv[0]="prog";argv[1]="d";...argv[4]=0;

## 7.含可变形参的函数

为了编写能处理不同数量实参的函数，c++11新标准提供了两种方法：

- **实参类型相同**

可以传递一个名为initializer_list的标准库类型，其提供的操作与vector类似

![8](F:\学习专用\note\pic\8.png)

例：

~~~c
void err(initializeer_list<string> li)
{
    for(auto beg=li.begin();beg!=li.end();beg++)
        cout<<*beg<<endl;
}
~~~

- **实参类型不同**

后面介绍

## 8.无返回值函数

返回void的函数不要求非的有return语句，函数的最后一句会隐式执行return，若在中间位置想提前退出，课使用return语句。

## 9.函数重载

同一个作用域内的几个函数名字相同但**形参列表不同**（类型/个数），称为重载函数（**返回值类型需相同**）

~~~
int f(int i);
int f(double j);
f(9);//调用第一个函数
f(9.22);//调用第二个函数
~~~

**注意：**

- 函数重载在本质上是相互独立的不同函数（静态链编）
- 重载函数的函数类型是不同的
- 函数返回值不能作为函数重载的依据，返回值不同，不为重载函数
- 函数重载由函数名和参数列表决定

### 9.1 重载和const形参

一个拥有顶层const的形参无法和另一个没有顶层const的形参区分开来

~~~c
Record look(Phone *);
Record look(Phone *const);//重复声明了Record look(Phone *)
Record look(Phone);
Record look(const Phone);//重复声明了Record look(Phone)
~~~

### 9.2 重载与作用域

在不同的作用域中无法重载函数名，内层作用域中的声明会隐藏外层作用域中的声明

~~~c
void f(int i);
void f(double j);
void func(int val)
{
    void f(const string c);//新作用域：隐藏了之前的f
    f("hello");//正确
    f(2);//错误：void f(int i)被隐藏
    f(2.99);//错误：void f(double j)被隐藏
    
}
~~~

正确使用方式：将一个函数的重载放同一个作用域中

~~~c
void f(int i);
void f(double j);
void f(const string c);
void func(int val)
{
    void f(const string c);//新作用域：隐藏了之前的f
    f("hello");//正确：调用void f(const string c)
    f(2);//正确：调用void f(int i)
    f(2.99);//正确：调用void f(double j)
    
}
~~~

### 9.3 函数重载与函数指针

~~~c++
//函数指针
//声明一个函数类型
typedef void(myFunc)(int a,int b);//函数别名创建，创建void fun(int a,int b)的别名为myFunc
//myFunc为一般类型，如int
myFunc *myfunc=NULL;//定义一个函数指针，这个指针指向函数的入口地址

//声明一个函数指针类型
typedef void(*myFunc2)(int a,intb);//声明了一个指针的数据类型
myFunc2 myfunc;//通过 函数指针类型定义了一个函数指针变量

//定义一个函数指针 变量
void （*myFunc3）(int a,int b);
~~~

~~~c++
#include<iostream>
using namespace std;

void fun(double a, double b)
{
	cout <<"a: "<< a << " b: " << b << endl;
}
void fun(int a, int b)//函数重载
{
	cout <<"a: "<< a << " b: " << b << endl;
}
void fun(int a)//函数重载
{
	cout <<"a: "<< a << endl;
}
typedef void(Func)(int a, int b);//声明一个函数类型
typedef void(*Func1)(double a, double b);//声明一个函数指针类型
void(*fun2)(int a);//定义一个函数指针变量
int main() {

	Func *myfun = NULL;//定义一个函数指针
	myfun = fun;
	myfun(1, 2);

	Func1 myfun1;//定义一个函数指针
	myfun1 = fun;
	myfun1(4.23, 5.22);//调用重载函数

	fun2 = fun;
	fun2(3);
	system("pause");
	return 0;
}
~~~

结果：

![22](F:\学习专用\note\pic\22.png)

函数重载，重写，重定义的区别？？？

## 10.默认实参

**注意**

1）默认参数规则，如果默认参数出现，那么默认参数右边的都必须有默认参数

如：

~~~c++
void fun(int a=9,int b=90,int c=23);//对
void fun2(int a=9,int c);//错
void fun3(int a,int b=90,int c=80);//对
~~~

2）若你填写参数，则使用你填写的参数，不写则使用默认的参数

~~~c++
string func(int i=20,int j = 10,char sa = "he");
string wind;
wind=func();//等价于func(20,10,"he")
wind=func(12);//等价于func(12,10,"he")
wind=func(2,12);//等价于func(2,12,"he")
~~~

## 11.函数匹配

确定某次调用应该选用哪个重载函数

~~~c
void f(int);
void f(int,int);
void f(double,double=3.14);
f(3.22);//将调用f(double,double=3.14)
~~~

分析：

- f(int)可行，可将double转换成int
- f(double,double=3.14)可行，第二个形参提供默认值，第一个形参为double更匹配，所以选用次重载函数

## 12.函数指针

指向函数的指针

### 12.1 使用函数指针

~~~c
bool lend(const string &);
pf=lend;//pf指向lend函数
bool b1=pf("hello");//调用lend函数
bool b2=(*pf)("hello");//等价的调用
~~~

### 12.2 重载函数的指针

~~~c
void f(int *);
void f(double);
void (*d)(double)=f;//d指向f（double）
~~~

## 13.内联函数

函数返回值前含有inline的函数为内联函数，如：

~~~c++
inline int fun(int a)
~~~

注意：

1)内联函数声明和函数体的实现，写在一起,如下所示

~~~c++
inline int fun(int a)
{
    int b=a;
    cout<<b<<endl;
}
~~~

2）c++编译器直接将函数体插入在函数调用的地方

~~~c++
inline int fun(int a)
{
    int b=a;
    cout<<b<<endl;
}
void main()
{
    int a=10;
    fun(a);//实际上c++编译器把 int b=a;cout<<b<<endl;两句内容插入到了主函数中，没有普通函数调用时的额外开销（压栈，跳转，返回）
}
~~~

3）现代c++编译器能够进行编译优化，因此一些函数即使没有使用inline声明，也可能被编译器内联编译

4）c++中内联编译的限制：

- 不能存在任何形式的循环语句
- 不能存在过多的添加判断语句

结论：

- 内联函数在编译时直接将函数体插入函数调用的地方
- inline只是一种请求，c++编译器不一定准许这种请求
- 内联函数省去了普通函数调用时压栈，跳转和返回的开销

## 14.函数占位参数

~~~c++
void fun(int a,int b,int c)
{
    cout<<a<<b<<endl;
}
void main()
{
    fun(90,80,70);//对
    fun(90,80);//错
}
~~~

# 四. 类

基本思想：数据抽象和封装

数据抽象：一种依赖于接口和实现分离的编程技术。

## 1.构造函数

初始化类对象的数据成员，一个类可含有多个构造函数与重载函数类似

特点：

- 与类名相同
- 没有返回类型

### 1.1合成的默认构造函数

类通过一个特殊的构造函数来控制默认初始化过程，这个函数为默认构造函数，默认构造函数无需任何实参。如果类没有显式的定义构造函数，那么编译器就会隐式的定义一个默认构造函数。也称合成的默认构造函数。

初始化类的数据成员的规则：

- 如果存在类内的初始值，用它来初始化成员
- 否则，默认初始化该成员

### 1.2 定义构造函数

c++新标准中，使用默认构造函数，可以在参数列表后写上=default来要求编译器生成构造函数，如果=default在类的内部，则默认构造函数是内联的

~~~c++
class f{
    f()=default;
    f(const std::string &sa):book(sa){}
    std::string book;
}
~~~

不使用default则构造函数在类的外部

~~~c++
f::f(const std::string &sa)
{
    book(sa);
}
~~~

## 2.访问控制与封装

封装的两层含义：

- 把属性和方法进行封装
- 对属性和方法进行访问控制

使用访问说明符加强类的封装性：

- public：成员在整个程序内可被访问，类内和类外均可
- protect：成员可以被类的成员函数访问，只能在类内被访问，不能在类外访问，用在继承中
- private：成员可以被类的成员函数访问，只能在类内被访问，不能在类外访问

**说明：**

类内：指类的内部：

~~~c++
class f
{
public:
int a;
void func(int b)//1.直接在类class里面进行函数定义
{
    a=b;
}
void func2(int a);
}
void f::func2(int a)//2. 在类里面进行函数声明，在外部使用域名
{
    cout<<a<<endl;
}
~~~

### 2.1 class和struct区别

使用class和struct关键字开始类的定义

**区别：**struct和class的默认访问权限不同，包含默认成员访问说明符和默认派生访问说明符

- 使用struct关键字，定义在第一个访问说明符之前的成员是public，使用class关键字，则这些成员为private
- 默认派生访问说明符不同，class关键字默认private继承，struct默认public继承

~~~c++
//默认成员访问说明符
class func {
	   int j;//private成员
	public:
		double num;
	};
	struct func {
	   int i;//public成员
	public:
		double num;
	};
//默认派生访问说明符
	struct child1:func{};//默认public继承
	class child2:func{};//默认private继承
~~~

### 2.2 封装的优点

- 确保用户代码不会无意间破坏封装对象的状态
- 被封装的类的具体实现细节可以随时改变，无须调整用户级别的代码

## 3.友元

类可以允许其他类或者函数访问他的非公有成员，方法是令其他类或者函数成为他的友元，添加一条以friend关键字开始的函数声明的语句。

### 友元函数

~~~c++
#include<iostream>
using namespace std;

class Test 
{
	//1. 声明友元函数 前加friend 友元函数声明的位置与访问限制符无关
	friend void modify(Test *p,int c);//2.函数modify 是类Test的好朋友
public:
	Test (int);
	void getT()
	{
		cout << a << endl;
	}
private:
	int a;
};

Test ::Test (int a)
{
	this->a = a;
}

void modify(Test *p,int c)
{
	p->a = c;
}
void main()
{
	Test t(90);
	cout << "a的初始值为： ";
	t.getT();
	cout << "友元函数修改类中私有成员a后a的值为：";
	modify(&t,100);
	t.getT();
	system("pause");
}
~~~

结果：

![30](F:\学习专用\note\pic\30.png)

### 友元类

若B类是A类的友元类，则B类的所有成员函数都是A类的友元函数



~~~c++
	class B {
	   friend class A;//声明友元类,A为B的友元类
	public:
		int num;
	};
	class A{
	public:
		void f(B *b,int a)
        {
            b->num=a;
        }；//对：A是B的友元类
	};
~~~

**注意：**

友元关系不能继承

## 4.委托构造函数

c++新标准扩展了构造函数初始值的功能，一个委托构造函数使用它所属类的其他构造函数执行它自己的初始化过程。

~~~c++
class sale{
public:
    sale(std::string s,int cn,double p):book(s),sold(cn),rev(p){}//非委托构造函数使用对应的实参初始化成员
    sale():sale("",0,0){}//构造函数委托给另一个构造函数初始化成员
    sale(std::string s):sale(s,0,0){}//构造函数委托给另一个构造函数初始化成员
}
~~~

## 5.聚合类

用户可以直接访问成员，聚合类的特点：

- 所有成员都是public
- 没有定义任何构造函数
- 没有类内初始值
- 没有基类，没有virtual函数

例：

~~~c++
struct data{
    int val;
    string s;
}
data da={0,"ame"};//初始化聚合类数据成员
~~~

## 6.类的静态成员

### 6.1 声明静态成员

在成员的声明前加关键字static使得其与类关联在一起

- 静态**数据成员**的类型可以是常量、引用、指针、类类型
- 静态**成员函数**不与任何对象绑定一起，不包含this指针，不能被声明为const的

~~~c++
class accout{
public:
    static void rate(double);//静态成员函数
private:
    int a;//普通变量
    static double interestrate;//静态成员变量
}
~~~

### 6.2 定义静态成员

~~~c
void account::rate(double newrate)//方式1
{
    interestrate=newrate;//对
    a=10;//错，不能对非静态成员进行操作，因为c++编译器不知道所操作的对象是哪一个
    //accout a1.a2; a1.rate(2);a2.rate(3);
}
double accout::interestrate=10;//方式2
void main()
{
    accout::rate(2.12);//调用静态成员函数的方式1
    accout a1;
    a1.rate(2.22);//调用静态成员函数的方式2
}
~~~

一般来说，不能在类的内部初始化静态成员，一个静态数据成员只能被定义一次。类似于全局变量，一旦定义将一直存在于程序的整个生命周期。

静态函数中，不能使用 普通成员函数和变量

**分析：**

![07_静态成员变量和静态成员函数分析](F:\学习专用\note\pic\07_静态成员变量和静态成员函数分析.bmp)

### 6.3 静态成员的类内初始化

特殊情况：静态成员const整数类型的类内初始化

要求：

- 静态成员必须是字面值常量类型的constexpr
- 初始值必须是常量表达式

~~~c++
class accout{
public:
    static void rate(double);
private:
    static constexpr double interestrate = 30;
}
~~~

### 6.4 静态变量的特殊作用

静态变量独立于任何对象，所有某些非静态数据成员可能非法的场合，静态成员能正常使用

#### 6.4.1 静态变量可以是不完成类型

~~~c
class bar{
    static bar men;//对，静态指针可以是不完全类型
    bar *men2;//对，指针成员可以是不完全类型
    bar men3;//错误，数据成员必须是完全类型
}
~~~

#### 6.4.2 使用静态成员作为默认实参

~~~c
class accout{
public:
    static void rate(double i=interestrate);
private:
    static double interestrate;
}
~~~

# 五.面向对象的程序设计

##  c++面向对象模型初探

~~~c++
#include "iostream"

using namespace std;

class C1
{
public:
	int i;  //4
	int j; //4
	int k;  //4
protected:
private:
}; //12

class C2
{
public:
	int i;
	int j;
	int k;

	static int m; //4
public:
	int getK() const { return k; } //4
	void setK(int val) { k = val; }  //4

protected:
private:
}; //12

struct S1
{
	int i;
	int j;
	int k;
}; //12

struct S2
{
	int i;
	int j;
	int k;
	static int m;
}; //12

int main()
{
	printf("c1:%d \n", sizeof(C1));
	printf("c2:%d \n", sizeof(C2));
	printf("s1:%d \n", sizeof(S1));
	printf("s2:%d \n", sizeof(S2));

	system("pause");
}

~~~

**结果：**

![28](F:\学习专用\note\pic\28.png)

**结论：**

C++类对象中的成员变量和成员函数是分开存储的

- 普通成员变量：存储于对象中，与struct变量有相同的内存布局和字节对齐方式
- 静态成员变量：存储于全局数据区中

###  C++编译器对类的内部处理 

![29](F:\学习专用\note\pic\29.png)

结论：

1. C++类对象中的成员变量和成员函数是分开存储的。C语言中的内存四区模型仍然有效！
2. C++中类的普通成员函数都隐式包含一个指向当前对象的this指针。
3. 静态成员函数与普通成员函数的区别

- 静态成员函数不包含指向具体对象的指针
- 普通成员函数包含一个指向具体对象的指针

### this指针

~~~c++
#include "iostream"

using namespace std;

class C1
{
public:
	C1(int a)//==>C1(C1 * const this,int a)
	{
		i = a;//this->i=a;
	}
	void print()
	{
		cout << i << endl;
	}
    void fun(int x,int y) const //void fun(const C1 *const this,int x,int y)
    {
        i=x;//报错,this->i=x;const修饰的是this指针所指向的内存空间
    }
protected:
private:
	int i;
}; 
int main()
{
	C1 c1(10);
	c1.print();//==》c1.print(&c1)
	system("pause");
}
~~~

### 三大特性：

封装，继承，多态

### **虚函数**：

基类希望他的派生类各自定义适合自身的版本，此时基类就将这些函数声明为虚函数

### **派生类中的虚函数：**

一个派生类的函数如果覆盖了某个继承而来的虚函数，则它的形参类型必须与被覆盖的基类函数完全一致，返回类型也必须与基类函数匹配。

~~~c++
class que{
public:
    virtual double func(string str) const;//const表示这个函数不能修改类里面成员变量的值
    virtual double func2(int) const;
}
~~~

### **override：**

c++中新标准允许派生类显式的注明他将使用哪个成员函数改写基类的虚函数

注意：使用override的好处：

- 使得程序员的意图更加清晰
- 让编译器发现一些错误（未覆盖虚函数则报错）

~~~c++
class que1::public que{
public:
     double func(string str) const override;//正确
     double func2(double) const override;//错：que中无形如func2（double）的虚函数
     double func3(string) const override；//错：que中无func3虚函数
     
}
~~~

### **final:**

- c++新标准提供一种**防止继承发生**的方法，即在类名后面跟一个关键字**final**：

```c++
class deve final { };//deve不能作为基类
```

- 在某个函数后加final，则派生类无法覆盖改函数

~~~c++
class que{
public:
    virtual double func2(int) const final;
}
class qu:public que{
public:
     double func2(int) const;//错误，无法覆盖func2
}
~~~

### **动态绑定**：

使用动态绑定能用一段代码同时处理基类和派生类的对象。函数的运行版本由实参决定，即在运行时选择函数的版本，所以动态绑定又称为运行时绑定。

**注意：**动态绑定只有当我们通过指针或引用调用虚函数时才会发生

~~~c++
#include<iostream>
using namespace std;

//动态绑定
namespace test1 {
	class func {
	public:
		virtual double print(double) const;//虚函数
	};
	double test1::func::print(double x) const
	{
		return x;
	
	}

	class child_func :public func {
	public:
		double print(double) const override;
	};
	double test1::child_func::print(double x) const
	{
		return x*5;

	}
	void f(const func &item, double n)
	{
		cout << item.print(n) << endl;
	}

}
int main()
{
	//动态绑定
	double i = 2.134;
	test1::func func1;
	test1::child_func child_func1;
    test1::f(func1,i);//func1的类型是func，调用func的print
	test1::f(child_func1, i);//child_func1的类型是child_func，调用child_func的print

}
~~~

## 1.派生类

| 基类成员   | 基类private成员 |        | 基类public成员 |        |
| ---------- | --------------- | ------ | -------------- | ------ |
| 派生方式   | private         | public | private        | public |
| 派生类成员 | 不可见          | 不可见 | 可见           | 可见   |
| 外部函数   | 不可见          | 不可见 | 不可见         | 可见   |

| 派生方式      | 基类的public成员  | 基类的protected成员 | 基类的private成员 | 派生方式引起的访问属性变化概括                 |
| ------------- | ----------------- | ------------------- | ----------------- | ---------------------------------------------- |
| private派生   | 变为private成员   | 变为private成员     | 不可见            | 基类中的非私有成员都成为派生类中的私有成员     |
| protected派生 | 变为protected成员 | 变为private成员     | 不可见            | 基类中的非私有成员在派生类中的访问属性都降一级 |
| public派生    | 仍为public成员    | 仍为protected成员   | 不可见            | 基类中的非私有成员在派生类中的访问属性保持不变 |

### 1.1 继承与静态成员

~~~c++
class Base{
public:
   static void st();
};
class deve:public Base{
    void f();
};
void deve::f()
{
    Base::st();//对：通过Base定义了st
    deve::st();//对：通过deve继承了st
    st();//对：通过this对象访问
}
~~~

## 2.抽象基类

含有（或者未经覆盖直接继承）纯虚函数的类是抽象基类，抽象基类负责定义接口，而后续的其他类可以覆盖该接口。

**注意：**不能定义抽象函数的对象

### 纯虚函数

纯虚函数没有实际意义，一个纯虚函数无须定义，在函数声明语句后书写=0就可以将一个虚函数说明为纯虚函数

~~~c++
#include<iostream>
using namespace std;
namespace test2 {
	class func {
	public:
		virtual double print(double) const;//虚函数
	};
	double test2::func::print(double x) const
	{
		return x;

	}

	class child_func :public func {//抽象基类
	public:
		double print(double) const =0;//声明为纯虚函数
	};

	class child2_func :public child_func {
	public:
		double print(double) const override;
	};

	double test2::child2_func::print(double x) const
	{
		return x*0.5;

	}
	void f(const func &item, double n)
	{
		cout << item.print(n) << endl;
	}

}
int main()
{
	//抽象基类
	test2::child2_func child_func2;
    cout<<child_func2.print(2.222)<<endl;
}
~~~

### 重构

在上述例子中，func继承体系中增加func1类是重构的典型例子。

重构是负责重新设计类的体系以便**将操作和数据从一个类移动到另一个类中**。

## 3.访问控制和继承

### 受保护的成员

类使用protected关键字来声明那些它希望与**派生类**分享但不想被其他公共访问使用的成员。

**特点：**

- 和私有成员类似，受保护的成员对于类的用户来说是不可访问的
- 和公有成员类似，受保护的成员对于派生类的成员和友元来说是可访问的
- 派生类的成员或友元只能通过派生类对象来访问基类的受保护成员，派生类对于一个基类对象中的受保护成员没有访问权

## 4.虚函数与作用域

~~~c++
class base{
public:
  virtual int fun();//虚函数
}
class c1:public base{
public:
    int fun(int);//与虚函数fun()不一致，隐藏基类的fun()
    virtual int fun2();//虚函数fun2（）
}
class c2:public c1{
public:
    int fun(int);//一个非虚函数，隐藏了c1的fun(int)
    int fun();//覆盖了base的虚函数fun()
    int fun2();//覆盖了c1的虚函数fun2()
}
~~~

### 通过基类调用隐藏的虚函数

~~~c++
base bo1;c1 bo2;c2 bo3;
base *ba1 = &bo1,*ba2=&bo2,*ba3=&bo3;
ba1->fun();//虚调用，调用base::fun()
ba2->fun();//虚调用，调用base::fun()
ba3->fun();//虚调用，调用c2::fun()
ba2->fun2();//静态类型和动态类型不一致，一个对象、引用或指针的静态类型决定该对像的哪些成员可见，静态类型base *，动态类型为c1，所以base类可见，c1不可见，而base中无fun2，语句错误
c1 *ca1 = &bo2;c2 *ca2=&bo3;
ca1->fun2();//虚调用，调用c1::fun2()
ca2->fun2();//虚调用，调用c2::fun2()
~~~

## 5.构造函数与拷贝控制

在基类中将析构函数定义成虚函数以确保执行正确的析构版本

~~~c++
class base{
public:
   virtual ~base()=default;//动态绑定析构函数
}
base *item=new base;//静态类型与动态类型一致
delete item;//调用base析构函数
item=new c1;//静态类型与动态类型不一致（参考前一段代码）
delete c1;//调用c1的析构函数
~~~

## 6.构造函数与析构函数

构造函数作用：为类中的成员进行初始化

析构函数作用：释放内存，对象生命周期结束后被自动调用

#### **构造函数分类**

-  无参构造函数
- 有参构造函数
- 拷贝构造函数（赋值构造函数）--若类中没有拷贝构造函数，c++编译器会自动提供一个默认的拷贝构造函数
- 默认构造函数（类中没有构造函数，c++编译器会自动提供一个默认构造函数）

~~~c++
#include<iostream>
using namespace std;

class point
{
public:
	int p_a;
	int p_b;
public:
	point()
	{
		p_a = 0;
		p_b = 0;
		cout << "无参构造函数" << endl;
	}
	point(int x, int y)
	{
		p_a = x;
		p_b = y;
		cout << "有参构造函数" << endl;
	}
	point(int x)
	{
		p_a = x;
		p_b = 0;
		cout << "有参构造函数" << endl;
	}
	point(const point& obj)//拷贝构造函数 作用：用一个对象初始化另一个对象
	{
		cout << "拷贝构造函数" << endl;
		p_a = obj.p_a;

	}
	void pointp()
	{
		cout << "p_a: " << p_a << endl;
	}
	~point()
	{
		cout << "析构函数，释放内存空间" << endl;
	}
};
void f(point p)
{
	cout << p.p_a << endl;
}
void play()
{
	point c5(1, 2);
	point c6 = c5;
	f(c6);//c实参初始化形参p,会调用构造函数
}
//f2函数返回一个元素
//返回的是一个新的匿名对象（所以会调回匿名对象类的拷贝构造函数）
point f2()
{
	point c7(2, 3);
	return c7;
}
void play2()
{
	point m = f2();//用匿名对象初始化m，此时c++编译器直接把匿名对象转成m（扶正）
				   //匿名函数去留：1. 匿名函数被扶正
	cout << "匿名函数去留：1. 匿名函数被扶正" << endl;
	cout << m.p_a << endl;
}
void play3()
{
	point m2(1, 2);
	m2 = f2();//用匿名对象 赋值给另外一个同类型的对象，匿名函数被直接析构，
			  //匿名函数去留：2. 匿名函数被直接析构
	cout << "匿名函数去留：2. 匿名函数被直接析构" << endl;
	cout << m2.p_a << endl;
}
int main() {
	//调用无参构造函数
	point p;

	//调用有参构造函数
	//1. 括号法  c++编译器自动调用构造函数
	point p2(11, 55);
	//2. 等号法 c++编译器自动调用构造函数
	//调用有一个参数的构造函数
	point p4 = (1, 2, 3);//c++对等号功能增强，括号中取最后一个数进行赋值，与point p4 = 3同义;
	point p1 = point(12, 45);//调用有两个参数的构造函数
	//3. 直接调用构造函数 手动的调用构造函数
	point p5 = point(2, 3);//产生一个匿名对象，后将匿名对象直接扶正为p5 

	//调用拷贝构造函数
	//1. 
	point c1(12, 23);
	point c2 = c1;//用c1来初始化c2
	c2.pointp();
	//2.
	point c3(10, 20);
	point c4(c3);//用c3来初始化c4
	c3.pointp();
	//3.函数形参需调用拷贝构造函数
	play();//调用三次析构函数，释放内存先后顺序为，匿名对象-c6-c5
	//4.函数返回值调用拷贝构造函数
	play2();//调用两次析构函数，一次释放c7内存，一次释放匿名对象内存
	//关于匿名函数的去留
	play3();//调用三次析构函数，一次释放c7内存，一次释放匿名对象内存，一次释放m2的内存
	system("pause");
	return 0;
}
~~~

结果：

![24](F:\学习专用\note\pic\24.png)

匿名对象的去留：

![25](F:\学习专用\note\pic\25.bmp)

#### **构造函数使用规则**

当类中定义了构造函数时，则c++编译器不会提供默认的构造函数。**即类中写了构造函数，就必须用**

#### 构造函数初始化列表

~~~c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Test
{
private:
	int m_a;
public:
	Test(int a)
	{
		m_a = a;
		cout << "Test的构造函数: " << m_a <<endl;
	}
	~Test()
	{
		cout << "Test的析构函数: " << m_a << endl;
	}

};
class Test2
{
public:
	Test2(int a, int b, int c, int d) :t1(c), t2(d),c(d)//初始化列表
	{
		n_a = a;
		n_b = b;
		cout << "Test2的构造函数" << endl;
	}
	~Test2()
	{
		cout << "Test2的析构函数 "<< endl;
	}
private:
	int n_a;
	int n_b;
	Test t2;
	Test t1;
    const int c;

};
//1. 先执行 被组合对象的构造函数
//2. 被组合对象的构造顺序 与定义顺序有关 与初始化列表的顺序无关
//3.析构函数：与构造函数的调用顺序相反
//4.初始化列表用来给const成员赋值
void testplay()
{
	Test2 t(1, 2, 3, 4);
}
int main() {
	testplay();
	system("pause");
	return 0;
}
~~~

结果：

![27](F:\学习专用\note\pic\27.png)

#### 构造函数总结

- 构造函数是c++中用于初始化对象状态的特殊函数
- 构造函数在对象创建时自动被调用
- 构造函数和普通成员函数都遵循重载规则
- 拷贝构造函数是对象正确初始化的重要保证
- 必要的时候，必须手工编写拷贝构造函数

## 7.深拷贝和浅拷贝

### 浅拷贝

默认拷贝构造函数可以完成对象的数据成员值简单的复制，对象的数据资源是由指针指示的堆时，默认拷贝构造函数仅作指针值复制，即拷贝的对象中成员也指向了相同的内存空间，析构时会出现对同一内存空间的两次内存释放，导致出错

![26](F:\学习专用\note\pic\26.bmp)

### 深拷贝

深拷贝，重新为对象成员分配新的内存空间，上图蓝色线条所示，手动编写拷贝构造函数代码，解决浅拷贝出现的问题

~~~c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Test
{
public:
	char *p_str;
	int p_len;
public:
	Test(const char *p)
	{
		p_len = strlen(p);
		p_str = (char *)malloc(p_len+1);//注意+1 strlen计算的长度不包含 '\0'
		strcpy(p_str, p);
	}
	Test(const Test& obj)//深拷贝，重新为对象分配新的内存空间，上图蓝色线条所示，手动编写拷贝构造函数代码，解决浅拷贝出现的问题
	{
		p_len = obj.p_len;
		p_str = (char *)malloc(p_len + 1);//注意+1 strlen计算的长度不包含 '\0'
		strcpy(p_str, obj.p_str);
	}
	~Test()
	{
		if (p_str != NULL)
		{
			free(p_str);
			p_str = NULL;
			p_len = 0;
		}
	}
	void print()
	{
		cout << p_str << endl;
	}
};

int main() {

	Test p("zxfll");
	p.print();
	Test t1 = p;
	t1.print();
    Test t2;
    t2=t1;//等号操作符，默认的为浅拷贝，分析图如下所示
	system("pause");
	return 0;
}
~~~

结果：

![25](F:\学习专用\note\pic\25.png)

**等号操作符图示：**

蓝色线为obj3本来分配的内存，红色线为等号操作符，浅拷贝后的修改的

![04_默认的等号赋值操作也是浅拷贝](F:\学习专用\note\pic\04_默认的等号赋值操作也是浅拷贝.bmp)

# 六.顺序容器

## 1.顺序容器概述

### 1.1 顺序容器类型

| vector       | 可变大小数组。支持快速随机访问。在**尾部**插入数据或删除元素很快，可以插入到其他位置，不过很耗时 |
| ------------ | ------------------------------------------------------------ |
| deque        | 双端队列。支持快速随机访问。在**头尾**位置插入/删除数据很快  |
| list         | 双向链表。只支持**双向**顺序访问。可在**任何位置**进行插入/删除操作 |
| forward_list | 单向链表。只支持**单向**顺序访问。可在**任何位置**进行插入/删除操作 |
| array        | 固定大小数组。支持快速随机访问。不能添加或删除元素           |
| string       | 与vector相似，专门用于保存字符。随机访问快，在**尾部**插入/删除速度快 |

### 1.2 选择容器基本原则

- 一般情况下选择vector容器
- 程序要求随机访问元素，应使用vector或deque
- 程序要求在中间位置插入或删除元素，应使用list或forward_list
- 程序需要在头尾插入或删除元素，且不在中间位置插入或删除，选用deque
- 程序在读取输入时需要在容器中间位置插入元素，随后需要随机访问元素，则输入阶段使用list，输入完成后，将list中的内容拷贝到vector中

## 2.顺序容器操作

### 2.1 向顺序容器添加元素

~~~c
push_back();//向后添加
push_front();//向前添加
insert();//插入元素
~~~

**注意：**

- forward_list 有自己专有的insert和emplace，不支持push_back和emplace_back
- vector和string不支持push_front和emplace_front
- vector，list，deque和string支持push_back
- list，deque，forward_list支持push_front
- 除了array，其他顺序容器均支持insert操作
- 元素插入到vector，deque和string中的任何位置都是合法的，只是这样做很耗时

### 2.2 insert操作

将元素插入到迭代器所指定的位置**之前**,返回指向**新添加的元素的迭代器**。

~~~c++
slit.insert(iter,"hello");//将"hello"插入到迭代器指向的位置的前一个位置
slit.insert(slit.end(),10,"al");//将10个"al"插入到slit的末尾
~~~

### 2.3 emplace操作

c++11新标准引入三个新成员-emplace_front,emplace和emplace_back这些是操作构造而不是拷贝元素，分别对应push_front、insert和push_back，允许我们将元素放置在容器头部、指定位置或容器尾部。

调用emplace_back时，会在容器管理的内存空间中直接创建对象，调用push_back则会创建一个临时对象，并将其压入容器中。

### 2.4 访问元素

顺序容器中的front和back函数，返回容器中的首元素和尾元素的**引用**。

与begin和end的区别：begin是指向容器首元素的位置，end是指向元素尾元素的下一个位置

**注意：**

- 每个顺序容器均有front函数，包含array
- 除forward_list外，每个顺序容器均有back函数

~~~C++
auto val1=*s.begin();auto val2 = s.front();//val1和val2均为s的第一个元素
auto end=s.end();auto val3=*(--end);
auto val4=s.back();//val3和val4均为s的最后一个元素
auto &sa=s.back();//获得最后一个元素的引用
sa = 90;//将s中的最后一个元素变为90
~~~

### 2.5 删除元素

~~~c
pop_back;//删除容器中的尾元素
pop_front();//删除容器中的首元素
erase();//删除迭代器所指的元素，返回指向被删元素后的下一个元素的迭代器
~~~

**注意：**

- forward_list有特殊版本的erase
- forward_list不支持pop_back
- vector和string不支持pop_front

### 2.6 erase操作

~~~c++
//删除单个元素
auto it=lis.begin();
it=lis.erase(it);//删除第一个元素，此时it指向第二个元素的位置
//删除多个元素
lis.clear();
lis.erase(lis.begin(),lis.end());//与上同，删除lis中的所有元素，删除连个迭代器所表示的范围
~~~

### 2.7 特殊的forward_list 操作

forward_list单向链表，未定义insert，emplace和erase，而是insert_after,emplace_after和erase_after

~~~c++
lis.before_begin();//指向链表首元素之前不存在的元素的迭代器，有点像end()
lis.insert_after(p,t);//在迭代器p之后插入元素t
lis.emplace_after();
lis.erase_after(p);//删除p指向位置之后的元素，返回一个指向被删除元素之后的元素的迭代器
~~~

例：

~~~c++
forward_list<int> lis={0,1,2,3,4,5,6,7,8,9};
auto bef_begin=lis.before_begin();
auto begin=lis.begin();
while(begin != lis.end())
{
    if(*begin%2)//如果lis中元素为奇数，则删除该元素
        begin=lis.erase_after(bef_begin);
    else{
        bef_begin=begin;
        ++begin;
    }
}
~~~

### 2.8 容器操作可能使迭代器失效

**向容器中添加元素：**

- 容器为vector或string，指向插入位置前的元素迭代器，指针和引用仍有效，指向插入位置之后的任何位置都会导致迭代器。指针和引用将失效
- 对于list，forward_list，指向容器的迭代器，指针和引用仍有效

**删除一个元素：**

- 容器为vector或string，指向删除位置之前的迭代器。指针和引用仍有效
- 对于list，forward_list，指向容器的迭代器，指针和引用仍有效

**注意：删除元素时，尾后迭代器总会失效，所以一般不保存尾迭代器的值**

## 3. string操作

#### append和replace函数

append操作是在string末尾进行插入操作

~~~c++
string s("hello "),s2=s;
s.insert(s.size(),"world");
s2.append("world");//s2==s 为"hello world"
~~~

replace函数，调用erase和insert的简写

~~~c
s.erase(6,5);//s=="hello "
s.insert(6,"c++");//s=="hello c++"
s2.replace(6,5,"c++");//s2=="hello c++"
s2.replace(6,5,"myfamily");//删除6个字符后的5个字符，插入字符s2=="hello myfamily"
~~~

## 4.vector和string内存分配

capacity操作告诉我们容器在不作扩张内存空间的情况下可以容纳多少个元素

reserve操作允许恶魔通知容器它应该准备保存多少个元素

~~~c
vector<int> icr;
cout<<icr.size()<<icr.capacity()<<endl;//此时打印的值均为0
icr.push_back(12);
cout<<icr.size()<<icr.capacity()<<endl;//此时size为1，capacity应大于等于1,具体值依赖于标准库实现
~~~

# 七. 模板与泛型编程

函数的业务逻辑一样，函数需要的参数不一样，则需要函数模板

## 1.函数模板

**作用：让类型参数化，方便程序员编码**

#### 函数模板机制结论

- 编译器并不是把函数模板处理成能够处理任意类的函数

- 编译器从函数模板通过具体类型产生不同的函数

- 编译器会对函数模板进行**两次编译**：

  在声明的地方对模板代码本身进行编译,生成简陋的函数模板原形；

  在调用的地方对参数替换后的代码进行编译，产生具体的函数原形。

~~~c++
template<typename T> //模板参数,关键字template
int compare(const T &v1,const T &v2)
{
    if(v1<v2) return -1;
    if(v2>v1) return 1;
    return 0;
}
~~~

模板参数表示在类或函数中定义中用到的类型或值。当使用模板时，指定模板实参，将其绑定到模板参数上。

### 1.1 实例化函数模板

- **显式类型调用**，用户自己定义模板参数类型

~~~c++
int x=10,y=20;
compare<int>(x,y);
char a='a',b='b';
compare<char>(a,b);
~~~

- **自动类型推导**，调用函数模板时，编译器用函数实参来为我们推断模板实参。

~~~c++
cout<<compare(2.12,3.11)<<endl;//T为double，实参类型为double，编译器会推断出模板实参为double，并将它绑定到模板参数T
cout<<compare(2,3)<<endl;//T为int
~~~

### 1.2 模板类型参数

~~~c
template<typename T> T foo(T *P)//返回类型和参数类型相同
{
    T tmp = *p;
    ...
    return tmp;
}
~~~

类型参数前必须使用关键字class或typename，两者没什么不同

~~~c
template<typename T,class U> cals (const T&,const U&);
~~~

### 1.3 非类型模板参数

~~~c
template<unsigned N, unsigned M>
int compare(const char (&p1)[N],const char (&p2)[M])
{
    return strcmp(p1,p2);
}
compare("hi","mom");//编译器实例化版本为int compare(const char (&p1)[3],const char (&p2)[4])
~~~

### 1.4 函数模板案例

~~~c++

#include <iostream>
using namespace std;
//让 类型参数化 ===, 方便程序员进行编码
// 泛型编程 
//template 告诉C++编译器 我要开始泛型编程了 .看到T, 不要随便报错
//排序
template <typename T1, typename T2>
void mySort(T1 *a, T2 b)
{
	int i, j;
	for (i = 0;i < b;i++)
	{
		for (j = i + 1;j < b;j++)
		{
			int tmp = 0;
			if (a[i] < a[j])
			{
				tmp = a[i];
				a[i] = a[j];
				a[j] = tmp; 
			}
		}
	}

}
//函数模板作参数
template <typename T1, typename T2>
void myprint(T1 *a, T2 b)
{
	for (int i = 0;i < b;i++)
		cout << a[i] << " ";
}
//函数模板的调用
// 显示类型 调用 
// 自动类型 推导
void main()
{
	////int型
	//int array[9] = { 12,45,98,78,546,125,2,3,4 };
	//int size = sizeof(array) / sizeof(array[9]);
	//mySort<int, int>(array, size); //显示类型调用 模板函数 <>
	//myprint<int, int>(array, size);

	//char型
	char str[] = "sadsdasd4554512";
	int size1 = strlen(str);
	mySort<char, int>(str, size1);
	myprint<char, int>(str, size1);
}
~~~

### 1.5 C++编译器模板机制剖析

思考：为什么函数模板可以和函数重载放在一块。C++编译器是如何提供函数模板机制的？

#### 编译器编译原理

#### **什么是gcc** 

| gcc（GNU Compiler Collection）编译器的作者是Richard   Stallman，也是GNU项目的奠基者。 |
| ------------------------------------------------------------ |
| *什么是gcc：gcc是GNU   Compiler Collection的缩写。最初是作为C语言的编译器（GNU C Compiler），现在已经支持多种语言了，如C、C++、Java、Pascal、Ada、COBOL语言等*。 |
| gcc支持多种硬件平台，甚至对Don Knuth 设计的 MMIX 这类不常见的计算机都提供了完善的支持 |

#### **gcc主要特征**

1）gcc是一个可移植的编译器，支持多种硬件平台

2）gcc不仅仅是个本地编译器，它还能跨平台交叉编译

3）gcc有多种语言前端，用于解析不同的语言

4）gcc是按模块化设计的，可以加入新语言和新CPU架构的支持

5）gcc是自由软件

#### **gcc编译过程**

预处理（Pre-Processing）

编译（Compiling）

汇编（Assembling）

链接（Linking）

Gcc *.c –o 1exe (总的编译步骤)

Gcc –E 1.c –o 1.i  //宏定义 宏展开

Gcc –S 1.i –o 1.s //汇编

Gcc –c 1.s –o 1.o  //二进制文件

Gcc 1.o –o 1exe

结论：gcc编译工具是一个工具链。。。。

![9](F:\学习专用\note\pic\9.png)

hello程序是一个高级Ｃ语言程序，这种形式容易被人读懂。为了在系统上运行hello.c程序，每条Ｃ语句都必须转化为低级机器指令。然后将这些指令打包成可执行目标文件格式，并以二进制形式存储器于磁盘中。

**gcc常用编译选项**

| 选项  | 作用                                                         |
| ----- | ------------------------------------------------------------ |
| -o    | 产生目标（.i、.s、.o、可执行文件等）                         |
| -c    | 通知gcc取消链接步骤，即编译源码并在最后生成目标文件          |
| -E    | 只运行C预编译器                                              |
| -S    | 告诉编译器产生汇编语言文件后停止编译，产生的汇编语言文件扩展名为.s |
| -Wall | 使gcc对源文件的代码有问题的地方发出警告                      |
| -Idir | 将dir目录加入搜索头文件的目录路径                            |
| -Ldir | 将dir目录加入搜索库的目录路径                                |
| -llib | 链接lib库                                                    |
| -g    | 在目标文件中嵌入调试信息，以便gdb之类的调试程序调试          |

**练习** 

- gcc -E   hello.c -o hello.i（预处理）
- gcc -S   hello.i -o hello.s（编译）
- gcc -c   hello.s -o hello.o（汇编）
- gcc   hello.o -o hello（链接）
- 以上四个步骤，可合成一个步骤   gcc   hello.c -o hello（直接编译链接成可执行目标文件）   gcc -c   hello.c或gcc -c hello.c -o hello.o（编译生成可重定位目标文件）   

~~~c

#include <iostream>
using namespace std;

// c.cpp

// g++ -S c.cpp  -o c.s
template <typename T>
void myswap(T &a, T &b)
{
	T c = 0;
	c = a;
	a = b;
	b = c;
	cout << "hello ....我是模板函数 欢迎 calll 我" << endl;
}
int main()
{
    {
		int x = 10; 
		int y = 20;

		myswap<int>(x, y); //1 函数模板 显示类型 调用
	}

	{
		char a = 'a'; 
		char b = 'b';

		myswap<char>(a, b); //1 函数模板 显示类型 调用
	}
	return 0;
}

~~~

~~~c
//通过gcc -S c.cpp -o c.s生成汇编文件，可以看到模板函数在编译过程中会根据显示内容调用生成不同的函数
    .file	"cc.cpp"
	.local	_ZStL8__ioinit
	.comm	_ZStL8__ioinit,1,1
	.text
	.globl	main
	.type	main, @function
main:
.LFB1022:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$16, %rsp
	movq	%fs:40, %rax
	movq	%rax, -8(%rbp)
	xorl	%eax, %eax
	movl	$10, -16(%rbp)
	movl	$20, -12(%rbp)
	leaq	-12(%rbp), %rdx
	leaq	-16(%rbp), %rax
	movq	%rdx, %rsi
	movq	%rax, %rdi
	call	_Z6myswapIiEvRT_S1_       //25行转到52行
	movb	$97, -16(%rbp)
	movb	$98, -12(%rbp)
	leaq	-12(%rbp), %rdx
	leaq	-16(%rbp), %rax
	movq	%rdx, %rsi
	movq	%rax, %rdi
	call	_Z6myswapIcEvRT_S1_      //32行转到90行
	movl	$0, %eax
	movq	-8(%rbp), %rcx
	xorq	%fs:40, %rcx
	je	.L3
	call	__stack_chk_fail
.L3:
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1022:
	.size	main, .-main
	.section	.rodata
	.align 8
.LC0:
	.string	"hello ....\346\210\221\346\230\257\346\250\241\346\235\277\345\207\275\346\225\260 \346\254\242\350\277\216 calll \346\210\221"
	.section	.text._Z6myswapIiEvRT_S1_,"axG",@progbits,_Z6myswapIiEvRT_S1_,comdat
	.weak	_Z6myswapIiEvRT_S1_
	.type	_Z6myswapIiEvRT_S1_, @function
_Z6myswapIiEvRT_S1_:            //52行
.LFB1023:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$32, %rsp
	movq	%rdi, -24(%rbp)
	movq	%rsi, -32(%rbp)
	movl	$0, -4(%rbp)
	movq	-24(%rbp), %rax
	movl	(%rax), %eax
	movl	%eax, -4(%rbp)
	movq	-32(%rbp), %rax
	movl	(%rax), %edx
	movq	-24(%rbp), %rax
	movl	%edx, (%rax)
	movq	-32(%rbp), %rax
	movl	-4(%rbp), %edx
	movl	%edx, (%rax)
	movl	$.LC0, %esi
	movl	$_ZSt4cout, %edi
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc
	movl	$_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_, %esi
	movq	%rax, %rdi
	call	_ZNSolsEPFRSoS_E
	nop
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1023:
	.size	_Z6myswapIiEvRT_S1_, .-_Z6myswapIiEvRT_S1_
	.section	.text._Z6myswapIcEvRT_S1_,"axG",@progbits,_Z6myswapIcEvRT_S1_,comdat
	.weak	_Z6myswapIcEvRT_S1_
	.type	_Z6myswapIcEvRT_S1_, @function
_Z6myswapIcEvRT_S1_:      //90行
.LFB1024:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$32, %rsp
	movq	%rdi, -24(%rbp)
	movq	%rsi, -32(%rbp)
	movb	$0, -1(%rbp)
	movq	-24(%rbp), %rax
	movzbl	(%rax), %eax
	movb	%al, -1(%rbp)
	movq	-32(%rbp), %rax
	movzbl	(%rax), %edx
	movq	-24(%rbp), %rax
	movb	%dl, (%rax)
	movq	-32(%rbp), %rax
	movzbl	-1(%rbp), %edx
	movb	%dl, (%rax)
	movl	$.LC0, %esi
	movl	$_ZSt4cout, %edi
	call	_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc
	movl	$_ZSt4endlIcSt11char_traitsIcEERSt13basic_ostreamIT_T0_ES6_, %esi
	movq	%rax, %rdi
	call	_ZNSolsEPFRSoS_E
	nop
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1024:
	.size	_Z6myswapIcEvRT_S1_, .-_Z6myswapIcEvRT_S1_
	.text
	.type	_Z41__static_initialization_and_destruction_0ii, @function
_Z41__static_initialization_and_destruction_0ii:
.LFB1033:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$16, %rsp
	movl	%edi, -4(%rbp)
	movl	%esi, -8(%rbp)
	cmpl	$1, -4(%rbp)
	jne	.L8
	cmpl	$65535, -8(%rbp)
	jne	.L8
	movl	$_ZStL8__ioinit, %edi
	call	_ZNSt8ios_base4InitC1Ev
	movl	$__dso_handle, %edx
	movl	$_ZStL8__ioinit, %esi
	movl	$_ZNSt8ios_base4InitD1Ev, %edi
	call	__cxa_atexit
.L8:
	nop
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1033:
	.size	_Z41__static_initialization_and_destruction_0ii, .-_Z41__static_initialization_and_destruction_0ii
	.type	_GLOBAL__sub_I_main, @function
_GLOBAL__sub_I_main:
.LFB1034:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	movl	$65535, %esi
	movl	$1, %edi
	call	_Z41__static_initialization_and_destruction_0ii
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE1034:
	.size	_GLOBAL__sub_I_main, .-_GLOBAL__sub_I_main
	.section	.init_array,"aw"
	.align 8
	.quad	_GLOBAL__sub_I_main
	.hidden	__dso_handle
	.ident	"GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.10) 5.4.0 20160609"
	.section	.note.GNU-stack,"",@progbits
~~~

#### 函数模板机制结论：

- 编译器并不是把函数模板处理成能够处理任意类的函数

- 编译器从函数模板通过具体类型产生不同的函数

- 编译器会对函数模板进行**两次编译**：

  在声明的地方对模板代码本身进行编译,生成简陋的函数模板原形；

  在调用的地方对参数替换后的代码进行编译，产生具体的函数原形。

## 2.类模板

**作用：**

-  类模板用于实现类所需数据的类型参数化 
-  类模板在表示如数组、表、图等数据结构显得特别重要 ， 这些数据结构的表示和算法不受所包含的元素类型的影响

与函数模板不同的是，编译器不能为类模板推断模板参数类型，只能使用显式类型调用

~~~c
//类的类型参数化 抽象的类
//单个类模板
template<typename T>
class A 
{
public:
	A(T t)
	{
		this->t = t;
	}
	T &getT()
	{
		return t;
	}
protected:
public:
	T t;
};
void main()
{
   //模板了中如果使用了构造函数,则遵守以前的类的构造函数的调用规则
	A<int> a(100); 
	a.getT();
	printAA(a);
	return ;
}
~~~

### 2.1 实例化类模板(单个类模板)

一个类模板的每个实例都形成一个独立的类

~~~c
#include<iostream>
using namespace std;

//类模板
template<typename T>
class A {
public:
	A(T b)
	{
		this->a = b;
	}
	void printA() {
		cout << a << endl;
	}
private:
	T a;
};
//类模版作函数参数，c++编译器要求具体的类所以要加A<double>
void printAA(A<double> &a1)
{
    a1.printA();
}
int main() {
    //实例化类模板
	A<double> a(11.32)，b(5.23);//模板类A<double>==>具体类a，b==>定义具体的变量11.32
	a.printA();
    printAA(a);
    printAA(b);
	return 0;
}
~~~

### 2.2 模板类派生

#### 模板类派生普通类

~~~c++
#include<iostream>
using namespace std;

//类模板
template<typename T>
class A {
public:
	A(T b)
	{
		this->a = b;
	}
	void printA() {
		cout << a << endl;
	}
protected:
	T a;
};
//模板类派生时，需要具体化模板类，c++编译器需要知道父类具体数据类型
//要知道父类所占的内存大小，只有数据类型固定下来，才知道如何分配内存
class B : public A<int>
{
public:
	B(int a, int b) : A<int>(a)//注意：必须调用父类的构造函数
	{

		this->b = b;
	}
	void printB()
	{
		cout << "a: " << a << "b: " << b << endl;
	}
private:
	int b;
};
int main() {
	B b1(15, 56);
	b1.printB();
	return 0;
}
~~~

结果：

![10](F:\学习专用\note\pic\10.png)

#### 模板类派生模板类

~~~c++
#include<iostream>
using namespace std;

//类模板
template<typename T>
class A {
public:
	A(T b)
	{
		this->a = b;
	}
	void printA() {
		cout << a << endl;
	}
protected:
	T a;
};
//模板类 派生 模板类
template<typename T>
class C : public A<T>
{
public:
	C(T a, T c) : A<T>(a)
	{
		this->c = c;
	}
	void printC()
	{
		cout << "a: " << a << "b: " << c << endl;
	}
private:
	T c;
};
int main() {
	C<double> c1(1.23,5.63);
	c1.printC();
	return 0;
}
~~~

### 2.3 类内中类模板使用

在类模板自己的作用域中，我们可以直接使用模板名而不提供实参

~~~c++
#include<iostream>
using namespace std;

//类模板函数，所有成员写在内部
template<typename T>
class A {
	//运算符重载的正规写法
	//重载<< >>只能用友元函数，其他运算符重载都携程成员函数
	friend ostream &operator<<(ostream &out, A &a)//重载<<
	{
		out << "a1+a2="<<a.a << endl;
		return out;
	}
public:
	A(T b)
	{
		this->a = b;
	}
	void printA() {
		cout << a << endl;
	}
	A operator+(A &x)//运算符“+”号重载,传入参数为“+”号右边的实参
	{
		A tmp(a + x.a);
		return tmp;
	}
protected:
	T a;
};
int main() {

	A<int> a1(10), a2(20);
	A<int> a3 = a1 + a2;//调用重载+号
	cout << a3;//调用重载<<

	return 0;
}
~~~

结果：

![11](F:\学习专用\note\pic\11.png)

### 2.4 类外使用类模板

不在类的作用域内，直到遇到类名才表示进入类的作用域

~~~c++
#include<iostream>
using namespace std;

//类模板函数，所有成员写在外部
//注意修改形参(引用类的)，返回值（返回为类类型）和域名，需要添加模板参数<T>，缺一不可
template<typename T>
class A {
	//运算符重载的正规写法
	//重载<< >>只能用友元函数，其他运算符重载都携程成员函数
	//注意：类外部实现重载 << ，需要在声明中operator<<后添加<T>，否则报错
	friend ostream &operator<< <T> (ostream &out, A &a);//重载<<
public:
	A(T b);
	void printA();
	A operator-(A &x);//运算符“+”号重载,传入参数为“+”号右边的实参
protected:
	T a;
};
//构造函数的实现 写在类外部
template<typename T>
A<T>::A(T b)
{
	this->a = b;
}
//函数printA的实现
template<typename T>
void A<T>::printA()
{
	cout << a << endl;
}
//运算符“-”号重载的类外实现，返回值和形参均需要添加模板参数<T>
template<typename T>
A<T> A<T>::operator-(A<T> &x)
{
	A tmp(a - x.a);
	return tmp;
}
//友元函数 实现 << 运算符重载
template<typename T>
ostream &operator<<(ostream &out, A<T> &a)//重载<<
{
	out << "a1-a2=" << a.a << endl;
	return out;
}
int main() {

	A<int> a1(10), a2(20);
	A<int> a3 = a1 - a2;//调用重载-号
	cout << a3;//调用重载<<

	return 0;
}
~~~

**结果：**

![12](F:\学习专用\note\pic\12.png)

**注意：**

- **类外部实现重载 << ，需要在声明中operator<<后添加<T>，否则报错**
- **由于类模版中友元函数使用较复杂，所以只用来进行左移，右移操作符的重载**

### 2.5 类外使用类模板，且类模板的声明和实现在不同的.h和.cpp文件中

也就是类模板函数说明和类模板实现分开

//类模板函数

构造函数

普通成员函数

友元函数：用友元函数重载<<>>; 

​                   使用类模板函数需添加类模板实现的头文件即**要包含.cpp**

### 2.6 类模板中的static关键字

- 从类模板实例化的每个模板类有自己的类模板数据成员，该模板类的所有对象共享一个static数据成员
- 和非模板类的static数据成员一样，模板类的static数据成员也应在文件范围定义和初始化
- 每个模板类有自己的类模板的static数据成员副本

分析：

![13](F:\学习专用\note\pic\13.png)

~~~c++
#include<iostream>
using namespace std;

template<typename T>
class A
{
public:
	static T sa;
};
template<typename T>
T A<T>::sa = 0;
int main() {

	A<int> a1,a2,a3;
	a1.sa = 10;
	a2.sa++;
	a3.sa++;
	cout << A<int>::sa << endl;//编译器会将类模版中的static，编译为int类型中的static

	A<char> b1, b2, b3;
	b1.sa = 'a';
	b2.sa++;
	b3.sa++;
	cout << A<char>::sa << endl;//编译器会将类模版中的static，编译为char类型中的static
	return 0;
}
~~~

结果：

![14](F:\学习专用\note\pic\14.png)

### 2.7 类模板案例

编写一个类似vector容器的数组模板类（MyVector），完成对int、char等类型元素的管理

设计：

- 类模版中的成员函数包含：
- 构造函数，初始化对象
- 拷贝构造函数，拷贝对象
- 重载<< , 可以直接打印对象中的元素，将调用内部元素过程写入重载函数中
- 重载=，可以直接进行赋值操作（a2=a1）
- 重载[] ，实现a.m_space[1]与a[1]同一个效果

~~~c++
//MyVector.h
#include<iostream>
using namespace std;

template<typename T>
class MyVector
{
	friend ostream &operator<< <T>(ostream &out, const MyVector &obj);//重载<<
protected:
	int size;
	T *m_space; 

public:
	MyVector(int size);//构造函数

	MyVector(const MyVector &obj);//拷贝构造函数
	~MyVector();//析构函数

	int get_len()
	{
		return size;
	}

	T &operator[](int index);//重载[]
	//a1=a2
	MyVector &operator=(const MyVector &obj);//重载=
};
~~~

~~~c++
//MyVector.cpp
#include<iostream>
#include"MyVector.h"
using namespace std;

template<typename T>
MyVector<T>::MyVector(int size)//构造函数
{
	this->m_space = new T[size];
	this->size = size;
}
//MyVector<int> a2 = a1;
template<typename T>	   
MyVector<T>::MyVector(const MyVector<T> &obj)//拷贝构造函数
{
	//根据a1的大小分配内存
	size = obj.size;
	m_space = new T[size];
	//拷贝数据
	for (int i = 0;i < size;i++)
	{
		m_space[i] = obj.m_space[i];
	}

}
template<typename T>
MyVector<T>::~MyVector()//析构函数
{
	if (m_space != NULL)
	{
		delete[] m_space;
		m_space = NULL;
		size = 0;
	}
}
template<typename T>
T& MyVector<T>::operator[](int index)//重载[]
{
	return m_space[index];
}
//cout<<a1[1]
template<typename T>
ostream &operator<<(ostream &out, const MyVector<T> &obj)//重载<<
{
	for (int i = 0;i < obj.size;i++)
	{
		out << obj.m_space[i] << " ";
	}
	out << endl;
	return out;
}
//a2=a1
template<typename T>	 
MyVector<T> &MyVector<T>::operator=(const MyVector<T> &obj)//重载=
{
	//先把a2的旧内存释放
	if (m_space != NULL)
	{
		delete[] m_space;
		m_space = NULL;
		size = 0;
	}
	//根据a1的大小分配内存
	size = obj.size;
	m_space = new T[size];
	//拷贝数据
	for (int i = 0;i < size;i++)
	{
		m_space[i] = obj.m_space[i];
	}
	return *this;//返回a2本身
}

~~~

~~~c++
//test_MyVector.cpp
#include<iostream>
#include"MyVector.cpp"//注意：添加的头文件为cpp文件
using namespace std;

int main()
{

	MyVector<int> a1(10);//定义int型数组
	for (int i = 0;i < a1.get_len();i++)
	{
		a1[i] = i;//重载[] 案例
	}
	cout << "a1的值为： " << a1 << " ";//重载<< 的案例
	cout << endl;

	MyVector<int> a2 = a1;//拷贝对象的案例
	cout << "拷贝a1后，a2的值为：" << a2 << " ";
	cout << endl;
	MyVector<int> a3(3);
	for (int i = 0;i < a3.get_len();i++)
	{
		a3[i] = i;
	}
	cout << "初始化后a3的值为： " << a3 << endl;
	a3 = a2;//重载= 案例
	cout <<"用a2对a3进行重新赋值后，a3为： "<< a3 << endl;

	MyVector<char> a4(3);//定义char型数组
	a4[0] = 'a';
	a4[1] = 'b';
	a4[2] = 'c';
	cout << "a4中的字符为： "<<a4 << endl;
	return 0;

}
~~~

结果：

![15](F:\学习专用\note\pic\15.png)

## 3.重载与模板

**函数模板和普通函数的区别：**

函数模板不允许自动类型转化，而普通函数可以

**函数模板和普通函数在一起，调用规则：**

- 函数模板可以像普通函数一样被重载
- c++编译器优先考虑普通函数
- 如果函数模板可以产生一个更好的匹配，那么选择模板
- 可以通过空模板实参列表的语法限定编译器只通过模板匹配

例：

~~~c++
#include "iostream"
using namespace std;

int Max(int a, int b)
{
	cout<<"int Max(int a, int b)"<<endl;
	return a > b ? a : b;
}

template<typename T>
T Max(T a, T b)
{
	cout<<"T Max(T a, T b)"<<endl;
	return a > b ? a : b;
}

template<typename T>
T Max(T a, T b, T c)
{
	cout<<"T Max(T a, T b, T c)"<<endl;
	return Max(Max(a, b), c);
}


void main()
{
	int a = 1;
	int b = 2;

	cout<<Max(a, b)<<endl; //当函数模板和普通函数都符合调用时,优先选择普通函数
	cout<<Max<>(a, b)<<endl; //若显示使用函数模板,则使用<> 类型列表

	cout<<Max(3.0, 4.0)<<endl; //如果 函数模板产生更好的匹配 使用函数模板

	cout<<Max(5.0, 6.0, 7.0)<<endl; //重载

	cout<<Max('a', 100)<<endl;  //调用普通函数 可以隐式类型转换 
	system("pause");
	return ;
}
~~~

## 4. 总结

**归纳以上的介绍，可以这样声明和使用类模板：**

1) 先写出一个实际的类。由于其语义明确，含义清楚，一般不会出错。

2) 将此类中准备改变的类型名(如int要改变为float或char)改用一个自己指定的虚拟类型名(如上例中的T)。

3) 在类声明前面加入一行，格式为：

​    template <class 虚拟类型参数>

如：

​    template <class T> //注意本行末尾无分号

​    class Compare

​    {…}; //类体

4) 用类模板定义对象时用以下形式：

​    类模板名<实际类型名> 对象名;

​    类模板名<实际类型名> 对象名(实参表列);

如：

​    Compare<int> cmp;

​    Compare<int> cmp(3,7);

5) 如果在类模板外定义成员函数，应写成类模板形式：

   template <class 虚拟类型参数>

   函数类型 类模板名<虚拟类型参数>::成员函数名(函数形参表列) {…}

**关于类模板的几点说明：**

1) 类模板的类型参数可以有一个或多个，每个类型前面都必须加class，如：

​    template <class T1,class T2>

​    class someclass

​    {…};

在定义对象时分别代入实际的类型名，如：

​    someclass<int,double> obj;

2) 和使用类一样，使用类模板时要注意其作用域，只能在其有效作用域内用它定义对象。

3) 模板可以有层次，一个类模板可以作为基类，派生出派生模板类。

# 八.关联容器

| map      | 关联数组：保存关键字-值对，关键字不可重复        |
| -------- | ------------------------------------------------ |
| set      | 关键字即值，即只保存关键字的容器，关键字不可重复 |
| multimap | 关键字可重复出现的map                            |
| multiset | 关键字可重复出现的set                            |

## 1.关联容器操作

- set中保存的值就是关键字,是const的
- map中，元素是关键字-值对，每个元素是一个pair对象，包含一个关键字和一个关联的值，pair的关键字部分是const的，pair的first成员是一个迭代器，指向具有给定关键字的元素，second成员是一个bool值，指出元素是插入成功还是已经存在于容器中。

### 1.1 关联容器迭代器

对于map

~~~c++
map<string int> count;
auto beg=count.begin();//beg为pair类型，*beg是指向一个pair<const string,size_t>对象的引用
cout<<beg->first;//打印元素的关键字
cout<<beg->second;//打印元素的值
++beg->second;//将值加1
~~~

对于set

~~~c++
set<int> ist={0,1,2,4,5};
auto beg=ist.begin();
if(beg != ist.end())
{
    cout<<*beg++<<endl;//打印关键字
}
~~~

### 1.2 添加元素

对set

~~~c++
vector<int> ict={1,2,3,1,2,3};
set<int> ict2;
ict2.insert(ict.begin(),ict.end());//ict2中只有3个元素
~~~

对map

~~~c++
//map中插入word的四种方法
map<string,size_t> count;
count.insert({word,1});
count.insert(make_pair(word,1));
count.insert(pair<string,size_t>(word,1));
count.insert(map<string,size_t>::value_type(word,1));
~~~

### 1.3 insert返回值

insert的返回值为pair，pair的first成员是一个迭代器，指向具有给定关键字的元素，second成员是一个bool值，指出元素是插入成功还是已经存在于容器中，成功则bool值为true，否则false

~~~c++
map<string,size_t> count;
string word;
while(cin>>word)
{
    auto res=count.insert({word,1});
    if(!res.second)//查看是否插入成功
        ++res.first->second; //不成功，则计数器加1
}
~~~

### 1.4 删除元素

使用erase删除元素，与顺序容器使用一样，通过传递给erase一个迭代器来删除一个元素或一个迭代器对来删除一个范围的元素。

返回值：删除元素的数量，不存在则为0，存在1个则为1，对于multimap可返回2或以上

### 1.5 访问元素

~~~c++
set<int> ist={0,1,2,3,4,5,6};
ist.find(1);//返回一个迭代器，指向key==1的元素，此返回值为1
ist.find(11);//返回0
ist.lower_bound(k);//返回一个迭代器，指向第一个关键字不小于k的元素
ist.upper_bound(k);//返回一个迭代器，指向第一个关键字大于k的元素
ist.count(k)；//返回关键字等于k的元素的数量，不允许重复的容器，返回值为0或1
~~~

# 九.动态内存

## 1.new和delete

~~~c++
//new delete基础
#include<iostream>
using namespace std;

class T
{
private:
	int a;
public:
	T(int x)
	{
		a = x;
	}
	~T()
	{
		cout << a << endl;
	}
};

//1. malloc free  c中的函数
//   new delete   操作符  基本语法
//2. new 基础类型变量 分配数组变量 分配类对象
int main()
{
	//分配基础类型
	int *p = (int *)malloc(sizeof(int));
	*p = 20;
	free(p);
	int *p2 = new int;//分配内存
	*p2 = 30;
	delete p2;
	int *p1 = new int(10);//分配内存并初始化
	cout << *p1 << endl;
	delete p1;
	

	//分配数组类型
	int *p4 = (int *)malloc(sizeof(int)*10);//int array[10]
	p[0] = 2;
	free(p);
	int *p5 = new int[10];
	p5[1] = 9;
	delete [] p5;//注意：数组不要把[] 忘记

	//分配对象  new 能执行类的构造函数 delete能执行类的析构函数
	T *t1 = (T *)malloc(sizeof(T));
	free(t1);
	T *t2 = new T(10);
	delete t2;
	system("pause");
	return 0;
}
~~~

**molloc free和new delete的主要区别：**

- new可以为对象分配内存并初始化（调用构造函数），molloc只能分配内存
- delete会调用类的析构函数，而free不会

# 十. 重载运算

### **基本语法**

- 运算符函数是一种特殊的成员函数或友元函数
- 语法形式：

~~~c
类型 类名：：operator op(参数表)     //例：Test operator+(Test &obj1, Test &obj2)
~~~

- 一个运算符被重载后，原有意义没有失去，只是定义了相对特定类的一个新运算符

**运算符重载的两种方法**：用成员或全局函数重载运算符

区别：成员函数具有this指针，全局函数没有this指针

**二元运算符：**

~~~c
obj1 op obj2
重载为成员函数，解释为obj1.operator op(obj2)
作操作数由obj1通过this指针传递，右操作数由参数obj2传递
//例：  t1 + t2 解释为t1.operator-(t2) 函数为Test operator-(Test &t2)
重载为全局函数，解释为operator op(obj1,obj2)
左右操作数都是由参数传递
//例：  t1 + t2 解释为operator-(t1,t2) 函数为Test operator-(Test &t1，Test &t2)
~~~

**全局函数、类成员函数方法实现运算符重载步骤**
1.写出重载函数的名称operator +、-、*...
2.根据操作数，写出函数参数
3.根据业务，完成函数返回值（看函数是返回引用 指针还是元素）

### 案例（重载+ - ++ -- << >>）

~~~c++
#include<iostream>
using namespace std;

//一般情况下 ，重载操作符 + - ++ --用成员函数重载  << >>只能用全局函数重载，返回值为iostream中的域
//不能用全局函数重载 = () [] ->
//++ -- << >> [] 返回值既可以作左值也可以作右值，需返回一个引用（因为含有当左值的可能）
class Test
{
	friend Test operator+(Test &obj1, Test &obj2);//用友元函数实现私有变量的访问
    friend Test& operator++(Test &obj);
	friend Test operator++(Test &obj, int);
	friend ostream& operator<<(ostream &out, Test &obj);
	friend istream& operator>>(istream &cin, Test &obj);
public:
	Test(int);
	void print()
	{
		cout << "m_a: " << m_a << endl;
	}
	//用成员函数重载操作符 -
	Test operator-(Test &obj)
	{
		Test tmp(this->m_a - obj.m_a);
		return tmp;
	}
	//用成员函数重载操作符 前置--
	Test& operator--()
	{
		this->m_a--;
		return *this;
	}
	//用成员函数重载操作符 后置--
	Test operator--(int)
	{
		Test tmp(this->m_a);
		this->m_a--;
		return tmp;
	}
private:
	int m_a;
};

Test::Test(int a)
{
	this->m_a = a;
}
//用全局函数重载操作符 +
Test operator+(Test &obj1, Test &obj2)//用全局函数重载操作符
{
	Test tmp(obj1.m_a + obj2.m_a);
	return tmp;
}
//用全局函数重载操作符 前置++
Test& operator++(Test &obj)
{
	obj.m_a++;
	return obj;
}
//用全局函数重载操作符 后置++
Test operator++(Test &obj, int)//添加占位符 区分重载前置++
{
	//先使用 再++
	Test tmp = obj;
	obj.m_a++;
	return tmp;
}
ostream& operator<<(ostream &out, Test &obj)
{
	out << obj.m_a << endl;
	return out;
}
istream& operator >> (istream &cin, Test &obj)
{
	cin >> obj.m_a;
	return cin;
}
/*
全局函数、类成员函数方法实现运算符重载步骤
1.写出重载函数的名称operator +、-、*...
2.根据操作数，写出函数参数
3.根据业务，完成函数返回值（看函数是返回引用 指针还是元素）
*/
void main()
{
	Test t1(10), t2(20);
	//用全局函数重载操作符 +
	Test t3 = t1 + t2;//对运算符+ 进行重载，本质是一个函数
	t3.print();
	//用成员函数重载操作符 -
	Test t5(30), t6(20);
	Test t4 = t5 - t6;
	t4.print();
	//用全局函数重载操作符 前置++
	++t1;
	t1.print();
	//用成员函数重载操作符 前置--
	--t2;
	t2.print();
	//用全局函数重载操作符 后置++
	t3++;
	t3.print();
	//用成员函数重载操作符 后置--
	t4--;
	t4.print();
	//用成员函数重载操作符 <<
	cout << t5<<t4;//使用链式输出，返回的是Ostream的引用
	//用成员函数重载操作符 >>
	cin >> t6;
	cout << t6;
	system("pause");
}
~~~

### 重载=操作符

~~~c++
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
using namespace std;

class Test
{
public:
	char *p_str;
	int p_len;
public:
	Test(const char *p)
	{
		p_len = strlen(p);
		p_str = (char *)malloc(p_len + 1);//注意+1 strlen计算的长度不包含 '\0'
		strcpy(p_str, p);
	}
	Test(const Test& obj)//深拷贝，重新为对象分配新的内存空间，上图蓝色线条所示，手动编写拷贝构造函数代码，解决浅拷贝出现的问题
	{
		p_len = obj.p_len;
		p_str = (char *)malloc(p_len + 1);//注意+1 strlen计算的长度不包含 '\0'
		strcpy(p_str, obj.p_str);
	}
	~Test()
	{
		if (p_str != NULL)
		{
			free(p_str);
			p_str = NULL;
			p_len = 0;
		}
	}
	void print()
	{
		cout << p_str << endl;
	}
	/*
	重载=操作符步骤：=操作符，从右往左赋值
	1.先释放旧内存
	2.根据obj分配内存大小
	3.用obj赋值
	4.注意返回值，应为类的引用
	*/

	Test& operator=(Test &obj)
	{
		//先释放旧内存
		if (this->p_str != NULL)
		{
			delete[] p_str;
			this->p_len = 0;
		}
		// 根据obj分配内存大小
		this->p_len = obj.p_len;
		this->p_str = new char[this->p_len+1];//注意需要加1，p_len不包括'\0',字符串中含有'\0'
		//用obj赋值
		strcpy(p_str,obj.p_str);
		return *this;
	}
};

int main() {

	Test p("zxfll");
	p.print();
	Test t1 = p;
	t1.print();
	Test t2("123");
	Test t3("456");
	t2.print();
	t2 = t1 = t3;//等号操作符，默认的为浅拷贝
	t2.print();
	system("pause");
	return 0;
}
~~~

### 案例（重载[] ==）

~~~c++
//MyArray.h
#pragma once
class Array
{
public:
	Array(int len);
	Array(const Array &obj);
	int getData(int index);
	void setData(int index,int data);
	int getLen();
	~Array();
	int& operator[](int index);
	Array& operator=(Array &obj);
	bool operator==(Array &obj);
private:
	int m_len;
	int *m_data;
};
~~~

~~~c++
//MyArray.cpp
#include"MyArray.h"
#include<iostream>

Array::Array(int len)
{
	m_len = len;
	m_data = new int[m_len];
}
Array::Array(const Array &obj)
{
	m_len = obj.m_len;
	m_data = new int[m_len];
	for (int i = 0;i < m_len;i++)
	{
		m_data[i] = obj.m_data[i];
	}
}
int Array::getData(int index)
{
	return m_data[index];
}
void Array::setData(int index, int data)
{
	m_data[index] = data;
}
int Array::getLen()
{
	return m_len;
}
Array::~Array()
{
	if (m_data != NULL)
	{
		delete[] m_data;
		m_len = 0;
	}
}
//重载操作符[]
int& Array::operator[](int index)//函数返回值当左值，需要返回一个引用
{
	return this->m_data[index];
}
//重载操作符=
//释放旧内存，分配新内存，赋值
Array&  Array::operator=(Array &obj)
{
	if (this->m_data != NULL)
	{
		delete[] m_data;
		m_len = 0;
	}
	this->m_len = obj.m_len;
	this->m_data = new int[m_len];
	for (int i=0;i < m_len;i++)
	{
		m_data[i] = obj.m_data[i];
	}
	return *this;
}
//重载操作符==
bool Array::operator==(Array &obj)
{
	if (this->m_len != obj.m_len)
	{
		return false;
	}
	else
	{
		for (int i = 0;i < m_len;i++)
		{
			if (m_data[i] != obj[i])
			{
				return false;
			}
		}
		return true;
	}
}
~~~

~~~c++
//Myarraytest.cpp
#include<iostream>
#include"MyArray.h"
using namespace std;

int main()
{
	Array a1(5);
	for (int i = 0;i < a1.getLen();i++)
	{
		a1[i] = i;//重载操作符[]
	}
	for (int i = 0;i < a1.getLen();i++)
	{
		cout << a1[i] << " ";
	}
	Array a4(5);
	for (int i = a1.getLen();i <= 0;i--)
	{
		a1[i] = i;//重载操作符[]
	}
	Array a2(3),a3(2);
	a2 = a3=a1;//重载操作符=
	for (int i = 0;i < a2.getLen();i++)
	{
		cout << a2[i] << " ";
	}
	for (int i = 0;i < a3.getLen();i++)
	{
		cout << a3[i] << " ";
	}

	//重载操作符==
	if (a1 == a4)
	{
		cout << "a1,a4相等\n" << endl;
	}
	else
	{
		cout << "a1,a4不相等\n" << endl;
	}
	system("pause");
	return 0;
}
~~~

结果：

![32](F:\学习专用\note\pic\32.png)

### 案例（重载()）

~~~C++
#include<iostream>
using namespace std;

class Test
{
public:
	int operator()(int a,int b)
	{
		m_a = a;
		m_b = b;
		return m_a + m_b;
	}
private:
	int m_a;
	int m_b;
};

void main()
{
	Test t1;
	cout << t1(3, 4);//重载()
	system("pause");
}
~~~

案例（字符串类 运算符重载）

~~~c++
//MyString.h
#pragma once
#include<iostream>
using namespace std;
class MyString
{
private:
	char *m_str;
	int m_len;
public:
	MyString();
	MyString(const char *str);
	MyString(const MyString &obj);
	~MyString();
	char* fun()
	{
		return m_str;
	}
public://重载<<
	friend ostream& operator<<(ostream &out, const MyString &obj);
public://重载= == + 
	MyString& operator=(const MyString &obj);
	MyString& operator=(const char *str);
	bool operator==(const MyString &obj) const;
	bool operator==(const char *str) const;
	friend MyString operator+(const MyString &obj1, const MyString &obj2);
public://重载[] <
	char& operator[](int index) const;
	bool operator<(const char *str)const;
};
//cout << m1 << endl;
~~~

~~~
//MyString.cpp
#define _CRT_SECURE_NO_WARNINGS
#include"MyString.h"
#include<iostream>
MyString::MyString(const char *str)
{
	m_len = strlen(str);
	m_str = new char[strlen(str) + 1];//注意：分配内存时，使用的是[] 而不是()
	strcpy(m_str,str);
}
MyString::MyString(const MyString &obj)
{
	m_len = obj.m_len;
	m_str = new char[m_len + 1];
	strcpy(m_str, obj.m_str);
}
MyString::MyString()
{
	m_len = 0;
	m_str = new char[m_len+1];
	strcpy(m_str, "");
	
}
MyString::~MyString()
{
	if (m_str != NULL)
	{
		delete [] m_str;
		m_len = 0;
	}
}
ostream& operator<<(ostream &out, const MyString &obj)
{
	out << obj.m_str;
	return out;
}
MyString& MyString::operator=(const MyString &obj)
{
	if (m_str != NULL)
	{
		delete [] m_str;
		m_len = 0;
	}
	m_len = obj.m_len;
	m_str = new char[m_len + 1];
	strcpy(m_str,obj.m_str);
	return *this;
}
MyString& MyString::operator=(const char  *str)
{
	if (m_str != NULL)
	{
		delete[] m_str;
		m_len = 0;
	}
	if (str == NULL)
	{
		m_len = 0;
		m_str = new char[m_len + 1];
		strcpy(m_str,"");
	}
	else
	{
		m_len = strlen(str);
		m_str = new char[m_len + 1];
		strcpy(m_str,str);
	}
	return *this;
}
bool MyString::operator==(const MyString &obj)const
{
	if (strcmp(this->m_str, obj.m_str)==0)
	{
		return true;
	}
	else
	{
		return false;
	}
}
bool MyString::operator==(const char *str)const
{
	if (str == NULL)
	{
		if (m_len == 0)
			return true;
	}
	else
	{
		if (strcmp(this->m_str, str) == 0)
			return true;
	}
	return false;
}
MyString operator+(const MyString &obj1,const MyString &obj2)
{
	/*int len = m_len + obj.m_len;
	char *str = strcat(m_str,obj.m_str);
	if (m_str != NULL)
	{
		delete[] m_str;
		m_len = 0;
	}
	m_len = len;
	m_str = new char[len+1];
	strcpy(m_str, str);
	return *this;*/
	char *str = strcat(obj1.m_str, obj2.m_str);
	MyString tmp(str);
	return tmp;
}
char& MyString::operator[](int index)const
{
	return m_str[index];
}
bool MyString::operator<(const char *str)const
{
	if (strcmp(m_str, str) < 0)
		return true;
	return false;
}

~~~

~~~c++
//MyString_Test.cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include"MyString.h"
using namespace std;
void main()
{

	MyString m;
	MyString m1("asd"),m3("123");
	cout << m1 << endl;
	MyString m2 = m1;
	cout << m2 << endl;
	m = m1;
	cout << m << endl;
	m = "123";
	cout << m << endl;
	if (m1 == m3)
	{
		cout << "m1==m3" << endl;
	}
	else
	{
		cout << "m1!=m3" << endl;
	}
	if (m1 == "asd2")
	{
		cout << "相等" << endl;
	}
	else
	{
		cout << "不相等" << endl;
	}
	if (m1 < "aaaa")
	{
		cout << "m1<aaaa" << endl;
	}
	else
	{
		cout << "m1>aaaa" << endl;
	}
	cout << m[2] << endl;
	//MyString m4 = m2 + m1;
	cout << m1;	
	system("pause");
}
~~~

**结果：**

![33](F:\学习专用\note\pic\33.png)

### 运算符重载的限制

![31](F:\学习专用\note\pic\31.png)

重载运算符函数可以对运算符作出新的解释，但原有基本语义不变：

- 不改变运算符的优先级
- 不改变运算符的结合性
- 不改变运算符所需要的操作数
- 不能创建新的运算符

### 为什么不要重载&&和||操作符

因为重载&&和||操作符，无法实现短路规则，即从左往右执行

例：

重载&&操作符，t1&&(t1+t2),若t1为假，则不会执行（t1+t2），而实际上是先运行了（t1+t2），不符合短路规则，所以不对&&和||进行重载

