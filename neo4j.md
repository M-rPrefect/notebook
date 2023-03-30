# CQL

CQL代表Cypher查询语言。

它是一种声明性模式匹配语言

它遵循SQL语法

| CQL命令  | 用法                         |
| -------- | ---------------------------- |
| CREATE   | 创建节点，关系和属性         |
| MATCH    | 检索有关节点，关系和属性数据 |
| RETURN   | 返回查询结果                 |
| WHERE    | 提供条件过滤检索数据         |
| DELETE   | 删除节点和关系               |
| REMOVE   | 删除节点和关系的属性         |
| ORDER BY | 排序检索数据                 |
| SET      | 添加或更新标签               |

创建一个节点

```cassandra
create (n:person) return n
```

创建一个关系

```cassandra
MATCH
  (a:Person),
  (b:Person)
WHERE a.name = 'A' AND b.name = 'B'
CREATE (a)-[r:RELTYPE]->(b)
RETURN type(r)
```

删除一个节点

```cassandra
MATCH (n:Person {name: 'UNKNOWN'})
DELETE n
```

删除一个节点以及所有关系

```cassandra
MATCH (n {name: 'Andy'})
DETACH DELETE n
```

仅删除一个关系

```
MATCH (n {name: 'Andy'})-[r:KNOWS]->()
DELETE r
```

