# API Reference: Config Server

The Config Server exposes standard Spring Cloud Config HTTP endpoints to serve environment-specific configuration files to client microservices, along with Spring Boot Actuator management endpoints.

> [!NOTE]
> This service is a standard Spring Cloud Config Server and uses framework-provided endpoints. Consequently, there are no custom request/response DTO or controller classes to reference in this document.


## Base URL
`http://localhost:8081` (or as specified by `SERVER_PORT`)

---

## Configuration Retrieval Endpoints

### 1. Retrieve Environment Object (JSON)
Fetches configuration properties for a specific service application, profile, and optional Git label (branch, tag, or commit hash).

* **URL Patterns:**
  - `GET /{application}/{profile}`
  - `GET /{application}/{profile}/{label}`
* **Path Parameters:**
  - `application`: Name of the client application (e.g., `inventory-service`, `order-service`, `application`)
  - `profile`: Active profile (e.g., `default`, `dev`, `test`, `prod`)
  - `label`: *(Optional)* Git branch, tag, or commit ID (default: `main`)
* **Headers:** `Accept: application/json`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
{
  "name": "inventory-service",
  "profiles": [
    "dev"
  ],
  "label": "main",
  "version": "c1f7a0b3c82d",
  "state": null,
  "propertySources": [
    {
      "name": "https://github.com/amankrmj09/bakery-config-repo.git/inventory-service-dev.yaml",
      "source": {
        "server.port": 8082,
        "spring.datasource.url": "jdbc:postgresql://localhost:5432/inventory_db"
      }
    },
    {
      "name": "https://github.com/amankrmj09/bakery-config-repo.git/application.yaml",
      "source": {
        "eureka.client.service-url.defaultZone": "http://localhost:8761/eureka/"
      }
    }
  ]
}
```
* **Success HTTP Status:** 200 OK
* **Error Responses:**
  - `404 Not Found`: Configuration for requested application/profile not found.
  - `500 Internal Server Error`: Git repository access error.

---

### 2. Retrieve Configuration in YAML Format
Returns merged configuration directly in YAML format.

* **URL Patterns:**
  - `GET /{application}-{profile}.yml`
  - `GET /{application}-{profile}.yaml`
  - `GET /{label}/{application}-{profile}.yml`
  - `GET /{label}/{application}-{profile}.yaml`
* **Path Parameters:**
  - `application`: Name of the client application
  - `profile`: Active profile
  - `label`: Git branch/tag/commit (default: `main`)
* **Response Format:** `text/yaml`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
{
  "server": {
    "port": 8082
  },
  "spring": {
    "datasource": {
      "url": "jdbc:postgresql://localhost:5432/inventory_db"
    }
  },
  "eureka": {
    "client": {
      "service-url": {
        "defaultZone": "http://localhost:8761/eureka/"
      }
    }
  }
}
```
* **Response Example (YAML 200 OK):**
```yaml
server:
  port: 8082
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/inventory_db
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```
* **Success HTTP Status:** 200 OK
* **Error Responses:**
  - `404 Not Found`: Configuration not found.
  - `500 Internal Server Error`: Git access failure.

---

### 3. Retrieve Configuration in Properties Format
Returns merged configuration formatted as Java `.properties`.

* **URL Patterns:**
  - `GET /{application}-{profile}.properties`
  - `GET /{label}/{application}-{profile}.properties`
* **Path Parameters:**
  - `application`: Name of the client application
  - `profile`: Active profile
  - `label`: Git branch/tag/commit (default: `main`)
* **Response Format:** `text/plain`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
{
  "eureka.client.service-url.defaultZone": "http://localhost:8761/eureka/",
  "server.port": 8082,
  "spring.datasource.url": "jdbc:postgresql://localhost:5432/inventory_db"
}
```
* **Response Example (Properties 200 OK):**
```properties
eureka.client.service-url.defaultZone=http://localhost:8761/eureka/
server.port=8082
spring.datasource.url=jdbc:postgresql://localhost:5432/inventory_db
```
* **Success HTTP Status:** 200 OK
* **Error Responses:**
  - `404 Not Found`: Configuration not found.
  - `500 Internal Server Error`: Git access failure.

---

### 4. Retrieve Plain Text / Raw Files
Retrieves unparsed raw config files (e.g., JSON schemas, SQL scripts, certificates) stored in the Git repository.

* **URL Patterns:**
  - `GET /{application}/{profile}/{label}/{path}`
* **Path Parameters:**
  - `application`: Application name scope
  - `profile`: Profile scope
  - `label`: Git branch or commit label
  - `path`: Relative path to file in repo (e.g., `db/schema.sql`, `certs/public.pem`)
* **Response Format:** Raw file content (matching mime-type)
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
"SELECT * FROM users;"
```
* **Success HTTP Status:** 200 OK
* **Error Responses:**
  - `404 Not Found`: File or repository path does not exist.
  - `500 Internal Server Error`: Git repository connectivity error.

---

## Actuator & Monitoring Endpoints

### 1. Health Endpoint
Checks status of Config Server and Git repository connectivity.

* **URL:** `GET /actuator/health`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
{
  "status": "UP",
  "components": {
    "configServer": {
      "status": "UP",
      "details": {
        "repositories": [
          {
            "name": "app",
            "profiles": ["default"],
            "label": "main"
          }
        ]
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```
* **Success HTTP Status:** 200 OK
* **Error Responses:**
  - `503 Service Unavailable`: Config server or Git repository backend is unreachable.

---

### 2. Information Endpoint
Provides build and application metadata.

* **URL:** `GET /actuator/info`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
{}
```
* **Success HTTP Status:** 200 OK

---

### 3. Prometheus Metrics Endpoint
Exposes operational metrics in Prometheus text format.

* **URL:** `GET /actuator/prometheus`
* **Response Format:** `text/plain; version=0.0.4`
* **Request Body (JSON Example):**
```json
{}
```
* **Response Body (JSON Example):**
```json
"jvm_memory_used_bytes{area=\"heap\",id=\"G1 Survivor Space\",} 1048576.0"
```
* **Success HTTP Status:** 200 OK
