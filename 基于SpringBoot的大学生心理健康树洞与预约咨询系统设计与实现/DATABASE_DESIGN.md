# 数据库设计文档

## 实体关系描述

本系统以**用户 (User)** 为核心实体，通过**用户角色关联 (UserRole)** 与**角色 (Role)** 进行绑定，区分学生、咨询师和管理员身份。若用户为咨询师，则在**咨询师 (Counselor)** 表中存储其专业背景等扩展信息。

在业务流程中：
*   **预约 (Appointment)** 关联了发起预约的学生用户和被预约的咨询师用户。
*   **树洞帖子 (TreeholePost)** 由用户发布，**树洞回复 (TreeholeReply)** 则关联了回复者（用户）和所属的帖子。
*   **心理问卷 (MentalTest)** 由咨询师用户创建，学生的**作答记录 (MentalTestAnswer)** 则关联了对应的问卷和作答学生。
*   **收藏 (Favourite)** 建立了学生用户与咨询师实体之间的关注关系。
*   **对话 (Dialog)** 记录归属于特定的咨询师。

## 实体关系图 (E-R Diagram)

```mermaid
erDiagram
    USER ||--|| USER_ROLE : "关联"
    ROLE ||--|{ USER_ROLE : "定义"
    USER ||--o| COUNSELOR : "扩展信息(1:1)"
    
    USER ||--o{ APPOINTMENT : "作为学生发起"
    USER ||--o{ APPOINTMENT : "作为咨询师接收"
    
    USER ||--o{ TREEHOLE_POST : "发布"
    USER ||--o{ TREEHOLE_REPLY : "回复"
    TREEHOLE_POST ||--o{ TREEHOLE_REPLY : "包含"
    
    USER ||--o{ MENTAL_TEST : "创建(咨询师)"
    USER ||--o{ MENTAL_TEST_ANSWER : "提交(学生)"
    MENTAL_TEST ||--o{ MENTAL_TEST_ANSWER : "拥有记录"
    
    USER ||--o{ FAVOURITE : "收藏(学生)"
    COUNSELOR ||--o{ FAVOURITE : "被收藏"
    
    COUNSELOR ||--o{ DIALOG : "拥有记录"

    USER {
        string id PK "用户ID"
        string username "用户名"
        string nickname "昵称"
    }
    ROLE {
        string id PK "角色ID"
        string name "角色名"
    }
    COUNSELOR {
        string id PK "咨询师ID"
        string user_id FK "关联用户ID"
        string name "姓名"
    }
    APPOINTMENT {
        bigint id PK "预约ID"
        string student_id FK "学生用户ID"
        string counselor_id FK "咨询师用户ID"
        datetime time "预约时间"
    }
    TREEHOLE_POST {
        string id PK "帖子ID"
        string user_id FK "发帖人ID"
        string title "标题"
    }
    TREEHOLE_REPLY {
        bigint id PK "回复ID"
        string post_id FK "帖子ID"
        string user_id FK "回复人ID"
    }
    MENTAL_TEST {
        string id PK "问卷ID"
        string user_id FK "创建人ID"
        string title "标题"
    }
    MENTAL_TEST_ANSWER {
        bigint id PK "作答ID"
        string user_id FK "作答人ID"
        string test_id FK "问卷ID"
        int score "得分"
    }
    FAVOURITE {
        bigint id PK "收藏ID"
        string student_id FK "学生用户ID"
        string counselor_id FK "咨询师ID"
    }
    DIALOG {
        bigint id PK "对话ID"
        string counselor_id FK "咨询师ID"
        string content "内容"
    }
```
