## namespace
* namespace通常用来给类或者函数做个区间定义，以使编译器能准确定位到适合的类或者函数。譬如说，自行实现了一个函数test(void)，而在该项目的库函数内也定义了一个函数test(void);当你调用test();函数的时候，编译器就困惑了，该调用哪个呢？这个时候namespace的作用就显现出来了。

* declaration
```
namespace first_space {
        // code declarations
        // 函数，类名等等
    }s
```

* index
```
first_space::code(); //此处code就是对应namespace内定义的类名或者函数名等等。
// or
using namespace first_space;
code();
```

## error exception

* you can reload the virtual class exception by:
```
include <exception>
using namespace std;
struct MyException : public exception
{
  const char * what () const throw ()
  {
    return "C++ Exception";
  }
};
 
int main()
{
  try
  {
    throw MyException();
  }
  catch(MyException& e)
  {
    std::cout << "MyException caught" << std::endl;
    std::cout << e.what() << std::endl;
  }
  catch(std::exception& e)
  {
    //其他的错误
  }
}
```

* if you want to catch the third party library error, code this:

```
  try
  {
    // third party staff.
  }catch(std::exception& e){
    //deal with e.what();
    return or continue follows;
  }
```

* the core of exception is about jump your code.
  as most situation, you can finish the error where your code fail. 
  some say exception is not necessary in most case.



