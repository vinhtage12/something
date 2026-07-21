## 1. The `undefined/unknown` Log Myth

* **The Log:** `org.hibernate.orm.connections.pooling : HHH10001005: Database info... Autocommit mode: undefined/unknown...`
* **The Reality:** This is a **harmless informational log**, not an error.
* **Why it happens:** Hibernate prints this log during early startup *before* HikariCP has opened its first real connection. Hibernate cannot see inside HikariCP's configuration at this exact millisecond, so it defaults to printing `undefined`. Your settings **are** being applied behind the scenes.
* **How to mute it:**
```yaml
logging.level.org.hibernate.orm.connections.pooling: WARN

```
---
## 2. How to Verify Your Real Pool Settings

To prove your custom pool sizes and autocommit settings are actually working, enable `DEBUG` logging for HikariCP.

* **The Configuration:**
```yaml
logging:
  level:
    com.zaxxer.hikari: DEBUG

```


* **The Output:** On startup, look for `com.zaxxer.hikari.HikariConfig - configuration:`. It will print the true values of your pool (e.g., `maximumPoolSize..........................10`).
---
## 3. PostgreSQL Prerequisites
1. **Dependency:** You must include the PostgreSQL driver in your `pom.xml` (Maven) or `build.gradle` (Gradle).
2. **Manual DB Creation:** PostgreSQL **will not** automatically create the database if it doesn't exist. You must create it manually via `psql` or pgAdmin before starting the app.
3. **Dialect:** In Spring Boot 3+ (Hibernate 6+), you **do not** need to manually define the SQL Dialect. Hibernate automatically detects it from the JDBC URL.
---
## 4. Master Configuration Cheat Sheet
Here is the complete, production-ready configuration block with quick-reference notes for each property.
### `application.yml`

```yaml
spring:
  datasource:
    # --- CORE SETTINGS ---
    url: jdbc:postgresql://localhost:5432/your_database_name # DB Location
    username: your_postgres_user                           # DB Login
    password: your_postgres_password                       # DB Password
    driver-class-name: org.postgresql.Driver               # Java-to-Postgres translator

    # --- HIKARI CONNECTION POOL SETTINGS ---
    hikari:
      maximum-pool-size: 10       # Max active connections allowed at once
      minimum-idle: 2             # Minimum standby connections kept warm
      auto-commit: false          # 'false' lets Spring manage multi-statement transactions
      connection-timeout: 30000   # Max time (ms) a request waits for a free connection (30s)
      idle-timeout: 600000        # Time (ms) an unused connection stays alive before closing (10m)
      max-lifetime: 1800000       # Maximum total life of any connection in the pool (30m)

  # --- JPA / HIBERNATE SETTINGS ---
  jpa:
    hibernate:
      ddl-auto: update            # 'update' for Dev (auto-syncs tables). Use 'validate' for Prod!
    show-sql: true                # Prints raw SQL queries to the console log
    properties:
      hibernate:
        format_sql: true          # Formats printed SQL into highly readable, indented blocks

```
### `application.properties` (Alternative Format)

```properties
# Core Settings
spring.datasource.url=jdbc:postgresql://localhost:5432/your_database_name
spring.datasource.username=your_postgres_user
spring.datasource.password=your_postgres_password
spring.datasource.driver-class-name=org.postgresql.Driver

# Hikari Connection Pool Settings
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=2
spring.datasource.hikari.auto-commit=false
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000

# JPA / Hibernate Settings
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

```
## Refer for read more
[How Spring Boot Optimizes Database Connections Through HikariCP](https://medium.com/@AlexanderObregon/how-spring-boot-optimizes-database-connections-through-hikaricp-f19be1a246d7)
