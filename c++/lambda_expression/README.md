# Lambda Expression / Lambda Function

## Overview

Where ever you use a function pointer, you can use a lambda.

Lambda Expression (或称之为 Lambda Function) 是：
* 首先，它是一个 没有名字的 function (anonymous function)
* 然后，它可以做到 refer to identifiers outside of its own (function) scope
* 它往往是一个一次性使用的 function（所以连名字都不需要），不需要 reuse，所以不必像 functor 或者 function pointer 一样还要煞有介事地定义出来站在那里

## Lambda Expression with no parameter and no return value
```cpp
#include <iostream>

using namespace std;

// void (*pLambdaExpression)() 的形式 很像 call 一个 function pointer，其中：
// 开头的 void 表示 这个 lambda expression 没有 return value，
// 结尾的 () 表示 这个 lamdba expression 没有 parameter，
// 中间的 (*pLambdaExpression) 里，* 表示这是一个指针，pLambdaExpression 这个名字是随意的，可以起任何名字
void runLambdaExpression(void (*pFunc)()) {
    pFunc();
}

int main() {
    // 定义一个 lambda expression 如下
    // 注意，一个 lambda expression 可以说就是 三个括号：一个 []，一个 ()，一个 {}
    auto lambdaExpression = []() { cout << "Hello" << endl; };

    // 直接 run 这个 lambda expression
    lambdaExpression(); // "Hello"
    
    // 用外面定义的 function 来 call 这个 lambda expression
    runLambdaExpression(lambdaExpression); // "Hello"

    // 一条龙，定义+直接call 一个 lambda expression 
    runLambdaExpression([]() {cout << "Hello" << endl; }); // "Hello"

    return 0;
}
```

## Lambda Expression with parameter and no return value
```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

void runLambdaHi(void (*lamHi)(string)) {
    lamHi("Pete");
}

int main()
{
    // 有parameter 没有return value 的 lambda expression.
    auto lambdaHi = [](string name) { cout << "Hi " << name << '!' << endl;};
    
    lambdaHi("Ben"); // "Hi Ben!"
    
    runLambdaHi(lambdaHi); // "Hi Pete!"
    
    return 0;
}
```

## Lambda Expression with parameter and return value
```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

void runLambdaAdd(int (*lamAdd)(int a, int b)) {
    cout << lamAdd(9, 3) << endl;
}

int main()
{
    // 有parameter 也有 return value 的 lambda expression.
    // The `-> int` at the end means this lambda expression returns an int.
    auto lambdaAdd = [](int num1, int num2) -> int {
        return num1 + num2;
    };
    
    cout << lambdaAdd(1, 3) << endl; // 4

    runLambdaAdd(lambdaAdd); // 12
    
    return 0;
}
```

## Capture

### Capture local variables to be input params by value or by reference
```cpp    
// Capture one and two by value
[one, two](){ cout << one << ", " << two << endl; }();

// Capture one by reference, and capture two by value
[&one, two](){ cout << one << ", " << two << endl; }();

// Capture all local variables by value.
[=](){ cout << one << ", " << two << endl; }();

// Capture all local variables by reference.
[&](){ three = 7; two = 8; cout << one << ", " << two << endl; }();
cout << two << endl;

// Capture all local variables by value, but three by reference.
[=, &three](){ three = 7; cout << one << ", " << two << endl; }();
cout << three << endl;

// Default capture all local variables by reference but two and three by value.
[&, two, three](){ one = 100;  cout << one << ", " << two << endl; }();
cout << one << endl;
```

### Capture by value and also change the input variable as a local-lambda-variable by "mutable" Capture
如果 capture by value，就不能再改变被capture的variable，比如如下的code会报错：`expression must be a modifiable lvalue`：
```cpp
int num = 1;
[=](){
    num = 8; // <== 这里会报错，不可修改
}();
```

这种情况下，加一个 `mutable` key word 到 lambda expression 里面的 `(...)` 的后面，就解决了，如下code。**但这么做是一种 bad practice！真要修改input param 的话，就 capture by reference.**
```cpp
int num = 1;
[=]() mutable {
    num = 8;
    cout << num << endl; // 这里是 8
}();
cout << num << endl; // 这里还是原来的 1 !
```
