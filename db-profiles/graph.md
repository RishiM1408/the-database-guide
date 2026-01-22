# Graph Databases (Neo4j)

> **The Networkers**: Optimized for traversing highly connected data.

## ⚙️ How it Works (Index-Free Adjacency)

In a Relational DB, checking "Friends of Friends" implies a `JOIN` operation, which does an Index Lookup ($O(log n)$) for every friend.
In a Graph DB (like Neo4j), a Node physically contains the **memory address** of its relationships.

- **Traversal**: Moving from Node A to Node B is a pointer dereference ($O(1)$).
- **Impact**: Performance remains constant even as the dataset grows, provided the _depth_ of the traversal is constant.

## 🚀 Options

| Database           | Model                  | Best For                                      |
| :----------------- | :--------------------- | :-------------------------------------------- |
| **Neo4j**          | Labeled Property Graph | **The Standard**. Rich Cypher query language. |
| **Amazon Neptune** | RDF / Property Graph   | AWS Managed ecosystems.                       |
| **TigerGraph**     | Native Parallel Graph  | Massive analytics/Analytics on Graphs.        |

## 🏗️ internal Structures

- **Node Store**: Fixed-size records (e.g., 15 bytes). ID, pointer to first relationship, pointer to first property.
- **Relationship Store**: Fixed-size records. ID, Start Node, End Node, Type, pointer to next relationship.
- **Doubly Linked Lists**: Relationships form a linked list for each node.

## ☕ Java Integration (Neo4j OGM)

Graph DBs are often used with an Object Graph Mapper (OGM).

```java
@NodeEntity
public class Person {
    @Id @GeneratedValue private Long id;
    private String name;

    @Relationship(type = "FRIEND_OF", direction = Relationship.UNDIRECTED)
    private Set<Person> friends;
}

// Repository
public interface PersonRepository extends Neo4jRepository<Person, Long> {

    // Cypher Query Language
    @Query("MATCH (p:Person {name: $name})-[:FRIEND_OF*2]-(fof) RETURN fof")
    List<Person> getFriendsOfFriends(String name);
}
```
