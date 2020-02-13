# JMeter（Java）

##### Apache JMeter是一款纯Java编写负载功能测试和性能测试开源工具软件，相比Loadrunner，JMeter小巧轻便免费，逐渐成为主流性能测试工具。

### JMeter脚本编写

#### 1. 添加线程组

右键点击“测试计划” -> "添加“ -> ”Threads(Users) -> "线程组“  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter1.PNG)  

可以设置线程组名称，线程数，准备时长（Ramp-Up Period(in seconds))循环次数，调度器等参数  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter2.PNG)  

🔺线程组参数：

- 线程数：虚拟用户数。一个虚拟用户占用一个进程或线程。设置多少虚拟用户数就是设置多少个线程数

- Ramp-Up Period(in seconds)准备时长：设置的虚拟用户数需要多长时间全部启动。如果线程数10，准备时长2，那么需要2秒钟启动10个线程，即每秒5个线程

- 循环次数：每个线程发送请求的次数。如果线程数10，循环次数100，那么每个线程发送100次请求，总请求数1000.如果勾选了”永远“，那么所有线程会一直发送请求，直到选择停止运行脚本

- Delay Thread creation until needed：直到需要时延迟线程的创建

- 调度器：设置线程组启动的开始时间和结束时间（配置调度器时，需要勾选循环次数为永远）

  持续时间（秒）：测试持续时间，会覆盖结束时间

  启动延迟（秒）：测试延迟启动时间，会覆盖启动时间

  启动时间：测试启动时间，启动延迟会覆盖它。当启动时间已过，手动只需测试时当前时间也会覆盖它

  结束时间：测试结束时间，持续时间会覆盖它

### 2. 添加HTTP请求

右键点击”线程组“ ->”添加“ -> "Sampler" -> ”HTTP请求"  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter3.PNG)  

我们采用如下接口：http://www.baidu.com/s?ie=utf-8&wd=jmeter性能测试  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter4.PNG)  

🔺HTTP请求参数：

- Web服务器

  协议：向目标服务器发送HTTP请求协议，可以是HTTP或HTTPS，默认为HTTP

  服务器名称或IP：HTTP请求发送的目标服务器名称或IP

  端口号：目标服务器的端口号，默认为80

- HTTP请求

  方法：发送HTTP请求的方法，可用方法包括GET、POST、HEAD、PUT、OPTIONS、TRACE、DELETE等

  路径：目标URL路径（URL中去掉服务器地址、端口及参数后剩余部分）

  Content encoding：编码方式，默认为ISO-8859-1编码，此处配置为utf-8

  同请求一起发送参数：在请求中发送的URL参数，用户可以将URL中所有参数设置在表中，表中每行一个参数，对应URL中的name=value，参数传入中文时需要勾选“编码”

### 3. 添加察看结果树

右键点击“线程组” ->“添加” ->“监听器” ->"察看结果树“  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter5.PNG)  

这时运行HTTP请求，修改响应数据格式为”HTML Source Formatted“：  
 
![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter6.PNG)  

### 4. 添加用户自定义变量

HTTP请求参数化，右键点击”线程组“ ->”添加“ ->"配置元件" ->"用户定义的变量"  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter7.PNG)  

新增一个参数wd，存放搜索词：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter8.PNG)  

在HTTP请求中使用该参数，格式为：${wd}：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter9.PNG)  

### 5. 添加断言

右键点击”HTTP请求“ ->"添加” -> "断言“ ->”响应断言“  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter10.PNG)  

校验返回的文本中是否包含搜索词，添加参数${wd}到要测试的模式中：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter11.PNG)  

### 6. 添加断言结果

右键点击”HTTP请求“ ->"添加” ->"监听器“ ->"断言结果"  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter12.PNG)  

运行，观察断言结果成功或失败：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter13.PNG)  

### 7. 添加聚合报告

右键点击”线程组“ ->”添加“ ->”监听器“ ->"聚合报告”，存放性能测试报告  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter14.PNG)  


### 执行性能测试

### 1. 配置线程组

配置性能测试相关参数：线程数、循环次数、持续时间等：配置并发用户数为10，持续时间60s：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter15.PNG)  

### 2. 执行测试

点击绿色箭头按钮启动测试，测试前点击小扫把按钮清除之前的调试结果  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter16.PNG)  



### 分析测试报告

性能测试执行完成后，打开聚合报告看到：  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeter17.PNG)  

🔺聚合报告参数：

- Label：每个JMeter的element（如HTTP Request）都有一个Name属性，显示Name属性的值
- #Samples：请求数——这个测试中一共发出多少个请求，如果模拟10个用户，每个用户迭代10次，这里显示100
- Average：平均响应时间——默认是单个Request的平均响应时间，当使用了Transaction Controller时，以Transaction为单位显示平均响应时间
- Median：中位数，即50%用户的响应时间
- 90%Line：90%用户的响应时间
- Min：最小响应时间
- Max：最大响应时间
- Error%：错误率——错误请求数/请求总数
- Throughput：吞吐量——默认是每秒完成的请求数（Request Per Second），当使用了Transaction Controller时，也可以表示类似LoadRunner得Transaction per Second数
- KB/sec：每秒从服务器端接收到的数据量，相当于LoadRunner中的Throughput/Sec

性能测试一般关注：#Samples请求数、Average平均响应时间、Min最小响应时间、Max最大响应时间、Error%错误率、Throughput吞吐量  

此外，也可以通过查看图形结果来分析测试报告  

### 一个例子  

Number of Threads（users）：10  

Ramp-Up period（seconds）：10  

Loop Count：2  

Duration（seconds）：100  

Web Server：Server Name of IP：www.baidu.com Protocol：https  

### 用表格查看结果  

![Image text](https://github.com/Pangxiaox/SoftwareTestingEngineerLearningNotes/blob/master/JMeter-pic/JMeterNew.PNG)




