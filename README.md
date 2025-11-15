# ETL-Pipeline

```mermaid
flowchart LR

    subgraph BulkMagicPlatforms
        direction TB
        UHungry[UHungry Platform]
        Bayangrom[Bayangrom Platform]
    end

    UHungry --> GAU[UHungry GA4 Data]
    Bayangrom --> GAB[Bayangrom GA4 Data]

    GAU --> S3[AWS S3 Storage]
    GAB --> S3

    S3 --> ETL[ETL Transform]
    ETL --> Redshift[Amazon Redshift Warehouse]
    Redshift --> Dashboards[BI Dashboards]

    subgraph AdditionalDataSources
        direction LR
        Supplier[Supplier Data]
        Inventory[Inventory Data]
        Orders[Orders Data]
    end
    
    Supplier --> S3
    Inventory --> S3
    Orders --> S3

    %% --------- STYLES ---------
    classDef platform fill:#D6EAF8,stroke:#1B4F72,stroke-width:2px,color:#0B1A33;
    classDef source fill:#EBF5FB,stroke:#2874A6,stroke-width:2px,color:#1B2631;
    classDef storage fill:#FDEDEC,stroke:#922B21,stroke-width:2px,color:#4A0404;
    classDef process fill:#FEF5E7,stroke:#B9770E,stroke-width:2px,color:#6E2C00;
    classDef warehouse fill:#E8F8F5,stroke:#117A65,stroke-width:2px,color:#0B3D2E;
    classDef viz fill:#F5EEF8,stroke:#6C3483,stroke-width:2px,color:#2E0F3E;

    class UHungry,Bayangrom platform;
    class GAU,GAB,Supplier,Inventory,Orders source;
    class S3 storage;
    class ETL process;
    class Redshift warehouse;
    class Dashboards viz;

