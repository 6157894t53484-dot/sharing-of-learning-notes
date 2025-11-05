# Verilog HDL 学习笔记

## 1. 硬件描述语言HDL概述

### 1.1 HDL简介
- **定义**: 硬件描述语言(Hardware Description Language)是一种以文本形式描述数字系统硬件结构和行为的语言
- **用途**: 表示逻辑电路图、逻辑表达式、复杂数字逻辑系统的逻辑功能
- **地位**: HDL是高层次自动化设计的起点和基础

### 1.2 常见HDL语言
- **ABEL** (Advanced Boolean Equation Language)
- **VHDL** (Very High Speed Integrated Circuit HDL)
- **Verilog HDL**: 句法根源出自C语言,相对VHDL更好用、好学

### 1.3 计算机对HDL的处理
- **逻辑综合**: 从HDL描述导出门级网表(基本元件列表及连接关系)
- **逻辑仿真**: 用仿真软件预测电路的结构和行为,以文本或波形图显示输出

---

## 2. Verilog基本语法规则

### 2.1 间隔符
- 空格符 `\b`
- TAB键 `\t`
- 换行符 `\n`
- 换页符

### 2.2 注释符
```verilog
// 单行注释:从//开始到行尾

/* 
   多行注释:
   可以跨越多行
*/
```

### 2.3 逻辑值集合
Verilog规定了4种基本逻辑值:
- `0` - 逻辑0、逻辑假
- `1` - 逻辑1、逻辑真
- `x` 或 `X` - 不确定的值(未知状态)
- `z` 或 `Z` - 高阻态

### 2.4 标识符和关键词
- **标识符**: 给对象取名的字符串,以英文字母或下划线开始
  - 例如: `clk`, `counter8`, `_net`, `bus_A`
- **关键词**: Verilog预定义的特殊字符串(全部小写)
  - 例如: `module`, `endmodule`, `input`, `output`, `wire`, `reg`, `and`等

### 2.5 常量表示

#### 整数型常量
```verilog
// 十进制形式
30
-2

// 带基数形式: <+/-><size>'<signed><base><number>
3'b101      // 3位二进制
-5'o37      // 5位八进制(带符号)
12'h13x     // 12位十六进制
8'b1001_0011  // 下划线提高可读性
```

#### 实数型常量
```verilog
// 十进制记数法
0.1, 2.0, 5.67

// 科学记数法
23_5.1e2    // 代表 23510.0
5E-4        // 代表 0.0005
```

#### 符号常量(参数)
```verilog
parameter BIT = 1, BYTE = 8, PI = 3.14;
```

---

## 3. 变量数据类型

### 3.1 线网类型(Wire)
- **定义**: 输出始终根据输入变化而更新的变量
- **特点**: 代表硬件电路中元件间的物理连线
- **语法**:
```verilog
wire L;                // 1位线网
wire [7:0] data_bus;   // 8位线网总线
```

### 3.2 寄存器类型

| 类型 | 说明 |
|------|------|
| `reg` | 常用的寄存器型变量 |
| `integer` | 32位带符号整数型变量 |
| `real` | 64位带符号实数型变量 |
| `time` | 64位无符号时间变量 |

**特点**:
- 寄存器型变量对应具有状态保持作用的电路元件
- 只能在`initial`或`always`块内部被赋值
- 是抽象描述,不一定对应具体硬件

```verilog
reg clock;           // 1位寄存器
reg [3:0] counter;   // 4位寄存器
```

---

## 4. 运算符及优先级

### 4.1 运算符分类

#### 算术运算符
```verilog
+   // 加
-   // 减(二进制)、取负(一元)
*   // 乘
/   // 除
```

#### 逻辑运算符
```verilog
!   // 逻辑非
&&  // 逻辑与
||  // 逻辑或
```

#### 关系运算符
```verilog
>   <   >=   <=   ==   !=
```

#### 位运算符
```verilog
~   // 按位取反
&   // 按位与
|   // 按位或
^   // 按位异或
~^  // 按位同或(也可写作 ^~)
```

#### 缩位运算符(单目)
```verilog
&   // 缩位与:将所有位进行与运算
~&  // 缩位与非
|   // 缩位或
~|  // 缩位或非
^   // 缩位异或
~^  // 缩位同或
```

**示例**:
```verilog
// A = 4'b1010
&A  = 1 & 0 & 1 & 0 = 0
|A  = 1 | 0 | 1 | 0 = 1
^A  = 1 ^ 0 ^ 1 ^ 0 = 0
```

#### 移位运算符
```verilog
<<  // 左移
>>  // 右移
```

#### 位拼接运算符
```verilog
{A, B, C}      // 将多个操作数拼接
{4{A}}         // 重复拼接:A重复4次
{2{A}, 2{B}, C} // 混合拼接
```

**示例**:
```verilog
A = 1'b1, B = 2'b10, C = 2'b00
{B, C} = 4'b1000
{A, B[1], C[0]} = 3'b110
{4{A}} = 4'b1111
```

#### 条件运算符(三目)
```verilog
condition ? expr1 : expr2;
// 如果condition为真,返回expr1,否则返回expr2
```

### 4.2 运算符优先级(从高到低)

| 优先级 | 运算符 |
|--------|--------|
| 最高 | `!` `~` `-`(取负) |
| ↓ | `*` `/` |
| ↓ | `+` `-` |
| ↓ | `<<` `>>` |
| ↓ | `<` `<=` `>` `>=` |
| ↓ | `==` `!=` |
| ↓ | `&` `~&` |
| ↓ | `^` `^~` |
| ↓ | `|` `~|` |
| ↓ | `&&` |
| ↓ | `||` |
| 最低 | `?:` |

---

## 5. Verilog程序基本结构

### 5.1 模块结构
```verilog
module 模块名(端口列表);
    // 端口类型说明
    input ...;
    output ...;
    inout ...;
    
    // 参数定义(可选)
    parameter ...;
    
    // 数据类型定义
    wire ...;
    reg ...;
    
    // 逻辑功能描述(顺序任意)
    // - 实例化低层模块和基本门级元件
    // - 连续赋值语句(assign)
    // - 过程块(initial和always)
    
endmodule
```

### 5.2 模块设计要点
1. 每个模块以`module`开始,以`endmodule`结束
2. 先定义端口,说明输入输出
3. 除`endmodule`外,每个语句后必须有分号
4. 可使用`/* */`和`//`添加注释

---

## 6. 三种建模方式

### 6.1 门级建模(结构描述)

#### 基本门级元件

**多输入门**(单输出,多输入):
- `and` `nand` `or` `nor` `xor` `xnor`

**多输出门**(单输入,多输出):
- `buf` `not`

**三态门**(一个输出,一个数据输入,一个控制输入):
- `bufif0` `bufif1` `notif0` `notif1`

#### 门级建模示例
```verilog
module mux2to1(D0, D1, S, Y);
    input D0, D1, S;
    output Y;
    wire Snot, A, B;
    
    not U1(Snot, S);
    and U2(A, D0, Snot);
    and U3(B, D1, S);
    or  U4(Y, A, B);
endmodule
```

### 6.2 数据流建模

使用**连续赋值语句**`assign`:
```verilog
wire [位宽] 变量名;
assign 变量名 = 表达式;
```

**示例**:
```verilog
module mux2to1_dataflow(D0, D1, S, Y);
    input D0, D1, S;
    output Y;
    wire Y;
    
    assign Y = (~S & D0) | (S & D1);
endmodule
```

### 6.3 行为级建模

使用`always`或`initial`过程块,内部可使用:
- 条件语句(`if-else`)
- 多路分支语句(`case`)
- 循环语句(`for`)

#### 6.3.1 if语句

```verilog
// 形式1:单分支
if (condition) 
    statement;

// 形式2:双分支
if (condition)
    statement1;
else
    statement2;

// 形式3:多分支
if (condition1)
    statement1;
else if (condition2)
    statement2;
else
    default_statement;
```

**示例:4选1数据选择器**
```verilog
module mux4to1_bh(D, S, Y);
    input [3:0] D;
    input [1:0] S;
    output reg Y;  // 行为级建模输出必须是reg类型
    
    always @(D, S)
        if (S == 2'b00) 
            Y = D[0];
        else if (S == 2'b01) 
            Y = D[1];
        else if (S == 2'b10) 
            Y = D[2];
        else 
            Y = D[3];
endmodule
```

#### 6.3.2 case语句

```verilog
case (case_expr)
    item_expr1: statement1;
    item_expr2: statement2;
    ...
    default: default_statement;  // 可选
endcase
```

**注意**:
- 多条语句需用`begin...end`包围
- `casex`和`casez`用于处理无关项

**示例**:
```verilog
module mux4to1_bh(D, S, En, Y);
    input [3:0] D;
    input [1:0] S;
    input En;
    output reg Y;
    
    always @(D, S, En)
    begin
        if (En == 1)
            Y = 0;  // 禁止工作
        else
            case (S)
                2'd0: Y = D[0];
                2'd1: Y = D[1];
                2'd2: Y = D[2];
                2'd3: Y = D[3];
            endcase
    end
endmodule
```

#### 6.3.3 for循环语句

```verilog
for (initial_assignment; condition; step_assignment)
    statement;
```

**示例:3-8译码器**
```verilog
module encoder3to8_bh(A, En, Y);
    input [2:0] A;
    input En;
    output reg [7:0] Y;
    integer k;
    
    always @(A, En)
    begin
        Y = 8'b1111_1111;  // 默认值
        for(k = 0; k <= 7; k = k+1)
            if ((En == 1) && (A == k))
                Y[k] = 0;
            else
                Y[k] = 1;
    end
endmodule
```

---

## 7. 层次化设计

### 7.1 设计方法
- **自顶向下**: 从系统整体到具体模块
- **自底向上**: 从基本模块到复杂系统

### 7.2 模块调用

#### 位置关联调用
```verilog
fulladder FA0(S[0], C0, A[0], B[0], C_1);
// 按端口定义顺序传递参数
```

#### 名称关联调用
```verilog
halfadder HA1(
    .A(A),
    .B(B),
    .S(S1),
    .C(D1)
);
// 使用.端口名(信号名)方式,顺序可变
```

### 7.3 带参数模块

```verilog
// 定义带参数的模块
module adder #(parameter n = 4)(
    input [n-1:0] A, B,
    input Ci,
    output [n-1:0] S,
    output Co
);
    assign {Co, S} = A + B + Ci;
endmodule

// 调用时指定参数
adder #(.n(8)) U1(
    .A(A1),
    .B(B1),
    .Ci(Cin1),
    .S(Sum1),
    .Co(Cout1)
);
```

### 7.4 层次化设计示例

**半加器→全加器→4位加法器**

```verilog
// 1. 半加器
module halfadder(input A, B, output S, C);
    xor (S, A, B);
    and (C, A, B);
endmodule

// 2. 全加器(调用半加器)
module fulladder(input A, B, CI, output S, CO);
    wire S1, D1, D2;
    halfadder HA1(.A(A), .B(B), .S(S1), .C(D1));
    halfadder HA2(.A(S1), .B(CI), .S(S), .C(D2));
    or (CO, D2, D1);
endmodule

// 3. 4位全加器(调用全加器)
module adder_4bit(
    input [3:0] A, B,
    input C_1,
    output [3:0] S,
    output C3
);
    wire C0, C1, C2;
    fulladder FA0(S[0], C0, A[0], B[0], C_1),
              FA1(S[1], C1, A[1], B[1], C0),
              FA2(S[2], C2, A[2], B[2], C1),
              FA3(S[3], C3, A[3], B[3], C2);
endmodule
```

---

## 8. 关键要点总结

### 8.1 三种建模方式对比

| 建模方式 | 主要语句 | 适用场景 |
|----------|----------|----------|
| 门级(结构) | 基本门元件调用 | 层次化设计 |
| 数据流 | `assign`连续赋值 | 组合逻辑电路 |
| 行为级 | `always`/`initial`块 | 组合和时序逻辑 |

### 8.2 重要规则
1. **assign语句**赋值对象必须是`wire`类型
2. **过程赋值**(`always`/`initial`内)对象必须是`reg`类型
3. `reg`类型不一定对应硬件寄存器,只是变量类型
4. 行为级建模的输出端口通常声明为`output reg`

### 8.3 敏感信号列表
```verilog
always @(信号1, 信号2, ...)  // 信号变化时触发
always @(*)                   // 所有信号变化时触发(组合逻辑推荐)
```

---

## 9. 学习建议

1. **从简单开始**: 先掌握门级建模,再学数据流,最后学行为级
2. **多写多练**: 尝试用三种方式实现相同电路
3. **注重仿真**: 编写testbench验证设计正确性
4. **理解综合**: 了解HDL代码如何映射到实际硬件
5. **层次设计**: 养成模块化、层次化设计习惯