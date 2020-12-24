# C++ 实现动静态反射和协程

反射是当代语言的一个重要特性，是指程序在运行期可以拿到一个对象的所有信息，进行检测和修改的能力。可以直接获取对象的属性和基本值，并进行修改。但是C++作为一种静态编程语言，并像Java Python等支持反射等语言特性。
现在通过用C++库中会添加反射支持，来减少编程的复杂程度。常见的C++反射解决方案有:[RTTR](https://github.com/rttrorg/rttr)和[meta](https://github.com/skypjack/meta)

C++作为语言中的重要特性就是模板，但是反射支持的困难也就在于此，可以通过模板的方式，来实现静态反射，但是造成的问题是编译期间生成中间代码太多，程序臃肿。此时需要动态反射作为补充，因此聪明如你，需要同时支持动态和静态反射。

下面是一些参考说明:
## 使用示例
下面是RTTR的反射代码示例:

```c++
#include <iostream>
#include <rttr/registration>
using namespace rttr;

struct MyStruct { MyStruct() {}; void func(double) {}; int data; };

//手动注册属性方法和构造函数
RTTR_REGISTRATION
{
    registration::class_<MyStruct>("MyStruct")
         .constructor<>()
         .property("data", &MyStruct::data)
         .method("func", &MyStruct::func);
}

int main() {

    //遍历类的成员
    type t = type::get<MyStruct>();
    for (auto& prop : t.get_properties())
        std::cout << "name: " << prop.get_name() << std::endl;

    for (auto& meth : t.get_methods())
        std::cout << "name: " << meth.get_name() << std::endl;

    //创建类型的实例
    type t2 = type::get_by_name("MyStruct");
    variant var = t2.create();    // 方式1

    constructor ctor = t2.get_constructor();  // 方式2
    var = ctor.invoke();
    std::cout << var.get_type().get_name() << std::endl;  // 打印类型名称

    //设置/获取属性
    MyStruct obj;

    property prop = type::get(obj).get_property("data");
    prop.set_value(obj, 23);

    variant var_prop = prop.get_value(obj);
    std::cout << var_prop.to_int() << std::endl; // prints '23'

    //调用方法
    MyStruct obj2;

    method meth = type::get(obj2).get_method("func");
    meth.invoke(obj2, 42.0);

    variant var2 = type::get(obj2).create();
    meth.invoke(var2, 42.0);

    return 0;
}
```

通过上述代码我们可以发现反射支持的基本功能在于，对象中类型和对应值以及使用方法的迭代遍历。


## 注意事项
对于静态反射:
- 支持(non-static / static) 成员变量/成员函数
- 支持对象基本属性
- 支持`enum`枚举类型
- 支持类模板与模板属性值
- 支持继承子类反射

对于动态反射在静态反射基础上需要支持:
- 支持基本：`non-const`, `const`, `non-static`, `static`关键识别
- 完全动态:可以注册一个不存在的类
- 非侵入式:类外声明

## 参考链接

- [C++反射机制的实现](https://blog.csdn.net/qq_22660775/article/details/89713867)
- [RTTR实现C++反射](https://blog.csdn.net/ruisun09/article/details/108535736)
- [C++ 动态反射库](https://zhuanlan.zhihu.com/p/337200770)
## 要求与目标

要求；
1. 实现反射的基本功能
2. 同时支持静态反射和动态反射，可以根据输入参数进行创建。
4. 使用至少2种设计模式。
5. 提供完善的文档代码注释和文件结构。
6. 只能使用C++标准库和操作系统API；禁止使用boost等第三放方库。

目标:
1. 熟悉C++编程与标准库使用
2. 熟悉C++模板编程
3. 深入理解C++编译过程和运行时机制，深入理解C++虚函数原理
4. 熟悉常用设计模式
5. 熟悉项目开发基本操作