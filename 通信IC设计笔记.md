# 通信IC设计笔记

## 介绍

第1章,主要介绍集成电路设计入门,重点讲述 Verilog HDL设计入门、各种设计思想以及设计规范。

第2章,主要面向FPGA开发者,讲述FPGA与ASC设计的不同,并详细讲述FPGA的各种特色应用,ASIC开发者也可以从中借鉴很多很好的FPGA设计理念。

## 1. 集成电路设计与HDL

本章的内容安排如下:

  1.1节从总体上论述什么是IC设计,IC设计的流程以及整体规划的重要性,并强调面向验证和软硬件结合的设计原则。

 1.2节简要介绍 Verilog语法,并通过8个例子讲述如何通过简单的HDL语句描述复杂的硬件电路。本节主要强调用HDL给出的描述需要有具体对应的基础电路原型,并明确时序的  概念。此外还给出各种常用的验证小技巧帮助读者快速进行验证设计。      

1.3节则是对第2节设计方法的一个总结和提炼。本节主要强调必须按照结构化方式设计电路,并需要遵循常规思维模式对复杂模块进行解耦,按照控制与数据分离的原则简化设计。  此外,还讲述了芯片中控制流与数据流的设计方法。        

1.4节则针对IC中的定点化进行论述,强调定点化的实质是人与机器的理解如何达到一致。

1.5节给出HDL语言描述规范,并强调遵循规范实质上是一种IC设计模式,能够有效地增加设计成功率。

 1.6节则引入吞吐率、电路时序等关键性概念,给出各种电路思想,并介绍四种经典电路优化方法:重定时、折叠、展开与脉动阵列。

### 1.1 集成电路设计基础

#### 1.1.1 集成电路概念

### 1.2 Verilog HDL 快速入门

#### 1.2.1 Verilog HDL简介

Verilog HDL诞生于1983年，由Gateway Design Automation公司发明，由于其简洁易用且集成电路设计与HDL17与C语言语法类似，逐渐为公众所接受，并于1995年成为IEEE标准，被称为IEEEStdl364-1995，即Verilog-1995。 

但在随后的使用中，设计人员发现Verilog-1995版本存在许多可以改进的地方。为了解决用户在使用Verilog-1995过程中遇到的问题，标准化组织对Verilog-1995进行了修正和扩展，并发展成为IEEE1364-2001标准，即Verilog-2001。Verilog-2001是针对Verilog-1995的一个重大改进版本，它具备很多新的实用功能，例如通用敏感列表、多维数组、生成语句块、命名端口连接等。目前，Verilog-2001是Verilog的主流版本，基本上所有的EDA工具均支持这一版本。

2005年，Verilog再次进行更新，即IEEE1364-2005标准。该版本只是对上一版本的细微修正。这个版本包括了一个相对独立的新部分，即Verilog-AMS。这个扩展使得传统的Verilog可以对模拟和混合信号系统进行建模。

 2009年，IEEE 1364-2005和IEEE1800-2005两个部分合并为IEEE1800-2009，成为了一个新的、统一的SystemVerilog硬件描述验证语言（Hardware Description and Verification Lan-
guage，HDVL）。该版本于2012年进行了修订，被称为SystemVerilog-2012。

2014年后的主流EDA商业软件（包括ASIC/FPGA）均支持本节所提到的所有Verilog版本，建议读者以Verilog-2001为基础进行学习，有条件的读者则可以从SystemVerilog-2012着手，结合UVM/VMM开展IC项目设计与验证。

#### 1.2.2 Verilog的表达能力

Verilog能够支持以下不同级别的电路描述,读者可以根据应用场景的不同进行选择: 

- 系统级( System):该级别基本属于电路行为级描述,通常用于仿真和高层建模,属于不可综合的范畴;但现在有部分高级综合工具在逐步支持系统级综合。此外,系统级  一般采用 Matlab或者C/C++进行描述。
- 算法级(Algorithm):这一级别重点关注各类复杂算法的实现,不关心具体的实现时序;而本书的描述重点就是将各种算法通过简单的 Verilog代码描述出来,并在此基础  上添加各种时序控制,最终完成算法级描述到RTL级描述的转换。目前很多 Verilog工具或者其他语言综合工具都能够支持基于算法级的描述,并能直接生成RTL代码或者最终的门级电路网表。同样,很多算法级描述采用 Matlab或C/C++实现。
-  RTL级( Register Transfer Level):这一级主要描述电路中的寄存器行为,以及寄存器之 间的组合电路是如何实现的。RTL级的描述是现在HDL设计的主战场,基本上所有的  成熟数字电路设计都是以RTL代码形式存在的。
- 门级(ate-Level-):用于描述逻辑门以及逻辑门之间的连接模型,逻辑综合器的一个典型作用就是将RTL代码转换为门级网表,以形成基本的元器件电路。
- 开关级( Switch-Level-):用于描述器件中三极管和储存节点以及它们之间的连接模型,通常布局布线的结果就是形成开关级电路。

通常在进行大型芯片项目设计时，往往会完整经历系统级设计、算法级设计和RTL级、门级电路设计。通过系统级设计保证正确的软硬件划分；通过算法级的建模来保证设计的系统性能；在算法级验证通过后，再通过在算法中添加时序的方法，将高级语言转化为RTL代码；当RTL代码完成验证后，就进入门级电路设计。上述层次之间的关系如图1-12所示

![image-20200322162528805](https://tva1.sinaimg.cn/large/00831rSTgy1gd2s90rusmj30oa0eigrj.jpg)

#### 1.2.3 第一个Verilog程序：通用加法器

 Verilog作为一门硬件电路描述语言，一种快速熟练掌握它的方法就是不断练习，反复动手实践，通过例子掌握隐藏在语句背后的硬件电路。因此本书通过很多典型例子，由浅入深，快速引导读者掌握Verilog的精髓。

下面是第一个需要学习的Verilog例子，学习该例子的目的是让读者掌握电路模块module的概念。

```verilog
module adderN #(parameter N=4)(
  input [N-1:0] a,
  input [N-1:0] b,
  input cin,
  output cout,
  output [N-1:0] sum
);
  assign {cout,sum} = a + b + cin;
endmodule
```

该例描述了一个4位加法器，从例子可看出整个模块是以module开始，endmodule结束。只有以module开始，以endmodule结束的描述才能被认为是一个完整的电路描述，其余都只能认为是电路片段。一个模块就是一个完整电路，如果有N个模块，这N个模块将通过某种机制结合起来，组成一个大的电路，但这N个模块都是独立运行的，而且是并行执行的。

上面代码的输入(定义为input)为a、b和cin，注意a、b前面有一个[N-1：0]，这表示a与b的位宽为N，最高位为第N-1位，最低位为0位；而cin前面没有位宽设定，则表示1位。对于任意有意义的逻辑变量（wire/reg，输入输出端口等），必须指定其位宽，如果没有显式指定，位宽就默认为1位。

该段代码的输出（定义为output)为sum和cout，其中，sum为标准加法和，位宽为N位，而cout则是1位变量，表示加法进位位。

除了单方向的输入input和输出output外，电路还存在双向输入输出，因此Verilog采用inout命名这类端口。

通用的module模块描述方法如图1-13所示。

![image-20200322164552735](https://tva1.sinaimg.cn/large/00831rSTgy1gd2su8xq30j30l20ay43s.jpg)

该模块隐含以下几个常用的语法点。

1. parameter N=4

parameter类型用来定义模块的全局符号常量，等效于C语言的参数设定。这是一种对模块进行抽象描述的重要手段，通过简单修改parameter设定，就能给出不同的电路模型。pa-rameter的主要目的是提高程序的可读性和可维护性，在对模型IP化时，会大量用到parame-ter型变量。另外，还有一个类似的符号常量localparam，该类型定义与parameter的区别在于，parameter可以被外部调用模块改变，相当于C/C++语言中传值的概念，而localparam仅在本模块内部有效，主要目的是为了提高本模块的可读性。
        在本段代码中可以通过调整参数N实现任意位宽的加法器。

2. assign

assign语句是指连续赋值的意思。加法器是一个组合电路，当输入变化时，输出一定立刻响应，而且这个过程是持续不断的。因此Verilog专门设计assign语句以指定这类连续过程，而不是采用传统的赋值符号“=”。可以认为assign就是描述组合电路的专用语句。

3. {cout,sum}

上述描述方法是一种对两个变量合并赋值的简化描述，HDL语法解析器和综合器将按照如下的理解对上述描述进行解析，并生成最终电路。

```verilog
wire[N:0] adder_temp;
assign cout = adder_temp[N];
assign sum[N-1:0] adder_temp[N-1:0];
```

4. a+b+cin的位宽匹配

这3个数位宽不同，但在Verilog语法中允许这三者直接相加。默认的规则为：算术表达式结果的长度由最长的操作数决定。在赋值语句下，算术操作结果的长度由操作符左端的目标长度决定。表达式中的所有中间结果应取最大操作数的长度，如果这个中间结果会赋值给左端的表达式，则最大操作数长度也包括左端目标长度。

因此a+b+cin的操作规则为：①如果不赋值给某个数，例如！（a+b+cin），则操作位宽在a/b/cin中选择最大值；②如果赋值给某个数dout，例如dout=(a+b+cin)，则操作位宽在a/b/cin/dout中选择最大值。

在这条语句中，a/b/cin的最大位宽为4，但由于cout，sum！表示5位的数据，所以加法器的代码等效于：

```verilog
a+b+cin = {a[N-1], a[N-1:0]} + {b[N-1],b[N-1:0]} + cin
```

一般的加法器会产生1位的进位,因此加法器的输出结果通常需要比输入位宽多1位,这是算法电路设计中的一种标准写法。

**例子小结**

读者在理解 module 语法的基础上，需要理解 module 模块是整个电路描述中的最基本单元;任何复杂的 电路都是通过多个简单的 module 搭建而成的。任何一个模块都是独立的存在，各个模块都是并行运行的。

此外还需要理解，Verilog 的描述基础是 bit，任何一个运算或表达式都是基于 bit 进行表达的;在此基础 上再去理解多个不同 bit 位宽的数据运算时，如何进行 bit 位对齐，中间结果的位宽如何取值，赋值的结果 位宽如何设定等。

为方便读者查阅 Verilog 语法，本书在附录中包含有标准的 Verilog 语法说明材料。欢迎读者在阅读例子 时对比参照，从而真正了解语法背后所要表达的思想。

#### 1.2.4 第二个verilog程序：多路选择与运算操作

本节需要描述的电路结构如图 1-14 所示，该电路是一个标准的四选一选择器，输入 bit 的位宽通过参数 N 指定，默认为 2bit，由控制选择信号 sel 决定选择哪一个结果输出，并将结果赋予 mux_out。

![image-20200322164743415](https://tva1.sinaimg.cn/large/00831rSTgy1gd2sw5qbktj30cu09wmzf.jpg)

该电路的 Verilog 描述代码如下：

```verilog
module mux #(parameter N=2)(
  input [N-1:0] a, // sel=00时，选择该输入
  input [N-1:0] b, // sel=01时，选择该输入
  input [N-1:0] c, // sel=10时，选择该输入
  input [N-1:0] d, // sel=11时，选择该输入
  input [1:0] sel, //选择器
  output[N-1:0] mux_out);// 选择器结果输出
  reg [N-1:0] mux_temp; // 临时变量，用于防止其他调用者误认为输出锁存 assign mux_out=mux_temp;

  //always_comb //该语句在systemverilog中可以替换下面的语句并检查 always @ (a or b or c or d or sel)
  case (sel)
  	0 : mux_temp = a; 
    1 : mux_temp = b; 
    2 : mux_temp = c; 
    3 : mux_temp = d;
    default : $display("Error with sel signal"); 
endcase
endmodule
```

上述代码包含三个语法点:(1)\$display 语句，该语句与 C 语言中的打印语句非常类似，能够在仿真时 输出各类中间变量信号，类似语句还有\$fprintf 等; (2)利用 case 语句进行分支选择; (3)通过 always 语句描述组合电路实现。

##### 1. Case语句语法

case 语句是一个多路条件分支选择的描述，其语法如下:

```verilog
case (case_expr)
case_item_expr{ ,case_item_expr} : procedural_statement 
  ...
	...
[default:procedural_statement]
endcase
```

case 语句首先对条件表达式 case_expr 求值，然后依次对各分支项求值并进行比较，第一个与条件表达 式值相匹配的分支中的语句被执行。可以在 1 个分支中定义多个分支项;这些值不需要互斥。缺省分支覆 盖所有没有被分支表达式覆盖的其他分支。case 的缺省项必须给出写，这样能够防止产生锁存器电路。

利用 case 语句描述组合电路时，必须提供所有的选择结果情形，否则 HDL 综合器将根据描述，自动生 成锁存电路。而锁存电路(latch)路，相对组合电路，会带来额外时钟延迟，并引入异步时序，不适合常规 电路设计。此外，Latch 在可测试性设计中，需要额外的逻辑实现。因此，建议读者在描述电路时，一定注 意不要编写不完全分支的电路语句，以免生成锁存 Latch。

##### 2. always语句语法

在 Verilog 电路描述中，always 语句是表明所描述的电路行为将被重复执行，而执行机制是通过对一个称 为敏感变量表的事件驱动来实现。

可综合的 always 语句只有两种写法，第一种为电平事件驱动，当敏感事件列表中的信号发生变化，就执 行内部的语句块，代码书写方式如下:

```verilog
always @ (敏感事件列表)
  //always	内部执行语句快
always @ (a or b or c or d or sel)//abcd sel均为敏感信号列表
```

第二种写法为边沿除法驱动，当敏感信号发生跳变时，就执行内部的语句块，典型的代码书写方式如下:

```verilog
always @ (posedge clock )
always @ (posedge clock or posedge reset )
```

上述语句中的`posedge` 均可替换为`negedge ` 。其中 表示`posedge`上升沿触发，而`negedge ` 表示下降沿触发。该语句表达的含义就是当 clock 信号跳变瞬间，执行语句块的内容。 

而对于没有提供敏感事件列表的语句，则是无条件执行，通常用于测试验证代码，属于不可综合。诸如下面的语句表示总是对 clock 信号取反，而这个取反过程会延迟 10ns，因此会产生一个 20ns 为周期的clock 方波。

```verilog
always #110 clock = ~clock;
```

初学者只需要记住，第一种`always`写法是组合电路的描述方法，而第二种`always`写法则是时序电路的描述方法。`Always` 语句既可以描述组合电路，也可以描述时序电路，而 reg 并不都是寄存器类型，也有 可能为连线。对于 reg 类型不能准确定义为寄存器，而是有可能为锁存器或者组合电路中的临时连线，这对 Verilog 语言设计而言，可以认为是一个较大缺憾，因此在后来的 SystemVerilog 语言中，通过引入 `always_comb` 、 `always_latch`、`always_ff` 来弥补 reg 以及 always 语句描述能力不足的问题。例如:

```verilog
always_comb #1ns a = b & c; //组合电路写法
always_latch if(ck) q <= d; //锁存器电路写法 
//下面内容为标准的时序可综合电路写法
always_ff@(posedge clock iff reset == 0 or posedge reset) begin
	r1 <= reset ? 0 : r2 + 1; 
  ...
end
```

1. **always语句的并行性**

在逻辑电路中，各个硬件都是并行执行的。站在电路角度讲，所有的 D 触发器都是并行执行的，而驱动源就是时钟。因此在 HDL 描述中，每一个带时钟的 always 语句块都是并发执行的。例如下面的例子，out1 赋值和 out2 赋值是同时的，没有先后顺序。

```verilog
always@(posedge clk) 
  begin
		out1<=in1;
  end
always@(posedge clk) 
  begin
		out2<=in2; 
  end
```

但在 always 语句块内部，任何一个语句块(以 begin 开始，end 结束)都是串行执行的，只是存在赋值立刻生效还是事后生效的差异，即后面将要重点论述的阻塞赋值和非阻塞赋值两种区别。

```verilog
always@(*)
  begin
  	temp=b; 
    b=a;
  	a=temp; 
  end
```

上面的例子，就是一个串行执行的例子，能够完成 a 与 b 的数值交换，如果不是串行执行，上述代码将 很难完成类似各类程序控制。

2. **always 内部的控制流**

目前所有的控制结构可以概括为三种:(1)顺序结构;(2)分支选择结构;(3)循环结构。而这些控制 流在 always 语句内部均能实现。

顺序结构就是标准的顺序执行，这在 always 语句块内部已经天然实现。分支选择结构目前有两类语句支 持，if-else 语句和 case 语句。If-else 语句的语法为:

```verilog
if(condition_1) 
  procedural_statement_1
{else if(condition_2) 
  procedural_statement_2}
{else 
  procedural_statement_3}
```

如果对 condition_1 求值的结果为个非零值，procedural_statement_1 将被执，如果 condition_1 的值为 0、 x 或 z，则 procedural_statement_1 不被执行。如果存在一个 else 分支，那么这个分支被执行。具体例子如下:

```verilog
if(a>b) sel_out<=in1;
else if(a==b) sel_out<=in2; 
else sel_out<=in3;
```

注意， `if`语句与`case`语句一样，在描述组合电路时，一定要给出全部分支情况，否则容易生成锁存 Latch 电路。

对于循环语句，主要包含` for、while、forever、repeat `四类语句，但只有` for `语句才有可能具备可 综合性，其余均为测试验证所准备。

循环语句 for 的语法为:

```verilog
for(表达式 1;表达式 2;表达式 3)语句
```

其实可以将 for 语句理解为:

```verilog
fox(循环变量赋初值:循环结束条件;循环变量增值)执行语句
```

for循环的例子如下，这是最原始的一个 8bit 乘法器实现，其中表示左移，等效于乘以 2 的移位次方:

```verilog
parameter size = 8; 
reg[size-1:0] opa, opb; 
reg[2*size-1:0] result; 
integer bindex; 
result=0;
for( bindex=0; bindex<=size-1; bindex=bindex+1 )
  if(opb[bindex]) result = result + (opa<<(bindex));
end
```

读者在检查逻辑综合结果后很容易发现，该代码实现其实是一个全并行的加法器，for 语句等效于将以下语句完全展开:

```verilog
if(opb[bindex]) result = result +(opa<<(bindex));
```

所以读者在编写 for 语句时，一定要理解这是一个并行完全展开语句，而不是串行多周期执行。

##### 3. 算术逻辑单元例子

下面的代码，通过 case 语句给出了一个最简单 ALU(算术逻辑单元)的描述方法，方便大家加深印象:

```verilog
module alu #(parameter N=8)(
	input [ 2:0] opcode, //操作码
 	input [N-1:0] a, 			//操作数
  input [N-1:0] b,			//操作数
	output reg[N-1:0] out  //操作结果
);
localparam add =3'd0;
localparam minus=3'd1;
localparam band =3'd2;
localparam bor =3'd3;
localparam bnot =3'd4;
always@(opcode or a or b)//电平敏感的 always 块
  case(opcode)
    add: out = a + b; //加操作
    minus: out = a - b; //减操作
    band: out = a & b; //求与
    bor: out = a | b; //求或
    bnot: out = ~a ; //求反
    default:out = 8'hx ; //未收到指令时，输出不定态
 endcase 
endmodule
```

这个例子同样是一个组合电路，总共有 8 种选择，但电路只明确给出前 5 种选择的结果，其余都通过缺省语句设置为不定态结果。

这个例子引入了两个语法:(1)localparam 用于指定本地参数,主要是取代传统的 define 宏定义，因为 \`define 一旦定义后，会全局有效，必须通过\`undef 配套才能使宏定义失效。新版本的 verilog 所以推荐 localparam 用于设定局部宏参数。(2)引入多个运算操作符。

该例子还引入了基数表示法。我们知道，自然数通常是十进制表示，例如16，-32等等，Verilog支持给 变量或信号赋值为自然数，但自然数通常都是默认32位整数，赋予某个变量后会产生各种截断处理，而且在操作时需要十进制转换为二进制，总之就是不够精确和灵活。因此，Verilog最常用的是基数表示法，例如`3'b011，4'd5，5'o27，6'h3f，`就分别表示3bit二进制011，4bit十进制5(为二进制101)，5bit八进制27(为二进制10111)，6bit十六进制3f(为二进制111111)。基数表示法的通用表示为:

```verilog
[size ]'base value
```

其中，size 定义以 bit 位计的常量的位长;base 为 o 或 O(表示八进制)，b 或 B(表示二进制)，d 或 D (表示十进制)，h 或 H(表示十六进制)之一;value 是基于 base 的值的数字序列。值 x 和 z 以及十六进制 中的 a 到 f 不区分大小写。

基数格式的长度定义(size)是可选的。如果没有定义一个整数型的长度，数的长度为相应值中定义的位数。如果定义的长度比为常量指定的长度长，<span style='color:red;;'>通常在左边填 0 补位。但是如果数最左边一位为 x 或 z ，就相应地用 x 或 z 在左边补位。</span>此外，如果长度定义得更小，那么最左边的位相应地被截断。下面是一系列的例子:

![image-20200319120601113](https://tva1.sinaimg.cn/large/00831rSTgy1gcz3w3i7s1j30rc065400.jpg)

4. **运算操作符**

读者很容易发现前面的例子中的运算操作符与 C 语言中的定义完全一致。实际上，Verilog 正是从 C 中借 鉴了各种运算符。Verilog 的各类操作运算符定义如下:

![image-20200322172606555](https://tva1.sinaimg.cn/large/00831rSTgy1gd2u03t1yhj30nz0kp446.jpg)

上述表达式包含三类运算符：

1. 单元运算符:可以带一个操作数,操作数放在运算符的右边。
2. 二元运算符:可以带二个操作数,操作数放在运算符的两边。
3. 三元运算符:可以带三个操作数,这三个数用三目运算符分隔开。

例如:

```verilog
clock=~clock;// ~是一个单元取反运算符,clock是操作数。 
c=a&b; // &是一个二元按位与运算符,a和b是操作数。 
r=sel ? a:b; // ?:是一个三元条件运算符,sel,a,b是操作数。
```

此外，为方便电路描述，使整体代码简洁，Verilog 相对 C 语言引入了具备电路特色的缩减运算符和连接 运算符。

缩减运算符是对单个操作数进行或与非递推运算,最后的运算结果是一位的二进制数。缩减运算符目前支持或与非三种操作。具体运算过程如下:第一步先将操作数的第一位与第二位进行或与非运算,第二步将运算结果与第三位进行或与非运算,依次类推,直至最后一位。例如:

```verilog
wire[3:0] A; 
wire B; 
assign B =&A;
```

等效于以下语句:

```verilog
assign B =((A[0]&A[1])& A[2])& A[3];
```

拼接运算符则与缩减运算符相反，主要目的是将两个或多个信号的某些位拼接起来进行运算操作。拼接运算不消耗任何逻辑资源，只是一个单纯的连线逻辑。其使用方法如下:

```
{信号1的某几位，信号2的某几位，..,..,信号n的某几位}
```

即把某些信号的某些位详细地列出来，中间用逗号分开，最后用大括号括起来表示一个整体信号。例如:

```verilog
{a,b[3:0],c,3'b101}
```

也可以写成如下形式:

```verilog
{a,b[3],b[2],b[1],b[0],c,1'b1,1'b0,1'b1}
```

在位拼接表达式中不允许存在没有指明位数的信号。这是因为在计算拼接信号的位宽的大小时必需知道其中每个信号的位宽。位拼接还可以用重复法来简化表达式，例如:

```verilog
{6{a}}//这等同于{a,a,a,a,a,a,a}，a可为任意比特位宽
```

位拼接还可以用嵌套的方式来表达，例如:

```verilog
{c,{3{a,b}}}//这等同于{c,a,b,a,b,a,b}
```

用于表示重复的表达式如上例中的 6 和 3，必须是常数表达式或者参数。

Verilog 在设计之初，就是考虑采用相同的 C 语言语法吸引大部分逻辑硬件设计者，同时在演进过程中也 同步引入 C 语言新版本的新特性，例如读者可以发现 verilog-2001 新的模块定义方法与 C99 语法非常类似。 而 SystemVerilog-2012 则大量引入 C++面向对象的特性，硬件接口和对象均具备抽象机制。

##### 5. 运算符的优先级

所有的运算符都是有优先级的。逻辑综合器所理解的优先级如下:

![image-20200322173543041](https://tva1.sinaimg.cn/large/00831rSTgy1gd2ua432f1j30a409pwfh.jpg)

如果读者描述的优先级与综合器和仿真器理解的不一致，就会出现错误的结果。所以在进行描述复杂语句时，一定多次确认优先级，如果有疑惑可以通过括号()指定运算规则。

##### 6. 例子小结

本例引入三个特性:(1)case 语句的特性;(2)数制的表示方法;(3)各类运算符。读者需要注意 case 必须添加 default 选项，并了解运算符的计算规则和优先级，尤其记住拼接运算符和缩减运算符的用法。

#### 1.2.5 第三个 Verilog 程序:D 触发器和多路延迟

前面两个例子均为组合电路，下面将介绍时序电路的描述方法。

```verilog
module DFF( 
  input D, 
  input CLK, 
  output reg Q
);
	always@(posedge CLK)
		Q <= D;
endmodule
```

上述代码描述了一个最简单的 D 触发器:输入数据 D，下一个时钟周期就输出 Q。其中数据的存储时刻 是在时钟 CLK 信号的上升沿(跳变瞬间)，即寄存器 Q 在时钟上升沿采样输入数据 D，并更新存储内容; 除时钟 CLK 上升沿的其余时刻，无论 D 如何变化，都不会存储到 Q 寄存器中。只要理解这一点，就明白了数字电路进行时序电路控制的基础。

该代码包含了一个语法点:非阻塞赋值(<=)。阻塞赋值与非阻塞赋值是 Verilog 中专门针对电路行为而 特殊设计的赋值语句。因为对于组合电路而言，信号传递过来是立刻生效，而且生效的值也将继续传递，直 到某个寄存器或存储单元终结;而对 D 触发器或者寄存器而言，对其任何赋予数值，只要不是在触发沿(依 赖于定义的是上升沿还是下降沿触发)，都是无效的，而且输出的值也是一直保持稳定不变，直到新的时钟 沿到来并更新存储的数据。因此对于寄存器的赋值应当是非阻塞赋值(<=)，因为寄存器采样数据发生在当前 模块执行完毕(通常是 always 语句执行完毕)后。而对于组合电路赋值则应当采用阻塞赋值(=)。

在 Verilog 中，根据电路描述的行为特征，定义了两类数据类型:线网类型(net type)和寄存器类型(reg type)。

##### 1. 线网类型

线网类型主要有 wire 和 tri 两种。线网类型用于对结构化器件之间的物理连线的建模。如器件的引脚， 内部器件如与非门的输出等。

由于线网类型代表的是物理连接线，因此它不存贮逻辑值。必须由器件所驱动。通常由 assign 进行赋 值。如 assign A = B + C;当一个 wire 类型的信号没有被驱动时，缺省值为 Z(高阻)。

当信号没有定义数据类型时，缺省为 wire 类型。因此前面的所有 module 端口没有定义数据类型的，全 部都默认为 wire 线网类型。所以通用加法器例子中的输出赋值采用 assign 语句赋值。

对于 tri 类型而言，主要用于定义三态的线网，由于该语句属于不可综合，因此只在仿真测试或功能模型 中使用。

##### 2. 寄存器类型

reg 类型定义语法如下:

```verilog
reg[msb:lsb] reg1, reg2, ...,regN;
```

msb 和 lsb 定义了范围，并且均为常数值表达式。范围定义省值为 1 位寄存器。例如:

```verilog
reg[3:0] count;// 4 bit寄存器 
reg flag;//1 位寄存器
```

此外还有一种专门针对存储器模型(RAM)的定义方法，例如:

```verilog
(* ramstyle ="MLAB"*)reg[31:0] RegFile1[15:0]; 
(* ramstyle ="MLAB"*)reg[31:0] RegFile2[15:0];
```

在 ASIC 设计中，这种描述方式只会被识别为一系列的寄存器堆，并不会被识别为 RAM;ASIC 中应当用 RAM 单元库(IP)例化的方法描述 RAM。而在 FPGA 中，综合器首先将这种描述识别为 RAM 的声 明，并通过识别对象的行为确认描述对象是 RAM 还是寄存器堆。如果后续的描述行为满足 RAM 的特征， 就自动替换为 FPGA 内部内置的 RAM 单元库，否则将识别为寄存器堆。

上例的 RegFile1 与 RegFile2 对象在 Altera FPGA 中，将被识别为 16 个 32bit 位宽的 RAM，而且指定 为 MLAB 类型。

需要注意的是，reg 类型的数据只能在 always 语句块中赋值。

##### 3. 带复位的触发器

这个例子是带异步复位的触发器，其中 reset 是低电平有效

```verilog
module DFF1( 
  input clk, 
  input reset, 
  input d, 
  output reg q
);
// negedge 表示复位信号低电平有， 
  always@(posedge clk or negedge reset)
    if(!reset)
      q <= 0; //异步清 0，低电平有效
    else
      q <= d; 
endmodule
```

如果是同步复位的触发器则应当修改为：

```verilog
module DFF1( 
  input clk, 
  input reset, 
  input d, 
  output reg q
);
	always@(posedge clk) //同步复位无需添加到敏感列表中。 
    if(!reset)
      q <= 0; //同步清 0，低电平有效 
  	else
      q <= d; 
endmodule
```

读者应当很容易找出同步复位与异步复位的区别就是在于:always 语句的敏感列表中是否添加了复位信号。

##### 4. 锁存器latch

前面已经反复谈到，锁存器在同步电路中应当尽量避免，除非做专门的特殊处理，但锁存器还是有一定的优点。因此在此处简单介绍一下锁存器，并描述一下锁存器是如何编码生成的。下面是一个电平敏感型锁存器结构:

```verilog
module latch_N#(parameter N=1)(
 	input clk;
  input [N-1:0] d;
	output [N-1:0] q;
); //电平敏感型的1位数据锁存器 
  assign q = clk ? d : q;
endmodule
```

当时钟信号为高电平时，将输入端信号打入锁存器;当时钟信号为低电平时，锁存原来已打入的数据。通常锁存器数据有效，滞后于控制信号有效。图 1-15 显示了锁存器的工作时序状态:

![image-20200322175022976](https://tva1.sinaimg.cn/large/00831rSTgy1gd2upd30luj30jx08rwie.jpg)

Latch 结构也可以采用 always 语句描述，例如:

```verilog
module latch_N#(parameter N=8)( 
  input clk;
  input [N-1:0] d;
  output [N-1:0] q;
); //电平敏感型的8位数据锁存器 
  always @(clk or d) //电平敏感
    if(clk) q=d; 
endmodule
```

读者可以发现，正是由于 if 语句的不完整，缺少 else 分支，导致 q 变成锁存器。

##### 5. 多级延迟的触发器

下面的代码是对输入信号进行多级延迟的代码：

```verilog
module DFF_N #(parameter N=3)( 
  input clk,
  input reset,
  input [N-1:0] D,
  output reg [N-1:0] Q
);
  reg [N-1:0]d0;
  reg [N-1:0]d1;
  always@(posedge clk or negedge reset)
    if(!reset)begin
      d0 <= 0; //异步清 0，低电平有效 
      d1 <= 0; //异步清 0，低电平有效 
      Q <= 0; //异步清 0，低电平有效
    end
 		else begin
      d0 <= D; 
      d1 <= d0; 
      Q <= d1;
    end
endmodule
```

其中，d0 是 D 的延迟一级采样或者称为 D 的延迟一拍信号，同样 d1 是 D 的延迟两拍信号，而 Q 则是 延迟三拍信号。该电路的主要用法: (1)如果 D 信号相对 clk 属于不稳定信号，则 Q 和 d1 输出相对 clk 属 于稳定信号; (2)可以通过 d1 和 Q 信号获取 D 信号的上升沿或者下降沿; (3)单纯的对 D 信号做延迟操作。

对于情况 1，会涉及时序和亚稳态的概念，后面的章节将会详细描述这种时序的处理方法。图 1-16 是一 个简单的不稳定时序概念示意图:

![image-20200322175653772](https://tva1.sinaimg.cn/large/00831rSTgy1gd2uw51z9rj30kb0c0dit.jpg)

对于情况二，先看如图 1-17 的时序图:

![image-20200322175736182](https://tva1.sinaimg.cn/large/00831rSTgy1gd2uwv0s15j30j30cvgov.jpg)

可以发现原始信号与延迟一拍的信号 d0 的反向信号相与，就是上升沿脉冲;而下降沿则是原始信号取反，然后与延迟一拍信号相与就是下降沿脉冲。如果担心采样不稳定，可以利用延迟两拍的 d1 信号进行相与。如果担心不定态，还可以将脉冲信号进行锁存。总之，信号延迟与信号本身做运算能够以最小代价生成边缘检测信号。实现参考代码如下:

```verilog
module DFF_N #(parameter N=1)( 
  input clk,
  input reset,
  input [N-1:0] D,
  output [N-1:0] D_rising_edge, //上升沿检测 
	output [N-1:0] D_falling_edge //下降沿检测
  );
  reg [N-1:0]d0;
  reg [N-1:0]d1;
  reg [N-1:0]Q;
  always@(posedge clk or negedge reset)
    if(!reset)begin 
      d0 <= 0;
   	 	d1 <= 0;
   		Q <= 0; 
    end
    else begin
      d0 <= D; 
      d1 <= d0; 
      Q <= d1;     
    end
  assign D_rising_edge = ~d0 & D; 
  assign D_falling_edge= d0 & ~D; 
endmodule
```

##### 6 计数器

在 Verilog 中计数器是非常常见的电路，主要用途包括:(1)计数;(2)看门狗;(3)特殊的有限状态机描述。最简单的 N 位计数器，默认计数范围为[􏰆0,$2􏰇^N$− 1􏰈]的写法为:

```verilog
module count#(parameter N=8)( 
  input clk,
  input clear,
  output[N-1:0] cnt_Q
);
  reg[N-1:0] cnt; 
  assign cnt_Q = cnt;
  always@(posedge clk) 
    if(clear)
  		cnt <= 'h0; //同步清 0，低电平有效 
  else  
    cnt <= cnt+1'b1; //减法计数 
endmodule
```

通过 clear 信号清除计数器的数值，然后下一周期开始加 1 计数;当计数器计到能够存储的最大数值时， 例如本例为 8 个 1，即 8'hff 就会自动回到 0，然后开始下一轮计数。

利用计数器进行状态指示，通常是将计数器的值与某一预设值比较，从而得出标志或状态指示，例如上面的计数器当计数到 8'h3f 就报警，可以通过如下的语句实现:

```verilog
assign warning = (cnt == 8'h3f) ? 1'b1 : 1'b0;
```

如果想要实现􏰆0, 􏰉􏰈范围内计数，其中􏰉 ≠ 2􏰇，可以将 语句修改为:

```verilog
always@(posedge clk) 
  if(clear)
    cnt <= 'h0; //同步清 0，高电平有效 else if(cnt==K) 
		cnt <= 'h0; 
	else
		cnt <= cnt+1'b1; //减法计数
```

前面是累加计数，下面给出的是一个既可以递增也能递减，且具备初始值装载和复位的计数器。

```verilog
module updown count #(parameter N = 8)(
	input clk,
	input clear,
	input load,
	input up_down,
	input [N-1:0] preset_D,
	output [N-1:0] cnt_Q
)
	reg[N-1:0] cnt;
	assign cnt_Q = cnt;
	always@(posedge clk)begin
		if(clear)
			cnt <= 'h0;
		else if(load)
			cnt <= present_D;
    else if(up_down)
    	cnt <= cnt + 1;
    else 
    	cnt <= cnt -1;
	end
```

用过单片机或者 DSP 的读者都知道，看门狗定时器(WDT，Watch Dog Timer)是一种非常常见的电路， 它实际上是一个计数器。在初始状态时，看门狗电路首先装载一个大数;当状态机或者程序开始运行后，看 门狗开始倒计数。如果状态机或程序运行正常，每隔一段时间应发出指令或信号让看门狗重新装载一个大 初始值，并再次开始倒计数。如果看门狗减到 0 就认为程序或状态机没有正常工作，就需要强制整个系统复位。

所以上面的计数器电路描述就是一个看门狗电路，只要加上 cnt==0 作为看门复位状态即可;而 load 信 号则是状态机或软件给出的喂狗动作。

##### 7. 分频器与门控使能信号

计数器实质上是对输入的驱动时钟进行计数，因此在某种意义上讲，计数器等同于对时钟进行分频。例如一个最大计数长度为N的计数器，将其最高位作为时钟输出（占空比不一定为1:1)，则工作频率为输入时钟的$\frac{1}{2N}$。通常在ASIC和FPGA中，时钟都是全局信号，都需要通过PLL处理才能使用，但在某些简易场合，计数器输出时钟也是能够使用的，只是需要注意时序约束罢了。
    下面的例子是一个基于计数器的通用时钟分频器，它能够支持任意整数倍分频，占空比尽量接近于1:1。

```verilog
module clock_div_async #( parameter cfactor= 2)( 
  input clk_in, //标准参考时钟输入
  input rst_x, //复位信号
  output clk_out//分频时钟输出
);
  reg clk_loc; //分频时钟的内部变量
  //reg [15:0] cnt;//最大支持65536分频
  reg [7:0] cnt;//最大支持256分频 
  //根据分频因子选择输入等于输出还是分频
  assign clk_out = (cfactor==1)? clk_in : clk_loc;
  always@(posedge clk_in or negedge rst_x) 
    if(!rst_x)begin
    	cnt <= 'd0;
   		clk_loc = 1; end
    else begin
   		cnt <= cnt + 1'b1; 
      if(cnt==cfactor/2-1) //分频时钟翻转
    		clk_loc = 0;
  		else if(cnt==cfactor-1) begin
    		cnt <= 'd0;
   			clk_loc = 1; 
      end
    end 
endmodule
```

此外，还可以通过对计数器状态判断，给出使能信号指示，从而间接达到门控时钟的目。例如，下面的 Q 寄存器，如果 enable 信号不为高，则无法更新内容，将一直保持现有工作状态。这与 Q 输入时钟clk&enable 的组合信号没有太多区别。而 enable 信号可以通过 cnt 的某一个计数区间或状态来获得。

```verilog
assign enable=(cnt[N-1]==1'b1)?1'b1:1'b0; 
always@(posedge clk)
if(enable)
	Q<=D;
```

##### 8. 例子小结

本节所讲的例子，主要是 D 触发器以及相关联的计数器以及分配时钟。需要注意两点: (1)D 触发器只 在指定时钟沿才会采样数据，并存储，而输出在整个时钟周期内维持不变。(2)明确阻塞赋值与非阻塞赋值 的区别。

#### 1.2.6 第四个Verilog程序：function与时序电路组合

前面给出的例子都是简单的赋值语句，而对于复杂的运算功能没有涉及，所以这里列举一个 function 的 例子，该函数的功能是计算有符号数的绝对值。

```verilog
function [width-1:0] DWF_absval;
input [width-1:0] A;
begin
  DWF_absval = ((^(A^A) !== 1'b0)) ? {width{1'bx}}: (A[width - 1] == 1'b0) ? A : (-A);
end
endfunction
```

该例子有两个语法点:(1) 函数的写法;(2)缩减运算符的用法，即自动缩减为 1bit，该语法能够自动判断 A 是否存在不定态或高阻态，主要用于仿真。

##### 1. function的用法

Function 的标准写法如下:

```verilog
function<返回值的类型或范围>(函数名); 
  <端口说明语句>// input XXX; 
  <变量类型说明语句>// reg YYY;
  begin
    <语句>
    ........
    函数名= ZZZ; //函数名就相当于输出变量;
  end
endfunction
```

函数的语法为:

- 定义函数时至少要有一个输入参量;可以按照 ANSI 和 module 形式直接定义输入端口;例如:

```verilog 
function[63:0] alu (input[63:0] a, b,input[7:0] opcode )
```

- 在函数的定义中必须有一条赋值语句给和函数名具备相同名字的变量赋值;

- 函数的定义不能包含有任何的时间控制语句，即任何用#、@、或 wait 来标识的语句;

- 函数不能启动任务;

- 如果描述语句是可综合的，则必须所有分支均赋值，不允许存在不赋值情况，只能按照组合逻辑方

  式描述。

##### 2. function 与触发器电路结合

下面的例子是完整的 function 用法例子，function 本身描述的是组合电路，但赋值给某个触发器。

```verilog
module MAC #(parameter N=8)( 
  input clk,
  input reset,
  input [N-1:0] opa, 
  input [N-1:0] opb, 
  output reg[2*N-1:0] out
  ）；
function[2*N-1:0] mult; //函数定义，mult 函数完成乘法操作 
  input[N-1:0] opa; //函数只能定义输入端
	input[N-1:0] opb; //输出端口为函数名本身
	reg[2*N-1:0] result;
  integer i; 
  begin
  	result = opa[0]? opb : 0; 
    for(i=1;i<=N;i=i+1) begin
      if(opa[i]==1) result=result+(opb<<(i-1));
    end
 	mult=result; 
  end
  endfunction
  
  wire[2*N-1:0] sum;
	assign sum = mult(opa,opb) + out; 
  always@(posedge clk or negedge reset) 
    if(!reset)
    	out<=0; 
  	else
  		out<=sum; 
endmodule
```

这段例子的含义是:将两个 bit 的数据相乘，并与当前已经缓存的数据相加，所得到的结果作为输出。这里的乘法电路实现是通过 bit 移位加实现的。实现流程图如图 1-16 所示:

![image-20200322222508452](https://tva1.sinaimg.cn/large/00831rSTgy1gd32n9gd1uj30mp0i8tdq.jpg)

上述代码包含电路实现原理图如图 1-19 所示:

![image-20200322222556769](https://tva1.sinaimg.cn/large/00831rSTgy1gd32o2f9fyj30ms0ahwhs.jpg)

整个电路图中 function 是对于移位累加部分，而 always 描述数据的存储过程。于是电路被很清晰的划分为两部分:(1)计算函数部分，这部分是纯组合电路实现;(2)D 触发存储部分。整体可抽象为:$􏰌􏰍􏰎out = 􏰏􏰐f_x(a􏰑, b􏰒, c... ,n 􏰓)，$描述伪代码为:

```verilog
module abstract_circuit#(parameter N=1)(
  input clk,
  input reset,
  input a,
  input b,
  ... //省略部分输入描述 
  input n,
  output reg[N-1:0] out
);
  function[N-1:0] fx; //函数定义，mult 函数完成乘法操作 
    input a; //函数只能定义输入端
		input b; //输出端口为函数名本身
		...
		input n; //输出端口为函数名本身 
    begin
 			fx=组合函数运算(a,b,....,n) 
    end
	endfunction        
  always@(posedge clk or negedge reset) 
    if(!reset)
			out<=0; 
  	else
			out<=fx(a,b,....,n); 
endmodule
```

这种方法描述出来的电路图如图 1-20 所示:

![image-20200322223226059](https://tva1.sinaimg.cn/large/00831rSTgy1gd32uszarkj30ih09udin.jpg)

所以通过上述方式，如果不考虑时序或者最高工作频率的问题，任意一个通信算法，只要给出函数形式，就可以按照上面的模式形成电路。

但读者肯定会想到一个问题:如果函数 $fx()$是一个简单的加法减法，上述实现方法肯定没有问题，但是 如果函数 $fx()$是许多个超越函数诸如􏰔􏰕􏰖􏰗􏰘等的组合结果，则肯定会有疑惑，电路能够这样简单实现吗?

这种疑惑是非常正确的，这时候就需要建立一个时序的概念，即函数 $fx()$只能够承担有限的计算量，而 超出的计算量需要进行分解，变成多个小的$f􏰙_n(􏰐)$实现。这种概念就是朴素的流水线电路设计。

##### 3. Verilog电路时序模型的建立

众所周知，电磁波的传播速度是光速，IC 内部信号的传导速度也是光速，但这个的前提是携带信号不发生变化。如果一个信号由 0 变为 1，实际上是对后面的负载电路整个进行充放电，而这个是需要时间的。任何一个电路都可以按照如下的 RCL(阻容和电感)模型进行描述:

![image-20200322223627250](https://tva1.sinaimg.cn/large/00831rSTgy1gd32z0cmb3j30ky0dfn1y.jpg)

所以，任何的函数计算可以简化为一系列的 RC 电路组合，这些电路或串接或并联很多的负载。<span style='color:red;;'>当输入发生 01 跳变时，等效于对后面的负载电路进行充放电，</span>而每级负载都会有对应的延迟，如果串接延迟过多 就必然会导致末级响应过慢，导致无法在高时钟频率下工作。

因此站在电路角度，时序优化的核心工作，就是降低后级的 RC 负载、增强前级的驱动能力。

##### 4. 基于算法视角的时序优化

前面提到了，如果设计的函数􏰏(􏰐)过大，就会需要进行函数拆解，从而使单个函数负载和时延控制在一 定的范围内。经典的分解方法有三种:
$$
\begin{aligned}
(1)\quad &f(x)=f_{1}(x)+f_{2}(x)\\
(2)\quad &f(x)=g_{1}(x) \times g_{2}(x)\\
(3)\quad &f(x)=m(y), y=n(x)
\end{aligned}
$$
这三种拆解方法通常是混合应用，最终得到最佳的实现方案。

1. **累加拆解**

这类拆解是针对类似需求通过累加求和得出最终结果而设计的，例如对于 FFT 而言，计算过程为:
$$
F[k]=\sum_{n=0}^{N-1} f[n] e^{-\mathrm{j} \frac{2 \pi}{N} k n} \quad 0 \leq k<N-1
$$
当􏰪 = 64或者更大时，一次性计算出􏰞􏰆􏰉􏰈，在逻辑芯片设计中可能性很低。所以此时就应当按照第一类方式进行拆解。即:
$$
F[k]=f_{0}(x)+f_{1}(x)+f_{2}(x)+\cdots+f_{N-1}(x)
$$
其中$f_{i}(x)=f[i] e^{-\mathrm{j} \frac{2 \pi}{N} k i}$

此时的计算步骤就变为如下的步骤:

```verilog
ACC_SUM=0
loop:
if(i<N)
  fi(x) = 计算fi(i)
  ACC_SUM = ACC_SUM + fi(x)
  i=i+1;
	goto loop
else
  输出最终结果=ACC_SUM//
```

可以发现，只需要单步计算分拆为 N 步计算，同时增添一个中间结果累加，即可在有限的时间内完成。 对应的 Verilog 实现伪代码如下，此时会引入 Verilog 设计中的控制流或者控制状态机的写法。

```verilog
module abstract_fx_add#(parameter BIT_W=8,parameter N=64 )(
	input clk,
	input reset,
	input start_fx,				//启动计算整个函数标志
  input [BIT_W-1:0]x_in, //每个节拍输入一个新的x，需要与start_fx配合
	output fx_finish,	
	output [BIT_W-1:0] fx_out
);
reg [7:0] control_cnt;		//控制计数器，大于等于N就停止
always @(posedge clk or negedge reset)
 if(!reset)
 	control_cnt<=0;
 else if(start_fx)
	control_cnt<=0;
 else if(control_cnt<N)
 	control_cnt<=control_cnt+1'b1;
//根据控制计数器，判断计算是否结束，目前需要计算N次
assign fx_finish=(control_cnt==N) ? 1'b1 : 1'b0;

function[BIT_W-1:0] subfx;
  input [BIT_W-1:0] x;
  begin
  	subfx=fx_i(x);
  end
endfunction

reg [2*BIT_W-1:0] sum_acc;
always @(posedge clk or negedge reset)
 if(!reset)
 	sum_acc<=0;
 else if(control_cnt<N)
  sum_acc<=sum_acc+subfx(x_in);		//对分拆结果累加
//输出最终的结果，截位至有效位，后面有专门章节论述
assign fx_out=sum_acc[2*BIT_W-1:BIT_W];
endmodule
```

这类拆解的关键包含两点:(1)设计一个计数器，用于统计当前的累加计算次数，并给出状态指示。(3) 单独的子函数计算以及将单次子函数计算结果与中间缓存结果合并。

实现的原理图如图 1-22 所示:

![image-20200322225316099](https://tva1.sinaimg.cn/large/00831rSTgy1gd33ghqjngj30m80egwiw.jpg)

第一种拆解实质是将一步完成的工作，分解为􏰪个小的步骤完成，整体完成的时间拉长了。后面的章节将会提到，这种设计方法称为折叠(fold)。读者也会发现，这里面引入了控制流的概念。

2. **累乘拆解**

针对第一步拆解后的子函数􏰏􏰖$f_{i}(x)=f[i] e^{-j \frac{2 \pi}{N} k i}$􏰤􏰖，读者很容易发现也不是简单一步就能够完成计算的。因为求􏰔 $e^{-j \frac{2 \pi}{N} k i}$肯定是一个非常耗时的算法，即使计算完毕，后面还有一个耗时的复数乘法在等待。整体电路描述如图 1-23 所示:

![image-20200322225621408](https://tva1.sinaimg.cn/large/00831rSTgy1gd33kbiev5j30j40d0q6w.jpg)

所以这就引出了第二类拆解，累乘拆解。将􏰏􏰖 (􏰐)分解为如下步骤，每个步骤占用一个运算周期:
$$
\begin{aligned}
&\text {计算}  g_{2}(x)=e^{-j \frac{2 \pi}{N} k i}\\
&\text{令} g_{1}(x)=f[i]\\
&\text {计算}f_{i}(x)=g_{1}(x) \times g_{2}(x)
\end{aligned}
$$
所以，读者可以对比一下两者的计算过程，具体如图 1-24 所示:

![image-20200322230003865](https://tva1.sinaimg.cn/large/00831rSTgy1gd33ny6n5cj30k10c5gq6.jpg)

第二类拆解的本质是通过中间插入寄存器，将耗时较长的运算链打断，从而达到时序优化，电路在较高的工作频率工作的目的。

图 1-25 为整体拆解过程的抽象图:

![image-20200322230058160](https://tva1.sinaimg.cn/large/00831rSTgy1gd33ohrrtlj30jf0cmn0r.jpg)

例如原始程序为：

```verilog
module decompose2 #(
parameter A_width = 16,
parameter B_width = 16
)(
input                    CLK,
input [A_width - 1 : 0]  A,
input [B_width - 1 : 0]  B,
output reg[A_width+B_width-1:0] PRODUCT
);
localparam width = A_width + B_width;
reg [width - 1 : 0] temp_a;
reg [width - 1 : 0] temp_b;

parameter angle_width = A_width;
parameter sin_width = width;
parameter cos_width = width;
`include "DW02_cos_function.inc"
`include "DW02_sin_function.inc"

function[width-1:0] gx1;
  input [A_width-1:0] x;
  begin
    gx1=DWF_cos(x);// gx1的内核函数
  end
endfunction

function[width-1:0] gx2;
  input [B_width-1:0] x;
  begin
    gx2=DWF_sin(x);// gx2的内核函数
  end
endfunction

//Sign extending
always @( A or B) //组合电路
begin
  temp_a = gx1(A); //将fx拆解为gx1与gx2的乘积
  temp_b = gx2(B);
end

//Multiplier - product with a clock latency of one
always @ ( posedge CLK )
  PRODUCT <= temp_a * temp_b; //乘法完成后存储

endmodule

```

分拆后的程序就是将 与 缓存到 D 触发器中，而乘法则从稳定后的触发器输出结果。所以分拆代码相对原始代码，只需要加上寄存器缓存即可，即代码变更为:

```verilog
always @ ( posedge CLK )//时序电路，插入流水线寄存器
  begin
	 temp_a <= gx1(A); //将fx拆解为gx1与gx2的乘积	
   temp_b <= gx2(B);
  end
```

3. **函数嵌套模式拆解**

继续针对前面的 FFT 表达式，可以发现$g_{2}(x)=e^{-j \frac{2 \pi}{N} k i}$ 􏰤􏰖，可以分解为两个步骤:

- 计算$\frac{2 \pi}{N} k i$，得出旋转的角度$\theta=\frac{2 \pi}{N} k i$
- 计算$e^{-j \theta}=\cos \theta+j \sin \theta$

也就是，$f(x)=e^{-j \theta}, \theta(i)=\frac{2 \pi}{N} k i$这种函数嵌套模式。这类模式在计算诸如多项式求和中应用广泛:
$$
f(x)=\sum_{i=0}^{N} c_{i} x^{i}
$$
因为􏰏$f(x)$可以化简为:$f(x)=\sum_{i=0}^{N} c_{i} x^{i}=\prod_{i=0}^{N}\left(a_{i}+x\right)$，即通过$\left(a_{i}+x\right)$个多项式相乘即可实现。按照标

准定义:􏰏$f(x)=m(y), y=n(x)$，对应有
$$
\begin{aligned}
&y_{i}=n(x)=\left(a_{i}+x\right)\\
&f(x)=m(y)=\prod_{i=0}^{N} y_{i}
\end{aligned}
$$
在矩阵变换和分组码纠错中，也存在大量的类似拆解方法。例如，对于 RS 等分组码在求伴随多项式时， 需要计算如下的矩阵:
$$
S^{T}=H R^{T}=\left[\begin{array}{ccccc}
(a)^{n-1} & \cdots & (a)^{2 t} \cdots & a & 1 \\
\vdots & \ddots & \cdots & \cdots & \vdots & \vdots \\
\left(a^{2 t}\right)^{n-1} & \cdots & \left(a^{2 t}\right)^{2 t} \cdots & a^{2 t} & 1
\end{array}\right]\left[\begin{array}{c}c_{n-1} \\
\vdots \\
c_{1} \\
c_{0}
\end{array}\right]=\left[\begin{array}{c}
s_{1} \\
\vdots \\
s_{2t-1} \\
s_{2t}
\end{array}\right]
$$
其中，􏰱􏰖 $c_i$为输入，􏰻$s_i$为需要计算的结果。

个矩阵算法如果按照常规算法，计算过程非常的繁琐，但如进行拆解后，就会发现:

$s_{i-1}$与$s_i$之间的单哥乘积项只相差$\left[a^{n-1} \quad a^{n-2} \quad \cdots \quad 1\right]$倍数。所以可以利用$s{i-1}$的中间计算结果，计算出$s_i$中间结果。$s_{i-1}$和$s_i$之间等效相差如下的乘法系数：
$$
H=\left[\begin{array}{lllllll}
a^{n-1} & a^{n-2} & \cdots & a^{2 t} & \cdots & a & 1
\end{array}\right]
$$
第一次的数据输入为$\left[\begin{array}{ccccccc}c_{n-1} & \mathbf{c}_{n-2} & \cdots & c_{2 t} & \cdots & c_{1} & c_{0}\end{array}\right]$，而后续第􏰫次滤波器输入为$\left[\begin{array}{llllllll}c_{n-1} a^{(i-1)(n-1)} & \cdots & c_{2 t} a^{(i-1) 2 t} & \cdots & c_{1} a^{i-1} & c_{0}\end{array}\right]$，这个序列正是计算第􏰫$i-1$次的中间结果。

这个例子的详情可以参见后续信道编解码中关于 RS 和 BCH 译码的章节，而实现的电路图如图 1-26 所示：

![image-20200323150235498](https://tva1.sinaimg.cn/large/00831rSTgy1gd3vh2fs4fj30li0ce0yg.jpg)

第三种拆解的电路模型，如图 1-27 所示:

![image-20200323150306127](https://tva1.sinaimg.cn/large/00831rSTgy1gd3vhl9mhmj30ll0d5dkm.jpg)

而对应的程序例子如下,其中原始代码如下:

```verilog
module decompose3 #(parameter width = 16)(
input                 CLK,
input [width-1:0]     A,
output reg[width-1:0] PRODUCT
);

reg [width - 1 : 0] temp_a;
reg [width - 1 : 0] temp_b;

parameter angle_width = width;
parameter sin_width = width;
parameter cos_width = width;
`include "DW_sqrt_function.inc"
`include "DW02_sin_function.inc"

function[width-1:0] nx;//nx=sin(x)
  input [width-1:0] x;
  begin
    nx=DWF_sin(x);// nx的内核函数,可替换任意函数
  end
endfunction

function[width-1:0] my;//my=sqrt(y)
  input [width-1:0] y;
  begin
    my=DWF_sqrt_tc(y);// my的内核函数,可替换任意函数
  end
endfunction

always @( A or temp_a )//组合电路，用于描述f(x)实现过程
begin
  temp_a <= nx(A); //fx可分解为函数嵌套f(x)=m(y),y=n(x)
  temp_b <= my(temp_a);
end

//Multiplier - product with a clock latency of one
always @ ( posedge CLK )
  PRODUCT <= temp_b; //存储temp_b

endmodule

```

进行函数嵌套修改后,对应的关键代码就是插入流水线寄存器,实现代码如下:

```verilog
always @ ( posedge CLK )//时序电路，插入流水线寄存器
 begin
   temp_a <= nx(A); //将fx分解为函数嵌套f(x)=m(y),y=n(x)  
   temp_b <= my(temp_a);
 end
```

4. **时许优化小节**

前面的三种拆解方法，核心都是在保证输入和输出结果不变的前提，通过插入寄存器或者改变输入输出的时间关系，实现时钟工作频率提高的方法。读者可以按照这种思路，拓展出更多的拆解方法。本章将在后面的内容详细讲述各种改变时序的技巧，包括折叠、展开、重定时和脉动。这些都是 IC 设计的基本功，在 基础单元设计时，就应当充分考虑这类实现方法。

##### 5. 函数与通用描述的转换方法

本小节讲述了 function 的抽象方法，但利用 function 描述有时候稍显繁琐，所以通常采用等效直观方法 进行描述。所以读者需要学会如何将 function 转换为通用描述。以下的例子是本节 function 例子改编为直接 描述形式的代码:

```verilog
module function2combine #(parameter N=8)( 
  input clk,
  input reset,
  input [N-1:0] opa,
  input [N-1:0] opb,
  output reg[2*N-1:0] out 
);
  reg [2*N-1:0]mult; //将function输出例化为变量 
  reg [2*N-1:0]result;//function内部变量例化 integer i;
  always@ (*) //组合电路，替代function描述 
    begin
  		result = opa[0]? opb : 0;
  		for(i= 1; i <= N; i = i+1) 
        begin//注意此处为阻塞赋值，设计电路将完全展开
  				if(opa[i]==1) result=result+(opb<<(i-1)); 
        end  
      mult=result; 
    end
 
  wire[2*N-1:0] sum;
  assign sum = mult + out; 
  always@(posedge clk or negedge reset) 
    if(!reset)
 		 out<=0; 
    else
     out<=sum; 
endmodule
```

读者可以发现，function 其实是一种抽象形式描述，是一种需要实例对象才能使功能生效的描述。如果将 function 的定义头替换为 <span style='color:blue;;'>always</span>@(*)，然后定义必要的变量，就可以直接转换为实际的组合电路。所以 <span style='color:blue;;'>function</span> 转为实际电路描述，可以采用 <span style='color:blue;;'>always</span>@直接例化的方法实现。

标准的转换方法示意图如图 1-28 所示:

![image-20200323151622180](https://tva1.sinaimg.cn/large/00831rSTgy1gd3vvegdajj30l80770wt.jpg)

##### 6. Function小结

站在 Verilog 综合器的角度，读者描述的任何一个组合电路块，或者组合 always 语句，以及所有赋值给 D 触发器之前的逻辑，都将抽象为多值函数:即多个输入与输出的组合。

前面论述的各种拆解方法，所有目的都是为了优化时序，并方便综合软件更快速、更灵活的实现 HDL 到 电路网表的转换。

#### 1.3.7 第五个Verilog 程序：有限状态机

我们知道，任何事物都可以通过这样的模型描述:给定某个输入就有特定的输出，这个输出可能只与当前输入相关，也可能与以前的历史输入相关(数学上称为马尔可夫过程)。

对应到逻辑电路设计上，任何一个复杂的数字电路功能，都可以通过对其输入输出的行为进行准确描述。通常单纯的输入输出行为描述是不能够完全描述特定的复杂行为，因为经常出现相同的输入，但由于历史输入不同导致输出不同的情况，所以如果想要避免上述情况发生，就必须记忆历史记录。所以，通过存储器加上组合电路就能够完整的描述任何复杂的数字电路功能。

在数字逻辑设计中，这种建模方法被称为有限状态自动机(Finite State Machine，FSM)。有限状态机能够 将任意模型简化为如下的描述:将要输出的结果是当前状态以及当前输入的组合。

##### 1. 有限状态机的设计思想

有限状态机 FSM 是由一组状态(states)、一个起始状态(start state)、一组输入和根据输入及现有状态转换 为下一个状态的转换函数(transition)组成的计算模型，具体如图 1-29 所示。

![image-20200323152159660](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w1a6321j30n9076t99.jpg)

有限状态机 FSM 思想广泛应用于硬件控制电路设计，也是软件上常用的一种处理方法(软件上称为 FMM-有限消息机)。它把复杂的控制逻辑分解成有限个稳定状态，但这并不意味着状态机只能进行有限次的处理; 相反，有限状态机是闭环系统，状态可以无限循环跳转，可以用有限的状态处理无穷的事务。

读者可以回忆一下，前面建立的逻辑电路基本模型(图 1-30)，正是一系列的寄存器(D 触发器)和寄存 器之间的组合电路。

![image-20200323152600689](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w5frtzyj30m40ad784.jpg)![image-20200323152633042](https://tva1.sinaimg.cn/large/00831rSTgy1gd3w5zt2wgj30kt0cc78g.jpg)

图 1-30 中的寄存器可以存储关于过去的信息，它反映从系统开始到现在时刻输入的变化;而通过组合逻辑，可以实现对输入信号做特定的动作响应，例如输入某个规定的动作，则电路做出对应的响应，并记录这些动作指示到寄存器中。因此从状态描述的角度讲，标准逻辑电路模型经过简单变形就是一个标准有限状态机(图 1-31)，具备描述任何自然模型的能力。

##### 2. 有限转态机设计

有限状态机的可以分成四大部分：状态机的编码、状态机的复位、状态机的条件转换和状态机的输出

1. **状态机的编码**

状态机编码的主要目的是为了定义参数，从而增强程序的可读性，状态编码有多种:顺序码(二进制码)，格雷码 Gray，独热编码 One-hot。此外还有 Johnson 码和 Nova 三态码。但常用的还是前面提到的三种。

采用格雷码，相邻状态可以减少瞬变的次数，有时不能在所有状态中采用格雷码，则应在状态矢量中增加触发器的数量以减少开关的次数。另一种方法是使用 One-hot 编码，该编码方式使用一组码元，每一个码元仅有 1bit 有效。虽然该编码使用的触发器较多，却可减少组合逻辑的使用，在带多个输出且每个输出是几个状态的函数的状态机更是如此。

2. **状态机的复位**

状态机的复位分成同步复位和异步复位。同步复位是指复位要与分频后的时钟信号同步，触发信号仅为分频后的时钟信号;而异步复位是指复位与分频后的时钟信号和复位信号都参与触发。归根到底就是在出发复位函数的敏感列表中是否把复位键信号作为触发信号。此外，由于电路外部干扰等情况，状态机存在进入未知状态的情况，此时需要对状态机自动复位，而添加看门狗电路就是最佳选择。

3. **状态机的条件跳转**

状态机的跳转可以说是状态机的核心部分，状态机的条件跳转是控制整个状态机状态之间的切换，从而决定输出的情况。通常需要列出类似如下的状态跳转表格，方便整理状态机的设计思路。

![image-20200323153117467](https://tva1.sinaimg.cn/large/00831rSTgy1gd3waxiatsj30gk07swf2.jpg)

4. **状态机的输出**

设计有限状态机的核心就是为了准确描述输入与输出之间的关系。这一步目前有两种描述风格<span style='color:red;;'>:Moore型和 Mealy 型。</span>

<span style='color:red;;'>Moore 型的状态机输出只与当前状态有关，实现电路如图 1-32 所示:</span>

![image-20200323153228129](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wc5g31sj30ms072acr.jpg)

<span style='color:red;;'>而 Mealy 型的状态机输出不仅与当前状态有关，还与输入有关，实现电路结构如图 1-33 所示:</span>

![image-20200323153309461](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wcv1hfjj30m0077dij.jpg)

<span style='color:red;;'>由于 Mealy 型状态机的输出与输入有关，很容易导致输出信号出现毛刺，所以建议读者使用 Moore 型状态机进行状态机描述。</span>

5. **转态机的设计步骤**

有限状态设计可以归纳为以下几个步骤:

第一步:对描述的逻辑进行抽象，得到状态转换图，实际就是将逻辑关系表示为时序逻辑函数的过程。通常包含以下工作。

- 分析给定的逻辑问题，确定输入变量、输出变量以及电路的状态数。通常是取原因(或条件)作为输入变量，取结果作为输出变量。
- 定义输入、输出逻辑状态的含意，并将电路状态顺序编号。
- 按照要求列出电路的状态转换表或画出状态转换图。

这样，就把给定的逻辑问题抽象到一个时序逻辑函数了。

第二步:状态化简，这一步能够有效降低电路实现的复杂度。电路的状态数越少，存储电路也就越简单。状态化简的目的在于将等价状态 合并，从而得到最简的状态转换图。

 第三步:状态分配，就是对每个状态分配一个寄存器值。

第四步:选定触发器的类型并求出状态方程、驱动方程和输出方程。

第五步:按照方程得出逻辑图。

##### 3. 状态机的三种风格描述

FSM 状态机可以有多种风格描述，包括一段式描述，两段式描述和三段式描述。每种都有其优缺点，都 有适用的应用场合，但从芯片设计实践角度，最佳的描述方法是三段式描述。

下面的例子包含三个输入:in1、in2、in3，三个输出:out1、out2、out3。而状态转移顺序以及输出结果 如图 1-34 所示。本书将提供三种不同描述风格的例子，方便对比各自差异。

![image-20200323155149031](https://tva1.sinaimg.cn/large/00831rSTgy1gd3wwafzjmj30eb08xwgl.jpg)

1. **一段式描述**

一段式的风格是将状态译码、状态寄存和输出放在一个 always 块，即在一个 always 块中完成整个状态机的设计。这种描述方式优点是代码简单，缺点是代码维护与修改非常困难。

上面的状态转移图(图 1-34 状态转移图)，采用一段式有限状态机进行描述的 Verilog 代码如下:

```verilog
module FSM_style1(
input clk,
input rst_n,
input in1,
input in2,
input in3,
output reg out1,
output reg out2,
output reg out3
);
reg[3:0] state;
parameter state0 = 4'b0001,state1 = 4'b0010,
          state2 = 4'b0100,state3 = 4'b1000;
always @(posedge clk or negedge rst_n)
  if(!rst_n)
    state <= state0;
  else
  case(state)
    state0: begin
      if(in1) begin
        state<=state1;
        out1 <= 1;
      end
      else state<=state0;
    end
    state1: begin
      state<=state2;
      out2 <= 1;
    end
    state2: begin
      if(in2) begin
        state<=state3; out3 <= 1;
      end
      else state<=state0;
    end
    state3: begin
      if(in3) state<=state0;
      else begin
        state<=state0;out3 <= 1;
      end
    end
    default:
    begin
      state <= state0; out1 =0;out2=0;out3=0;
    end
  endcase

endmodule

```

2. **二段式与三段式描述**

两段式的代码风格由两个always 块构成，其中一个always块用于完成状态寄存(时序逻辑)，另一个always块即把状态译码和状态输出两个组合逻辑放在一起。

三段式的代码风格则将状态译码、状态寄存和输出分别放在三个 always 块，相对两段式逻辑层次更清 晰。下面的代码是图 1-34 状态转移图的三段式代码描述。

```verilog
module FSM_style3(
input clk,
input rst_n,
input in1,
input in2,
input in3,
output reg out1,
output reg out2,
output reg out3
);
reg[3:0] state,next_state;
parameter state0 = 4'b0001, state1 = 4'b0010,
          state2 = 4'b0100, state3 = 4'b1000;
//第一段 组合电路用于状态译码
always @(state or in1 or in2 or in3) 
  case(state)
    state0:if(in1) //根据条件，选择目标跳转状态
        next_state<=state1;
      else
        next_state <= state0;
    state1: next_state<=state2;
    state2: if(in2)
        next_state<=state3;
      else
        next_state <= state0;
    state3: if(in3)
        next_state<=state0;
      else
        next_state <= state3;
    default:
      next_state <= state0;
  endcase

//第二段：更新状态寄存器
always @(posedge clk or posedge rst_n)
  if(!rst_n)
    state <= state0;
  else
    state <= next_state;
//第三段：利用状态寄存器输出控制结果
always @(state)
begin
  //首先产生默认值，后续再改写，防止锁存器产生
  {out1,out2,out3}=3'b000;
  case(state)
    state1: {out1,out2,out3}=3'b100;
    state2: {out1,out2,out3}=3'b110;
    state3: {out1,out2,out3}=3'b111;
  endcase
end
endmodule

```

3. **FSM编码风格小结**

对于有限状态机的描述风格，业内公认的结论如下:

1. 一段式的状态机从功能上说没有错误，但其可读性差，这种风格的状态机不能被综合工具很好的识别，同时很难对其进行优化。
2. 两段式的状态机的写法将组合逻辑和时序逻辑分开，具有较好的可读性，更有利于综合工具对状态机的优化。
3. 三段式的状态机的写法除了具有两段式的优点，还可以对输出进行寄存，可以有效地滤除毛刺，提高工作效率。

所以推荐读者在开始采用 HDL 编写 Verilog 代码时，就采用标准的三段式描述。

4. **有限状态机的判别标准**

一个好的状态机的标准很多,但最重要的几点如下：

第一，状态机必须是所有状态都能遍历，任何一个状态都能最终转移到缺省安全状态，状态机不会进入死循环，或者不会进入非预知的状态。这里面包含两层含义:(1)要求 FSM 要完备，即使状态机由于受到 干扰而进入异常状态，也能很快恢复到正常状态;(2)状态机不会因为代码编写风格问题，导致跳转异常或 者出现临时状态(毛刺)。所以对于复杂的状态机，通常会设计一个看门狗电路(计数器定时清零电路)，当 状态机在规定的时间内没有跳转，就会复位到规定合法状态。

第二，状态机的设计要清晰易懂、易维护。每一个状态都有明确的转入状态和转出状态，而且转入转出条件均是可达的。对于复杂的状态描述，最好采用一个主状态机和若干个从状态机分开描述。

第三，状态机的设计要满足设计的面积和速度的要求。

此外，建议对状态机的各个状态利用 localparam 或 parameter，将状态定义为有意义的符号名。前面的程 序例子均采用类似规则命名。

##### 4. 有限状态机举例

下面的例子是一个标准的 Moore 型有限状态机描述方法，也是逻辑设计中推荐的设计风格，属于三段式 描述:

```verilog
module FSM(
  input clk,
  input reset,
  input start,
  input step2,
  input step3,
  output reg[2:0] fsm_out
);
  localparam state0=2'b00; 
  localparam state1=2'b01; 
  localparam state2=2'b11; 
  localparam state3=2'b10; 
  //标准三段式编码，每个周期更新当前状态 
  reg[1:0] state;
  reg[1:0] next_state;
	always@(posedge clk or negedge reset)
	  if(!reset)
			state <= state0;
   else
 			state <= next_state;
 //根据当前状态和输入，确认下一个周期的状态 
  always@(state or start or step2 or step3)
    begin
   		case(state)
        state0: begin
        	if(start) //如果接到start启动信号，就跳转到state1
          	 next_state <=state1; 
          else
          	next_state <=state0; 
        end
        state1: begin //无条件跳转到state1 
            next_state <= state2;
          end
        state2: begin
          if(step2) //如果输入step2有效，则跳转到state3
            next_state <=state3; 
          else
            next_state <=state0; 
          end
        state3: begin 
          if(step3)
            next_state <=state0; 
          else
            next_state <=state3; 
          end
        default: next_state <=state0;//缺省状态 
      endcase
  always @(state) //该进程定义FSM的输出 
    case(state)
      state0: fsm_out=3'b001;
      state1: fsm_out=3'b010;
      state2: fsm_out=3'b100;
      state3: fsm_out=3'b111;
      default:fsm_out=3'b001; //default语句，避免锁存器的产生
    endcase 
endmodule
```

1. **代码详解**

上述 Verilog 代码表达的是这样一个电路:(1)电路初始状态 state0 下输出 3'b001，在接收到启动信号 start 后，则进入 state1 状态,否则继续维持在本状态;(2)在 state1 状态下，直接输出一个周期的 3'b010，并立刻进入 state2 状态;(2)在 state2 状态下，输出 3'b100，并等待 step2 信号有效，如 果 step2 信号有效，则进入 state3 状态，否则继续维持在本状态;(3)在 state3 状态下，输出 3'b111， 并等待 step3 信号，如果 step3 信号有效，则回归到 state0，即初始状态，否则继续维持在本状态。

状态转移图如图 1-35 所示:

![image-20200323164600390](https://tva1.sinaimg.cn/large/00831rSTgy1gd3ygo62pfj30hd0b3jvn.jpg)

上述 state0 等的表述有点抽象，所以如果按照这种说法，读者可以更清楚的理解:这个状态机是描述 IT码农的生活，state0 表示在家休息，而早上七点的起床闹钟就表示 start 信号，此时的状态输出值表示工作输 出强度。通常 IT 码农休息时的战斗力是较差的，所以输出为 3'b001。但闹钟响了，就必须立刻起床买早 餐挤地铁，所以跳转到 state1，即早餐状态。但早餐状态只能持续小小的一段时间，所以无条件的跳转到早 上工作状态，即 state2。通常早上的输出状态是“半血输出”，即输出为:3'b100。而上午不停的例会、评 审以及面谈，一直需要等待老板允许午饭，即 step2 信号。当 step2 信号来到后，经过中午或 tea break，就 需要进入下午的工作状态了，即 state3。下午的工作通常都是满血输出，即输出为 3'b111，而可以下班回 家的信号 step3 常常不能准时到来，有时候甚至需要通宵加班到象科比一样见到早上 5 点钟的太阳，然后回 家休息。

同样，读者可以将这个状态具体化到某个流程，例如通信接收信号的处理或者发射信号序列处理。

2. **有限状态机的状态添加**

上述例子仅包含 4 个状态，在实际应用中，状态数目可能远远超出 4 个，也经常会发生状态修改或添加的情形。所以下面给出一个如何由上面 4 个状态调整为 5 个状态的例子。该例子的改动核心包括:(1)在组合 always 块中添加一个状态，并加入如何跳转到该状态以及如何跳转出该状态即可。(2)调整状态寄存器 的位宽，并增加状态数量。

支持 5 个状态的 FSM 状态机描述代码如下:

```verilog
module FSM(
  input clk,
  input reset,
  input start,
  input step2,
  input step3,
  input step4,
  output reg[2:0] fsm_out
);
  localparam fsm_width=3;
  localparam state0=3'b000; 
  localparam state1=3'b001; 
  localparam state2=3'b011; 
  localparam state3=3'b010;
  localparam state4=3'b100;
  //标准三段式编码，每个周期更新当前状态 
  reg[fsm_width-1:0] state;
  reg[fsm_width-1:0] next_state;
	always@(posedge clk or negedge reset)
	  if(!reset)
			state <= state0;
   else
 			state <= next_state;
 //根据当前状态和输入，确认下一个周期的状态 
  always@(*)
    begin
   		case(state)
        state0: begin
        	if(start) //如果接到start启动信号，就跳转到state1
//省略部分与前面完全相同
          end
        state3: begin 
          if(step3)
            next_state <=state4; 
          else
            next_state <=state3; 
          end
        state4: begin 
          if(step4)
            next_state <=state0; 
          else
            next_state <=state4; 
          end
        default: next_state <=state0;//缺省状态 
      endcase
  always @(state) //该进程定义FSM的输出 
    case(state)
//省略部分与前面完全相同
      state4: fsm_out=3'b011;
      default:fsm_out=3'b001; //default语句，避免锁存器的产生
    endcase 

endmodule

```

##### 5.  JTAG标准的状态机实现

提起 JTAG，大家的第一印象就是芯片的调试接口。JTAG 作为一项国际标准测试协议(IEEE 1149.1 兼 容)，主要用于芯片内部测试和调试。目前的主流芯片均支持 JTAG 协议,如 DSP、FPGA、ARM、部分单片 机等。标准的 JTAG 接口是 20Pin，但 JATG 实际使用的只有 4 根信号线，再配合电源、地;目前常见的各 种接口形式(20pin、14pin、10pin)如下:

![image-20200323165456647](https://tva1.sinaimg.cn/large/00831rSTgy1gd3ypz6tzpj30ky076tae.jpg)

芯片开发所用的 JTAG 口调试实物如图 1-36 所示:

![image-20200323165524416](https://tva1.sinaimg.cn/large/00831rSTgy1gd3yqg63eyj30lo0afk1m.jpg)

1. **JTAG 标准介绍**

JTAG 的基本原理是在器件内部定义一个 TAP(Test Access Port 测试访问口)通过专用的 JTAG 测试工具对内部节点进行测试。JTAG 测试允许多个器件通过 JTAG 接口串联在一起，形成一个 JTAG 链，能实现对 各个器件分别测试。JTAG 引脚的定义如下:

| 信号名称 | 信号说明                                                     |
| -------- | ------------------------------------------------------------ |
| TCK      | TCK在IEEE1149.1标准里是强制要求的。TCK为TAP的操作提供了一个独立的、基本的时钟信号，TAP的所有操作都是通过这个时钟信号来驱动的 |
| TMS      | TMS在IEEE1149.1标准里是强制要求的。TMS信号在TCK的上升沿有效，用来控制TAP状态机的转换。通过TMS信号，可以控制TAP在不同的状态间相互转换 |
| TDI      | TDI在IEEE1149.1标准里是强制要求的。TDI是数据输入的接口,所有要输入到特定寄存器的数据都是通过TDI接口一位一位串行输入的(由TCK驱动) |
| TDO      | TDO在IEEE1149.1标准里是强制要求的。TDO是数据输出的接口,所有要从特定的寄存器中输出的数据都是通过TDO接口一位一位串行输出的(由TCK驱动) |
| TRST     | 可选项，TRST可以用来对TAP控制器进行复位(初始化）。因为通过TMS也可以对TAP控制器进行复位(初始化），所以有四线JTAG与五线JTAG之分 |
| RTCK     | 可选项,由目标端反馈给仿真器的时钟信号,用来同步TCK信号的产生,不使用时直接接地 |
| nSRST    | 可选项，与目标板上的系统复位信号相连，可以直接对目标系统复位。同时可以检测目标系统的复位情况，为了防止误触发应在目标端上加上适当的上拉电阻 |

JTAG 标准的信号时序图如图 1-37 所示:

![image-20200323170346058](https://tva1.sinaimg.cn/large/00831rSTgy1gd3yz5ia65j30l9078wfn.jpg)

通过 JTAG 连接，可以完成如下的功能:

1.  对所有串接在一起的 IC 进行引脚连接性测试，确认 PCB 是否焊接正常; 
2. 对 CPU、DSP、FPGA 等进行调试 debug;
3. 通过 JTAG 对 FPGA 进行编程。

进行引脚链接测试的 JTAG 用法如图 1-38 所示，各个芯片引脚联通状态可以依次串接通信到 PC 的 TDO引脚中。

![image-20200323172504072](https://tva1.sinaimg.cn/large/00831rSTgy1gd3zlbelvkj30e0072zla.jpg)

按照菊花链方式串接调试的 JTAG 用法如图 1-39 所示，多个串接在一起的 CPU 和 FPGA 都能够在一起进行调试和测试:

![image-20200323172535024](https://tva1.sinaimg.cn/large/00831rSTgy1gd3zluakg2j30hi08d3zn.jpg)

上图已经给出 JTAG 的调试原理:(1)所有的调试芯片的 IR 寄存器串接在一起，然后进行串行移位，最后所有数据都进入到 JTAG 口的 TDO 中;PC 通过对 TDO 数据串并转换，获得每个 CPU/DSP 或 FPGA 的 内部寄存器状态。(2)PC 将需要写入 CPU/DSP 或 FPGA 的数据通过并串转换放置到 TDI 总线上，然后通 过状态移位到规定的 CPU 寄存器上，然后通过 TMS 指定生效时刻。(3)TCK 就是 TDI 和 TDS 的移位时 钟，而 TMS 则是控制指令。

所以 JTAG 通过一个标准状态机就能将 CPU/DSP/FPGA 的内部状态明确，也能改变内部寄存器内容，这也是一种状态控制原理。在 1990 年前，JTAG 的状态机基本是各个厂商自行定义，但后来出现的 IEEE1149.1 标准对状态转移过程进行了标准化。在某种意义上讲，JTAG 的状态机实现过程是最佳的 FSM 学习对象。

2. **JTAG状态机的设计**

JTAG 内部的状态转移图如图 1-40 所示，其中的状态值读者可以自行定义，但推荐采用 one-hot 或者格雷码进行编码:

![image-20200323173023217](https://tva1.sinaimg.cn/large/00831rSTgy1gd3zqu6dz4j30jf0fzjxk.jpg)

下面的代码是实现 JTAG 功能的 FSM 部分，该代码已经应用到多款 ASIC 芯片中，具有较高的研究价值。

```verilog
//TAP FSM implementation
module tap_FSM #(parameter sync_mode = 1)(
input   tck,
input   trst_n,
input   tms,
input   tdi,
output  byp_out,
output updateIR,reset_n,

output reg  clockDR, updateDR, clockIR, tdo_en, shiftDR,shiftIR,
output  selectIR, sync_capture_en, sync_update_dr, flag,
output [15:0] tap_state
);
//Inter signal declaration
reg [15:0] state;
reg [15:0] next_s;
reg scan_out_a, scan_out_s, updateIR_a;

localparam TEST_LOGIC_RESET = 16'h0001, RUN_TEST_IDLE = 16'h0002, SELECT_DR_SCAN = 16'H0004,
  CAPTURE_DR= 16'h0008, SHIFT_DR = 16'h0010, EXIT1_DR = 16'h0020,PAUSE_DR = 16'h0040,
  EXIT2_DR  = 16'h0080, UPDATE_DR= 16'h0100, SELECT_IR_SCAN = 16'h0200,
  CAPTURE_IR= 16'h0400, SHIFT_IR = 16'h0800, EXIT1_IR = 16'h1000,
  PAUSE_IR  = 16'h2000, EXIT2_IR = 16'h4000, UPDATE_IR= 16'h8000;

assign flag = state[10] || state[11];
wire updateIR_s = state == UPDATE_IR;
assign updateIR   = sync_mode ? updateIR_s : updateIR_a;
assign tap_state= state;

always @(posedge tck or negedge trst_n)
  if ( !trst_n )
    state<=TEST_LOGIC_RESET;
  else
    state<=next_s;

always @(*)
  case(state)
    TEST_LOGIC_RESET: if(tms)
                        next_s=TEST_LOGIC_RESET;
                      else
                        next_s=RUN_TEST_IDLE;
    RUN_TEST_IDLE: if( tms )
                     next_s=SELECT_DR_SCAN;
                   else
                     next_s=RUN_TEST_IDLE;
    SELECT_DR_SCAN: if(tms)
                      next_s=SELECT_IR_SCAN;
                    else
                      next_s=CAPTURE_DR;
    CAPTURE_DR: if(tms)
                  next_s=EXIT1_DR;
                else
                  next_s=SHIFT_DR;
    SHIFT_DR: if(tms)
                next_s=EXIT1_DR;
              else
                next_s=SHIFT_DR;
    EXIT1_DR: if(tms)
                next_s=UPDATE_DR;
              else
                next_s=PAUSE_DR;
    PAUSE_DR: if(tms)
                next_s=EXIT2_DR;
              else
                next_s=PAUSE_DR;
    EXIT2_DR: if(tms)
                next_s=UPDATE_DR;
              else
                next_s=SHIFT_DR;
    UPDATE_DR: if(tms)
                next_s=SELECT_DR_SCAN;
              else
                next_s=RUN_TEST_IDLE;
    SELECT_IR_SCAN:if(tms)
                     next_s=TEST_LOGIC_RESET;
                   else
                     next_s=CAPTURE_IR;
    CAPTURE_IR: if(tms)
                  next_s=EXIT1_IR;
                else
                  next_s=SHIFT_IR;
    SHIFT_IR: if(tms)
                next_s=EXIT1_IR;
              else
                next_s=SHIFT_IR;
    EXIT1_IR: if(tms)
                next_s=UPDATE_IR;
              else
                next_s=PAUSE_IR;
    PAUSE_IR: if(tms)
                next_s=EXIT2_IR;
              else
                next_s=PAUSE_IR;
    EXIT2_IR: if(tms)
                next_s=UPDATE_IR;
              else
                next_s=SHIFT_IR;
    UPDATE_IR: if(tms)
                next_s=SELECT_DR_SCAN;
              else
                next_s=RUN_TEST_IDLE;
  endcase

//FSM outputs
reg rst_n;

//reg  clockDR, updateDR, clockIR, tdo_en, rst_n, shiftDR, shiftIR;
//ClockDR/ClockIR - posedge occurs at the posedge of tck
//updateDR/updateIR - posedge occurs at the negedge of tck
always @( tck or state )begin
    if ( !tck && ( state == CAPTURE_DR || state == SHIFT_DR ))
      clockDR = 0;
    else
      clockDR = 1;

    if ( !tck && ( state == UPDATE_DR ))
      updateDR = 1;
    else
      updateDR = 0;

    if ( !tck && ( state == CAPTURE_IR || state == SHIFT_IR ))
      clockIR = 0;
    else
      clockIR = 1;

    if ( !tck && ( state == UPDATE_IR ))
      updateIR_a = 1;
    else
      updateIR_a = 0;
  end

always  @( negedge tck )
  if ( state == SHIFT_IR || state == SHIFT_DR )
    tdo_en <= 1;
  else
    tdo_en <= 0;

always  @( negedge tck ) 
  if ( state == TEST_LOGIC_RESET )
    rst_n <= 0;
  else
    rst_n <= 1;

always @(negedge tck or negedge trst_n)
  if ( !trst_n )
    shiftDR <= 0;
  else if ( state == SHIFT_DR )
    shiftDR <= 1;
  else
    shiftDR <= 0;

always @(negedge tck or negedge trst_n)
  if ( !trst_n )
    shiftIR <= 0;
  else if ( state == SHIFT_IR )
    shiftIR <= 1;
  else
    shiftIR <= 0;

assign reset_n = rst_n & trst_n;
assign selectIR = state == SHIFT_IR;
assign sync_capture_en = ~(shiftDR | (state == CAPTURE_DR) | (state == SHIFT_DR));
assign sync_update_dr = state == UPDATE_DR;

always @( posedge clockDR )
  scan_out_a <= shiftDR & tdi & ~(state == CAPTURE_DR);

wire nxt_st_3 = (state == SELECT_DR_SCAN) & ~tms;
wire nxt_st_4 = ((state == CAPTURE_DR) & ~tms) || ( state == SHIFT_DR & ~tms);

reg sel;
always @(posedge tck or negedge trst_n)
  if(!trst_n )
    sel <= 0;
  else
    sel <= ~(nxt_st_3 | nxt_st_4);

wire scan_out = sel ? scan_out_s : shiftDR & tdi;
always @(posedge tck )
  scan_out_s <= scan_out & ~(state == CAPTURE_DR);

assign byp_out = sync_mode ? scan_out_s : scan_out_a;

endmodule

```

<span style='color:red;;'>对于 JTAG 实现</span>，还有一种非常简洁的代码描述，同样是三段式描述风格，但最重要的组合电路部分， 通过手工推导，直接将 always 描述的组合电路化简为最小逻辑实现代码。

```verilog
 module jtag_fsm1 (
 input   clk,    // Internal clock
 input   tdo_mux,// TDO before the negative edge flop
 input   bypass, // JTAG instruction=BYPASS
 input   tck,    // clock input
 input   trst_n, // optional async reset active low
 input   tms,    // Test Mode Select
 input   tdi,    // Test Data In

 output reg tdo, // Test Data Out
 output reg tdo_enb,//Test Data Out tristate enable

 output  tdi_r1,   // TDI flopped on TCK.
 output  tck_rise, // tck rate clock enable
 output  captureDR,// JTAG state=CAPTURE_DR
 output  shiftDR,  // JTAG state=SHIFT_DR
 output  updateDR, // JTAG state=UPDATE_DR
 output  captureIR,// JTAG state=CAPTURE_IR
 output  shiftIR,  // JTAG state=SHIFT_IR
 output  updateIR
 );
 reg     tck_r1,tck_r2,tck_r3;
 reg     tdi_f_local; //  local version
 wire    tdo_enb_nxt; //  D input to TDO_ENB flop
 wire    tdo_nxt; //  D input to TDO flop
 wire    itck_rise;
 wire    tck_fall;

 reg     [3:0] state; //  current state
 wire    a,b,c,d,a_nxt,b_nxt,c_nxt,d_nxt;
 assign {d,c,b,a} = state[3:0]; //a:0,b:1,c:2,d:3

 assign a_nxt=(~tms& ~c & a)|(tms &~b)|(tms & ~a)|(tms& d & c);
 assign b_nxt=(~tms& b & ~a)|(~tms&~c)|(~tms & ~d & b)|(~tms & ~d & ~a)|(tms & c & ~b)|(tms & d & c & a);
 assign c_nxt=(c & ~b)|(c&a)|(tms &~b);
 assign d_nxt=(d & ~c)|(d&b)|(~tms&c&~b)|(~d & c & ~b &~a);

 assign tdo_enb_nxt = state == 4'b0010 | state == 4'b1010 ? 1'b1: 1'b0;
 assign captureIR = state == 4'b1110 ? 1'b1:1'b0;
 assign shiftIR = state == 4'b1010 ? 1'b1:1'b0;
 assign updateIR = state == 4'b1101 ? 1'b1:1'b0;
 assign captureDR = state == 4'b0110 ? 1'b1:1'b0;
 assign shiftDR = state == 4'b0010 ? 1'b1:1'b0;
 assign updateDR = state == 4'b0101 ? 1'b1:1'b0;
 assign tdo_nxt = (bypass ==1'b1&state == 4'b0010)?tdi_f_local:tdo_mux;
 assign tdi_r1 = tdi_f_local;

 always @(posedge clk) begin:rtck_proc
   tck_r3 <= tck_r2;
   tck_r2 <= tck_r1;  //synchronizers for edge detection
   tck_r1 <= tck;
 end
 assign tck_rise = itck_rise;
 assign itck_rise = tck_r2  &  ~tck_r3;
 assign tck_fall = ~tck_r2  &  tck_r3;

 always @(posedge clk)
   if (trst_n == 1'b0)
      state <= 4'b1111;
   else if (itck_rise == 1'b1)begin
      state <= {d_nxt, c_nxt, b_nxt, a_nxt};
   end

 always @(posedge clk)
    if (trst_n == 1'b0)
       tdi_f_local <= 1'b0;
    else if (itck_rise == 1'b1 ) begin
       tdi_f_local <= tdi;
    end

 always @(posedge clk)
   if (trst_n == 1'b0)begin
       tdo <= 1'b0;
       tdo_enb <= 1'b0;
    end
    else if (tck_fall == 1'b1 ) begin
       tdo <= tdo_nxt;
       tdo_enb <= tdo_enb_nxt;
    end

 endmodule // module vjtag
```

JTAG 接口除了标准 4 信号引脚外，TI 还定义了一种叫 SBW-JTAG 的接口，<span style='color:red;;'>仅使用两根引脚(SBWTCK， SBWTDIO)即可实现 JTAG 功能，通常用于管脚受限的芯片上。</span>ARM 的 Cortex-M 系列 CPU，均包含 SW- JTAG 定义标准。读者可以参考相关资料，编写对应的 JTAG 状态机代码。

##### 6. 有限状态机小结

学习有限状态机的关键是理解有限状态机的运行原理，知道通过 FSM 能够将一个复杂的逻辑流程简化为有限的几个状态，所有的输出响应都是状态对应的输出。FSM 的编写风格应当采用三段式编码，并尽可能的采用 Moore 型状态机。

#### 1.3.8 第六个Verilog程序：写testbench

前面已经给出了很多的 Verilog 电路实现例子，但如何证明上述代码实现正确?因此，本节将引入测试验 证的概念。所谓测试验证，就检查编写的 RTL 是否真正完全遵守最初的设计规范(Design Specification)。 更进一步就是生成最终网表中的任何一个中间步骤是否完全满足了最初的设计规范和需求列表(feature list)。 所以，测试验证的目的是证明实现过程的正确性，而不是去证明这个 RTL 代码多么美妙或者代码多么的有 技巧。如果最初的需求设定就错误了，验证并不能纠正这个错误，而只是尽早的通过结果显示这个设定展现 的后果。

因此，验证过程是一个多次重复的过程，是一个不断向期望结果靠近的收敛过程。目前的验证主要包括功能验证和时序验证两种方法。功能验证主要是验证所设计的 RTL 代码是否符合系统规范，主要方法包括功能仿真和形式验证。而时序验证则验证综合和 Layout 后，含有延时信息的网表时序是否满足要求。通常功能仿真在 HDL 代码设计前期是最重要的，就是我们俗称的 TestBench 和各种 Case。

##### 1. TestBench

在 Verilog 仿真验证中，设计模块(DUT)与测试模块之间的关系图如图 1-41 所示:

![image-20200324153637538](https://tva1.sinaimg.cn/large/00831rSTgy1gd522ufscaj30i00d1jum.jpg)

上图中就是一个标准的测试验证框架。其中包含了测试驱动(driver)、理想参考模型(Golden Reference

Model)、测试结果检测(Monitor)和记分牌(ScoreBoard)。

其中 Driver 将各种的激励施加给 Verilog 实现模型(DUT)和理想模型，两者将同时输出对应的激励响

应。由于 HDL 获取激励响应的结果通常不太直观，所以大型设计通常都会设计一个专门的 monitor，用来监 测 DUT 的输出。此外，scoreboard，它专门的比较期望值与 monitor 监测到的 DUT 的输出，并对输出结果 做一定的处理，方便统计和得到更高层次的结果。

<span style='color:red;;'>在读者开始接触编写 TestBench 时，一定要明确:测试的目的是为了验证所设计的 HDL 代码是否真正与 设计规范一致，而不是以 RTL 代码可以运行为目的。所以写 TestBench 一定要满足完备性和充分性，所设 计的激励能够充分覆盖需求规范，并确定各种未知情况的结果。因此，Testbench 追求产生激励的便利性和 可验证性，而不关心是否能够转化为真实电路，所以 Testbench 往往采用有可重用、具备丰富组合能力的代 码，并以提高覆盖率为核心理念。</span>

##### 2. 计数器的测试例子

下面的例子是第三个例子中计数器的测试例子。

```verilog
`timescale 1ns/1ns
module count_tb;
localparam DELY=100;
reg clk,reset;  //测试输入信号,定义为reg 型
wire[3:0] cnt;  //测试输出信号,定义为wire型

always#(DELY/2) clk = ~clk; //产生时钟波形     
initial
begin                 //激励信号定义
  clk =0; reset=0;
  #DELY   reset=1;
  #DELY   reset=0;
  #(DELY*100) $finish(2);
end
//定义结果显示格式
initial $monitor($time," clk=%d reset=%d cnt=%d",clk, reset,cnt);

//调用测试对象
count#(.N(4))U_cnt(
 .clk   (clk  )
,.clear (reset)
,.cnt_Q (cnt  )
);    

endmodule
```

其计数器代码为：

```verilog
module count#(parameter N=8)(
input clk,
input clear,
output[N-1:0] cnt_Q
);
reg[N-1:0] cnt;
assign cnt_Q = cnt;

always@(posedge clk)
  if(clear)
    cnt <= 'h0;      //同步清 0，高电平有效
  else
    cnt <= cnt+1'b1; //减法计数

endmodule
```

程序的部分测试结果大致为:

```
clk=0 reset=0 cnt=1
clk=1 reset=0 cnt=2
clk=0 reset=0 cnt=2
clk=1 reset=0 cnt=3
clk=0 reset=0 cnt=3
```

上述测试程序有几个语法点：

1. 模块 module 的例化调用以及对应的参数设置。
2. initial 语句含义
3. 时钟波形的产生
4. 激励信号的简单产生方法
5.   测试结果的查看方法



- 单独描述时钟
  ![image-20200320152306341](https://tva1.sinaimg.cn/large/00831rSTgy1gd0f7h00z8j30pe04i3zf.jpg)

- 结构化描述
  ![image-20200320152506358](https://tva1.sinaimg.cn/large/00831rSTgy1gd0f9kdqy2j30r204ago0.jpg)
  ![image-20200320152953751](https://tva1.sinaimg.cn/large/00831rSTgy1gd0fejahnmj30q406xwge.jpg)

- 对文件的引用

  ![image-20200320162653654](https://tva1.sinaimg.cn/large/00831rSTgy1gd0h1ur8xgj30hv079t9t.jpg)

  ![image-20200320163614635](https://tva1.sinaimg.cn/large/00831rSTgy1gd0hbl2q1ij30na05wgmv.jpg)

  

##### 7. testebench中的任务与fork-join

![image-20200321105754457](https://tva1.sinaimg.cn/large/00831rSTgy1gd1d5wbav6j30qg0gu449.jpg)

##### 8.在testbench中dump波形

#### 9. [SPI总线](https://www.cnblogs.com/liujinggang/p/9609739.html)

##### 3. 时钟极性和时钟相位

![image-20200321124133066](https://tva1.sinaimg.cn/large/00831rSTgy1gd1g5pzh9rj30qe0dmjw2.jpg)![1426240-20180908165549249-409316412](https://tva1.sinaimg.cn/large/00831rSTgy1gd1mo07qwaj30lw0ahjza.jpg)

![1426240-20180908165913998-1219288597](https://images2018.cnblogs.com/blog/1426240/201809/1426240-20180908165913998-1219288597.png)

```verilog
 always @(posedge sysclk or negedge rst_x)
   if(!rst_x)
     state <= IDLE;
   else if (enable == 1'b1)
     state <= next_state;
   else
     state <= IDLE;
 
 always @(state or tx_data_ready or i_sck or d_sck)
 begin
   clear_tx_data_ready = 1'b0;
   shift_enable = 1'b0;
   tx_shift_reg_load = 1'b0;
   i_ss = 1'b1;
   sck_enable = 1'b0;
   load_rx_data_reg = 1'b0;
   next_state = IDLE;
   case (state)
     IDLE :if (tx_data_ready == 1'b1 & i_sck == 1'b1 & d_sck == 1'b0)
       begin
         clear_tx_data_ready = 1'b1;
         next_state = LOAD_SHIFT_REG;
       end
       else
         next_state = IDLE;
     LOAD_SHIFT_REG : begin
       tx_shift_reg_load = 1'b1;
       if (d_sck == 1'b0)begin
         i_ss = 1'b0;
         if (i_sck == 1'b1)
           next_state = D7;
         else
           next_state = LOAD_SHIFT_REG;
       end
       else
         next_state = LOAD_SHIFT_REG;
     end
     D7 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D6;
       end
       else
         next_state = D7;
     end
     D6 : begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D5;
       end
       else
           next_state = D6;
     end
     D5 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D4;
       end
       else
         next_state = D5;
     end
     D4 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D3;
       end
       else
         next_state = D4;
     end
     D3 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D2;
       end
       else
         next_state = D3;
     end
     D2 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D1;
       end
       else
         next_state = D2;
     end
     D1 :begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = D0;
       end
       else
         next_state = D1;
     end
     D0 : begin
       i_ss = 1'b0;
       sck_enable = 1'b1;
       if (i_sck == 1'b1 & d_sck == 1'b0)begin
         shift_enable = 1'b1;
         next_state = FINAL_CYCLE;
       end
       else
         next_state = D0;
     end
     FINAL_CYCLE :if (d_sck == 1'b1)begin
         i_ss = 1'b0;
         next_state = FINAL_CYCLE;
       end
       else begin
         load_rx_data_reg = 1'b1;
         i_ss = 1'b1;
         if (tx_data_ready == 1'b1 & i_sck == 1'b1)begin
             clear_tx_data_ready = 1'b1;
             next_state = LOAD_SHIFT_REG;
         end
         else
           next_state = IDLE;
       end
   endcase
 end
 

localparam[3:0]  IDLE=0,D7=1,D6=2,D5=3,D4=4,D3=5,D2=6,D1=7,D0=8,FINAL_CYCLE=9,LOAD_SHIFT_REG=10;

```

#### 10. 异步uart

#### 11. 一些有用的verilog程序

##### 2. 双向端口例子

当某个模块端口为 inout类型时,该端口同时具备输入和输出功能。通常在ASIC设计中,   只有在最顶层文件( IOPAD文件)中进行pin引脚设定时才会使用 inout类型;而对于FPGA,  则是直接体现在顶层文件中。使用双向端口的目的是减少引脚数量。    

   通常 inout需要通过三态门来实现,三态门的第三个状态就是高阻(Z)。当 inout端口不输出时,将三态门置高阻,这样信号就不会因为两端同时输出而出错了。要使用 inout类型数  据,可以用如下写法:

下面的代码则是一个完整的双向端口例子:

```verilog
module bidir_data#(parameter N=8)(
  input en,
  input clk,
  inout[N-1:0] bidir
);
reg[N-1:0] temp;
assign bidir= en ? temp : 8'bz;
always@(posedge clk)
  if(en) temp=bidir;
  else   temp=temp+1;
endmodule
```

编写测试模块时,对于 inout类型的端口,需要定义成wire类型变量,而其他输入端口都定义成reg类型,这两者是有区别的。

方法一:在激励模块中生成一个与测试信号对应的反相 inout端口,这等效于在两个模块之间采用 inout双向口互连。

```verilog
module test();
  wire bidir;
  reg data_reg;
  reg inout_ctr;
  initial begin
    .........
  end
  assign bidir = inout_ctr?data_reg:1'bZ;
endmodule
```

方法二:使用 Verilog特有的 force和 release语句。其中, force语句能够强迫任意信号赋予规定的值,也等效于将该信号短接为指定的值;而 release则可释放该信号,让其恢复原有的赋值。 force与 release是编写测试程序的一大利器,能够很快捷地实现某些验证意图。

```verilog
module test();
  wire bidir;
  reg clk, en;
  ......
  bidir_data DUT(en, clk, bidir);
  initial begin
    #xx
    force DUT.bidi =l'bx;
    #xx
    release DUT.bidir
  end
endmodule
```

##### 3. JTAG的双向端口

下面这个例子是ARM cortex-M1的JTAG包装程序，因为原始的M1代码没有提供标准的JTAG引脚，只提供了多个单向引脚。

```verilog
module CM1_JTAG(
  input         HCLK,  // AHB总线时钟
  input         rst_x, // 板级全局异步复位信号
  // JTAG inputs
  input         nTRST,       // JTAG reset
  input         TCK,         // JTAG clock
  input         TDI,         // JTAG data in
  inout         TMS,         // JTAG mode select
  // JTAG outputs
  output        TDO,         // JTAG data out
  output        nTDOEN,      // JTAG data enable
  output        RTCK,        // JTAG data out
  // Cortex-M1标准信号
  input         CM1_LOCKUP,
  input         CM1_HALTED,
  input         CM1_JTAGNSW,
  input         CM1_JTAGTOP,
  input         CM1_TDO,
  input         CM1_nTDOEN,
  input         CM1_SWDO,
  input         CM1_SWDOEN,
  input         CM1_SYSRESETREQ,
  output        CM1_SYSRESETn,
  input         CM1_DBGRESTARTED,
  output        CM1_EDBGRQ,
  output        CM1_DBGRESTART,
  output        CM1_DBGRESETn,
  output        CM1_nTRST,
  output        CM1_SWCLKTCK,
  output        CM1_SWDITMS,
  output        CM1_TDI      
);
reg rst_x_d0,rst_x_d1,rst_x_d2;
always @(posedge HCLK)
begin
  rst_x_d0<=rst_x;
  rst_x_d1<=rst_x_d0;
  rst_x_d2<=rst_x_d1;
end
reg CM1_SYSRESETREQ_d0,CM1_SYSRESETREQ_d1,CM1_SYSRESETREQ_d2;
always @(posedge HCLK or negedge CM1_DBGRESETn)
  if(!CM1_DBGRESETn) begin
    CM1_SYSRESETREQ_d0<=0;
    CM1_SYSRESETREQ_d1<=0;
    CM1_SYSRESETREQ_d2<=0;
  end else begin
    CM1_SYSRESETREQ_d0<=CM1_SYSRESETREQ;
    CM1_SYSRESETREQ_d1<=CM1_SYSRESETREQ_d0;
    CM1_SYSRESETREQ_d2<=CM1_SYSRESETREQ_d1;
  end
assign CM1_SYSRESETn = rst_x_d1 & rst_x_d2 
                     & ~CM1_SYSRESETREQ_d1 & ~CM1_SYSRESETREQ_d2;
assign CM1_DBGRESETn = rst_x_d1 & rst_x_d2 ;
assign CM1_EDBGRQ = 1'b0;
assign CM1_DBGRESTART = 1'b0;
/////////////////////////////////////
assign TDO       = (CM1_nTDOEN==1'b0) ? CM1_TDO : 1'bZ;
assign nTDOEN    = CM1_nTDOEN;
assign CM1_TDI   = TDI;
assign CM1_nTRST = nTRST;
assign TMS       = (CM1_SWDOEN==1'b1) ? CM1_SWDO : 1'bZ;
assign CM1_SWCLKTCK = TCK;
assign CM1_SWDITMS  = TMS ;// | CM1_JTAGNSW;

reg tck1,tck2,tck3;
// debug clock synchroniser
always @ (posedge HCLK or negedge nTRST)
  if (~nTRST) begin
      tck1    <= 1'b0;
      tck2    <= 1'b0;
      tck3    <= 1'b0;
  end else begin
      tck1    <= TCK;
      tck2    <= tck1;
      tck3    <= tck2;
  end
assign RTCK = tck3;
endmodule
```

##### 4. 通用1位寄存器的例子

通用1位移位寄存器的代码实现如下。

```verilog
module Shifter#(parameter N=8)(
input clk,
input reset, 
input left_in, 
input right_in,
input [1:0] mod, 
input [N-1:0] shift_in, 
output reg[N-1:0] shift_out
);
always @(posedge clk or posedge reset)
	if(reset)
		shift_out <= 'b0; //清除移位寄存器内部状态
	else case(mod)
		2'b00:shift_out<={shift_out[N-2:0], right_in};//左移
		2'b01:shift_out<={left_in, shift_out[N-1:1]}; //右移
		2'b10:shift_out<= shift_in;				// 并行输入
	endcase
endmodule
```

##### 5. 简单的中断控制器

```verilog
module simple_vic#(parameter INT_NUM=6,parameter INT_WIDTH=8)(
  input clk,
  input rst_x,
  input [INT_NUM-1:0] int_source,
  input [INT_NUM-1:0] int_mask,
  input [INT_NUM-1:0] int_clr,
  output vic_int,
  output reg [INT_NUM-1:0] int_reg,
  output reg [INT_NUM-1:0] int_mask_reg
);
reg [INT_NUM-1:0] int_source_d0;
reg [INT_NUM-1:0] int_source_d1;
wire[INT_NUM-1:0] int_source_pulse;

always @(posedge clk or negedge rst_x)
  if(!rst_x)begin
    int_source_d0<=0;
    int_source_d1<=0;
  end else begin
    int_source_d0<=int_source;
    int_source_d1<=int_source_d0;
  end

assign int_source_pulse=(int_source_d0&~int_source_d1);
wire vic_int_pulse  =|(int_source_pulse[INT_NUM-1:0] & int_mask[INT_NUM-1:0]);

always @(posedge clk or negedge rst_x)
  if(!rst_x)
    int_reg<='d0;
  else if(|int_source_pulse)
    int_reg<=int_reg|int_source_pulse;
  else if(|int_clr) 
  	int_reg<=int_reg&(~int_clr);

always @(posedge clk or negedge rst_x)
  if(!rst_x)
    int_mask_reg<='d0;
  else if(vic_int_pulse)
    int_mask_reg<=int_mask_reg | {int_source_pulse&int_mask};
  else if(|int_clr) 
  	int_mask_reg<=int_mask_reg&(~int_clr);

reg [INT_WIDTH-1:0] clk_cnt;
reg int_state;
always @(posedge clk or negedge rst_x)
	if(!rst_x)begin
		clk_cnt<=0;
		int_state<=1'b0;
	end	else if(int_state==0)	begin
		clk_cnt<=0;
		if(vic_int_pulse)
			int_state<=1'b1;
	end	else begin 
		if(~(|int_mask_reg))
			int_state<=1'b0;
		if(clk_cnt=={INT_WIDTH{1'b1}})
			int_state<=0;
		if(clk_cnt=={INT_WIDTH{1'b1}})
			clk_cnt<=0;
		else
			clk_cnt<=clk_cnt+1'b1;
	end
// int duration!	
assign vic_int=(INT_WIDTH==0) ? vic_int_pulse:int_state;
endmodule

```

##### 6. 4位转16位解码器

下面的这段代码在某国产CPU里面使用,读者可以思考一下为什么这样进行译码?

```verilog
module decoder_4_16(
	input enable,				   
	input [3:0] in,
	output [15:0] out 
);
//	wire enable;
//	wire [3:0] in;
//	wire [15:0] out;
	wire [3:0] high_d;
	wire [3:0] low_d;

	assign high_d[3]=( in[3])&( in[2])&enable;
	assign high_d[2]=( in[3])&(~in[2])&enable;
	assign high_d[1]=(~in[3])&( in[2])&enable;
	assign high_d[0]=(~in[3])&(~in[2])&enable;

	assign low_d[3]=( in[1])&( in[0]);	
	assign low_d[2]=( in[1])&(~in[0]);
	assign low_d[1]=(~in[1])&( in[0]);
	assign low_d[0]=(~in[1])&(~in[0]);

	assign out[15]=high_d[3]&low_d[3];
	assign out[14]=high_d[3]&low_d[2];
	assign out[13]=high_d[3]&low_d[1];
	assign out[12]=high_d[3]&low_d[0];
	assign out[11]=high_d[2]&low_d[3];
	assign out[10]=high_d[2]&low_d[2];
	assign out[ 9]=high_d[2]&low_d[1];
	assign out[ 8]=high_d[2]&low_d[0];	
	assign out[ 7]=high_d[1]&low_d[3];
	assign out[ 6]=high_d[1]&low_d[2];
	assign out[ 5]=high_d[1]&low_d[1];
	assign out[ 4]=high_d[1]&low_d[0];
	assign out[ 3]=high_d[0]&low_d[3];
	assign out[ 2]=high_d[0]&low_d[2];
	assign out[ 1]=high_d[0]&low_d[1];
	assign out[ 0]=high_d[0]&low_d[0];	
endmodule
```

> 利用权重的关系，对应相等就可以，很巧妙，如$in[3]\&in[2]$ 代表了12的基数

##### 7. FSM测试例子

在编写FSM的测试程序时，经常需要检查FSM的state，常规的做法是：

- 将state定义为输出端口，并引出，这种做法通常会浪费有限的硬件资源，而且多余。
- 采用打印语句，例如$display（）函数，在命令行中显示出来。若这样做，结果不是显示在波形里面，不直观。

一个比较好的做法是在testbench中直接引用该状态变量，并通过case 语句将state转换为自然语言表述。具体做法为：

1. 在testbench里定义一个reg型变量，用于存储ASCIl字符串。例如，以下定义了一个最大长度为4的字母。

   ```verilog
   reg [4*8-1: 0] current_state, next_state；
   ```

2. 利用模块变量的直接引用，并通过always语句将状态机的各个状态与自然语言一一对应起来：

   ```verilog
   always@(tb.uut.state or tb.uut.next_state)begin
     case(tb.uut.state)
       Zero:current_state = "Zero";
       one:current_state = "one";
       two:current_state = "two";
       three:current_state = "three";
       four:current_state = "four";
       default:current_state = "Error";
     endcase
     case(tb.uut.next_state)
       Zero:next_state = "Zero";
       one:next_state = "one";
       two:next_state = "two";
       three:next_state = "three";
       four:next_state = "four";
       default:next_state = "Error";
     endcase
   end
   ```

3. 在仿真工具的波形窗口里，把前面定义的reg型state indicator的radix改成ASCIl，就可以在波形窗口里面看到文本表示的state了。

##### 8. Generate 生成语句

假定读者需要设计一个100×100的阵列乘法器，一定会思考有没有用几条语句就搞定这种描述的简单方法？Verilog-2001针对这类情况添加了generate生成语句与循环语句，通过该语句能够简化代码编写，梳理设计思路。

通过generate循环语句，允许产生module和primitive的多个实例化，同时也可以产生多个variable、wire、task、function、assign、always、initial。在generate 语句中可以引入for、if-else和case 语句，根据条件不同产生不同的实例。为此，Verilog-2001增加了以下关键字：generate、endgenerate、genvar、localparam。语法为：

1. genvar 后面的for，其变量必须是genvar变量。
2. for里必须有begin，哪怕只有一句。
3. begin必须有名称，因为该语句块中的变量必须要设定一个名称。
4. generate例化的模块名称是 inst [j].unit
5. generate 语句可以看作是标准化为块的综合指令。

###### 1. for循环  

for循环以 begin开始,end结東, begin后边必须有一个唯一的标识符,例如下面的8位加法器例化过程

```verilog
generate  
  genvar i;
  for(i=0;1<=7;i=1+1)  
    begin: for_name  
      adder U_add(a[8*i+7:8*i],b[8*i+7:8*i], ci[i], sum_for[8*i+7:8*i], c0_or[i+1]);
    end
endgenerate                                       
```

在for循环里使用always语句的例子如下：

```verilog
generate
  genvar i;
  for(i = 0; i<11; i=i+1)begin:iq_data_gen
    always@(*)begin
      iq[i*2] = i_in[i];
      iq[i*2+1] = q_in[i];
    end
endgenerate
```

###### 2. If-else 例化

这个例子基于数据宽度例化不同的乘法器：

```verilog
generate
  if(IF_WIDTH < 10)
    begin:if_name
      multiplier_impl #(IF_WIDTH) U1(a,b,sum_if);
    end
  else
    begin:else_name
      multiplier_imp2 #(IF_WIDTH) u2(a,b,sum_if);
    end
endgenerate
```

###### 3. Generate-case

这个例子与上面的例子基本一致，只是分支情况更多一些：

```verilog
generate
  case(WIDTH)
    1:begin: case1_name
      adder #(WIDTH*8) x1(a, b, c, sum_case, c0_case);
    end
    2:begin: case2_name
      adder #(WIDTH*4) x2(a, b, c, sum_case, c0_case);
    end
    default:begin:d_case_name
      adder x3(a, b, c, sum_case, c0_case);
    end
  endcase
endgenerate
```

#### 12. Verilog 不同版本差异

图1-61的内容是Verilog各个发展阶段的关键字列表。通过这个列表，应当体会到为什么要添加这些额外的关键字，以及背后隐藏的出发点。例如Verilog-2001中的signed types就是针对Verilog-1995不能自然表达有符号数的运算而添加的；而Verilog-2005中的$clog2系统函数就是为了方便计算数据位宽，避免位浪费，同时又保持IP的灵活性而设计的；而SystemVerilog中的always_comb/always_ff就是针对always不能清楚区分寄存器和组合电路而设计的。

![image-20200322154338736](https://tva1.sinaimg.cn/large/00831rSTgy1gd2r1jhktyj30m70e9jxm.jpg)

#### 13. Verilog语法小结

##### 1. 数据类型语法小结

1. wire型数据通常用assign关键字进行赋值。wire只能被assign连续赋值，reg只能在initial 和always中赋值。Input端口只能定义成wire型。如果端口没有声明，则默认是wire线网型，且输入端口只能是wire线网型。
2. reg是寄存器数据类型的关键字。寄存器是数据存储单元的抽象，通过赋值语句可以改变寄存器存储的值，相当于改变触发器存储的值。reg型常用来表示always模块内的指定信号，代表触发器。在always块内被赋值的每一个信号都必须定义为reg型，即赋值操作符的左端变量必须是reg型。reg型只能在initial和always中赋值。
3. 所谓的always必须用reg的意思是always里面的赋值语句中的被赋值变量为reg型，而不是说在 always里面出现的变量都为reg型。
4. reg型与wire型的区别在于:reg型保持最后一次的赋值,而wire型需要持续的驱动。
5. memory型通过对reg型变量建立数组以对存储器建模。reg[n-1:0]的存储器名[m-1:0] reg[n-1:0]定义了存储器中每一个存储单元的大小,即该存储器单元是一个n位宽的寄存器,[m-1:0]代表了存储器的大小,即该存储器中有多少个这样的寄存器。
6. 一个n位寄存器可以在一条赋值语句中直接赋值,而一个完整的存储器则不行,如果要对 memory型存储单元进行读写,则必须要指定地址。
7. 用 parameter定义常量。

##### 2. 数制表示语法小结

1. 下划线“_”可以随意用在整数和实数中，没有实际意义,只是为了提高可读性。如56等效于5_6。
2. 整数负数使用补码形式表示,而不能表示为诸如:16‘h-9之类的形式。
3. 在进行基本算术运算时,如果某一操作数有不确定的值X,则运算结果也是不确定X。

##### 3. 赋值过程语法小结

1. 连续赋值语句只能用来对线网型变量进行赋值,而不能对寄存器变量进行赋值。一个线网型变量一旦被连续赋值语句赋值之后,赋值语句右端的赋值表达式的值将持续对被赋值变量产生连续驱动。只要右端表达式中任一操作数的值发生变化,就会立即触发对被赋值变量的更新操作。其基本的语法格式如下:线网型变量类型[线网型变量位宽]线网型变量名；assign#(延时量)线网型变量名=赋值表达式。
2. 过程赋值主要用于两种结构化模块（initial模块和always模块）中的赋值语句。在过程块中只能使用过程赋值语句(不能在过程块中出现连续赋值语句），同时过程赋值语句也只能用在过程赋值模块中。
3. 过程赋值语句只能对寄存器类型的变量(reg、integer、real和time)进行操作，经过赋值后，上面这些变量的取值将保持不变，直到另一条赋值语句对变量重新赋值为止。
4. memory型只能对指定地址单元的整个字进行赋值，不能对其中的某些位单独赋值。
5. 在关系运算符中，若某个操作数的值不定，则关系是模糊的，返回的是不定值X。
6. 实例算子“\=\=\=”和“\!\=\=”可以用于比较含有X、Z的操作数，在模块的功能仿真中有着广泛应用。

7. 对组合逻辑建模采用阻塞赋值；对时序逻辑建模采用非阻塞式赋值；用多个always块分别对组合和时序逻辑建模；尽量不要在同一个always块内混合使用阻塞赋值和非阻塞赋值，如果在同一个always块里面既为组合逻辑，又为时序逻辑建模，应使用非阻塞赋值；不要在多个always块中为同一个变量赋值。

##### 4. 结构描述小结

1. 一个程序可以有多个initial模块、always模块、task模块和function模块。initial模块和always 模块都是同时并行执行的，区别在于initial模块只执行一次，而always模块则是不断重复运行，task模块和function模块能被多次调用。initial模块是面向仿真的，是不可综合的。
2. begin...end块定义语句中的语句是串行执行的，而fork...join块语句中的语句是并行执行的。
3. 利用always实现组合逻辑时，要将所有的信号放进敏感列表中，而实现时序逻辑时却不一定要将所有的结果放进敏感信号列表中。
4. 延时控制只能在仿真中使用，是不可综合的，在综合时，所有的延时控制都会被忽略。
5. if语句中的else不能省，case语句的default分支虽然可以默认，但是一般不要默认。
6. case语句的分支是并行执行的，各个分支没有优先级，而f语句的选择分支是串行执行的。
7. forever循环语句用于连续执行过程语句，必须写在initial模块中。
8. repeat循环语句执行指定循环数，如果循环技术表达式的值不确定，即为X或Z时，那么循环次数按0处理。

##### 5. verilog 实现相关小结

1. 组合逻辑电路在逻辑功能上的特点是：任意时刻的输出仅仅取决于该时刻的输入,与电路原来的状态无关。而时序逻辑电路在逻辑功能上的特点是:任意时刻的输出不仅取决于当时的输入信号,而且还取决于电路原来的状态,或者说,还与以前的输入有关。
2. 状态机一般包括组合逻辑和寄存器逻辑两部分。组合电路用于状态译码和产生输出信号,寄存器用于存储状态。
3. 所谓高阻是输出端属于浮空状态,只有很小的漏电流流动,其电平随外部电平高低而定,门电平放弃对输出电路的控制或者可以理解为输出与电路是断开的。双向引脚的输出就  是在不使能状态下,输出为高阻,而在使能状态下输出为正常。

### 1.3 复杂逻辑模的设计

前面章节详细介绍了Verilog的语法，并通过8个简单例子给出了常见的逻辑电路设计套路。本节将讲述如何利用简单的Verilog描述实现复杂的逻辑电路。

对于复杂的逻辑电路，可能有很多的答案，但有一点是一定的：电路描述的信息熵很高。逻辑电路的复杂性通常表现在以下几个方面：①内部结构组成复杂；②控制状态复杂且高度耦合；③接口复杂且信号数量很多；④模块数量巨大。

站在工程学的角度，内部结构组成的复杂可以通过树状结构或网状结构进行消解，即通常所说的结构化设计（Hierarchy)和总线设计；<span style='color:red;;'>控制状态的复杂则可以通过控制流分解，</span>按照主从状态细化的方法降低单个组成单元的复杂度；而任何复杂的接口都可以简化为流量控制处理，并通过成熟的通信协议设计降低难度；数量巨大的模块可以通过分类整理，实现条理化、清晰化。因此，任何复杂的逻辑设计最终可以演变为：①对基本单元的归类整理和层次化设计，即结构化设计；②基本功能的单元设计，即数据流设计；③将层次化的基本单元进行梳理，最终形成全局电路控制，即控制流设计。

