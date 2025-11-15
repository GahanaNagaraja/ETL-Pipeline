# ETL-Pipeline

```mermaid
flowchart LR

    %% ---------- PLATFORMS ----------
    subgraph BulkMagicPlatforms
        direction TB
        UHungry[UHungry Platform]
        Bayangrom[Bayangrom Platform]
    end

    UHungry --> GAU[UHungry GA4 Data]
    Bayangrom --> GAB[Bayangrom GA4 Data]

    %% ---------- MAIN PIPELINE ----------
    GAU --> S3[AWS S3 Storage]
    GAB --> S3

    S3 --> ETL[ETL Transform]
    ETL --> Redshift[Amazon Redshift Warehouse]
    Redshift --> Dashboards[BI Dashboards]

    %% ---------- ADDITIONAL SOURCES ----------
    subgraph AdditionalDataSources
        direction LR
        Supplier[Supplier Data]
        Inventory[Inventory Data]
        Orders[Orders Data]
    end
    
    Supplier --> S3
    Inventory --> S3
    Orders --> S3
