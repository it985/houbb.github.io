---
layout: post
title:  Neo4j GDS-17-neo4j GDS 库创建 graph 图投影更复杂的场景
date:  2018-1-8 14:18:33 +0800
categories: [SQL]
tags: [nosql, neo4j]
published: true
---


# neo4j apoc 系列

[Neo4j APOC-01-图数据库 apoc 插件介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-apoc-01-intro)

[Neo4j GDS-01-graph-data-science 图数据科学插件库概览](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-01-overview)

[Neo4j GDS-02-graph-data-science 插件库安装实战笔记](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-02-install-inaction)

[Neo4j GDS-03-graph-data-science 简单聊一聊图数据科学插件库](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-03-intro-chat)

[Neo4j GDS-04-图的中心性分析介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-04-chat-middle-analysis-intro)

[Neo4j GDS-05-neo4j中的中心性分析算法](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-04-chat-middle-analysis-impl)

[Neo4j GDS-06-neo4j GDS 库中社区检测算法介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-06-chat-community-detection-intro)

[Neo4j GDS-07-neo4j GDS 库中社区检测算法实现](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-07-chat-community-detection-impl)

[Neo4j GDS-08-neo4j GDS 库中路径搜索算法介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-08-chat-path-search-intro)

[Neo4j GDS-09-neo4j GDS 库中路径搜索算法实现](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-09-chat-path-search-impl)

[Neo4j GDS-10-neo4j GDS 库中相似度算法介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-10-chat-similar-intro)

[Neo4j GDS-11-neo4j GDS 库中相似度算法实现](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-11-chat-similar-impl)

[Neo4j GDS-12-neo4j GDS 库中节点插入（Node Embedding）算法介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-12-chat-node-embedding-intro)

[Neo4j GDS-13-neo4j GDS 库中节点插入算法实现](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-13-chat-node-embedding-impl)

[Neo4j GDS-14-neo4j GDS 库中链接预测算法介绍](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-14-chat-link-pre-intro)

[Neo4j GDS-15-neo4j GDS 库中链接预测算法实现](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-15-chat-link-pre-impl)

[Neo4j GDS-16-neo4j GDS 库创建 graph 图投影](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-16-chat-create-graph)

[Neo4j GDS-17-neo4j GDS 库创建 graph 图投影更复杂的场景](https://houbb.github.io/2018/01/08/neo4j-plugins-gds-17-chat-create-graph-more)

# 实际测试

## 数据初始化

i_app 节点，i_app 指向 i_vm，i_phy。

i_app 有 name 属性。

i_vm, i_phy 有 ip 属性。

创建 4 个 i_app 节点，分别指向 4 个 i_vm。2个 i_vm 一组，分别指向 i_phy 节点。

给出 cypher 初始化语句

```
CREATE (phy1:i_phy {ip: '192.168.1.1'}),
       (phy2:i_phy {ip: '192.168.1.2'}),

(vm1:i_vm {ip: '10.0.0.1'})-[:BELONGS_TO]->(phy1),
(vm2:i_vm {ip: '10.0.0.2'})-[:BELONGS_TO]->(phy1),
(vm3:i_vm {ip: '10.0.0.3'})-[:BELONGS_TO]->(phy2),
(vm4:i_vm {ip: '10.0.0.4'})-[:BELONGS_TO]->(phy2),

(app1:i_app {name: 'app1'})-[:POINTS_TO]->(vm1),
(app2:i_app {name: 'app2'})-[:POINTS_TO]->(vm2),
(app3:i_app {name: 'app3'})-[:POINTS_TO]->(vm3),
(app4:i_app {name: 'app4'})-[:POINTS_TO]->(vm4);

```

## 目标

希望达到的效果：

```
neo4j 中。如何实现一个从 i_app 节点，且 i_app 的 AppName 属性 在指定的列表中，比如 ['app1']，从 i_app 作为 start，i_vm 或者 i_phy 作为终止节点，最大2层。

任意关系。对应的图投影语句怎么写？我最终的目的是创建投影之后，通过 pageRank 找到关联节点比较多的元素返回。

所以你帮我拆分为3步：

1. apoc 找到符合条件的节点+关系 

2. gds 创建图投影

3. gds pageRank 找到score 比较高的元素 Top9 返回。 

先给我拆分具体的每一步的 cypher 语句，然后给出 java 实现.
```



# 实现代码


## 第一个版本

```java
package org.example;

import org.neo4j.driver.*;

import java.util.Arrays;
import java.util.List;

public class PageRankService implements AutoCloseable {
    private final Driver driver;

    // 初始化Driver（建议使用连接池）
    public PageRankService(String uri, String user, String password) {
        driver = GraphDatabase.driver(uri, AuthTokens.basic(user, password));
    }

    // 主业务方法（appNames为动态参数）
    public List<Record> calculateTopPageRankNodes(List<String> appNames) {
        try (Session session = driver.session()) {
            // 步骤1：创建Cypher投影
            createGraphProjection(session, appNames);

            // 步骤2：执行PageRank算法
            return session.writeTransaction(tx -> {
                Result result = tx.run(
                        "CALL gds.pageRank.stream('myGraph') "
                                + "YIELD nodeId, score "
                                + "RETURN gds.util.asNode(nodeId) AS node, score "
                                + "ORDER BY score DESC LIMIT 9");


                // 删除
                tx.run("CALL gds.graph.drop('myGraph')");

                return result.list();
            });
        }
    }

    // 创建动态图投影（私有方法封装）
    private void createGraphProjection(Session session, List<String> appNames) {
        String nodeQuery =
                "MATCH (start:i_app) WHERE start.name IN $appNames " +
                        "CALL apoc.path.expandConfig(start, { " +
                        "    relationshipFilter: '>', " +
                        "    labelFilter: '/i_phy', " +
                        "    minLevel: 1, " +
                        "    maxLevel: 2, " +
                        "    uniqueness: 'NODE_GLOBAL' " +
                        "}) YIELD path " +
                        "UNWIND nodes(path) AS node " +
                        "RETURN DISTINCT id(node) AS id, labels(node) AS labels";

        String relQuery =
                "MATCH (start:i_app) WHERE start.name IN $appNames " +
                        "CALL apoc.path.expandConfig(start, { " +
                        "    relationshipFilter: '>', " +
                        "    labelFilter: '/i_phy', " +
                        "    minLevel: 1, " +
                        "    maxLevel: 2, " +
                        "    uniqueness: 'NODE_GLOBAL' " +
                        "}) YIELD path " +
                        "UNWIND relationships(path) AS rel " +
                        "RETURN DISTINCT id(startNode(rel)) AS source, " +
                        "id(endNode(rel)) AS target, type(rel) AS type";

        // 执行Cypher投影（带参数）
        session.run(
                "CALL gds.graph.create.cypher($graphName, $nodeQuery, $relQuery, {parameters: {appNames: $appNames}})",
                Values.parameters(
                        "graphName", "myGraph",
                        "nodeQuery", nodeQuery,
                        "relQuery", relQuery,
                        "appNames", appNames
                )
        ).consume(); // 显式消费结果确保执行
    }

    @Override
    public void close() throws Exception {
        driver.close();
    }

    // 示例用法
    public static void main(String[] args) {
        try (PageRankService service = new PageRankService("bolt://[::1]:17687", "neo4j", "12345678")) {
            List<String> appNameList = Arrays.asList("app1", "app2", "app3", "app4");
            List<Record> topNodes = service.calculateTopPageRankNodes(appNameList);
            topNodes.forEach(record ->
                    System.out.println(record.get("node").asNode() + " Score: " + record.get("score")));
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

## 执行异常

```
Exception in thread "main" java.lang.RuntimeException: org.neo4j.driver.exceptions.ClientException: There is no procedure with the name `gds.graph.create.cypher` registered for this database instance. Please ensure you've spelled the procedure name correctly and that the procedure is properly deployed.
	at org.example.PageRankService.main(PageRankService.java:93)
Caused by: org.neo4j.driver.exceptions.ClientException: There is no procedure with the name `gds.graph.create.cypher` registered for this database instance. Please ensure you've spelled the procedure name correctly and that the procedure is properly deployed.
	at org.neo4j.driver.internal.util.Futures.blockingGet(Futures.java:111)
	at org.neo4j.driver.internal.InternalSession.run(InternalSession.java:62)
	at org.neo4j.driver.internal.InternalSession.run(InternalSession.java:47)
	at org.neo4j.driver.internal.AbstractQueryRunner.run(AbstractQueryRunner.java:34)
	at org.example.PageRankService.createGraphProjection(PageRankService.java:69)
	at org.example.PageRankService.calculateTopPageRankNodes(PageRankService.java:22)
	at org.example.PageRankService.main(PageRankService.java:89)
	Suppressed: org.neo4j.driver.internal.util.ErrorUtil$InternalExceptionCause
```

## 修正

首先确认当前 GDS 的方法到底是什么？

这个应该是版本问题，这个版本中的 2.5.3 方法是 `gds.graph.project.cypher`，

我当时测试另一个版本 1.8.8 确实是 `gds.graph.create.cypher`

PS: 当初自己怎么安装了一个这么老的版本。

```
CALL gds.list()
YIELD name, description
WHERE name contains 'graph'
RETURN name, description
```

结果：

```
╒═══════════════════════════════════════════════╤══════════════════════════════════════════════════════════════════════╕
│name                                           │description                                                           │
╞═══════════════════════════════════════════════╪══════════════════════════════════════════════════════════════════════╡
...
├───────────────────────────────────────────────┼──────────────────────────────────────────────────────────────────────┤
│"gds.graph.project"                            │"Creates a named graph in the catalog for use by algorithms."         │
├───────────────────────────────────────────────┼──────────────────────────────────────────────────────────────────────┤
│"gds.graph.project.cypher"                     │"Creates a named graph in the catalog for use by algorithms."         │
├───────────────────────────────────────────────┼──────────────────────────────────────────────────────────────────────┤
...
└───────────────────────────────────────────────┴──────────────────────────────────────────────────────────────────────┘
```


### 参数

```
CALL gds.list()
YIELD name, description, signature
WHERE name contains 'gds.graph.project.cypher'
RETURN name, description, signature
```

如下：

```
╒═══════════════════════════════════╤═════════════════════════════════════════════════════════════════════╤══════════════════════════════════════════════════════════════════════╕
│name                               │description                                                          │signature                                                             │
╞═══════════════════════════════════╪═════════════════════════════════════════════════════════════════════╪══════════════════════════════════════════════════════════════════════╡
│"gds.graph.project.cypher"         │"Creates a named graph in the catalog for use by algorithms."        │"gds.graph.project.cypher(graphName :: STRING?, nodeQuery :: STRING?, │
│                                   │                                                                     │relationshipQuery :: STRING?, configuration = {} :: MAP?) :: (nodeQuer│
│                                   │                                                                     │y :: STRING?, relationshipQuery :: STRING?, graphName :: STRING?, node│
│                                   │                                                                     │Count :: INTEGER?, relationshipCount :: INTEGER?, projectMillis :: INT│
│                                   │                                                                     │EGER?)"                                                               │
├───────────────────────────────────┼─────────────────────────────────────────────────────────────────────┼──────────────────────────────────────────────────────────────
```

API 为：

```java
CALL gds.graph.project.cypher(
    graphName: String, 
    nodeQuery: String, 
    relationshipQuery: String, 
    configuration: Map
)
```


## 再次执行

修正为当前版本的方法名。

```java
session.run(
                "CALL gds.graph.project.cypher($graphName, $nodeQuery, $relQuery, {parameters: {appNames: $appNames}})",
                Values.parameters(
                        "graphName", "myGraph",
                        "nodeQuery", nodeQuery,
                        "relQuery", relQuery,
                        "appNames", appNames
                )
        ).consume(); // 显式消费结果确保执行
```


结果如下：

```
node<0> Score: 0.62175
node<1> Score: 0.62175
node<4> Score: 0.2775
node<3> Score: 0.2775
node<5> Score: 0.2775
node<2> Score: 0.2775
node<8> Score: 0.15000000000000002
node<9> Score: 0.15000000000000002
node<7> Score: 0.15000000000000002
```

发现如果匹配的数据为空，实际上会报错如下：

```
Exception in thread "main" java.lang.RuntimeException: org.neo4j.driver.exceptions.ClientException: Failed to invoke procedure `gds.graph.project.cypher`: Caused by: java.lang.IllegalArgumentException: Node-Query returned no nodes
	at org.example.PageRankService.main(PageRankService.java:91)
Caused by: org.neo4j.driver.exceptions.ClientException: Failed to invoke procedure `gds.graph.project.cypher`: Caused by: java.lang.IllegalArgumentException: Node-Query returned no nodes
	at org.neo4j.driver.internal.util.Futures.blockingGet(Futures.java:111)
	at org.neo4j.driver.internal.InternalResult.blockingGet(InternalResult.java:107)
	at org.neo4j.driver.internal.InternalResult.consume(InternalResult.java:98)
	at org.example.PageRankService.createGraphProjection(PageRankService.java:75)
	at org.example.PageRankService.calculateTopPageRankNodes(PageRankService.java:20)
	at org.example.PageRankService.main(PageRankService.java:87)
```

## 节点本身的基本信息

NodeInfo 基本信息：

```java
public class NodeInfo {

    private Long nodeId;
    private List<String> labels;
    private Map<String, Object> properties;

    //get/set/toString

}
```

核心方法：

```java
public List<NodeInfo> getNodeInfo(List<Long> nodeIds) {
    Session session = driver.session();
    String cypherQuery =
            "MATCH (n) " +
                    "WHERE id(n) IN $nodeIds " +
                    "RETURN id(n) AS nodeId, labels(n) AS labels, properties(n) AS properties";
    List<NodeInfo> nodeInfoList = new ArrayList<>();
    session.readTransaction(tx -> {
        Result result = tx.run(cypherQuery, Values.parameters("nodeIds", nodeIds));
        while (result.hasNext()) {
            Record record = result.next();
            NodeInfo info = new NodeInfo();
            info.setNodeId(record.get("nodeId").asLong());
            info.setLabels(record.get("labels").asList(Value::asString));
            info.setProperties(record.get("properties").asMap(Value::asObject));
            nodeInfoList.add(info);
        }
        return null;
    });
    return nodeInfoList;
}
```

测试效果：

```java
public static void main(String[] args) {
        try (PageRankService_V2_5_3 service = new PageRankService_V2_5_3("bolt://[::1]:17687", "neo4j", "12345678")) {
            List<String> appNameList = Arrays.asList("app1", "app2", "app3", "app4");
            List<Record> topNodes = service.calculateTopPageRankNodes(appNameList);
            topNodes.forEach(record ->
                    System.out.println(record.get("node").asNode() + " Score: " + record.get("score")));

            // 结果处理
            List<Long> nodeIdList = topNodes.stream().map(new Function<Record, Long>() {
                @Override
                public Long apply(Record record) {
                    return record.get("node").asNode().id();
                }
            }).collect(Collectors.toList());

            List<NodeInfo> nodeInfoList = service.getNodeInfo(nodeIdList);
            nodeInfoList.forEach(System.out::println);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
```

输出如下：

```
NodeInfo{nodeId=0, labels=[i_phy], properties={ip=192.168.1.1}}
NodeInfo{nodeId=1, labels=[i_phy], properties={ip=192.168.1.2}}
NodeInfo{nodeId=2, labels=[i_vm], properties={ip=10.0.0.1}}
NodeInfo{nodeId=3, labels=[i_vm], properties={ip=10.0.0.2}}
NodeInfo{nodeId=4, labels=[i_vm], properties={ip=10.0.0.3}}
NodeInfo{nodeId=5, labels=[i_vm], properties={ip=10.0.0.4}}
NodeInfo{nodeId=7, labels=[i_app], properties={name=app2}}
NodeInfo{nodeId=8, labels=[i_app], properties={name=app3}}
NodeInfo{nodeId=9, labels=[i_app], properties={name=app4}}
```


## 查询对应的数据

我们可以直接查询对应的数据效果

```
MATCH (start:i_app) WHERE start.name IN ['app1', 'app2', 'app3', 'app4']
CALL apoc.path.expandConfig
(start, 
{     
    relationshipFilter: '>',     
    labelFilter: '/i_phy',     
    minLevel: 1,     
    maxLevel: 2,     
    uniqueness: 'NODE_GLOBAL' 
}
) 
YIELD path 
with path, nodes(path) as all_nodes
UNWIND all_nodes as node
with path, all_nodes, node
where id(node) in [0]
with path, all_nodes
return distinct relationships(path) as all_relationships, all_nodes
```

效果如下：


<svg xmlns="http://www.w3.org/2000/svg" width="335.4267272949219" height="328.6940002441406" viewBox="-156.72311401367188 -155.77711486816406 335.4267272949219 328.6940002441406"><title>Neo4j Graph Visualization</title><desc>Created using Neo4j (http://www.neo4j.com/)</desc><g class="layer relationships"><g class="relationship" transform="translate(64.32676514546043 143.91686644644471) rotate(306.39457944491096)"><path class="b-outline" fill="#A5ABB6" stroke="none" d="M 25 0.5 L 43.55696618385781 0.5 L 43.55696618385781 -0.5 L 25 -0.5 Z M 93.33430993385781 0.5 L 111.89127611771562 0.5 L 111.89127611771562 3.5 L 118.89127611771562 0 L 111.89127611771562 -3.5 L 111.89127611771562 -0.5 L 93.33430993385781 -0.5 Z"/><text text-anchor="middle" pointer-events="none" font-size="8px" fill="#000000" x="68.44563805885781" y="3" font-family="Helvetica Neue, Helvetica, Arial, sans-serif">POINTS_TO</text></g><g class="relationship" transform="translate(149.7036085657004 28.091593080134434) rotate(219.40552139148542)"><path class="b-outline" fill="#A5ABB6" stroke="none" d="M 25 0.5 L 40.70405936294998 0.5 L 40.70405936294998 -0.5 L 25 -0.5 Z M 97.82515311294998 0.5 L 113.52921247589995 0.5 L 113.52921247589995 3.5 L 120.52921247589995 0 L 113.52921247589995 -3.5 L 113.52921247589995 -0.5 L 97.82515311294998 -0.5 Z"/><text text-anchor="middle" pointer-events="none" font-size="8px" fill="#000000" x="69.26460623794998" y="3" transform="rotate(180 69.26460623794998 0)" font-family="Helvetica Neue, Helvetica, Arial, sans-serif">BELONGS_TO</text></g><g class="relationship" transform="translate(-127.72311814249437 -126.77710584159497) rotate(435.56822637411364)"><path class="b-outline" fill="#A5ABB6" stroke="none" d="M 25 0.5 L 42.813713603696996 0.5 L 42.813713603696996 -0.5 L 25 -0.5 Z M 92.591057353697 0.5 L 110.40477095739399 0.5 L 110.40477095739399 3.5 L 117.40477095739399 0 L 110.40477095739399 -3.5 L 110.40477095739399 -0.5 L 92.591057353697 -0.5 Z"/><text text-anchor="middle" pointer-events="none" font-size="8px" fill="#000000" x="67.702385478697" y="3" font-family="Helvetica Neue, Helvetica, Arial, sans-serif">POINTS_TO</text></g><g class="relationship" transform="translate(-92.23200700552373 11.134096827698855) rotate(329.7799574437129)"><path class="b-outline" fill="#A5ABB6" stroke="none" d="M 25 0.5 L 42.866771029495254 0.5 L 42.866771029495254 -0.5 L 25 -0.5 Z M 99.98786477949525 0.5 L 117.85463580899051 0.5 L 117.85463580899051 3.5 L 124.85463580899051 0 L 117.85463580899051 -3.5 L 117.85463580899051 -0.5 L 99.98786477949525 -0.5 Z"/><text text-anchor="middle" pointer-events="none" font-size="8px" fill="#000000" x="71.42731790449525" y="3" font-family="Helvetica Neue, Helvetica, Arial, sans-serif">BELONGS_TO</text></g></g><g class="layer nodes"><g class="node" aria-label="graph-node6" transform="translate(64.32676514546043,143.91686644644471)"><circle class="b-outline" cx="0" cy="0" r="25" fill="#57C7E3" stroke="#23b3d7" stroke-width="2px"/><text class="caption" text-anchor="middle" pointer-events="none" x="0" y="5" font-size="10px" fill="#2A2C34" font-family="Helvetica Neue, Helvetica, Arial, sans-serif"> app1</text></g><g class="node" aria-label="graph-node2" transform="translate(149.7036085657004,28.091593080134434)"><circle class="b-outline" cx="0" cy="0" r="25" fill="#F79767" stroke="#f36924" stroke-width="2px"/><text class="caption" text-anchor="middle" pointer-events="none" x="0" y="5" font-size="10px" fill="#FFFFFF" font-family="Helvetica Neue, Helvetica, Arial, sans-serif"> 10.0.0.1</text></g><g class="node" aria-label="graph-node0" transform="translate(37.25720222957948,-64.2910751317527)"><circle class="b-outline" cx="0" cy="0" r="25" fill="#C990C0" stroke="#b261a5" stroke-width="2px"/><text class="caption" text-anchor="middle" pointer-events="none" x="0" y="5" font-size="10px" fill="#FFFFFF" font-family="Helvetica Neue, Helvetica, Arial, sans-serif"> 192.168.…</text></g><g class="node" aria-label="graph-node7" transform="translate(-127.72311814249437,-126.77710584159497)"><circle class="b-outline" cx="0" cy="0" r="25" fill="#57C7E3" stroke="#23b3d7" stroke-width="2px"/><text class="caption" text-anchor="middle" pointer-events="none" x="0" y="5" font-size="10px" fill="#2A2C34" font-family="Helvetica Neue, Helvetica, Arial, sans-serif"> app2</text></g><g class="node" aria-label="graph-node3" transform="translate(-92.23200700552373,11.134096827698855)"><circle class="b-outline" cx="0" cy="0" r="25" fill="#F79767" stroke="#f36924" stroke-width="2px"/><text class="caption" text-anchor="middle" pointer-events="none" x="0" y="5" font-size="10px" fill="#FFFFFF" font-family="Helvetica Neue, Helvetica, Arial, sans-serif"> 10.0.0.2</text></g></g></svg>


# 一些技巧

有些保证图的删除。

如果每次图都不同，又保证不同的图不冲突，可以生成唯一标识，生成后删除。

或者改为定时删除。

------------------


# chat

## 目标场景

希望达到的效果：

```
如何实现一个从 i_app 节点，且 i_app 的 AppName 属性 在指定的列表中，比如 ['my-web']，从 i_app 作为 start，i_vm 或者 i_phy 作为终止节点，最大2层。任意关系。

对应的图投影语句怎么写？结合 apoc 的方式来实现
```

## 实现



### 分步实现说明

#### 步骤1：使用APOC找到符合条件的节点和关系
通过`apoc.path.expandConfig`动态获取起始节点为`i_app`（AppName在指定列表），终止节点为`i_vm`或`i_phy`，最大深度为2的路径。此步骤直接嵌入到后续的图投影中，无需单独存储中间结果。

```cypher
// 步骤1和步骤2合并：动态获取子图并创建投影
CALL gds.graph.project.cypher(
    'myGraph',
    // 节点查询：收集所有路径中的节点（包括i_app、中间节点、i_vm/i_phy）
    'MATCH (start:i_app) WHERE start.name IN $appNames
     CALL apoc.path.expandConfig(start, {
         relationshipFilter: ">",       // 允许所有关系类型和方向
         labelFilter: ">i_vm|i_phy",   // 终止节点必须是i_vm或i_phy
         minLevel: 1,                  // 最小深度1（避免仅包含i_app）
         maxLevel: 2,                  // 最大深度2
         uniqueness: "NODE_GLOBAL"     // 节点全局唯一，避免重复遍历
     }) YIELD path
     UNWIND nodes(path) AS node
     RETURN DISTINCT id(node) AS id, labels(node) AS labels',
    // 关系查询：收集所有路径中的关系
    'MATCH (start:i_app) WHERE start.name IN $appNames
     CALL apoc.path.expandConfig(start, {
         relationshipFilter: ">",
         labelFilter: ">i_vm|i_phy",
         minLevel: 1,
         maxLevel: 2,
         uniqueness: "NODE_GLOBAL"
     }) YIELD path
     UNWIND relationships(path) AS rel
     RETURN DISTINCT id(startNode(rel)) AS source, id(endNode(rel)) AS target, type(rel) AS type',
    { parameters: { appNames: ['my-web'] } }  // 指定应用名列表
)
YIELD graphName, nodeCount, relationshipCount;  // 返回投影信息
```


**关键点解析**：
- **路径过滤逻辑**：通过`labelFilter: ">i_vm|i_phy"`确保路径末端节点为目标类型，`minLevel:1`排除仅包含起始节点的情况。
- **性能优化**：`uniqueness: "NODE_GLOBAL"`减少重复遍历。

---

#### 步骤2：创建GDS图投影
使用Cypher投影方式动态构建子图，仅包含步骤1筛选出的节点和关系。此方法无需预先定义标签或关系类型，适合动态场景。

```cypher
// （已合并到步骤1的代码中）
```


**参数说明**：
- **节点投影**：返回所有路径节点的ID及标签。
- **关系投影**：返回所有路径关系的起点、终点及类型。
- **动态性优势**：无需预先知道涉及的标签或关系类型，直接通过路径扩展动态捕获。

---

#### 步骤3：运行PageRank算法并返回Top9结果
对投影后的图执行PageRank算法，按得分降序取前9名节点。

```cypher
// 步骤3：执行PageRank并返回高影响力节点
CALL gds.pageRank.stream('myGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId) AS node, score  // 将节点ID转换为实际节点对象
ORDER BY score DESC
LIMIT 9;
```


**算法配置**：
- **默认参数**：阻尼系数为0.85，迭代次数为20（未显式指定时使用默认值）。
- **结果解释**：PageRank分数反映节点在图中的连接重要性，高分节点通常是关键枢纽。

---

### 完整代码整合
```cypher
// 步骤1+2：动态创建子图投影
CALL gds.graph.project.cypher(
    'myGraph',
    'MATCH (start:i_app) WHERE start.name IN $appNames
     CALL apoc.path.expandConfig(start, {
         relationshipFilter: ">",
         labelFilter: ">i_vm|i_phy",
         minLevel: 1,
         maxLevel: 2,
         uniqueness: "NODE_GLOBAL"
     }) YIELD path
     UNWIND nodes(path) AS node
     RETURN DISTINCT id(node) AS id, labels(node) AS labels',
    'MATCH (start:i_app) WHERE start.name IN $appNames
     CALL apoc.path.expandConfig(start, {
         relationshipFilter: ">",
         labelFilter: ">i_vm|i_phy",
         minLevel: 1,
         maxLevel: 2,
         uniqueness: "NODE_GLOBAL"
     }) YIELD path
     UNWIND relationships(path) AS rel
     RETURN DISTINCT id(startNode(rel)) AS source, id(endNode(rel)) AS target, type(rel) AS type',
    { parameters: { appNames: ['app1'] } }
)
YIELD graphName, nodeCount, relationshipCount;

// 步骤3：PageRank计算
CALL gds.pageRank.stream('myGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId) AS node, score
ORDER BY score DESC
LIMIT 9;
```


### 常见问题及优化建议
1. **性能问题**：
   - **路径深度限制**：最大深度设为2层，避免大规模图遍历。
   - **索引优化**：为`i_app`节点的`AppName`属性创建索引，加速起始节点查询。
     ```cypher
     CREATE INDEX FOR (n:i_app) ON (n.name);
     ```


2. **结果准确性**：
   - **终止节点验证**：通过`labelFilter`确保终止节点类型正确，避免无关节点混入。
   - **权重调整**：若需考虑关系权重，可在关系查询中返回`weight`属性，并在PageRank配置中添加`relationshipWeightProperty`参数。

3. **扩展性**：
   - **动态参数**：将`appNames`设为变量，便于灵活调整目标应用。
   - **多算法对比**：可尝试其他中心性算法（如Betweenness）验证结果一致性。



## graph.cypher 参数说明

### 全面解析 `gds.graph.project.cypher` 方法的参数及核心逻辑

`gds.graph.project.cypher` 是 Neo4j Graph Data Science（GDS）库中用于动态构建内存图投影的核心方法，其参数设计直接决定了图的筛选逻辑、属性和性能表现。以下从参数结构、使用规范到实践策略进行深度剖析：

---

#### 一、参数构成与语法规范

| 参数名                | 是否必选 | 数据类型 | 描述                                                                               | 资料引用 |
|-----------------------|----------|----------|------------------------------------------------------------------------------------|----------|
| `graphName`           | 是       | String   | 内存图投影的唯一标识名，用于后续算法调用                                           |  |
| `nodeQuery`           | 是       | String   | 定义节点筛选逻辑的Cypher查询，需返回 `id` 列（节点ID）和可选的 `labels`（标签）列 |  |
| `relationshipQuery`   | 是       | String   | 定义关系筛选逻辑的Cypher查询，需返回 `source`（起点）、`target`（终点）列          |  |
| `configuration`       | 否       | Map      | 控制内存分配、并发等高级参数（如 `readConcurrency`、`parameters` 动态传参）        |  |


**语法原型**：
```cypher
CALL gds.graph.project.cypher(
    graphName: String,
    nodeQuery: String,
    relationshipQuery: String,
    configuration: Map
) YIELD graphName, nodeCount, relationshipCount
```


---

#### 二、核心参数详解

##### 1. **`nodeQuery`（节点查询）**
- **必选字段**：`id`（节点ID）
- **可选字段**：`labels`（节点标签）、自定义属性（如 `n.age AS age`）
- **作用**：通过Cypher查询动态筛选参与图计算的节点集合
- **示例**：
  ```cypher
  // 筛选年龄小于30且标签为Person的节点，携带属性
  'MATCH (n:Person) WHERE n.age < 30 RETURN id(n) AS id, labels(n) AS labels, n.age AS age'
  ```

- **特殊场景**：
  - **虚拟节点**：通过 `UNWIND` 生成虚拟节点（如 `RETURN 0 AS id`）
  - **动态传参**：通过 `$param` 语法注入外部参数（如 `WHERE n.name = $name`）

##### 2. **`relationshipQuery`（关系查询）**
- **必选字段**：`source`（起点ID）、`target`（终点ID）
- **可选字段**：`type`（关系类型）、自定义属性（如 `r.weight AS weight`）
- **作用**：定义节点间的连接关系及属性，支持聚合逻辑（如 `COUNT(*) AS count`）
- **示例**：
  ```cypher
  // 筛选KNOWS类型关系，聚合重复边并计算权重总和
  'MATCH (a)-[r:KNOWS]->(b) 
   RETURN id(a) AS source, id(b) AS target, 
          SUM(r.weight) AS totalWeight, 
          "KNOWS" AS type'
  ```

- **高级特性**：
  - **关系去重**：使用 `DISTINCT` 或 `COUNT()` 处理平行边
  - **方向控制**：通过 `[:REL_TYPE]` 或反向查询实现关系方向调整

##### 3. **`configuration`（配置参数）**
- **关键子参数**：

  | 参数名              | 作用                                                                 | 默认值 |
  |---------------------|----------------------------------------------------------------------|--------|
  | `parameters`        | 向查询中注入动态参数（如列表过滤）                                   | `{}`   |
  | `readConcurrency`   | 控制并发线程数（影响内存占用和性能）                                 | 4      |
  | `validateRelationships` | 验证关系两端节点是否存在于节点集合中（避免孤立边）           | true   |
- **示例**：
  ```cypher
  { 
      parameters: { appNames: ["my-web"] },
      readConcurrency: 8,
      validateRelationships: false
  }
  ```



---

#### 三、返回值与性能优化
**YIELD 字段**：
- `graphName`：投影图的名称（与输入参数一致）
- `nodeCount`：加载的节点总数
- `relationshipCount`：加载的关系总数

**优化策略**：
1. **索引加速**：为 `nodeQuery` 中的筛选条件（如 `AppName`）创建索引
2. **内存控制**：使用 `gds.graph.create.cypher.estimate` 预估算内存消耗（避免OOM）
3. **动态参数化**：通过 `parameters` 配置复用查询模板，提升灵活性
4. **批量写入**：在事务外执行投影操作，减少内存锁竞争

---

#### 四、典型错误与规避方案
1. **空图错误**：
   - **现象**：`Failed to load a relationship because...`
   - **根因**：`nodeQuery` 未覆盖关系的端点节点
   - **解决**：启用 `validateRelationships: false` 或调整节点查询逻辑

2. **版本兼容性**：
   - **Neo4j 4.1.1 异常**：升级到最新补丁版本
   - **Legacy Cypher 迁移**：使用新语法 `gds.graph.cypher.project`

3. **属性缺失**：
   - **处理技巧**：使用 `coalesce(r.weight, 1.0)` 设置默认值

---

#### 五、应用场景对比

| 场景特征               | 适用方法               | 优势                              | 局限                     |
|------------------------|------------------------|-----------------------------------|--------------------------|
| 动态子图筛选           | Cypher投影             | 灵活定义节点/关系逻辑             | 内存消耗较高        |
| 全图或固定模式         | 原生投影（Native）     | 加载速度快，适合生产环境          | 灵活性低      |
| 复杂属性处理           | 混合投影（Cypher+Native） | 结合两种优势（需GDS 2.0+）       | 配置复杂度高       |


---

#### 六、完整示例
```cypher
// 动态创建包含过滤条件的图投影
CALL gds.graph.project.cypher(
    'filteredGraph',
    // 节点查询：筛选AppName在列表中的i_app节点及其关联节点
    'MATCH (n:i_app) WHERE n.name IN $apps
     CALL apoc.path.expandConfig(n, {maxLevel:2}) 
     UNWIND nodes(path) AS node 
     RETURN DISTINCT id(node) AS id, labels(node) AS labels',
    // 关系查询：捕获所有关联关系（含方向）
    'MATCH (a)-[r]->(b) 
     WHERE a.name IN $apps AND b.name IN $apps 
     RETURN id(a) AS source, id(b) AS target, type(r) AS type',
    // 配置参数：动态传入应用名列表
    { parameters: { apps: ['my-web'] } }
)
YIELD nodeCount, relationshipCount;

// 执行PageRank算法
CALL gds.pageRank.stream('filteredGraph')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).id AS nodeId, score
ORDER BY score DESC LIMIT 10;
```

**设计要点**：
- 使用 `apoc.path.expandConfig` 实现深度控制
- 通过 `DISTINCT` 避免重复节点
- 参数化提升查询复用性

## 将刚才符合我要求 i_app 开始，pageRank 排序的 cypher 代码，调整为 neo4j driver java 代码实现方法，其中 appNames 作为方法的参数。

### 基于 Java Driver 的 PageRank 动态图计算实现方案

#### 一、需求解读
需将原有 Cypher 查询转换为 Java 代码，实现以下功能：
1. **动态参数传递**：`appNames` 作为方法参数传入
2. **内存图投影**：通过 Cypher 动态构建子图（i_app 起始，最大 2 层）
3. **算法执行**：运行 PageRank 并返回 Top9 结果
4. **资源管理**：符合 Driver API 规范（连接池、事务、异常处理）

---

#### 二、核心实现逻辑分解

```mermaid
flowchart TD
    A[初始化Driver] --> B[创建内存图投影]
    B --> C[执行PageRank算法]
    C --> D[提取Top9结果]
    D --> E[资源释放]
```


---

#### 三、完整 Java 实现代码
```java
import org.neo4j.driver.*;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class PageRankService implements AutoCloseable {
    private final Driver driver;

    // 初始化Driver（建议使用连接池）
    public PageRankService(String uri, String user, String password) {
        driver = GraphDatabase.driver(uri, AuthTokens.basic(user, password));
    }

    // 主业务方法（appNames为动态参数）
    public List<Record> calculateTopPageRankNodes(List<String> appNames) {
        try (Session session = driver.session()) {
            // 步骤1：创建Cypher投影
            createGraphProjection(session, appNames);
            
            // 步骤2：执行PageRank算法
            return session.executeRead(tx -> {
                Result result = tx.run(
                    "CALL gds.pageRank.stream('myGraph') "
                    + "YIELD nodeId, score "
                    + "RETURN gds.util.asNode(nodeId) AS node, score "
                    + "ORDER BY score DESC LIMIT 9");
                
                return result.list();
            });
        }
    }

    // 创建动态图投影（私有方法封装）
    private void createGraphProjection(Session session, List<String> appNames) {
        String nodeQuery = 
            "MATCH (start:i_app) WHERE start.name IN $appNames " +
            "CALL apoc.path.expandConfig(start, { " +
            "    relationshipFilter: '>', " +
            "    labelFilter: '>i_vm|i_phy', " +
            "    minLevel: 1, " +
            "    maxLevel: 2, " +
            "    uniqueness: 'NODE_GLOBAL' " +
            "}) YIELD path " +
            "UNWIND nodes(path) AS node " +
            "RETURN DISTINCT id(node) AS id, labels(node) AS labels";

        String relQuery = 
            "MATCH (start:i_app) WHERE start.name IN $appNames " +
            "CALL apoc.path.expandConfig(start, { " +
            "    relationshipFilter: '>', " +
            "    labelFilter: '>i_vm|i_phy', " +
            "    minLevel: 1, " +
            "    maxLevel: 2, " +
            "    uniqueness: 'NODE_GLOBAL' " +
            "}) YIELD path " +
            "UNWIND relationships(path) AS rel " +
            "RETURN DISTINCT id(startNode(rel)) AS source, " +
            "id(endNode(rel)) AS target, type(rel) AS type";

        // 执行Cypher投影（带参数）
        session.run(
            "CALL gds.graph.project.cypher($graphName, $nodeQuery, $relQuery, {parameters: {appNames: $appNames}})",
            Values.parameters(
                "graphName", "myGraph",
                "nodeQuery", nodeQuery,
                "relQuery", relQuery,
                "appNames", appNames
            )
        ).consume(); // 显式消费结果确保执行
    }

    @Override
    public void close() throws Exception {
        driver.close();
    }

    // 示例用法
    public static void main(String[] args) {
        try (PageRankService service = new PageRankService("bolt://localhost:7687", "neo4j", "password")) {
            List<Record> topNodes = service.calculateTopPageRankNodes(List.of("my-web"));
            topNodes.forEach(record -> 
                System.out.println(record.get("node").asNode() + " Score: " + record.get("score")));
        }
    }
}
```


---

#### 四、关键实现细节解析

1. **参数化传递**：
   - 使用 `$appNames` 占位符注入参数（避免 SQL 注入）(#evidence1)
   - 通过 `Values.parameters()` 构建参数映射，支持 List/Map 等复杂类型

2. **资源管理**：
   - 实现 `AutoCloseable` 接口自动关闭 Driver(#evidence8)
   - 使用 `try-with-resources` 管理 Session 生命周期

3. **投影创建优化**：
   - 将节点/关系查询拆分为独立字符串，提高可维护性
   - `session.run().consume()` 确保操作原子性(#evidence15)

4. **结果处理**：
   - `executeRead` 用于只读操作，提升并发性能(#evidence3)
   - 使用 `gds.util.asNode()` 直接获取节点对象，减少二次查询

---

#### 五、性能优化建议

1. **索引加速**：
   ```java
   // 在应用启动时创建索引
   session.run("CREATE INDEX FOR (n:i_app) ON (n.name)").consume();
   ```


2. **批量处理**：
   ```java
   // 分批次处理大型appNames列表
   List<List<String>> batches = Lists.partition(appNames, 100);
   batches.forEach(batch -> calculateTopPageRankNodes(batch));
   ```


3. **内存控制**：
   ```java
   // 添加内存估算检查
   Result result = session.run(
       "CALL gds.graph.project.cypher.estimate($nodeQuery, $relQuery) " +
       "YIELD requiredMemory", params);
   String memory = result.single().get("requiredMemory").asString();
   ```


---

#### 六、异常处理策略

1. **超时机制**：
   ```java
   session.withConfig(QueryConfig.builder().withTimeout(Duration.ofSeconds(30)))
   ```


2. **重试逻辑**：
   ```java
   int retries = 3;
   while (retries-- > 0) {
       try {
           return calculateTopPageRankNodes(appNames);
       } catch (ServiceUnavailableException e) {
           Thread.sleep(1000);
       }
   }
   ```


---

#### 七、扩展性设计

1. **多算法支持**：
   ```java
   public enum Algorithm {
       PAGERANK("gds.pageRank.stream"), 
       BETWEENNESS("gds.betweenness.stream");

       private final String procedure;
   }
   ```


2. **动态结果映射**：
   ```java
   public <T> List<T> mapResult(Result result, Function<Record, T> mapper) {
       return result.stream().map(mapper).collect(Collectors.toList());
   }
   ```


---

#### 八、生产环境注意事项

1. **连接池配置**：
   ```java
   Config config = Config.builder()
       .withConnectionPoolSize(10)
       .withMaxConnectionLifetime(30, TimeUnit.MINUTES)
       .build();
   driver = GraphDatabase.driver(uri, AuthTokens.basic(user, password), config);
   ```


2. **监控集成**：
   ```java
   MetricsAdapter metrics = new MetricsAdapter();
   driver.metrics(metrics); // 对接Micrometer/Prometheus
   ```


---

以上实现方案充分利用了 Java Driver 的参数化查询、资源自动管理等特性(#evidence1)(#evidence8)，同时结合 GDS 库的内存图计算能力(#evidence4)，构建了一个高效、可维护的动态 PageRank 计算服务。通过方法参数化、异常重试、监控集成等设计，可满足生产环境复杂场景需求。

# 参考资料

https://github.com/neo4j/graph-data-science


* any list
{:toc}


