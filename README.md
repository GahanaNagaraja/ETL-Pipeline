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
        U[UHungry Platform<br/>(Food / Restaurant)]:::platform
        B[Bayangrom Platform<br/>(Apparel / Marketplace)]:::platform
    end

    U --> A1[UHungry GA4<br/>Analytics (CSV Export)]:::source
    B --> A2[Bayangrom GA4<br/>Analytics (CSV Export)]:::source

    %% ---------- MAIN PIPELINE ----------
    A1 --> S[(AWS S3<br/>Raw Storage)]:::storage
    A2 --> S

    S --> T[AWS Glue / Python ETL<br/>Cleaning & Transformation]:::transform
    T --> W[(Amazon Redshift<br/>Analytics Data Warehouse)]:::warehouse
    W --> D[Power BI / Tableau Dashboards<br/>UHungry & Bayangrom Insights]:::viz

    %% ---------- ADDITIONAL DATA SOURCES ----------
    subgraph Additional_Data_Sources
        direction LR
        F[Supplier Data]:::source
        G[Inventory Data]:::source
        H[Orders Data]:::source
    end
    
    F --> S
    G --> S
    H --> S
