# MIA data storage

flowchart TB
  %% ============================================
  %% MIA: Storage-Focused Architecture (Postgres + GCS)
  %% ============================================

  UI[Streamlit Frontend] -->|POST /api/v1/analyze| API[FastAPI Backend\n(Orchestrator + Confidence Aggregator)]

  subgraph TOOLS["Tool Outputs Produced Per Job"]
    T1[Tool 1: Market Intelligence\n(market sizing, trends, sources)]
    T2[Tool 2: Competitor Intelligence\n(tiers, pricing, gap analysis)]
    T3[Tool 3: Geographic Intelligence\n(city ranking + Census/BEA signals)]
  end

  API --> T1
  API --> T2
  API --> T3

  %% -------- Storage Layer (Main Focus) --------
  subgraph STORAGE["Recommended Storage: PostgreSQL (Hot/Queryable) + GCS (Large/Immutable Artifacts)"]
    subgraph PG["PostgreSQL: System of Record (Relational + JSONB)"]
      JOBS[(jobs)\njob_id (PK)\nbrand, niche, geography\ncreated_at, status]
      TOOLRUNS[(tool_runs)\njob_id (FK)\ntool_name, status\nconfidence, latency_ms\noutput_json (JSONB)]
      CITY[(city_scores)\njob_id (FK)\ncity, score\nbreakdown_json (JSONB)\nlive_signals_json (JSONB)]
      ART[(artifacts)\njob_id (FK)\ntool_name, type\ngcs_uri, content_hash\nsize_bytes, created_at]
      CONF[(confidence)\njob_id (FK)\nper_tool_conf\noverall_confidence\nrule_version]
    end

    subgraph GCS["Google Cloud Storage: Artifact Store (Cheap, Scalable)"]
      EVID[(Evidence Objects)\nraw pages, pdfs, html\nscreenshots]
      EXTR[(Extract Objects)\nextracted text\nchunks]
      REPT[(Report Objects)\nreport.md / report.pdf\nfinal summaries]
      LOGS[(Debug/Logs)\nrun logs, traces]
    end
  end

  %% -------- Write Paths --------
  API -->|Create job record| JOBS
  T1 -->|Write JSON outputs + metrics| TOOLRUNS
  T2 -->|Write JSON outputs + tiers| TOOLRUNS
  T3 -->|Write city ranks + live signals| CITY
  API -->|Write per-tool + overall confidence| CONF

  %% -------- Artifact Upload + Linking --------
  T1 -->|Upload evidence/extract/report artifacts| EVID
  T1 --> EXTR
  T1 --> REPT

  T2 -->|Upload evidence/extract/report artifacts| EVID
  T2 --> EXTR
  T2 --> REPT

  T3 -->|Optional: store snapshots / cached responses| EVID

  API -->|Store artifact pointers (GCS URIs + hashes)| ART

  %% -------- Read Paths --------
  UI <-->|Read job status/results| API
  API -->|Query latest job + outputs| JOBS
  API --> TOOLRUNS
  API --> CITY
  API --> CONF
  API -->|Resolve artifact URIs\n(optional signed URL)| ART
  ART -->|points to| GCS

  %% -------- Cost Controls --------
  subgraph COST["Cost Optimization Controls"]
    LC[Lifecycle Policy\nStandard → Nearline → Coldline\n(30/90+ days)]
    DEDUP[Dedup by content_hash\navoid storing duplicate evidence]
    TTL[Retention/TTL\nkeep only last N runs per brand (optional)]
  end

  GCS --> LC
  ART --> DEDUP
  PG --> TTL

