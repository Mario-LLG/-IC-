# C++面向对象编程快速入门

 以快速读懂代码为目的，概念与其他面向对象语言通用 

作者：周捷

[zhoujie@micron.com](mailto:zhoujie@micron.com)

[foxzhou@shu.edu.cn](mailto:foxzhou@shu.edu.cn) 

吐血原创，侵权请客，没有科班学过，菜鸟码农 

 

​       针对学过一点C没学过C++的人，请注意这里的代码只是为了举例说明原理，精简了很多东西，并不可以被编译的

 

## 目录

[toc]

## part I

### 1. 什么是类(class)，什么是对象(obj)？

a)    形式如C语言中常见的: int a=10，这句话里int表示整数类，也就是一个类，这个类是系统自带的类，而a则是这个类的一个对象，string, float都是类

b)   面向对象编程，就是要创建自己的类和对象。如：学生类 student，对象是张三同学。一个类可以创建多个对象，比如张三和李四，就好比int a; int b;一样

 

### 2. 如何创建自己的类

a)    形式如

```cpp
Class Student//这个大括号里的叫类定义
{ 
  string name; //学生的名字
  int age;    //学生的年龄
}
```

​       其中，student是类的名字，这个类里有两个内容，即年龄和名字，age和name叫做这个类的成员数据，或者叫属性，类比int a=10，10这个数就是对象a的属性

b)   到这里为止，类和C语言的结构体还是一样的

 

### 3.  公有和私有的初步概念

a)    在类的定义里，形式如：

```cpp
Class Student //这个大括号里的叫类定义
{ 
  Public:
    string name;
  private:
    int age;
}
```

b)   意思是name是公有的，类的外部(主函数里或者其他类)可以读写name的值，但年龄是私有的，外面看不到。当然也可以将名字和年龄都写在public里，对于初学者可以先不写private，private以后再讨论

 

### 4. 如何使用一个类

a)    当类已经被定义后，就可以创建类的对象，形式和int a创建完全一样

```cpp
Student aa; 
```

这就是创建了一个student类的对象，名字叫aa

 

### 5. 什么是成员函数(又叫方法)

a)    例如int a=1 b=2，则a+b=3，其中加法就是int类的成员函数，如果a b是字符串，那当然是不能用加法的，所以，成员函数就是只能被某个类的对象使用的函数

b)   建立自己的类的方法，扩展一下student类

```cpp
Class Student  //这个大括号里的叫类定义
{ 
  Public:   //接下去的很多节我们都暂时不管private，把age name全写在public里
    int age; 
    string name;
    bool set (int a); //这个就是成员函数的声明，又叫方法
}
```

c)    成员函数的声明和C语言的普通函数完全一样，只是写在类的里面

d)   成员函数的定义，也就是具体内容，注意写在类的外面：

```cpp
Bool student :: set (int a) //特点就是多了两个冒号，表示他是属于student类的
{
	Age = a;  //大括号里的叫成员函数定义
	Return true
}
```

e)    有了方法，就能真正操作类的对象了，以下是主函数：

```cpp
Student aa;  //创建一个对象aa
aa. set(20);   //把aa的年龄属性设成20，使用方法用句号连接，如果对象aa不是student类的，则不能使用这个 .set的方法
aa. age ;   //直接访问aa的年龄属性，返回20
```

f)    和C语言的普通函数一样，成员函数也是可以重载的，就是不同函数可以有相同的函数名，通过不同的输入参数来识别，如：

```cpp
Class Student
{ 
Public:
  int age; 
  string name;
  bool set (int i); //两个函数名字一样，但输入格式不一样
  bool set (string a);//这个函数定义略过，name = a; return true;即可
}
```

g)    请注意以上给出的类的方法set()这个例子太过简单，只是对age name赋值了，实际并不需要这么做，还有更简单的，就是直接赋值，aa.age = 20; aa.name = “张三”; 

h)   但是，如果直接写aa.age = 20会引起一个问题，比如aa.age = -5; 从程序的角度没有错，但实际年龄不可能为负数，所以一般在实际程序中，都会使用成员函数来对成员变量赋值，即aa. set(20); 并且在set (int a)的函数定义里，增加一些防错机制，比如: if a>0 return pass; else return fail; 

 

### 阶段总结：

到此为止已经讲完全部的基本概念，1类(学生，类定义里要写成员和方法的名字)，2对象(aa)，3成员数据(年龄和名字，又叫属性)，4成员函数(set，给年龄赋值，又叫方法，具体内容写在类的外面)

一个面向对象的代码最少有三部分：1 类的定义， 2 成员函数的定义， 3 创建对象和调用方法



##  part II

### 6. 构造函数：

a)    写一句最简单的int a; 则表示创建一个对象a，类为int，这里的a虽然没有给一个a=10的值，但系统已经有了一个默认的值，就是a=0，类似的，写student aa时，创建了aa对象，那aa有默认值吗？这里，如果有构造函数，那aa就有默认值

b)   为什么要构造函数？对学生类建立一个对象aa后，要给他的年龄和姓名分别赋值，那就要写两行代码set(“张三”)；set(20)，使用构造函数，即使你有再多的成员数据，就像int a一样，不用一一写了

c)    构造函数的本质即对象初始化，给对象的成员数据填一个默认的数，而构造函数的名字必须和类的名字一样，形式如下：

```cpp
Class Student  //类定义里先申明构造函数
{ 
Public:
  int age; 
  string name;
  student () ;  //这是构造函数，没有输入值和返回值，并且名字和类的名字一样
}

student :: student () //构造函数定义，特点就是冒号两边是一样的，注意这个函数没有输入值
{
  Age = 20;//构造函数里，可以对全部的成员数据给一个默认值
  Name = “张三”; 

}
//下面是主函数
Student aa ; //新建一个student类的对象，名字叫aa，这里不用再人工赋值年龄和名字了，将自动调用构造函数给年龄和名字默认值
```

### 7. 带参数的构造函数：

a)    上边的构造函数没有输入值，只有默认值，年龄只能是20那有没有办法改进一下呢？我们让构造函数进化一下：

```cpp
Class Student  //类定义里先申明构造函数
{ 
Public:
 int age; 
 string name;
 student () ;  //这是没参数的构造函数申明，允许几个名字一样的函数，通过不同输入值来区别
 student (int a, string b) //同名的构造函数申明，即重载，带2个输入参数，即名字和年龄
}

student :: student (int a, string b) //这是带参数构造函数定义，大括号里是函数定义，前一个student代表类后一个代表构造函数
{
  Age = a;   //这样名字和年龄就不是默认值了，可以动态输入
  Name = b
}
//下面是主函数
Student aa ;  //新建一个student类的对象aa，自动给aa的属性填默认值
Student bb (25, “李四”); //构造函数重载，新建一个student类的对象bb，同时动态输入名字和年龄 
```

### 8. 析构函数：

a)    这节不做深入，只要知道析构函数是销毁内存数据就行

b)   对象aa被创建后，其数据就一直在内存中了，那这块内存什么时候能被释放？就是使用析构函数的时候。在对象aa已经没用的时候，析构函数专门用于销毁对象

c)    对于析构函数的内容，这里不做深入，主函数中运行：delete aa; 时，系统会自动调用析构函数

d)   析构函数写法和构造函数类似

```cpp
cstudent :: ~student () //析构函数名字比构造函数多了个波浪号，没有输入值，没有返回值
{ cout<<”delete object”; } //C++中cout相当于C语言中的printf就是打印
```

 

### 9. 常成员函数，关键字const

a)    前面说了一个成员函数set，用来给年龄和姓名赋值，同样的，创建对象aa之后，可以做一个函数来读出他的年龄和名字，如果这个方法只读取属性，而不修改属性，就是常成员函数。

b)  

```cpp
 Class Student  //类定义，这里省略了构造函数
{ 
  Public:
    int age; 
    string name;
    bool set (int i); //这个就是成员函数的声明，又叫方法
    bool read () const; //后面加了const，表示这个函数只读不写
}

Bool student :: read() const  //常成员函数内容，后面加了const，只读不写
{
  Cout<<name;  //打印出姓名
  Cout<< age;  //打印出年龄
  Return true;
}
Student aa;  //创建对象
aa. read();  //在创建aa后，调用read方法
```

 

c)    实际可以不使用const，普通方法也可以读成员数据，加const只是安全起见，防止意外修改数据。更加不正规的做法就是像第5小节那样直接读取：aa.age，<span style='color:red;;'>但在正规程序中还是会使用const来读读取。</span> 

### 10. 静态成员函数，静态成员数据，关键字static

a)    一个类可以创建多个对象，就像int a; int b; int c; int d;一样。student类可以不停新建aa bb cc dd对象，那需要一个变量cnt=4来表示程序已经创建了4个对象了，cnt这个数，与student类有关，但又不属于aa bb cc中间任何一个对象，像这种描述全局，又与某个对象属性无关的，叫做静态成员数据，读取静态成员数据的方法，叫静态成员函数

b)   在类定义中加入静态成员数据，静态成员函数：

```cpp
Class Student  //类定义
{
  Public:
    int  age;        //成员数据，年龄
    string name;     //成员数据，名字
    student () ;      //这是没参数的构造函数，创建对象时给age name默认值
    static int cnt;      //static表示静态成员数据，统计有多少个对象
    static int count();    //static表示静态成员函数，返回有多少个对象
}
```

c)    如何定义静态成员数据：

```cpp
student :: student () //构造函数定义
{
  Age = 20;   //构造函数里，可以对全部的数据给一个默认值
  Name = “张三”;
  Cnt = cnt +1;  //每次调用构造函数创建对象时，cnt加一，就可以统计出有多少对象了
}

Int student ::count()  //静态成员函数的定义，注意这里不写static 了
{
	Return cnt;     //返回静态成员数据，统计有多少个对象
}
```

d)   如何使用静态成员函数，下面是主函数：

```cpp
Student aa; //创建对象aa
Student bb; //再创建对象bb
aa.count();  //调用count方法，返回静态成员数据cnt=2，已建立两个对象，这行和下一行是一样的
bb.count();    //注意不论aa还是 bb，返回是一样的，静态成员不依赖于某个对象
student :: count ();  //返回静态成员数据cnt=2，这个写法和aa.count()效果完全一样，只有不依赖于对象的静态函数才可以这么写
```

e)    以上三行效果完全相同

f)    在实际程序中，静态成员除了统计产生了多少个对象可以用，还可以用在所有共有对象共有的数据，假设这个程序里所有的学生都是S大学的，那就可以设置一个static string university = “S University”;   (这里说的对吗？？？)

### 阶段总结：第二阶段讲了类的成员函数，又叫方法：

​    1 普通成员函数bool student::set(int a)

​    2构造函数，自动初始化对象 student::student ()

​    3 带参数的构造函数，动态初始化对象 student::student (int a, string b)

​    4 析构函数，销毁对象 student::~student ()

​    5 常成员函数，只读不写，和普通成员函数类似bool student::read() const

​    6 静态成员函数，依赖于类，但不依赖于某个对象static int count()



> 前方预警，接下去的内容会越来越难，编程不归路
>
> 

## Part III

### 11. 再谈谈public和private

a)    这节比较晦涩，我也想跳过，但跳过后面就没法讲了

b)   前面在第二小节说了，类的内容分公有私有，设置私有的目的，就是那些编程高手所说的“封装性”，对类的外部隐藏了类内部工作机制。简单的说，public里的内容，可以在主函数里看到，而private的内容，只能在类的内部看到

c)    写一个简化的例子，在student类的public和private分别写一个方法

```cpp
Class student:     //类定义，我没写全，为了突出重点省略了构造函数和属性
{
  Public:             //公有部分
   Void print_name();   //这个print名字是公有的方法
  Private:            //私有部分
   Void print_age();    //这个print年龄是私有的方法
}

Void student :: print_name()  //定义print name公有方法
{ cout<<“张三”; }

Void student :: print_age()    //定义print age私有方法
{  cout<<“20”; }

//以下是主函数
Student aa;     //创建对象aa，构造函数我省略了
aa.print_name();  //调用公有方法，打印出张三
aa.print_age();   //调用私有方法，不允许，这行会报错，提示student类不存在print_age这个方法
```

 

d)   通过上面这个例子，大概知道了公有和私有的皮毛，既然私有方法不能被使用，那还有什么存在的意义？下面加深一下这个例子，看看如何使用私有方法，其核心思想是通过公有方法的外壳调用私有方法

```cpp
Class student: //类定义，里面我没写全，为了突出重点省略了构造函数
{
  Public:
   Void print_age_public()    //这个print_age_public名字是公有的方法，外面看得到
  Private:
   Void print_age_private()   //这个print_age_private是私有的方法，外面看不到
}

Void student :: print_age_public ()  //定义print_age_public公有方法
{  print_age_private(); }     //重点来了，这里通过公有方法的外壳调用了私有方法

Void student :: print_age_private()  //定义print_age_private私有方法
{  cout << 20; }         //打印出20

//以下是主函数
Student  aa;       //创建对象aa，构造函数我省略了
aa.print_age_public ();  //这里通过公有函数的外壳，调用私有，然后打印出年龄20
aa.print_age_private();  //这里出错，不允许直接调用私有方法
```

 

e)    我写的这个例子只用了类的方法，并没有类的成员(属性)，属性也分public和private，和例子中的方法一样，不能直接访问，只能通过类内部的公有方法才能读写private属性

 

### 12. 友元类， friend

a)    这节内容也不做深入，建议死记硬背

b)   在11小节，介绍了private，不能被类的外部访问，但有一个例外，就是友元类

c)    我们一直在以学生类来举例，现在增加一个类，教师类teacher，并将teacher设置为student的友元，<span style='color:red;;'>teacher类的成员函数就可以访问student 的所有public和private信息，</span>形式如下：

```cpp
Class student
{
  Friend class  teacher; 
  Public: ……   //内容省略
  Private: ……
}
```

 

### 13. 类的派生继承初步

a)    我们一直在说student这个类，那学生可以细分成本科生和研究生，学生类是个大类，本科生和研究生也是学生，是学生的子类，这种把类细分的做法就叫类的派生继承。学生是父类，也叫基类或者超类，所谓派生，是相对于父类而言的，所谓继承，是相对子类而言的。

b)   先看派生出本科生的写法：

```cpp
Class undergraduate : public student  //本科生类定义，冒号后面表示从student类公有派生而来
{    
  Public:
 	 String course ;    //这行新增定义了一个公开的字符串属性course，比如本科生的大学物理课程
}
```

再看看研究生的

```cpp
Class postgraduate : public student   //研究生类定义，冒号后面表示从student类公有派生而来
{    
  Public:
  	String research;    //这行新增定义了一个和本科生不一样的字符串属性research，比如研究生的方向:芯片设计
}
```

c)    对比这两个类，请注意他们都没有写两个基本属性name 和age，因为根本不用写，子类自动继承父类的属性和方法，也就是说，创建一个研究生或者本科生类的对象后，他也带有name和age两个属性

d)   这两个子类没有构造函数，他的对象是怎么被创建的呢？name和age是否有值呢？答案是肯定的

```cpp
Postgraduate bb  //创建研究生对象，此时子类无构造函数，系统将调用父类student的无参数的构造函数给name和age赋一个默认值，其默认值在第6小节中，也就是”张三”，age=20

bb. set (25) ;   //Postgraduate类本身没有set方法，这里调用了第4小节中父类student的方法，对age赋值
```

通过上面两行，说明了子类可以自动继承父类的属性和方法。 

e)    最后请注意，研究生类和本科生类除了共有的name和age，也有不同的东西，研究生是research而本科生是course，这是非常重要的类的派生特性，除了继承来的，还能自己发展壮大，可以有自己的属性，也可以有自己的方法。

### 14. 类在不同情况下的继承，关键字public, private, protect

a)    这节又是纯理论

b)   除了之前说过的public和private以外，还有一个和他们平级的关键字，protect(保护)。之前一直没有说protect，<span style='color:red;;'>是因为在没有派生继承的情况下，protect与private效果完全一样，即不能被外部访问，</span>但在有了类的继承后，情况出现了变化

c)    我们在上一节定义研究生类开头是这样写的class postgraduate { public student…… } 表示从student类继承，但为什么前面要加public这个字呢？因为这里的public表示公有继承，他只继承student的公有部分(age和name)，并且age和name也将做为研究生类的public部分。而研究生类是无法继承也无法访问student类的私有部分的

d)   当student类定义中的private被替换成protect时，对student本身无任何变化，但class postgraduate { public student…… }时候，除了public被继承了，protect(可以理解为私有)也被研究生继承了，并且student的protect内容被继承到了postgraduate的protect中

e)    理论略晕，总结一下：

父类student, public内容A，private内容B，子类postgraduate公有继承，则只有public内容A

父类student, public内容A，protect内容B，子类postgraduate公有继承，则子有public内容A，protect内容B

f)    还有更晕的事，上边一直说的都是公有继承，也就是代码中的class postgraduate { public student…… }。那这里的public能否替换成private和protect呢？答案是可以的，就是私有继承和保护继承，具体如下，纯理论，没兴趣的跳过

​    父类public内容A，private内容B，子类私有继承，则子类只有内容A ，且为private

​    父类public内容A，protect内容B，子类保护继承，则子类有内容A B，且都为protect

g)    类似排列组合还有很多，但我们只要记住，在保密优先级上，private最私密，protect其次，public最公开。<span style='color:red;;'>Private里的内容无论如何都不能被继承</span>



### 15. 子类的构造函数

a)    子类是可以有自己的构造函的。之前的postgraduate和undergraduate子类没有构造函数，所以系统会调用父类student的构造函数，但是，不论子类有没有自己构造函数，创建子类对象时，父类构造函数都将被调用

b)   这里我们加上子类的构造函数，子类的构造函数依然和前面所说的构造函数一样，分为带参数的和不带参数的，以研究生类为例：

```cpp
Class postgraduate : public student //研究生类定义, 表示从student 派生而来
{   
  Public:       //研究生的公有成员
    String research ;   //这行新增定义了一个公开的属性research，字符串，比如研究生的方向:芯片设计
    postgraduate ();   //无参数的构造函数声明
    postgraduate ( int a, string b, string c); //带参数的构造函数声明，参数为年龄 姓名 研究方向
}

Postgraduate :: postgraduate ()  //无参数构造函数定义
{
  research = “asic design”; //这里对研究生的research属性赋值为asic design，注意这里没有age和name
}
//下面是主函数：
Postgraduate bb ; //创建研究生对象bb，调用无参数构造函数
```

 

c)    先说说无参数的构造函数postgraduate (); 这个构造函数里只对research进行赋值，而没有对age和name赋值，但在13节说过，age和name会自动在父类student的构造函数中赋值，换句话说，在创建子类bb对象时，系统首先自动运行父类student构造函数，对age和name赋值，随后运行子类postgraduate构造函数，对bb对象进行拓展，增加research内容。再换句话说，子类是不能继承父类的构造函数的，只能调用父类构造函数。

 

d)   再来看看带参数的子类构造函数，首先他的定义和声明长的不一样，后面多了东西

```cpp
Postgraduate :: postgraduate (int a, string b, string c) : student(a, b)  //带参数构造函数定义
{ Research = c; }
//下面是主函数：
Postgraduate bb( 25, “李四”, “ASIC design”) ; //创建研究生对象bb，传入有参数的构造函数
```

这里程序会先调用父类student构造函数，把25和李四两个值传入父类student带参数的构造函数，随后在把参数c=asic design传给bb自己的research属性。这样实现了对研究生对象的全动态的初始化赋值。



### 16. 多态的初步

a)    在第5小节，第一次介绍类的方法(成员函数)时说，方法是专属于某个类的，我举了个例子，int a=1; int b=2; 则 a+b=3;的因为加法也是类方法，专属于整数类的，如果是string a ; string b，显然是不能用加法的

b)   这里我要颠覆一下这个概念，在一些比较新的编程语言中，比如python/ruby，两个字符串是可以执行加法的，形式如下：

a = “xxxx” #这是python无需定义数据类型

b =”yyyy”

print a+b 

这里并不会报错，会打印出字符串“xxxxyyyy”，就是拼接了两个字符串，是不是很奇怪？

 

c)    这就是面向对象编程思想里大名鼎鼎的“多态”，这里我先给一个不太严谨的说法，其思想是，不同类的对象也可以用相同的方法，这里的相同指的是相同的方法名字，而方法里面的内容具体干了什么，是不一样的。比如当a b是数字，符号”+”执行传统意义的加法，当a b 是字符串，符号”+”执行字符串拼接，系统将根据当前情况智能选择采用那种方法

d)   上面这个不严谨的说法，在python中是正确的，但在C++中略有不同，C++中，如果要实现相同的函数名执行不同的内容，有3种方法：

​          i.      重载

​         ii.      隐藏

​        iii.      覆盖 (override，又叫重写，这个才是C++的多态)

 

e)    第一个是之前提过的重载，在C语言中就有，不依赖于面向对象，重载的函数名相同，靠不同的输入参数类型来识别调用哪个函数，如第5小节，set(20);则调用set(int a)，set(“张三”);则调用set( string b );上边举的python这个例子，其实看上去更接近重载。重载有时也被称为“假多态”。另外，重载两个函数的参数格式必须是不一样的，但是隐藏和覆盖的同名函数的参数可以完全一样

f)    第二个是隐藏，隐藏和覆盖都依赖于面向对象，隐藏的具体做法：在父类student中定义一个函数study( int a)，在子类postgraduate中也定义一个函数study( bool b)，(这里把两个函数参数设的不一样主要对比一下隐藏和重载的区别，其实隐藏是可以参数完全一样的)。我们看看会有什么结果，例子如下：

```cpp
//下面是学生定义
Class student      //父类学生定义，为了突出重点省略了构造函数和其他属性
{
  Public:
   	void study (bool b);   //声明study方法，注意参数是bool
}
void postgraduate :: study (bool b);     //学生study方法定义 这里感觉有问题，是不如应该这样修改
//void student :: study (bool b);  
{ cout<< “好好学习”;  }         //学生要好好学习

//下面是研究生定义
Class postgraduate : public student     //研究生类定义，为了突出重点省略了构造函数和属性，从student 派生而来
{       
  Public:           //研究生的公有成员
   	void study ( int a );     //声明study方法，注意参数是int
}
void postgraduate :: study( int a) ;  //研究生study方法定义
{ cout<< “芯片设计”; }       //打印出芯片设计

//下面是主函数
Postgraduate bb;      //建研究生对象
Student aa;         //建学生类对象
bb. study(2);        //研究生对象调用研究生的study方法，参数为int，打印出芯片设计
aa. study( true );      //学生对象调用学生的study方法，参数为bool，打印出好好学习
bb. study( true );      //这行出错，研究生对象，但参数为bool，本来应该重载父类的study方法，因为这时父类方法被隐藏了，系统找不到对应的方法，这就是隐藏和重载的区别
```

 

g)    隐藏会使子类不能调用父类的方法，但是隐藏可以让student和postgraduate两个类都调用自己的study同名方法，也实现了类似数字a+b和字符串a+b不同。在实际程序中，隐藏使用的非常少(我个人感觉)。

h)   第三种就是覆盖，也就是C++中真正的多态，他依靠类的指针来实现，所以在讲多态之前，我们不得不先讲一下类的指针。

  

### 17. 类的指针

a)    在讲真正多态前必须先讲解类的指针，并且在实际程序中，会大量使用类指针，之前我一直回避了指针的问题，因为在C中指针就是个难点，现在回避不了必须讲。

b)   如果暂时不考虑继承，简单的类指针与结构体指针类似：

```cpp
Student *p;  //新建一个student类指针
Student aa;  //新建aa对象
p = &aa ;    //p指针指向aa对
p -> name ;   //这个就和aa.name一样，返回aa的name值
p -> study();   //这个相当于aa.study()，对aa执行成员函数
```

 

c)    接着我们把继承考虑进去，会复杂一点：(类定义省略，且只考虑公有继承)

```cpp
Student *p1;      //新建一个student父类指针
Postgraduate *p2;    //新建一个Postgraduate子类指针
Student aa;       //新建父类对象aa
Postgraduate bb;    //新建子类对象bb
p1 = &aa;        //父类指针指向父类对象，可以
p2 = &bb;        //子类指针指向子类对象，也可以，下面颠倒一下试试
p1 = &bb;         //父类学生指针指向子类研究生对象，可以
p2 = &aa;         //子类研究生指针指向父类学生对象，这里报错
```

 

d)   结论是父类指针是可以指向子类成员的，因为研究生一定是学生的一种，但反过来则不行，学生可不一定都是研究生

e)    另外，当student指针指向Postgraduate时，虽然是可以的，但p1 -> research 会报错，也就是说，虽然父指针指向了postgraduate，但不能调用postgraduate中的属性和方法，<span style='color:red;;'>父类指针指向子类时，仍然只能用父类中的方法。</span>

f)    在下一节，我们会讲怎么样让父类指针使用子类的方法，也就真正的是多态。

  

### 18. 真正的多态与虚函数(覆盖)，关键字virtual

a)    前面说了，重载靠同名函数不同参数格式来识别，而覆盖则依赖于指针识别，同名的函数输入参数格式可以完全一样，或者干脆都没有参数也可以。

b)   我们分别在student，undergraduate，postgraduate建一个方法，都叫study，但每个study学习内容是不一样的，并且在所有子类父类study声明前面加上关键字virtual，表示这是一个虚函数。在多态的高级用法中，并不是每个同名函数都必须加virtual的，这里我们不做深入，只用一个偷懒的办法，<span style='color:red;;'>就是子类父类所有同名函数不管三七二十一都加virtual。</span>



```cpp
Class student      //学生类定义，为了突出重点省略了构造函数和其他属性
{
  Public:
 	 virtual void study ();   //声明study方法，注意前面加了virtual，是个虚函数，并且没有参数
}
void postgraduate :: study ();  //注意这里没有virtual，长的和普通成员一样,跟之前有一处问题一样，是否应该如下修改
//void student :: study ();
{ cout<<“好好学习”; }     //学生类没有具体学的内容，只有好好学习

//下面是研究生
Class postgraduate : public student    //研究生类定义，为了突出重点省略了构造函数和属性，从student 派生而来
{     
	Public:         //研究生的公有成员
		virtual void study ();   //声明study方法，注意前面加了virtual，是个虚函数，也没有参数
}
void postgraduate :: study () ;  //注意这里没有virtual，长的和普通成员一样
{ cout<<“芯片设计”; }     //研究生学习芯片设计

//下面是本科生
Class undergraduate : public student //本科生类定义，为了突出重点省略了构造函数和属性，从student 派生而来
{    
	Public:        //本科生的公有成员
 		virtual void study ();  //声明study方法，注意前面加了virtual，是个虚函数，也没有参数
}
void undergraduate :: study ()   //注意这里没有virtual，长的和普通成员一样
{ cout<<“大学物理”; }      //本科生学习大学物理
```

 

c)    好了，我们在student，undergraduate，postgraduate三个类中都设置了study虚函数，并且他们的输入参数都是一样的也就是没有参数，这点和重载不同，下面是主函数

```cpp
Student aa;         //学生对象aa
Postgraduate bb;      //研究生对象bb
Undergraduate cc;      //本科生对象cc
Student *p;     //定义父类指针，学生类指针
p = &aa;      //指针指向学生对象
p -> study();     //调用学生类的study方法，打印出好好学习
p = &bb;       //父类指针指向子类对象
p -> study();     //父类指针调用子类研究生的方法，打印出芯片设计，就是多态
p = &cc;       //父类指针指向另一个子类对象
p -> study();     //父类指针调用子类研究生的方法，打印出大学物理，就是多态
```

 

d)   C++多态的核心，就是只要在程序开始时候设置一个父类指针，之后这个指针可以动态的指向不同的类，并且指针还可以动态的调用不同的类的方法，从而实现了不同数据类使用相同的方法

e)    再谈谈多态(覆盖)和重载的区别：这里牵涉到编译原理，不做深入。简单说，重载是静态的，当定义了set(int a)和set(string b)两个同名函数后，经过编译，系统内部其实把他们改成了两个不同名的函数，比如一个叫int_set(int a)另一个叫string_set(string b)，所以从系统角度看，本质上他们两个并不是同名函数。而当使用多态时，参数格式可以是完全一样的，由于依赖于指针，系统在编译时并不知道这个指针会指向哪个类(比如我们可以小改上面这个程序，让用户用键盘随机选择指向研究生或者本科生)，因此随着程序的动态发展，指针指向不同的子类，根据当前状态调用相应的类的方法，这被称为”动态”。

(这段写的是不是有问题？)

 

 

### 19. 纯虚函数与抽象类

a)    先讲解抽象类这个概念，学生是一个类，研究生本科生或者中小学生都是学生类的子类，但在现实中，如果建立一个学生对象张三，那张三一定是研究生本科生或者中小学生中的一员，不存在一个笼统的学生张三，而又不属于任何一个子类的对象。因此，单纯只属于学生类的对象张三在现实中是不存在的，他一定是某子类的一员。所以学生类不应该具有自己的对象，于是学生类被叫做抽象类。

b)    

c)    设置抽象类依赖纯虚函数，形式如下

```cpp
Class student //学生类定义
{
  Public:
   Student ();         //抽象类也是有构造函数的
   Student (int a , string b);    //构造函数重载
   virtual void study () =0;      //声明study方法，注意前面加了virtual还有=0，表明这是纯虚函数
}
```

 

d)   <span style='color:red;;'>当设置了virtual void study () =0;后，student就是一个抽象类了，</span>并且study ()函数只有声明，没有定义，其具体内容靠子类的多态来实现。

e)    在主函数中，我们依然可以创建一个student的指针，<span style='color:red;;'>但不能创建student对象了</span>

```cpp
//下面是主函数
Student aa ;     //这里报错，不允许创建抽象类对象
Student *p ;    //可以创建抽象类指针
Postgraduate bb;   //创建子类对象
p = &bb ;      //父类指针指向子类
p -> study();     //通过多态调用子类的study方法
```

 

f)    使用抽象类的形式即把至少一个父类函数方法定义为纯虚函数，并且这个方法只有声明没有内容定义。使用了抽象类后，父类student依然可以有年龄姓名等基本成员，因为这些成员是所有学生都有的，但父类没有了自己的study方法”好好学习”了，因为研究生本科生中小学生每个阶段都有自己的学习内容，所以具体是study函数内容，靠子类去具体实现。

### 20. 子类的析构与虚析构函数

a)    和上一次讲析构函数一样，这里不做深入，子类的析构函数形式和一般的析构函数完全一样，即：

```cpp
postgraduate :: ~postgraduate()
```

b)   设置虚析构函数：

父类定义中声明：virtual ~student ();    //父类里设置虚析构函数，父类中有name age两个属性

子类定义中声明： ~postgraduate ();     //子类不设虚函数，我们曾经在子类中设了一个research属性

c)    如果没有虚析构函数，delete子类对象研究生bb时，系统只会清除子类属性research所占用的那块内存，而父类属性所占用的姓名和年龄还在内存中。

d)   采用虚析构函数后，delete子类对象时，会先清除子类新增属性占用的内存，随后自动清除父类属性占用的内存。

 

 

### 阶段总结：

第三部分主要讲了：

公有和私有，公有部分可以随便使用，私有函数和属性需要在类的内部通过公有外壳来调用。

类的派生与继承，子类可以获得父类的成员和方法，并且还能增加自己的成员和方法。

类的继承public, private, protect分不同情况。

子类构造函数，创建子类对象时，会先调用父类构造函数，随后再调用子类构造函数

实现相同函数名执行不同内容：1重载，2 隐藏，3 覆盖(多态)

什么是多态：在父类子类所有函数声明都加virtual，并且定义父类指针，随着指针指向不同的对象，程序会自动找到不同类里的同名方法

什么是抽象类：父类只是拥有一些共有的属性和方法，比如年龄姓名，但并不能创建自己的对象，创建对象依赖子类实现

设置虚析构函数的目的：为了在销毁子类对象时，同步清除子类属性和父类属性占用的内存

## 后记：

在整理systemverilog 资料时发现这样一篇讲面向对象编程的文章，通过非常形象简介的语言可以帮助你快速了解什么是面向对象编程，面向对象编程有什么优点，在IC设计中，UVM验证方法学是非常重要的验证方法，主要是起可移植性非常好，利用其中的phase机制，可以容易实现跨平台移植，UVM基于systemverilog，systemverilog借鉴了很多c++特性，也是一种面向对象的设计方法。故掌握面向对象设计无论是在软件设计或者硬件设计都非常重要。因此将这篇文章重新排版，作为一种笔记梳理方式。并想通过blog分享给需要了解面向对象编程的同学。

​																																	廖梁钢 2020 04 20 与于佳