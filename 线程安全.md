C++ STL如何做到线程安全

STL 语义上不提供**任何强度的线程安全保证**



众所周知，STL容器不是线程安全的。对于vector，即使写方（生产者）是单线程写入，但是并发读的时候，由于潜在的内存重新申请和对象复制问题，会导致读方（消费者）的迭代器失效。实际表现也就是招致了core dump。另外一种情况，如果是多个写方，并发的push_back()，也会导致core dump。





### 线程安全

1. 能同时在不同容器上由不同线程调用所有容器函数。更广泛而言， C++ 标准库函数不读取能通过其他线程访问的对象，除非这些对象能直接或间接地经由函数参数，包含 this 指针访问。
2. 能同时在同一容器上由不同线程调用 const 成员函数。而且，成员函数 `begin()` 、 `end()`, `rbegin()` 、 `rend()` 、 `front()` 、 `back()` 、 `data()` 、 `find()` 、 `lower_bound()` 、 `upper_bound()` 、 `equal_range()` 、 `at()` 和除了关联容器中的 `operator[]` 对于线程安全的目标表现如同 const （即它们亦能同时在同一容器上由不同线程调用）。更广泛而言， C++ 标准库函数不修改对象，除非这些对象能直接或间接地经由函数参数，包含 this 指针访问。
3. 同一容器中不同元素能由不同线程同时修改，除了 std::vector<bool> 的元素（例如， [std::future](https://zh.cppreference.com/w/cpp/thread/future) 对象的 vector 能从多个线程接收值）。
4. 迭代器操作（例如自增迭代器）读但不修改底层容器，而且能与同一容器上的其他迭代器操作同时由 const 成员函数执行。非法化任何迭代器的容器操作修改容器，且不能与任何在既存迭代器上的操作同时执行，即使这些迭代器未被非法化。
5. 同一容器上的元素可以同时由不指定为访问这些元素的函数修改。更广泛而言， C++ 标准库函数不间接读取能从其参数访问的对象（包含容器的其他对象），除非其规定要求如此。
6. 任何情况下，容器操作（还有算法，或其他 C++ 标准库函数）可于内部并行化，只要不更改用户可见的结果（例如 [std::transform](https://zh.cppreference.com/w/cpp/algorithm/transform) 可并行化，但指定了按顺序观览序列的每个元素的 [std::for_each](https://zh.cppreference.com/w/cpp/algorithm/for_each) 不行）

来源：[容器库 - cppreference.com](https://zh.cppreference.com/w/cpp/container)