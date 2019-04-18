# C++
C++ Primer Plus(第六版)笔记
***
## 1～4基础（1）

***
## 7～8函数（3）

***
## 9内存模型和名称空间（4）

***
## 10对象和类（5）


***

# 12类和动态内存分配
* 动态内存和类
* -->让程序运行时决定内存分配，而不是在编译时决定。
* ---->使用new和delete运算符来动态控制内存
* ------>在类中使用这些运算符将导致许多新的编程问题。这种情况下，析构函数将是必不可少的，而不再是可有可无的。
* -------->有时候还必须重载赋值运算符。

## C++为类自动提供了下面这些成员函数
* **1.默认构造函数**，如果没有定义构造函数；记得自己定义了构造函数时，编译器不会再提供默认构造函数，记得自己再定义一个默认构造函数。
带参数的构造函数也可以是默认构造函数，只要所有参数都有默认值。
* **2.默认析构函数**，如果没有定义；用于销毁对象
* **3.复制（拷贝）构造函数**，如果没有定义；用于将一个对象赋值到**新创建**的对象中（将一个对象初始化为另外一个对象）。用于初始化的过程中，而不是常规的赋值过程。
> * 每当程序生成对象副本时（函数按值传递对象，函数返回对象时），编译器都将使用复制构造函数
> * 编译器生成临时对象是，也将使用复制构造函数
`默认的复制构造函数的功能--->逐个复制非静态成员（成员复制也叫浅复制,给两个对象的成员划上等号），复制的是成员的值；如果成员本身就是类对象，则将使用这个类的复制构造函数复制成员对象,静态成员变量不受影响，因为它们属于整个类，而不是各个对象`
* 浅复制面对指针时会出现错误，在复制构造函数中浅复制的等价于sailor.len=sport.len;sailor.str=sport.str;前一个语句正确，后一个语句错误，因为成员char* str是指针，**得到的是指向同一字符串的指针！！！**
* 当出现动态内存分配时，要定义一个现实复制构造函数--->进行深度复制(deep copy)
```C++
StringBad::StringBad(const StringBad & st)
{
len=st.len;
str=new char[len+1];
std::strcpy(str,st.str);
}
```
* **4.赋值运算符**，如果没有定义；赋值运算符只能由类成员函数重载的运算符之一。将已有的对象赋值给另外一个对象时（将一个对象复制给另外一个对象），使用赋值运算符。
`原型：class_name & class_name::operator==(const class_name &);`接受并返回一个指向类对象的引用。
> * 与复制构造函数相似，赋值运算符的隐式实现也对成员进行逐个复制
* 解决：提供赋值运算符（进行深度复制）定义，其实现业余复制构造函数相似，但有一些差别
```C++
//代码1：先申请内存，再delete
CMyString& CMyString::operator=(const CMyString& str)
{
if(this==str)
{
char *temp_pData=new char[strlen(str.m_pData)+1)];
delete[]m_pData;
m_pData=temp_pData;
strcpy(m_pData,str.m_pData);
}
return *this;
}
//代码2：调用复制构造函数
CMyString& CMyString::operator=(const CMyString& str)
{
if(this==str)
{
CMyString strTemp(str);//复制构造函数创建临时对象，临时对象失效时会自动调用析构函数
char* pTemp=strTemp.m_pData;//创建一个指针指向临时对象的数据成员m_pData
strTemp.m_pData=m_pData;//交换
m_pData=pTemp;//交换
}
return *this;
}
```
* **5.地址运算符**，如果没有定义；
* **6.移动构造函数(move construtor)**，如果没有定义；
* **7.移动赋值运算符(move assignment operator)**。

## 静态类成员函数
* 1.**静态函数**：静态函数与普通函数不同，它只能在声明它的文件中可以见，不能被其他文件使用。定义静态函数的好处：静态函数不会被其他文件使用。其他文件中可以定义相同名字的函数，不会发生冲突。
* 2.**静态类成员函数**:与静态成员数据一样，我们可以创建一个静态成员函数，**它为类的全部服务**，而不是为某一个类的具体对象服务。静态成员函数与静态成员数据一样，都是在类的内部实现，属于类定义的一部分。普通成员函数一般都隐藏了一个this指针，this指针指向类的对象本身。
* 3.静态成员函数由于不是与任何对象相联系，因此不具有this指针，**从这个意义上讲，它无法访问属于类对象的非静态数据成员，也无法访问非静态成员函数，它只能调用其余的静态成员函数**
### 静态成员函数总结：
> * 1.出现在类体外的函数不能指定关键字static；
> * 2.静态成员之间可以互相访问，包括静态成员函数访问静态数据成员和访问静态成员函数；
> * 3.非静态成员函数可以任意地访问静态成员函数和静态数据成员；
> * 4.静态成员函数不能访问非静态成员函数和非静态数据成员
> * 5.由于没有this指针的额外开销，因此静态成员函数与类的全局函数相比，速度上会有少许的增长
> * 6.调用静态成员函数，可以用成员访问操作符(.)和(->)为一个类的对象或指向类对象的指调用静态成员函数。

## 构造函数中使用new时应注意的事项
* 1.如果**构造函数**中使用new来初始化指针成员，则应在析构函数中使用delete。
* 2.new和delete必须相互兼容，new对应于delete，new[]对应于delete[]
* 3.如果有多个构造函数，**则必须以相同的方式使用new**，要么中括号，要么都不带。因为**只有一个析构函数**，所有的构造函数都必须与它兼容。然而，可以在一个构造函数中使用new来初始化指针，而在另外一个构造函数中初始化为空（0或nullptr），这是因为delete（无论是带括号，还是不带括号）可以用于空指针。
* 4.应定义一个复制构造函数，通过深度复制将一个对象初始化为另外一个对象。
* 5.应当定义一个赋值运算符，通过深度复制将一个对象复制给另外一个对象。

## 有关返回对象的引用
* 1.首先，返回对象将调用**复制构造函数（给新创建的临时对象复制（初始化））**，而返回引用不会
* 2.其次，返回引用指向的对象因该在调用函数执行时存在。
* 3.返回作为参数输入的常量引用，返回类型必须为const，这样才匹配。
## 使用指向对象的指针
`Class_name* ptr = new Class_name;`调用默认构造函数
### 定位new运算符/常规new运算符
```C++
//使用new运算符创建一个512字节的内存缓冲区
char* buffer =new char[512];//地址：(void*)buffer=00320AB0
//创建两个指针；
JustTesting *pc1,*pc2;
//给两个指针赋值
pc1=new (buffer)JustTesting;//使用了new定位符，pc1指向的内存在缓冲区 地址：pc1=00320AB0
pc2=new JustTesting("help",20);//使用了常规new运算符，pc2指向的内存在堆中
//创建两个指针；
JustTesting *pc3,*pc4;
//给两个指针赋值
pc3=new (buffer)JustTesting("Bad Idea",6);//使用了new定位符，pc3指向的内存在缓冲区 地址：pc3=00320AB0
//创建时，定位new运算符使用一个新对象覆盖pc1指向的内存单元。
//问题1：显然，如果类动态地为其成员分配内存，该内存还没有释放，成员就没了，这将引发问题。
pc4=new JustTesting("help",10);//使用了常规new运算符，pc4指向的内存在堆中
//释放内存
delete pc2；//free heap1
delete pc4；//free heap2
delete[] buffer//free buffer
```
* 解决问题1，代码如下：
```C++
pc1=new (buffer)JustTesting;
pc3=new (buffer+sizeof(JustTesting))("Bad Idea",6);
```
* 问题2：
> 将delete用于pc2，pc4时，将自动调用为pc2和pc4指向的对象调用析构函数；问题2：然而，将的delete[]用于buffer时，**不会为使用定位new运算符创建的对象调用析构函数**
* 解决问题2：显式调用析构函数
```C++
pc3->~JustTesting;
pc1->~JustTesting;
```
## 嵌套结构和类
* 在类声明的结构、类或枚举被认为是被嵌套在类中，其作用域为整个类
* 这种声明不会创建数据对象，而是指定了可以在类中使用的类型。
> * 1.如果声明是在类的私有部分进行的，则只能在这个类中使用被声明的类型。
> * 2.如果声明是在公有部分进行的，则可以从类的外部通过作用域解析运算符使用被声明的类型
> 例如，如果Node是在Queue类的公有部分声明的，则可以在外部声明Queue::Node类型的变量。
## 默认初始化
### a.内置类型的变量初始化
如果定义变量时没有指定初始值，则变量被默认初始化。默认值由变量类型和定义变量的位置决定。
* 如果是内置类型的变量未被显示初始化，它的值由定义位置决定。定义于任何函数体外的变量被初始化为0。
```C++
//不在块中
int i;//正确，i会被值初始化为0，也称为零初始化
```
* 定义于函数体内部的内置类型变量将不被初始化（uninitialized）。一个未被初始化的内置类型变量的值是未定义的，如果试图拷贝或其他形式的访问此类型将引发错误
```C++
1 {//在一个块中
2 int i;//默认初始化，不可直接使用
3 int j=0;//值初始化
4 j=1;//赋值
5 }
```
### b.类类型的对象初始化
类通过一个或几个特殊的成员函数来控制其对象的初始化过程，这些函数叫做构造函数（constructor）。构造函数的任务是初始化类对象的数据成员。
由编译器提供的构造函数叫（合成的默认构造函数）。
合成的默认构造函数将按照如下规则初始化类的数据成员。
* 如果存在类内初始值（__C++11新特性__），用它来初始化成员。
```C++
class CC
{
public:
    CC() {}
    ~CC() {}
private:
    int a = 7; // 类内初始化，C++11 可用
}
```
* 否则，没有初始值的成员将被默认初始化。
## 成员列表初始化
* 使用成员初始化列表的构造函数将覆盖相应的类内初始化
* 对于简单数据成员，使用成员初始化列表和在函数体中使用复制没什么区别
* **对于本身就是类对象的成员来说，使用成员初始化列表的效率更高**
> 如果Classy是一个类，而mem1，mem2，mem3都是这个类的数据成员，则类构造函数可以使用如下的语法来初始化数据成员。
```C++
Classy::Classy(int n,intm):mem1(n),mem2(0),men3(n*m+2)
{
//...
}
```
* 1.这种格式只能用于构造函数
* 2.必须用这种格式来初始化非静态const数据成员（至少在C++之前是这样的）；
* 3.必须用这种格式来初始化引用数据成员
* 数据成员被初始化顺序与它们出现在类声明中的顺序相同，与初始化器中的排列顺序无关

***

# 13类继承
## 基类和派生类的特殊关系
* 1.派生类对象可以使用非私有的基类方法
* 2.基类指针（引用）可以在不进行显示转换的情况下指向（引用）派生类对象（**反过来不行**）；**基类指针或引用只能用来调用基类方法，不能用来调用派生类方法。**
* 3.不可以将基类对象和地址赋给派生类对象引用和指针。

## 派生类构造函数要点
1.首先创建基类对象；
2.派生类构造函数应通过成员初始化列表将基类信息传递给基类构造函数。
3.派生类构造函数应初始化新增的数据成员。
注意：可以通过初始化列表语法知名指明要使用的基类构造函数，否则使用默认的基类构造函数。派生类对象过期时，程序**将首先调用派生类的析构函数，然后在调用基类的析构函数**。
```C++
RetedPlayer::RetedPlayer(unsigned int r,const string & fn,const string &ln, bool ht)//:TableTennisPlayer()等价于调用默认构造函数
{
rating = r;
}
```
## 虚方法
* 经常在基类中将派生类会重新定义的方法声明为**虚方法**。方法在基类中被声明为虚的后。它在派生类中将**自动生成**虚方法。然而，在派生类中使用关键字virtual来指出哪些函数是虚函数也不失为一个好方法。
* 如果要在派生类中重新定义基类的方法，通常将基类方法声明为虚。这样，**程序根据对象类型而不是引用或指针类型来选择方法版本**，为基类声明一个虚的析构函数也是一种惯例，为了确保释放派生类对象时，按正确的顺序调用析构函数。
* 虚函数的作用：基类指针（引用）指向（引用）派生类对象，会发生自动向上类型转换，即派生类升级为父类，虽然子类转换成了它的父类型，但却可正确调用属于子类而不属于父类的成员函数。这是虚函数的功劳。

## 派生类方法可以调用公有的基类方法
在派生类方法中，标准技术是使用作用域解析运算符来调用基类方法，如果没有使用作用域解析符，有可能创建一个不会终止的递归函数。如果派生类没有重新定义基类方法，那么代码不必对该方法是用作用域解析符（即该方法只有在基类中有）。

## 静态联编和动态联编
**函数名联编**（binding）：将代码中的函数调用解释为执行特定的代码块。
* 在C语言中，这非常简单，因为每个函数名都对应一个不同的函数。
* 在C++中，由于函数重载的缘故，这个任务更繁杂，编译器必须查看函数参数以及函数名才能确定使用哪个函数。
>静态联编(static binding)
* 在编译过程中进行联编，又称为早期联编
>动态联编(dynamic binding)
* 编译器在程序运行时确定将要调用的函数，又称为晚期联编
### 什么时候使用静态联编，什么时候使用动态联编？
* 编译器对虚方法使用静态联编，因为方法是非虚的，可以根据指针类型关联到方法。
* 编译器对虚方法使用动态联编，因为方法是虚的，程序运行时才知道指针指向的对象类型，才来选择方法。（引用同理）
### 效率：为使程序能够在运行阶段进行决策，必须采取一些方法来跟踪基类指针和指向引用对象的对象类型，这增加了额外的处理开销
* 例如，如果类不会用作基类，则不需要动态联编。
* 同样，如果派生类不重新定义基类的任何方法，也不需要动态联编。
* 通常，编译器处理函数的方法是：给每个对象添加一个隐藏成员--指向函数地址数组的**指针**(vptr)
>使用虚函数时，在内存和执行速度上有一定的成本，包括：
a.每个对象为存储地址的空间；
b.对于每个类，比那一期都将创建一个虚函数地址表（数组）vtbl；
c.对于每个函数调用，都需要执行一项额外的操作，到表中查找地址。虽然非虚函数的效率比虚函数稍高，但不具有动态联编的功能
### 总结：
* 在基类方法的声明中使用关键字virtual可使该方法在基类以及所有的派生类（包括从派生类派生出来的类）中是虚的。
* 如果使用指向对象的引用或指针来调用虚方法，程序将使用为对象类型定义的方法，而不是用为引用或者指针类型定义的方法。这个成为动态联编或者晚期联编。这种行为非常重要。因为这样基类指针或引用可以指向派生类对象。
* 如果定义的类将被用作基类，则应该将那些在派生类中重新定义的类方法生命为虚的。

## 虚函数细节
* 1.构造函数不能是虚函数，派生类不能继承基类的构造函数，将类构造函数声明为虚没什么意义。
* 2.析构函数应当是虚函数，除非类不用作基类。
> 1.当子类指针指向子类是，析构函数会先调用子类析构再调用父类析构，释放所有内存。
> 2.当父类指针指向子类时，只会调用父类析构函数，子类析构函数不会被调用，会造成内存泄漏。（基类析构函数声明为虚，可以使得父类指针能够调用子类虚的析构函数）**所以我们需要虚析构函数，将父类的析构函数定位为虚，那么父类指针会先调用子类的析构函数，再调用父类析构，使得内存得到释放**。
* 3.友元不能是虚函数，因为友元不是类成员，只有类成员才是虚函数。
* 4.如果派生类没有重新定义函数。将使用该函数的基类版本。
* 5.**重新定义将隐藏方法**不会生成函数的两个重载版本，而是隐藏基类版本。如果派生类位于派生链中，则使用最新的虚函数版本，例外的情况是基类版本是隐藏的。总之，重新定义基本的方法并不是重载。如果重新定义派生类中的函数，将不只是使用相同的函数参数列表覆盖其基类声明，无论参数列表是否相同，该操作将隐藏所有的同名方法。
## 两条经验规则
* 1.如果重新定义继承的方法，应确保与原来的原型完全相同，但是**如果返回类型是积累的引用或指针，则可以修改为指向派生类的引用或指针（只适用于返回值而不适用于参数）**，这种例外是新出现的。这种特性被称为**返回类型协变（convariance of return type）**，因此返回类型是随类类型变化的。
```C++
//基类
class Dwelling
{
public:
virtual Dwelling & build(int n);
}
//派生类
class Hovel:public Dwelling
{
public:
virtual Hovel & build(int n);
}
```
* 2.如果基类声明被重载了，则应该在派生类中重新定义所有基类版本。
```C++
//基类
class Dwelling
{
public:
//三个重载版本的showperks
virtual void showperks（int    a）const；
virtual void showperks（double a）const；
virtual void showperks（        ）const；
}
//派生类
class Hovel:public Dwelling
{
public:
//三个重新定义的的showperks
virtual void showperks（int    a）const；
virtual void showperks（double a）const；
virtual void showperks（        ）const；
}
```
**如果只重新定义一个版本，则另外两个版本将被隐藏，派生类对象将无法使用它们，**
注意，如果不需要修改，则新定义可知调用基类版本：
```C++
void Hovel::showperk()const
{
Dwelling::showperks();
}
```
## 访问控制：protected
* 1.关键字protected与private相似，在类外，只能用公有类成员来访问protected部分中的类成员。
* 2.private和protected之间只有在基类派生的类才会表现出来。派生类的成员可以直接访问基类的保护成员，但是不能直接访问基类的私有成员。
### 提示：
* 1.最好对类的数据成员采用私有访问控制，不要使用保护访问控制。
* 2.对于成员函数来说，保护访问控制很有用，它让派生类能够访问公众不能使用的内部函数。

## 抽象基类（abstract base class）ABC->至少包含一个纯虚函数
* 在一个**虚函数的声明语句**的分号前加上 =0 ；就可以将一个虚函数变成**纯虚函数**，其中，=0只能出现在类内部的虚函数声明语句处。
* 纯虚函数只用声明，而不用定义，其存在就是为了提供接口，含有纯虚函数的类是抽象基类。我们不能直接创建一个抽象基类的对象，但可以创建其指针或者引用。
* 值得注意的是，你也可以为纯虚函数提供定义，**不过函数体必须定义在类的外部**。但此时哪怕在外部定义了，也是纯虚函数，不能构建对象。  
* **派生类构造函数只直接初始化它的直接基类。多继承的虚继承除外。**

### 抽象类应该注意的地方
* 抽象类不能实例化，所以抽象类中不能有构造函数。
* 纯虚函数应该在派生类中重写，**否则派生类也是抽象类，不能实例化。**

### 抽象基类的作用
* C++通过使用纯虚函数（pure virtual function）提供未实现的函数。纯虚函数声明的结尾处为=0，
```C++
virtual double Area() const=0;//=0指出类是一个抽象基类，在类中可以不定义该函数 
```
* 可以将ABC看作是一种必须的接口。ABC要求具体派生类覆盖其纯虚函数---迫使派生类遵顼ABC设置的接口规则。简单来说是：因为在派生类中必须重写纯虚函数，否则不能实例化该派生类。所以，派生类中必定会有重新定义的该函数的接口。
* 从两个类(**具体类concrete**)（如：Ellipse和Circle类）中抽象出他们的共性，将这些特性放到一个ABC中。然后从该ABC派生出的Ellipse和Circle类。
**这样，便可以使用基类指针数组同时管理Ellipse和Circle对象，即可以使用多态方法**



## 友元
* 就像友元关系不能传递一样，友元关系同样不能继承，基类的友元在访问派生类成员时不具有特殊性，类似的，派生类的友元也不能随意访问基类的成员。

## 继承和动态内存分配(todo)
* 只有当一个类被用来做基类的时候才会把析构函数写成虚函数。
* 当基类和派生类都采用动态内存分配是，派生类的析构函数，复制构造函数，赋值运算符都必须使用相应的基类方法来处理基类

***

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

1.

## RTTI
## 类型转换运算符

