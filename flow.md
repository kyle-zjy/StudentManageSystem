flowchart TD
    subgraph Portal["Portal"]
        A[用户] -->|输入账号密码| B[用户登录界面]
        B --> |查询用户密码| C[SSO]
    end
    
    subgraph SSO["SSO"]
        C -->|判断密码是否正确| D{密码正确?}
        D -- 否 --> E[错误页面]
        D -- 是 --> F[生成 Token]
        F --> |设置有效期| G[存入 Redis]
        G --> H[Token 存入 Cookie]
        H --> I[登录成功，返回首页]
    end
    
    subgraph Redis["Redis"]
        G --> J[保存 Token]
    end
    
    subgraph "用户请求"
        K[用户请求页面] --> |从 Cookie 获取 Token| L{Token 是否存在?}
        L -- 否 --> M[重新登录]
        L -- 是 --> |调用 SSO 服务| N[根据 Token 查询 Redis]
        N --> O[返回用户数据]
    end
    
    %% 连接关系
    I --> K
    O --> K
