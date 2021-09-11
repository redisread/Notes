



- [ ] C++多重继承下的虚函数指针情况

![C++指针](https://raw.githubusercontent.com/redisread/Image/master/Windowsimage-20210303101013072.png)

- 若继承的两个基类中都有同名的虚函数，但是子类没有实现这个虚函数，那么子类调用该同名虚函数的时候不知道调用哪个基类的虚函数，最终会报错。(编译时候就会报错)

  ```cpp
  class A{
      public:
      virtual void g(){
          cout <<"A : g" <<endl;
      }
      virtual void h(){
          cout <<"A : h" << endl;
      }
  };
  
  class B{
      public:
      virtual void g(){
          cout <<"B : g" <<endl;
      }
      virtual void h(){
          cout <<"B : h" << endl;
      }
  };
  
  
  class C:public A,public B{
      public:
      // virtual void g(){
      //     cout <<"C : g" <<endl;
      // }
      virtual void h(){
          cout <<"C : h" << endl;
      }
  };
  
  int main()
  {
      // A *a = new A();
      // B *b = new B();
      C *c = new C();
      c->g();
  
      return 0;
  }
  ```

  ![error](https://raw.githubusercontent.com/redisread/Image/master/Windowsimage-20210303101352165.png)


