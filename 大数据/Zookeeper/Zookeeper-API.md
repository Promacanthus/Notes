znode 节点可能包含数据，也可能没有包含数据，如果一个znode节点包含任何数据，那么数据存储为字节数组（byte array）。字节数组的具体格式特定于每个应用的实现，Zookeeper并不直接提供解析的支持。

# Zookeeper的API暴露的方法

```
create /path data
```
创建一个名为/path的znode节点，并包含数据data

```
delete /path
```
删除名为/path的znode

```
exists /path
```
检查是否存在名为/path的znode


```
setData /path data
```
设置名为/path的znode的数据为data


```
getData /path
```
返回名为/path的znode的数据信息

```
getChildren /path
```
返回名为/path的znode的所有子节点列表

> zookeeper不允许局部写入或读取znode的数据，放设置一个znode的数据或读取时，znode的内容会被整个替换或全部读取进来

# znode
四种类型：
1. 持久的（persistent）：只能通过delete进行删除
2. 临时的（ephemeral）：创建该节点的客户端崩溃或关闭了与zookeeper的连接时被删除
3. 持久有序的（persistent_sequential）
4. 临时有序的（ephemeral_sequential）

### 持久节点
为应用保存数据，即使该znode的创建者不再属于应用系统时，数据也可以保存下来而不丢失。
> 例如，主从模式中，需要保存从节点任务分配情况，即使分配任务的主节点已经崩溃了

### 临时节点
传达了应用某些方面的信息，仅当创建者的会话有效时这些信息必须有效保存。
> 例如，在主从模式中，当主节点创建的znode为临时节点时，该节点的存在意味着有一个主节点，且主节点状态处于正常运行中。如果主节点消失后，该znode仍然存在，那么系统将无法检测到主节点崩溃，这样就阻止系统继续运行。

**一个临时znode会在以下两种情况被删除：**
1. 当创建该znode的客户端的会话因为超时或主动关闭而中止时
2. 当某个客户端（不一定是创建者）主动删除该节点时

### 有序节点
有序znode被分配唯一单调递增的整数，当创建一个有序节点时，一个序号会被追加到路径之后。

> 有序节点通过提供了创建具有唯一名称的znode的简单方式，同时也通过这种方式可以直观地查看znode的创建顺序。


# 监视与通知
Zookeeper通常以远程服务的方式被访问，如果每次访问znode时，客户端都需要获得节点中的内容，代价太高，导致更高的延迟，而且zookeeper需要做更多的操作。

**与远程节点通信，通常的措施：轮询机制或者通知机制。**

zookeeper采用基于通知的机制：客户端向zookeeper注册需要接收通知的znode，通过对znode设置监视点（watch）来接收通知。

> 监视点是一个单次触发的操作，为了接收多个通知，客户端必须在每次通知后设置一个新的监视点。

**如果在触发监视点后，设置新的监视点时znode节点发生了新的变化，是否会丢失数据而错过状态的变化？**

- 为了避免出现数据丢失而错过状态变化的情况，在设置新的监视点前，客户端会读取znode的状态，通过这种方式，在设置新的监视点前读取znode的状态就不会丢书数据而错过状态的变化。

## 设置不同类型的监视点
依赖于设置监视点对应的通知类型，客户端可以设置多种监视点：
1. 监视znode的数据变化
2. 监视znode子节点变化
3. 监视znode的创建或删除

# 版本
每一个znode都有一个版本号，随着数据变化而自增。两个API操作可以有条件地执行：setData和delete。这两个调用以版本号作为转入参数，只有当转入参数的版本号与服务器上的版本号一致时调用才会成功。

**当多个客户端对同一个znode进行操作时，版本号显得尤为重要。**

举例：
1. 客户端1对znode写入第一个版本的配置信息/config
2. 客户端2读取/donfig的信息，并对znode/config中写入第二版本的信息
3. 客户端1尝试对/config进行写入，但因为版本号不匹配而请求失败



