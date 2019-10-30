## 条款5：优先选用auto，而非显示型别声明

用auto声明的变量，其型别都推导自其初始产物，所以它们必须初始化。

```cpp
int x1;                     // potentially uninitialized

auto x2;                    // error! initializer required

auto x3 = 0;                // fine, x's value is well-defined
```

 由于auto使用了型别推导，就可以用它来表示只有编译器才能掌握的型别。

```cpp
auto derefUPLess =                        // comparison func.
  [](const std::unique_ptr<Widget>& p1,   // for Widgets
     const std::unique_ptr<Widget>& p2)   // pointed to by
  { return *p1 < *p2; };                  // std::unique_ptrs
```

在C++14中，lambda表达式的形参都可以使用auto：

```cpp
auto derefLess =                          // C++14 comparison
  [](const auto& p1,                      // function for
     const auto& p2)                      // values pointed
  { return *p1 < *p2; };                  // to by anything
                                          // pointer-like
```

### std::function

std::function是C++11标准库中的一个模板，它把函数指针的思想加以推广。

函数指针只能指涉到函数，而std::function却可以指涉到任何可调用对象，即任何可以像函数一样实施调用之物。

想要创建一个函数指针，就必须指定函数的型别（即该指针指涉到的函数的签名），想要创建一个std::function对象就必须指定欲指涉的函数的型别。这一步是通过std::function模板形参来完成的。

所以可以这样来定义func：

```cpp
std::function<bool(const std::unique_ptr<Widget>&,
                   const std::unique_ptr<Widget>&)> func;
```

因为lambda表达式可以产生可调用物对象，std::function对象中就可以储存闭包。这就意味着，C++11中，不用auto也可以声明derefUPLess：

```cpp
std::function<bool(const std::unique_ptr<Widget>&,
                   const std::unique_ptr<Widget>&)>
  derefUPLess = [](const std::unique_ptr<Widget>& p1,
                   const std::unique_ptr<Widget>& p2)
                  { return *p1 < *p2; };
```

抛开词法上的啰嗦和需要指定重复的形参型别，使用std::function和auto还是有所不同。

使用auto声明的、存储着一个闭包的变量和该闭包是同一型别，从而它要求的内存量也和该闭包一样。

而使用std::function声明的、存储着一个闭包的变量是std::function的一个实例，所以不管给定的签名如何，它都占有固定尺寸的内存，而这个尺寸对于其储存的闭包而言不一定够用。这样的话，std::function的构造函数就会分配堆上的内存来储存该闭包。

从结果上看，std::function对象一般都会比使用auto声明的变量使用更多内存。而且，编译器的实现细节一般都会限制内联。并会产生间接函数调用，把这些因素考虑在内的话，通过std::function来调用闭包机会必然会比通过auto声明的变量调用同一闭包来的要慢。

在持有闭包的这场auto和std::function之间的较量中，auto可谓大获全胜（如果再来一场较量，在auto和std::function之间比较持有std::bind的调用结果，则比分则会是一样的）。







