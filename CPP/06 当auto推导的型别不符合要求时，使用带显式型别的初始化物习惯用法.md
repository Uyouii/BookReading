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

之所以有std::vector<bool>::reference，是因为std::vector<bool>做过特化，用了一种压缩形式表示其持有的bool元素，每个bool元素用一个比特来表示。

这种做法给std::vector<bool>的operator[]带来一个问题，因为按常理来说std::vector<T>的operator[]应该返回一个T&，然而C++中却禁止比特的引用。既然不能返回一个bool&，std::vector<bool>的operator[]转而返回了一个表现得像bool&的对象。实现这个效果的原理是，std::vector<bool>::reference做了一个向bool的隐式型别转换。  