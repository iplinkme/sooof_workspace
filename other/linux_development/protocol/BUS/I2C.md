# I2C

###### 0X01 --- Hardware 说明

I2C总线是由Philips公司开发的一种简单、双向二线制同步串行总线。它只需要两根线即可在连接于总线上的器件之间传送信息。

主器件用于启动总线传送数据，并产生时钟以开放传送的器件，此时任何被寻址的器件均被认为是从器件．在总线上主和从、发和收的关系不是恒定的，而取决于此时数据传送方向。如果主机要发送数据给从器件，则主机首先寻址从器件，然后主动发送数据至从器件，最后由主机终止数据传送；如果主机要接收从器件的数据，首先由主器件寻址从器件．然后主机接收从器件发送的数据，最后由主机终止接收过程。在这种情况下．主机负责产生定时时钟和终止数据传送

###### 0X02 --- 工作原理

SDA（串行数据线）和SCL（串行时钟线）都是双向I/O线，接口电路为开漏输出．需通过上拉电阻接电源VCC．当总线空闲时．两根线都是高电平，连接总线的外同器件都是CMOS器件，输出级也是开漏电路．在总线上消耗的电流很小，因此，总线上扩展的器件数量主要由电容负载来决定，因为每个器件的总线接口都有一定的等效电容．而线路中电容会影响总线传输速度．当电容过大时，有可能造成传输错误．所以，其负载能力为400pF，因此可以估算出总线允许长度和所接器件数量。
主器件用于启动总线传送数据，并产生时钟以开放传送的器件，此时任何被寻址的器件均被认为是从器件．在总线上主和从、发和收的关系不是恒定的，而取决于此时数据传送方向。如果主机要发送数据给从器件，则主机首先寻址从器件，然后主动发送数据至从器件，最后由主机终止数据传送；如果主机要接收从器件的数据，首先由主器件寻址从器件．然后主机接收从器件发送的数据，最后由主机终止接收过程。在这种情况下．主机负责产生定时时钟和终止数据传送。

###### 0X02 --- 特征

I2C总线特点可以概括如下：
(1)在硬件上，I2C总线只需要一根数据线和一根时钟线两根线，总线接口已经集成在芯片内部，不需要特殊的接口电路，而且片上接口电路的滤波器可以滤去总线数据上的毛刺．因此I2C总线简化了硬件电路PCB布线，降低了系统成本，提高了系统可靠性。因为I2C芯片除了这两根线和少量中断线，与系统再没有连接的线，用户常用IC可以很容易形成标准化和模块化，便于重复利用。
(2)I2C总线是一个真正的多主机总线，如果两个或多个主机同时初始化数据传输，可以通过冲突检测和仲裁防止数据破坏，每个连接到总线上的器件都有唯一的地址，任何器件既可以作为主机也可以作为从机，但同一时刻只允许有一个主机。数据传输和地址设定由软件设定，非常灵活。总线上的器件增加和删除不影响其他器件正常工作。
(3)I2C总线可以通过外部连线进行在线检测，便于系统故障诊断和调试，故障可以立即被寻址，软件也利于标准化和模块化，缩短开发时间。
(4)连接到相同总线上的IC数量只受总线最大电容的限制，串行的8位双向数据传输位速率在标准模式下可达100Kbit/s，快速模式下可达400Kbit/s，高速模式下可达3．4Mbit/s。
(5)总线具有极低的电流消耗．抗高噪声干扰，增加总线驱动器可以使总线电容扩大10倍，传输距离达到15m；兼容不同电压等级的器件，工作温度范围宽。

###### 0X03 --- 特征

* 字节格式

发送到SDA 线上的每个字节必须为8 位，每次传输可以发送的字节数量不受限制。每个字节后必须跟一个响应位。首先传输的是数据的最高位（MSB），如果从机要完成一些其他功能后（例如一个内部中断服务程序）才能接收或发送下一个完整的数据字节，可以使时钟线SCL 保持低电平，迫使主机进入等待状态，当从机准备好接收下一个数据字节并释放时钟线SCL 后数据传输继续。

* 应答响应

数据传输必须带响应，相关的响应时钟脉冲由主机产生。在响应的时钟脉冲期间发送器释放SDA 线（高）。
在响应的时钟脉冲期间，接收器必须将SDA 线拉低，使它在这个时钟脉冲的高电平期间保持稳定的低电平。
通常被寻址的接收器在接收到的每个字节后，除了用CBUS 地址开头的数
据，必须产生一个响应。当从机不能响应从机地址时（例如它正在执行一些实时函数不能接收或发送），从机必须使数据线保持高电平，主机然后产生一个停止条件终止传输或者产生重复起始条件开始新的传输。
如果从机接收器响应了从机地址，但是在传输了一段时间后不能接收更多数据字节，主机必须再一次终止传输。这个情况用从机在第一个字节后没有产生响应来表示。从机使数据线保持高电平，主机产生一个停止或重复起始条件。
如果传输中有主机接收器，它必须通过在从机发出的最后一个字节时产生一个响应，向从机发送器通知数据结束。从机发送器必须释放数据线，允许主机产生一个停止或重复起始条件。

I2C总线数据传输和应答
I2C总线数据传输和应答

* 时钟同步

所有主机在SCL线上产生它们自己的时钟来传输I2C总线上的报文。数据只在时钟的高电平周期有效，因此需要一个确定的时钟进行逐位仲裁。
时钟同步通过线与连接I2C 接口到SCL 线来执行。这就是说SCL 线的高到低切换会使器件开始数它们的低电平周期，而且一旦器件的时钟变低电平，它会使SCL 线保持这种状态直到到达时钟的高电平。但是如果另一个时钟仍处于低电平周期，这个时钟的低到高切换不会改变SCL 线的状态。因此SCL 线被有最长低电平周期的器件保持低电平。此时低电平周期短的器件会进入高电平的等待状态。
当所有有关的器件数完了它们的低电平周期后，时钟线被释放并变成高电平。之后，器件时钟和SCL线的状态没有差别，而且所有器件会开始数它们的高电平周期。首先完成高电平周期的器件会再次将SCL线拉低。
这样产生的同步SCL 时钟的低电平周期由低电平时钟周期最长的器件决定，而高电平周期由高电平时钟周期最短的器件决定。
