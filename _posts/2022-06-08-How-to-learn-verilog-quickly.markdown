## 什么是verilog编程？

首先verilog是一门编程语言。verilog的主要应用场景是数字前端开发，也即是通常所说的RTL开发。
verilog作为一种编程语言，是数字前段开发的必备工具。同时区别于面对对象语言（如C++等），函数式语言（python等），脚本语言（bash等），她是一种“描述式语言”，也可以称之为“面向实体”的语言。
那么描述的目标是什么呢？

## 作为一种描述语言
既然是描述语言，她主要描述什么？当然是电路。描述电路的逻辑行为，以及电路之间的连接关系。如果熟悉《数字电路》，我们知道电路的基本组成是逻辑单元（与、或、非等逻辑运算）和存储单元（D触发器等）。至于为什么这么划分，就要从图灵机和冯诺伊曼架构说起了，这就大大超出了本文篇幅，留待以后再介绍。
因此verilog语言的本质是对这些电路结构的描述。
## verilog里面的基本结构
所有的物理电路在verilog中抽象成2种类型的实体：module和always块。
电路无论小到基本的元器件，还是由众多元器件组成的复杂功能电路，都是一个实体，即：module。
在verilog中首先声明一个module，语法如下：

```clike
module abc(
	input clk,
	input rstb,
	input a,
	input b,
	output c
);

//add always here

endmodule
```
模块的定义主要是声明模块的名称，模块的端口（输入和输出）。名称在整个设计中具有唯一性。

另外一个重要的“实体”就是always块。always块是内嵌在module里面的电路结构，主要的作用是描述电路的行为，即功能。

## verilog里面的变量
几乎所有的编程语言的语法书都是从数据结构入手的。即读者先要知道语法的数据结构才能组织程序。这里在本文的中间部分才引入变量系统，因为verilog里面的数据结构非常简单，读者只需要知道两种类型即可。这两种分别是：wire和reg。

wire就是电路中的连线。reg顾名思义就是电路中的“寄存器”，但其实reg并不完全等同于存储器。
严格意义上说，wire用于连续性赋值，reg用于触发性赋值。本文也不打算展开了阐述，读者只需要记住，assign语句中用wire型，always语句中用reg型就行了。

## 电路需要如何描述？
电路的结构抽象地用两种方式描述：连接关系与逻辑行为。
所谓的连接关系描述是为了表示电路中的子模块之间的互联行为的。
举例来说，如果A的输出连到B上，B的输出又连的C上，诸如此类，可以这样表达：

```clike
module top(
	input in2a,
	output c2out
);

wire a2b;
wire b2c;

A u_a(
	.in1(in2a),
	.out1(a2b)
);

B u_b(
	.in1(a2b),
	.out1(b2c)
);

C u_c(
	.in1(b2c),
	.out1(c2out)
);
endmodule
```
上面的例子只用到了module以及通过wire将他们互联起来。如果电路只有这些基本的语法，那么这样的代码可以称之为门级网表。为了能够表达比较复杂的电路，需要借助高级的语言。用人类易于理解的方式来表达电路，这就需要引入always块。

always块的好处是可以在里面内嵌诸如：分支语句（if else结构，case语句）、逻辑操作（&、｜、～等）、算数运算（+、-等）。这些语法特点都是其他高级语言所共有的，比较容易理解。

其中有别于其他语言的重要特点是always分时序逻辑和组合逻辑。

下面举例说明：
```clike
always@(posedge clk or negedge rstn)
begin
	if(!rstn)
		cnt <= 8'd0;
	else
		cnt <= cnt+8'd1;
end
```
这是一个简单的计数器的例子。每个时钟周期计数器值加1。
再举一个例子：

```clike
always@(*)
begin
	if(add_en)
		out_c = in_a+in_b;
	else
		out_c = in_a-in_b;
end
```
这个例子是一个组合逻辑电路，即没有锁存结构。
从verilog来看，主要区别就是always后面的敏感列表不同。敏感列表里面有时钟沿的信息就是时序逻辑，否则就是组合逻辑。
细心的读者还会发现里面的赋值语句表达式不太一样，一个是用的“<=”，一个是"="。这个需要注意。verilog里这两种赋值的场景非常多，但对于表达实际电路来说，只需要记住在时序电路里用<=赋值，在组合电路里用=赋值就行了。

## 小结
到此为止，我们已经窥探了verilog语法的基本组成。对于RTL开发来讲，无论多么复杂的设计，无外乎上述一些语法片段的组合。熟练掌握上述语法，基本上是掌握了verilog入门的钥匙。verilog本身还有更多的语法，但大部分是不可综合（即和实际电路无关），这些留在后续再继续补充完善。

