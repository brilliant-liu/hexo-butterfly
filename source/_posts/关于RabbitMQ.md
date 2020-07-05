--- 
title: '关于RabbitMQ'
date: 2020-06-19 15:05:27 
tags:
  - 教程
  - MQ
categories:
  - [MQ] 
cover: 'http://doofuu.com/upload/2019/06/21/5d0cd08e17474.jpg'
--- 
 
# 什么是MQ  
消息队列（Message Queue）,是一种跨进程，异步通讯的通讯机制，用于上下游传递消息，异步解耦、流量削峰、数据分发，日志收集等。 
 
# 什么是AMQP 
AMQP，即Advanced Message Queuing Protocol，高级消息队列协议，具有现代特征的二进制协议，是应用层协议的一个开放标准，为面向消息的中间件设计。 
AMQP的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。   
![image.png](https://i.loli.net/2020/06/26/JO6rIDHmXTvVlsu.png)

## AMQP的核心概念
+ Server: 又称Broker,接受客户端的连接，实现AMQP的实体服务
+ Connection: 连接，应用程序域Broker的网络连接
+ Channel: 网络信道，几乎所有的操作都早Channel中进行，Channel是进行消息读写的通道。客户端可建立多个Channel，每个Channel代表一个会话任务。
+ Message: 消息，服务器和应用程序之间传递的数据。由Properties和Body（消息内容）组成。
+ Virtual Host:虚拟地址，用于进行逻辑隔离，最上层的消息路由。一个Virtual Host内可以有若干个Exchange和Queue,同一个Virtual Host里面不能有名称相同的Exchange或者Queue
+ Exchange: 交换机，接受消息，根据路由键转发消息到绑定的队列
+ Binding: Exchange和Queue之间的虚拟连接，binding中可以包含routing key
+ Routing key: 一个路由规则，虚拟机可以用它来确定如何路由一个特定的消息
+ Queue: 也称Message Queue,消息队列，保存消息并将他们转发给消费者。

# MQ对比  

# RabbitMQ
## RabbitMQ的优点
+ 开源、性能优秀、稳定性好
+ 能提供可靠的消息投递模式（confirm）,返回模式（return）
+ 能与springAMQP完美整合，aip丰富
+ 集群模式丰富，表达式配置，HA模式，镜像队列模型
+ 保证数据不丢失的前提下做到高可靠性，可用性。

## RabbitMQ的高性能的原因
Erlang语音最初在于交换机领域的架构模式，使得RabbitMQ在broker之间进行数据交互的性能非常优秀，Erlang语音有着和原生的Socket一样的延迟。

## RabbitMQ的整体架构

![image.png](https://i.loli.net/2020/06/26/Oq8pz5WbT7xJgDK.png)  

## 基本命令行操作
+ rabbitmqctl stop_app : 关闭应用
+ rabbitmqctl start_app : 启动应用
+ rabbitmqctl status : 节点状态
+ rabbitmqctl add_user username password : 添加用户
+ rabbitmqctl list_users ： 列出所有用户
+ rabbitmqctl delete_user username : 删除用户
+ rabbitmqctl clear_permissions -p vhostpath username: 清除用户权限
+ rabbitmqctl list_user_permissions username: 列出用户权限
+ rabbitmqctl rabbitmqctl change_password username newpassword: 修改密码
+ rabbitmqctl set_permissions -p vhostpath username ".\*" ".\*" ".\*" :设置用户权限
+ rabbitmqctl add_vhost vhostpath: 创建虚拟主机
+ rabbitmqctl list_vhosts: 列出所有的虚拟主机
+ rabbitmqctl list_permissions -p vhostpath:列出虚拟主机上的所有权限
+ rabbitmqctl delete_vhost vhostpath :删除虚拟主机
+ rabbitmqctl list_queues : 查看所有队列的信息
+ rabbitmqctl -p vhostpath purge_queue blue: 清楚队列里的消息
+ ...

## 高级操作
+ rabbitmqctl reset ：移除所有数据，要在rabbitmqctl stop_app之后使用。
+ rabbitmqctl join_cluster <clusternode> \[--ram] :组成集群命令
+ rabbitmqctl cluster_status : 查看集群状态
+ rabbitmqctl change_cluster_node_type disc | ram : 修改集群节点的存储形式
+ rabbitmqctl forget_cluster_node \[--offline] : 忘记节点（摘除节点）
+ rabbitmqctl rename_cluster_node oldnode1 newnode \[oldnode2] \[newnode2]:修改节点名称

## Exchange交换机
属性：  
+ name： 交换机的名称
+ Type: 交换机的类型：direct、topic、fanout、headers
+ Durability： 是否需要持久化，true为持久化
+ Auto Delete: 当最后一个绑定到Exchange上的队列删除后，自动删除该Exchange.
+ Internal：当前Exchange是否用于RabbitMQ内部使用，默认false.
+ Arguments: 扩展参数，用用于扩展AMQP协议自定义化使用。

### Direct Exchange
所有发送到Direct Exchange的消息被转发到RouteKey中指定的Queue。
注意：Direct模式使用RabbitMQ自带的Exchange:default Exchange时，不需要将Exchange进行任何的绑定操作，route key必须完全匹配才会被队列接受，否则消息会被抛弃。


### Topic Exchange
所有发送到Topic Exchange的消息都被转发到所有关心Routekey中指定的Topic的Queue上。
Exchange 将Route key和某个Topic进行模糊匹配，此时队列需要绑定一个topic

模糊匹配规则：
+ '#':匹配一个或者多个词  
+ '*':匹配一个词  
> 例如： "log.#":可以匹配到"log.info.icop";"log.*"只能匹配到”log.error“

### Fanout Exchange
不处理路由键，只需要简单的将队列绑定到交换机上，发送到交换机的消息都会被转发到与该交换机绑定的所有队列上。
Fanout交换机，其转发消息时最快的。

### Binding-绑定
Exchange和Exchange、Queue之间的连接关系。binding中可以包含Routingkey或者参数。

### Queue-消息队列
属性：  
+ 持久化(Durability): durable:是，Transient:否
+ 自动删除（Auto Delete）: 如果选yes,当最后一个监听被移除之后，该Queue会被自动删除。

### Message消息
本质上是一段数据，由Properties和payload(body)组成。  
常用属性：
+ delivery mode: 可以做内存级别的持久化
+ headers： 自定义属性
+ content_type：消息格式
+ content_encoding：编码
+ priority: 优先级
+ correlation_id: 唯一的ID
+ reply_to:
+ expiration： 消息的过期时间，超时自动删除
+ ...

### 消息的投递机制

#### 保障消息的100%的投递成功
1. 生产端的可靠性投递
+ 保障消息的成功发送
+ 保障MQ节点的成功接受
+ 发送端收到MQ节点（Broker）确认应答
+ 完善的消息补偿机制

解决方案：
+ 消息落库，对消息状态进行更改控制  

![image.png](https://i.loli.net/2020/06/26/9hpkPOmMRt8dHrs.png)  

+ 消息的延迟投递，做二次确认，回调检查  

![image.png](https://i.loli.net/2020/06/26/s6b1qIBmQhe5MaP.png)  

#### 消息的幂等性
消费端的幂等性保障操作 
+ 唯一ID + 指纹码机制，利用数据库主键去重  
+ 利用redis的原子性去实现  

#### Confirm确认消息
消息的确认，是指生产者投递消息后，如果broker收到消息，则会给生产者一个应答。
生产者进行接受应答，用来确定这条消息是否正常的发送到Broker.

![image.png](https://i.loli.net/2020/06/26/dX9MRiNthJmCZoO.png)

如何实现Confirm确认消息：  
1. 在channel上开启确认模式：`channel.confirmSelect()`
2. 在channel上添加监听：`addConfirmListener`,监听成功和失败的返回结果，根据具体的结果对消息进行重新发送，或日志记录等后续操作。

#### return消息机制
Return Listener 用于处理一些不可路由的消息。在某些情况下如果我们在发送消息的时候，当前exchange不存在或者指定路由key路由不到的时候，我们如果需要监听这个消息，就需要使用Return Listener.  

关键配置：  
Mandatory: 如果为True,监听器接收到路由不可达的消息，然后进行后续处理，如果为false,则broker端会自动删除该消息。默认为false.

#### 消费端的限流
RabbitMQ提供了一种qos(服务质量保证)功能，即在非自动确认消息的前提下，如果一定数目的消息（通过基于consume或者channel设置Qos的值）未被确认前，不进行消费新的消息。
` void BasicQos(unit prefetchSize,ushort prefetchCount,bool global);`
prefetchCount: 会告诉RabbitMQ不要同时给消费者推送多于N个消息，即一但有N个消息还没有ack，则consumer将block掉，一直到有消息ack.  
global: true\false是否将上面的设置应用于channel,就是上面的限制是channel级别还是consumer级别的。  

#### 消费端的ACK与重回队列  
消费端的手工ACK和NACK  
+ 消费端进行消费的时候，如果由于业务异常我们可以根据日志进行记录，然后进行补偿。
+ 如果服务器宕机等严重问题，那我们需要手动进行ACK保障消费端消费成功。
消费端的重回队列  
消费端重回队列是为了让没有处理成功的消息，把消息重新传给broker。一般实际应用中都会关闭重回队列，也就是设置为false.

#### TTL队列/消息
TTL(time to live) :生存时间，在消息发送的时候指定消息的过期时间，从消息进入消息队列开始计时，如果超过了队列的超时配置，则消息会被自动清楚。

#### 死信队列
死信队列：DLX(Dead-letter-Exchange)
利用DLX,当消息在一个队列中变成死信之后，它可能被重新publish到另外一个Exchange，这个Exchange就是DLX.

消息成为死信的情况：  
+ 消息被拒绝（basic.reject/basic.nack）并且requeue = false;
+ 消息TTL过期
+ 队列达到最大的长度

DLX也是一个正常的Exchange,它能在任何队列上被指定，实际上就是设置的每个队列的属性。
当这个队列中存在死信时，RabbitMQ就会自动将这个消息重新发布到设置的Exchange上去，进而被路由到另一个队列。
可以监听这个队列中的消息做响应的处理。

死信队列的设置：
1. Exchange: dlx.exchange
2. Queue: dlx.queue
3. RoutingKey: #
4. 然后正常生命交换机，队列，绑定，只是需要在队列上添加一个参数：`arguments.put("x-dead-letter-exchange","dlx.exchange")`

### RabbitMQ的高级应用
#### RabbitMQ整合spring AMQP
重点组件：  
+ RabbitAdmin： 
    > 其可以提供很好的操作RabbitMQ的API,在spring中直接进行注入即可，需要注意的时autoStartUp必须设置为true,否正spring容器不会加载RabbitAdmin类。 
+ SpringAMQP声明：
+ RabbitTemplate (消息模板) 
    > 进行发送消息的关键类，提供了丰富的发送消息的API,包括可靠性投递的方法，回调监听消息接口ConfirmCallback,返回值确认接口ReturnCallback等。
+ SimpleMessageListenerContainer:
    > 简单的消息监听，对于消费者的配置项，可以用来监听队列，自动启动，自动声明功能，，设置事务等，设置消费者数量，设置消息的确认模式，标签策略，是否重回队列，异常捕捉，支持运行动态设置等。                           
+ MessageListenerAdapter：
    > 消息监听适配器：
+ MessageConverter：
    > 消息转化器：json转化器（Jackson2JsonMessageConverter）,java对象转化器（DefaultJackson2JavaTypeMapper）,自定义二进制转化器
            
             


#### RabbitMQ整合SpringBoot实战
+ @RabbitListener，消费端监听，是一个组合注解，里面配置@QueueBinding,@Queue,@Exchange
+ @RabbitHandler,消费方法监听


# 写在最后：
> 本内容主要针对RabbitMQ的重要理论，使用场景，设计的思想。特别感谢UP主:snakezzw的视频，参考网址：https://www.bilibili.com/video/BV1ck4y1r77k


