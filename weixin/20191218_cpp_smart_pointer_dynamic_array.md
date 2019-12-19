
# C++ 中用「智能指针」和「动态数组」

`C++` 中没有垃圾回收，动态申请的内存需要手动释放。

- 「智能指针」是 `C++` 中管理动态内存、防止资源泄漏的一种数据结构。

- 「动态数组」指的是通过 `operator new[]` 申请的数组。

「动态数组」是动态内存，并且「智能指针」是用来管理动态内存的。但是它们的合作却不简单。

## 单个动态对象 v.s. 动态数组

内存管理简单来说可以描述为：通过 `new` 来申请内存，通过 `delete` 来释放内存，每个 `new` 对应一个 `delete`。
智能指针利用对象的析构函数来自动调用 `delete` 。

```
char* p = new char('a');
// ...
delete p;
```

问题是单个对象和数组对象所用的操作符是不一样的。上面的代码是单个对象，可以用 `C++` 中的智能指针 `auto_ptr` `shared_ptr` `unique_ptr`来正确管理。

而对于动态数组的 `operator new[]` 需要通过 `operator delete[]` 来释放内存，这时智能指针就不一定能正确工作了。

## 如何用「智能指针」管理「动态数组」

0. `std::vector`

最直接的办法就是不用动态数组，而使用 `std::vector` 来代替。这样就不需要手动管理内存。

1. `boost::shared_array`

`Boost` 中针智能指针 `shared_array` 专门为解决这个问题而生。如果你的项目中使用了 `Boost` 库，这会是一个轻松的解决方法。

```C++
boost::shared_ptr<int[]> p(new int[10]);
```

2. 指定 `shared_ptr` 的 `deleter`

在 `C++17` 以前，`shared_ptr` **不可以**直接用来管理动态数组，但是可以通过指定 `deleter` 的方式来使用。

```C++
std::shared_ptr<int> p(new int[10], std::default_delete<int[]>());
```

需要注意的是 `shared_ptr<>` 中的类型是 `int`，而 `default_delete<>` 中的类型是 `int[]`。

3. `C++11` 中的 `unique_ptr`

如果你的项目中使用了 `C++11`，那么可以用 `unique_ptr` 可以用来管理动态数组。

```C++
std::unique_ptr<int[]> p(new int[10]);
```

注意 `unique_ptr<>` 中的类型是 `int[]`。另外使用时请注意 `unique_ptr` 的特点。

4. `C++17` 中的 `shared_ptr`

终于，从 `C++17` 开始， `shared_ptr` 可以用于管理动态数组。

```C++
std::shared_ptr<int[]> p(new int[10]);
```

注意 `shared_ptr<>` 中的类型是 `int[]`。

【完】

**References:**
```
https://stackoverflow.com/questions/13061979/shared-ptr-to-an-array-should-it-be-used
https://en.cppreference.com/w/cpp/memory/unique_ptr
https://www.boost.org/doc/libs/1_61_0/libs/smart_ptr/shared_array.htm
```




