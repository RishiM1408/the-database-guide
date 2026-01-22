# Resilience Patterns (Resilience4j)

> **The Reality**: Databases fail. Networks glitch.
> **The Goal**: Prevent a single database hiccup from taking down your entire application.

## 🛡️ Circuit Breaker Pattern

**The Problem**: If the DB is down, 10,000 requests/second will all pile up, consume threads, and crash your server (Cascading Failure).
**The Solution**: After X failures, "Open the Circuit" and fail fast immediately without calling the DB.

### Implementation (Resilience4j)

```java
// Configuration
CircuitBreakerConfig config = CircuitBreakerConfig.custom()
    .failureRateThreshold(50) // Open if 50% of requests fail
    .waitDurationInOpenState(Duration.ofMillis(1000)) // Wait 1s before trying again
    .permittedNumberOfCallsInHalfOpenState(2) // Allow 2 test calls
    .build();

CircuitBreaker circuitBreaker = CircuitBreaker.of("dbService", config);

// Usage
Supplier<User> decoratedSupplier = CircuitBreaker
    .decorateSupplier(circuitBreaker, () -> userRepository.findById(id));

Try.ofSupplier(decoratedSupplier)
    .recover(CallNotPermittedException.class, ex -> User.GUEST); // Fallback
```

---

## 🔄 Retry Pattern (With Backoff)

**The Problem**: A network blip causes a `ConnectionTimeout`.
**The Solution**: Try again. But wait a bit (Exponential Backoff).

### Implementation

```java
RetryConfig config = RetryConfig.custom()
    .maxAttempts(3)
    .waitDuration(Duration.ofMillis(100))
    .build();

Retry retry = Retry.of("dbService", config);

Supplier<User> retryingSupplier = Retry
    .decorateSupplier(retry, () -> userRepository.findById(id));
```

> **Warning**: Never use Retries on **Non-Idempotent** operations (like charging a credit card) unless you are 100% sure it failed before execution.
