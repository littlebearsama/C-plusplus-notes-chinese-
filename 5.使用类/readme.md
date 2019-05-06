# 11使用类
## 运算符重载
* 运算符重载或函数多态---定义多个名称相同但特征标（参数列表）不同的函数
* 运算符重载---允许赋予运算符多种含义

运算符函数：`operator op(argument-list)`示例：
```C++
//有类方法：
Time Sum(const Time &t)const;
//定义：
Time Time::Sum(const Time & t)const
{
Time sum;
sum.minutes = minutes + t.minutes;
sum.hours = hours + t.hours + sum.minutes/60;
sum.minutes%=60;
retrun sum;
}
```
返回值是函数创建一个新的Time对象（sum），但由于sum对象是局部变量，在函数结束时将被删除，因此引用指向一个不存在的对象，返回类型Time意味着程序将在删除sum之前构造他的拷贝，调用函数将得到该拷贝
* 运算符重载，只需把上述函数名修改即可`Sum()的名称改为operator+()`
```C++
//调用：
total=coding.Sum(fixing);
//运算符重载后调用
1. total=coding.operator+(fixing);
2. total=coding+fixing;
//t1,t2,t3,t4都是Time对象
t4=t1+t2+t3;
```
### 重载限制

下面是可重载的运算符列表：

| 运算符 | 分别 |
| -------------- | ------------------------------------------------------------ |
| 双目算术运算符 | + (加)，-(减)，*(乘)，/(除)，% (取模)                        |
| 关系运算符     | ==(等于)，!= (不等于)，< (小于)，> (大于>，<=(小于等于)，>=(大于等于) |
| 逻辑运算符     | \|\|(逻辑或)，&&(逻辑与)，!(逻辑非)                          |
| 单目运算符     | + (正)，-(负)，*(指针)，&(取地址)                            |
| 自增自减运算符 | ++(自增)，--(自减)                                           |
| 位运算符       | \| (按位或)，& (按位与)，~(按位取反)，^(按位异或),，<< (左移)，>>(右移) |
| 赋值运算符     | =, +=, -=, *=, /= , % = , &=, \|=, ^=, <<=, >>=              |
| 空间申请与释放 | new, delete, new[ ] , delete[]                               |
| 其他运算符     | **()**(函数调用)，**->**(成员访问)，**,**(逗号)，**[]**(下标) |

下面是不可重载的运算符列表：

* `.`：成员访问运算符
* `.*`, `->*`：成员指针访问运算符
* `::`：域运算符
* `sizeof`：长度运算符
* `?:`：条件运算符

1. 重载的运算符(有些例外情况)不必是成员函数,但必须至少有一个操作数是用户定义的类型.**这防止用户标准类型重载运算符**
2. 使用运算符时不能违反运算符原来的语句法则,例如,不恩那个将秋末运算符(%)重载成一个操作数。
3. 不能创建新的运算符
4. 不能重载下面的运算符
* `sizeof`：sizeof运算符
* `.`：成员运算符
* `::`：作用域解析运算符
* `？:`：条件运算符
* `typeid`：一个RTTI运算符
* `const_cast`：强制类型转换运算符
* `dynamic_cast`：强制类型转换运算符
* `static_cast`：强制类型转换运算符
* `reinterpret_cast`：强制类型转换运算符
5. 下面运算符只能通过成员运算符函数进行重载
* `=`：赋值运算符
* `()`：函数调用运算符
* `[]`：下标运算符
* `->`：通过指针访问类成员的运算符

## 友元函数
C++控制类对象私有部分的访问。通常，公有方法提供唯一的访问途径。
* C++提供了另外一种形式的访问权限：友元
1. 友元函数
2. 友元类（15章）
3. 友元成员函数（15章）

* 友元函数：通过让函数成为类的友元，可以赋予该函数与类成员函数相同的访问权限。
* 问：为何需要友元？为类重载二元运算符是（带两个参数的运算符）常常需要友元函数。将**Time对象**乘以**实数**就属于这种情况之前我们有运算符重载：

A = B * 2.75;//Time operator*(double n)const;

如果要实现

A=2.75 * B;//不对应成员函数 cannot correspond to a member function

因为2.75不是TIme类型的对象。左侧炒作书应是调用对象
* 解决：
1. 告知每个人（包括程序员自己），只能按 B * 2.75这种格式编写。
2. **非成员函数**（非成员函数来重载运算符），非成员函数不是由对象调用的，它所使用的所有值（包括对象）都是显式参数。

有函数原型：
```C++
Time operator * (double m,const Time &t);
```
使用：
```C++
A=2.75 * B;或 A=operator *(2.75,B);
```
* 问题：非成员函数不能访问类的私有数据，至少常规非成员函数不能访问
* 解决：友元函数（非成员函数，但其访问权限与成员函数相同。）
### 创建友元函数
**将其原型放在类声明中，并在原型声明前加上关键字friend**
* 声明：`friend Time operator * (double m,const Time & t);`。该原型声明意味着下面两点：
1. 虽然，operator* ()函数是在类中声明的，但它不是成员函数，因此不能使用成员运算符来调用；
2. 虽然，operator* ()函数不是成员函数，但它与成员函数的访问权限相同。
* 定义：不要使用Time::限定符，不要再定义中使用关键字friend
```C++
Time operator*(double m,const Time & t)
{
Time result;
long totalminutes=t.hours*mult*60+t.minutes*mult;
resut.hours = totalminutes/60;
result.minutes=totalminutes%60;
return result;
}
```
* 注：**不必是友元函数**（不访问数据成员也能完成功能)
```C++
Time operator * (double m,const Time & t)
{
return t*m;//调用了Time operator*(double n)const
}
```
## 重载<<运算符
常用友元：重载座左移运算符
### 第一种重载版本
1. 使用一个Time成员函数重载<<
```C++
trip<<cout;//（trip是Time对象）这样会让人困惑
```
2. 通过使用友元函数，可以像下面这样重载运算符：
```C++
void operator<<(ostream & os,const Time& t)
{
os<<t.hours<<"hours"<<t.minutes<<"minute";
}
```
* 该函数成为Time类的一个友元函数（operator<<()直接访问Time对象的私有成员），但不是ostream类的友元（从始至终都将ostream对象作为一个整体来使用）
### 第二种重载版本
按照上面的定义，下面语句会出错：
```C++
cout<<"Trip Time:"<<trip<<"(Tuesday)\n"//不能这么做
```
应该修改友元函数返回ostream对象的引用即可：
```C++
ostream& operator<<(ostream & os,const Time& t)
{
os<<t.hours<<"hours"<<t.minutes<<"minute";
return os;
}
```
按照上面的定义，下面可以正常运行：
```C++
cout<<"Trip Time:"<<trip<<"(Tuesday)\n"//正常运行
```
### 类继承属性让ostream引用能指向ostream对象和ofstream对象
```C++
#include<fstream>
ofstram fout;
fout.open("Savetime.txt");
Time trip(12,40);
fout<<trip;//等价于operator<<(fout,trip);
```
## 类的自动类型转换和强制转换
有构造函数`Stonewt(double lbs);`可以编写下列代码：
```C++
stonewt mycat;//创建一个对象
mycat=19.6；//使用了Stonewt(double lbs)构造函数创意了一个临时对象
```
**上面使用了一个Stonewt(double lbs)构造函数创建了一个临时对象，然后将该对象内容复制到了mycat中**，这一过程（19.6利用构造函数变成类对象）需要隐式转换，因为是自动进行的，而不需要显式强制转换。
* --->只接受一个参数类型的构造函数定义了**从参数类型到类类型**的转换

注意：只有接受一个参数的构造函数才能作为转换函数，然而，如果第二个参数提供默认值，它便可用于转换int
```C++
Stonewt(int stn,double lbs=0);
```
### explicit
这种自动特性并非总是合乎需要的，因为会导致意外的类型转换。
* **新增关键字explicit，用于关闭这种自动特性，也就是说，可以这样声明构造函数：**
```C++
explicit Stonewt(double lbs);//关闭了上面的隐式转换，但允许显式转换，即显式强制类型转换

Stonewt mycat;
mycat =19.6;//错误代码

mycat = Stonewt(19.6);//这里是调用构造函数
mycat =(Stonewt)19.6;//这里是前置类型转换
```
### 总结
* 当构造函数只接受一个参数是，可以使用下面的格式来初始化类对象。
```C++
Stonewt incognito=2.75;
```
这等价于前面介绍过的另外两种格式：(这两种格式可用于接收多个参数的构造函数)
Stonewt incognito(2.75);
Stonewt incognito = Stonewt(2.75);

* 下面函数中，Stonewt和Stonewt&形参都与Stonewt实参匹配
```C++
void display(const Stonewt & st,int n)
{
for(int=0;i<n;i++)
{
cout<<"WOW!";
st>show_stn();
}
}
```
* 语句display(422,2);中
1. 编译器先查找自动类型转换中42转Stone的构造函数`Stonewt(int)`
2. 不存在`Stonewt(int)`的话，Stonewt(double)构造函数满足这种要求因为，编译器将int转换为double

## 类的转换函数
* 构造函数只用于某种类型到类类型的转换，**要进行相反的转换，必须用到特殊的C++运算符---转换函数**
* 转换函数必定是类方法
* 用户定义的强制类型转换，可以向使用强制类型转换那样使用它们。
```C++
Stonewt wolf(285,7);
double host = double(wolfe);//格式1
double thinker=(double)wolfe;//格式2
```
* 也可以让编译器来决定如何做：
```C++
Stonewt wolf(20,3);
double star =wells;//隐式转换
```
### 创建转换函数
**opeator typeName();**
* 转换函数必定是类方法
* 转换函数不能指定返回类型
* 转换函数不能有参数

例如：**转换函数为double类型的原型如下**
```C++
operator double();//不要返回类型也不要参数
```
### 如何定义
1. 头文件中声明： 
```C++
operator int() const;
operator double() const;
```
2. cpp文件中定义：
```C++
Stonewt::opeator int() const
{
return int (pounds+0.5);//四舍五入
}

Stonewt::opeator double() const
{
return pounds;//四舍五入
}
```
### 二义性
C++中，int和double值都可以被赋值给long变量，下面语句被编译器认为有**二义性**而拒绝了。
```C++
long gone = poppins;//注：poppins是Stonewt对象
//但是还是可以进行强制类型转换
long gone = (double)poppins;
long gone =int (poppins);
```
### 避免隐式转换
* 方法1：C++98中，关键字`explicit`不能用于转换函数，但C++11消除了这种限制。因此，在C++11中，可将转换运算符声明为显示的：
```C++
class Stonewr
{
...
//conversion functions
explicit operator int() const;
explicit operator double() const;
};
```
有了这些声明后，需要前置转换时，将调用这些运算符。
* 方法2：用一个功能相同的**非转换函数**替换转换函数即可，但仅在被显式调用时，该函数才会执行。也就是说，可以将：
```C++
Stonewt::operator int(){return int(pounds+0.5);}
```
替换为
```C++
int Stonewt stone_to_int(){return int(pounds+0.5);}
```
这样下面语句为非法的：
```C++
int plb=popins;
```
需要转换时只能调用stone_to_int()：
```C++
int plb =poppins.stone_to_int();
```
***
