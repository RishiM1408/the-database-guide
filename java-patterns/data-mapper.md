# Data Mapper (MongoDB)

> **Goal**: Translating between the messy Document world (JSON) and the clean Object world (Java).

## The Problem

MongoDB stores BSON (Binary JSON). Java needs strictly typed Objects.

## The Pattern

A Data Mapper handles the bidirectional conversion, often dealing with "Impedance Mismatch" (e.g., how to store Enums, Dates, or Polymorphism).

### Example (Custom Mapper Logic)

```java
public class UserMapper {

    public User toDomain(Document doc) {
        User u = new User();
        u.setId(doc.getObjectId("_id").toHexString());
        u.setName(doc.getString("username"));
        // Complex logic: Convert String List to Enum Set
        List<String> roles = doc.getList("roles", String.class);
        u.setRoles(roles.stream().map(Role::valueOf).collect(Collectors.toSet()));
        return u;
    }

    public Document toDocument(User user) {
        Document doc = new Document();
        if (user.getId() != null) {
            doc.append("_id", new ObjectId(user.getId()));
        }
        doc.append("username", user.getName());
        doc.append("roles", user.getRoles().stream().map(Enum::name).toList());
        return doc;
    }
}
```
