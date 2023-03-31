# MQTT协议

## 1.MQTT协议概念

MQTT是基于Publish/Subscribe(**发布订阅**)模式的物联网通信协议
特点:

1. 简单易实现
2. 支持Qos(服务质量)
3. 报文小
   MQTT协议构建于TCP/IP协议之上

**发布订阅模式**:

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325142152999.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)
>
> 客户端只需要订阅这个主题
>
> 当有其他客户端向这个服务端发布消息时
>
> 这个客户端就可以收到这个消息

**请求响应模式**:

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325142609688.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)客户端向服务端发送请求，服务端收到请求后，向客户端返回响应

### 1.1 MQTT简介

MQTT(Message Queuing Telemetry Transport, 消息队列遥测传输协议)，是一种基于发布/订阅(publish/subscribe)模式的"轻量级"通讯协议，该协议构建于TCP/IP协议上，由IBM在1999年发布。

MQTT最大优点在于，可以以极少的代码和有限的带宽，为远程连接设备提过实时可靠的消息服务，作为一种低开销、低带宽占用的即时通讯协议，使其在物联网、小型设备、移动应用等方面有较广泛的应用。

MQTT是一个基于**客户端-服务器**的消息发布/订阅传输协议。MQTT协议是轻量、简单、开放和易于实现的，这些特点使它适用范围非常广泛。在很多情况下，包括受限的环境中，如:机器与机器（M2M）通信和物联网(loT)。其在，通过卫星链路通信传感器、偶尔拨号的医疗设备、智能家居、及一些小型化设备中已广泛使用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325143650897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

### 1.2 MQTT协议设计规范

- (1) 精简，不添加可有可无的功能；
- (2) 发布/订阅(Pub/Sub)模式，方便消息在传感器之间传递，解耦Client/Server模式，带来的好处在于**不必预先知道对方的存在**(ip/port), **不必同时运行**
- (3) 允许用户动态创建主题(不需要预先创建主题)，零运维成本；
- (4) 把传输量降到最低以提高传输效率
- (5) 把低带宽、高延迟、不稳定的网络等因素考虑在内；
- (6) 支持连续的会话保持和控制(**心跳协议**)
- (7) 理解客户端计算能力可能很低
- (8) 提供服务质量( quality of service level: QoS)管理:
- (9) 不强求传输数据的类型与格式，保持灵活性(指的使应用层业务数据)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325145247810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

### 1.3 MQTT协议主要特性

- (1）开放消息协议，简单易实现。
- (2）使用发布/订阅消息模式，提供一对多的消息发布，解除应用程序耦合。
- (3）对负载（协议携带的应用数据）内容**屏蔽**的消息传输。
- (4）基于TCP/IP网络连接,提供有序，无损，双向连接。
  主流的MQTT是基于TCP连接进行数据推送的，但是同样有基于UDP的版本，叫做MQTT-SN。这两种版本由于基于不同的连接方式，优缺点自然也就各有不同了。
  由于基于不同的连接方式，优缺点自然也就各有不同了。
- (5）消息服务质量(QoS）支持，可靠传输保证;
  有三种消息发布服务质量:
  QoSO:“**至多一次**”，消息发布完全依赖底层TCP/IP网络。会发生消息丢失或重复。这一级别可用于如下情况，环境传感器数据，丢失一次读记录无所谓，因为不久后还会有第二次发送。这一种方式主要普通APP的推送，倘若你的智能设备在消息推送时未联网，推送过去没收到，再次联网也就收不到了。
  QoS1:“**至少—次**”，确保消息到达，但消息重复可能会发生。
  QoS2:“**只有一次**”，确保消息到达一次。在一些要求比较严格的计费系统中，可以使用此级别。在计费系统中，消息重复或丢失会导致不正确的结果。这种最高质量的消息发布服务还可以用于即时通讯类的APP的推送，确保用户收到且只会收到一次。
- (6) 1字节固定报头，2字节心跳报文，最小化传输开销和协议交换，有效减少网络流量。
  这就是为什么在介绍里说它非常适合"在物联网领域，传感器与服务器的通信，信息的收集，要知道嵌入式设备的运算能力和带宽都相对薄弱，使用这种协议来传递消息再适合不过了。
- (7) 在线状态感知:使用Last Will和Testament特性通知有关各方客户端异常中断的机制。
  Last Will:即遗言机制，用于通知同一主题下的其他设备，发送遗言的设备已经断开了连接。
  Testament:遗嘱机制，功能类似于Last Will。

## 2.MQTT协议原理

### 2.1 MQTT协议实现方式

实现MQTT协议需要客户端和服务器端**通讯**完成， 在通讯过程中, MQTT协议中有**三**种身份: 

1. 发布者(Publish)
2. 代理(Broker)(服务器)
3. 订阅者(Subscribe)。

其中，消息的发布者和订阅者都是客户端，消息代理是服务器，消息发布者可以同时是订阅者。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210325152218242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

MQTT传输的消息分为: 

1. 主题(Topic) 
2. 负载(payload)两部分：

 (1) Topic: 可以理解为**消息的类型**，订阅者订阅(Subscribe)后，就会收到该主题的消息内容(payload)
 (2) payload: 可以理解为**消息的内容**，是指订阅者具体要使用的内容

### 2.2 网络传输与应用消息

MQTT会构建**底层网络传输**:
它将建立客户端到服务器的连接，提供两者之间的一个有序的、无损的、基于字节流的双向传输。
当应用数据通过MQTT网络发送时，MQTT会把与之相关的服务质量(QoS)和主题名(Topic)相关联。

### 2.3 MQTT客户端

一个使用MQTT协议的应用程序或者设备，它总是建立到服务器的网络连接。客户端可以:

- (1) 发布其他客户端可能会订阅的信息
- (2) 订阅其他客户端发布的消息
- (3) 退定或删除应用程序的消息
- (4) 断开与服务器的连接

### 2.4 MQTT服务器端

MQTT服务器以称为"消息代理"(Broker), 可以是一个应用程序或一台设备，它是位于消息发布者和订阅者之间，它可以:

- (1) 接受来自客户的网络连接;
- (2) 接受客户发布的应用信息
- (3) 处理来自客户端的订阅和退订请求
- (4) 向订阅的客户转发应用程序消息

### 2.5 发布/订阅、主题、会话

MQTT是基于**发布(Publish)/订阅(Subscribe)**模式来进行通信及数据交换的，与HTTP的**请求(Request)/应答(Response)**的模式有本质的不同

**订阅者(Subscriber)**会向 **消息服务器(Broker)**订阅一个**主题(Topic)**。
成功订阅后，消息服务器会将该主题下的消息转发给所有订阅者

主题(Topic)以’**/**‘为分隔符区分不同的层级，包含通配符’+’ 或 ‘#’的主题又称为**主题过滤器(Topic Filters);** 不含通配符的成为**主题名(Topic Names)** 例如:

> ```tex
> chat/ room/1
> 
> sensor/10/temperature
> 
> sensor/+/temperature
> 
> $SYS/broker/metrics/packets/received
> 
> $SYS/broker/metrics/#
> ```

'+' : 表示通配一个层级， 例如a/+，匹配a/x, a/y

‘#’ : 表示通配多个层级，例如a/#，匹配a/x, a/b/c/d

> 注: ‘+’ 通配一个层级，‘#’ 通配多个层级(必须在末尾)

发布者(Publisher)只能向主题名发布消息，订阅者(Subscriber)则可以通过订阅主题过滤器来通配多个主题名称

**会话(Session)**

每个客户端与服务器建立连接后就是一个会话，客户端和服务器之间有状态交互。会话存在于一个网络之间，也可能在客户端和服务器之间跨越多个连续的网络连接。

### 2.6 MQTT协议中的方法

MQTT协议中定义了一些方法(也被称为动作)，来于表示对确定资源所进行操作。这个资源可以代表**预先存在的数据或动态生成数据**，这取决于服务器的实现。通常来说，资源指服务器上的文件或输出。主要方法有:

- (1) CONNECT: 客户端连接到服务器
- (2) CONNACK: 连接确认
- (3) PUBLISH: 发布消息
- (4) PUBACK: 发布消息确认
- (5) PUBREC: 发布的消息已接收
- (6) PUBREL: 发布的消息已释放
- (7) PUBCOMP: 发布完成
- (8) SUBSCRIBE: 订阅请求
- (9) SUBACK: 订阅确认
- (10) UNSUBSCRIBE: 取消订阅
- (11) UNSUBACK: 取消订阅确认
- (12) PINGREQ: 客户端发送心跳
- (13) PINGRESP: 服务端心跳响应
- (14) DISCONNECT: 断开连接
- (15) AUTH: 认证

## 3.MQTT协议数据包结构

1. 固定头(Fixed header)
2. 可变头(Variable header)
3. 消息体(payload)

在MQTT协议中，一个MQTT数据包由: 以上三部分构成。

MQTT数据包结构如下:

![](https://img-blog.csdnimg.cn/20210327155246427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

(1) 固定头(Fixed header)。
	存在于**所有**MQTT数据包中，表示**数据包类型及数据包的分组类标识**，如连接，发布，订阅，心跳等。其中固定头是**必须**的，所有类型的MQTT协议中，都必须包含固定头。

(2) 可变头(Variable header)。
	存在于**部分**MQTT数据包中，数据包类型决定了可变头是否存在及其具体内容。可变头部不是可选的意思，而是指这部分在**有些协议类型中存在，在有些协议中不存在。**

(3) 消息体（Payload)。
	存在于**部分**MQTT数据包中，表示客户端**收到的具体内容**。与可变头一样,在有些协议类型中有消息内容，有些协议类型中没有消息内容。

### 3.1 固定头(Fixed Header)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210327160210282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

固定头存在于所有MQTT数据包中
固定头包含两部分内容：

1. **首字节(字节1)**、
2. **剩余消息报文长度(从第二个字节开始，长度为1-4字节)**

剩余长度是**当前包中剩余内容长度的字节数**，包括变量头和有效负载中的数据)。剩余长度不包含用来编码剩余长度的字节。
剩余长度使用了一种可变长度的结构来编码，这种结构使用单一字节表示0-127的值。大于127的值如下处理。每个字节的低7位用来编码数据，最高位用来表示是否还有后续字节。因此每个字节可以编码128个值，再加上一个标识位。剩余长度最多可以用四个字节来表示。

**数据包类型**
位置: 第一个字节(Byte 1)中的7-4个bit位(Bit[7-4]),标识4位无符号值

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210329141718919.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

**标志位**
位置: 第一个字节中的0-3个bit位(Bit[3-0])。意思是字节位Bit[3-0]用作报文的标识。
首字节的低4位(bit3-bit0)用来表示某些报文类型的控制字段，实际上只有少数报文类型有控制位，如下图:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210329144030953.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

(1) : 其中Bit[3]为DUP字段，如果该值为1，表明这个数据包是一条**重复**的消息；否则该数据包就是第一次发布的消息
(2) : Bit[2-1]为QoS字段:
如果Bit 1 和 Bit 2都为0，表示QoS 0: 至多一次；
如果Bit 1为1, 表示QoS 1: 至少一次；
如果Bit 2为1，表示QoS 2：只有一次；
如果同时将Bit 1 和 Bit 2都设置成1，那么客户端或服务器认为这是一条非法的消息，会关闭当前连接。

**MQTT消息QoS**

MQTT发布消息服务质量保证(QoS)不是端到端的，是客户端与服务端之间的。订阅者收到MQTT消息的QoS级别，最终取决于发布消息的QoS和主题订阅的QoS

QoS: 服务质量是指客户端和服务端之间的服务质量

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210329173039745.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

**QoS消息订阅(至多一次):**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330090015856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

**QoS1消息发布订阅(至少一次)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330090345904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

**QoS2消息发布订阅(只有一次)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330090900466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

### 3.2 可变头(Variable Header)

可变头的意思是可变化的消息头部。有些报文类型包含可变头部有些报文则不包含。可变头部在固定头部和消息内容之间，其内容根据报文类型不同而不同.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330094020704.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

**协议版本**
位无符号值表示客户端的版本等级。3.1.1版本
**MQTT会话**
MQTT客户端向服务器发起CONNECT请求时，可以通过’Clean Session’标志设置会话。
‘Clean Session’设置为**0**，表示创建一个持久会话，在客户端断开连接时，会话仍然保持并保存离线消息，直到会话超时注销。
‘Clean Session’设置为**1**，表示创建一个新的临时会话，在客户端断开时，会话自动销毁
**Will Flag /Will QoS/Will Retain**
Will Flag : 遗言标志位
Will QoS: 遗言的消息质量
Will Retain: 遗言的保持状态
**Keep Alive timer(心跳时长)**
心跳协议:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330112114341.png)

Keep Alive timer 最大时间间隔

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330113043365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

### 3.3 消息体(Payload)

有些报文类型是包含Payload的,Payload的意思是消息载体的意思
如PUBLISH的Payload就是指消息内容(**应用程序发布的消息内容**)。而CONNECT的Payload则包含Client Identifier, Will Topic, Will Message, Username, Password等信息

包含payload的报文类型如下:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210404104011495.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzMDYyMTA0,size_16,color_FFFFFF,t_70)

Payload是消息内容，也只在某些报文类型中出现，其内容和格式也根据报文类型不同而不同。
具体不同报文类型所包含的不同Payload。