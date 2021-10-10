# 尽可能使用const
## const关键字的使用场景
>* 修饰对象：`const char c; `
> 
>* 修饰指针(迭代器)：`const std::vector<int>::iterator iter;`
>
>* 修饰成员函数：`void foo::foo()const;`确保在函数体内任何数据成员都不会被改变，**在const成员函数内，只能调用const成员函数**,不能间接调用函数改变成员数据。
![const成员函数](\pics/can_i_set_v.png)
>### 但是如果返回一个非const类型的指针或引用，则无法避免这个指针或者引用在函数外部改变数据成员。
>```c++
>int& foo::foo()const{
>    return v;
>} 
>{
>    ...
>    int& a=foo();
>    a=100;                          //可以被改变
>}
>```
>### **特例** - `mutable`关键字
>```c++
>class a{
>private:
>    mutable v;
>public:
>    void setv()const{v=1};      //可行！
>};
>```
>* **运用const成员函数实现non-const版本是值得了解的，而反向做法，令const版本调用non-const版本以避免代码重复，是不正确的。**
>```c++
>class TextBlock{
>    public:
>    ...
>    const char& operator[](std::size_t position)const{
>        ...
>        return text[position];
>    }
>    char& operator[](std::size_t position){
>        return
>            const_cast<char&>(
>                static_cast<const TextBlock&>(*this)
>                    [position]
>            );
>    }
>    ...
>};
> //值得提倡，因为减少了代码重复。

## **小结：**
### 1.将对象声明为const有助于编译器侦测出对象的错误用法，将逻辑错误转为编译错误，更加容易检出；
### 2.编译器实现的是bitwise constness，而逻辑上的constness应该用程序员自己注意；
### 3.const 与 non-const成员函数有着实质等价的实现时，令non-const调用const版本课避免代码重复。