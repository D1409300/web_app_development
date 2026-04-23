# 流程圖：任務管理系統

本文件根據 `docs/PRD.md` 與 `docs/ARCHITECTURE.md`，視覺化呈現任務管理系統的使用者操作路徑（User Flow）以及系統資料互動流程（System Flow）。

## 1. 使用者流程圖（User Flow）

描述使用者從進入系統到執行各項操作的完整路徑：

```mermaid
flowchart LR
    Start([開啟系統]) --> Home[首頁：任務列表]
    Home --> Action{選擇操作}
    
    Action -->|新增任務| Create[填寫新增任務表單]
    Create -->|送出表單| Home
    
    Action -->|編輯任務| Edit[進入編輯頁面 / 彈出視窗]
    Edit -->|修改並送出| Home
    
    Action -->|刪除任務| Delete[確認並刪除任務]
    Delete --> Home
    
    Action -->|切換狀態| Toggle[標記為已完成 / 未完成]
    Toggle --> Home
```

## 2. 系統序列圖（Sequence Diagram）

以下序列圖以「**使用者新增任務**」為例，展示系統各元件間的資料互動過程：

```mermaid
sequenceDiagram
    actor User as 使用者
    participant Browser as 瀏覽器 (View)
    participant Flask as Flask 路由 (Controller)
    participant Model as Task Model
    participant DB as SQLite 資料庫
    
    User->>Browser: 填寫任務標題並點選「新增」
    Browser->>Flask: POST /add (攜帶表單資料)
    Flask->>Flask: 驗證資料有效性
    Flask->>Model: 呼叫 create_task(title)
    Model->>DB: 執行 SQL: INSERT INTO tasks
    DB-->>Model: 回傳寫入成功
    Model-->>Flask: 新增成功
    Flask-->>Browser: HTTP 302 重導向至首頁 (GET /)
    Browser->>User: 重新載入並顯示最新任務列表
```

## 3. 功能清單對照表

列出 PRD 中的各項功能對應的 HTTP 方法與預期的 URL 路由設計：

| 功能名稱 | HTTP 方法 | URL 路徑 | 說明 |
| :--- | :--- | :--- | :--- |
| **檢視任務列表** | `GET` | `/` | 顯示所有任務清單（也是系統首頁） |
| **新增任務** | `POST` | `/add` | 接收表單並新增一筆任務，完成後重導回首頁 |
| **編輯任務頁面** | `GET` | `/edit/<task_id>` | 顯示特定任務的編輯表單頁面 |
| **更新任務資料** | `POST` | `/edit/<task_id>` | 接收變更並更新資料庫，完成後重導回首頁 |
| **刪除任務** | `POST` | `/delete/<task_id>` | 根據任務 ID 刪除資料，完成後重導回首頁 |
| **切換完成狀態** | `POST` | `/toggle/<task_id>` | 切換任務的「已完成/未完成」狀態，完成後重導回首頁 |
