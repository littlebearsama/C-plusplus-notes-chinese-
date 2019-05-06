# 14C++中的代码重用（公有继承，包含对象的类，私有继承，多重继承，类模板）
## 包含（containment）：包含对象成员的类
本身是另外一个类的对象。这种方法称为包含（containment），组合（composition），或层次化（laying）
## 私有继承（还是has-a关系）
基类的公有成员和保护成员都将成为派生类的私有成员。和公有继承一样，基类的私有成员是会被派生类继承但是不能被派生类访问。基类方法将不会成为派生类对象公有接口的一部分，但可以在派生类中使用它们。
* 1.初始化基类组件
>和包含不同，对于继承类的新版本的构造函数将使用成员初始化列表语法，它使用类名（std::string，std::valarry）而不是成员名来表示构造函数
```C++
//Student类私有继承两个类派生而来,本来包含的时候两个基类分别是name和score
class Student:private std::string,private std::valarry<double>
{
public:
......
};
//如果是包含的构造函数
Student(const char *str,const double *pd,int n):name(str),score(pd,n)
{
}
//继承类的构造函数 
Student(const char *str,const double *pd,int n):std::string(str),std::valarry<double>(pd,n)
{
}
```
* 2.访问基类的方法
> a.包含书用对象（**对象名**）来调用方法

> b.私有继承时，**将使用类名和作用域解析运算符来调用方法**
```C++
double Student::Average() const
{
if(ArrayDb::size()>0）//ArrayDb typedef为std::valarry<double>
  return ArrayDb::sum()/ArrayDb::size();
else
  return 0;
}
```
* 3.访问基类对象

使用私有继承时，该string对象没有名称。**那么，student类的代码如何访问内部string对象呢？** **强制类型转换!**
> **本来**子到父自动类型提升,不需要强制类型转换。父到子才需要强制类型转换。**但是**下面是强制类型转换，原因在第4点那里写着。

> 由于Student类是从string类派生而来的，因此可以通过强制类型转换，将Student对象转换为S=string对象
```C++
//成员方法：打印出学生的名字
//因为不是包含，只能通过强制类型转换
const string & Student::Name()const
{
retrun (const string &) *this;
}  
```
* 4.访问基类友的元函数

用类名显式地限定函数名不适合友元函数，**因为友元不属于类**。不能通过这种方法访问基类。

解决：**通过显示地转换为基类来调用正确的函数**
```C++
osstream & operator<<(ostream & os,const Student & stu)
{
os << "Score for "<<(const String &) stu <<":\n";//显式地将stu转换为string对象引用，进而调用基类方法
}
```
**引用不会自动转换为string引用**
原因：
> * a.在私有继承中，未进行显示类型转换的派生类引用或指针，无法赋值给基类的引用或指针。
> * b.即使这个例子使用的是公有继承，也必须使用显示类型转换。原因之一是，如果不使用类型转换，下述代码将无法与函数原型匹配从而导致递归调用，os<<stu
> * c.由于这个类使用的是多重继承，编译器将无法确定应转换成哪个基类，如果两个基类都提供函数operator<<()。

* 5.使用包含还是私有继承？
通常，应使用包含来建立has-a关系；如果新需要访问原有的保护成员，或重新定义虚函数，则应使用私有继承。
* 6.保护继承
>* 基类的公有成员和保护成员都将成为派生类的保护成员。
>* 共同点：和私有继承一样，基类的接口在派生类中也是可用的，但在继承和结构之外是不可用的。
>* 区别：使用私有继承时，第三代类将不能使用基类的接口，这是因为公有方法在派生类中将变成私有方法；使用保护继承时，基类的公有方法在第二代将变成保护的，因此第三代派生类可以使用它们。

| 特征 | 公有继承 | 保护继承 |私有继承 |
| ------ | ------ | ------ |------ |
| 公有成员变成 | 派生类的公有成员 | 派生类的保护成员 |派生类的私有成员 |
| 保护成员变成 | 派生类的保护成员 | 派生类的保护成员 |派生类的私有成员 |
| 私有成员变成 | 只能通过基类接口访问 |只能通过基类接口访问|只能通过基类接口访问 |
| 能否隐式向上转换 | 是 | 是（但只能在派生类中） |否 |

* 7.使用using重新定义访问权限
使用派生或私有派生时，基类的公有成员将成为保护成员或私有成员，假设要让基类方法在派生类外面可用
> * 方法1，定义一个使用该基类方法的派生类方法
```C++
double Student::sum() const
{
return std::valarray<double>::sum();
}
```
> * 方法2，将函数调用包装在另外一个函数调用中，即使用一个using声明（就像空间名称一样）
```
class Student::private std::string,private std::valarray<double>
{
...
public:
  using std::valarray<double>::min;
  using std::valarray<double>::max;
}
```
//using声明只适用于继承，而不适用于包含
//using声明只使用成员名---没有圆括号，函数特征表和返回类型
## 多重继承
必须使用关键字public来限定每一个基类，这是因为，除非特别指出，否则编译器将认为是私有派生。（class 默认访问类型是私有，strcut默认访问类型是公有）
### 多重继承带来的两个主要问题：
* 1.从两个**不同的基类**继承**同名方法**。
* 2.从两个或更多相关的基类那里继承**同一个类的多个实例**。
```C++
class Singer:public Worker{...};
class Waiter:public Worker{...};
class SingerWaiter:public Singer,public Waiter{...};
```
* Singer和Waiter都继承一个Worker组件，因此SingerWaiter将包含**两份Worker的拷贝**-->通常可以将派生来对象的地址赋给基类指针，但是现在将出现二义性。（基类指针调用基类方法时不知道调用哪个基类方法），第二个问题：比如worker类中有一个对象成员，那么就会出现

### 虚基类（virtual base class）
* 虚基类使得从多个类（他们的基类相同）派生出的对象只继承一个基类对象。
```C++
class Singer:virtual public Worker{...};//virtual可以和public调换位置
class Waiter:public virtual Worker{...;
//然后将SingingWaiter定义为
class SingingWaiter：public Singer,public Waiter{...};
```
> 现在,SingingWaiter对象只包含Worker对象的一个副本
### 为什么不抛弃将基类声明为虚的这种方式，使虚行为成为MI的准则呢？（为什么不讲虚行为设为默认，而要手动设置）
> * 第一，一些情况下，可能需要基类的多个拷贝；
> * 第二，将基类作为虚的要求程序完成额外的计算，为不需要的工具付出代价是不应当的；
> * 第三，这样做是有缺点的，**为了使虚基类能够工作，需要对C++规则进行调整，必须以不同的方式编写一些代码。另外，使用虚基类还可能需要修改已有的代码**
### 虚基类的构造函数（需要修改）
> * 对于非虚基类，唯一可以出现在初始化列表的构造函数是即是基类构造函数。
> * 对于虚基类，需要对类构造函数采用一种新的方法。
* **基类是虚的时候,禁止信息通过中间类自动传递给基类**,因此向下面构造函数将初始化成员panache和voice，但wk参数中的信息将不会传递给子对象Waiter。然而，**编译器必须在构造派生对象之前构造基类对象组件；**在下面情况下，编译器将使用Worker的默认构造函数（**即类型为Worker的参数没有用！而且调用了Worker的默认构造函数**）
```C++
SingingWaiter(const Worker &wk,int p=0;int v=Singer:other):Waiter(wk,p),Singer(wk,v){}//flawed
```
* 如果不希望**默认构造函数来构造虚基类对象**，则需要显式地调用所需的基类构造函数。
```C++
SingingWaiter(const Worker &wk,int p=0;int v=Singer:other):Worker(wk),Waiter(wk,p),Singer(wk,v){}
```
* 上述代码将**显式地调用构造函数**worker(const Worker&)。请注意，这种调用是合法的，**对于虚基类，必须这样做；但对于非虚基类，则是非法的。**
### 有关MI的问题
* 多重继承可能导致函数调用的二义性。
> 假如每个祖先（Singer，waiter）都有Show()函数。那么如何调用
>
> * 1.可以使用作用域解析符来澄清编程者的意图：
```C++
SingingWaiter newhire("Elise Hawks",2005,6,soprano);
newhire.Singer::Show();//using Singer Version
```
> * 2.然而，更好的方法是在SingingWaiter中重新定义Show(),并指出要使用哪个show。
```
P559～P560
```
#### 1.混合使用虚基类和非虚基类
* 如果基类是虚基类，派生类将包含基类的一个子对象；
* 如果基类不是虚基类，派生类将包含多个子对象
* 当虚基类和非虚基类混合是，情况将如何呢？
```C++
//有下面情况
class C:virtual public B{...};//B为虚基类
class D:virtual public B{...};//B为虚基类
class X: public B{...};       //B为非虚基类
class Y: public B{...};       //B为非虚基类
class M:public C,public D,public X,public Y{...};
```
> * 这种情况下，类M从虚派生祖先C和D那里**共继承了一个B类子对象**，并从每一个非虚派生祖先X和Y**分别继承了一个B类子对象**。因此它(M)包含**三个**B类子对象。
> * 当类通过多条虚途径和非虚途径继承了某个特定的基类时，该类包含**一个**表示所有的虚途径的基类子对象**和**分别表示各条非虚途径的多个基类子对象。(本例子中是**1+2=3**)
#### 2.虚基类和支配(使用虚基类将改变C++解释二义性的方式)
* 使用非虚基类是，规则很简单，如果类从不同的类那里继承了两个或更多的同名函数（数据或方法），则使用该成员名是，如果没有用类名进行限定，将导致二义性。
* 但如果使用的是虚基类，则这样做不一定会导致二义性。这种情况下，如果某个名称**优先于（dominates）**其他所有名称，则使用它时，即使不使用限定符，也不会导致二义性。
```C++
class B
{
public:
short q();
...
};

class C:virtual public B
{
public:
long q();
int omg();
...
};

class D:public C
{
...
}

class E:virtual public B
{
private:
int omg();
...
};

class F: public D,public E
{
...
};
```
> * 1.类C中的q()定义优先于类B中的q()定义，**因为类C是从类B派生而来的**。因此F中的方法可以使用q()来表示C::q().（**父子类之间有优先级，子类大于父类**）
> * 2.任何一个omg()定义都不优先于其他omg()定义，因为**C和E都不是对方的基类**。所以，在F中使用非限定的omg()将导致二义性。
> * 3.虚二义性规则与访问规则（pravite,public,protected）无关，**也就是说即使E::omg是私有的，不能在F类中直接访问，但使用omg()仍将导致二义性。**


## 类模板
### 类模板
* 类模板和模板函数都是以template开头（当然也可以使用class），后跟类型参数；类型参数不能为空，多个类型参数用逗号隔开。
```C++
template <typename 类型参数1，typename 类型参数2，typename 类型参数3>class 类名
{
//TODO
}
```
* 类模板中定义的类型参数可以用在**函数声明和函数定义中**，
* 类模板中定义的类型参数可以用在**类型类声明和类实现中**，
* 类模板的目的同样是将数据的类型参数化。
```C++
template <class Type>
class Stack
{
private:
        enum {MAX=10};
        Type items[MAX];
        int top;
public:
        Stack();
        ……
}
template <class Type>
Stack<Type>::Stack()
{
        top=0;
}
```
> * **Type**:泛型标识符，这里的type被称为类型参数。**这意味着它们类似于变量，但赋给它们的不是数字，而只能是类型**
> * 相比于函数模板，类模板必须显式的提供所需的类型。
* 模板不是函数，它们不能单独编译。模板必须与特定的**模板实例化(instantiation)**请求一起使用,为此，最简单的方法是将所有模板信息放在一个文件中，并在要使用这些模板的文件中包含该头文件。
```C++
//类声明Stack<int>将使用int替换模板中所有的Type
Stack<int>kernels;
Stack<string>colonels;
```
### 深入探讨模板
### 模板具体化（instantiation）和实例化（specialization）
> 模板以泛型的方式描述类，而具体化是使用具体的类型生成类声明。
* 类模板具体化**生成类声明**
* 类实例化**生成类对象**
* 1.**隐式实例化(implicit instantiation)**
> 他们声明一个或多个对象，指出所需的类型，而编译器使用通用模板提供的处方生成具体的类定义；
```C++
Array<int,100>stuff;//隐式实例化
//在编译器处理对象之间，不会生成隐式实例化，如下
Array<double,30>*pt;//a pointer,no object needed yet
//下面语句导致编译器生成类定义，并根据该定义创建一个对象昂
pt=new Array<double,30>;
```
* 2.**显式实例化(explicit instantiation)**
> 当使用关键字template并指出所需类型来声明类时，编译器将生成类声明的实例化
```C++
template class ArrayTP<string,100>;
```
> 这种情况下，虽然没有指出创建或提及类对象，编译器也将生成类声明（包含方法定义）。和隐式实例化也将根据通用模板来生成具体化。
* 3.**显式具体化(explicit specialization)**---是特定类型（用于替换模板中的泛型）的定义
> 格式：template<>class Classname<specialized-type-name>{...};
> 有时候，可能需要在特殊类型实例化是，对模板进行修改，使其行为不同。在这种情况下，可以创建显式实例化。
```C++
//原来的类模板
template <typename T>class sortedArray
{
...//details omitted
};
```
> 当**具体化模板**和**通用模板**都与**实例化**请求匹配时，编译器将使用具体化版本。
```C++
//新的表示法提供一个专供const char*类型使用的SortedArray模板
template<>class SortedArray<const char*>
{
...//details omitted
};
```
* 4.**部分具体化(partical specialization)**
> 部分限制模板的通用性
```C++
//general template 一般模板
    template<class T1,class T2>class Pair{...};
//specialization with T2 set to int部分具体化
    template<class T1>class Pair<T1,int>{...};
```
> 如果有多个模板可供选择，编译器将使用具体化程度最高的模板
```C++
Pair<double,double>p1;//使用了一般的Pair类模板
Pair<double,int>p2;//使用了部分具体化Pair<T1,int>
Pair<int,int>p3//使用了显式实例化Pair<int,int>
```
> 也可以通过为**指针**提供特殊版本来部分具体化现有模板：
```C++
    template<class T>
    class Feen{...};//一般版本的类模板
    template<class T*>
    class Feen{...};//部分具体化
```

### 将模板用作参数
`template<template<typename T>class Thing>class Crab`
### 模板类和友元
> 模板类声明也可以有友元。模板的友元分为3类：
* 非模板友元：
* 约束(bound)模板友元，即友元的类型取决于类被实例化时的类型；
* 非约束(unbund)模板友元，即友元的所有具体化都是类的每一个具体化的友元。
#### 模板类的非模板友元函数
* 在模板类中奖一个常规函数声明为友元：
```C++
template <class T>
class HasFriend
{
public:
friend void counts();
...
};
```
> 上述声明指定counts()函数称为模板所有实例化的友元
* **counts()函数不是通过对象调用**（它是友元不是成员函数），也没有对象参数，那么如何访问HasFriend对象？
> * 1.它可以访问全局对象
> * 2.它可以使用全局指针访问非全局对象
> * 3.可以创建自己的对象
> * 4.可以访问独立于对象的模板类的静态成员函数

#### 模板类的约束模板友元
* 1.首先，在类定义的前面声明每个模板函数
> template<typename T>void counts();
> template<typename T>void report(T &);
* 2.然后，在函数中再次将模板声明为友元。这些语句根据类模板参数的类型声明
```C++
    template<typename TT>
    class HasFriendT
    {
    ...
    friend coid counts<TT>();
    friend coid report<>(HasFriendT<TT> &);
    };
```
* 3.为友元提供模板定义
  
#### 模板类的非约束模板友元函数
* 前一节中的约束模板友元函数在类外面声明的模板的具体化。int类具体化获得int函数具体化，依此类推。通过类内部声明模板，可以创建非约束友元函数，即每个函数具体化都是每个类具体化的友元。对于非约束友元，友元模板类型参数与模板类类型参数是不同的：
```C++
template<typename T>
class ManyFriend
{
...
template<typename C,typename D>friend void show2(C &,D &);
};
```
### 模板别名(C++11)
* 1.如果能为类型指定别名，浙江爱你个很方便，在模板设计中尤为如此。**可使用typedef为模板具体化指定别名**
```C++
typedef std::array<double,12> arrd;
typedef std::array<int,12> arri;
typedef std::array<std::string,12> arrst;
//使用
arrd gallones；
arri days;
arrst months;
```
* 2.C++11新增了一项功能---**使用模板提供一系列别名**
```C++
template<typename T>
using arrtype=std::array<T,12>;//template aliases
```
> 这将arrtype定义为一个模板别名，可以用它来指定类型
```C++
arrtype<double> gallones;
arrtype<int> days;
arrtype<std::string> months;
```
* C++11允许**将语法using=用于非模板**。用于非模板是，这种语法与常规typedef等价：
```C++
typedef const char *pc1; //typedef syntax/ 常规typedef语法
using pc2=const char*;   //using = syntax/ using =语法
```

### 可变参数模板(variadic template)18章
***
