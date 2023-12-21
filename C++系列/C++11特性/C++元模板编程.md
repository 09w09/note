# 宏

* **#的功能是将其后面的宏参数进行字符串化操作**
* **##被称为连接符（concatenator），用来将两个Token连接为一个Token**

# `std::declval`

作用：返回其模板参数类型的引用，推断不实际执行的操作的结果类型

# `std::function`

### 1 声明

```c++
#include <functional>
std::function<return_type(arg_types)> functionName;
```

### 2 示例

```c++
#include <iostream>
#include <functional>

void displayMessage() {
    std::cout << "Hello World!" << std::endl;
}

int main() {
    //例子1
    std::function<void()> myFunction = displayMessage;
    myFunction();
    
    //例子2
    std::function<void(int)> lambdaFunction = [](int value) {
    std::cout << "Value: " << value << std::endl;
	};
	lambdaFunction(5);  // 输出: Value: 5
  
    return 0;
}
```

### 3 与类成员函数配合使用

为了与类的成员函数一起使用，您需要使用`std::bind`：

```c++
#include <iostream>
#include <functional>

class MyClass {
public:
    void displayMessage() {
        std::cout << "Message from MyClass!" << std::endl;
    }
};

int main() {
    MyClass obj;
    std::function<void()> myFunction = std::bind(&MyClass::displayMessage, obj);
    myFunction();
    return 0;
}
```

### 4 与仿函数配合使用

```c++
struct FunObj {
    void operator()() {
        std::cout << "Function object called!" << std::endl;
    }
};

std::function<void()> funcObjFunction = FunObj();
funcObjFunction();  // 输出: Function object called!
```

# 可变数量参数用法

### 1.可变参数宏

```c++
// 宏定义PR为printf函数
#ifndef PR
#define PR(pStr, ...) \
	printf(pStr, __VA_ARGS__);
#endif

int main(int argc, char** argv){
	PR("%d, %d\n", 10, 20)
}
```

### 2 .可变参数函数

```c++
#include <cstdarg>
#include <iostream>

double calSum(int count, ...) {
  double result = 0.0;
  va_list args;           //用于存储给定参数列表的信息
  va_start(args, count);  //初始化args以供后续的va_arg宏使用
  for (int i = 0; i < count; ++i) {
    result += va_arg(args, double);  //使用va_arg宏从args中获取下一个参数的值
  }
  va_end(args);  //清理与args相关的任何内存
  return result;
}

int main() {
  std::cout << calSum(5, 1.0, 2.0, 3.0, 4.0, 5.0) << std::endl;  // 输出: 15
  std::cout << calSum(3, 1.1, 2.2, 3.3) << std::endl;            // 输出: 6.6
  return 0;
}
```

### 3. 可变参数模板

* 递归

```c++
#include <iostream>

// 基础函数（递归终止条件）
template <typename T>
T sum(T t) {
  return t;
}

// 可变参数的模板函数
template <typename T, typename... Args>
T sum(T first, Args... args) {
  std::cout << "args size: " << sizeof...(args) << std::endl;
  return first + sum(args...);
}

int main() {
  std::cout << sum(1, 2, 3, 4, 5) << std::endl;  // 输出 15
  std::cout << sum(1.1, 2.2, 3.3) << std::endl;  // 输出 6.6
  return 0;
}
```

* 展开

```c++
template<typename Func, typename... Args>
void expandFunc(const Func& f, Args... args){
	std::initializer_list<int>{(f(std::forward<Args>(args)), 0)...};
}

int main(int argc, char** argv) {
	expandFunc([](int i){std::cout << i << std::endl; }, 1, 2, 3);
}
```

## type_traits

通过type_traits可以实现在编译期计算、查询、判断、转换和选择，提供了模板元编程需要的一些常用元函数。

编译期的常量定义

```c++
using one_type = std::integral_constant<int, 1>;

template<class T>
struct one_type : std::integral_constant<int, 1>{};
```

获取常量

```c++
one_type::value
```

<br>

# 模板

### 模板类

在C++中`class`和`struct`区别仅仅在默认权限上。实际项目中我们更在意的是直观表达，所以对于纯数据类型一般习惯用`struct`，而对于带有操作的一般更习惯用`class`。

但是纯数据类型的模板类在项目中貌似没有多大价值，所以对于模板类来说，`class`和`struct`的使用习惯也有些不同。在模板中用于生成对象的模板类一般习惯于用`class`,而对于模板元编程的静态语言描述中，我们更习惯用`srtuct`。

### 模板函数



### 模板全局常量

模板全局常量一般由一个模板类来引导，因为模板必须在编译期实例化，因此模板全局常量一定会在编译期有一个确切的值，必须由`constexpr`修饰，而不可以是变量。



### 模板类型重命名

### 模板参数

1. 类型
2. 整数（及衍生类型）
3. 模板

### 小数比较

完美转发forward
