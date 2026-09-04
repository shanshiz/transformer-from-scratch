# Effective C++ 笔记

## 条款 1-4 精要（2026.09.04）

### 条款 1：视 C++ 为一个语言联邦
C++ = 4 个子语言，分块对待：数据块用 C 思维、对象用 OO 思维、模板用泛型思维、STL 用容器/迭代器/算法思维。

### 条款 2：尽量以 const, enum, inline 替换 #define
- 宏没有类型、没有作用域，不参与调试符号。
- 替换法：
  - 数值：`const int kMax = 17;`
  - 字符串：`const std::string s("hello");`
  - 类内常量：`static const int n = 5;`（需要取地址时用 `enum { N = 5 };` hack）
  - 宏函数：`#define MAX(a,b) ...`（先宏展开、无法类型检查）→ `template<typename T> inline T maxT(const T& a, const T& b){...}`

### 条款 3：尽可能使用 const
- 两种读法：`const int* p` = 指针指向的东东是 const（指向不可变）；`int* const p` = 指针本身 const（不可改指向）。
- 成员函数加 const = 这个函数可以作用于 const 对象，编译器保证函数内不修改对象（*this 为 const this）。
- 常重载：`const char& operator[](size_t) const` 和 `char& operator[](size_t)` 成对提供（注：《Effective C++》条款 3 对应代码示例）。
- 例外：`mutable` 成员允许在 const 函数中被修改（如缓存/互斥锁）。

### 条款 4：确定对象被使用前已先被初始化
- 内置类型（int/float/指针）声明即初始化：`int x = 0;` 别写 `int x;`。
- 对象：用**初始化列表**（member-initializer list），不要用构造函数体内赋值。
- 注意：成员按**声明顺序**初始化（不是按初始化列表的顺序），初始化列表的书写顺序最好与声明一致。
- 跨编译单元的静态对象初始化次序问题 → 换成**函数内局部静态对象**（`static X& get(){ static X s; return s; }`，C++11 起线程安全、首次调用时构造）。
