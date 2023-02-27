## c++笔记-->异常抛出

异常(Exception)是程序发生意料外的情况时出现的，异常需要进行处理，否则会导致程序崩溃

### 自定义异常

- 如何创建一个异常

  ```c++
  int func(float *p){
      if(!p){
          throw p;
      }
      return 0;
  }
  ```

- 如何捕获异常

  一般是捕获异常对象而不是一个值，这样符合面向对象的编程

  ```c++
  float *p = nullptr;
  try{
      //这里写可能抛异常的函数
      func(float *p);
  }catch(float exception){
      //()内写要匹配的异常的类型，以及抛出的值
      //{}内写要如何处理异常
      cout<<"null ptr exception: " + exception <<endl;
  }
  try{
      func(float *p);
  }catch(...){
      //()内可以写...表示捕获所有异常
      cout<<"null ptr exception" <<endl;
  }
  ```

- 异常的自动传递

  异常会根据函数调用链一直向上抛出，直到main函数，如果一直向上抛但没接，一旦到达main那么程序会直接崩溃

- 异常抛出声明

  为了可读性，在函数声明和定义时候可以加上可能要抛出的异常的类型

  ```c++
  int func(float *p) throw(float*){
      if(!p){
          throw p;
      }
      return 0;
  }
  ```

- 异常类和异常类继承

  符合面向对象标准，大家一般都会这样写。以下是java经典异常类在c++实现

  ```c++
  class NullPointerException :public Exception{
      public:
      void print(){
          cout<< "空指针异常"<<endl;
      }
  }
  int func(float *p) throw(NullPointerException){
      if(!p){
          throw NullPointerException();
      }
      return 0;
  }
  try{
      func(float *p);
  }catch(Exception e){
      e.print();
  }
  ```

### 栈解旋

抛出异常的时候会像return一样，抛异常的函数内变量和try块内变量的生命周期都将直接结束，这种情况叫栈解旋

### 追溯调用栈

c++可以使用backtrace函数以及各种框架和库来追溯栈调用，这样做的好处是可以找到函数的调用链从而定位是哪个函数的问题