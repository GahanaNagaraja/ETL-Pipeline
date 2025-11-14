# ETL-Pipeline

```mermaid
flowchart LR
    A[Google Analytics 4 CSV Export] --> B[(AWS S3)]
    B --> C[AWS Glue or Python Transform]
    C --> D[(Amazon Redshift Data Warehouse)]
    D --> E[Power BI / Tableau Dashboards]
    
    subgraph Additional Data Sources
        F[Supplier Data]
        G[Inventory Data]
        H[Orders Data]
    end
    
    F --> D
    G --> D
    H --> D
    
    classDef storage fill:#f3f9ff,stroke:#6cace4,stroke-width:2px;
    classDef transform fill:#fdf6ec,stroke:#f5b041,stroke-width:2px;
    classDef warehouse fill:#eafaf1,stroke:#27ae60,stroke-width:2px;
    classDef viz fill:#fcefee,stroke:#e74c3c,stroke-width:2px;

    class A,F,G,H storage;
    class B storage;
    class C transform;
    class D warehouse;
    class E viz;
