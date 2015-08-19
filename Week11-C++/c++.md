# 1.Const 用法 #
## a.const 修饰成员变量  ##
    int a1=3;   ///non-const data
    const int a2=a1;///const data
    int * a3 = &a1;   ///non-const data,non-const pointer
    const int * a4 = &a1;   ///const data,non-const pointer
    int * const a5 = &a1;   ///non-const data,const pointer
    int const * const a6 = &a1;   ///const data,const pointer
    const int * const a7 = &a1;   ///const data,const pointer

## b.const修饰函数参数 ##

## c.const修饰成员函数 ##
### (1)const修饰的成员函数不能修改任何的成员变量(mutable修饰的变量除外) ###
### (2)const成员函数不能调用非const成员函数，因为非const成员函数可以会修改成员变量 ###

## d.const修饰函数返回值 ##
### (1)指针传递 ###
如果返回const data,non-const pointer，返回值也必须赋给const data,non-const pointer。因为指针指向的数据是常量不能修改。
### (2)值传递 ###

如果函数返回值采用“值传递方式”，由于函数会把返回值复制到外部临时的存储单元中，加const 修饰没有任何价值。所以，对于值传递来说，加const没有太多意义。
所以：

**不要把函数int GetInt(void) 写成const int GetInt(void)。**

**不要把函数A GetA(void) 写成const A GetA(void)，其中A 为用户自定义的数据类型。**

问题1：const T& f(int v) const; 这个函数，non-const对象可以调用吗？

可以调用的话，比如operator[]这个重载函数，v[i] = 3;这样不是更改了内容吗？
    
    class A{
    public:
    	A(string &s) :_text(s){}
    	const char &operator[](size_t position) const{
    		return _text[position];	
    	}
    	/*char &operator[](size_t pos) {
    		return _text[pos];
    	}*/
    	const string get() const {
    		return _text;
    	}
    private:
    	string _text;
    };
    
    	string s("hello");
    	const A a(s);
    	A b(s+"1");
    	b[0] = '2';//编译出错
    	cout << a[0] << endl;
# 2.	类型转换 #

## a. reinterpret_cast<new_type>(expression) ##
该操作符用于将一种类型转换为另一种不同的类型，比如可以把一个整型转换为一个指针，或把一个指针转换为一个整型，因此使用该操作符的危险性较高，一般不应使用该操作符。

reinterpret_cast（重述转换）主要是将数据从一种类型的转换为另一种类型。所谓“通常为操作数的位模式提供较低层的重新解释”也就是说将数据以二进制存在形式的重新解释。比如：

    int i;
    char *p = "This is a example.";
    i = reinterpret_cast<int>(p);

此时结果，i与p的值是完全相同的。reinterpret_cast的作用是说将指针p的值以二进制（位模式）的方式被解释为整型，并赋给i，一个明显的现象是在转换前后没有数位损失，即一定不改变元数据。


## b. dynamic_cast<new_type>(expression) ##
将一个指向派生类的基类指针或引用转换为派生类的指针或引用。（安全向下转型）

dynamic_cast转换符只能用于含有虚函数的类，其表达式为 dynamic_cast<类型>(表达式)

    A *pb = new B;
    B *p = dynamic_cast<B *>(pb);
dynamic_cast转换符只能用于指针或者引用。dynamic_cast转换符只能用于含有虚函数的类。dynamic_cast转换操作符在执行类型转换时首先将检查能否成功转换，如果能成功转换则转换之，如果转换失败，如果是指针则反回一个0值，如果是转换的是引用，则抛出一个bad_cast异常，所以在使用dynamic_cast转换之间应使用if语句对其转换成功与否进行测试，比如pd = dynamic_cast(pb); if(pd){…}else{…}，或者这样测试if(dynamic_cast(pb)){…}else{…}。 


## c. static_cast<new_type>(expression) ##
该操作符用于非多态类型的转换，任何标准转换都可以使用他，即static_cast可以把int转换为double，但不能把两个不相关的类对象进行转换，比如类A不能转换为一个不相关的类B类型。static_cast本质上是传统c语言强制转换的替代品。

    int i;
    float f = 166.7f;
    i = static_cast<int>(f);


## d. const_cast<new_type>(expression) ##

const_cast最常用的用途就是删除const属性,唯一具有此能力的c++类型转换操作符

const_cast操作符不能改变类型的其他方面，只能改变const或volatile，即const_cast不能把int改变为double，但可以把const int改变为int。const_cast只能用于指针或引用。

const_cast的用法举例

比如：

    int a = 3; 
	const int *b = &a; 
	int* c = const_cast<int *>(b);
	*c = 4; 
	cout << a << " "<< *c << endl;

这时输出两个4，如果不使用const_cast转换符则常量指针*c的值是不能改变的，在这里使用const_cast操作符，通过指针b就能改变常量指针和变量a的值。

# 3.深、浅拷贝 #


# 4.sizeof 内存对齐 #
struct Test
{
int Num;
   char pcName; 
   short sDate;
   char cha[2];
   short sBa[4];
}

# 5.volatile关键词 #
嵌入式编程中经常用到 volatile这个关键字，在网上查了下他的用法可以归结为以下两点：

一：告诉compiler不能做任何优化

       比如要往某一地址送两指令： 
       int *ip =...; //设备地址 
       *ip = 1; //第一个指令 
       *ip = 2; //第二个指令 
       以上程序compiler可能做优化而成： 
       int *ip = ...; 
       *ip = 2; 
       结果第一个指令丢失。如果用volatile, compiler就不允许做任何的优化，从而保证程序的原意： 
       volatile int *ip = ...; 
       *ip = 1; 
       *ip = 2; 
       即使你要compiler做优化，它也不会把两次付值语句间化为一。它只能做其它的优化。这对device driver程序员很有用。

二：表示用volatile定义的变量会在程序外被改变,每次都必须从内存中读取，而不能把他放在cache或寄存器中重复使用。


       如   volatile char a;   
    		a=0; 
		    while(!a){ 
		        //do some things;   
		    }   
		    doother(); 
       如果没有 volatile doother()不会被执行

# 6.类成员内存分布，包括数据成员、普通成员函数和static静态成员函数 this指针的作用#

**？？？**

一个对象的this指针并不是对象本身的一部分，不会影响sizeof(对象)的结果。this作用域是在类内部，当在类的非静态成员函数中访问类的非静态成员的时候，编译器会自动将对象本身的地址作为一个隐含参数传递给函数。也就是说，即使你没有写上this指针，编译器在编译的时候也是加上this的，它作为非静态成员函数的隐含形参，对各成员的访问均通过this进行。


# 7.多态的理解，虚函数，虚函数表，虚继承？，空间分配？ #

# 8.static用法 #

**静态全局变量**有以下特点： 

- 该变量在全局数据区分配内存； 
- 未经初始化的静态全局变量会被程序自动初始化为0（自动变量的值是随机的，除非它被显式初始化）； 
- 静态全局变量在声明它的整个文件都是可见的，而在文件之外是不可见的；　

**静态局部变量**有以下特点：

- 该变量在全局数据区分配内存；
-  静态局部变量在程序执行到该对象的声明处时被首次初始化，即以后的函数调用不再进行初始化； 
-  静态局部变量一般在声明处初始化，如果没有显式初始化，会被程序自动初始化为0； 
-  它始终驻留在全局数据区，直到程序运行结束。但其**作用域为局部作用域，当定义它的函数或语句块结束时，其作用域随之结束；**

**静态函数**的好处： 

- 静态函数不能被其它文件所用； 
- 其它文件中可以定义相同名字的函数，不会发生冲突；

**静态数据成员**有以下特点： 

- 静态数据成员被当作是类的成员。无论这个类的对象被定义了多少个，静态数据成员在程序中也只有一份拷贝，由该类型的所有对象共享访问。也就是说，静态数据成员是该类的所有对象所共有的。对该类的多个对象来说，静态数据成员只分配一次内存，供所有对象共用。所以，静态数据成员的值对每个对象都是一样的，它的值可以更新； 
- 静态数据成员存储在全局数据区。静态数据成员定义时要分配空间，所以不能在类声明中定义。

	 静态数据成员初始化与一般数据成员初始化不同。静态数据成员初始化的格式为：
	＜数据类型＞＜类名＞::＜静态数据成员名＞=＜值＞ 

- 静态数据成员和普通数据成员一样遵从public,protected,private访问规则； 
- 因为静态数据成员在全局数据区分配内存，属于本类的所有对象共享，所以，它不属于特定的类对象，在没有产生类对象时其作用域就可见，即在没有产生类的实例时，我们就可以操作它； 

- 类的静态数据成员有两种访问形式：
- ＜类对象名＞.＜静态数据成员名＞ 或 ＜类类型名＞::＜静态数据成员名＞

如果静态数据成员的访问权限允许的话（即public的成员），可在程序中，按上述格式来引用静态数据成员 ； 

- 同全局变量相比，使用静态数据成员有两个优势： 
- 1. 静态数据成员没有进入程序的全局名字空间，因此不存在与程序中其它全局名字冲突的可能性； 
- 2. 可以实现信息隐藏。静态数据成员可以是private成员，而全局变量不能；

**静态成员函数**由于不是与任何的对象相联系，因此它不具有this指针。从这个意义上讲，它无法访问属于类对象的非静态数据成员，也无法访问非静态成员函数，它只能调用其余的静态成员函数。
关于静态成员函数，可以总结为以下几点： 

- 出现在类体外的函数定义不能指定关键字static； 
- 静态成员之间可以相互访问，包括静态成员函数访问静态数据成员和访问静态成员函数； 
- 非静态成员函数可以任意地访问静态成员函数和静态数据成员； 
- 静态成员函数不能访问非静态成员函数和非静态数据成员； 
- 由于没有this指针的额外开销，因此静态成员函数与类的全局函数相比速度上会有少许的增长； 
- 调用静态成员函数，可以用成员访问操作符(.)和(->)为一个类的对象或指向类对象的指针调用静态成员函数，也可以直接使用如下格式：
＜类名＞::＜静态成员函数名＞（＜参数表＞）
调用类的静态成员函数。


# 9.define,const,inline 三者区别#

define,const定义常量区别：
两点： 


- #define 定义的常量，没有进入记号表，不便于调试，
- 不能用来定义class专属常量，不能提供任何封装性。
- 宏替换会产生多份，而后者保证只有一个副本。

define定义的函数存在的问题 
    #define MAX(a, b) ((a) > (b) ? (a):(b)) //得到两个数中的最大值
    
    int a = 5, b = 0;
    MAX(++a, b); //a会被递增两次
    MAX(++a, b+10); //由于b+10>a，a只被递增一次！！
    
在c++中，可以使用inline函数来解决：

    
    template<typename T>
    inline T MAX(const T& a, const T&b)
    {
      return a > b ? a : b;
    }

使用reference-to-const可以确保不对对象进行修改；而inline函数本身可以在编译时进行优化，提高编译速度。


# 10. 面向对象程序设计的三大特征是什么? #
    1.封装(encapsulation): 封装是面向对象方法的一个重要原则。它有两个涵义：第一个涵义是，把对象的全部属性和全部服务结合在一起，形成一个不可分割的独立单位（即对象）。第二个涵义也称作“信息隐蔽”，即尽可能隐蔽对象的内部细节，对外形成一个边界（或者说形成一道屏障），只保留有限的对外接口使之与外部发生联系。这主要是指对象的外部不能直接地存取对象地属性，只能通过几个允许外部使用地服务与对象发生联系。

    2.继承（inheritance）: (单继承和多继承)子类可以继承父类中的属性和操作，也可以定义自己的属性和操作

    3.多态性(polymorphism):在一般类中定义的属性或操作被特殊类继承之后，可以具有不同的数据类型或表现出不同的行为。这使得同一个属性或操作名在一般类及其各个特殊类中具有不同的语义。

# 11.C++程序的内存布局 #
C++程序的内存格局通常分为四个区：

    （1）全局数据区(data area):存放全局变量、静态数据、常量。
    （2）代码区(code area):存放类成员函数、其他函数代码。
    （3）栈区(stack area):存放局部变量、函数参数、返回数据、返回地址。
    （4）堆区 (heap area) :自由存储区。

