以下翻译自https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

# 安装es

## 配置es

发现和集群构成设置

发现和集群构成被下面的设置影响：

**discovery.seed_hosts**

提供一个集群中master候选节点的地址列表。也可以是一个包含多个用逗号分隔的地址的字符串。每个地址的格式是host:port或host。这个host是一个将要被dns解析的主机名称，ipv4地址或者ipv6地址。ipv6地址必须被放到方括号里面。如果一个主机名称通过dns解析成了多个地址，es会使用全部地址。dns查找请见jvm dns缓存。如果这个端口没有被提供，那么端口通过按顺序检查下列设置来确定：

1. `transport.profiles.default.port`
2. `transport.port`

如果这些设置没被设置，那么这个默认的端口是9300。discovery.seed_hosts的默认值是["127.0.0.1", "[::1]"]。

这个设置以前被称为discovery.zen.ping.unicast.hosts。虽然它老的配置名称被废弃,但是为了向后保持兼容，老的配置名称继续起作用。在未来的某个版本中，对老的配置名称的支持会被移除。

**discovery.seed_providers**

指定使用哪些种子主机的提供者类型来获取用于启动发现进程的种子节点的地址。默认情况，seed hosts provider的类型是基于设置的seed hosts provider,它从设置discovery.seed_hosts获取种子节点的地址。这个设置以前被称为discovery.zen.hosts_provider。它老的名字被废弃，但是为了向后保持兼容，老的配置名称继续起作用。在未来的某个版本中，对老的配置名称的支持会被移除。

**discovery.type**

指定es是否应该构建成一个多节点的集群。默认情况下，当构建一个集群时es发现其他节点，并且允许其他节点稍后加入集群。如果discovery.type被设置成single-node，es构成一个单节点的集群，并且废除由cluster.publish.timeout和cluster.join.timeout设置的超时时间。

**cluster.initial_master_nodes**

在一个全新的集群中，设置初始的master候选节点列表。默认情况，这个列表是空的，这意味着这个节点希望加入一个已经被启动的集群中。

**专家级的设置**

发现和集群构成也被下面的专家级别的设置影响，尽管改变这些设置的默认值不被推荐。

**警告** 如果你调整这些设置，那么你的集群可能不会正确构建，或者会变得不稳定，或者会产生某些无法容忍的故障。

**discovery.cluster_formation_warning_timeout**

设置一个节点在记录一个集群不能构造的警告日志之前，会尝试多久来构造一个集群。默认值是10秒。如果经过discovery.cluster_formation_warning_timeout设置的超时时间后，一个集群还没有构造成功，那么这个节点会记录一个以master not discovered开头的警告消息，这个消息描述了发现进程的当前状态。

**discovery.find_peers_interval**

设置一个节点在尝试下一轮发现操作前，会等待多久。默认值是1秒。

**discovery.probe.connect_timeout**

设置当尝试连接到每一个地址时，需要等待多长时间。默认值时3秒。

**discovery.probe.handshake_timeout**

设置当试图通过握手来鉴定远程节点时，等待多长时间。默认值是1秒。

**discovery.request_peers_timeout**

设置一个节点询问其他相同角色的节点后，会等待多长时间，才认为这个询问请求失败。默认值时3秒。

**discovery.seed_resolver.max_concurrent_resolvers**

指定当解析种子节点的地址时，需要并发执行多少个dns解析。默认值是10。这个设置先前叫做discovery.zen.ping.unicast.concurrent_connects。它老的设置名称被废弃，但是为了向后保持兼容，继续有效。对老的设置名称的支持，在未来的某个版本中会被移除。

**discovery.seed_resolver.timeout**

指定解析种子节点的地址时，执行每个dns查找需要等待多长时间。默认值是5秒。这个设置之前叫做discovery.zen.ping.unicast.hosts.resolve_timeout。它老的设置名称已被废弃，但是为了向后保持兼容，老的设置名称继续有效。对老的设置名称的支持，在未来的某个版本中会被移除。

**cluster.auto_shrink_voting_configuration**

