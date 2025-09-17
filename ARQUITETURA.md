# Arquitetura do Browser Copilot

## 🏗️ Visão Geral da Arquitetura

```mermaid
graph TB
    subgraph "Browser"
        EXT[Browser Extension<br/>Vue.js + TypeScript]
        WEB[Web Application<br/>Qualquer site]
    end
    
    subgraph "Backend Services"
        AGENT[AI Agent<br/>Python + FastAPI]
        AUTH[Keycloak<br/>Authentication]
        AI[OpenAI API<br/>GPT Models]
    end
    
    EXT <--> AGENT
    EXT <--> AUTH
    AGENT <--> AI
    EXT <--> WEB
    
    style EXT fill:#e1f5fe
    style AGENT fill:#f3e5f5
    style AUTH fill:#fff3e0
    style AI fill:#e8f5e8
```

## 🔄 Fluxo de Comunicação

```mermaid
sequenceDiagram
    participant U as Usuário
    participant E as Extensão
    participant A as Agente
    participant AI as OpenAI
    
    U->>E: Abre extensão
    E->>A: GET /manifest.json
    A-->>E: Metadados do agente
    
    U->>E: Inicia conversa
    E->>A: POST /sessions
    A-->>E: Session ID
    
    U->>E: Faz pergunta
    E->>A: POST /sessions/{id}/questions
    A->>AI: Processa com LLM
    AI-->>A: Resposta
    A-->>E: Resposta estruturada
    E-->>U: Exibe resposta
    
    Note over E,A: Automações podem incluir<br/>interações com páginas web
```

## 🧩 Componentes Principais

### 1. Browser Extension

```
browser-extension/
├── src/
│   ├── background.ts        # Service Worker Principal
│   ├── side-panel.ts        # Content Script
│   ├── pages/Popup.vue      # Interface Principal
│   ├── components/          # Componentes Vue
│   │   ├── Chat.vue         # Interface de Chat
│   │   ├── AgentList.vue    # Lista de Agentes
│   │   └── Settings.vue     # Configurações
│   └── scripts/
│       ├── agent.ts         # Comunicação com Agentes
│       ├── flow.ts          # Automação de Fluxos
│       └── auth.ts          # Autenticação
```

### 2. AI Agents

```
agent-*/
├── gpt_agent/
│   ├── __main__.py          # Entry Point
│   ├── agent.py             # FastAPI App
│   ├── api.py               # Endpoints API
│   ├── auth.py              # Autenticação
│   ├── domain.py            # Modelos de Dados
│   └── assets/
│       ├── manifest.json    # Metadados
│       └── logo.png         # Logo
```

## 🔐 Fluxo de Autenticação

```mermaid
graph LR
    subgraph "Opcional - Com Autenticação"
        A[Extensão] --> B[Keycloak]
        B --> C[Token JWT]
        C --> D[Agente]
    end
    
    subgraph "Desenvolvimento - Sem Auth"
        E[Extensão] --> F[Agente]
    end
    
    style A fill:#e1f5fe
    style B fill:#fff3e0
    style E fill:#e1f5fe
    style F fill:#f3e5f5
```

## ⚙️ Automação de Fluxos

A extensão pode automatizar interações com páginas web:

```mermaid
graph TD
    A[Usuário faz pergunta] --> B[Agente processa]
    B --> C{Requer automação?}
    C -->|Sim| D[Gera steps de automação]
    C -->|Não| E[Resposta simples]
    D --> F[FlowExecutor executa]
    F --> G[Interage com página]
    G --> H[Retorna resultado]
    H --> I[Exibe resposta final]
    E --> I
```

### Tipos de Automação Suportados:

- **GOTO** - Navegar para URL
- **CLICK** - Clicar em elementos
- **FILL** - Preencher formulários
- **SCROLL** - Rolar página
- **MESSAGE** - Exibir mensagens

## 🛠️ Ambiente de Desenvolvimento

```mermaid
graph TB
    subgraph "Devbox Environment"
        DEV[devbox.json]
        NODE[Node.js + pnpm]
        PY[Python + Poetry]
        DOCKER[Docker Compose]
    end
    
    subgraph "Hot Reload Development"
        VITE[Vite Dev Server<br/>browser-extension]
        UVICORN[Uvicorn Server<br/>agent]
        KC[Keycloak<br/>auth service]
    end
    
    DEV --> NODE
    DEV --> PY
    DEV --> DOCKER
    
    NODE --> VITE
    PY --> UVICORN
    DOCKER --> KC
    
    style DEV fill:#e8f5e8
    style VITE fill:#e1f5fe
    style UVICORN fill:#f3e5f5
```

## 📦 Build e Deploy Pipeline

```mermaid
graph LR
    subgraph "Development"
        A[Code Changes] --> B[Local Testing]
        B --> C[Git Push]
    end
    
    subgraph "CI/CD"
        C --> D[GitHub Actions]
        D --> E[Build & Test]
        E --> F[Create Artifacts]
    end
    
    subgraph "Release"
        F --> G[Chrome Web Store]
        F --> H[GitHub Releases]
    end
    
    style D fill:#fff3e0
    style G fill:#e8f5e8
    style H fill:#e8f5e8
```

## 🔌 Integração com APIs Externas

```mermaid
graph TB
    subgraph "Agent Backend"
        AGENT[FastAPI Agent]
        LANG[LangChain]
        TOOLS[Custom Tools]
    end
    
    subgraph "External APIs"
        OPENAI[OpenAI API]
        AZURE[Azure OpenAI]
        CUSTOM[Custom APIs]
    end
    
    AGENT --> LANG
    LANG --> TOOLS
    LANG --> OPENAI
    LANG --> AZURE
    TOOLS --> CUSTOM
    
    style AGENT fill:#f3e5f5
    style OPENAI fill:#e8f5e8
    style AZURE fill:#e1f5fe
```

## 🧪 Estratégia de Testes

```mermaid
graph TD
    A[Unit Tests] --> B[Integration Tests]
    B --> C[E2E Tests]
    C --> D[Manual Testing]
    
    subgraph "Ferramentas"
        E[pytest - Python]
        F[Vitest - TypeScript]
        G[Chrome DevTools]
    end
    
    A --> E
    B --> F
    D --> G
```

## 📊 Monitoramento e Observabilidade

```mermaid
graph LR
    subgraph "Frontend"
        A[Extension Logs]
        B[Console Messages]
    end
    
    subgraph "Backend"
        C[FastAPI Logs]
        D[LangChain Traces]
    end
    
    subgraph "Infrastructure"
        E[Docker Logs]
        F[System Metrics]
    end
    
    A --> G[Debugging]
    B --> G
    C --> G
    D --> G
    E --> G
    F --> G
```

---

Esta arquitetura permite desenvolvimento ágil, escalabilidade e fácil manutenção do Browser Copilot, com separação clara de responsabilidades entre frontend e backend.