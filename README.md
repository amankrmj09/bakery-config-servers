# Spring Cloud Config Server

Centralized Configuration Server for Bakery Microservices built with Spring Boot and Spring Cloud Config Server.

## 1. Role
The Config Server acts as a centralized configuration manager for all microservices in the Bakery distributed system. It serves configuration properties across all environments (development, staging, production). By pulling configuration files directly from the centralized remote Git repository (`bakery-config-repo`), it ensures that configurations are externalized, version-controlled, and consistently applied to microservices without requiring code changes or application rebuilds.

## 2. Architecture
At startup, each microservice (the Config Client) connects to the Config Server before initializing its ApplicationContext. The microservice requests its specific configuration based on its application name and active profile (e.g., `inventory-service-dev.yaml`). The Config Server fetches these properties from the backed Git repository (`https://github.com/amankrmj09/bakery-config-repo.git`) and serves them to the client microservice.

## 3. Directory Structure

```text
config-server/
├── .env
├── .env.example
├── .gitattributes
├── .gitignore
├── API_REFERENCE.md
├── build.gradle.kts
├── Dockerfile
├── gradle.properties
├── gradlew
├── gradlew.bat
├── README.md
├── settings.gradle.kts
└── src/
    ├── main/
    │   ├── java/com/blubugtech/configserver/ConfigServerApplication.java
    │   └── resources/
    │       ├── application.yaml
    │       └── logback-spring.xml
    └── test/
        └── java/com/blubugtech/configserver/ConfigServerApplicationTests.java
```

## 4. Configuration Properties
Key properties defined in [`./src/main/resources/application.yaml`](./src/main/resources/application.yaml):

- **Port:** Configured via `SERVER_PORT` (default: `8081`).
- **Git Repository URI:** Configured to pull from `https://github.com/amankrmj09/bakery-config-repo.git`.
- **Authentication:** Authentication to private Git repositories uses `GITHUB_ACTOR` and `GITHUB_TOKEN`.
- **Refresh Rate:** 60 seconds config refresh check interval.

### Environment Variables
| Variable | Description | Default Value |
| --- | --- | --- |
| `SERVER_PORT` | HTTP port for the Config Server | `8081` |
| `GITHUB_ACTOR` | GitHub username/actor for Git auth | Required for private repos |
| `GITHUB_TOKEN` | Personal Access Token or GitHub token | Required for private repos |

## 5. Running Locally

### Prerequisites
- JDK 25 installed
- Git configured and repository access granted

### Commands

1. **Clone & Set Up Environment Variables:**
   Copy `.env.example` to `.env` and populate your credentials:
   ```bash
   cp .env.example .env
   ```

2. **Run Application using Gradle Wrapper:**
   On Linux/macOS:
   ```bash
   ./gradlew bootRun
   ```
   On Windows (PowerShell/CMD):
   ```cmd
   .\gradlew.bat bootRun
   ```

3. **Build Application JAR:**
   ```bash
   ./gradlew build
   ```

4. **Run using Docker:**
   ```bash
   docker build -t bakery-config-server .
   docker run -p 8081:8081 -e GITHUB_ACTOR=your_user -e GITHUB_TOKEN=your_token bakery-config-server
   ```

5. **Verify Server:**
   Once running on `http://localhost:8081`, test by retrieving a configuration file:
   ```bash
   curl http://localhost:8081/application/default
   ```

## 🔗 Related Links

*For overall architecture, contribution guidelines, and security policies, please refer to the main [Blu's Bakery](https://github.com/amankrmj09/Blu_s_Bakery) repository.*

