# Spring Cloud Config Server

This repository contains the Spring Cloud Config Server for the Bakery application.

## 1. Role
The Config Server acts as a centralized configuration manager for all microservices in the distributed system. It is responsible for serving configuration properties across all environments. By pulling configuration files directly from the centralized `config-repo`, it ensures that configurations are externalized, version-controlled, and consistently applied to services without requiring code changes or application rebuilds.

## 2. Architecture
At boot time, each microservice (the Config Client) connects to the Config Server before initializing its own ApplicationContext. The microservice requests its specific configuration based on its application name and active profile (e.g., `application-name-dev.yml`). The Config Server fetches these properties from the backed version control system (`config-repo`) and serves them to the microservice. This allows the microservice to start with the correct, externalized configurations seamlessly.

## 3. Configuration Properties
The Config Server itself requires some initial configuration to run. This is typically defined in its `application.yml` (or via environment variables/`.env` file).

Key properties include:
- **Port:** The server runs on port `8888` by default (`server.port=8888`).
- **Git URI:** The location of the configuration repository. 
  Example `application.yml` configuration:
  ```yaml
  server:
    port: 8888
  spring:
    application:
      name: config-server
    cloud:
      config:
        server:
          git:
            uri: <URI_TO_YOUR_CONFIG_REPO> # e.g., file://${user.home}/config-repo or a GitHub URL
            default-label: main
  ```

## 4. Running Locally
To run the Config Server locally:

**Prerequisites:**
- Java Development Kit (JDK) installed
- Maven or Gradle installed
- The `config-repo` available locally or via remote URL.

**Steps:**
1. Navigate to the `config-server` directory.
2. Ensure the Git URI in `src/main/resources/application.yml` is pointing to your `config-repo`.
3. Build and run the application using Maven:
   ```bash
   ./mvnw spring-boot:run
   ```
   Or using Gradle:
   ```bash
   ./gradlew bootRun
   ```
4. The server should start on `http://localhost:8888`.
5. You can verify it's working by navigating to an endpoint for a specific configuration, for example: `http://localhost:8888/application/default`.
