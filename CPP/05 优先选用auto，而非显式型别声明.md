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









