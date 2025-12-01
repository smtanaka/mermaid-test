# mermaid-test
```mermaid
flowchart TD
    A[開始] --> B[処理1]
    B --> C{条件？}
    C -->|Yes| D[処理2]
    C -->|No| E[処理3]
    D --> F[終了]
    E --> F[終了]
