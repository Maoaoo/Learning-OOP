# 尽量以const,enum,inline替换#define(宁可以编译器替换预处理器)
 >- ## **用const代替#define定义的常量**
 >>- 用 `const double PI=3.14 ` 代替 `#define PI 3.14 `
 >>>- 因为#define本质上是预处理器的简单字符串替换，所以当出现与常量有关的编译错误时，提示的时 `3.14`而不是`PI`,因为`PI`根本没有进入**符号表**，，也就是对编译器不可见，这会很难定位到错误。  
 >>>- ***ATTENTION:***  
 `const char* const Name = "Andy"`这句话第一个`const`限定了`Andy`不可变,第二个`const`限定了`Name`这个指针不可变。如果只有第一个`const`,则可以通过对`Name`进行重新赋值从而改变所指向的内容；如果只有第二个`const`，则可以直接改变内容而不可改变指针。
 >>- ***ATTENTION:*** 在class内的常量，例：
 >>```c++
 >>class test{
 >>private:
 >>    static const int NUM=0;      //声明式
 >>public:
 >>    test(){
 >>        cout<<&NUM;
 >>    }
 >>};
 >>```
 >>将会触发编译器错误，因为类内`static`常量不可取地址，若为`const int NUM=0`则可取地址。  
 所有类内`static`整型数据 *(int,bool,char)* 都可以只提供声明式而不提供定义式，但是不能对其取地址。
![类内常量](\pics\const_vari_in_class1.png "类内常量")
![类内常量](\pics\const_vari_in_class2.png "类内常量")
>>
>>如果编译器一定要求提供定义式，而你不想这么做则可以用`enum`关键字
>>```c++
>>class test{
>>private:
>>    enum{NUM=5};
>>    //......
>>};
>>```
>>同样`NUM`也不可以被取地址。
>> ***ATTENTION:*** 如果你不想一个整型数据被取地址或成为引用，也可用以上方法。 
>- ## **使用`inline`关键字：**
>>- `inline`如何实现？将代码在调用处展开，以此来节省压栈和回调的时间开销。但事实上，声明了一个`inline`函数并不代表编译器一定会使用`inline`在调用处展开函数，`inline`只是给编译器的一个建议。如下图
![我上当了](\pics/inline-func.png "我上当了")
所以，这个还是交给编译器吧，毕竟我比编译器菜。  
***@@@@修正，在DEBUG时，不会展开，而当编译时使用Release模式，inline会被展开@@@@***
![你没上当](\pics/inline_func_correct.png "你没上当")


## ***小结***：
- 1.尽量用`const`代替`#define`；
- 2.`const char* const Name = "Andy"`第一个`const`只限定了字符数组内容，第二个`const`限定了字符数组的指针;
- 3.在类内的`static const`与`enum{...}`所定义的常量不可被取地址且只为所有类对象提供一个实例；