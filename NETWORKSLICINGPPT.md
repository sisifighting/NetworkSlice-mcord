# NetworkSlice-mcordppt

本文是针对mcord公开的网络切片原理与方案进行了整理。

PPT地址：https://opencord.org/wp-content/uploads/2018/01/Network-Slicing-and-Private-LTE.pdf
视频地址：https://www.youtube.com/watch?v=0dBLNgnF_nA&list=PLCnPGaNt7C5f01BYKyP9TmrrVSl5CIicH&index=4

5G不仅仅是LTE吞吐量的扩展，他也为目前4G没有覆盖的垂直业务提供用户体验，这些用例将需要新类型的连接服务，需要具备高度可扩展能力，并且在速度、容量、安全性、可靠性、可用性、延迟时间和电池寿命方面通过可编程实现。5G系统将建成以满足一系列的性能的目标。5G系统将建成可以满足一系列性能
指标的通信系统，因此，各种不同的服务需求可以部署在一个单一的基础设施上。

网络切片是一种在通用基础设施上动态创建和管理功能独立的虚拟化网络的机制。

网络切片需要思考的主要问题：
1. 决定虚拟网络资源
2. 如何控制虚拟网络资源
3. 切片特定的额外能力
4. 怎么控制切片
## RAN切片

1. 虚拟网络资源：TIME-FREQUENCY-SPACE BLOCKS(LTE的资源块)
2. 控制虚拟网络资源：切片到虚拟资源块的动态映射
3. 切片可定制能力：上下行定时切换管理；接入控制策略；频谱利用；双工策略；链路聚合策略；SON策略
4. 切片管理：资源管理；能力管理；用户管理；流管理

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/RANSlice.PNG)

ProgRAN架构

SDN控制eNB到MME的RAN，实现3GPP CP面和UP面的协议。其中SD-RAN 控制器可以提供北向接口，实现外界控制，南向通过开源接口与本地控制代理交互，实现从PHY到RRC层的控制，其中每一层的元素都可以并行动态修改。

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/ProgRAN%20%E6%9E%B6%E6%9E%84.PNG)

资源模块分配：资源模块分配有三种方式，一种是静态分配，为切片分配固定的资源块数量，不能改变；二是每个切片中的资源块数量可以动态分配，实现动态扩缩容；三是每个用户可以根据需求量实现资源块的动态扩缩。

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/RAN%E5%88%87%E7%89%87%E8%B5%84%E6%BA%90%E5%9D%97.PNG)

ProgRAN操作

ProgRAN控制器可以集成切片APP，实现对ProgRAN的切片控制，每个切片通过接入控制、切换控制、链路聚合、上下行编排、资源分配与映射等模块实现RAN，这些切片共享同一块物理资源，通过对物理资源的资源映射和逻辑隔离，分隔出每个切片所需的虚拟资源块，实现RAN切片的分离。

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/progranOperation.PNG)

C-RAN ProgRAN操作

接入控制和切换控制被虚拟化到C-RAN，北向通过切片APP控制，南向需要配置，为其分配对应的切片。


![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/CRAN.PNG)

RAN切片需要包含的信息
1. 需要接入的eNB
2. 需要服务的用户
3. 切片的时间
4. 切片特定功能：上述ProgRAN架构中提到的组件控制器和策略的选择
5. 资源分配的最大值和分配策略

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/RANSliceProfile.PNG)

## 核心网切片

1. 虚拟资源：计算、存储以及运行在上面的VNF
2. 资源管理：云管理编排
3. 切片的隔离度：独立还是共享（有的切片之间会共享网元）
4. 切片控制：核心网切片作为一个服务工作，切片之间通过SDN控制，用户/流管理

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/CORE.PNG)

RAN在选择核心网切片时，通过RAN forwarder、无线链路控制器、及CN forwarder确定到指定一个或多个核心网切片，其中Prog控制器对核心网切片进行解析选择。

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/RANTOCore.PNG)

核心网切片架构：可以独立也可以共享网络功能。

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/CN.PNG)




## 传输网切片

1. 虚拟资源：链路、端口、代理、带宽
2. 资源控制：SDN控制
3. QoS整形，隔离，安全保障
4. 网络切片作为服务通过服务操作系统

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/transport%20slicing.PNG)

## LWIP链路聚合示例

在UE和eNB通信中，数据采用IP包的形式传输，其中数据通过LWIP实现从eNB传输到WiFi接入器中，从而实现通过WLAN进行数据传输。而信令部分通过LTE网络传输

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/LinkAGG.PNG)

在切片时，需要选择纯LTE切片，LTE与WIFI共享切片以及纯WIFI切片

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/LinkAggSlice.PNG)

在模拟系统中搭建的系统示例如下：

![image](https://github.com/sisifighting/NetworkSlice-mcord/blob/master/image/AggBuild.PNG)

