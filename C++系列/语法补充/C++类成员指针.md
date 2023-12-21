> 下面代码的编译器和开发环境 gcc version 11.4.0 (Ubuntu 11.4.0-1ubuntu1~22.04) 

# 1 类内存模型

![](img/类内存模型.png)

1. **类独占内存区域**：实例化对象时，系统会为每一个实例化对象分配存储空间，存储空间包括成员变量和虚函数指针（如果存在虚函数）。

2. **类共享内存区域**：一种类类型编译器只会为该类分配一个内存去存储**类函数代码**和**虚函数表**（如果存在虚函数），也就是说全部的实例化对象共享类成员**类函数代码**和**虚函数表**。

3. **虚函数表**：每一种有虚函数的类中都有一个虚函数表，一个类只需要也只有一个虚函数表，同一个类的所有实例对象都公用同一个虚表。对于基类与派生类，基类有基类的虚函数表，派生类有派生类的虚函数表。

   * 虚函数表中的每个虚函数都会被指派一个固定的索引值。

   * 类中具体的虚函数实际上存储的是虚函数表中相应函数指针的偏移字节。

     ```c++
     void (B::*vFunc1Ptr)() = &B::vfun1;
     void (B::*vFunc2Ptr)() = &B::vfun2;
     
     uint64_t *ptr1 = reinterpret_cast<uint64_t *>(&vFunc1Ptr);
     uint64_t *ptr2 = reinterpret_cast<uint64_t *>(&vFunc2Ptr);
     
     std::cout << "ptr1[0]: " << ptr1[0] << std::endl;
     std::cout << "ptr1[1]: " << ptr1[1] << std::endl;
     std::cout << "ptr2[0]: " << ptr2[0] << std::endl;
     std::cout << "ptr2[1]: " << ptr2[1] << std::endl;
     
     /*************************************************************************
     	输出：
             ptr1[0]: 1
             ptr1[1]: 0
             ptr2[0]: 9
             ptr2[1]: 0
     *************************************************************************/
     ```

4. **虚函数指针**：每一个有虚函数的类至少有一个指针，指向自己所使用的虚函数表。这个函数指针由编译器自动创建，添加到类成为第一个类成员变量，并指向例化类类型的虚函数表，一般称为`*__vptr`。

### 虚函数调用过程

以上面类B和类C为例子，我创建了下面的代码。

```c++
B *p1 = new B();
B *p2 = new C();
p1->vfun2();
p2->vfun2();
```

在C++中多态的调用细节是怎么样的？

1. 实例化类B，并用指针`p1`指向这段内存。B类中系统会创建3个变量：`__vptr`、`m_a`、`m_b`。`__vptr`被赋值为虚函数表B的内存地址。
2. 实例化类C，并用指针`p2`指向这段内存。C类中系统会创建3个变量：`__vptr`、`m_a`、`m_b`。`__vptr`被赋值为虚函数表C的内存地址。
3. `p1`调用`vfun2()`时
   1. 系统通过`p1`指向的B类内存地址中的成员变量`__vptr`查到虚函数表B。虚函数表是一个指针数组，每个元素指向类中每个虚函数的实际地址。
   2. 在虚函数表B中，通过`vfun2()`的偏移量找到B的成员函数`&B::vfun2`的实际地址。
   3. 执行函数`B::vfun2()`。
4. `p2`调用`vfun2()`时，过程和`p1`调用`vfun2()`时是一样的，只不过通过成员变量`__vptr`查到是虚函数表C。

# 2 函数指针

**声明**：`类型 (*指针名)(参数列表) = 函数名;`

**原理**：函数指针在C++中是一个指向函数的指针，在指针变量中存储的是函数的内存地址。因为可以通过指针调用函数，所以使得程序可以在运行时选择要调用的函数。

```c++
int print_hello(int a) {
  std::cout << "Hello, World!";
  return a;
}

int main() {
  //普通函数
  int (*funp1)(int) = &print_hello;  // &可以省略
  int (*funp2)(int) = print_hello;   // 直接使用函数名
  //Lambda 表达式
  void (*funp3)() = []() { std::cout << "no!no!no!" << std::endl; };

  std::cout << funp1(1) << std::endl;
  std::cout << funp2(2) << std::endl;
  funp3();

  return 0;
}
/*************************************************************************
	输出：
        Hello, World!1
        Hello, World!2
        no!no!no!
*************************************************************************/
```



# 3 类成员指针

### 3.1 成员变量指针

**声明**：`类型 类名::*指针名 = &类名::成员变量名;`

**原理**：成员变量指针的值表示从类对象开始地址到成员变量地址的偏移量字节。因为同一个类对成员变量的布局都是相同的，所以成员变量相对于对象的开始地址也是固定的。

```c++
typedef char int8;
typedef short int int16;
typedef int int32;

class A {
 public:
  int8 m_a;
  int16 m_b;
  int32 m_c;
  int m_arr[3];
};

int main() {
  int8 A::*p_a = &A::m_a;
  int16 A::*p_b = &A::m_b;
  int32 A::*p_c = &A::m_c;
  int32(A::*p_arr)[3] = &A::m_arr;

  std::cout << "p_a = " << *(reinterpret_cast<size_t *>(&p_a)) << std::endl;
  std::cout << "p_b = " << *(reinterpret_cast<size_t *>(&p_b)) << std::endl;
  std::cout << "p_c = " << *(reinterpret_cast<size_t *>(&p_c)) << std::endl;
  std::cout << "p_arr = " << *(reinterpret_cast<size_t *>(&p_arr)) << std::endl;
  std::cout << "sizeof(p_a) = " << sizeof(p_a) << std::endl;

  return 0;
}

/*************************************************************************
	输出：
        p_a = 0
        p_b = 2
        p_c = 4
        p_arr = 8
        sizeof(p_a) = 8
*************************************************************************/
```

因此，成员变量指针实际上不是一个常规的指针，他表示的是实例中的字节偏移量。下面我尝试绑定实例对象，通过字节偏移量获取类某个成员变量的实际值。

```c++
A inst;
inst.m_a = 1;
inst.m_b = 2;
inst.m_c = 3;
inst.m_arr[0] = 4;
inst.m_arr[1] = 5;
inst.m_arr[2] = 6;

A *instp = &inst;

std::cout << "inst.*p_b = " << inst.*p_b << std::endl;
std::cout << "instp->*p_b = " << instp->*p_b << std::endl;
std::cout << "(inst.*p_arr)[0] = " << (inst.*p_arr)[0] << std::endl;
std::cout << "(instp->*p_arr)[0] = " << (instp->*p_arr)[0] << std::endl;

/*************************************************************************
	输出：
        inst.*p_b = 2
        instp->*p_b = 2
        (inst.*p_arr)[0] = 4
        (instp->*p_arr)[0] = 4
*************************************************************************/
```



### 3.2 成员函数指针

**声明**：`返回类型 (类名::*指针名)(参数列表) = &类名::成员函数名;`

#### 3.2.1 非静态函数指针

**原理**：非静态成员函数的指针，其值就是指向一块内存地址。（因为同类型的全部实例化的类都是公用一个成员函数的地址）

```c++
class A {
 public:
  void nonVirtualFunc1() {
    std::cout << "A non-virtual function 1" << std::endl;
  }
  void nonVirtualFunc2() {
    std::cout << "A non-virtual function 2" << std::endl;
  }
};

int main() {
    void (A::*nvFunc1Ptr)() = &A::nonVirtualFunc1;
    void (A::*nvFunc2Ptr)() = &A::nonVirtualFunc2;
    
    uint64_t *ptr4 = reinterpret_cast<uint64_t *>(&nvFunc1Ptr);
 	uint64_t *ptr5 = reinterpret_cast<uint64_t *>(&nvFunc2Ptr);
    
    std::cout << "ptr4[0]: 0x" << std::hex << ptr4[0] << std::endl;
    std::cout << "ptr4[1]: " << std::hex << ptr4[1] << std::endl;
    std::cout << "ptr5[0]: 0x" << std::hex << ptr5[0] << std::endl;
    std::cout << "ptr5[1]: " << std::hex << ptr5[1] << std::endl;  
    std::cout << "sizeof(nvFuncPtr) = " << std::dec  << sizeof(vFunc1Ptr) << std::endl;
    
    return 0;
}

/*************************************************************************
	输出：
        ptr4[0]: 0x55b9010e4924
        ptr4[1]: 0
        ptr5[0]: 0x55b9010e4962
        ptr5[1]: 0
        sizeof(nvFuncPtr) = 16
*************************************************************************/
```

但是指针不能直接调用，必须绑定到一个具体的对象才能调用。

```c++
A inst;
A *instp = &inst;

(inst.*nvFunc1Ptr)();
(inst.*nvFunc2Ptr)();
(instp->*nvFunc1Ptr)();
(instp->*nvFunc2Ptr)();

/*************************************************************************
	输出：
        A non-virtual function 1
        A non-virtual function 2
        A non-virtual function 1
        A non-virtual function 2
*************************************************************************/
```

#### 3.2.2 虚拟成员函数指针

**原理**：虚拟成员函数指针的值表示从虚函数表中表头偏移量+1字节到该函数的偏移量字节。

```c++
class A {
 public:
  virtual void virtualFunc1() {
    std::cout << "A virtual function 1" << std::endl;
  }
  virtual void virtualFunc2() {
    std::cout << "A virtual function 2" << std::endl;
  }
  virtual void virtualFunc3() {
    std::cout << "A virtual function 2" << std::endl;
  }
};

int main() {
    void (A::*vFunc1Ptr)() = &A::virtualFunc1;
    void (A::*vFunc2Ptr)() = &A::virtualFunc2;
    void (A::*vFunc3Ptr)() = &A::virtualFunc3;
    
    uint64_t *ptr1 = reinterpret_cast<uint64_t *>(&vFunc1Ptr);
    uint64_t *ptr2 = reinterpret_cast<uint64_t *>(&vFunc2Ptr);
    uint64_t *ptr3 = reinterpret_cast<uint64_t *>(&vFunc3Ptr);
    
    std::cout << "ptr1[0]: " << ptr1[0] << std::endl;
    std::cout << "ptr1[1]: " << ptr1[1] << std::endl;
    std::cout << "ptr2[0]: " << ptr2[0] << std::endl;
    std::cout << "ptr2[1]: " << ptr2[1] << std::endl;
    std::cout << "ptr3[0]: " << ptr3[0] << std::endl;
    std::cout << "ptr3[1]: " << ptr3[1] << std::endl;
    std::cout << "sizeof(vFuncPtr) = " << sizeof(vFunc1Ptr) << std::endl;
    
    return 0;
}
/*************************************************************************
	输出：
        ptr1[0]: 1
        ptr1[1]: 0
        ptr2[0]: 9
        ptr2[1]: 0
        ptr3[0]: 17
        ptr3[1]: 0
        sizeof(nvFuncPtr) = 16
*************************************************************************/
```

但是指针不能直接调用，也必须绑定到一个具体的对象才能调用。

```c++
A inst;
A *instp = &inst;

(inst.*vFunc1Ptr)();
(inst.*vFunc2Ptr)();
(inst.*vFunc3Ptr)();
(instp->*vFunc1Ptr)();
(instp->*vFunc2Ptr)();
(instp->*vFunc3Ptr)();

/*************************************************************************
	输出：
        A virtual function 1
        A virtual function 2
        A virtual function 3
        A virtual function 1
        A virtual function 2
        A virtual function 3
*************************************************************************/
```

# 4 回调对象封装

### 4.1 std::function

**原理**：`std::function` 是 C++11 引入的一种通用的多态函数封装器。它的实例可以存储、复制以及调用任何类型的**可调用对象**。可调用对象包括：普通函数、Lambda 表达式、函数指针、以及拥有 `operator()` 的对象。

```c++
// 普通函数
int fun1(int a, int b) { return a + b; }
// Lambda达式
auto fun2 = [](int a, int b) { return a % b; };
// 仿函数
struct Fun3 {
  int operator()(int a, int b) { return a * b; }
};

int main() {
  //函数指针
  int (*funp)(int, int) = fun1;

  std::function<int(int, int)> funp1 = fun1;
  std::function<int(int, int)> funp2 = fun2;
  std::function<int(int, int)> funp3 = funp;
  std::function<int(int, int)> funp4 = Fun3();
    
  std::cout << funp1(1, 2) << std::endl;
  std::cout << funp2(3, 4) << std::endl;
  std::cout << funp3(5, 6) << std::endl;
  std::cout << funp4(7, 8) << std::endl;

  return 0;
}
/*************************************************************************
	输出：
        3
        3
        11
        56
*************************************************************************/
```

### 4.2 std::bind

**原理**：`std::bind`是一个通用的函数适配器，它允许你将函数调用的参数进行预设，或者改变它们的顺序，从而创建一个新的可调用对象。

```c++
void print(int a, int b, int c) {
  std::cout << a << " " << b << " " << c << std::endl;
}

class Foo {
 public:
  void print_sum(int n1, int n2) { std::cout << n1 + n2 << '\n'; }
  int data = 10;
};

int main() {
  //绑定普通函数
  auto boundFunction1 = std::bind(print, 1, 2, 3);
  boundFunction1();

  //绑定占位符，讲参数1和2的位置调换，并将第3个参数设置默认值
  auto boundFunction2 =
      std::bind(print, std::placeholders::_2, std::placeholders::_1, 3);
  boundFunction2(1, 2);
  boundFunction2(1, 2, 3);

  //仿函数
  Foo foo;
  auto f = std::bind(&Foo::print_sum, &foo, std::placeholders::_1,
                     std::placeholders::_2);
  f(1, 2);

  return 0;
}
/*************************************************************************
	输出：
        1 2 3
        2 1 3
        2 1 3
        3
*************************************************************************/
```

