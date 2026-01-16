# WordPress Helm Chart

這是一個基於 Helm 的 WordPress Chart，內建 MySQL，支援：

- WordPress 前端 Deployment + PVC
- MySQL Deployment + PVC + Secret
- Frontend / Backend Service
- Ingress（可選 TLS）
- 可透過 `values.yaml` 配置資源、儲存與密碼

          ┌───────────────┐
          │  Ingress      │
          │  (nginx)      │
          └──────┬────────┘
                 │ HTTP/HTTPS
                 ▼
          ┌───────────────┐
          │ WordPress     │
          │ Service       │
          └──────┬────────┘
                 │ selector
                 ▼
          ┌───────────────┐
          │ WordPress     │
          │ Deployment    │
          │  + PVC        │
          └──────┬────────┘
                 │ env (DB info from Secret)
                 ▼
          ┌───────────────┐
          │ MySQL Service │
          └──────┬────────┘
                 │ selector
                 ▼
          ┌───────────────┐
          │ MySQL         │
          │ Deployment    │
          │  + PVC        │
          │  + Secret     │
          └───────────────┘
