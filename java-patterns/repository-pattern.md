# Repository Pattern (RDBMS)

> **Goal**: Abstracting the persistence logic from the domain logic.

## The Problem

Your business logic (Service Layer) should not know you are using SQL. It should speak in "Objects."

## The Pattern

The Repository acts as an in-memory collection of objects.

### Java Interface

```java
public interface UserRepository {
    User save(User user);
    Optional<User> findById(String id);
    List<User> findAllActive();
}
```

### Spring Data JPA Implementation

In Spring, you rarely write the implementation manually.

```java
@Repository
public interface JpaUserRepository extends JpaRepository<UserEntity, Long> {

    // Spring generates the SQL automatically based on method name
    @Query("SELECT u FROM UserEntity u WHERE u.status = 'ACTIVE'")
    List<UserEntity> findAllActive();
}
```

### Usage in Service

```java
@Service
public class UserService {
    private final UserRepository userRepo;

    public void register(User user) {
        // Business Rule: Check email
        if (user.getEmail().contains("@spam.com")) {
            throw new ValidationException("No spam allowed");
        }
        // Save without writing SQL
        userRepo.save(user);
    }
}
```
