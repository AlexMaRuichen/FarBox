---
Title: C++&STL用法汇总
date: 2016-10-27 16:00
status: public
---

[TOC]

# PreFace

在无数次使用STL翻车之后,我决定抽出时间学习STL及个各种基础C&C++函数,本文兼有学习和总结的双重含义,希望能对我和诸位的学习有所帮助

# Standard Template Library

## Containers

### Vector

一个动态申请空间的数组,所以定义的时候比正常数组少一维

#### header

``` c++
#include <vector>
using namespace std;
```



#### Construction

``` c++
vector <int> v;
```

标准的定义方式
``` c++
vector <int> (5,10) //{10,10,10,10,10......,0}
```
直接给数组的前几个位置赋值

``` c++
int a[]={0,1,2,3,4,5};
vector <int> v1(a+1,a+n+1);
vector <int> v2(v1.begin(),v2.begin());
```
定义时传入两个指针或迭代器

``` c++
vector <int> v1(5,10);
vector <int> v2(v1);
```
直接复制

#### Capacity
size

直接返回vector中元素的个数,注意类型为unsigned int,和int做比较时会出warning(可以无视)

 resize

``` c++
vector <int> v(1000,10);//{10,10,10,10,10,......10}
v.resize(5);//{10,10,10,10,10}
v.resize(10,7);//{10,10,10,10,10,7,7,7,7,7}
v.resize(11);//{10,10,10,10,10,7,7,7,7,7,0}
```

重新定义vector的大小,一般配合unique和distance使用

empty

返回vector是否为空

**注意,对于所有的STL容器,empty的效率都要优于size,请改掉自己原来在条件语句的习惯**

#### Iterators

**由于vector的内存是动态分配的,iterator是有使用期限的,请认真考虑自己使用的迭代器是否有效**

##### Construction

``` c++
vector <int> :: iterator it;
vector <int> :: reverse_iterator rit; 
```

begin

返回vector的头迭代器

end

返回vector的尾迭代器

**注意,所有的STL容器均为左闭右开,所以*v.end()是没有值的**

rbegin

返回vector的**最后一个元素**的迭代器,使用效果与--v.end()

定义时为 reverse_iterator

 rend

返回vector反向的最后一个元素,依然没有值

#### Modifiers

push_back

将元素插入数组尾


insert

在插入是可以通过迭代器指定位置,不过没有push_back常用

clear

清除vector

assign

与定义2,3的用法和效果相同(钦定)

### Heap

priority_queue效率太低且功能较少,请使用heap(默认大根堆),或pb_ds中的优先级队列

#### Header

``` c++
#include <algorithm>
using namespace std;
```

#### make_heap

``` c++
int heap[1010];
int size;
make_heap(heap+1,heap+size+1);
```

将一个数组建立成堆

#### push_heap

``` c++
heap[++size]=x;
push_heap(heap+1,heap+size+1);
```

#### pop_heap

``` c++
pop_heap(heap+1,heap+size--+1);
```

实现方式:将heap[1]与heap[size]交换后调整位置

#### top

``` c++
heap[1];//你要是非用0当开始也可以
```

#### sort_heap

``` c++
sort_heap(heap+1,heap+size+1);
```

将heap(已建堆)中的元素排序

#### greater

``` c++
#include <functional>
using namespace std;

heap[++size]=x;
push_heap(heap+1,heap+size+1,greater<int>());
```

在所有的函数中传入参数greater<int>(),即可构建小根堆

###　Set

Rb_Tree,**注意每个存储的权值都不相同**

#### Header

``` c++
#include <set>
using namespace std;
```

#### Construction

``` c++
set <int> s;
```

标准定义

``` c++
int a[]={1,2,3,4,5};
set <int> s(a,a+5);
```

直接赋值

``` c++
set <int> s1;
set <int> s2(s1);
```

复制

还有一种重载比较函数的方式,需要手写比较结构体,不是很常用

#### Iterators

begin

end

rbegin

rend

使用方式与vector方式相同

**set的迭代器不会失效**

#### Capacity

empty

size

用法和vector相同

#### Modifiers

##### Insert

``` c++
set <int> :: iterator it=s.insert(5).first;
// iterator to 5
if(s.insert(5).second)
// bool
```

insert函数的返回值是一个pair \<iterator,bool\> ,first是指向该元素的迭代器,second是插入是否成功(若该元素在set中存在,插入失败,返回false)

##### Erase

参数为迭代器

##### Clear

清除set

#### Operations

##### find

返回查找元素位置的迭代器

不存在返回end()

##### count

返回查找元素的个数

不是0就是1

##### lower_bound&upper_bound

二分查找,返回第一个大于等于(大于)该元素位置的迭代器

### Multiset

set的姊妹版,支持相同元素

这是STL的黑科技啊,pb_ds的tree都不支持重复元素

具体用法与set相同

**count函数有了实际意义**

### Map

Rb_tree,与set的区别是每个权值都拥有一个键值

#### Header

``` c++
#include <map>
using namespace std;
```

#### Construction

定义方式与set相同

#### Iterators

迭代器与vector相同

#### Modifiers

##### Operator []

``` c++
map <string,int> m;
string s;
++m[s="Hello, world"];
printf("%d\n",m[s]);
```

map支持直接通过operator[ ]访问并修改元素

##### Insert

``` c++
map <string,int> m;
m.insert(pair<string,int>("Hello, world!",1));
map <string,int> m2;
m2.insert(m.begin(),m.end());
```

由于Operator[ ]的存在,insert函数显得没有必要

##### Erase

用法与set相同

**查找的是权值,清除的是键值**

##### Clear

用法与set相同

#### Function

##### find

##### count

##### lower_bound&upper_bound

用法均与set相同

### Mulitmap

map的姊妹版,支持相同权值的不同键值

#### Differences Between Map

由于有相同权值的存在,**取消了operator[],插入只能用insert**

find函数返回的是**第一个**匹配的权值的键值

count函数有了实际意义

取出所有相同权值的方法:lower_bound+upper_bound,返回迭代器之间的元素就是关于查找元素的所有键值

### String

c++中的字符串,**支持直接比较相等,与其他的容器也有较好的适配性**

#### Header

``` c++
#include <string>
using namespace std;
```



#### Construction

``` c++
string s0 ("Initial string");
string s1;
string s2 (s0);
string s3 (s0, 8, 3);
string s4 ("A character sequence");
string s5 ("Another character sequence", 12);
string s6a (10, 'x');
string s6b (10, 42);      // 42 is the ASCII code for '*'
string s7 (s0.begin(), s0.begin()+7);
```

请见代码

#### Operator []

支持直接访问,类型为char

#### Iterators

与vector相同,但几乎不使用

#### Capacity

##### size&length

含义与用法相同,返回string的长度,一般使用length

##### resize

用法与vector::resize相同

##### capacity

返回目前分配给string的长度大小,不是很常用

##### reverse

反转string

#### Modifiers

##### Operator +=

在string后增加后缀,与strcat效果相同

##### push_back

向string末尾添加1个字符

##### assign

钦定,就是区间修改

##### insert

在指定位置插入,用处不是很大

``` c++
string str="to be question";
string str2="the ";
string str3="or not to be";
string::iterator it;

// used in the same order as described above:
str.insert(6,str2);                 // to be (the )question
str.insert(6,str3,3,4);             // to be (not )the question
str.insert(10,"that is cool",8);    // to be not (that is )the question
str.insert(10,"to be ");            // to be not (to be )that is the question
str.insert(15,1,':');               // to be not to be(:) that is the question
it = str.insert(str.begin()+5,','); // to be(,) not to be: that is the question
str.insert (str.end(),3,'.');       // to be, not to be: that is the question(...)
str.insert (it+2,str3.begin(),str3.begin()+3); // (or )
```



##### erase

删除元素,支持单点,区间

``` c++
string str ("This is an example sentence.");
cout << str << '\n';
                                           // "This is an example sentence."
str.erase (10,8);                        //            ^^^^^^^^
cout << str << '\n';
                                           // "This is an sentence."
str.erase (str.begin()+9);               //           ^
cout << str << '\n';
                                           // "This is a sentence."
str.erase (str.begin()+5, str.end()-9);  //       ^^^^^
cout << str << '\n';
                                           // "This sentence."
```

#### Function

##### find

##### rfind

##### find_first_of

##### find_last_of

##### find_first_not_of

##### find_last_not_of

上面这些是干什么的不用我说,可以查找单个字符或string

返回值是下表,**注意,string第一个元素是str[0]**

##### substr

生成子串,很有用

参数:

``` c++
string str="We think in generalities, but we live in details.";
string str2 = str.substr (3,5);     // "think"
size_t pos = str.find("live");      // position of "live" in str
string str3 = str.substr (pos);     // get from "live" to the end
cout << str2 << ' ' << str3 << '\n';
```

### Bitset

通过bit存储bool关系的容器

#### Header

``` c++
#include <bitset>
using namespace std;
bitset <length> bar;
```

#### Construction

``` c++
bitset<16> foo;
bitset<16> bar (0xfa2);
bitset<16> baz (std::string("0101111001"));

foo: 0000000000000000
bar: 0000111110100010
baz: 0000000101111001
```

可以用unsigned long和string初始

#### operator[]

直接访问元素

#### count

返回1的个数

#### test

将该位置的bit值转化为bool

#### set

``` c++
bitset<4> foo;
cout << foo.set();       // 1111
cout << foo.set(2,0);    // 1011
cout << foo.set(2);      // 1111
```

无参:全置为1

1参:将该位置置位1

2参:将改位置置为val(用这个肯定是置0)

### reset

``` c++
bitset<4> foo (std::string("1011"));
cout << foo.reset(1) << '\n';    // 1001
cout << foo.reset() << '\n';     // 0000
```

set的逆运算

### flip

``` c++
bitset<4> foo (std::string("0001"));
cout << foo.flip(2) << '\n';     // 0101
cout << foo.flip() << '\n';      // 1010
```

01反转

### to_string&to_ulong

将bitset转化为string/unsigned long

## Algorithm

### sort

用法大家应该都会

### stable_sort

用法与sort相同,区别在于**有相等元素时不交换,即保证相等元素的相对位置不变**,复杂度最坏$O(N\log^2N)$

感觉用处不大

### unique

去重,但是**去相邻元素的重**,使用前要sort

返回值是一个指向新数组末(开)的指针,用distance取一下长度即可

### distance

计算两个指针(迭代器)间有多少个元素,一般配合unique使用

### next_permutation

生成该数组的下一个排列

标准姿势

``` c++
do
{
  /*code*/
}
while(next_permutation(a+1,a+n+1));
```

### prev_permutation

生成该数组的上一个全排列

不过一般有next_permutation就全都搞定,用处不大

### rotate

``` c++
rotate(a+1,a+pos+1,a+n+1);
```

将pos~n位置的元素前移,之前的元素原序插入尾部

### reverse

反转区间元素

``` c++
reverse(a+1,a+n+1);
```



# __builtin

gcc内建函数(宏),大多与位运算有关,还是相当方便的

## __builtin_ffs(x)

返回x中最后一个1是从后向前的第几位,如

``` c++
__builtin_ffs(2)=2;
```

## __builtin_popcount(x)

返回x中1的个数

## __builtin_ctz(x)

求末尾0的个数(x=0时无定义)

## __builtin_clz(x)

求前导零的个数(x=0时无定义)

## __builtin_parity(x)

x中1的奇偶性(奇1偶0)

# Pb_ds

Policy-Based Data Structures,封装了许多的高效数据结构

## Priority_queue

### Header

``` c++
#include <ext/pb_ds/priority_queue.hpp>
using namespace __gnu_pbds;
```

### Construction

``` c++
__gnu_pbds::priority_queue <Type>;
__gun_pbds::priority_queue <Type,Comp,Tag>
```

Type表示数组类型,Comp表示比较函数(默认less),Tag表示堆的类别

* binary_heap_tag(二叉堆)
* binomial_heap_tag(二项堆)
* rc_binomial_heap_tag(???)
* pairing_heap_tag(配对堆)
* thin_heap_tag(斐波那契堆)

### Differences Between std :: priority_queue

1. 可以使用迭代器遍历
2. 可以修改并删除元素
3. 可并堆

### Functions
#### push

返回值为point operator,不是iterator

#### modify

``` c++
__gnu_pbds::priority_queue <int> :: point iterator it=pq.insert(10);
pq.modify(it,5);
```

#### erase

参数:迭代器

#### join

``` c++
pq.join(priority queue &other);
```

把other合并到\*this,然后清空other

**可并堆!!!!**

## Tree

一个与set类似的数据结构,但功能比set强大

### Header

``` c++
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
```

### Construction

```  c++
using namespace __gnu_pbds;
__gnu_pbds::tree<int,null_mapped_type,less<int>,rb_tree_tag,tree_order_statistics_node_update> 
  		//	                                    splay_tree_tag
```

### Function

#### Find_by_order

``` c++
iterator find_by_order(size_type order)
```

找第oredr+1小的元素的迭代器,没有返回end()

#### Order_by_key

``` c++
size_type order_of_key(const_key_reference r_key)
```

询问有多少个比r_key小的元素

#### Join

``` c++
void join(tree &other)
```

把other的所有元素移动到*this

**注意:要求原来other和*this的值域不能相交,否则会抛出异常**

# Others

## Operator ::

::运算符可以用来区分全局和局部变量

``` c++
#include<iostream>
int sum=5050;
int main()
{
  int arr[3],i;
  cout<<"input 3 num:"　<<endl;
  for(i=0;i<3;i++)
  	cin>>arr[i];
  int sum=0;
  for(i=0;i<3;i++)
  	sum+=arr[i];
  for(i=0;i<3;i++)
  	cout<<setw(4)<<arr[i]　<<endl;
  cout<<"局部sum="<<sum　<<endl;
  ::sum+=sum;
  cout<<"全局sum=";
  cout<<::sum<<endl;//5056
  system("pause");
  return0;//在这里::是一元作用符
}
```

## Stderr

与stdin和stdout类似,stderr是标准错误输出,输出的内容会在屏幕中直接显示(不会缓存),并且重导向stdout后也不影响输出内容

``` c++
fprintf(stderr,"Error!");
```

## Priorities of Operators

优先级从上到下依次递减，最上面具有最高的优先级，逗号操作符具有最低的优先级。

相同优先级中，按结合顺序计算。大多数运算是从左至右计算，只有三个优先级是从右至左结合的，它们是单目运算符、条件运算符、赋值运算符。

基本的优先级需要记住：

指针最优，单目运算优于双目运算。如正负号。

先乘除（模），后加减。

先算术运算，后移位运算，最后位运算。请特别注意：1 << 3 + 2 & 7等价于 (1 << (3 + 2))&7.

逻辑运算最后计算。

**由于编译器不同,不确定的运算符请加括号**

| 运算符                                      | 描述         | 例子                                       | 能否重载 |
| ---------------------------------------- | ---------- | ---------------------------------------- | :--- |
| **Group 1**(no associativity)            |            |                                          |      |
| ::                                       | 域运算符       | Class::age = 2;                          | NO   |
| **Group 2**                              |            |                                          |      |
| ()                                       | 调用函数       | isdigit('1')                             | YES  |
| ()                                       | 初始化成员      | c_tor(int x, int y) : _x(x), _y(y*10){}; | YES  |
| []                                       | 访问数组       | array[4] = 2;                            | YES  |
| ->                                       | 指向结构体成员运算符 | ptr->age = 34;                           | YES  |
| .                                        | 成员运算符      | obj.age = 34;                            | NO   |
| ++                                       | 调用后自加      | for( int i = 0; i < 10; i++ ) cout << i; | YES  |
| --                                       | 调用后自减      | for( int i = 10; i > 0; i-- ) cout << i; | YES  |
| **Group 3**(right-to-left associativity) |            |                                          |      |
| !                                        | 逻辑非        | if( !done ) …                            | YES  |
| not                                      | 逻辑非        |                                          |      |
| ~                                        | 按位补码       | flags = ~flags;                          | YES  |
| compl                                    | 按位补码       |                                          |      |
| ++                                       | 自加后调用      | for( i = 0; i < 10; ++i ) cout << i;     | YES  |
| --                                       | 自减后调用      | for( i = 10; i > 0; --i ) cout << i;     | YES  |
| -                                        | 负号         | int i = -1;                              | YES  |
| +                                        | 正号         | int i = +1;                              | YES  |
| *                                        | 复引用运算符     | int data = *intPtr;                      | YES  |
| &                                        | 取地址        | int *intPtr = &data;                     | YES  |
| new                                      | 动态分配内存     | long *pVar = new long;MyClass *ptr = new MyClass(args); | YES  |
| new []                                   | 动态分配内存(数组) | long *array = new long[n];               | YES  |
| delete                                   | 释放内存       | delete pVar;                             | YES  |
| delete []                                | 释放内存(数组)   | delete [] array;                         | YES  |
| (type)                                   | 类型转换       | int i = (int) floatNum;                  | YES  |
| sizeof                                   | 返回大小(字节)   | int size = sizeof floatNum;int size = sizeof(float); | NO   |
| **Group 4**                              |            |                                          |      |
| ->*                                      | 成员指针选择符    | ptr->*var = 24;                          | YES  |
| .*                                       | 成员函数指针     | obj.*var = 24;                           | NO   |
| **Group 5**                              |            |                                          |      |
| *                                        | 乘          | int i = 2 * 4;                           | YES  |
| /                                        | 除          | float f = 10.0 / 3.0;                    | YES  |
| %                                        | 膜          | int rem = 4 % 3;                         | YES  |
| **Group 6**                              |            |                                          |      |
| +                                        | 加          | int i = 2 + 3;                           | YES  |
| -                                        | 减          | int i = 5 - 1;                           | YES  |
| **Group 7**                              |            |                                          |      |
| <<                                       | 位左移        | int flags = 33 << 1;                     | YES  |
| >>                                       | 位右移        | int flags = 33 >> 1;                     | YES  |
| **Group 8**                              |            |                                          |      |
| <                                        | 小于         | if( i < 42 ) …                           | YES  |
| <=                                       | 小于等于       | if( i <= 42 ) ...                        | YES  |
| >                                        | 大于         | if( i > 42 ) …                           | YES  |
| >=                                       | 大于等于       | if( i >= 42 ) ...                        | YES  |
| **Group 9**                              |            |                                          |      |
| ==                                       | 等于         | if( i == 42 ) ...                        | YES  |
| eq                                       | 等于         |                                          |      |
| !=                                       | 不等于        | if( i != 42 ) …                          | YES  |
| not_eq                                   | 不等于        |                                          |      |
| **Group 10**                             |            |                                          |      |
| &                                        | 位与         | flags = flags & 42;                      | YES  |
| bitand                                   | 位与         |                                          |      |
| **Group 11**                             |            |                                          |      |
| ^                                        | 位异或        | flags = flags ^ 42;                      | YES  |
| xor                                      | 位异或        |                                          |      |
| **Group 12**                             |            |                                          |      |
| \|                                       | 位或         | flags = flags \| 42;                     | YES  |
| bitor                                    | 位或         |                                          |      |
| **Group 13**                             |            |                                          |      |
| &&                                       | 逻辑与        | if( conditionA && conditionB ) …         | YES  |
| and                                      |            |                                          |      |
| **Group 14**                             |            |                                          |      |
| \|\|                                     | 逻辑或        | if( conditionA \|\| conditionB ) ...     | YES  |
| or                                       | 逻辑或        |                                          |      |
| **Group 15**(right-to-left associativity) |            |                                          |      |
| ? :                                      | 三目运算符      | int i = (a > b) ? a : b;                 | NO   |
| **Group 16**(right-to-left associativity) |            |                                          |      |
| =                                        | 赋值运算符      | int a = b;                               | YES  |
| +=                                       |            | a += 3;                                  | YES  |
| -=                                       |            | b -= 4;                                  | YES  |
| *=                                       |            | a *= 5;                                  | YES  |
| /=                                       |            | a /= 2;                                  | YES  |
| %=                                       |            | a %= 3;                                  | YES  |
| &=                                       |            | flags &= new_flags;                      | YES  |
| and_eq                                   |            |                                          |      |
| ^=                                       |            | flags ^= new_flags;                      | YES  |
| xor_eq                                   |            |                                          |      |
| \|=                                      |            | flags \|= new_flags;                     | YES  |
| or_eq                                    |            |                                          |      |
| <<=                                      |            | flags <<= 2;                             | YES  |
| >>=                                      |            | flags >>= 2;                             | YES  |
| **Group 17**                             |            |                                          |      |
| throw                                    |            | throw EClass(“Message”);                 | NO   |
| **Group 18**                             |            |                                          |      |
| ,                                        | 逗号运算符      | for( i = 0, j = 0; i < 10; i++, j++ ) …  | YES  |