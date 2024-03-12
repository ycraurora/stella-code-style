# Stella c++ 代码规范
## 基本原则
- **模块功能导向**: 项目开发时需要着重考虑功能模块划分，便于任务分发与维护。通常将不同模块封装为库以供主程序调用，模块设计需根据功能需求，保证结构条理明确，逻辑清晰;
- **设计类内函数**: 需要充分考虑代码复用与功能区分。若多个函数之间功能类似，代码相似程度高，则需要考虑函数融合，通过不同参数区别功能变化;
- **模块内部变量**: 除必要总体调度函数外，应尽量避免使用全局变量，以确保模块的独立性和可维护性;
- **类的设计过程**: 类的设计秉承开放封闭原则，合理利用继承、重载、多态等特性，正确处理`public`，`protected`，`private`等成员函数与成员变量修饰符; 
- **函数的返回值**: 函数的返回值通常为自定义状态或`bool`型，用于反映函数执行结果。通过返回值类型，可有效判断程序运行状态，预防错误数据累积和程序崩溃的发生;
- **函数参数的传递**: 
  - 输入参数: 使用const修饰符表示为不可改动的输入参数，基本类型使用值传递，非基本类型使用引用传递;
  - 输出参数: 使用引用传递或指针传递;
  例如:
  ```
  bool calc_angle(const cv::Mat &src_image, const double start_point, double &angle);
  ```
- **结构体的设计**: 根据结构体复杂程度合理设计构造函数，运算符重载等，避免冗长的初始化和赋值过程; 
  例如:
  ```
  struct Point
  {
    double x = 0.0;
    double y = 0.0;
    double z = 0.0;

    Point() = default; // 默认构造函数
    Point(double _x, double _y, double _z) : x(_x), y(_y), z(_z) {}
    // 重载赋值运算符
    Point &operator=(const Point &other) {
        this->x = other.x;
        this->y = other.y;
        this->z = other.z;
        return *this;
    }
  }
  ```
- **命名空间的设计**: 需要充分考虑命名冲突问题，对于不同的项目，模块，使用不同的命名空间，推荐使用多层命名空间。
  例如:
  ```
  namespace stellauto {
  namespace host {
    // do something
  } // host
  } // stellauto
  ```
- **命名空间的使用**: 避免对标准命名空间的使用(`using namespace std;`)，尽量使用`std::`; 避免在头文件中引入命名空间(`using namespace`)，仅在源文件中使用引入命名空间
## 命名规则
- **命名空间**: 使用全小写字母，参考**命名空间设计**一节给出的示例，尽量使用多层命名空间，单层命名空间名称尽量使用单个名词进行命名
- **类或结构体**: 每段单词首字母大写，无分隔符
  例如:
  ```
  class StellautoHost {
    // do something
  };
  ``` 
- **成员函数**: `public`成员函数使用全小写字母，单词之间使用下划线`_`分隔; `protected`成员函数以下划线`_`开始，使用全小写字母，单词之间使用`_`分隔; `private`成员函数以双下划线开始`__`，其他同前二者; 成员函数命名尽量使用动宾结构短语，避免使用纯名词短语与局部变量命名冲突导致意外错误。
  例如:
  ```
  public:
    bool init();
  protected:
    bool _process(const InputParams &input_params);
  private:
    bool __format_output(Output &output);
  ```
- **函数形参**: 使用全小写字母，单词之间使用下划线`_`分隔
  例如:
  ```
  bool execute(const float vehicle_length);
  ```
- **成员变量**: 类成员变量使用全小写字母，单词之间使用下划线`_`分隔，并添加前缀`m_`; 结构体成员变量同前者但不添加前缀; 定义成员变量时注意初始化
  例如:
  ```
  class StellautoHost
  {
  private:
    std::string m_addr_ip = "";  
  };

  struct Point
  {
    double x = 0.0;
    double y = 0.0;
    double z = 0.0;
  }
  ```
- **常量**: 声明为`constexpr`或`const`的常量在命名时添加前缀`k_`
  例如:
  ```
  constexpr double k_double_epsilon = 1.0e-6;
  ```
- **全局变量**: 尽量避免在头文件中定义全局变量，在源文件中定义的全局变量使用匿名命名空间包裹
  例如:
  ```
  namespace {
    bool g_flag = false;
  }
  ```
  **注**: 命名规则遵循的优先级为 成员变量 > 常量 > 全局变量
- **局部变量**: 局部变量使用全小写字母，单词之间使用下划线`_`分隔，尽量使用名词短语; 合理使用auto自动推理变量类型
  例如:
  ```
  double vehicle_speed;
  PathOptimizer path_optimizer;
  auto way_point = way_point_list.begin();
  ```
- **宏定义**: 使用全大写字母，单词之间使用下划线`_`分隔
  例如:
  ```
  #define DISALLOW_COPY_AND_ASSIGN(classname)              \
    classname(const classname &) = delete;                 \
    classname &operator=(const classname &) = delete;
  ```
## 注释风格
建议使用doxygen支持的注释语法，以便后续自动生成文档，在使用clangd语言服务器时，推荐如下注释风格:
```
/// \brief 功能说明
/// \tparam type_name 模板参数含义
/// \param name 参数含义
/// \param[in] input_param 输入参数含义
/// \param[out] output_param 输出参数含义
/// \return 返回值含义
```
建议灵活使用doxygen支持的其他特殊注释关键字，如: `bug`，`warning`，`author`，`version`等。
- **类注释**: 尽量对类的定义添加功能和用法注释，注释风格如下:
```
/// \brief 类的简要描述
/// 详细描述用途，用法，注意事项等
/// 如有依赖或约束，也需要说明
```
- **函数注释**: 对每个函数都需要进行如下注释:
  - 函数功能
  - 使用注意事项(如有)
  - 输入输出参数
  - 返回值
- **变量注释**: 使用`//`风格，对于每个变量的定义，写在其后方; 对于多个变量的统一说明，写在上方，并通过注释或空行的方式明确注释的有效范围
  例如:
  ```
  // 初始化参数
  double m_length = 0.0; // 长度
  double m_width = 0.0; // 宽度
  // 计算参数
  int m_num = 0; // 数量
  ```
- **函数内注释**: 使用`//`风格，对于每个需要注释的过程，写于其上方，并通过注释或空行的方式明确注释的有效范围
  例如:
  ```
  // 长宽检测
  CHECK_GT(m_length, k_math_epsilon);
  CHECK_GT(m_width, k_math_epsilon);
  // 初始化角点
  __init_corners();
  ```
## 其他规范
- **头文件**: 为避免头文件重复引用，有如下两种方式
  - 使用宏定义
  例如:
  ```
  #ifndef STELLAUTO_HOST_H
  #define STELLAUTO_HOST_H

  // do something

  #endif // STELLAUTO_HOST_H
  ```
  此种方式被c++标准支持，通用性较强; 编译过程中较耗费时间，对于大型项目不友好; 需要注意避免重复定义宏
  - 使用`pragma once`语法
  例如:
  ```
  #pragma once

  // do something
  ```
  此种方式由编译器支持，通用性较弱; 编译效率高于宏定义方式; 该方式考察头文件路径, 需要注意同一份文件的不同拷贝引起的重复引用问题
  当前大部分编译器均支持pragma语法，在大型项目中尽量使用第二种方式。
- **指针**: 尽量使用智能指针来避免出现野指针、资源未释放、重复释放等问题，并根据合理使用`std::unique_ptr`，`std::shared_ptr`与`std::weak_ptr`，禁止使用`std::auto_ptr`
- **nullptr**: 表示空指针时使用`nullptr`，避免使用`NULL`(int类型的0)
- **bool**: 禁止对bool类型的变量赋值int型数据0或1，即便false，true和0，1等价
- **赋值**: 除底层数据处理外，尽量避免使用`memcpy`，`strcpy`等函数对内存进行直接操作
- **效率**: 
  - `push_back`与`emplace_back`: 一般来说，`emplace_back`效率高于`push_back`，且支持如下的构造方式:
  ```
  std::vector<Point> point_vector;
  point_vector.emplace_back(1.0, 0.0, 0.0);
  ``` 
  但是如果容器中的类型构造函数参数过多，建议使用如下方式进行构造，以避免出现意外错误:
  ```
  std::vector<Point> point_vector;
  point_vector.emplace_back(Point(1.0, 0.0, 0.0));
  ```
  或者避免设计过多参数的构造函数。
  - `++i`与`i++`: 二者在如下`for`循环中的使用是等价的，但是`++i`的效率高于`i++`，尤其是迭代器遍历: 
  ```
  for (int i = 0; i < 100; ++i)
  {
    // do something
  }
  for (auto iter = point_vector.begin(); i != point_vector.end(); ++iter)
  {
    // do something
  }
  ```
- **类型的隐式转换**: 通常情况下，应避免出现不同类型之间的隐式转换
  - 初始化: 请使用正确的数据类型对变量进行初始化，如`double a = 0.0; float b = 0.0f; int c = 0;`
  - 类型转换: 请使用`static_cast`进行强制类型转换，避免使用`(double)`等方式，以便在大型项目中查找类型转换的位置，帮助排查问题
- **数值比较**:
  - 等于: 避免对浮点型进行等于0的比较，应使用如下方式: 
  ```
  double epsilon = 1.0e-6; // or std::numeric_limits<double>::epsilon()
  if (std::abs(a) < epsilon)
  {
    // 判定为0
  }
  ```
  - 大小: 避免无符号类型与有符号类型之间的大小比较，通常出现在如下情况中:
  ```
  for (int i = 0; i < point_vector.size(); ++i)
  {
    // do something
  }
  ```
  其中`i`为int类型，`point_vector.size()`为unsigned long(linux)或unsigned long long(windows)，此处应使用size_t定义`i`的类型:
  ```
  for (size_t i = 0; i < point_vector.size(); ++i)
  {
    // do something
  }
  ```
  此外，应避免for循环中无符号类型减去常量的情况:
  ```
  for (size_t i = 0; i < point_vector.size() - 1; ++i)
  {
    // 当point_vector为空时，point_vector.size() - 1变为该类型最大值，导致错误
  }
  ```
  此处应使用如下方式进行判断:
  ```
  for (size_t i = 0; i + 1 < point_vector.size(); ++i)
  {
    // do something
  }
  ```
**注**: c++使用中有诸多提高效率的方式和需要注意的地方，此处无法一一列举，在编译过程中开启全部警告是一种规范代码的较好方式
## 程序测试
c++开发程序的测试标准通常基于软件工程的最佳实践和质量保证标准
- **单元测试**
  - 编写测试用例来测试单个函数、类或模块;
  - 测试用例应覆盖各种输入情况: 包括边界值条件和异常情况;
- **集成测试**
  - 测试不同模块之间的交互;
  - 确保各个模块按照预期整合在一起;
- **功能测试**
  - 确保程序的功能按照预期工作;
  - 测试整个程序的功能;
- **性能测试**
  - 测试程序在各个工作负载下的性能表现;
  - 包括测试程序的响应时间、吞吐量、资源利用率等方面的性能;
- **回归测试**
  - 在进行更改或修复bug后，确保之前的功能仍然正常工作;
  - 重新执行测试用例，以确保没有引入新的问题;
- **可靠性测试**
  - 确保程序在各种相应条件下都能可靠运行;
- **安全性测试**
  - 确保程序没有安全漏洞，如: 缓冲区溢出、代码注入等;
  - 使用静态代码分析、动态代码分析和漏洞扫描辅助工具进行安全性检查;
- **可移植性测试**
  - 确保程序在符合条件的操作系统和硬件平台上皆可正常运行;
- **文档测试**
  - 确保程序的文档(包括注释、用户手册等)与实际代码保持同步和准确;
- **自动化测试**
  - 尽可能自动化测试过程，以提高效率和减少人为错误;
  - 使用自动化测试工具和脚本来执行各种测试

**注**: 以上测试标准可以根据项目的需求和规模进行调整和拓展。同时，采用持续继承和持续交付等实践可以帮助确保代码的质量和稳定性。