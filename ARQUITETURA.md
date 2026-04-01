# Arquitetura MonkAI

> Última atualização: 2026-04-01

---

## Visão Geral da Plataforma

```mermaid
flowchart LR
    U["👤 Canais do Cliente<br/>WhatsApp • Teams • Web<br/>Telegram • Periskope"] --> APIM["🌐 API de Entrada<br/>API Management (Azure)"]

    APIM --> GW["⚡ Gateway<br/>Azure Functions :7071<br/>api-monkai • Routing • Webhooks"]

    GW --> PROC["⚙️ Processor<br/>Azure Functions :7072<br/>DeepRead • Doc Processing"]

    GW --> Agent["🤖 MonkAI Agents<br/>AtendentePro • GRKMemory<br/>MonkAI_agent Runtime"]

    Agent --> AOAI["🧠 IA<br/>Azure OpenAI<br/>LangChain • OpenAI Agents SDK"]
    Agent --> Data["💾 Dados e Memória<br/>Cosmos DB (MongoDB API)<br/>MongoDB 7 • Azure Tables"]
    Agent --> KV["🔐 Segurança<br/>Key Vault • Azure Policy<br/>Fernet Encryption"]
    Agent --> Cache["⚡ Cache<br/>Redis 7<br/>TTL 10min (secrets)"]

    Agent --> Trace["📊 MonkAI Trace<br/>Observabilidade<br/>Session Replay • Custos • Auditoria"]

    Agent --> Int["🔗 Integrações<br/>CRM • ERP • Outlook<br/>Microsoft Graph • Gmail"]

    PROC --> Blob["📦 Storage<br/>Azure Blob Storage<br/>Azurite (local dev)"]

    subgraph Clients["🏢 Clientes"]
        direction TB
        Vivo["Vivo<br/>SuporteONE • Teams"]
        WM["White Martins<br/>NFS-e • Gigi"]
        Popai["Popai<br/>Periskope • Mercos<br/>Campanhas"]
    end

    U --- Clients

    subgraph Deploy["🚀 Entrega e Operação"]
        direction TB
        CI["CI/CD<br/>GitHub Actions<br/>ci.yml • deploy.yml • tests.yml"]
        Artifacts["Artefatos<br/>Azure Artifacts (strava_feed)<br/>PyPI (Cython wheels)"]
        Docker["Containers<br/>Docker Compose<br/>5 serviços (gw+proc+azurite+mongo+redis)"]
    end

    CI --> GW
    CI --> PROC
    Artifacts --> GW
```

---

## Stack de Canais de Entrada

```mermaid
flowchart TB
    subgraph Canais["Canais de Comunicação"]
        direction TB
        WPP["WhatsApp<br/>Evolution API (Baileys + Meta Cloud)<br/>monkai-whatsapp • Node.js/TS"]
        TG["Telegram<br/>MonkAI Bot<br/>Python"]
        Teams["Microsoft Teams<br/>monkai-graph<br/>Webhooks"]
        Digisac["Digisac<br/>API REST"]
        Periskope["Periskope<br/>WhatsApp Business"]
        Web["Web / API REST<br/>Clientes diretos"]
    end

    subgraph Handlers["api-monkai — Handlers"]
        direction TB
        H1["WhatsAppHandler"]
        H2["TeamsHandler"]
        H3["DigisacHandler"]
        H4["PeriskopeHandler"]
        H5["MetaHandler"]
        H6["StandardHandler"]
        H7["TesterHandler"]
    end

    WPP --> H1
    Teams --> H2
    Digisac --> H3
    Periskope --> H4
    Web --> H5
    Web --> H6
    Web --> H7

    Handlers --> Router["Agent Router<br/>YAML Config (clients.yaml)<br/>Hot-reload • Pydantic 2.0"]

    Router --> Agents["MonkAI Agents Runtime"]
```

---

## Stack de Dados e Segurança

```mermaid
flowchart TB
    subgraph Security["🔐 Segurança"]
        KV["Azure Key Vault<br/>70+ secrets"]
        Policy["Azure Policy<br/>Governance e Compliance"]
        Fernet["Fernet Encryption<br/>Dados sensíveis (Vivo)"]
        Identity["Azure Identity<br/>Managed Identity + RBAC"]
    end

    subgraph Data["💾 Dados"]
        Cosmos["Cosmos DB<br/>MongoDB API<br/>Sessões • Histórico • Estado"]
        Mongo["MongoDB 7<br/>Local dev"]
        Tables["Azure Tables<br/>Campanhas Popai (read-only)"]
        Blob["Azure Blob Storage<br/>Documentos • Spreadsheets • Profiles"]
    end

    subgraph Caching["⚡ Cache"]
        Redis["Redis 7 Alpine<br/>Secret cache (TTL 10min)<br/>Session state"]
    end

    KV --> |"monkai-keyvault<br/>fallback: Redis → KV → local.settings → env"| App["Azure Functions"]
    Data --> App
    Caching --> App
```

---

## Stack Completa por Camada

```mermaid
flowchart TB
    subgraph FE["🖥️ Frontend"]
        direction LR
        Hub["MonkAI Hub<br/>React • Lovable"]
        SaaS["DeepRead SaaS<br/>React • Lovable"]
        TF["TrackFuel<br/>React PWA • Lovable"]
        AIC["AIConsulta<br/>React • Supabase"]
        Sort["Sortimento<br/>React • Supabase"]
    end

    subgraph BE["⚙️ Backend"]
        direction LR
        GW["Gateway :7071<br/>Python 3.11<br/>Azure Functions v2"]
        PROC["Processor :7072<br/>Python 3.11<br/>Azure Functions v2"]
        EVO["Evolution API<br/>Node.js 20 • TypeScript<br/>Express • Prisma • PostgreSQL"]
    end

    subgraph LIBS["📦 Pacotes Internos (Azure Artifacts)"]
        direction LR
        L1["monkai-keyvault"]
        L2["monkai-shared"]
        L3["monkai-data"]
        L4["monkai-whatsapp"]
        L5["monkai-graph"]
        L6["monkai-gmail"]
        L7["api-monkai"]
        L8["monkai-drop"]
        L9["monkai-trace"]
    end

    subgraph PROD["🛡️ Produtos IP (PyPI — Cython wheels)"]
        direction LR
        P1["AtendentePro"]
        P2["GRKMemory"]
        P3["DeepRead"]
        P4["DeepRead.contract"]
    end

    subgraph AI["🧠 IA e LLMs"]
        direction LR
        AOAI["Azure OpenAI"]
        LC["LangChain<br/>langchain-openai"]
        OAI["OpenAI Agents SDK"]
        Claude["Claude API<br/>Anthropic SDK"]
    end

    subgraph INFRA["☁️ Infraestrutura Azure"]
        direction LR
        AF["Azure Functions"]
        AKV["Key Vault"]
        ABS["Blob Storage"]
        CDB["Cosmos DB"]
        AT["Azure Tables"]
        AP["Azure Policy"]
        AI2["Azure Identity"]
        AA["Azure Artifacts"]
    end

    subgraph DEV["🛠️ Dev e CI/CD"]
        direction LR
        GH["GitHub Actions<br/>CI • Deploy • Tests"]
        DK["Docker Compose<br/>5 serviços"]
        AZ["Azurite<br/>Storage emulator"]
        CBW["cibuildwheel<br/>Multi-platform wheels"]
        Twine["Twine<br/>PyPI + Azure Artifacts"]
    end

    FE --> BE
    BE --> LIBS
    BE --> PROD
    BE --> AI
    BE --> INFRA
    DEV --> BE
```

---

## Ambientes de Deploy

```mermaid
flowchart LR
    subgraph DEV["🧪 Development"]
        DevGW["monkai-services-dev<br/>Gateway"]
        DevTR["monkai-event-triggers<br/>Triggers"]
    end

    subgraph PROD["🏭 Produção"]
        ProdGW["monkai-services<br/>Gateway"]
        ProdTR["monkai-event-triggers<br/>Triggers"]
    end

    Feature["Feature Branch"] -->|"PR"| Dev["Branch: development"]
    Dev -->|"deploy-function-apps.yml"| DEV
    Dev -->|"PR aprovado"| Main["Branch: main"]
    Main -->|"deploy-function-apps.yml"| PROD

    style Feature fill:#f59e0b,stroke:#d97706,color:#000
    style Dev fill:#3b82f6,stroke:#2563eb,color:#fff
    style Main fill:#10b981,stroke:#059669,color:#fff
```

---

## GitHub Workflows — Mapa Completo

```mermaid
flowchart TB
    subgraph AS["Azure-Servers"]
        direction TB
        AS_CI["✅ CI<br/>ci.yml<br/><i>push + PR → lint + validate</i>"]
        AS_TEST["🧪 Tests<br/>tests.yml<br/><i>push + PR → pytest repo inteiro</i>"]
        AS_DEPLOY["🚀 Deploy Function Apps<br/>deploy-function-apps.yml<br/><i>CI pass on main → auto deploy</i><br/><i>manual → dev ou prod</i>"]
        AS_TRIGGERS["🚀 Deploy Triggers<br/>deploy-triggers.yml<br/><i>manual → monkai-event-triggers</i>"]
        AS_TESTER["🤖 Test Agents<br/>test-agents.yml<br/><i>manual → monkai-tester KPIs</i>"]

        AS_CI -->|"main ✅"| AS_DEPLOY
        AS_TEST -.->|"bloqueia merge"| AS_CI
    end

    subgraph API["api-monkai"]
        direction TB
        API_CI["✅ CI<br/>ci.yml<br/><i>push main/dev + PR → ruff + pytest</i>"]
        API_DEPLOY_READY["🔍 Deploy-ready<br/>deploy_ready.yml<br/><i>push/PR dev → lint + build</i>"]
        API_PUBLISH["📦 Publish Azure<br/>publish_azure.yml<br/><i>push main → Azure Artifacts</i>"]

        API_CI -.->|"quality gate"| API_PUBLISH
    end

    subgraph CYTHON["Produtos Cython (PyPI)"]
        direction TB

        subgraph ATP["AtendentePro"]
            ATP_CI["✅ CI<br/><i>push main/develop + PR</i><br/><i>matrix: 3.10, 3.12, 3.13</i>"]
            ATP_PUB["📦 Publish PyPI<br/><i>push main + pyproject.toml</i><br/><i>3 fases: priority → core → extended</i>"]
        end

        subgraph GRK["GRKMemory"]
            GRK_CI["✅ CI<br/><i>push main/develop + PR</i><br/><i>matrix: 3.10, 3.12, 3.13</i>"]
            GRK_PUB["📦 Publish PyPI<br/><i>push main + pyproject.toml</i><br/><i>cibuildwheel 3 fases</i>"]
        end

        subgraph DR["DeepRead"]
            DR_CI["✅ CI<br/><i>push main/develop + PR</i><br/><i>matrix: 3.10, 3.12, 3.13</i>"]
            DR_PUB["📦 Publish PyPI<br/><i>push main + pyproject.toml</i><br/><i>cibuildwheel 3 fases</i>"]
        end

        subgraph DRC["DeepRead.contract"]
            DRC_CI["✅ CI<br/><i>push main/develop + PR</i>"]
            DRC_PUB["📦 Publish PyPI<br/><i>push main + pyproject.toml</i><br/><i>cibuildwheel 3 fases</i>"]
        end
    end

    subgraph PURE["Pacotes Pure Python"]
        direction TB
        MT_CI["✅ monkai-trace CI<br/><i>push main/develop + PR</i>"]
        MT_PUB["📦 monkai-trace Publish<br/><i>push main + pyproject.toml</i><br/><i>py3-none-any (MIT)</i>"]
    end

    subgraph FRONT["Frontend"]
        direction TB
        AIC_CI["✅ AIConsulta CI<br/><i>push + PR</i>"]
        DIH_CI["✅ DeepRead SaaS CI<br/><i>push + PR</i>"]
        TFC_CI["✅ TrackFuel CI<br/><i>push + PR</i>"]
        HUB["❌ MonkAI Hub<br/><i>sem CI (Lovable)</i>"]
    end

    %% Dependências entre repos
    API_PUBLISH -->|"api_monkai pkg"| AS_DEPLOY
    ATP_PUB -->|"atendentepro pkg"| AS_DEPLOY
    DR_PUB -->|"DeepRead.Monkai pkg"| AS_TRIGGERS
    GRK_PUB -->|"grkmemory pkg"| AS_DEPLOY
    MT_PUB -->|"monkai-trace pkg"| AS_DEPLOY

    classDef ci fill:#22c55e,stroke:#16a34a,color:#fff
    classDef publish fill:#8b5cf6,stroke:#7c3aed,color:#fff
    classDef deploy fill:#f59e0b,stroke:#d97706,color:#000
    classDef test fill:#3b82f6,stroke:#2563eb,color:#fff
    classDef none fill:#6b7280,stroke:#4b5563,color:#fff

    class AS_CI,API_CI,ATP_CI,GRK_CI,DR_CI,DRC_CI,MT_CI,AIC_CI,DIH_CI,TFC_CI ci
    class API_PUBLISH,ATP_PUB,GRK_PUB,DR_PUB,DRC_PUB,MT_PUB publish
    class AS_DEPLOY,AS_TRIGGERS deploy
    class AS_TEST,AS_TESTER,API_DEPLOY_READY test
    class HUB none
```

---

## Pipeline de Publicação — Produtos Cython

```mermaid
flowchart LR
    subgraph Phase1["Fase 1 — Priority<br/>~2 min • $0.01"]
        P1["cp312-manylinux_x86_64<br/>(Azure Functions target)"]
    end

    subgraph Phase2["Fase 2 — Core<br/>~5 min • $0.15"]
        P2["Linux restante<br/>+ Windows<br/>cp310/311/312/313"]
    end

    subgraph Phase3["Fase 3 — Extended<br/>~5 min • $0.62"]
        P3["macOS x86_64<br/>cp310/311/312/313"]
    end

    subgraph Local["Local (macOS arm64)<br/>Build manual"]
        P4["macOS arm64<br/>cp310/311/312/313<br/>upload antes do CI"]
    end

    P4 -->|"twine upload"| PyPI["📦 PyPI"]
    P1 -->|"publish imediato"| PyPI
    P2 -->|"publish batch"| PyPI
    P3 -->|"publish batch"| PyPI

    PyPI -->|"pip install"| AzFunc["Azure Functions<br/>requirements.txt"]
    PyPI -->|"pip install"| DevLocal["Dev Local<br/>pip install"]

    style Phase1 fill:#22c55e,stroke:#16a34a,color:#fff
    style Phase2 fill:#3b82f6,stroke:#2563eb,color:#fff
    style Phase3 fill:#f59e0b,stroke:#d97706,color:#000
    style Local fill:#8b5cf6,stroke:#7c3aed,color:#fff
```

---

## Resumo de Workflows por Repo

| Repo | CI | Publish | Deploy | Tests | Trigger |
|---|---|---|---|---|---|
| **Azure-Servers** | `ci.yml` (push+PR) | — | `deploy-function-apps.yml` (auto main + manual) | `tests.yml` + `test-agents.yml` | push, PR, CI pass, manual |
| **api-monkai** | `ci.yml` (push+PR) | `publish_azure.yml` (push main → Azure Artifacts) | — | via CI | push main, PR |
| **AtendentePro** | `ci.yml` (matrix 3.10/12/13) | `publish.yml` (3-phase cibuildwheel → PyPI) | — | via CI | push main+develop, PR |
| **GRKMemory** | `ci.yml` (matrix 3.10/12/13) | `publish.yml` (3-phase cibuildwheel → PyPI) | — | via CI | push main+develop, PR |
| **DeepRead** | `ci.yml` (matrix 3.10/12/13) | `publish.yml` (3-phase cibuildwheel → PyPI) | — | via CI | push main+develop, PR |
| **DeepRead.contract** | `ci.yml` | `publish.yml` (cibuildwheel → PyPI) | — | via CI | push main+develop, PR |
| **monkai-trace** | `ci.yml` | `publish.yml` (py3-none-any → PyPI) | — | via CI | push main+develop, PR |
| **AIConsulta** | `ci.yml` | — | — (Lovable) | via CI | push, PR |
| **DeepRead SaaS** | `ci.yml` | — | — (Lovable) | via CI | push, PR |
| **TrackFuel** | `ci.yml` | — | — (Lovable) | via CI | push, PR |
| **MonkAI Hub** | ❌ | — | — (Lovable) | — | — |
