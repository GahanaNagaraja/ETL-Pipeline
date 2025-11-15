# ETL-Pipeline
```mermaid
flowchart LR

    %% ---------- STYLING ----------
    classDef platform fill:#D6EAF8,stroke:#1B4F72,color:#0B1A33,stroke-width:2px;
    classDef source fill:#EBF5FB,stroke:#2874A6,color:#1B2631,stroke-width:2px;
    classDef storage fill:#FDEDEC,stroke:#922B21,color:#4A0404,stroke-width:2px;
    classDef transform fill:#FEF5E7,stroke:#B9770E,color:#6E2C00,stroke-width:2px;
    classDef warehouse fill:#E8F8F5,stroke:#117A65,color:#0B3D2E,stroke-width:2px;
    classDef viz fill:#F5EEF8,stroke:#6C3483,color:#2E0F3E,stroke-width:2px;

    %% ---------- BULKMAGIC PLATFORMS ----------
    subgraph BulkMagic_Platforms
        direction TB
        UHungry[UHungry Platform\n(Food / Restaurant)]:::platform
        Bayangrom[Bayangrom Platform\n(Apparel / Marketplace)]:::platform
    end

    UHungry --> GA_U[GA4 Analytics\n(CSV Export)]:::source
    Bayangrom --> GA_B[GA4 Analytics\n(CSV Export)]:::source

    %% ---------- MAIN PIPELINE ----------
    GA_U --> S3[(AWS S3\nRaw Storage)]:::storage
    GA_B --> S3

    S3 --> ETL[AWS Glue / Python ETL\nCleaning & Transformation]:::transform
    ETL --> Redshift[(Amazon Redshift\nAnalytics Data Warehouse)]:::warehouse
    Redshift --> Dashboards[Power BI / Tableau Dashboards\n(Platform Insights)]:::viz

    %% ---------- ADDITIONAL DATA SOURCES ----------
    subgraph Additional_Data_Sources
        direction LR
        Supplier[Supplier Data]:::source
        Inventory[Inventory Data]:::source
        Orders[Orders Data]:::source
    end
    
    Supplier --> S3
    Inventory --> S3
    Orders --> S3
