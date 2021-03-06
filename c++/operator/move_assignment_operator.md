# Move Assignment Operator

Move Assignment Operator 的目的是 **偷窃或者说挪用 他者 正在拥有的 allocated memory**，所以就不必再 allocate 一遍 memory。这样就：
* 节约了space
* 避免了对各种东西进行copy的时间消耗
* 之后在 run destructor 的时候，也不必 de-allocate 2次memory，而只用 de-allocate 一次

被挪用的 他者 是某个RValue，比如说某个 temporary variable。
Move Assignment Operator 和 RValue Reference 紧密相关，可以参考我关于 RValue Reference 的 note。

如果class里还有 overloaded assignment operator 的话，move assignment operator 会和它长得有点像，要区分开：
```cpp
class Person {
    ...
    
    // Overloaded assignment operator.
    Person & operator = (const Person & other) {
        ...
        return *this;
    }

    // Move assignment operator.
    Person & operator = (Person && other) { // The input param is a RValue Reference
        ...
        return *this;
    }
}
```

完整的示例code：
```cpp

```

