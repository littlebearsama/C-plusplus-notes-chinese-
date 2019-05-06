# 15友元、异常和其他
## 友元类
例子：模拟电视机和遥控器的简单程序
> 公有继承is-a关系并不适用。**遥控器可以改变电视机的状态，这表明应将Remove类作为TV类的一个友元**
* 友元声明 friend class Remote；--->友元声明可以位于公有、私有或保护部分，其所在的位置无关紧要。该声明让整个类成为友元并不需要前向（实现）声明，因为友元语句本身已经指出Remote是一个类。
* **友元Remove可以使用TV类的所有成员**
* 大多类方法都被定义为内联。代码中，除构造函数外，所有Remove方法都将一个TV对象引用作为参数，这表明遥控器必须针对特定的电视机
* 同一个遥控器可以控制不同的电视机
```C++
TV S42；
TV S58(TV::ON);
Remote grey;
grey.set_chan(S42,10);
grey.set_chan(S58,28);
```
## 友元成员函数
* 某一个类的成员函数作为另外一个类的友元函数
> 例子：将TV成员中Remote方法Remote::set_chan()，成为另外一个类的成员
```C++
class TV
{
friend void Remote::set_chan(TV& t,int c);
...
};
```
> * 问题1：在编译器在TV类声明中看到Remote的一个方法被声明为TV类的友元之前，应先看到Remote类的声明和set_chan()方法的声明。
```C++
//排列次序应如下：
class TV;//forward declaration
class Remote{...};
class TV{...};
```
> * 问题2：Remote声明包含内联代码，例如：
`void onoff(TV & t){t.onoff();}`
> 由于这将调用TV的一个方法，所以编译器此时必须看到一个TV的类声明，解决：**使Remote声明中只包含方法声明，并将实际的定义放在TV类之后**
```C++
#include<iostream>
   

class B
{
public :
    B()
    {
        myValue=2;
        std::cout<<"B init"<<std::endl;
    }
    ~B()
    {
        std::cout<<"B end"<<std::endl;
    }
    
    //这样做可以
    /*B operator+(const B av)
    {
        B a;
        a.myValue=myValue+av.myValue;
        return a;
    }*/
    //也可以这么做
    friend B operator+(const B b1,const B b2);

    //------------------------------------------------
    int GetMyValue()
    {
        return myValue;
    }
    //重载<<
    friend std::ostream& operator<<(std::ostream& os,B);
private :
    int myValue;
};

B operator+(const B b1,const B b2)
{
    B a;
    a.myValue=b1.myValue+b2.myValue;
    return a;
}
std::ostream& operator<<(std::ostream& os,B b)
{
    return os<<"重载实现："<<b.myValue<<std::endl;
}
int main()
{
    B b1;
    std::cout<<b1;
    B b2;
    B b3=b1+b2;
    std::cout<<b3<<std::endl;
    std::cin.get();
    return 0;
}
```
* 内联函数的链接性是内部的，这意味着函数定义必须在使用函数的文件中，这个例子中内联定义位于头文件中，因此在使用函数的文件中包含头文件可确保将定义放在正确的地方。这可以将定义放在实现文件中，**但必须删除关键字inline**这样函数的链接性将是外部的

## 嵌套类
* 在另外一个类中声明的类被称为嵌套类（nested class）
* 包含类的成员函数可以创建和使用被嵌套的对象。而仅当声明位于公有部分，才能在包含类外面使用嵌套类，而且必须使用作用域解析运算符
* 访问权限：嵌套类、结构和美剧的作用域特征（三者相同）

| 声明位置 | 包含它的类是否可以使用它 | 从包含它的类派生而来的类是否可以使用它 |在外部是否可以使用 |
| ------ | ------ | ------ |------ |
| 私有部分 | 是 | 否 | 否 |
| 保护部分 | 是 | 是 | 否 |
| 公有部分 | 是 | 是 | 是，可以通过类限定符来使用 |
* 访问控制
> * 1.类声明的位置决定了类的作用域或可见性
> * 2.类可见后，访问控制规则（公有，保护，私有，友元）将决定程序对嵌套类成员的访问权限。
```C++
//在下面的程序中，我们创建了一个模板类用于实现Queue容器的部分功能，并且在模板类中潜逃使用了一个Node类。
// queuetp.h -- queue template with a nested class
#ifndef QUEUETP_H_
#define QUEUETP_H_

template <class Item>
class QueueTP
{
private:
    enum {Q_SIZE = 10};
    // Node is a nested class definition
    class Node
    {
    public:
        Item item;
        Node * next;
        Node(const Item & i) : item(i), next(0) {}
    };
    Node * front;       // pointer to front of Queue
    Node * rear;        // pointer to rear of Queue
    int items;          // current number of items in Queue
    const int qsize;    // maximum number of items in Queue
    QueueTP(const QueueTP & q) : qsize(0) {}
    QueueTP & operator=(const QueueTP & q) { return *this; }
public:
    QueueTP(int qs = Q_SIZE);
    ~QueueTP();
    bool isempty() const
    {
        return items == 0;
    }
    bool isfull() const
    {
        return items == qsize;
    }
    int queuecount() const
    {
        return items;
    }
    bool enqueue(const Item &item); // add item to end
    bool dequeue(Item &item);       // remove item from front
};
// QueueTP methods
template <class Item>
QueueTP<Item>::QueueTP(int qs) : qsize(qs)
{
    front = rear = 0;
    items = 0;
}

template <class Item>
QueueTP<Item>::~QueueTP()
{
    Node * temp;
    while (front != 0)      // while queue is not yet empty
    {
        temp = front;
        front = front->next;
        delete temp;
    }
}

// Add item to queue
template <class Item>
bool QueueTP<Item>::enqueue(const Item & item)
{
    if (isfull())
        return false;
    Node * add = new Node(item);    // create node
    // on failure, new throws std::bad_alloc exception
    items ++;
    if (front == 0)             // if queue is empty
        front = add;            // place item at front
    else
        rear->next = add;       // else place at rear
    rear = add;
    return true;
}

// Place front item into item variable and remove from queue
template <class Item>
bool QueueTP<Item>::dequeue(Item & item)
{
    if (front == 0)
        return false;
    item = front->item;         // set item to first item in queue
    items --;
    Node * temp = front;        // save location of first item
    front = front->next;        // reset front to next item
    delete temp;                // delete former first item
    if (items == 0)
        rear = 0;
    return true;
}

#endif // QUEUETP_H_
```


## 异常
* 意外情况
> 1.程序可能会试图打开一个不可用的文件
> 2.请求过多内存
> 3.遭遇不能容忍的值
### 1.调用abort()--原型在cstdlib（或stdlib.h）中
* 其典型实现是向标准错误流（即cerr使用的错误流）发送信息abnormalprogram termination（程序异常中止），然后终止程序。它返回一个随实现而异的值，告诉操作系统，处理失败。
* 调用abort()将直接终止程序（调用时，不进行任何清理工作）
* 使用方法：1.判断触发异常的条件 2.满足条件时调用abort()
> * 1.exit():
在调用时，会做大部分清理工作，但是决不会销毁局部对象，因为没有stack unwinding。
会进行的清理工作包括：销毁所有static和global对象，清空所有缓冲区，关闭所有I／O通道。终止前会调用经由atexit()登录的函数，atexit如果抛出异常，则调用terminate()。

> * 2.abort():调用时，不进行任何清理工作。直接终止程序。

> * 3.retrun:调用时，进行stack unwinding，调用局部对象析构函数，清理局部对象。如果在main中，则之后再交由系统调用exit()。

* return返回，可析构main或函数中的局部变量，尤其要注意局部对象，如不析构可能造成内存泄露。exit返回不析构main或函数中的局部变量，但执行收工函数，
故可析构全局变量（对象）。abort不析构main或函数中的局部变量，也不执行收工函数，故全局和局部对象都不析构。
  **所以，用return更能避免内存泄露，在C++中用abort和exit都不是好习惯。**

### 2.返回错误代码
一种比异常终止更灵活的方法是，使用函数的返回值来指出问题
### 3.异常机制
* C++异常是对程序运行过程中发生的异常情况（例如被0除）的一种响应。异常提供了将控制权从程序的一个部分传递到另外一部分的途径
* 异常机制由三个部分组成
#### 1.引发异常
```C++
double hmean(double a,double b)
{
if(a==-b)
throw "bad heam() arguments:a=-b not allowed";//throw关键字表示引发异常（实际上是跳转）
return 2.0*a*b/(a+b);
}
```
#### 2.使用异常处理程序（exception handler）来捕获异常
#### 3.使用try块：try块标识其中特定异常可能会被激活的代码，它后面跟一个或多个的catch块
* 例子：
```C++
#include <iostream>
 
using std::cout;
using std::cin;
using std::cerr;
 
int fun(int & a, int & b)
{
if(b == 0)
{
	throw "hello there have zero sorry\n"; //引发异常
}
return a / b;
}
 
int main()
{
	int a;
	int b;
	while(true)
	{
	cin >> a;
	cin >> b;
	
	try //try里面是可能引发异常代码块
	{
	cout << " a / b = "<< fun(a,b) << "\n";
	}
	catch(const char *str)  接收异常,处理异常
	{
		cout << str;
	cerr <<"除数为0\n"; //cerr不会到输出缓冲中 这样在紧急情况下也可以使用
	}
	}
	system("pause");
	return 1;
}
```
> 1.try:try块标识符其中特定的异常可能被激活的代码块,他后面跟一个或者多个catch块.

> 2.catch:类似于函数定义,但并不是函数定义,关键字catch表明这是给一个处理程序,里面的`const cahr* str`会接受throw传过来错误信息.

> 3.throw:抛出异常信息,类似于执行返回语句,因为它将终止函数的执行,但是它不是将控制权交给调用程序,而是导致程序沿着函数调用序列后退,知道找到包含try块的函数.

注意：

1.如果程序在try块外面调用fun(),将无法处理异常。

2.throw出的异常类型可以是**字符串，或其他C++类型：通常为类类型**

3.执行throw语句类似于执行返回语句，因为它也将终止函数的执行。

4.执行完try中的语句后，如果没有引发任何异常，则程序跳过try块后面的catch块，直接执行后面的第一条语句。

5.如果函数引发了异常，而没有try块或没有匹配处理程序时，将会发生什么情况。**在默认情况下，程序最终调用abort()函数！**


### 4.将对象用作异常类型 P622
### 5.栈解开（栈解退）stack unwind
* C++如何处理函数调用和返回的？

1.程序将**调用函数的地址（返回地址）**放入到栈中。当被调用的函数执行完毕后，程序将使用该地址来确定从哪里开始执行。

2.函数调用将**函数参数**放入到栈中。**在栈中，这些函数参数被视为自动变量。如果被调用的函数创建的自动变量，则这些自动变量也将被添加到栈中**

3.如果被调用的函数调用了另外一个函数，则后者的信息将被添加到栈中，依此类推。

* 假设函数出现异常（而不是返回）而终止，则程序也将释放栈中的内存，但不会释放栈中的第一个地址后停止，而是继续释放，直到找到一个位于try块中的返回地址。随后，控制权将转到块尾的异常处理程序，而不是函数调用后的第一条语句，这个过程被称为栈解退。

## exception类（头文件exception.h/except.h第一了exception类）**C++可以把它用作其他异常类的基类**
* 1.stdexcept 异常类（头文件stdexcept定义了其他几个异常类。）
> * 该文件定义了`1.logic_error类 2.runtime_error类`他们都是以公有的方式从exception派生而来的。
> * 1.logic_error类错误类型（domain_error、invalid_argument、length_error、out_of_bounds）。每个类都有一个类似于logic_error的构造函数，让您能够提供一个供方法what()返回的字符串。
> * 2.runtime_error类错误类型（range_error、overflow_error、underflow_error）。每个类都有一个类似于runtime_error的构造函数，让您能够提供一个供方法what()返回的字符串。

* 2.bad-alloc异常和new(头文件new)

对于使用new导致的内存分配问题，C++的最新处理方式是让new引发bad_alloc异常。头文件new包含bad_alloc类的生命，他是从exception类公有派生而来的。但在以前，当无法分配请求的内存量时，new返回一个空指针。

* 3.异常类和继承

1.可以像标准C++库所做的那样，从一个异常类派生出另外一个。

2.可以在类定义中嵌套异常类声明类组合异常。

3.这种嵌套声明本身可被继承，还可用作基类。

## RTTI(运行阶段类型识别)(Run-Time Type Identification)
* 旨在为程序运行阶段确定对象的类型提供一种标准方式

**RTTI的工作原理**

C++有三个支持RTTI的元素

> 1.如果可能的话，**dynamic_cast运算符**将使用一个指向**基类的指针**来生成**指向派生类**的指针；否则，该运算符返回0---空指针。

> 2.typeid运算符返回指出对象类型的值

> 3.type_info结构存储了有关特定类型的信息。

* 1.dynamic_cast运算符是最常用的RTTI组件

他不能回答“指针指向的是哪类对象”这样的问题，但能回答“是否可以安全地将对象的地址赋给特定类型的指针”这样的问题

用法：`Superb* pm = dynamic_cast<Super*>(pg);`其中pg指向一个对象

提出这样的问题：指针pg类型是否可被安全地转换为Super* ?如果可以返回对象地址，否则返回一个空指针。

* 2.typeid运算符和type_info类。

typeid运算符使得能够确定两个对象是否为同类型,使用：如果pg指向的是一个Magnification对象，则下述表达式的结果为bool值true，否则为false；
```C++
typeid(Magnification)==typeid(*pg)

type_info类的实现岁厂商而异，但包含一个name()成员，该函数返回一个随实现而异的字符串；通常（但并非一定）是类的名称。例如下面的语句想爱你是指针pg指向的对象所属的类定义的字符串：
​```C++
cout<<"Now Processing type"<<typeid(*pg).name()<<".\n";
```

## 类型转换运算符
 4个类型转换运算符:`dynamic_cast\const_cast\static_cast\reinterpret_cast`

 1.dynamic_cast<type_name>(expression)
 * 该运算符的用途是，使得能够在类层次结构中进行向上转换（由于is-a关系，这样的类型转换是安全的），不允许其他转换。

 2.const_cast<type_name>(expression)
 * 该运算符用于执行只有一种用途的类型转换，即改变之const或volatile其语法与dynamic_cast运算符相同。

 3.static_cast<type_name>(expression)


 4.reinterpret_cast<type_name>(expression)
 * 用于天生危险的类型转换。
