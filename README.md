# 🧁 Config Server

![Java](https://img.shields.io/badge/Java-21%2B-orange.svg)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)

Welcome to the **Config Server**, a core component of the Shah's Bakery Microservice Platform.

## 📑 Table of Contents
- [Features](#-features)
- [Folder Structure](#-folder-structure)
- [Dependencies](#-dependencies)
- [Endpoints](#-endpoints)
- [How to Run](#-how-to-run)
- [Related Links](#-related-links)

## ✨ Features
- Centralized configuration management for all microservices.
- Dynamic loading of properties from a remote Git repository (`config-repo`).
- Seamless updates to- Secure exposure of configuration data to clients.

## 📁 Folder Structure
The main `src/main/java` directory is organized as follows:
```text
src/
└── main/
    └── java/org/devofblue/configserver/
        └── ConfigServerApplication.java # The main Spring Boot entry point with @EnableConfigServer.
```

## 🛠️ Dependencies
- **Framework:** Spring Boot, Spring Cloud Config
- **Key Modules:** Eureka Client, Spring Cloud Config Server

## 🌐 Endpoints
> [!NOTE]
> The Config Server serves property files internally to microservices rather than exposing typical public REST APIs.

- `GET /{application}/{profile}` - Retrieves configuration properties for a specific service and environment profile.

## 🚀 How to Run

1. **Clone the repository:**
   ```bash
   git clone https://github.com/amankrmj09/bakery-config-servers.git
   cd config-server
   ```

2. **Configure Environment:**
   Ensure your `.env` or `application.yml` properties correctly point to the `config-repo` directory or repository URL.

3. **Run the application:**
   ```bash
   ./gradlew bootRun
   ```

## 🔗 Related Links
- [Main Platform README](../README.md)
