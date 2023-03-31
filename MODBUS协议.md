# MODBUS协议

## 1.协议简介

自从 1979 年出现工业串行链路的事实标准以来，Modbus使成千上万的自动化设备能够通信。

Modbus 是OSI模型**第 7 层**上的**应用层报文传输协议**，它在连接至不同类型总线或网络的设备之间提供客户机/服务器通信。

Modbus应用层协议和服务规范主要包含两类：

一类是**串行链路**上的Modbus（Modbus 串行链路取决于TIA/EIA 标准：232-F 和 485-A）。

另一类是**TCP/IP** 上的Modbus（Modbus TCP/IP 取决于IETF 标准：RFC793 和 RFC791 有关）。

> ![img](https://img-blog.csdnimg.cn/20200405102345590.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)Modbus通信栈示意图如上所示：

Modbus协议允许在**各种网络体系结构**内进行简单通信，每种设备（PLC、HMI、控制面板、驱动程序、动作控制、输入/输出设备）都能使用 Modbus协议来启动远程操作。

在基于串行链路和以太 TCP/IP 网络的 Modbus上可以进行相同通信，一些网关允许在几种使用 Modbus协议的总线或网络之间进行通信。Modbus网络体系结构的实例如下所示：

![img](https://img-blog.csdnimg.cn/20200405102605797.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

## 2.协议格式

Modbus协议本质上和我们平时自定义的协议一致，就是一种数据传输格式，也是由起始帧、数据帧、校验帧等组成，只不过Modbus协议更为规范。

Modbus协议定义了一个与基础通信层无关的**简单协议数据单元（PDU）**。

特定总线或网络上 的 Modbus协议映射能够在应用数据单元（ADU）上引入一些附加域，通用Modbus帧协议如下图所示：

> **![img](https://img-blog.csdnimg.cn/202004051033335.png)例如上图的ADU中，除去简单协议数据单元(PDU)，还包括了附加域如地址域和差错校验**

具体解析如下：

·    地址域：子节点的地址信息；

·    功能码：指明服务器要执行的动作，有效的码字范围是十进制 **1-255**（128-255 为异常响应保留），当从客户机向服务器设备发送报文时，功能码域通知服务器执行哪种操作（功能码内容较多，建议查阅Modbus协议说明手册，上面介绍的很清晰）。

·    数据：传输的数据内容，在某种请求中，数据**可以是不存在的**（0 长度），在此情况下服务器不需要任何附加信息，功能码仅说明操作。

·    差错校验：验证收、发的数据是否正确。

如果在一个正确接收的 Modbus ADU 中，不出现与请求Modbus功能有关的差错，那么服务器至客户机的响应数据域包括请求数据。

如果出现与请求 Modbus功能有关的差错，那么域**包括一个异常码**，服务器应用能够使用这个域确定下一个执行的操作。Modbus事务处理的状态图如下所示：

![img](https://img-blog.csdnimg.cn/20200405110636682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

一旦服务器处理请求，使用合适的 Modbus 服务器事务建立 Modbus 响应，根据处理结果，可以建立**两种**类型响应：

一个正确Modbus响应：响应功能码 = 请求功能码 ；

一个 Modbus异常响应：用来为客户机提供处理过程中与被发现的差错相关的信息，提供一个异常码来指示差错原因。
响应功能码 = 请求功能码 + 0x80。
将上述图示分解理解，例如，客户机与服务器执行Modbus 通信协议，当服务器对客户机响应时，它**使用功能码**来指示正常（无差错）响应或者出现某种差错（称为异常响应）。

对于一个正常响应来说，服务器仅对原始功能码响应，Modbus 事务处理（无差错）如下图所示：

![img](https://img-blog.csdnimg.cn/20200405105210687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

对于**异常**响应，服务器返回一个与原始功能码等同的码，设置该原始功能码的**最高有效位为逻辑 1**，Modbus 事务处理（异常响应）如下图所示：

![img](https://img-blog.csdnimg.cn/20200405105405218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

Modbus 使用一个Big-Endian 表示地址和数据项。这意味着当发射多个字节时，首先发送最高有效位。例如：

> ```cobol
> 寄存器    数据
> 0x4001  0x1234
> ```

发送的第一字节为 0x12，第一字节为0x34。

## 3.数据模型

Modbus以一系列具有不同特征表格上的数据模型为基础，四个基本表格如下表所示：

![img](https://img-blog.csdnimg.cn/20200405110454403.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

对于基本表格中任何一项，协议都**允许单个地选择 65536 个数据项**，而且设计那些项的读写操作可以**越过多个连续数据项直到数据大小规格限制**，这个数据大小规格限制与**事务处理功能码**有关。

数据模型这个概念有限抽象，让我们通过以下两个示例加深理解。

### **示例1**：有 **4** **个独立块的设备**

示例描述：一个设备中的数据结构，含有**数字量和模拟量、输入量和输出量**。由于不同块中的数据不相关，每个块是相互独立。使用Modbus数据模型，按不同 Modbus功能码访问每个块，实现原理如下所示：

![img](https://img-blog.csdnimg.cn/20200405112544144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)

###  **示例2：仅有** **1** 个块的设备

示例描述：一个设备仅有 1 个数据块，使用Modbus数据模型，通过**几个Modbus功能码**可能得到**一个相同数据**，或者通过 16 比特访问或 1 个访问比特，实现原理如下所示：

![img](https://img-blog.csdnimg.cn/20200405112802559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM4MTA2OTIz,size_16,color_FFFFFF,t_70)