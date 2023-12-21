# 三、泛型编程

## 1 模板

### 1.1 模板概念

* **作用**：主要提高代码的复用率。可以使得函数或类在对应不同的类型（types）的时候正常工作，而无需为每一种类型分别写一份代码。
  * C++的另一种编程思想泛型编程，主要利用的计算就是模板。
  
  * C++提供两种模板机制：函数模板和类模板。
  
* **模板的声明**（declaration）：其实并未给出一个函数或者类的完全定义（definition），只是提供了一个函数或者类的语法框架（syntactical skeleton）。

* **模板实例化**：是指从模板构建出一个真正的函数或者类的过程。用具体类型代替模板参数的过程叫做实例化；从而产生一个模板实例。
  * 如果实例化一种类型，而该类型并不支持函数所使用的操作，那么就会导致一个编译错误
  
* 实例化有两种类型

  1. 显示实例化：在代码中明确指定要针对哪种类型进行实例化。
  2. 隐式实例化：在首次使用时根据具体情况使用一种合适的类型进行实例化。

### 1.2 函数模板

* **作用**：建立一个通用的函数，其返回值和参数类型可以不具体制定，使用一个符号代替。

#### 1.2.1 基本语法

* 使用示例

  ```c++
  /*
  	template 声明创建模板
  	typename 用于自订后面所更符号为一种数据类型（可以用class代替）
  	T 通用数据类型（通常为大写字母）
  */
  template<typename T>
  void swap(T &a,T &b){
      T t = a;
      a = b;
      b = t;
  }
  
  int main()
  {
      int a = 99;
      int b = 11;
      char c = 'a';
      
      // 自动推导类型
      swap(a,b);
      swap(a,c);	//错误！推导出不一致的T类型
      
      // 手动指定类型
      swap<int>(a,b);
      
      return 0;
  }
  ```

* 模板必须要确定出模板T数据类型，才能使用

  ```c++
  template<typename T>
  void fun()
  {
      cout << "调用fun()" << endl;
  }
  
  fun();			//错误！
  fun<int>();
  ```

* **案例1**：利用模板制作一个可以对不同数据类型数组排序的模板。

  ```c++
  template<class T>
  void sort(T arr[],int len)
  {
  	for(int i=0;i<len;i++){
  		int max = i;
          for(int j=i+1;j<len;j++){
              if(arr[max]<arr[j]){
                  max = j;
              }
          }
          if(max != i){
              T t = arr[max];
              arr[max] = arr[i];
              arr[i] = t;
          }
  	}
  }
  ```

#### 1.2.2 普通函数&函数模板

* **普通函数和函数模板的区别**

  * 普通函数调用能发生自动类型转换（隐式转换）。

    ```c++
    int func1(int a){
        cout << "普通函数 " << a << endl;
        return a;
    }
    
    int a = 199;
    char c = 'a';
    func1(a);		//输出：普通函数 199
    func1(c);		//输出：普通函数 97（此处发生了隐式转换）
    ```

  * 函数模板调用，如果利用自动类型推导，则不会发生隐式类型转换。

    ```c++
    template<class T>
    T func2(T a,T b){
        cout << "模板函数 " << b << endl;
        return a;
    }
    
    int a = 99;
    char c = 'a';
    func2(a,c);		//错误！推导出不一致的T类型
    ```

  * 如果利用显示指定类型的方法，可以发生隐式类型转换。

    ```c++
    func2<int>(a,c);		//正确！显示指定会进行隐式转换
    ```

* **总结**：建议使用显示指定类型的方式调用函数模板，因为可以自己确定通用类型。

* **普通函数和模板函数调用规则**

  * 如果函数模板和普通函数都可以实现，优先调用普通函数。

    ```c++
    void func(int a){
        cout << "普通函数 " << a << endl;
    }
    
    template<class T>
    void func(T a){
        cout << "模板函数 " << a << endl;
    }
    
    int a = 99;
    func(a);		//输出：普通函数 99
    ```

  * 可以通过空模板`<>`参数列表来强制调用函数模板。

    ```c++
    func<>(a);		//输出：模板函数 99
    ```

  * 函数模板也可以发生重载

    ```c++
    template<class T>
    void func(T a,T b){
        cout << "模板函数 " << a << b << endl;
    }
    
    func(a,88);	//输出：模板函数 9988
    ```

  * 如果函数模板可以产生更好的匹配，优先调用函数模板。

    ```c++
    char c1 = 'a';
    func(c);	//输出：模板函数 a
    ```

* **总结**：提供了模板函数，就尽量不要提供普通函数，以免造成二义性。

#### 1.2.3 局限性

* 例如下面例子：传入一个数组就无法实现了

  ```c++
  template<class T>
  void func1(T& a,T& b){
      a = b;
  }
  
  template<class T>
  void func2(T a,T b){
      if(a>b){
          //...
      }
  }
  ```

* 为了解决上述问题，C++提供模板重载。

  ```c++
  //为数组准备的重载
  template<class T>
  void func1(T arr1[],T arr2[],int len){
      for(int i=0;i<len;i++){
          arr1[i] = arr2[i];
      }
  }
  ```

* 不仅如此，还可以为特定类型提供具体化模板。

  ```c++
  //利用具体化某一类型，来有限调用
  template<>
  void func1(Point& p1,Point& p2){
      p1.x = p2.x;
      p1.y = p2.y;
  }
  ```

* **总结**：具体化模板可以解决特殊情况的问题。

### 1.3 类模板

#### 1.3.1 基本语法

* **作用**：建立一个类数据成员类型可以不具体制定的通用类。

  ```c++
  /*
  	template 声明创建模板
  	typename 用于自订后面所更符号为一种数据类型（可以用class代替）
  	T 通用数据类型（通常为大写字母）
  */
  template<typename T>
  class Person{
    public:
      Person(T name):m_name(name){
      }
      
      T m_name;
  };
  
  Person<string> p1("美国队长");
  ```

* 类模板无法进行自动类型推导

  ```c++
  Person p1(1231);	//错误！
  ```

* 类模板可以有默认类别参数

  ```c++
  /*
  	template 声明创建模板
  	typename 用于自订后面所更符号为一种数据类型（可以用class代替）
  	T 通用数据类型（通常为大写字母）
  */
  template<typename T = string>
  class Person{
    public:
      Person(T name):m_name(name){}
      
      T m_name;
  };
  
  Person<> p1("美国队长");
  ```

* 类模板中的成员函数，在需要调用模板时才创建。

#### 1.3.2 类模板对象做函数参数

* **作用**：类模板实例化的对象，向函数传参的方式。

  ```c++
  template<class T1,class T2>
  class Person{
    public:
      Person(T1 name,T2 age)
      : m_name(name),m_age(age){}
      
      void show(){
          cout << "姓名：" << m_name << "年龄：" << m_age << endl;
      }
      
      T1 m_name;
      T2 m_age;
  };
  ```

  * 指定传入类型

  ```c++
  void printPerson(Person<string,int> &p){
      p->show();
  }
  
  Person<string,int> p("美国队长",99);
  ```

  * 参数模板化

  ```c++
  template<class T1,class T2>
  void printPerson(Person<T1,T2> &p){
      p.show();
      cout << "T1的类型为：" << typeid(T1).name() << endl;
      cout << "T2的类型为：" << typeid(T2).name() << endl;
  }
  
  Person<string,int> p("美国队长",100);
  printPerson(p);
  ```

  * 整个类模板化

  ```c++
  template<class T>
  void print(T &p){
      p.show();
  }
  
  Person<string,int> p("美国队长",100);
  printPerson(p);
  ```

#### 1.3.3 类模板继承

* 类模板继承规则

  ```c++
  template<class T>
  class Base{
    T m;  
  };
  
  //当继承一个模板类时，子类不是模板类时，父类必须要指定具体类型。
  class Son : public Base<int>{
      int a;
  };
  
  //如果不指定父类类型，则子类也必须为模板类。
  template<class T1,class T2>
  class Son2 : public Base<T2>{
      T1 a;
  }
  ```

#### 1.3.4 类成员函数

* 类成员函数类外实现

  ```c++
  template<class T1,class T2>
  class Person{
  public:
  	Person(T1 name,T2 age);
      void show();
      
      T1 m_name;
      T2 m_age;
  }
  
  template<class T1,class T2>
  Person<T1,T2>::Person(T1, name,T2 age){
      m_name = name;
      m_age = age;
  }
  
  template<class T1,class T2>
  void Person<T1,T2>::show()
  {
      cout << "姓名：" << m_name << "年龄：" << m_age << endl;
  }
  ```

* 类模板成员函数分文件编写。

  ```c++
  //////////////////////////////////////////////////////////
  //person.h
  template<class T1,class T2>
  class Person{
  public:
  	Person(T1 name,T2 age);
      void show();
      
      T1 m_name;
      T2 m_age;
  }
  
  //////////////////////////////////////////////////////////
  //person.cpp
  template<class T1,class T2>
  Person<T1,T2>::Person(T1, name,T2 age){
      m_name = name;
      m_age = age;
  }
  
  template<class T1,class T2>
  void Person<T1,T2>::show()
  {
      cout << "姓名：" << m_name << "年龄：" << m_age << endl;
  }
  ```

* **问题**：类模板中成员函数创建时机是在调用阶段，导致分文件编写无法连接到。

* **解决**：

  * 方法一：直接包含`.cpp`文件。

  ```c++
  #include "person.cpp"
  
  Person<string,int> p("美国队长",99);
  p.show();
  ```

  * 方法二：将声明`.h`和实现`.cpp`写到同一个文件，并更改后缀为`.hpp`。（不强制是`.hpp`，也可以为`.h`）

#### 1.3.5 类模板友元

* **作用**：类的友元函数实现

  ```c++
  template<class T1,class T2>
  class Person{  
      //全局函数类内实现
      friend void printPerson1(Person<T1,T2> p){
          cout << "1 姓名：" << p.m_name << "年龄：" << p.m_age << endl;
      }
      //全局函数类外实现
      friend void printPerson2(Person<T1,T2> p);
      
    public:
      Person(T1 name,T2 age)
      : m_name(name),m_age(age){}
      
      void show(){
          cout << "姓名：" << m_name << "年龄：" << m_age << endl;
      }
    
    private:
      T1 m_name;
      T2 m_age;
  };
  
  template<class T1,class T2>
  void printPerson2(Person<T1,T2> &p){
     cout << "2 姓名：" << p.m_name << "年龄：" << p.m_age << endl;
  }
  ```

#### 1.3.6 类模板案例——数组

* **案例**：实现一个通用数组类。

  ```c++
  template <class T>
  class Array {
   public:
    //构造函数，可以自定义容量
    Array(int capacity = 10) {
      m_capacity = capacity;
      m_length = 0;
      m_pAddr = new T[capacity];
    }
  
    //拷贝构造函数，进行深拷贝
    Array(const Array& arr) {
      m_capacity = arr.m_capacity;
      m_length = arr.m_length;
  
      //深拷贝，并将数据复制过来
      m_pAddr = new T[m_capacity];
      for (int i = 0; i < m_length; i++) {
        m_pAddr[i] = arr.m_pAddr[i];
      }
    }
  
    //析构函数，及时释放堆区数据
    ~Array() {
      if (m_pAddr != nullptr) {
        delete[] m_pAddr;
        m_pAddr = nullptr;
      }
    }
  
    //赋值运算符重载，进行深拷贝赋值
    Array& operator=(const Array& arr) {
      //如果原先有数据先释放
      if (m_pAddr != nullptr) {
        delete[] m_pAddr;
      }
  
      m_capacity = arr.m_capacity;
      m_length = arr.m_length;
  
      //深拷贝，并将数据复制过来
      m_pAddr = new T[m_capacity];
      for (int i = 0; i < m_length; i++) {
        m_pAddr[i] = arr.m_pAddr[i];
      }
      return *this;
    }
  
    //向数组尾部添加数据
    void pushBack(const T& val) {
      // 1. 判断容量释放充足，不够则扩容
      if (m_capacity <= m_length) {
        m_capacity *= 2;
        // 1.1创建新堆区，将旧堆区数据移入
        T* pTemp = new T[m_capacity];
        for (int i = 0; i < m_length; i++) {
          pTemp[i] = m_pAddr[i];
        }
        // 1.2删除旧堆区
        delete[] m_pAddr;
        m_pAddr = pTemp;
      }
  
      // 2.向尾部添加数据
      m_pAddr[m_length++] = val;
    }
  
    //删除数组尾部数据
    void popBack() {
      if (m_length == 0) {
        return;
      }
      m_length--;
    }
  
    //通过下标方式访问数组中的元素
    T& operator[](const int index) { return m_pAddr[index]; }
  
    //返回当前容器
    int capacity() { return m_capacity; }
  
    //返回数组长度
    int length() { return m_length; }
  
   private:
    T* m_pAddr;      //数组开辟地址空间的首地址
    int m_capacity;  //数组容量
    int m_length;    //数组长度
  };
  
  int main() {
    std::string str;
    Array<std::string> arr;
    arr.pushBack("abc");
    arr.pushBack("def");
    arr.pushBack("gji");
    arr.pushBack("hfgtrh");
    arr.pushBack("qwe");
    arr.pushBack("12");
    arr.pushBack("34");
    arr.pushBack("56");
    arr.pushBack("78");
    arr.pushBack("90");
    arr.pushBack("-=");
    arr.popBack();
  
    std::cout << "length:" << arr.length() << std::endl;
    std::cout << "capacity:" << arr.capacity() << std::endl;
    for (int i = 0; i < arr.length(); ++i) {
      std::cout << "arr[" << i << "] = " << arr[i] << std::endl;
    }
  
    return 0;
  }
  ```

<br>

## 2 STL初始

### 2.1 基本概念

* STL（Standard Template Library），即标准模板库，是一个高效的C++程序库，包含了诸多常用的基本数据结构和基本算法。
* **基本观念**：将数据和操作分离。数据由容器进行管理，操作则由算法进行，而迭代器在两者之间充当粘合剂，使任何算法都可以和任何容器交互运作。

### 2.2 六大组件

1. **容器**：提供各种数据类型。比如：`vector`、`list`、`deque`、`set`、`map`等。
2. **迭代器**：遍历容器，或与算法之间的胶合剂。
3. **算法**：提供各种常用算法。比如`sort`、`find`、`copy`、`for_each`等。
4. **仿函数**：行为类似函数，可作为算法的某种策略。
5. **适配器**：用来修饰容器、仿函数或迭代器的接口。
6. **空间配置器**：负责空间的配置与管理。

### 2.3 STL主要部分

#### 2.3.1 容器

* **作用**：用来管理某类对象的集合。STL容器将运用最广泛的一些数据结构实现了出来。
* **常用容器**：数组、链表、栈、树、队列、集合、映射表等。
  * 序列式容器：强调值的排序，容器里每个元素都有固定的位置。
  * 关联式容器：二叉树结构，各元素间没有严格的物理顺序关系。

#### 2.3.2 迭代器

* **作用**：用来在一个对象集合的元素上进行遍历动作。

  | 种类       | 功能                               | 支持运算                          |
  | ---------- | ---------------------------------- | --------------------------------- |
  | 输入迭代器 | 只读访问                           | 支持++、==、!=                    |
  | 输出迭代器 | 只写操作                           | 支持++                            |
  | 前向迭代器 | 读写操作，并能向前推进迭代器       | 支持++、==、!=                    |
  | 双向迭代器 | 读写操作，并能向前和向后推进迭代器 | 支持++、--                        |
  | 随机迭代器 | 读写操作，并能跳跃方式访问任意数据 | 支持++、--、[n]、-n、<、<=、>、>= |

* 常用容器中迭代器种类为双向迭代器或随机访问迭代器。

#### 2.3.3 算法

* **作用**：用来处理对象集合中的元素。我们只需撰写一次算法，就可以将它应用于任意容器之上，这是因为所有容器的迭代器都提供一致的接口
* **算法分类**：
  * 质变算法：指运算过程中会更改区间内的元素内容。比如拷贝、替换、删除等。
  * 非质变算法：指运算过程中不会更改区间内的元素内容。比如查找、计数、遍历、寻极值等。

### 2.4 名称介绍

#### 2.1.1 顺序性容器

* 顺序性容器就是将一组具有相同类型的元素以严格的线性形式组织起来

| 容器         | 简介说明                                                     |
| ------------ | ------------------------------------------------------------ |
| vector       | 可变大小数组。相当于数组，可动态构建，支持随机访问，无头插和尾插，仅支持inset插入，除尾部外的元素删除比较麻烦。但使用最为广泛 |
| deque        | 双端队列。支持头插、删，尾插、删，随机访问较vector容器来说慢,但对于首尾的数据操作比较方便 |
| list         | 双向循环链表。使用起来很高效，对于任意位置的插入和删除都很快，在操作过后，以后指针、迭代器、引用都不会失效 |
| forward_list | 单向链表。只支持单向访问，在链表的任何位置进行插入/删除操作都非常快 |
| array        | 固定数组。vector的底层即为array数组，它保存了一个以严格顺序排列的特定数量的元素 |

#### 2.2 关联式容器

* 关联式容器每一个元素都有一个键值（key），对于二元关联容器，还拥有实值（value）容器中的元素顺序不能由程序员来决定，有set（集合）和map（映射）这两大类，它们均是以RB-Tree（red-black tree，红黑树）为底层架构。
* 有序容器，底层用红黑树实现。

| 容器         | 简介说明                                                     |
| ------------ | ------------------------------------------------------------ |
| set/mutliset | 集合/多重集合。对于set，在使用insert插入元素时，已插入过的元素不可重复插入，这正好符合了集合的互异性，在插入完成显示后，会默认按照升序进行排序，对于multiset，可插入多个重复的元素 |
| map/mutlimap | 映射/多重映射。二者均为二元关联容器（在构造时需要写两个参数类型，前者对key值，后者对应value值），因为有两个参数，因此在插入元素的时候需要配合对组pair进行插入，具体见深入详解 |

* 无序容器，底层用hash表实现。

| 容器                             | 简介说明           |
| -------------------------------- | ------------------ |
| unordered_set/unordered_mutliset | 无序，用hash表组织 |
| unordered_map/unordered_mutlimap | 无序，用hash表组织 |

#### 2.3 容器适配器

* 容器适配器是一个封装了序列容器的一个类模板=，它在一般的序列容器的基础上提供了一些不同的功能。之所以称为容器适配器，是因为它是适配容器来提供其它不一样的功能。通过对应的容器和成员函数来实现我们需要的功能

| 容器           | 简介说明                                                     |
| -------------- | ------------------------------------------------------------ |
| stack          | 堆栈。其原理是先进后出（FILO），其底层容器可以是任何标准的容器适配器，默认为deque双端队列 |
| queue          | 队列。其原理是先进先出（FIFO），只有队头和队尾可以被访问，故不可有遍历行为，默认也为deque双端队列 |
| pirority_queue | 优先队列。它的第一个元素总是它所包含的元素中优先级最高的，就像数据结构里的堆，会默认形成大堆，还可以使用仿函数来控制生成大根堆还是生成小根堆，若没定义，默认使用vector容器 |

<br>

## 3 STL常用容器

### 3.1 string字符串

* **功能**：字串串管理类。
* **本质**：`string`是一个c++类，和`char*`相比提供了更便捷的接口。而且不用担心`char*`数组越界等问题。

#### 3.1.1 构造函数

```c++
string();					//空字符串
string(const char* s);		//使用字符串数组构造string
string(int n,char c);		//使用n个字符c初始化
string(const string& str);	//拷贝构造函数
```

#### 3.1.2 赋值操作

```c++
string& operator=(const char* s);		//char*类型字符串 赋值给当前字符串
string& operator=(const string &s);		//string类型字符串 赋值给当前字符串
string& operator=(char c);				//char类型字符 赋值给当前字符串
string& assign(const char* s);			//字符串s 赋给当前字符串
string& assign(const char* s,int n);	//字符串s的前n个字符 赋值给当前字符串
string& assign(const string &s);		//字符串s 赋值给当前字符串
string& assign(int n,char c);			//用n个字符c 赋值给当前字符串
```

#### 3.1.3 拼接操作

```c++
string& operator+=(const char* str);
string& operator+=(const char c);
string& operator+=(const string& str);
string& append(const char *s);			//把字符串s 连接到当前字符串结尾
string& append(const char *s,int n);	//把字符串s的前n个字符 连接到当前字符串结尾
string& append(const string &s);		//把字符串s 连接到当前字符串结尾
string& append(const string &s,int pos,int n);	//字符串从pos开始的n个字符连接到字符串结尾
```

#### 3.1.4 查找和替换

```c++
//从左往右查
int find(const string& str, int pos = 0) const;		//查找str第一次出现位置,从pos开始查找
int find(const char* s, int pos = 0) const;			//查找s第一次出现位置,从pos开始查找
int find(const char* s, int pos, int n) const;		//从pos位置查找s的前n个字符第一次位置
int find(const char c, int pos = 0) const;			//查找字符c第一次出现位置

//从右往左查
int rfind(const string& str, int pos = npos) const;	//查找str最后一次位置,从pos开始查找
int rfind(const char* s, int pos = npos) const;		//查找s最后—次出现位置,从pos开始查找
int rfind(const char* s, int pos， int n) const;		//从pos查找s的前n个字符最后一次位置
int rfind(const char c, int pos = 0) const;			//查找字符c最后一次出现位置

//替换字符
string& replace(int pos, int n, const string& str);	//替换从pos开始n个字符为字符串str
string& replace(int pos, int n,const char* s);		//替换从pos开始的n个字符为字符串s
```

#### 3.1.5 比较

* 字符串比较是按字符的ASCII码进行对比

  * 等于= 返回 0
  * 大于> 返回 1
  * 小于< 返回 -1

* 函数原型

  ```c++
  int compare(const string &s) const;		
  int compare(const char *s) const;		
  ```

* 示例程序

  ```c++
  string str1 = "hello";
  string str2 = "hello";
  string str3 = "iello";
  string str4 = "gello";
  
  cout << str1.compare(str2) << endl;		// 等于 输出  0
  cout << str1.compare(str3) << endl;		// 小于 输出 -1
  cout << str1.compare(str4) << endl;		// 大于 输出  1
  ```

#### 3.1.6 存取

```c++
char& operator[](int n);		//[]存取字符
char& at(int n);				//at()存取字符
```

#### 3.1.7 插入和删除

```c++
string& insert(int pos,const char* s);		//在pos位置插入字符串
string& insert(int pos,const string& str);	//同上
string& insert(int pos,int n,char c);		//在pos位置插入n个字符c
string& erase(int pos,int n = npos);		//删除从pos开始的n个字符
```

#### 3.1.8 子串

* 从字符中获取想要的字串

```c++
string substr(int pos = 0,int n = npos);	//返回pos开始的n个字符组成的字符串
```

### 3.2 vector向量

* **概要**：顺序容器（支持随机访问），动态调整大小，使用内存分配器动态管理内存，连续内存。
* **描述**： 此容器是一种序列式容器，事实上和数组差不多，但它比数组更优越。一般来说数组不能动态拓展，因此在程序运行的时候不是浪费内存，就是造成越界。而 vector 正好弥补了这个缺陷，它的特征是相当于可拓展的数组（动态数组），它的随机访问快，在中间插入和删除慢，但在末端插入和删除快。
* **动态扩展**：并不是在原空间之后接新空间，而是新创建一个更大的空间（原空间的2倍）将原来的数据拷贝进去，然后释放原空间。
* **特点**：
  * 拥有一段连续的内存空间，并且起始地址不变，因此它能非常好的支持随机存取，即 [] 操作符，但由于它的内存空间是连续的，所以在中间进行插入和删除会造成内存块的拷贝，另外，当该数组后的内存空间不够时，需要重新申请一块足够大的内存并进行内存的拷贝。这些都大大影响了 vector 的效率。
  * 对头部和中间进行插入删除元素操作需要移动内存，如果你的元素是结构或类，那么移动的同时还会进行构造和析构操作，所以性能不高。
  * 对最后元素操作最快（在后面插入删除元素最快），此时一般不需要移动内存，只有保留内存不够时才需要。

* **优点：**支持随机访问，查询效率高。


* **缺点：**当向其头部或中部插入或删除元素时，为了保持原本的相对次序，插入或删除点之后的所有元素都必须移动，所以插入的效率比较低。
* **适用场景**：适用于对象简单，变化较小，并且频繁随机访问的场景。

#### 3.2.1 构造函数

```c++
vector<T> v;		
vector(begin,end);			//将v[begin():end()]区间元素拷贝出来
vector(n,T());				//将n个T()拷贝出来
vector(const vector &vec);	//拷贝构造函数
```

#### 3.2.2 赋值和交换

```c++
vector& operator=(const vector &vec);	//赋值运算符重载
assign(begin,end);						//将v[begin():end()]区间元素拷贝给本身
assign(n,T());							//将n个T()拷贝给本身

v1.swap(v2);		//v1和v2元素进行交换
```

#### 3.2.3 容量和大小

```c++
empty();		//容器是否为空
capacity();		//容器的容量
size();			//返回容器中元素的个数
resize(int num);//重新指定容器的长度。若变长：以默认值填充新位置。若变短，删除超出长度数据
resize(int num,T());	//同上，只是在边长时以T()填充新位置
```

#### 3.2.4 插入和删除

```c++
push_back(T());						//尾部插入一个元素
pop_back();							//删除最后一个元素

insert(const_iterator pos,T());			//迭代器指向位置插入元素
insert(const_iterator pos,int n,T());	//迭代器指向位置插入n个元素

erase(const_iterator pos);						//删除迭代器指向元素
erase(const_iterator start,const_iterator end);	//删除迭代器之间的元素
clear();	//清除容器中全部元素
```

#### 3.2.5 数据存取

```C++
T& operator[](int n);		//[]存取字符
T& at(int n);				//at()存取字符
T& front();					//返回容器中第一个元素
T& back();					//返回容器中最后一个元素
```

#### 3.2.6 预留空间

* **作用**：减少vector动态扩容的次数。

```c++
//设置len个元素长度预留空间，预留位置不初始化，且无法访问
reserve(int len);
```

### 3.3 deque双端队列

* **概要**：顺序容器（支持随机访问），动态调整大小，使用内存分配器动态管理内存，分段连续内存。
* **描述**：deque（double-ended queue）是由一段一段的定量连续空间构成。一旦要在 deque 的前端和尾端增加新空间，便配置一段定量连续空间，串在整个 deque 的头端或尾端。因此不论在尾部或头部安插元素都十分迅速。 在中间部分安插元素则比较费时，因为必须移动其它元素。deque 的最大任务就是在这些分段的连续空间上，维护其整体连续的假象，并提供随机存取的接口。

![deque原理](img\deque原理.png)

* **特点**：
  * 按页或块来分配存储器的，每页包含固定数目的元素。
  * deque 是 list 和 vector 的折中方案。兼有 list 的优点，也有vector 随机线性访问效率高的优点。

* **优点**：支持随机访问，即 [] 操作和 .at()，所以查询效率高；可在双端进行 pop，push。
* **缺点**：不适合中间插入删除操作；占用内存多。
* **适用场景**：适用于既要频繁随机存取，又要关心两端数据的插入与删除的场景。

#### 3.3.1 构造函数

```c++
deque<T> d;
deque(begin,end);			//将v[begin():end()]区间元素拷贝出来
deque(n,T());				//n个T()构造
deque(const deque &deq);	//拷贝构造函数
```

#### 3.3.2 赋值操作

```c++
deque& operator=(const deque &deq);	
assign(beign,end);
assign(n,T());
```

#### 3.3.3 大小操作

```c++
empty();		//容器是否为空
size();			//返回容器中元素的个数
resize(int num);//重新指定容器的长度。若变长：以默认值填充新位置。若变短，删除超出长度数据
resize(int num,T());	//同上，只是在边长时以T()填充新位置
```

#### 3.3.4 插入和删除

```c++
push_back(T());		//在尾部插入一个元素
push_front(T());	//在头部插入一个元素
pop_back(T());		//删除尾部一个元素
pop_front(T());		//删除头部一个元素

insert(pos,T());		//在pos位置插入一个T(),并返回新元素位置
insert(pos,n,T());		//在pos位置的插入n个T()，无返回值
insert(pos,beign,end);	//在pos位置的插入[begin:end]，无返回值

clear();			//清空容器全部数据
erase(begin,end);	//删除[begin:end]，并返回下一个数据的位置
erase(pos);			//删除pos位置的数据，并返回下一个数据的位置
```

#### 3.3.5 数据存取

```c++
T& operator[](int n);		//[]存取字符
T& at(int n);				//at()存取字符
T& front();					//返回容器中第一个元素
T& back();					//返回容器中最后一个元素
```

#### 3.3.6 容器排序

* 利用算法`sort(iterator begin,iterator end)`对区间内元素进行排序。

### 3.4 list链表

* **概要**：顺序容器（可顺序访问，但不支持随机访问），双链表，使用内存分配器动态管理内存，离散内存。
* **描述**：List 由双向链表（doubly linked list）实现而成，元素也存放在堆中，每个元素都是放在一块内存中，他的内存空间可以是不连续的，通过指针来进行数据的访问，这个特点使得它的随机存取变得非常没有效率，因此它没有提供 [] 操作符的重载。但是由于链表的特点，它可以很有效率的支持任意地方的插入和删除操作。

![deque原理](img\list原理.png)

* **特点**：
  - 没有空间预留习惯，所以每分配一个元素都会从内存中分配，每删除一个元素都会释放它占用的内存。
  - 在哪里添加删除元素性能都很高，不需要移动内存，当然也不需要对每个元素都进行构造与析构了，所以常用来做随机插入和删除操作容器。
  - 访问开始和最后两个元素最快，其他元素的访问时间一样。
* **优点**：内存不连续，动态操作，可在任意位置插入或删除且效率高。
* **缺点**：不支持随机访问。
* **适用场景**：适用于经常进行插入和删除操作并且不经常随机访问的场景。

#### 3.6.1 构造函数

```c++
list<T> lst;
list(begin,end);		//将list[begin():end()]区间元素拷贝出来
list(n,T());			//将n个T()拷贝出来
lisr(const list &lst);	//拷贝构造函数
```

#### 3.6.2 赋值和交换

```c++
list& operator=(const list &lst);//赋值运算符重载
assign(begin,end);				//将list[begin():end()]区间元素拷贝给本身
assign(n,T());					//将n个T()拷贝给本身

lst1.swap(lst2);				//lst1和lst2元素交换
```

#### 3.6.3 大小操作

```c++
empty();		//容器是否为空
size();			//返回容器中元素的个数
resize(int num);//重新指定容器的长度。若变长：以默认值填充新位置。若变短，删除超出长度数据
resize(int num,T());	//同上，只是在边长时以T()填充新位置

size_type max_size() const;			// 返回list对象最大允许容量
```

#### 3.6.4 插入和删除

```c++
push_back(T());						//尾部插入一个元素
pop_back();							//删除最后一个元素
push_front();						//首部插入一个元素
pop_front();						//删除第一个元素

insert(const_iterator pos,T());			//迭代器指向位置插入元素
insert(const_iterator pos,int n,T());	//迭代器指向位置插入n个元素
insert(const_iterator pos,begin,end);	//迭代器指向位置插入[begin():end()]区间元素

erase(const_iterator pos);						//删除迭代器指向元素
erase(const_iterator start,const_iterator end);	//删除迭代器之间的元素
clear();		//清除容器中全部元素
remove(T());	//删除容器中全部与T()匹配的元素
```

#### 3.6.5 数据存取

```c++
T& front();					//返回容器中第一个元素
T& back();					//返回容器中最后一个元素
```

#### 3.6.6 反转和排序

```c++
sort(iterator begin,iterator end);		//排序
reverse();								//反转链表顺序
```

### 3.5 set/multiset集合

* **概要**：`set`：关联容器**，**有序，元素自身即key，元素有唯一性，使用内存分配器动态管理内存 。		`multiset`：关联容器**，**有序，元素自身即key，允许不同元素值相同，使用内存分配器动态管理内存。
* **描述**：set（集合）由红黑树实现，其内部元素依据其值自动排序，每个元素值只能出现一次，不允许重复。
* **特点**
  - set 中的元素都是排好序的，集合中没有重复的元素;
  - map 和 set 的插入删除效率比用其他序列容器高，因为对于关联容器来说，不需要做内存拷贝和内存移动。
* **优点**：使用平衡二叉树实现，便于元素查找，且保持了元素的唯一性，以及能自动排序。
* **缺点**：每次插入值的时候，都需要调整红黑树，效率有一定影响。
* **适用场景**：适用于经常查找一个元素是否在某群集中且需要排序的场景。
* 两者主要区别：
  * set不允许容器中有重复元素。
  * multiset允许容器中有重复元素

#### 3.7.1 构造函数

```c++
set<T> st;
set(const set& st);
```

#### 3.7.2 赋值和交换

```c++
set& operator=(const set& st);

st1.swap(st2);
```

#### 3.7.3 大小操作

```c++
size();		//返回容器中元素的个数
empty();	//返回容器是否为空
```

#### 3.7.4 插入和删除

```c++
insert(T());	//在容器中插入元素（会自动排序）

erase(pos);			//删除pos迭代器所指元素，返回下一个元素迭代器
erase(begin,end);	//删除区间[beign:end]全部元素，返回下一个元素迭代器
erase(T());			//删除容器值为T()的元素
clear();			//清除全部元素
```

#### 3.7.5 查找和统计

```c++
find(T());		//查找元素T()，若存在放回元素对应的迭代器，否则返回end迭代器
count(T());		//统计元素T()的个数
```

#### 3.7.6 set和multiset区别

* set不允许容器中有重复元素，multiset允许容器中有重复元素。

* set插入数据的同时会返回插入结果，表示插入是否成功。

  ```c++
  set<int> st;
  pair<set<int>::iterator ,bool> ret = st.insert(11);
  ```

#### 3.7.7 pair对组

* **功能**：成对数据出现时，可以利用对组返回两个数据。

  ```c++
  pair<type,type> p(value1,value2);
  pair<type,type> p = make_pair(value1,value2);
  
  p.first;		//value1
  p.second;		//value2
  ```

#### 3.7.8 容器排序

* set容器默认排序规则为从小到大，利用仿函数可以改变排序规则。

  ```c++
  class Compare{
  public:
      bool operator()(int v1,int v2){
          return v1 > v2;
      }
  };
  
  set<int,Compare> st;
  ```

* 自定义数据指定排序规则

  ```c++
  class Person{
  public:
      Person(string name,int age)
      	: m_name(name),m_age(age) {}
      string m_name;
      int m_age;
  };
  
  class ComparePerson{
  public:
      bool operator()(const Person& p1,const Person& p2){
          //按年龄从大到小
          return p1.m_age > p2.m_age;
      }
  }
  
  set<Person,ComparePerson> st;
  ```

### 3.6 map/multimap关联

* **概要**：`map`：关联容器**，**有序，元素类型<key, value>，key是唯一的，使用内存分配器动态管理内存 。`multimap`：关联容器**，**有序，元素类型<key, value>，允许不同元素key相同，使用内存分配器管理内存 。

* **描述**：map 由红黑树实现，其元素都是 “键值/实值” 所形成的一个对组（key/value pairs)。每个元素有一个键，是排序准则的基础。每一个键只能出现一次，不允许重复。

  map 主要用于资料一对一映射的情况，map 内部自建一颗红黑树，这颗树具有对数据自动排序的功能，所以在 map 内部所有的数据都是有序的。

* **特点**

  - 自动建立 Key - value 的对应。key 和 value 可以是任意你需要的类型。

  - 根据 key 值快速查找记录，查找的复杂度基本是 O(logN)，如果有 1000 个记录，二分查找最多查找 10次(1024)。

  - 增加和删除节点对迭代器的影响很小，除了那个操作节点，对其他的节点都没有什么影响。

  - 对于迭代器来说，可以修改实值，而不能修改 key。

- **优点**：使用平衡二叉树实现，便于元素查找，且能把一个值映射成另一个值，可以创建字典。
- **缺点**：每次插入值的时候，都需要调整红黑树，效率有一定影响。
- **适用场景**：适用于需要存储一个数据字典，并要求方便地根据key找value的场景。
* 两者区别：
  * map不允许容器中有重复键(key)元素。
  * multimap允许容器中有重复键(key)元素。

#### 3.8.1 构造函数

```c++
map<T1,T2> mp;
map(const map& mp);
```

#### 3.8.2 赋值和交换

```c++
map& operator=(const map& st);

mp1.swap(mp2);
```

#### 3.8.3 大小操作

```c++
size();		//返回容器中元素的个数
empty();	//判断容器是否为空
```

#### 3.8.4 插入和删除

```c++
insert(T());	//在容器中插入元素（会自动排序）

erase(pos);			//删除pos迭代器所指元素，返回下一个元素迭代器
erase(begin,end);	//删除区间[beign:end]全部元素，返回下一个元素迭代器
erase(key);			//按照键(key)删除元素
clear();			//清除全部元素

map<int,int> mp;
mp.insert(pair<int,int>(1,500));
mp.insert(make_pair(2,500));
mp.insert(map<int,int>::value_type(3,500));
mp[4] = 555;
```

#### 3.8.5 查找和统计

```c++
find(key);		//查找元素key，若存在放回元素对应的迭代器，否则返回end迭代器
count(key);		//统计key的个数
```

#### 3.8.6 容器排序

* set容器默认排序规则为从小到大，利用仿函数可以改变排序规则。

  ```c++
  class Compare{
  public:
      bool operator()(int v1,int v2){
          return v1 > v2;
      }
  };
  
  map<int,int,Compare> st;
  ```

* 自定义数据指定排序规则

  ```c++
  class Person{
  public:
      Person(string name,int age)
      	: m_name(name),m_age(age) {}
      string m_name;
      int m_age;
  };
  
  class ComparePerson{
  public:
      bool operator()(const Person& p1,const Person& p2){
          //按年龄从大到小
          return p1.m_age > p2.m_age;
      }
  }
  
  map<Person,string,ComparePerson> st;
  ```

### 3.7 stack堆栈

* **概要**：先进后出(First In Last Out，FILO)，且只有一个出口。

![deque原理](img\stack原理.png)

* 栈中只有顶端元素才可被外界使用，此容器没有遍历行为。

#### 3.4.1 构造函数

```c++
stack<T> stk;
strack(const stack &stk);
```

#### 3.4.2 赋值操作

```c++
stack& operator=(const stack &stk);
```

#### 3.4.3 大小操作

```c++
empty();	//返回堆栈是否为空
size();		//返回堆栈大小
```

#### 3.4.4 数据存取

```c++
push(T());		//向栈顶添加一个元素
pop();			//从栈顶删除一个元素
top();			//返回栈顶元素
```

### 3.8 queue队列

* **概要**：先进先出(First In First Out，FIFO)，有两个出口。

![deque原理](img\queue原理.png)

* 队列中只有队头和队尾元素才可被外界使用，此容器没有遍历行为。

#### 3.5.1 构造函数

```
queue<T> stk;
queue(const stack &stk);
```

#### 3.5.2 赋值操作

```
queue& operator=(const queue &stk);
```

#### 3.5.3 大小操作

```c++
empty();		//判断队列是否为空
size();			//返回队列的大小
```

#### 3.5.4 数据存取

```c++
push(T());		//向队列尾部添加一个元素
pop();			//从队头移除一个元素
back();			//返回队尾元素
front();		//返回队头元素
```

### 3.9 forward_list单链表

* **概要**：顺序容器（可顺序访问，但不支持随机访问），单链表，使用内存分配器动态管理内存 。

### 3.10 priority_queue优先队列

* **概要**：容器适配器，严格弱序（Strict Weak Ordering），优先级队列。
* 优先级队列：其底层是用堆来进行实现的。在优先队列中，队首元素一定是当前队列中优先级最高的那一个。

#### 3.10.1 构造函数

```c++
#include <functional>

priority_queue(int) q1;
priority_queue(int,vector<int>,less<int> ) q2;   //大顶堆
priority_queue(int,vector<int>,greater<int> ) q3;//小堆顶
```

#### 3.10.2 赋值操作

```c++
priority_queue& operator=(const priority_queue &stk);
```

#### 3.11.2 大小操作

```c++
empty();		//判断队列是否为空
size();			//返回队列的大小
```

#### 3.12.2 数据存取

```c++
push(T());		//向插入一个元素到队列
top();			//返回堆顶一个元素
pop();			//从堆顶移除一个元素
```

<br>

## 4 STL函数对象

### 4.1 函数对象

* 重载函数调用操作符的类，其对象称为函数对象。
* 函数对象使用重载运算符`()`时，行为类似函数调用，使用也叫**仿函数**。
* 函数对象（仿函数）是一个类，不是一个函数。

#### 4.4.1 使用

* 函数对象在使用时，可以像普通函数一样调用，有参数有返回值。

  ```c++
  class Add{
  public:
      int operator()(int v1,int v2){
          return v1 + v2;
      }
  };
  
  Add ad;
  cout << ad(99,1) << endl;
  ```

* 函数对象超出普通函数的概念，函数对象可以拥有自己的状态。

  ```c++
  class Print{
  public:
      Print() : count(0){}
      
      void operator()(string str){
          cout << str << ++count << endl;
      }
      int count;
  }
  
  Print pt;
  pt("你好：");
  ```

* 函数对象可以作为参数传递。

  ```c++
  void doPrint(Print &mp,string test){
  	mp(test);
  }
  ```

### 4.2 谓词

* 仿函数中，返回bool类型的仿函数称为**谓词**。
* `operator()`接受一个参数，叫做一元谓词。
* `operator()`接受两个参数，叫做二元谓词。

#### 4.2.1 一元谓词

```c++
#include <algorithm>

class GreaterFive{
public:
	bool operator()(int val){
        return val > 5;		//大于5的元素返回true
    }
};

vector<int> v = {1,2,3,4,5,6,7,8,9,10};
vector<int>::iterator it = find_if(v.begin(),v.end(),GreaterFive());
if(it == v.end()){
	cout << "未找到大于5的数字" << endl;
}else{
    cout << "找到了大于5的数字，数字为：" << *it << endl;
}
```

#### 4.2.2 二元谓词

```C++
#include <algorithm>

class Compare{
public:
    bool operator()(int v1,int v2){
        return v1 > v2;		//从大到小排序
    }
};

vector<int> v = {6,7,8,9,10,1,2,3,4,5,};
sort(v.begin(),v.end(),Compare());
```

### 4.3 内置函数对象

* STL内建仿函数对象，用法和一般函数完全相同。
* 使用内建仿函数对象，需要引入头文件`#include <functional>`。

#### 4.3.1 算术仿函数

* **功能**：实现四则运算，其中negate是一元运算，其他都是二元运算。

  ```c++
  template<class T> T negate<T>;			//取反仿函数
  
  template<class T> T plus<T>;			//加法仿函数
  template<class T> T minus<T>;			//减法仿函数
  template<class T> T multiplies<T>;		//乘法仿函数
  template<class T> T divides<T>;			//除法仿函数
  template<class T> T modules<T>;			//取模仿函数
  ```

* 部分示例

  ```c++
  negate<int> ng(50);
  cout << ng(50) << endl;		//输出：-50
  
  plus<int> pu;
  cout << pu(99,1) << endl;	//输出：100
  ```

#### 4.3.2 关系仿函数

* **功能**：实现关系对比。

  ```c++
  template<class T> bool equal_to<T>;			//等于
  template<class T> bool not_equal_to<T>;		//不等于
  template<class T> bool greater<T>;			//大于
  template<class T> bool greater_equal<T>;	//大于等于
  template<class T> bool less<T>;				//小于
  template<class T> bool less_equal<T>;		//小于等于
  ```

* 部分示例

  ```c++
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  //降序
  sort(v.begin(),v.end(),greater<int>());
  ```

#### 4.3.3 逻辑仿函数

* **功能**：实现逻辑运算。

  ```c++
  template<class T> bool logical_and<T>;		//逻辑与
  template<class T> bool logical_or<T>;		//逻辑或
  template<class T> bool logical_not<T>;		//逻辑非
  ```

* 部分示例

  ```c++
  vector<bool> v1 = {0,1,0,1};
  vector<bool> v2 = {0,0,0,0};
  
  //利用逻辑非，将v取反，并搬到v2
  transform(v1.begin(),v1.end(),v2.begin(),logical_not<bool>());
  
  //v2的容器大小一定要大于等于v1，否则会出现错误！
  ```

<br>

## 5 STL常用算法

* `<algorithm>`：涉及比较、交换、查找、遍历、复制、修改等算法。
* `<numeric>`：涉及几个序列进行简单运算的模板函数。
* `<functional>`：涉及一些通用的仿函数。

### 5.1 遍历算法

#### 5.1.1 for_each

* **功能**：遍历容器。

```c++
void print1(int val){
    cout << val << endl;
}

class Print2{
public:
    void operator()(int val){
        cout << val << endl;
    }
};

vector<int> v = {6,7,8,9,10,1,2,3,4,5};
for_each(v.begin(),v.end(),print1);
for_each(v.begin(),v.end(),Print2());

int idx = 0;
for_each(arr.begin(),arr.end(),[&idx](int &x){
   cout << val << endl;
});
```

#### 5.1.2 transform

* **功能**：搬运容器到另一个容器。

  ```c++
  class Transform{
  public:
      int operator()(int v){
          return v;		//直接搬运
      }    
  }
  
  vector<bool> v1 = {0,1,0,1};
  vector<bool> v2.resize(4);
  
  //利用逻辑非，将v取反，并搬到v2
  transform(v1.begin(),v1.end(),v2.begin(),Transform());
  ```

### 5.2 查找算法

#### 5.2.1 find

* **功能**：查找指定元素，返回该元素的迭代器，找不到返回end()。

  ```c++
  iterator find(begin,end,value);
  ```

* 基本数据类型

  ```c++
  vector<int> v = {1,2,3,4,5,6,7};
  vector<int>::iterator it = find(v.begin(),v.end(),5);
  if(it == v.end()){
      cout << "未找到等于5的元素" << endl;
  }else{
      cout << "找到元素：" << *it << endl;
  }
  ```

* 自定义数据类型

  ```c++
  class Student{
  public:
  	Student(int id,string name)
          : m_id(id),m_name(name){}
      
      int m_id;
      string m_name;
      
      //重载 == 符号，让find知道如何比较
      bool operator==(const Student &s){
          return m_id == s.m_id
      }
  }
  
  Student s(2,"xiaozhang");
  vector<Student> v = {
      {1,"xiaowang"},
      {2,"xiaozhang"},
      {3,"xiaofang"}
  };
  vector<Student>::iterator it = find(v.begin(),b.end(),s);
  if(it == v.end()){
      cout << "未找到元素" << endl;
  }else{
      cout << "找到元素：" << endl;
      cout << "\t编号："<< it->m_id << endl;
      cout << "\t姓名："<< it->m_name << endl;
  }
  ```


#### 5.2.2 find_if

* **功能**：按照条件查找元素，返回满足条件的第一个元素迭代器，找不到返回end()。

  ```c++
  iterator find(begin,end,_pred);
  //_pred：谓词
  ```

* 案例参照一元谓词

#### 5.2.3 adjacent_find

* **功能**：查找相邻重复元素，返回相邻元素前一个元素的迭代器。

  ```c++
  iterator adjacent_find(begin,end);
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,1,1,2,3,4,5};
  vector<int>::iterator it = adjacent_find(v.begin(),b.end());
  if(it == v.end()){
      cout << "未找到相邻重复元素" << endl;
  }else{
      cout << "找到相邻重复元素：" << *it << endl;
  }
  ```

#### 5.2.4 binary_search

* **功能**：查找指定元素是否存在。

  ```c++
  bool binary_search(begin,end,value);
  ```

* 基本数据类型

  ```c++
  vector<int> v = {1,2,3,4,5,6,7};
  bool b = find(v.begin(),v.end(),5);
  if(!b){
      cout << "未找到元素" << endl;
  }else{
      cout << "找到元素"  << endl;
  }
  ```

* 自定义数据类型

  ```c++
  class Student{
  public:
  	Student(int id,string name)
          : m_id(id),m_name(name){}
      
      int m_id;
      string m_name;
      
      //重载 == 符号，让find知道如何比较
      bool operator==(const Student &s){
          return m_id == s.m_id
      }
  }
  
  Student s(2,"xiaozhang");
  vector<Student> v = {
      {1,"xiaowang"},
      {2,"xiaozhang"},
      {3,"xiaofang"}
  };
  bool b = find(v.begin(),v.end(),s);
  if(!b){
      cout << "未找到元素" << endl;
  }else{
      cout << "找到元素"  << endl;
  }
  ```

#### 5.2.5 count

* **功能**：统计容器中某个元素的个数，返回元素个数。

  ```c++
  int count(begin,end,value);
  ```

* 基本数据类型

  ```c++
  vector<int> v = {1,2,3,4,5,6,7,5};
  int ct = count(v.begin(),v.end(),5);
  cout << "5的元素个数：" << ct << endl
  ```

* 自定义数据类型

  ```c++
  class Student{
  public:
  	Student(int id,string name)
          : m_id(id),m_name(name){}
      
      int m_id;
      string m_name;
      
      //重载 == 符号，让find知道如何比较
      bool operator==(const Student &s){
          return m_id == s.m_id
      }
  }
  
  Student s(2,"xiaozhang");
  vector<Student> v = {
      {1,"xiaowang"},
      {2,"xiaozhang"},
      {3,"xiaofang"}
  };
  int ct = count(v.begin(),v.end(),s);
  cout << "元素个数：" << ct << endl
  ```

#### 5.2.6 count_if

* **功能**：按条件统计容器中某个元素的个数，返回元素个数。

  ```c++
  int count_if(begin,end,pred);
  //pred：谓词
  ```

* 基本数据类型

  ```c++
  class GreaterFive{
  public:
  	bool operator()(int val){
          return val > 5;		//大于5的元素返回true
      }
  };
  
  vector<int> v = {1,2,3,4,5,6,7,8,9,10};
  int ct = count_if(v.begin(),v.end(),GreaterFive());
  cout << "元素个数：" << ct << endl
  ```

* 自定义数据类型

  ```c++
  class Student{
  public:
  	Student(int id,string name)
          : m_id(id),m_name(name){}
      int m_id;
      string m_name;
  }
  
  class GreaterFive{
  public:
  	bool operator()(const Student &s){
          return s.m_id > 5;		//id大于5的元素返回true
      }
  };
  
  Student s(2,"xiaozhang");
  vector<Student> v = {
      {1,"xiaowang"},
      {2,"xiaozhang"},
      {3,"xiaofang"}
  };
  int ct = count_if(v.begin(),v.end(),GreaterFive());
  cout << "元素个数：" << ct << endl
  ```

### 5.3 排序算法

#### 5.3.1 sort

* **功能**：对容器内元素进行排序

  ```c++
  sort(begin,end,_pred);
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  
  //升序
  sort(v.begin(),v.end());
  //降序
  sort(v.begin(),v.end(),greater<int>());
  ```

#### 5.3.2 random_shuffle

* **功能**：对容器内元素进行洗牌（随机排序）

  ```c++
  random_shuffle(begin,end);
  ```

* 使用案例

  ```c++
  #include <ctime>
  
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  
  //重置随机种子
  srand((unsigned int)time(NULL));
  //洗牌
  random_shuffle(v.begin(),v.end());
  ```

#### 5.3.3 merge

* **功能**：合并容器，并存储到另一容器中

  ```c++
  merge(begin1,end1,begin2,end2,iterator dest);
  //前4个参数分别是2个容器的开始与结束迭代器
  //dest：目标容器的开始迭代器
  ```

* 使用案例

  ```c++
  vector<int> v1 = {6,7,8,9,10,1,2,3,4,5};
  vector<int> v2 = {60,70,80,90,100,10,20,30,40,50};
  vector<int> dest;
  dest.resize(v1.size() + v2.size());
  merge(v1.begin(),v2.end(),v2.begin(),v2.end(),dest.begin());
  
  //merge合并的两个序列必须为有序序列
  ```

#### 5.3.4 reverse

* **功能**：对容器的顺序进行反转

  ```c++
  reverse(begin,end);
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  reverse(v.begin(),v.end());
  ```

### 5.4 拷贝和替换算法

#### 5.4.1 copy

* **功能**：拷贝容器指定范围元素到另一个容器

  ```c++
  copy(begin,end,dest);
  //前2个参数分别是源容器的开始与结束迭代器
  //dest：目标容器的开始迭代器
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  vector<int> dest;
  dest.resize(v.size());
  copy(v.begin(),v.end(),dest.begin());
  ```

#### 5.4.2 replace

* **功能**：将容器指定范围旧元素修改为新元素

  ```c++
  replace(begin,end,oldValue,newValue);
  //前2个参数分别是源容器的开始与结束迭代器
  //后2个参数分别是 被替换元素 和 替换元素
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,10,10,2,3,4,5};
  //10替换成5
  replace(v.begin(),v.end(),10,5);	
  ```

#### 5.4.3 replace_if

* **功能**：将容器指定范围满足条件的旧元素修改为新元素

  ```c++
  replace(begin,end,_pred,newValue);
  //前2个参数分别是源容器的开始与结束迭代器
  //_pred：条件谓词
  //newValue 替换元素
  ```

* 使用案例

  ```c++
  class GreaterFive{
  public:
  	bool operator()(int val){
          return val > 5;		//大于5的元素返回true
      }
  };
  
  vector<int> v = {6,7,8,9,10,10,2,3,4,5};
  //替换大于5的元素为666
  replace_if(v.begin(),v.end(),GreaterFive(),666);	
  ```

#### 5.4.4 swap

* **功能**：交换两个容器的元素

  ```c++
  swap(container c1,container c2);
  ```

* 使用案例

  ```c++
  vector<int> v1 = {6,7,8,9,10,1,2,3,4,5};
  vector<int> v2 = {60,70,80,90,100,10,20,30,40,50};
  
  swap(v1,v2);
  ```

### 5.5 算术生成算法

* 使用是需要包含头文件`#include <numeric>`

#### 5.5.1 accumulate

* 功能：计算容器元素累计总和。

  ```c++
  int accumulate(begin,end,value);
  //value起始值
  ```

* 使用案例

  ```c++
  vector<int> v = {6,7,8,9,10,1,2,3,4,5};
  int total1 = accumulate(v.begin(),v.end(),0);	//输出55
  int total2 = accumulate(v.begin(),v.end(),10);	//输出65
  ```

#### 5.5.2 fill

* **功能**：重置容器中元素的值。

  ```c++
  accumulate(begin,end,value);
  //value填充值
  ```

* 使用案例

  ```c++
  vector<int> v;
  v.resize(10);
  //v中元素的值全置为100
  fill(v.begin(),v.end(),100);
  ```

### 5.6 集合算法

#### 5.6.1 set_intersection

* **功能**：求两个容器的交集，返回目标容器末尾迭代器。

  ```c++
  set_intersection(begin1,end1,begin2,end2,dest);
  ```

* 使用案例

  ```c++
  vector<int> v1 = {1,2,3,4,5,6,7,8,9};
  vector<int> v2 = {1,2,3,4,5,10,11,12,13};
  vector<int> dest;
  dest.resize(min(v1.size(),v2.size()));
  
  vector<int>::iterator destEnd = set_intersection(v1.begin(),v1.end(),v2.begin(),v2.end(),dest.begin());
  //输出：1,2,3,4,5
  ```

#### 5.6.1 set_union

* **功能**：求两个容器的并集

  ```
  set_union(begin1,end1,begin2,end2,dest);
  ```

* 使用案例

  ```c++
  vector<int> v1 = {1,2,3,4,5,6,7,8,9};
  vector<int> v2 = {1,2,3,4,5,10,11,12,13};
  vector<int> dest;
  dest.resize(v1.size()+v2.size());
  
  vector<int>::iterator destEnd = set_union(v1.begin(),v1.end(),v2.begin(),v2.end(),dest.begin());
  //输出 1,2,3,4,5,6,7,8,9,10,11,12,13
  ```

#### 5.6.2 set_difference

* **功能**：求两个容器的差集

  ```
  set_difference(begin1,end1,begin2,end2,dest);
  ```

* 使用案例

  ```c++
  vector<int> v1 = {1,2,3,4,5,6,7,8,9};
  vector<int> v2 = {1,2,3,4,5,10,11,12,13};
  vector<int> dest;
  dest.resize(v1.size()+v2.size());
  
  //v1和v2的差集
  vector<int>::iterator destEnd = set_difference(v1.begin(),v1.end(),v2.begin(),v2.end(),dest.begin());
  //输出 6,7,8,9
  
  //v2和v1的差集
  destEnd = set_difference(v2.begin(),v2.end(),v1.begin(),v1.end(),dest.begin());
  //输出 10，11，12，13
  ```

  
