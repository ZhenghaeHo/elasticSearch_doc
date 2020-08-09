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

控制选举配置是否自动忽略挂掉的节点，只要es集群仍然包含至少3个节点。默认值是true。如果设置为false,这个选举配置从不会自动缩容，并且你必须使用选举配置排出api手动移除挂掉的节点。

**cluster.election.back_off_time:集群选举缓冲时间**

设置每次选举失败时，开始新的选举前，最长等待时间需要增加多少。注意这是线性的缓冲。这个设置的默认是100毫秒。修改这个设置的默认值或许会使你的es集群选举一个主节点失败。

**cluster.election.duration：集群选举时长**

设置在一个节点认为选举失败且计划重试前，每次选举允许花费多长时间。这个设置的默认值是500毫秒。修改这个设置的默认值或许会使你的es集群选举一个主节点失败。

**cluster.election.initial_timeout：集群选举初始超时时间**

设置一个节点，初始的或在被选举的主节点宕机后尝试第一次选举前，需要等待的最长时间。这个设置的默认值是100毫秒。修改这个设置的默认值或许会使你的es集群选举一个主节点失败。

**cluster.election.max_timeout：集群选举最大超时时间**

设置一个节点尝试第一次选举前，最大的等待时长，以至于一个持续长时间的网络中断不会产生极少的选举。这个设置的默认值是10秒。修改这个设置的默认值或许会使你的es集群选举一个主节点失败。

**cluster.fault_detection.follower_check.interval：集群从节点故障检测周期**

设置被选举出来的主节点等待多长时间,使得每个从节点完成与集群中的其他节点的故障检测。默认值是1秒。修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.fault_detection.follower_check.timeout：集群从节点故障检测超时时间**

设置被选举出来的主节点接收一个从节点故障检测的响应，需要等待多长时间，才认为这个从节点故障检测失败。默认值是10秒。修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.fault_detection.follower_check.retry_count：集群从节点故障检测重试次数**

设置每个节点必须连续经过多少次故障检测都是有故障，被选举出来的主节点才认为那个节点才是真的有故障并且讲它从集群中移除。默认值是3.修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.fault_detection.leader_check.interval：集群主节点故障检测周期**

设置每个节点需要等被选举出来的主节点的故障检查的时间。默认值是1秒。

修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.fault_detection.leader_check.timeout：集群主节点故障检测超时时间**

设置每个节点接收一个主节点故障检查的响应，等待多长时间，才认为这个故障检查失败。默认值是10秒。修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.fault_detection.leader_check.retry_count：集群主节点故障检测重试次数**

设置主节点连续多少次检测出来都是故障，一个节点才认为被选举出来的主节点发生了故障，并试图查找或选举一个新的主节点。默认值是3.修改这个设置的默认值或许会使你的集群变得不稳定。

**cluster.follower_lag.timeout：集群从节点延迟超时时间**

设置主节点等待多长时间来接收来自延迟节点的集群状态变更确认。默认值是90秒。如果一个节点在这段时间内，没有成功地应用这个集群状态变更，这个节点会被认为已发生了故障，并且会从这个集群中被移除。

**cluster.join.timeout：集群加入超时时间**

如果discovery.type没有被设置为single-node，设置一个节点发送一个加入集群的请求后，会等待多长时间，才认为这个请求失败并重试。默认值是60秒。

**cluster.max_voting_config_exclusions：集群最大投票配置排除**

在任何时候，给投票配置排除设置一个限制。默认值是10.

**cluster.publish.info_timeout：集群状态变更发布信息超时时间**

设置主节点等多长时间，使每一个集群状态变更完整地被发送到所有节点，才记录一个说明某些节点响应迟缓的消息。默认值是10秒。

**cluster.publish.timeout：集群状态变更发布超时时间**

如果discovery.type没有被设置为single-node，设置主节点等多长时间，使每一个集群状态变更完整地被发送到所有节点。默认值是30秒。

**cluster.no_master_block：集群无主节点阻止**

当在一个集群中没有有效的主节点时，指定哪些操作被拒绝。这个设置有2个有效的值：

**all**

​	在这个节点上的所有操作（包含读写操作）都被拒绝。这也适用于API形式的集群状态读写操作，比如获取索引的设置，修改映射和集群状态的API.

**write**

​	（默认）写操作被拒绝。基于上个已知的集群配置，读操作可以成功。这种情况或许会导致可以读取部分老旧数据，因为这个节点可能会和集群的其他节点隔离开来。

**注意**

- cluster.no_master_block设置不适用于基于节点的API（例如集群统计，节点信息和节点统计API)。这些api请求不会被阻止，能够在任何可用的节点上运行。
- 为了使集群能够全面运转，集群必须有一个有效的主节点。

**警告**

这个设置替换了早期版本的discovery.zen.no_master_block设置。discovery.zen.no_master_block设置被忽略了。