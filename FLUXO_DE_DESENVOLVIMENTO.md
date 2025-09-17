# Fluxo de Desenvolvimento - Browser Copilot

Este documento explica o fluxo de desenvolvimento completo do repositório Browser Copilot, desde a configuração inicial até a implementação de novas funcionalidades.

## 📋 Visão Geral do Projeto

O **Browser Copilot** é uma extensão de navegador que permite usar assistentes de IA existentes ou customizados para ajudar em tarefas cotidianas de aplicações web. O projeto é composto por dois componentes principais:

1. **Browser Extension** - A extensão do navegador (Vue.js + TypeScript)
2. **Agents** - Serviços backend que implementam os assistentes de IA (Python + FastAPI)

## 🏗️ Arquitetura do Projeto

```
browser-copilot/
├── browser-extension/     # Extensão do navegador
├── agent-extended/        # Agente completo com OpenAI + LangChain
├── agent-simple/          # Agente simples para testes
├── agent-mock/           # Agente mock para desenvolvimento
├── agent-upcamp/         # Agente específico (exemplo)
├── keycloak/             # Configuração de autenticação
├── .github/workflows/    # CI/CD workflows
├── devbox.json          # Configuração do ambiente de desenvolvimento
└── docker-compose.yml   # Orquestração de serviços
```

## 🛠️ Configuração do Ambiente de Desenvolvimento

### Pré-requisitos

O projeto utiliza as seguintes ferramentas para gerenciamento do ambiente:

- **[Devbox](https://www.jetpack.io/devbox)** - Gerenciamento de ambiente de desenvolvimento
- **[Direnv](https://github.com/direnv/direnv)** - Carregamento automático de variáveis de ambiente

### Instalação Inicial

1. **Instalar dependências do sistema:**
   ```bash
   # Instalar devbox e direnv (seguir documentação oficial)
   ```

2. **Clonar o repositório:**
   ```bash
   git clone https://github.com/softwareengineer-imerjr/browser-copilot.git
   cd browser-copilot
   ```

3. **Instalar todas as dependências:**
   ```bash
   devbox run install
   ```

   Este comando irá:
   - Instalar dependências do browser-extension (pnpm)
   - Instalar dependências do agent-extended (Poetry)
   - Criar arquivo `.env` se não existir

## 🔄 Fluxo de Desenvolvimento

### 1. Desenvolvimento de Agentes

Os agentes são serviços backend que implementam a lógica dos assistentes de IA. Existem três exemplos no repositório:

#### Estrutura Básica de um Agente

Todo agente compatível deve implementar 4 endpoints obrigatórios:

1. **`/manifest.json`** - Metadados do agente
2. **`/logo.png`** - Logo exibido na extensão
3. **`/sessions`** - Criação de sessões (POST)
4. **`/sessions/{session_id}/questions`** - Processamento de perguntas (POST)

#### Tipos de Agentes Disponíveis

**1. agent-mock** - Agente básico para testes
```bash
cd agent-mock
devbox shell
poetry install --no-root && poetry run python agent.py
```

**2. agent-simple** - Agente com integração básica OpenAI
```bash
cd agent-simple
# Configurar .env com credenciais OpenAI
poetry install && poetry run python agent.py
```

**3. agent-extended** - Agente completo com autenticação e recursos avançados
```bash
# Configurar agent-extended/.env
devbox run agent
```

#### Desenvolvimento de Novo Agente

Para criar um novo agente:

1. **Copiar estrutura de um agente existente:**
   ```bash
   cp -r agent-simple meu-agente
   cd meu-agente
   ```

2. **Configurar dependências:**
   ```bash
   # Editar pyproject.toml conforme necessário
   poetry install
   ```

3. **Implementar endpoints obrigatórios:**
   - Manifest com metadados
   - Logo (PNG)
   - Criação de sessões
   - Processamento de perguntas

4. **Testar localmente:**
   ```bash
   poetry run python agent.py
   # Testar em http://localhost:8000
   ```

### 2. Desenvolvimento da Extensão

A extensão é desenvolvida em Vue.js + TypeScript usando Vite.

#### Estrutura da Extensão

```
browser-extension/
├── src/
│   ├── manifest.json        # Manifest da extensão
│   ├── background.ts        # Service worker
│   ├── side-panel.ts        # Script de conteúdo
│   ├── pages/Popup.vue      # Interface principal
│   └── components/          # Componentes Vue
├── public/                  # Assets estáticos
└── package.json            # Dependências Node.js
```

#### Desenvolvimento com Hot-reload

```bash
# Inicia desenvolvimento com recarga automática
devbox run browser
```

Este comando:
- Inicia servidor de desenvolvimento Vite
- Abre Chrome com extensão carregada
- Habilita hot-reload para mudanças em tempo real

#### Principais Arquivos

- **`src/background.ts`** - Gerencia interações entre extensão e agentes
- **`src/side-panel.ts`** - Cria e controla a barra lateral
- **`src/pages/Popup.vue`** - Interface principal da extensão
- **`src/components/`** - Componentes reutilizáveis da UI

### 3. Autenticação (Keycloak)

Para desenvolvimento com autenticação:

1. **Iniciar Keycloak:**
   ```bash
   devbox run keycloak
   # Disponível em http://localhost:8080
   ```

2. **Credenciais de teste:**
   - Usuário: `test`
   - Senha: `test`

3. **Desenvolvimento sem autenticação:**
   - Comentar seção Keycloak no `.env` do agente
   - Acelera ciclo de desenvolvimento

## 🚀 Build e Deploy

### Build Local

```bash
# Build completo da extensão
devbox run build
```

Este comando:
- Compila TypeScript
- Gera build otimizado
- Cria arquivo ZIP para distribuição

### CI/CD Pipeline

O projeto possui workflows automatizados:

#### **Build Workflow** (`.github/workflows/build.yml`)
- Executa em todos os commits
- Instala dependências
- Executa build completo
- Valida integridade do código

#### **Release Workflow** (`.github/workflows/release.yml`)
- Execução manual com versão específica
- Valida versão semântica
- Cria release draft no GitHub
- Faz build de produção
- Publica na Chrome Web Store
- Cria release final com artefatos

### Deploy Manual

1. **Build da extensão:**
   ```bash
   devbox run build
   cd browser-extension/dist
   ```

2. **Arquivo gerado:**
   - `browser-copilot.zip` - Pronto para upload

## 🧪 Testes e Validação

### Testes de Agentes

```bash
cd agent-extended
poetry run pytest
```

### Validação da Extensão

1. **Carregar extensão no Chrome:**
   - Abrir `chrome://extensions/`
   - Ativar "Modo desenvolvedor"
   - Carregar extensão descompactada

2. **Testar funcionalidades:**
   - Adicionar URL do agente
   - Testar chat e interações
   - Verificar automações

### Testes de Integração

```bash
# Iniciar agente
devbox run agent

# Em outro terminal, iniciar extensão
devbox run browser

# Testar fluxo completo:
# 1. Adicionar agente na extensão
# 2. Iniciar conversa
# 3. Testar comandos específicos
```

## 📝 Melhores Práticas

### Desenvolvimento de Agentes

1. **Implementar endpoints obrigatórios primeiro**
2. **Usar agent-mock para prototipagem rápida**
3. **Implementar streaming para melhor UX**
4. **Adicionar logs detalhados para debug**
5. **Validar entrada de dados**

### Desenvolvimento da Extensão

1. **Testar em múltiplos navegadores**
2. **Verificar permissions no manifest**
3. **Implementar tratamento de erros**
4. **Otimizar performance**
5. **Seguir guidelines de acessibilidade**

### Versionamento

1. **Seguir Semantic Versioning**
2. **Atualizar CHANGELOG.md**
3. **Testar release candidates**
4. **Coordenar releases entre extensão e agentes**

## 🔧 Comandos Úteis

```bash
# Ambiente de desenvolvimento
devbox run install          # Instalar dependências
devbox run agent            # Executar agente com hot-reload
devbox run browser          # Executar extensão com hot-reload
devbox run keycloak         # Iniciar autenticação
devbox run build            # Build de produção

# Desenvolvimento individual
cd agent-extended && poetry run python -m gpt_agent
cd browser-extension && pnpm dev
cd browser-extension && pnpm build

# Docker (alternativo)
docker-compose up           # Executar agente + Keycloak
```

## 🎯 Próximos Passos

Para contribuir com o projeto:

1. **Fork do repositório**
2. **Criar branch de feature**
3. **Implementar mudanças seguindo este fluxo**
4. **Testar localmente**
5. **Criar Pull Request**

## 📚 Recursos Adicionais

- [Documentação do agent-extended](./agent-extended/README.md)
- [Documentação da browser-extension](./browser-extension/README.md)
- [Exemplos de agents](./agent-mock/README.md)
- [Devbox Documentation](https://www.jetpack.io/devbox)
- [Chrome Extensions Developer Guide](https://developer.chrome.com/docs/extensions/)

---

*Este documento deve ser atualizado conforme o projeto evolui. Para dúvidas ou sugestões, abra uma issue no repositório.*