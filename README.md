# ETL-Pipeline

```mermaid
flowchart LR

    %% ---------- STYLING ----------
    classDef source fill:#D6EAF8,stroke:#1B4F72,color:#0B1A33,stroke-width:2px;
    classDef storage fill:#FDEDEC,stroke:#922B21,color:#4A0404,stroke-width:2px;
    classDef transform fill:#FEF5E7,stroke:#B9770E,color:#6E2C00,stroke-width:2px;
    classDef warehouse fill:#E8F8F5,stroke:#117A65,color:#0B3D2E,stroke-width:2px;
    classDef viz fill:#F5EEF8,stroke:#6C3483,color:#2E0F3E,stroke-width:2px;

    %% ---------- MAIN PIPELINE ----------
    A[Google Analytics 4 CSV Export] --> B[(AWS S3 Raw Storage)]
    B --> C[AWS Glue / Python Data Transform]
    C --> D[(Amazon Redshift Data Warehouse)]
    D --> E[Power BI / Tableau Dashboards]

    %% ---------- ADDITIONAL SOURCES ----------
    subgraph Additional Data Sources
        direction LR
        F[Supplier Data]:::source
        G[Inventory Data]:::source
        H[Orders Data]:::source
    end
    
    F --> B
    G --> B
    H --> B

    %% ---------- APPLY COLORS ----------
    class A,F,G,H source;
    class B storage;
    class C transform;
    class D warehouse;
    class E viz;

