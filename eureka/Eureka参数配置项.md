# Eureka参数配置项详解
Eureka涉及到的参数配置项数量众多，它的很多功能都是通过参数配置来实现的，  
了解这些参数的含义有助于我们更好的应用Eureka的各种功能。  
&nbsp;&nbsp;

## Eureka客户端配置
* 1、 RegistryFetchIntervalSeconds  
从eureka服务器注册表中获取注册信息的时间间隔（s），默认为30秒

* 2、 InstanceInfoReplicationIntervalSeconds  
复制实例变化信息到eureka服务器所需要的时间间隔（s），默认为30秒

* 3、 InitialInstanceInfoReplicationIntervalSeconds  
最初复制实例信息到eureka服务器所需的时间（s），默认为40秒

* 4、 EurekaServiceUrlPollIntervalSeconds  
询问Eureka服务url信息变化的时间间隔（s），默认为300秒

* 5、 ProxyHost  
获取eureka服务的代理主机，默认为null

* 6、 ProxyPort  
获取eureka服务的代理端口, 默认为null

* 7、 ProxyUserName
获取eureka服务的代理用户名，默认为null

* 8、 ProxyPassword  
获取eureka服务的代理密码，默认为null

* 9、 GZipContent  
eureka注册表的内容是否被压缩，默认为true，并且是在最好的网络流量下被压缩

* 10、 EurekaServerReadTimeoutSeconds  
eureka需要超时读取之前需要等待的时间，默认为8秒

* 11、 EurekaServerConnectTimeoutSeconds  
eureka需要超时连接之前需要等待的时间，默认为5秒

* 12、 BackupRegistryImpl  
获取实现了eureka客户端在第一次启动时读取注册表的信息作为回退选项的实现名称

* 13、 EurekaServerTotalConnections  
 eureka客户端允许所有eureka服务器连接的总数目，默认是200

* 14、 EurekaServerTotalConnectionsPerHost  
eureka客户端允许eureka服务器主机连接的总数目，默认是50

* 15、 EurekaServerURLContext  
表示eureka注册中心的路径，如果配置为eureka，则为http://x.x.x.x:x/eureka/，  
在eureka的配置文件中加入此配置表示eureka作为客户端向注册中心注册，从而构成eureka集群。  
此配置只有在eureka服务器ip地址列表是在DNS中才会用到，默认为null

* 16、 EurekaServerPort  
获取eureka服务器的端口，此配置只有在eureka服务器ip地址列表是在DNS中才会用到。默认为null

* 17、 EurekaServerDNSName  
获取要查询的DNS名称来获得eureka服务器，此配置只有在eureka服务器ip地址列表是在DNS中才会用到。默认为null

* 18、 UseDnsForFetchingServiceUrls  
eureka客户端是否应该使用DNS机制来获取eureka服务器的地址列表，默认为false

* 19、 RegisterWithEureka  
实例是否在eureka服务器上注册自己的信息以供其他服务发现，默认为true

* 20、 PreferSameZoneEureka  
实例是否使用同一zone里的eureka服务器，默认为true，理想状态下，eureka客户端与服务端是在同一zone下

* 21、 AllowRedirects  
服务器是否能够重定向客户端请求到备份服务器。 如果设置为false，服务器将直接处理请求，  
如果设置为true，它可能发送HTTP重定向到客户端。默认为false

* 22、 LogDeltaDiff  
是否记录eureka服务器和客户端之间在注册表的信息方面的差异，默认为false

* 23、 DisableDelta(*)  
默认为false

* 24、 fetchRegistryForRemoteRegions  
eureka服务注册表信息里的以逗号隔开的地区名单，如果不这样返回这些地区名单，则客户端启动将会出错。默认为null

* 25、 Region  
获取实例所在的地区。默认为us-east-1

* 26、 AvailabilityZones  
获取实例所在的地区下可用性的区域列表，用逗号隔开。

* 27、 EurekaServerServiceUrls  
Eureka服务器的连接，默认为http：//XXXX：X/eureka/,但是如果采用DNS方式获取服务地址，则不需要配置此设置。

* 28、 FilterOnlyUpInstances（*）  
是否获得处于开启状态的实例的应用程序过滤之后的应用程序。默认为true

* 29、 EurekaConnectionIdleTimeoutSeconds  
Eureka服务的http请求关闭之前其响应的时间，默认为30 秒

* 30、 FetchRegistry  
此客户端是否获取eureka服务器注册表上的注册信息，默认为true

* 31、 RegistryRefreshSingleVipAddress  
此客户端只对一个单一的VIP注册表的信息感兴趣。默认为null

* 32、 HeartbeatExecutorThreadPoolSize(*)  
心跳执行程序线程池的大小,默认为5

* 33、 HeartbeatExecutorExponentialBackOffBound(*)  
心跳执行程序回退相关的属性，是重试延迟的最大倍数值，默认为10

* 34、 CacheRefreshExecutorThreadPoolSize(*)  
执行程序缓存刷新线程池的大小，默认为5

* 35、 CacheRefreshExecutorExponentialBackOffBound  
执行程序指数回退刷新的相关属性，是重试延迟的最大倍数值，默认为10

* 36、 DollarReplacement  
eureka服务器序列化/反序列化的信息中获取“$”符号的的替换字符串。默认为“_-”

* 37、 EscapeCharReplacement  
eureka服务器序列化/反序列化的信息中获取“_”符号的的替换字符串。默认为“__”

* 38、 OnDemandUpdateStatusChange（*）  
如果设置为true,客户端的状态更新将会点播更新到远程服务器上，默认为true

* 39、 EncoderName  
这是一个短暂的编码器的配置，如果最新的编码器是稳定的，则可以去除，默认为null

* 40、 DecoderName  
这是一个短暂的解码器的配置，如果最新的解码器是稳定的，则可以去除，默认为null

* 41、 ClientDataAccept（*）  
客户端数据接收

* 42、Experimental（*）  
当尝试新功能迁移过程时，为了避免配置API污染，相应的配置即可投入实验配置部分，默认为null  
&nbsp;&nbsp;

## 实例微服务端配置
* 1、 InstanceId  
此实例注册到eureka服务端的唯一的实例ID,其组成为  
${spring.application.name}:${spring.application.instance_id:${random.value}}

* 2、 Appname  
获得在eureka服务上注册的应用程序的名字，默认为unknow

* 3、 AppGroupName  
获得在eureka服务上注册的应用程序组的名字，默认为unknow

* 4、 InstanceEnabledOnit（*）  
实例注册到eureka服务器时，是否开启通讯，默认为false

* 5、 NonSecurePort  
获取该实例应该接收通信的非安全端口。默认为80

* 6、 SecurePort  
获取该实例应该接收通信的安全端口，默认为443

* 7、 NonSecurePortEnabled  
该实例应该接收通信的非安全端口是否启用，默认为true

* 8、 SecurePortEnabled  
该实例应该接收通信的安全端口是否启用，默认为false

* 9、 LeaseRenewalIntervalInSeconds  
eureka客户需要多长时间发送心跳给eureka服务器，表明它仍然活着,默认为30 秒

* 10、 LeaseExpirationDurationInSeconds  
Eureka服务器在接收到实例的最后一次发出的心跳后，需要等待多久才可以将此实例删除，默认为90秒

* 11、 VirtualHostName  
此实例定义的虚拟主机名，其他实例将通过使用虚拟主机名找到该实例。

* 12、 SecureVirtualHostName  
此实例定义的安全虚拟主机名

* 13、 ASGName（*）  
与此实例相关联 AWS自动缩放组名称。此项配置是在AWS环境专门使用的实例启动，它已被用于流量停用后自动把一个实例退出服务。

* 14、 HostName  
与此实例相关联的主机名，是其他实例可以用来进行请求的准确名称

* 15、 MetadataMap(*)  
获取与此实例相关联的元数据(key,value)。这个信息被发送到eureka服务器，其他实例可以使用。

* 16、 DataCenterInfo（*）  
该实例被部署在数据中心

* 17、 IpAddress  
获取实例的ip地址

* 18、 StatusPageUrlPath（*）  
获取此实例状态页的URL路径，然后构造出主机名，安全端口等，默认为/info

* 19、 StatusPageUrl(*)  
获取此实例绝对状态页的URL路径，为其他服务提供信息时来找到这个实例的状态的路径，默认为null

* 20、 HomePageUrlPath（*）  
获取此实例的相关主页URL路径，然后构造出主机名，安全端口等，默认为/

* 21、 HomePageUrl(*)  
获取此实例的绝对主页URL路径，为其他服务提供信息时使用的路径,默认为null

* 22、 HealthCheckUrlPath  
获取此实例的相对健康检查URL路径，默认为/health

* 23、 HealthCheckUrl  
获取此实例的绝对健康检查URL路径,默认为null

* 24、 SecureHealthCheckUrl  
获取此实例的绝对安全健康检查网页的URL路径，默认为null

* 25、 DefaultAddressResolutionOrder  
获取实例的网络地址，默认为[]

* 26、 Namespace  
获取用于查找属性的命名空间，默认为eureka  
&nbsp;&nbsp;

## Eureka服务端配置
* 1、 AWSAccessId  
获取aws访问的id，主要用于弹性ip绑定，此配置是用于aws上的，默认为null

* 2、 
* 3、 
* 4、 
* 5、 
* 6、 
* 7、 
* 8、 
* 9、 
* 10、 
