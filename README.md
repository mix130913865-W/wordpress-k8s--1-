# WordPress Helm Chart

這是一個基於 Helm 的 WordPress Chart，內建 MySQL，支援：

- WordPress 前端 Deployment + PVC
- MySQL Deployment + PVC + Secret
- Frontend / Backend Service
- Ingress（可選 TLS）
- 可透過 `values.yaml` 配置資源、儲存與密碼

       [ 外部流量 (Users) ]
                |
                v
+-------------------------------+
|       Ingress (Nginx)         | <--- 管理 Domain & SSL (TLS)
+---------------+---------------+
                |
          HTTP / HTTPS
                |
                v
+-------------------------------+
|       WordPress Service       | <--- 穩定的內部 IP (ClusterIP)
+---------------+---------------+
                |
            Selector
                |
                v
+-------------------------------+       +--------------------------+
|     WordPress Deployment      | <---  |      Shared Secret       |
|  - Pods (WP Engine)           | <---  | (DB_USER, DB_PASSWORD)   |
|  - PVC (wp-content uploads)   |       +------------+-------------+
+---------------+---------------+                    |
                |                                    |
          TCP (Port 3306)                            | 注入環境變數
          Connect to DNS:                            |
          "mysql-service"                            |
                |                                    |
                v                                    |
+-------------------------------+                    |
|         MySQL Service         |                    |
+---------------+---------------+                    |
                |                                    |
            Selector                                 |
                |                                    |
                v                                    |
+-------------------------------+                    |
|       MySQL StatefulSet       | <------------------+
|  - Pod (Database Engine)      |
|  - PVC (Database Files)       |
+-------------------------------+
