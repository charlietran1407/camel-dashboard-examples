# Camel Dashboard Examples

This repository contains a collection of Apache Camel examples ranging from beginner integrations to advanced microservices and AI-driven workflows. All examples are designed to integrate seamlessly with the **Camel Dashboard**.

---

## 🚀 Getting Started

> [!IMPORTANT]
> **Prerequisite:** You MUST start the core infrastructure services (database, cache, tracing, collector, and dashboard) via Docker Compose **before** working on or deploying any of the examples in this repository.

### Step 1: Start the Infrastructure
From the root directory of this repository, run the following command to start all services in the background:

```bash
docker compose -p camel-dashboard up -d
```

This will automatically spin up and configure the following services:

| Service Name | Port | Description |
| :--- | :--- | :--- |
| **Camel Dashboard** | `8081` | The main management and UI dashboard for Apache Camel. |
| **PostgreSQL** | `5432` | Relational database used for storing route/dashboard state and examples. |
| **Redis** | `6379` | In-memory cache and stream broker for clustered event synchronization. |
| **Jaeger** | `16686` | Distributed tracing UI to monitor trace spans of your Camel routes. |
| **OpenTelemetry Collector** | `4317` / `4318` | Collector for application logs, metrics, and tracing spans. |
| **Adminer** | `8383` | A web-based database management tool to inspect the PostgreSQL DB. |

To verify that the dashboard is up and healthy, open your browser and navigate to:
👉 **[http://localhost:8081/cameldash](http://localhost:8081/cameldash)**

---

## 📂 Repository Structure

Once the Docker Compose environment is healthy, you can explore the following categories of examples:

*   **[Beginner](./Beginner)**: Easy-to-understand Camel integrations showing REST endpoints, JSON transformations, Exception handling, Timers, and File watchers.
*   **[Advanced](./Advanced)**: Advanced scenarios including API Gateway setup with Consul-based service discovery and dynamic routing.
*   **AI & LLM Integration**:    
    *   **[langchain4j-chat](./langchain4j-chat)**: Interactive AI chat service integration.
    *   **[langchain4j-rag](./langchain4j-rag)**: Retrieval-Augmented Generation (RAG) with vector search.    
---

## 🇻🇳 Hướng dẫn nhanh (Vietnamese)

> [!IMPORTANT]
> **Điều kiện tiên quyết:** Bạn **PHẢI** khởi động môi trường Docker Compose (bao gồm database, cache, tracing, và dashboard) ở thư mục gốc trước khi thực hành hoặc triển khai bất kỳ ví dụ nào trong repository này.

### Khởi động môi trường hạ tầng:
Mở terminal tại thư mục gốc của repository này và chạy:

```bash
docker compose -p camel-dashboard up -d
```

Các dịch vụ sẽ được chạy tại các cổng tương ứng:
*   **Camel Dashboard**: [http://localhost:8081/cameldash](http://localhost:8081/cameldash) (Giao diện giám sát chính)
*   **PostgreSQL**: Cổng `5432` (Lưu trữ dữ liệu)
*   **Redis**: Cổng `6379` (Đồng bộ hóa cache & stream event)
*   **Jaeger**: [http://localhost:16686](http://localhost:16686) (Giám sát Distributed Tracing)
*   **Adminer**: [http://localhost:8383](http://localhost:8383) (Giao diện quản lý Database)