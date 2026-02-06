flowchart TB
  UI[Streamlit Frontend] -->|POST /api/v1/analyze| API[FastAPI Backend (Orchestrator + Confidence Aggregator)]

  subgraph TOOLS["Tool Outputs Produced Per Job"]
    T1[Tool 1: Market Intelligence (market sizing, trends, sources)]
    T2[Tool 2: Competitor Intelligence (tiers, pricing, gap analysis)]
    T3[Tool 3: Geographic Intelligence (city ranking + Census/BEA signals)]
  end

  API --> T1
  API --> T2
  API --> T3

  subgraph STORAGE["Recommended Storage: PostgreSQL + GCS"]
    subgraph PG["PostgreSQL: System of Record"]
      JOBS[(jobs)]
      TOOLRUNS[(tool_runs)]
      CITY[(city_scores)]
      ART[(artifacts)]
      CONF[(confidence)]
    end

    subgraph GCS["Google Cloud Storage"]
      EVID[(Evidence)]
      EXTR[(Extracts)]
      REPT[(Reports)]
      LOGS[(Logs)]
    end
  end

  API --> JOBS
  T1 --> TOOLRUNS
  T2 --> TOOLRUNS
  T3 --> CITY
  API --> CONF

  T1 --> EVID
  T1 --> EXTR
  T1 --> REPT
  T2 --> EVID
  T2 --> EXTR
  T2 --> REPT
  T3 --> EVID

  API --> ART
  ART --> GCS
