## 条款6： 当auto推导的型别不符合要求时，使用带显式型别的初始化物的习惯用法

```cpp
std::vector<bool> features(const Widget& w);

Widget w;
…

bool highPriority = features(w)[5];  // is w high priority?
…

processWidget(w, highPriority);      // process w in accord
                                     // with its priority
```

如果给上述代码做一个看似无害的改变，把highPriority的型别改成auto，

```cpp
auto highPriority = features(w)[5];  // is w high priority?
```

虽然代码仍然可以通过编译，但是行为变得不可预期了:

```cpp
processWidget(w, highPriority);      // undefined behavior!
```

processWidget的调用现在会导致未定义行为。

其实改了auto的代码中，highPriority的型别不再是bool了。

从概念上说，std::vector<bool>应该持有的是bool型别的元素，但是std::vector<bool>的opertor[]的返回值并不是容器中的一个元素的引用（对于其他所有形参型别而言，std::vector::operator[]都返回这样的值，单单bool是个例外）。

它返回的是个std::vector<bool>::reference型别的对象(这是一个嵌套在std::vector<bool>)里面的类。

