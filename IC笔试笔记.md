#     1. 环形振荡器

**[环形振荡器]([https://zh.wikipedia.org/wiki/%E7%8E%AF%E5%BD%A2%E6%8C%AF%E8%8D%A1%E5%99%A8](https://zh.wikipedia.org/wiki/环形振荡器))**是一种采用奇数个[非门](https://zh.wikipedia.org/wiki/非门)组成的环形电路。电路的输出是[振荡](https://zh.wikipedia.org/wiki/振荡)产生两个电平。偶数个反向器无法形成环形振荡器。这是由于输出的情况和输入相同。

![File:Ring_oscillator_(3-stage)](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f7/Ring_oscillator_%283-stage%29.svg/300px-Ring_oscillator_%283-stage%29.svg.png)

# 2. 同步复位和异步复位比较

同步复位：可以滤除高于手中频率的毛刺，复位信号必须大于时钟周期，同时考虑时钟skew；由于大多数都是异步复位，所以采用同步复位需要在数据输入口中增加组合逻辑，消耗更多的逻辑资源。

异步复位：不需要额外的数据口插入逻辑，所以节约资源，异步信号识别方便；但是容易受毛刺影响；在复位信号释放容易出现问题，复位信号在时钟有效沿时释放，使寄存器输出出现亚稳态，导致亚稳态。

异步复位，同步释放：将复位信号与时钟同步后。

```verilog
//异步复位，同步释放代码
module prac ( clk, reset_n,  dataa,   datab,   outa,  outb );
    input        clk;
    input        reset_n;
    input        dataa;
    input        datab;
    output        outa;
    output        outb;
    reg            reg1;
    reg            reg2;
    reg            reg3;
    reg            reg4;
    assign    outa    =>     assign    rst_n    = reg4;
    always @(posedge>                 reg4    <= 1’b0;
            end
        else
            begin
                reg3    <= 1’b1;
                reg4    <= reg3;
            end
    end

    always @ (posedge>                 reg2    <= 1’b0;
            end
        else
            begin
                reg1    <=>             end
    end
endmodule
```

# 3. Johnson 计数器

约翰逊(Johnson)计数器又称扭环计数器,是一种用n位触发器来表示2n个状态的计数器。它与环形计数器不同,后者用n位触发器仅可表示n个状态。2~n进制计数器(n为触发器的个数)有2~n个状态。若以四位二进制计数器为例,它可表示16个状态。但由于8421码每组代码之间可能有二位或二位以上的二进制代码发生改变,这在计数器中特别是异步计数器中就有可能产生错误的译码信号,从而造成永久性的错误。而约翰逊计数器的状态表中,相邻两组代码只可能有一位二进制代码不同,故在计数过程中不会产生错误的译码信号。鉴于上述优点,约翰逊计数器在同步计数器中应用比较广泛。

```verilog
module johnson
#( parameter N=4)
(    
  	input clk,
    input rst_n,
    output reg [N-1:0] q
);

always @ (posedge clk,negedge rst_n)
if(!rst_n)
    q <= {N{1'b0}};
else if(!q[0])
    q <= {1'b1,q[N-1:1]};
else
    q <= {1'b0,q[N-1:1]};   

endmodule
```

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdp1fndmwlj30k007s40y.jpg)

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdp1gjcylxj30k001pmxb.jpg)

**优缺点**n个触发器组成的扭环形计数器 2n 个有效状态，有效状态利用率比环形计数器增加一倍。3 位扭环形计数器可构成 6 节拍发生器 T0 ~ T5，但需加译码电路。由于电路在每次状态转换时，只有一位触发器改变状态，电路译码时不会产生竞争冒险现象，而且，译码电路简单。

 

# 4. 触发器

## 4.1 RS 触发器

Set-reset， 基本RS触发器又称SR锁存器，是触发器中最简单的一种，也是各种其他类型触发器的基本组成部分。两个[与非门](https://zh.wikipedia.org/wiki/与非门)或[或非门](https://zh.wikipedia.org/wiki/或非门)的输入端输出端进行交叉耦合或首尾相接，即可构成一个基本RS触发器。

当R与S皆为低电位，[回授](https://zh.wikipedia.org/wiki/回授)会让Q与Q（Q的反相）保持于一个固定的状态。当S("Set")为高电位，R("Reset")为低电位时，输出Q会被强制设置为高电位；相反的，当S为低电位，R为高电位时，输出Q会被强制设置为低电位。

| SR锁存器运算[[2\]](https://zh.wikipedia.org/wiki/触发器#cite_note-2) |       |           |              |                                                |           |       |       |
| :----------------------------------------------------------: | :---: | --------- | ------------ | ---------------------------------------------- | --------- | ----- | ----- |
|    [状态转移表](https://zh.wikipedia.org/wiki/状态转移表)    |       |           |              | [激励表](https://zh.wikipedia.org/wiki/激發表) |           |       |       |
|                            **S**                             | **R** | **Qnext** | **动作**     | **Q**                                          | **Qnext** | **S** | **R** |
|                              0                               |   0   | Q         | 保持         | 0                                              | 0         | 0     | X     |
|                              0                               |   1   | 0         | 重置         | 0                                              | 1         | 1     | 0     |
|                              1                               |   0   | 1         | 设置         | 1                                              | 0         | 0     | 1     |
|                              1                               |   1   | X         | 不允许的输入 | 1                                              | 1         | X     | 0     |

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdqq8rmbydg306404hmzt.gif)

## 4.2 D触发器

D触发器有一个输入、一个输出和一个时脉输入，当时脉由0转为1时，输出的值会和输入的值相等。此类触发器可用于防止因为[噪声](https://zh.wikipedia.org/wiki/雜訊_(通訊學))所带来的错误，以及通过[管线](https://zh.wikipedia.org/wiki/管線)增加处理数据的数量。

| D    | CK   | Q    | **Qnext** |
| ---- | ---- | ---- | --------- |
| 0    | 1    | X    | **0**     |
| 1    | 1    | X    | **1**     |
| X    | 0    | 0    | **0**     |
| X    | 0    | 1    | **1**     |

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdqqb2q26fj306407cdfn.jpg)

## 4.3 JK触发器

JK触发器和触发器中最基本的RS触发器结构相似，其区别在于，RS触发器不允许R与S同时为1，而JK触发器允许J与K同时为1。当J与K同时变为1的同时，输出的值状态会反转。也就是说，原来是0的话，变成1；原来是1的话，变成0。 对应表如下:

| JK触发器运算[[4\]](https://zh.wikipedia.org/wiki/触发器#cite_note-manokime-4) |       |          |           |                                                |           |          |       |       |
| :----------------------------------------------------------: | :---: | -------- | --------- | ---------------------------------------------- | --------- | -------- | ----- | ----- |
|    [状态转移表](https://zh.wikipedia.org/wiki/状态转移表)    |       |          |           | [激励表](https://zh.wikipedia.org/wiki/激發表) |           |          |       |       |
|                            **J**                             | **K** | **动作** | **Qnext** | **Q**                                          | **Qnext** | **动作** | **J** | **K** |
|                              0                               |   0   | 保持     | Q         | 0                                              | 0         | 不变     | 0     | X     |
|                              0                               |   1   | 重置     | 0         | 0                                              | 1         | 设置     | 1     | X     |
|                              1                               |   0   | 设置     | 1         | 1                                              | 0         | 重置     | X     | 1     |
|                              1                               |   1   | 反转     | $\bar{Q}$ | 1                                              | 1         | 不变     | X     | 0     |

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdqqd5qcorj306407cjr7.jpg)

## 4.4 T触发器

T触发器（Toggle Flip-Flop，or Trigger Flip-Flop）设有一个输入和输出，当时脉由0转为1时，如果T和Q不相同时，其输出值会是1。输入端T为1的时候，输出端的状态Q发生反转；输入端T为0的时候，输出端的状态Q保持不变。把JK触发器的J和K输入点连接在一起，即构成一个T触发器。

| T触发器运算[[4\]](https://zh.wikipedia.org/wiki/触发器#cite_note-manokime-4) |                                                |      |                                                            |      |      |      |      |
| :----------------------------------------------------------: | :--------------------------------------------: | :--: | :--------------------------------------------------------: | :--: | :--: | :--: | :--: |
|    [状态转移表](https://zh.wikipedia.org/wiki/状态转移表)    | [激励表](https://zh.wikipedia.org/wiki/激發表) |      |                                                            |      |      |      |      |
|                              T                               |                       Q                        |      |                            动作                            |      |      |      | 动作 |
|                              0                               |                       0                        |  0   | 保持（无[上升沿](https://zh.wikipedia.org/wiki/信号边缘)） |  0   |  0   |  0   | 不变 |
|                              0                               |                       1                        |  1   | 保持（无[上升沿](https://zh.wikipedia.org/wiki/信号边缘)） |  1   |  1   |  0   | 不变 |
|                              1                               |                       0                        |  1   |                            反转                            |  0   |  1   |  1   | 反相 |
|                              1                               |                       1                        |  0   |                            反转                            |  1   |  0   |  1   | 反相 |

![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a9/T-Type_Flip-flop.svg/220px-T-Type_Flip-flop.svg.png)

# 5. 多路复用器实现D锁存器

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdqsa9i85oj305b04fjs3.jpg)

# 门控时钟

通过锁存器，作为门控时钟，锁存器为低时改变，为高时保持，可以使输出时钟稳定。

![image-20200503082612335](https://tva1.sinaimg.cn/large/007S8ZIlgy1geeyfalkvoj30sn0el78v.jpg)

# 跨时钟域处理

## 1. 单bit信号

两级同步，边沿检测、脉冲检测：

## 2. 多bit信号

fifo、 双口ram、握手机制：

# 状态机

# 1. 米勒型：输出与状态和输入有关

## 2. 摩尔型：输出仅与状态有关。

**总结：**采用moore型状态机比较好，因为只与状态有关，所以就可以较好避免竞争与冒险（比如有多输入状态，可能由于信号到达的时间差，导致输出不同，而moore型是内部时许信号，所以可以很好的规避竞争冒险）

# systemverilog

## 1. interface 如何定义

![image-20200504110133396](https://tva1.sinaimg.cn/large/007S8ZIlgy1geg8j9on1qj30km0drdm6.jpg)

- 这样定义以后arb_if 就相当于形参类型（如 reg 一样）

![image-20200504110609347](https://tva1.sinaimg.cn/large/007S8ZIlgy1geg8o0no3fj30nz0g8k10.jpg)

- 这样是比较常用的方式

![image-20200504111411307](https://tva1.sinaimg.cn/large/007S8ZIlgy1geg8wdyw7jj30n60et48s.jpg)

- modport 可以预先定义模块使用端口

# 6. linux 快捷操作

## 1. grep

![image-20200416105232300](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdvf4dhdofj30nr0c2tfm.jpg)

可以用于查找error 在log 文件中使用。

## 2. >,>>

![image-20200416105652682](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdvf8sjktoj30bt01bmxk.jpg)

重定向，和追加

## 3. ｜ 管道

将前面的输出作为后面的输入

## 4. More,less,cat

More: 在终端显示文件内容，安enter键会显示下一页，q会退出

less：同more，区别在于，按 下方向键 翻页

cat：在终端全部显示，如果不够，则只显示最后一部分。

## 5. cp 

![image-20200418091609562](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdxnko946dj30n00ay10w.jpg)

../  表示上一级目录

## 6. mv

mv既可以移动目录，也可以对目录进行重命名。

如果移动目录的话，需要添加斜杠

```shell
mv llg.txt ../   %%表示将当前目录llg.txt 文件移动到上一级目录
mv llg.txt ll.txt %%表示将当前目录的llg.txt文件重命明为ll.txt
mv text txt/  %%表示将当前目录下的text文件，移动到txt文件夹下
```

## 7. find

查找文件

```shell
find . -name "*.log"  %%表示在当前目录文件下(包括子目录)， 以名字方式查找，以.log结尾的文件
```

## 8 rm

删除

## 9. vim 一些基本操作

### 1. 替换 

```vim
：%s/error/warning/g  %%表示将文件中的error 字符替换为warning,采用全局替换的方式（global）
：100，200s/error/warning/g  %%表示替换100到200行间的元素
```

### 2. u 撤销操作  .  重复操作

### 3. gf 直接文件跳转

使用方式： 在vim环境中，有一个相对地址，鼠标放在地址上，直接按gf

# 7. Verilog 语法

### 1. RAM表示

前面的是宽度，后面是深度。

 Reg [RAM_WIDTH-1:0]  Mem [RAM_DEPTH-1:0];     

## 2. 数值计算

- 算术运算

数值计算包含三种情况：两个有符号数运算、两个无符号数运算、有符号数运算和无符号数运算。

<span style='color:red;;'>只有两个操作数都是有符号数，才会把两个操作数都看作有符号数计算，否则无论是有符号数还是无符号数都会按照无符号数计算。</span>

- 下部分比较老，是98版verilog语法

```verilog
reg [7:0] a, b;
reg [3:0] c;
reg [7:0] sum1, sum2, sum3, sum4;	
sum1 = a + b;// same width. can be applied to signed and unsigned
sum2 = a + c;// automatica 0 extensionsum2 = a + c;
sum3 = a + {4{ 1'b0 }, c};// manual 0 extension
sum4 = a + {4{c[3]}, c};// manual sign extension
```

在第一条语句中，a、b和sum1有相同的位宽，因此无论是转译成有符号数还是无符号数，它都将引用相同的加法器电路。

在第二条语句中，c的位宽仅为4，在加法运算中，它的位宽会被调整。因为reg类型被作为无符号数看待，所以c的前面会被自动置入0扩展位。

在第三条语句中，我们给c手动前置4个0，以实现和第二个表达式一样的效果。

在第四条语句中，我们需要把变量转译成有符号数。为了实现所需的行为，c必须扩展符号位到8位。没有其他的办法，只好手动扩展。在代码中，我们重复复制c的最高位4次（4{c[3]}）来创建具有扩展符号位的8位数。

- 有符号数赋值

将一个数或计算结果A(有符号或者无符号)赋值给另一个数B，根据位宽不同有以下三种情况：

当位宽相同的时候直接赋值，无论B是有符号或者无符号，对赋值结果没有影响，只是把二进制位完全不变的赋值进来。

当B的位宽比A小的时候，位宽截断如前面所说，从低位开始截取

当B的位宽比A大的时候，A的二进制码先填充在B的低位；高位填充分三种情况：

当B的位宽比A大的时候，A的二进制码先填充在B的低位；高位填充分三种情况：
    若A,B都是有符号数，B的高位用A的最高位填充
    若A,B都是无符号数，B的高位用0填充
    若A是有符号数，B是无符号数，B的高位用A的最高位填充
    若A是无符号数，B是有符号数，B的高位用0填充

<span style='color:red;;'>总结：若A是有符号数，则B高位空余用A的最高位填充，若A是无符号数，B的高位用0填充</span>

# 8. ASIC 流程

# 9. perl 

##  1. 纯变量(标量 )

`$a ` 

```perl
$a =23		 #将23赋值给 $a
$a++.  #d等于$a = $a +1
```

## 2. 数组

### 1. 数组实量

```perl
(1,2,3,4,5) #具有1，2，3，4，5 五个数值数组
(llg,2,3)  #具有llg，2，3 的数组	
```



### 2. 数组标量

以 `@`开头

```perl
@llg = {1,2,3}  #将1，2，3 复杂给@llg
（$a, $b, $c） = (1,2,3)
```

### 3. eg

```perl
#!/usr/bin/perl #所有perl语言默认，主要是指向编译器	
$a = 123;
$b = "Hellow world";

print $a;  #这样会连起来输出
print $b;
```

![image-20200420172923463](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge0d2hgm0cj30eq03ggll.jpg)

```perl
#!/usr/bin/perl #所有perl语言默认，主要是指向编译器	
$a = 123;
$b = "Hellow world";
@a = (1,2,3,5);
$c = @a
print "$a\n";  #这样会连起来输出 
print "$b\n";
print "$c\n";
```

### 4 . 哈希结构(同python 字典)

`%`

```
%ARRAY = （key1,value1,key2,value2,key3,value3）;
```

# 10. UVM

<span style='color:red;;'>不是语言，验证方法学，基于systemverilog  </span>,是从OVM演化过来的，架构，一套比较完整的库，成熟的运行机制。

![image-20200424153702113](https://tva1.sinaimg.cn/large/007S8ZIlly1ge4wat31sfj30lq0f5dgm.jpg)

## 1. 验证主要做什么

- 灌激励
- 集相应：
- 做比较：实际输出与预计输出做比较

 

## 2. UVM细节

### 1. phase 运行机制（UVM核心）

phase 分为两种，function phase， task phase

function 不占用仿真时间，task会占用仿真时间

phase 分为系统phase 和用户自定义phase

phase运行机制：

- 系统phase 顺序执行（这里有一张表，从上往下执行，build_phase -> main_phase， 如果没有显形，也会执行，只是空壳子，也会运行一遍)，在不同class内的phase是相互关联的， 但是同级phase 是同步的。
- 用户自定义phase： 在调用时才执行

## 2. interface（标准接口）

采用interface 通用性好，可移植性好。

1. UVM-info：打印。
2. UVM-warning：警告。
3. UVM-error：错误，可以暂时忽略，不用停止仿真，最后再统一处理。
4. UVM-fatal：严重错误，应当马上停止仿真。

## 3. transation（打包传输）

#### 写注册表

1. UVM_object_utils：
2. UVM_component_utils： 这两句话是用于注册使用，因为UVM有自己的运行机制，如果不注册，是无法运行的。

```
 uvm_object_utils (my_driver)   // 对my_driver 进行注册
 uvm_component_utils (my_driver) //不同与上一种注册方式
```

#### rand 机制

表示一个变量是可以随机的。 

```verilog
rand bit A;
```



#### sv fifo 信息：

1. bit[7:0] A[100]   ：固定了100深度
2. bit[7:0]A($)    :这种写法不沾深度，只是第一个地址指针，可以减少内存占用率。 哈希数组写法

#### 两个函数

1. push_back : 有个size值，push一次，size 加一，压入数组
2. pop_front :

## 4. Raise drop

有raise 和drop，在phase机制里面才能参考运行时间。可以理解为举手，没有raise 就会忽略这一运行时间，只考虑举手的这一部分。

super 会继承父类，通过extend重写，引入之前的phase（运行机制）。

## 5. create new区别

create，new 都是创建地址空间，但是create比new多一些功能，就是可以挂到UVM树型结构上，如果挂到树型结构上就可以利用UVM的一些运行机制，如phase机制。具体使用办法如下图所示

![image-20200504114617027](https://tva1.sinaimg.cn/large/007S8ZIlgy1geg9trfhw2j30oo0bwn4i.jpg)

- my_env只是对之前的内容重新打包。



# 11 计算time skew

![image-20200502114610374](https://tva1.sinaimg.cn/large/007S8ZIlgy1gedyl3vx9hj30vn0l37fp.jpg)

setup slack ： 是指两个时钟之间的冗余量， 计算time skew 只需要另setup slack 为零。

latch_edge  

![image-20200502144339243](https://tva1.sinaimg.cn/large/007S8ZIlgy1gee3prs8rij30xe0lwwqk.jpg)

