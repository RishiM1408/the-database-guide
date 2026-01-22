# CQRS (Command Query Responsibility Segregation)

> **Goal**: Optimizing for conflicting Read vs. Write requirements (crucial for Cassandra).

## The Problem

- **Write Model**: Needs to validate rules and persist data quickly.
- **Read Model**: Needs to query data in _many different shapes_ (by ID, by Name, by Date), which Cassandra _cannot_ do on a single table.

## The Pattern

Split the system into COMMANDS (Writes) and QUERIES (Reads).

### 1. The Command Side (Write)

```java
public class ChatCommandHandler {
    public void handle(PostMessageCommand cmd) {
        // Validation
        if (cmd.text().isEmpty()) throw new IllegalArgumentException();

        // Write to MULTIPLE tables (Denormalization)
        cassandra.executeBatch(
            "INSERT INTO messages_by_id ...",
            "INSERT INTO messages_by_room ...",
            "INSERT INTO messages_by_user ..."
        );
    }
}
```

### 2. The Query Side (Read)

The Query side acts as a thin layer that selects from the _pre-optimized_ table.

```java
public class ChatQueryHandler {

    // Query 1: Need history for a room? Use table optimized for that.
    public List<Message> getRoomHistory(String roomId) {
        return cassandra.select("SELECT * FROM messages_by_room WHERE room_id = ?", roomId);
    }

    // Query 2: Need user history? Use the other table.
    public List<Message> getUserHistory(String userId) {
        return cassandra.select("SELECT * FROM messages_by_user WHERE user_id = ?", userId);
    }
}
```
