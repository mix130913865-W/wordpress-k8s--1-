# WordPress Helm Chart

這是一個基於 Helm 的 WordPress Chart，內建 MySQL，支援：

- WordPress 前端 Deployment + PVC
- MySQL Deployment + PVC + Secret
- Frontend / Backend Service
- Ingress（可選 TLS）
- 可透過 `values.yaml` 配置資源、儲存與密碼

###
```
wordpress-chart/
├── Chart.yaml              # Chart 的元數據（包含版本號、名稱、描述等）
├── values.yaml             # 所有的配置參數（如 Image 版本、密碼、PVC 大小等預設值）
├── .helmignore             # 類似 .gitignore，打包時排除不需要的檔案
├── README.md               # 專案說明文件
└── templates/              # K8s 資源模板目錄 (會引用 values.yaml 的參數)
    │
    ├── NOTES.txt           # 安裝完成後顯示在終端機的指示（例如：如何取得登入網址）
    ├── _helpers.tpl        # 定義可重複使用的模板片段（如名稱與標籤計算邏輯）
    │
    ├── [ WordPress 應用層 ]
    ├── wordpress-deployment.yaml # 定義 WordPress Pod 的執行方式與容器設定
    ├── wordpress-service.yaml    # 定義 WordPress 的內部存取點 (ClusterIP)
    ├── wordpress-ingress.yaml    # 定義外部流量如何進入（如 Domain 設定）
    ├── wordpress-pvc.yaml        # 請求儲存空間，用於存取上傳的圖片與外掛
    ├── wordpress-secret.yaml     # 儲存機密資訊（如資料庫連線密碼、管理員密碼）
    │
    └── [ MySQL 資料庫層 ]
        ├── mysql-deployment.yaml # 定義 MySQL Pod 的執行方式
        ├── mysql-service.yaml    # 讓 WordPress 能透過穩定名稱找到資料庫
        └── mysql-pvc.yaml        # 請求儲存空間，確保資料庫數據不會因重啟而丟失
```
###
```
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
```
