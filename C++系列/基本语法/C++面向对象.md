# 二、类与对象

* C++面向对象三大特性：封装、继承、多态。
* C++中可认为万事万物皆为对象。
* 所谓的对象就应该有行为和属性。
  * 学生：属性：姓名、班级、身高、体重...。行为：上课、写作业、干饭、睡觉...。
  * 汽车：属性：车名、油箱、发动机、车轮...。行为：进车、倒车、开空调、导航...。

## 1 封装

### 1.1 封装的意义

* **作用**：一个对象实例中将**复杂的内部细节**全部进行**封装**，只给我们留下简单的接口，通过接口进行调用和操作。

* **举例**：计算器就像是已经被封装好的类，计算器复杂的电路板按键就好比对象中复杂的变量或属性。你不用知道计算机怎么制造的，你就能使用计算器。

* **盒子类案例**：

  ```c++
  class Box
  {
     public:
        double m_length;   // 长
        double m_width;  // 宽
        double m_height;   // 高
      
        // 成员函数声明
        double getVolume();
        void set(double len, double wid, double hei);
  };
  
  // 成员函数定义
  double Box::getVolume()
  {
      return m_length * m_width * m_height;
  }
   
  void Box::set( double len, double wid, double hei)
  {
      m_length = len;
      m_width = wid;
      m_height = hei;
  }
  ```

* **类成员函数**：指那些把定义和原型写在类定义内部的函数。

  ```c++
  class Box
  {
     public:
        double m_length;   // 长
        double m_width;  // 宽
        double m_height;   // 高
      
    
        double getVolume()
        {
            return m_length * m_width * m_height;
        }
      
        void set( double len, double wid, double hei )
        {
            m_length = len;
            m_width = wid;
            m_height = hei;
        }
  };
  ```

### 1.2 如何封装（访问修饰符）

* **作用**：用来控制哪些属性或方法能被外部访问，哪些不行。

  | 修饰符                  | 类内部 | 类外部 | 子类   |
  | ----------------------- | ------ | ------ | ------ |
  | **公有（public）**      | 允许   | 允许   | 允许   |
  | **私有（private）**     | 允许   | 不允许 | 不允许 |
  | **受保护（protected）** | 允许   | 不允许 | 允许   |

* 案例

  ```c++
  class Base {
  public:
  // 公有成员
  	int a1;
      void fun1(){
          cout << "fun1" << endl;
      }
      
  private:
  // 私有成员
      int a2;
      void fun2(){
          cout << "fun2" << endl;
      }
      
  protected:
  // 受保护成员
   
  };
  ```

### 1.3 struct和class的区别

* **唯一区别**：默认访问权限不同。

  ```c++
  class C{
  	int m_a;	//默认为私有变量
  }
  struct S{
      int m_a;	//默认为共有变量
  }
  ```

### 1.4 成员属性私有化

* **优点**：可以自己控制读写权限，防止用户误操作内部数据。

* **汽车类案例**：

  ```c++
  class Car{
  //留给外部的接口，允许外部访问
  public:
  	void forwardRun();
  	void backRun();
      void listenMusic();
      int getOil();
      bool setOil(int oil);
      
  //此处为复杂的内部细节，不允许外部访问
  private:
      int m_oil;			//油（0-1000L）
  	int m_engineLife;	//发动机寿命  
  
  };
  
  //关键函数...
  int Car::getOil(){
      return m_oil;
  }
  bool setOil(int oil){
      if(oil<0 || oil>1000){
          return false;	//加油失败
      }
      return true;
  }
  ```

* 设计**圆形类**和**点类**，并能描述他们之间的关系。

<br>

## 2 类初始化

### 2.1 构造和析构函数

* 构造函数会在创建类时调用，析构函数会在类销毁前调用。都无需自己调用，编译器自动调用
* 如果我们不提供构造函数和析构函数，编译器会提供构造函数和析构函数的空实现。

#### 2.1.1 构造函数

* **作用**：在创建类时为成员变量设置初始值。

  ```c++
  class Point {
   public:
    Point();              // 构造函数
    Point(int x, int y);  //构造函数可重载
  
    void setPos(int x, int y);
    int getX();
    int getY();
  
   private:
    int m_x;
    int m_y;
    int *ptr;
  };
  
  Point::Point(){
      m_x = 10;
      m_y = 10;
      ptr = new int(88);
      cout << "构造函数Point()" << endl;
  }
  
  Point::Point(int x, int y){
      m_x = x;
      m_y = y;
      ptr = new int(88);
      cout << "构造函数Point(int x, int y)" << endl;
  }
  
  void Point::setPos(int x, int y){
      m_x = x;
      m_y = y;
  }
  
  int Point::getX(){
      return m_x;
  }
  
  int Point::getY(){
      return m_y;
  }
  
  Point p1;
  Point p2(1,2);
  ```

* 隐式转换调用构造函数

  ```c++
  Point(int xy) : m_x(xy) , m_y(xy) {
          cout << "Point(int xy)构造函数" << endl;
  }
  
  Point p1 = 10
  ```

#### 2.1.2 初始化列表

* 使用初始化列表来初始化字段：

  ```c++
  Point::Point(int x, int y)
  	: m_x(x),m_y(y),ptr(nullptr)
  {
      cout << "构造函数Point(int x, int y)" << endl;
  }
  ```

* 上面的语法等同于如下语法：

  ```c++
  Point::Point(int x, int y){
      m_x = x;
      m_y = y;
      ptr = nullptr;
      cout << "构造函数Point(int x, int y)" << endl;
  }
  ```

* 但是有几种情况只能在初始化列表初始化

  ```c++
  class Len {
   public:
    Len() : m_a(88) {}
    const int m_a;
  };
  ```

#### 2.1.3 析构函数

* **作用**：类销毁前执行一些清理工作，比如释放内存。

  ```C++
  class Point {
   public:
    //...
    ~Point();              // 析构函数
    //...
      
   private:
   	int *ptr;   
  };
  
  Point::~Point(){
      delete ptr;
      cout << "析构函数" << endl;
  }
  ```

### 2.2 拷贝构造函数

* **作用**：使用同一类型另一个类来初始化新类。

  ```c++
  Point::Point(const Point &obj)
  {
      cout << "调用拷贝构造函数" << endl;
      m_x = obj.m_x;
      m_y = obj.m_y;
      
      ptr = new int;
      *ptr = *obj.ptr; // 拷贝值
  }
  ```

* 通过使用另一个同类型的对象来初始化新创建的对象。

  ```c++
  Point p1;
  Point p2(p1);
  
  Point p3;
  Point p4 = Point(p3);
  Point p5 = Point(p3);
  ```

* **浅拷贝**：简单的赋值拷贝。（增加了一个指针指向已存在的内存地址）
* **深拷贝**：有指针的地方重新开辟一段内存，让后进行拷贝。（增加了一个指针并且申请了一个新的内存，把对象复制了一份）

### 2.3 类类型变量作为类成员

* C++允许将一个类作为另一个类的成员变量，因为类本质也是一个数据类型。

  ```c++
  class Point{
  public:
      int m_x;
      int m_y;
  };
  
  class Size{
  public:
      int width;
      int hieght;
  };
  
  class Rect{
  public:
      Point m_p1;
      Size m_sz;
  };
  ```

### 2.4 静态成员

#### 2.4.1 静态成员变量

* 所有的类共享同一份数据。

* 在编译阶段分配内存。

* 类的内部声明，类的外部初始化

  ```c++
  class Box{
  public:
      Box(){
          m_count++;
      }
      int getCount(){
          return m_count;
      }
  
  private:
    	int m_length;
      static int m_count;
  };
  
  Box box1;
  Box box2[10];
  cout << box1.getCount();
  ```

* 静态成员变量不占用类的空间

  ```c++
  cout << sizeof(Box) << endl;	//4字节
  ```

#### 2.4.2 静态成员函数

* 静态函数只能访问静态变量。

* 静态函数的调用不要求类的实例化。

  ```c++
  class Box {
   public:
    Box() { m_count++; }
    int getCount() { return m_count; }
    static void print(){
  //      cout << m_length << endl;   //错误，不能访问成员变量
        cout << "静态函数 " << m_count << endl;
    }
  
   private:
    int m_length;
    static int m_count;
  };
  
  //通过命名空间直接访问
  Box::print();
  ```

<br>

## 3 对象模型

### 3.1 基本概念

* C++中成员变量和成员函数是分开存储的。
* 每一种类非静态成员函数只会有一个函数实例，多个对象公用同一个函数。
* 那么非静态成员函数是怎么调用类成员变量的？通过`this`指针。
* `this`指针指向被调用成员函数所属的对象

### 3.2 this指针

* **作用**：类非静态成员函数用来访问类其他成员变量和函数。

* 每一个类非静态成员函数都有this指针，不用声明可以直接调用

  ```c++
  void Box::fun1()
  {
  	cout << this->m_length << " " << this->getCount() << endl;
  }
  ```

* 在类非静态成员函数中使用成员函数或成员变量时，不添加`this->`编译器也会在编译时自动生成

  ```c++
  //与fun1()效果一模一样
  void Box::fun2()
  {
  	cout << m_length << " " << getCount() << endl;
  }
  ```

* **`this`的本质**：当我们调用成员函数时，实际上是替某个对象调用它。

  ```c++
  void Box::fun(int x)
  {
      cout << x << endl;
  	cout << this->m_length << " " << this->getCount() << endl;
  }
  Box box;
  box.fun(10);
  
  /*
  	//编译器内部会把我们的函数转换成这样
  	void Box::fun1(Box *this,int x){
  	  	cout << x << endl;
  		cout << this->m_length << " " << this->getCount() << endl;
  	}
  	Box box;
  	Box::fun1(&box,10);
  */
  ```

### 3.3 空指针调用成员函数

* 因为函数并不占用类的空间，使用空指针也能调用成员函数。但是要注意成员函数中不允许有成员变量，否则报错。

  ```c++
  Box *box = nullptr;
  box.getCount();
  ```

### 3.4 const修饰成员

#### 3.4.1 常函数

* **作用**：常函数内不可修改成员函数。

  ```c++
  class Obj{
  public:
      // 常函数的声明与定义
      // 普通成员函数内this指针： C * const this;
      // 常函数内this指针：const C* const this;
  	void print() const{
          // this->m_a = 88; //错误
          this->m_b = 909;
      }
      int m_a;
      mutable int m_b;	//即使在常函数中也可以修改
  }
  ```

#### 3.4.2 常对象

* **作用**：和修饰基本变量一样，常对象无法被修改。

  ```c++
  const Obj obj;
  // obj.m_a = 100;	//错误
  obj.m_b = 88;	//正确
  ```

## 4 运算符重载

* **作用**：对已有的运算符重新定义新的功能，来适应不同的数据类型。

* **本质**：运算符重载的为函数重载。但有一定的规则需要遵循。

  1. 重载运算符时,运算符的运算顺序和优先级不变,操作数个数不变。
  2. 不能创造新的运算符,只能重载C++中已有的运算符,并且规定有6个运算符不能重载,如表所示。

  | 不能重载的运算符 | 含义           |
  | ---------------- | -------------- |
  | `.`              | 类属关系运算符 |
  | `.*`             | 成员指针运算符 |
  | `::`             | 作用域运算符   |
  | `?:`             | 条件运算符     |
  | `#`              | 编译预处理符号 |
  | `sizeof()`       | 取数据类型大小 |

### 1.赋值运算符的重载

* 赋值运算符包括`=`、`+=`、 `-=`、 `*=`、 `/=`、`%=`、 `&=`、`|=`、`^=`、`<<=`、`>>=`。这里以`=`、`+=`为例。

  ```c++
  class HugeDouble{
  public:
      HugeDouble():m_int(0),m_dec(0){}
      HugeDouble(const char* h);
      HugeDouble &operator=(HugeDouble &c);
      HugeDouble &operator+=(HugeDouble &c);
      void print();
  
  private:
      unsigned long long m_int;	//整数部分
      unsigned long long m_dec;	//小数部分
  };
  
  HugeDouble::HugeDouble(const char* h)
  {
      std::string str(h);
      int pos = str.find('.');    //找到.在字符串位置
  
      //分别将整数或小数字符串部分转换成整数
      this->m_int = atoi(str.substr(0,pos).c_str());
      this->m_dec = atoi(str.substr(pos+1,str.length()-(pos+1)).c_str());
  }
  
  HugeDouble &HugeDouble::operator=(HugeDouble &c)
  {
      this->m_int = c.m_int;
      this->m_dec = c.m_dec;
      return *this;
  }
  
  HugeDouble &HugeDouble::operator+=(HugeDouble &c)
  {
      this->m_int += c.m_int;
      this->m_dec += c.m_dec;
      return *this;
  }
  
  void HugeDouble::print()
  {
      std::cout << m_int << "." << m_dec << std::endl;
  }
  ```

### 2.算术运算符的重载

* 算术运算符包括`+`、`-`、`*`、`/`、`%`、等。这里以`+`、`-`为例。

  ```c++
  class HugeDouble{
      HugeDouble operator+(HugeDouble &c);
      HugeDouble operator-(HugeDouble &c);
      //其余省略...
  };
  
  HugeDouble HugeDouble::operator+(HugeDouble &c)
  {
      HugeDouble temp;
      temp.m_dec = this->m_dec + c.m_dec;
      temp.m_int = this->m_int + c.m_int;
      return temp;
  }
  
  HugeDouble HugeDouble::operator-(HugeDouble &c){
      HugeDouble temp;
      temp.m_int = this->m_int - c.m_int;
      temp.m_dec = this->m_dec - c.m_dec;
      return temp;
  }
  ```

### 3.关系运算符的重载

* 关系运算符包括`>`、`<` 、`>=`、`<=`、`==`、`!=`等。这里以`>`、`==`为例。

  ```c++
  class HugeDouble{
        friend bool operator>(const HugeDouble &st1, const HugeDouble &st2);
      friend bool operator==(const HugeDouble &hd1, const HugeDouble &hd2);
      //其余省略...
  };
  
  bool operator==(const HugeDouble &hd1, const HugeDouble &hd2)
  {
      //整数部分长度不相等
      if(hd1.m_int!=hd2.m_int){
          return false;
      }
  
      //小数部分长度不相等
      if(hd1.m_dec!=hd2.m_dec){
          return false;
      }
  
      return true;
  }
  
  bool operator>(const HugeDouble &hd1, const HugeDouble &hd2)
  {
      //hd1整数部分大
      if(hd1.m_int>hd2.m_int){
          return true;
      }
      //hd2整数部分大
      if(hd1.m_int<hd2.m_int){
          return false;
      }
  
      //hd1小数部分大
      if(hd1.m_dec>hd2.m_dec){
          return true;
      }
      //hd2小数部分大
      if(hd1.m_dec<hd2.m_dec){
          return false;
      }
  
      return false;
  }
  ```

### 4.其他运算符的重载

* 位运算符：`|` (按位或)、`&` (按位与)、`~`(按位取反)、`^`(按位异或)、`<<` (左移)、`>>`(右移)

* 自增自减运算符：`++`(自增)、`–`(自减)

* 逻辑运算符： `||`(逻辑或)、`&&`(逻辑与)、`!`(逻辑非)

* 单目运算符 ：`+` (正)、`-`(负)、`*`(指针)、`&`(取地址)

* 空间申请与释放： `new`、 delete、new[ ] 、delete[]

* 其他运算符： `()`(函数调用)、`->`(成员访问)、`,`(逗号)，

* 这里以`<<`、`>>`、`[]`的重载为例。

  ```c++
  class HugeDouble{
  	friend ostream & operator<<(ostream & cout, const HugeDouble & st);
      friend istream & operator>>(istream & cin, HugeDouble & st);
      //其余省略...
  };
  
  ostream &operator<<(ostream & cout, const HugeDouble &st)
  {
      cout << st.m_int << '.' << st.m_dec;
      return cout;
  }
  
  istream &operator>>(istream & cin, HugeDouble &st)
  {
      string temp;
      cin >> temp;
      st = temp.c_str();
      return cin;
  }
  ```

<br>

## 5 继承

* **作用**：扩展原有的代码，应用到其他程序中，而不必重新编写这些代码。

  ![继承](img\继承.png)

### 5.1 基本用法

* 当创建一个类时，您不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。这个已有的类称为**基类**，新建的类称为**派生类**。

  ```c++
  //基类Animal
  class Animal{
  public:
      //每个动物都有名字，都要吃饭睡觉
      string m_name;
      void sleep(){
          cout << m_name << " 睡觉！！" << endl;
      }
      void eat(){
          cout << m_name << " 吃东西！！" << endl;
      }
  
  };
  
  //派生类Cat
  class Cat : public Animal{
  public:
      //设置动物信息
      Cat(string name) {
          m_name = name;
          cout << m_name << "是一只猫咪" << endl;
      }
      //该种动物特有的行为
      void Act() {
          cout << m_name <<"爱吃鱼" << endl;
      }
  };
  
  //派生类Dog
  class Dog : public Animal{
  public:
      Dog(string name){
          m_name = name;
          cout << m_name << " 是一只狗狗" << endl;
      }
      //该种动物特有的行为
      void Act() {
          cout << m_name <<" 爱吃骨头" << endl;
      }
  };
  ```

### 5.2 继承方式

* 派生类可以访问基类中所有的非私有成员。因此基类成员如果不想被派生类的成员函数访问，则应在基类中声明为 private。

  ```c++
  class A{
    public:
      int m_a;
    protected:
    	int m_b;
    private:
      int m_c;
  };
  
  class B :public A{
      B(){
          m_a = 1;
          m_b = 2;
          // m_c = 3;  //错误
      }
  }
  ```

* 我们可以根据访问权限总结出不同的访问类型。

  | 访问方式 | public | protected | private |
  | :------- | :----- | :-------- | :------ |
  | 同一个类 | 允许   | 允许      | 允许    |
  | 派生类   | 允许   | 允许      | 不允许  |
  | 外部     | 允许   | 不允许    | 不允许  |

* 派生类不会继承基类的情况
  * 基类的重载运算符。
  * 基类的友元函数。

### 5.3 继承类型

* 当一个类派生自基类，该基类可以被继承为 **public、protected** 或 **private** 几种类型。继承类型是通过上面讲解的访问修饰符 access-specifier 来指定的。

* 我们几乎不使用 **protected** 或 **private** 继承，通常使用 **public** 继承。当使用不同类型的继承时，遵循以下几个规则：

  - **公有继承（public）：**当一个类派生自**公有**基类时，基类的**公有**成员也是派生类的**公有**成员，基类的**保护**成员也是派生类的**保护**成员，基类的**私有**成员不能直接被派生类访问，但是可以通过调用基类的**公有**和**保护**成员来访问。

  - **保护继承（protected）：** 当一个类派生自**保护**基类时，基类的**公有**和**保护**成员将成为派生类的**保护**成员。

  - **私有继承（private）：**当一个类派生自**私有**基类时，基类的**公有**和**保护**成员将成为派生类的**私有**成员。

### 5.4 继承中的构造和析构

* 子类继承父类后，在创建子类编译器会先后调用父类、子类的构造函数，先后调用子类、父类的析构函数。

  ```c++
  class Base{
  public:
      Base(int a):m_a(a){
          cout << "Base类的构造函数" << endl;
      }
      ~Base(){
          cout << "Base类的析构函数" << endl;
      }
      int getA(){
          return m_a;
      }
  
  private:
      int m_a;
  };
  
  class Derived : public Base{
  public:
      Derived(int a,int b) : Base(a) , m_b(b){
          cout << "Derived类的构造函数" << endl;
      }
      ~Derived(){
          cout << "Derived类的析构函数" << endl;
      }
      int getB(){
          return m_b;
      }
  
  private:
      int m_b;
  };
  ```

### 5.5 父子类同名成员处理

#### 5.5.1 同名普通成员

* 父子类出现同名成员

  ```C++
  class B{
  public:
      void fun(){
          cout << "B fun()" << endl;
      }
  protected:
      int m_a;
  };
  
  class D : public B{
  public:
      void fun(){
          cout << "D fun()" << endl;
      }
  protected:
      int m_a;
  };
  ```

* 访问自己类的同名成员可以直接访问。

  ```c++
  class D{
      //...
      void fun2(){
          fun();
      }
      void set(int a){
          m_a = a;
      }
  };
  ```

* 访问父类同名成员需要加作用域访问

  ```c++
  class D{
      //...
      void fun2(){
          B::fun();
      }
      void set(int a){
          B::m_a = a;
      }
  };
  ```

#### 5.5.2 同名静态成员

* 父子类出现同名成员成员

  ```c++
  class B{
  public:
      static void fun(){
          cout << "B fun()" << endl;
      }
  
  public:
      static int m_a;
  
  };
  
  class D : public B{
  public:
      static void fun(){
          cout << "D fun()" << endl;
      }
  
  public:
      static int m_a;
  };
  ```

* 访问方式

  ```c++
  int B::m_a = 1;
  int D::m_a = 2;
  int main()
  {
      B::fun();
      D::fun();
      D::B::fun();
  
      cout << B::m_a << endl;
      cout << D::m_a << endl;
      cout << D::B::m_a << endl;
  
      return 0;
  }
  ```

### 5.6 多继承用法

#### 5.6.1 多继承

* 多继承即一个子类可以有多个父类，它继承了多个父类的特性。

  ```c++
  // 基类 Shape
  class Shape
  {
  public:
      Shape(int w,int h)
          : m_width(w), m_height(h) {}
  
  protected:
    int m_width;
    int m_height;
  
  };
  
  // 基类 PaintCost
  class PaintCost
  {
  public:
      PaintCost(int p) : m_price(p){}
      int getCost(int area)
      {
       return area * m_price;
      }
  
  protected:
    int m_price;
  
  };
  
  // 派生类
  class Rectangle: public Shape, public PaintCost
  {
  public:
      Rectangle(int w,int h,int p)
          : Shape(w,h),PaintCost(p){}
  
      int getArea()
      {
          return (m_width * m_height);
      }
  };
  
  int main()
  {
      Rectangle rect(5,6,2);
  
      int area = rect.getArea();
      int price = rect.getCost(area);
  
      cout << "矩形的总面积: " << area << "平方米" << endl;
      cout << "矩形的总花费: " << price << "元" << endl;
  
      return 0;
  }
  ```

#### 5.6.2 菱形继承

* 菱形继承又叫钻石继承，下面是一个典型的菱形继承。

  ![菱形继承](img\菱形继承.png)

* 菱形继承会造成继承两次同一份数据

  ```c++
  class A{
  public:
      int a;
  };	//4字节
  
  class B:public A{
  public:
      int b;
  };	//8字节
  
  class C:public A{
  public:
      int c;
  };	//8字节
  
  class D:public B,public C{
  public:
      int d;
  };	//20字节，里面有D::d + B::b + C::c + B::a + C::a
  
  
  int main()
  {
      D d;
      d.B::a = 1;
      d.C::a = 2;
  //    d.a = 3;  //报错，编译器不知道你访问的哪个a
      cout << d.B::a << endl;
      cout << d.C::a << endl;
  
      return 0;
  }
  ```

<br>

## 6 多态

### 6.1 多态初识

* **作用**：多态性指相同对象收到不同消息或不同对象收到相同消息时产生不同的实现动作。

* 多态性(polymorphism)可以简单地概括为“一个接口，多种方法”，它是面向对象编程领域的核心概念。

* C++支持两种多态性：编译时多态性，运行时多态性。

  1. 编译时多态性（静态多态）：通过重载函数实现：先期联编 early binding

     ```c++
     //基类Animal
     class Animal{
     public:
         //每个动物都有名字，都要吃饭睡觉
         string m_name;
         void speak(){
             cout << m_name << " 叫！！" << endl;
         }
     
     };
     
     //派生类Cat
     class Cat : public Animal{
     public:
         //设置动物信息
         Cat(string name) {
             m_name = name;
             cout << m_name << "是一只猫咪" << endl;
         }
         //该种动物特有的行为
         void speak(){
             cout << m_name << " 喵喵喵！！" << endl;
         }
     };
     
     //派生类Cat
     class Cat : public Animal{
     public:
         //设置动物信息
         Cat(string name) {
             m_name = name;
             cout << m_name << "是一只猫咪" << endl;
         }
         //该种动物特有的行为
         void speak(){
             cout << m_name << " 汪汪汪！！" << endl;
         }
     };
     
     //声明类型指针调用就执行谁的函数，编译阶段确定函数的地址
     Animal *c1 = new Cat("小咪");
     c1->speak();
     Cat *c2 = new Cat("迷小");
     c2->speak();
     Animal *c3 = new Animal();
     ```

  2. 运行时多态性（动态多态）：通过虚函数实现 ：滞后联编 late binding

     ```C++
     //基类Animal
     class Animal{
     public:
         string m_name;
         virtual void speak(){	//基类函数添加关键字virtual
             cout << m_name << " 叫！！" << endl;
         }
     
     };
     
     //构造时类型是什么就执行谁的函数，运行阶段确定函数的地址
     Animal *c1 = new Cat("小咪");
     c1->speak();
     Cat *c2 = new Cat("迷小");
     c2->speak();
     Animal *c3 = new Animal();
     ```

* **虚函数** 是在基类中使用关键字 **virtual** 声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为**动态链接**，或**后期绑定**。

### 6.2 多态案例——计算器

* 循序渐进体现多态给我们带来的好处。

* 没有多态写法

  ```c++
  class Calculator{
  public:
      double getResult(char op){
          switch(op){
              case '+':
                  return (m_num1 + m_num2);
              break;
              case '-':
                  return (m_num1 - m_num2);
              break;
              case '*':
                  return (m_num1 * m_num2);
              break;
              case '/':
                  return (m_num1 / m_num2);
              break;
              default:break;
          }
          return 0;
      }
      
     //操作数
     double m_num1;
     double m_num2;
  };
  ```

* 多态写法

  ```c++
  //计算器抽象类
  class AbstractCalculator{
  public:
      virtual double getResult(){ return 0; }
      
     //操作数
     double m_num1;
     double m_num2;
  };
  
  //加法计算器类
  class AddCalculator : public AbstractCalculator{
  public:
      double getResult() override{ 
          return (m_num1 + m_num2); 
      }
  };
  
  //减法计算器类
  class SubCalculator : public AbstractCalculator{
  public:
      double getResult() override{ 
          return (m_num1 - m_num2); 
      }
  };
  
  //乘法计算器类
  class MulCalculator : public AbstractCalculator{
  public:
      double getResult() override{ 
          return (m_num1 * m_num2); 
      }
  };
  
  //除法计算器类
  class DivCalculator : public AbstractCalculator{
  public:
      double getResult() override{ 
          return (m_num1 / m_num2); 
      }
  };
  
  ```

* 为什么多态比非多态要好，因为对应了设计模式的一个原则**单一职责**和**开闭原则**。

### 6.3 纯虚函数

* 您可能想要在基类中定义虚函数，以便在派生类中重新定义该函数更好地适用于对象，但是您在基类中又不能对虚函数给出有意义的实现，这个时候就会用到纯虚函数。

  ```c++
  class AbstractCalculator{
  public:
      virtual double getResult() = 0;
      
     //操作数
     double m_num1;
     double m_num2;
  };
  
  // AbstractCalculator *cal = new AbstractCalculator();	//错误
  ```

* 有纯虚函数的类无法被实例化，且子类必须重新实现纯虚函数

  ```c++
  //求幂计算器类
  class ExpCalculator : public AbstractCalculator{
  public:
  	//错误
  };
  ```

### 6.4 多态案例——制作饮料

* 茶和咖啡制作工艺是一样的：烧水->放料->冲泡->等待泡完。

  ```c++
  class Drink{
  public:
      virtual void boil() = 0;    //烧水
      virtual void add() = 0;     //添加调料
      virtual void brew() = 0;    //冲泡
      virtual void wait() = 0;    //等待
  
      void make(){
          this->boil();
          this->add();
          this->brew();
          this->wait();
      }
  
  };
  
  class Tea : public Drink{
  public:
      void boil() override{
          std::cout << "1.将水烧至85摄氏度。" << std::endl;
      }
  
      void add() override{
          std::cout << "2.把茶叶放入杯中。" << std::endl;
      }
  
      void brew() override{
          std::cout << "3.用水冲泡茶叶。" << std::endl;
      }
  
      void wait() override{
          std::cout << "4.泡五分钟，饮用口感最佳。" << std::endl;
      }
  
  };
  
  class Coffee : public Drink{
  public:
      void boil() override{
          std::cout << "1.将水烧至100摄氏度。" << std::endl;
      }
  
      void add() override{
          std::cout << "2.把咖啡放入咖啡机中。" << std::endl;
      }
  
      void brew() override{
          std::cout << "3.将热水加入咖啡机研磨。" << std::endl;
      }
  
      void wait() override{
          std::cout << "4.等待研磨完成。" << std::endl;
      }
  
  };
  
  ```

### 6.5 虚析构和纯虚析构

* **作用**：解决如果子类属性开辟到堆区，父类指针指向此内存无法调用到子类析构代码的问题。

* 虚析构和纯虚析构共性：可以解决父类指针释放子类对象。

* 虚析构和纯虚析构区别：如果是纯虚析构则父类无法被实例化。

* 问题代码

  ```c++
  class Base{
  public:
      Base(){
          cout << "Base构造函数" << endl;
      }
      ~Base(){
          cout << "Base析构函数" << endl;
      }
  };
  
  class Derived : public Base{
  public:
      Derived(){
          cout << "Derived构造函数" << endl;
      }
      ~Derived(){
          cout << "Derived析构函数" << endl;
      }    
  };
  
  Base *b = new Derived();
  delete b;
  ```

* 解决问题代码

  ```c++
  class Base{
  public:
      Base(){
          cout << "Base构造函数" << endl;
      }
      virtual ~Base(){
          cout << "Base析构函数" << endl;
      }
  };
  
  class Derived : public Base{
  public:
      Derived(){
          cout << "Derived构造函数" << endl;
      }
      ~Derived(){
          cout << "Derived析构函数" << endl;
      }    
  };
  
  Base *b = new Derived();
  delete b;
  ```

  

<br>

## 7 友元

* **作用**：让一个函数或者类运行访问一个类中私有成员。

* **例子**：你家有2种房间，客厅和卧室，客厅所有客人都能访问，卧室只有你一个人可以访问。你想让你的好基友也能进你的卧室，这就需要友元了。

  ```c++
  class House{
  public:
  	House() : m_livingRoom("客厅") , m_bedRoom("卧室") {}
      
  public:
      string m_livingRoom;
      
  private:
      string m_bedRoom;
  
  };
  ```

### 7.1 全局友元函数

* **作用**：允许某个全局函数访问某个类的私有变量。

  ```c++
  class House{
  	friend void visitBedRoom(House *h);
  //...省略其他函数变量
  };
  
  void visitBedRoom(House *h){
      cout << "全局函数正在访问类私有成员：" << h->m_bedRoom << endl;
  }
  ```

### 7.2 类友元函数

* **作用**：允许某个类任何成员函数访问另一个类的私有变量。

  ```c++
  class House{
  	friend class GoodGay;
  //...省略其他函数变量
  };
  
  class GoodGay{
      public:
      void visit(){
      	cout << "类正在访问其他类私有成员：" << m_h.m_bedRoom << endl; 
      }
      
      House m_h;
  };
  ```

### 7.3 类成员友元函数

* **作用**：允许某个类某个成员函数访问另一个类的私有变量。

  ```c++
  class House{
  	friend void FBI::search();
  //...省略其他函数变量
  };
  
  class FBI{
      public:
      void visit(){
      	cout << "类正在访问其他类共有成员：" << m_h.m_h << endl; 
      }
      void search(){
      	cout << "类正在访问其他类私有成员：" << m_h.m_bedRoom << endl; 
      }
      
      House m_h;
  };
  ```

