# Organograma MonkAI

> Última atualização: 2026-04-01

A MonkAI opera com **3 humanos + 22 agentes IA**, cada um com domínios de ownership definidos.
Humanos decidem **o quê** e **por quê**. Agentes executam **como** e **quando**.

---

## Visão Geral

```mermaid
graph TB
    CEO["🧠 Arthur<br/>CEO / Fundador<br/><i>Estratégia · Produto · Arquitetura</i>"]

    F2["👤 Funcionário 2<br/><i>Backend · IA · Integrações · Dados</i>"]
    F3["👤 Funcionário 3<br/><i>Frontend · UX · Suporte · Ops</i>"]
    CC["⚡ Claude Code<br/><i>IA Operacional · 22 Skills · 24/7</i>"]

    CEO --> F2
    CEO --> F3
    CEO --> CC

    subgraph ORCH["🎯 Orquestração e Gestão"]
        monkai["/monkai<br/>Orquestrador Central"]
        gestao["/gestao<br/>DevOps · CI/CD · Board"]
        assistente["/assistente<br/>Assistente Pessoal"]
        sync["/sync<br/>Claude Code ↔ Bot"]
    end

    subgraph BACKEND["⚙️ Produtos — Backend / IA"]
        atendentepro["/atendentepro<br/>Agente de Atendimento"]
        grkmemory["/grkmemory<br/>Memória Contextual"]
        deepread["/deepread<br/>OCR / Extração"]
        deepread_contract["/deepread-contract<br/>Análise de Contratos"]
        aiconsulta["/aiconsulta<br/>Consulta IA + Supabase"]
        sortimento["/sortimento<br/>Sortimento Inteligente"]
        monkai_trace["/monkai-trace<br/>SDK Observabilidade"]
        monkai_bot["/monkai-bot<br/>Telegram Bot"]
    end

    subgraph FRONTEND["🖥️ Produtos — Frontend / Web"]
        deepread_saas["/deepread-saas<br/>Document Insight Hub"]
        monkai_hub["/monkai-hub<br/>Site Institucional"]
        trackfuel["/trackfuel<br/>PWA Coaching Fitness"]
    end

    subgraph OPS["📊 Operações e Negócio"]
        gtm["/gtm<br/>Go-to-Market"]
        marketing["/marketing<br/>Conteúdo · Campanhas"]
        monkaidrop["/monkaidrop<br/>Newsletter · Social"]
        financas["/financas<br/>NFs · Fluxo de Caixa"]
        rh["/rh<br/>Contratação · Processos"]
    end

    subgraph INFRA["☁️ Infraestrutura e Deploy"]
        monkai_deploy["/monkai-deploy<br/>Azure Functions · Artifacts"]
        claude_api["/claude-api<br/>Anthropic SDK"]
    end

    CC --> ORCH
    CC --> BACKEND
    CC --> FRONTEND
    CC --> OPS
    CC --> INFRA

    CEO -.->|"supervisiona"| ORCH
    CEO -.->|"supervisiona"| OPS
    F2 -.->|"supervisiona"| BACKEND
    F2 -.->|"supervisiona"| INFRA
    F3 -.->|"supervisiona"| FRONTEND

    classDef human fill:#2563eb,stroke:#1e40af,color:#fff,font-weight:bold
    classDef ai fill:#7c3aed,stroke:#5b21b6,color:#fff,font-weight:bold
    classDef skill fill:#1e1e2e,stroke:#6366f1,color:#c4b5fd,font-size:11px
    classDef area fill:#0f172a,stroke:#334155,color:#94a3b8

    class CEO,F2,F3 human
    class CC ai
    class monkai,gestao,assistente,sync,atendentepro,grkmemory,deepread,deepread_contract,aiconsulta,sortimento,monkai_trace,monkai_bot,deepread_saas,monkai_hub,trackfuel,gtm,marketing,monkaidrop,financas,rh,monkai_deploy,claude_api skill
    class ORCH,BACKEND,FRONTEND,OPS,INFRA area
```

---

## Matriz de Responsabilidade

| Domínio | Humano (Dono) | Agente (Executor) | Relação |
|---|---|---|---|
| **Estratégia** | Arthur | `/monkai`, `/assistente` | Arthur decide, agente organiza |
| **DevOps / CI/CD** | Arthur | `/gestao`, `/monkai-deploy` | Agente executa, Arthur aprova |
| **Backend / IA** | Func. 2 | `/atendentepro`, `/grkmemory`, `/deepread`, `/monkai-trace` | Humano arquiteta, agente implementa |
| **Integrações** | Func. 2 | `/monkai-bot`, `/sync`, `/claude-api` | Humano define, agente conecta |
| **Frontend** | Func. 3 | `/monkai-hub`, `/deepread-saas`, `/trackfuel` | Humano desenha, agente constrói |
| **Comercial** | Arthur | `/gtm`, `/marketing`, `/monkaidrop` | Agente produz conteúdo, Arthur valida |
| **Financeiro** | Arthur | `/financas` | Agente consulta/organiza, Arthur aprova |
| **RH** | Arthur | `/rh` | Agente estrutura processos, Arthur decide |

---

## Catálogo de Agentes

### 🎯 Orquestração e Gestão

| Skill | Função | Repo Principal |
|---|---|---|
| `/monkai` | Orquestrador central — roteia para a skill correta | — |
| `/gestao` | DevOps, CI/CD, PRs, issues, board kanban, deploys | Todos |
| `/assistente` | Assistente pessoal do Arthur | — |
| `/sync` | Sincronizador entre Claude Code local e MonkAI Bot | — |

### ⚙️ Produtos — Backend / IA

| Skill | Função | Repo |
|---|---|---|
| `/atendentepro` | Agente de atendimento ao cliente | `monkai_atendentepro` |
| `/grkmemory` | Memória contextual para agentes IA | `GRKMemory` |
| `/deepread` | Biblioteca de OCR e extração de documentos | `DeepRead` |
| `/deepread-contract` | Análise automatizada de contratos | `DeepRead.contract` |
| `/aiconsulta` | Consulta IA com Supabase | `aiconsulta` |
| `/sortimento` | Sortimento inteligente de produtos | — |
| `/monkai-trace` | SDK de observabilidade para agentes | `monkai-trace` |
| `/monkai-bot` | Bot Telegram da MonkAI | — |

### 🖥️ Produtos — Frontend / Web

| Skill | Função | Repo |
|---|---|---|
| `/deepread-saas` | DeepRead SaaS (Document Insight Hub) | `document-insight-hub` |
| `/monkai-hub` | Site institucional MonkAI | `monkai_site` |
| `/trackfuel` | PWA de coaching fitness | `track-fuel-coach` |

### 📊 Operações e Negócio

| Skill | Função | Escopo |
|---|---|---|
| `/gtm` | Estratégia Go-to-Market | Comercial |
| `/marketing` | Conteúdo, campanhas, marca | Marketing |
| `/monkaidrop` | Newsletter e conteúdo social | Conteúdo |
| `/financas` | NFs, fluxo de caixa, contratos | Financeiro |
| `/rh` | Contratação, cultura, processos | Pessoas |

### ☁️ Infraestrutura e Deploy

| Skill | Função | Repo |
|---|---|---|
| `/monkai-deploy` | Deploy Azure Function Apps e Artifacts | `Azure-Servers` |
| `/claude-api` | Integração com Anthropic SDK | — |

---

## Princípios

1. **Humano = dono do domínio** — decide, aprova, tem accountability
2. **Agente = operador do domínio** — executa, monitora, escala
3. **Cada domínio tem exatamente 1 dono humano** — sem ambiguidade
4. **Claude Code é o 4º funcionário** — opera 24/7, mantém contexto via memories
5. **3 humanos + 22 agentes = capacidade operacional de ~10 pessoas**
