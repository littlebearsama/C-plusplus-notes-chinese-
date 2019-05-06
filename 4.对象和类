# 10对象和类
* 类声明：以数据成员的方式描述数据部分，以成员函数（被称为方法）的方式描述公有接口-->C++程序员将接口（类定义）放在头文件中
* 类方法定义：描述如何实现成员函数-->并将实现（类方法代码）放在源代码文件中

细节：
1. 使用#ifndef等来访问多次包含同一个文件
2. 将类的首字母大写

* 控制访问关键字：private public protected
* C++对结构进行了扩展，使之具有与类相同的特性。他们之间唯一的区别是：**结构的默认访问类型是public，类为private**
* 通常，数据成员被放在私有部分中=>数据隐藏；成员函数被放在公有部分中=>公有接口
## 实现类成员方法
成员函数两个特殊的特征：
1. 定义类成员函数时。使用作用域解析符（::）来标识函数所属的类；
2. 类方法可以访问类的private组件。

```C++
class Stock
{
      private:
              char company[30];
              int shares;
              double share_val;
              double total_val;
              void set_tot(){total_val = shares * share_val;}
      public:
             Stock();              
             Stock(const char * co , int n = 0 , double pr = 0.0);
             ~Stock(){}
             void buy(int num , double price);
             void sell(int num , double price);
             void update(double price);
             void show() const;
             const Stock & topval(const Stock & s) const;
};
```
1. set_tot()只是实现代码的一种方式，而不是公有接口的组成部分，因此这个类将其声明为私有成员函数（即编写这个类的人可以使用它，但编写带来来使用这个类的人不能使用）。
2. **内联方法**，
* 其定义位于类声明中的函数都将自动成为内联函数。因此Stock::set_tot()是一个内联函数。
* 在类声明之外定义内联函数
```C++
class Stock
{
private:
...
void set_tot();
public:
...
};
inline void Stock::set_tot(){
total_val = shares * share_val;
}
```
* 内联函数有特殊规则，**要求每个使用它们的文件都对其进行定义**。确保内联定义对多个文件程序中的所有文件都可用的最简便方法是：**将内联定义放在头文件中**

3. 如何将类方法应用于对象？（对象，数据和成员函数）
所创建的每个新对象都有自己的存储空间，用于存储其**内部变量**和**类成员**。但同一个类的所有对象共享一组类方法，即每种方法只有一个副本。

* 要使用类，**要创建类对象**，可以声明类变量，也可以使用new为类对象分配存储空间。
* 实现了一个使用stock00接口和实现文件的程序后，**将其与stock00.cpp一起编译，并确保stock00.h位于当前文件夹中**
* 类成员函数(方法)可通过类对象来调用。为此，需要使用成员运算符句点。

## 类的构造函数和析构函数
### 构造函数
原因：数据部分的访问状态是私有的，这意味着程序不能直接访问数据成员（私有部分数据）。程序只能通过成员函数来访问数据成员，因此需要设计合适的成员函数才能将对象初始化。---**类构造函数**
* 声明和定义构造函数
```C++
//construtor prototype with some default argument
Stock(const string &co,long n=0,double pr=0.0);//原型
```
原型声明位于类声明的公有部分。

构造函数可能的一种定义
```C++
Stock::Stock(const string &co,long n,double pr)
{
company = co;
if(n>0)
{
std::cerr<<"Number of shares can't be negative;"
         << company <<" shares set to 0.\n";
	 shares = 0;
}
else
shares=n;
share_val=pr;
set_tot();
}
```
* 注意“参数名co，n，pr不能与类成员相同.构造函数的参数表示不是类成员，而是赋给类成员的值。
* 区分参数名和类成员：一种常见的做法是在数据成员名中使用m_前缀 string m_company;;另外一种常见的做法是，在成员名中使用后缀_ string company_;
#### 使用构造函数
1. 显式调用
```C++
Stock food = Stock1("World cabbage",250,1.25);
```
2. 隐式调用
```C++
Stock garment("Furry Mason",50,2.5);
//等价于
Stock garment= Stock("Furry Mason",50,2.5);
```
3. 每次创建类对象（甚至使用new动态分配内存）时，C++都是用类结构函数。
```C++
Stock *pstock= new Stock("Electroshock Games",18,19.0);//对象没有名称，但可以使用指针来管理对象
```
### 默认构造函数
未提供显示初始值是，用来创建对象的构造函数。例:
```C++
Stock fluffy_the_cat;//use the default constructor
```
* 当且今当没有定义任何构造函数时，**编译器**才会提供默认构造函数。
* 为类定义了构造函数后，**程序员**就必须为它提供默认构造函数
* 如果提供了非默认构造函数（如Stock(const string &co,long n,double pr);），但没有提供构造函数，下面声明将出错（**禁止创建未初始化对象**）
```C++
Stock stock1;
```
#### 如何定义默认构造函数：
方法1：给已有构造函数函数的**所有参数提供默认值**
```C++
Stock(const string &co="Error",int n=0,double pr=0.0); 
```
方法2：通过函数重载来定义另外一个构造函数---一个没有参数的构造函数
```C++
Stock();
```
为Stock类提供一个默认构造函数：
```C++
//隐式初始化所有对象，提供初始值
Stock::Stock()
{
company = "no name";
shares = 0;
share_val = 0.0;
total_val = 0.0;
}
```
* 使用默认构造函数：
```C++
Stock first;//隐式地调用默认的构造函数
Stock first = Stock();//显式地
Stock * prelief=new Stock;//隐式地
```
* 然而不要被废默认构造函数的隐式形式所误导：
```C++
Stock first("Concrete Conglomerate");//调用构造函数
Stcok second();                      //声明一个函数
Stock third；                        //调用默认构造函数
```
### 析构函数
* 对象过期是，程序将自动调用该特殊的成员函数。析构函数完成清理工作
* **如果构造函数使用new来分配内存，则析构函数将使用delete来释放这些内存。**

什么时候调用析构函数？这由编译器决定，不应在代码中显示地调用析构函数
1. 如果创建的是**静态存储类对象**，其析构函数将在程序结束时自动被调用。
2 .如果创建的是**自动存储类对象**，则其析构函数将在程序执行完代码块时（该对象是在其中定义的）自动被调用。
3. 如果对象是通过**new创建的**，则它将驻留在栈内存或自由存储区中，**当使用delete来释放内存时，其析构函数将自动被调用。**
   程序可以创建临时变量对象来完成特定操作，在这种情况下，程序将在结束对该对象的使用时自动调用其析构函数。
4. 总的来说：**类对象过期时（需要被销毁时），析构函数将自动被调用。**因此必须有一个析构函数。如果程序员没有提供析构函数，编译器将隐式地声明一个默认构造函数。

## C++列表初始化
只要提供与某个构造函数的参数列表匹配的内容，并用大括号将它们括起。
```C++
Stock hot_tip = {"Derivatives Plus Plus",100 ,45.0};//构造函数
Stock jock {"Sport Age Storage,Inc"};//构造函数
Stock temp{};//默认构造函数
```
前两个声明中，用大括号括起的列表与下面的构造函数匹配：
```C++
Stock(const string &co,long n=0,double pr=0.0);//原型
```
因此，用该构造函数来创建这两个对象。创建对象jock时，第二和第三个参数将默认值为0和0.0。第三个声明与默认构造函数匹配，因此将使用该构造函数创建对象temp。
## const成员函数
```C++
void Stock::show() const;//promises not to change invoking object
```
以这种方式声明和定义的类成员函数被称为**const成员函数**。就像应景可能将const引用和指针作函数参数一样，**只要类方法不修改调用对象，就应该将其声明为const**

## this指针
有的方法可能涉及两个对象，在这种情况下需要使用C++的this指针（比如运算符重载）

提出问题：如何实现：定义一个成员函数，查看两个Stocl对象，并返回股价高的那个对象的引用。
* 最直接的方法是，返回一个引用，该引用指向股价总值较高的对象，因此，用于比较的方法原型如下：
```C++
const Stock & topval(const Stock & s) const;//该函数隐式地访问一个对象，并返回其中一个对象
```
1. 第一个const：由于返回函数返回两个const对象之一的引用，因此返回类型也应为const引用
2. 第二个const：表明该函数不会修改被**显式**访问的对象
3. 第三个const：表明该函数不会修改被**隐式**访问的对象

调用：
```C++
top = stock1.topval(stock2);//隐式访问stock1，显式访问stock2
```
* this 指针用来指向调用成员函数的对象（this被作为隐藏参数传递给方法）。
* 每个成员函数（包括构造函数和析构函数）都有一个this指针。this指针指向调用对象
* 如果方法需要引用整个调用对象，可一个使用表达式`*this`。

实现：
```C++
const Stock & topval(const Stock & s) const
{
if(s.total_val>total_val)
return s;
else
return *this;
}
```
## 创建对象数组
```C++
Stock stocks[4]={
		Stock("NanoSmart",12,20.0),
		Stock("Boffo Objects",200,2.0),
		Stock("Monolothic Obelisks",130,3.25),
		Stock("Fleep Enterprises",60,6.5)
};
```
## 类作用域
回顾：
* 全局（文件）作用域，局部（代码块）作用域
* 可以在全局变量所属的任何地方使用它，而局部变量只能在其所属的代码块中使用。函数名称的作用域也可以是全局的，但不能是局部的。
### 类作用域
* 在类中定义的名称（如类数据成员和类成员函数名）的作用域为整个类。
* 类作用域意味着不能从外部直接访问类成员，公有函数也是如此。也就是说，要用调用公有成员函数，必须通过对象。
* 使用类成员名时，必须根据上下文使用，**直接成员运算符（.），间接成员运算符（->）或者作用域解析符（::）**
### 作用域为类的常量
下面是错误代码
```C++
class Bakery
{
private:
const int Months=12;//错误代码
double cots[Months];
}
```
* 这是行不通的，因为声明类只是描述了对象的形式，并没有创建对象。因此在创建对象之前，并没有用于存储值的空间。

解决：
1. 方法一：使用枚举
```C++
class Bakery
{
private:
enum{Months=12};
double costs[Months];
}
```
* 这种方式声明枚举并不会创建类数据成员。也就是说，所有对象都不包含枚举。另外，Months只是一个符号名称，在作用域为整个类的代码中遇到他时，编译器将用12来替换它。

2. 方法二：使用关键字static
```C++
class Bakery
{
private:
static const int Months=12；
double costs[Months];
}
```
* 这将创建一个名为Months的常量，**该常量与其他静态变量存储在一起，而不是存储在对象中。**因此，只有一个Months常量，被所有Bakery对象共享。


## 作用域内枚举（C++11）
传统的枚举存在一些问题，其中之一是两个枚举定义的枚举量可能发生冲突。
```C++
enum egg{Small，Medium,Large，XLarge};
enum t_shirt{Small,Medium,Large,Xlarge};
```
* 这将无法通过编译因为egg Small和t_shirt Small位于相同的作用域内，他们将发生冲突。
### 新枚举
```C++
enum class egg{Small，Medium,Large，XLarge};
enum class t_shirt{Small,Medium,Large,Xlarge};
```
* 作用域为类后，不同枚举定义的枚举量就不会发生冲突了。
* class也可以用关键字struct来代替

使用：
```C++
egg choice = egg::Large;
t_shirt Floyd=t_shirt::Large;
```
注意：作用域内枚举不能隐式地转换为整型，下面代码错误
```C++
int ring = Floyd;//错误
```
但是必要时可以进行显式转换
```C++
int Floyd = int(t_shirt::Small);
```

## 抽象数据类型ADT(Abstract Data Type)
* 以抽象的方式描述数据类型，而没有引入语言和细节
***
