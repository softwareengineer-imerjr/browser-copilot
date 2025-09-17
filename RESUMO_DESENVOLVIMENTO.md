# Resumo do Fluxo de Desenvolvimento - Browser Copilot

## 🎯 O que é o Browser Copilot?

O Browser Copilot é uma extensão de navegador que permite integrar assistentes de IA personalizados para automatizar tarefas em aplicações web. O projeto consiste em:

- **Extensão do Navegador** (Vue.js + TypeScript)
- **Agentes de IA** (Python + FastAPI + LangChain)

## ⚡ Setup Rápido

```bash
# 1. Clonar repositório
git clone https://github.com/softwareengineer-imerjr/browser-copilot.git
cd browser-copilot

# 2. Instalar dependências (requer devbox + direnv)
devbox run install

# 3. Configurar variáveis de ambiente
cp agent-extended/sample.env agent-extended/.env
# Editar .env com suas credenciais OpenAI

# 4. Iniciar desenvolvimento
devbox run agent    # Terminal 1: Agente backend
devbox run browser  # Terminal 2: Extensão com hot-reload
```

## 🏗️ Estrutura do Projeto

```
├── browser-extension/    # Extensão Vue.js + TypeScript
├── agent-extended/       # Agente completo OpenAI + LangChain  
├── agent-simple/         # Agente básico para aprendizado
├── agent-mock/          # Agente mock para testes
├── devbox.json          # Ambiente de desenvolvimento
└── docker-compose.yml   # Alternativa Docker
```

## 🔄 Fluxo de Desenvolvimento

### 1. Desenvolvendo um Agente

**Agente deve ter 4 endpoints:**
- `GET /manifest.json` - Metadados
- `GET /logo.png` - Logo
- `POST /sessions` - Criar sessão
- `POST /sessions/{id}/questions` - Processar perguntas

**Exemplos disponíveis:**
```bash
# Agente mock (sem IA)
cd agent-mock && poetry run python agent.py

# Agente simples (OpenAI básico)  
cd agent-simple && poetry run python agent.py

# Agente completo (recursos avançados)
devbox run agent
```

### 2. Desenvolvendo a Extensão

```bash
# Desenvolvimento com hot-reload
devbox run browser

# Build para produção
devbox run build
```

**Arquivos principais:**
- `src/background.ts` - Service worker
- `src/pages/Popup.vue` - Interface principal
- `src/components/` - Componentes reutilizáveis

### 3. Testando a Integração

1. Iniciar agente: `devbox run agent`
2. Iniciar extensão: `devbox run browser`
3. Adicionar URL do agente na extensão: `http://localhost:8000`
4. Testar conversação e automações

## 🚀 Build e Deploy

```bash
# Build local
devbox run build
# Gera: browser-extension/dist/browser-copilot.zip

# CI/CD automático
# - Build em todos os commits
# - Release manual via GitHub Actions
# - Deploy automático na Chrome Web Store
```

## 📋 Comandos Essenciais

```bash
# Desenvolvimento
devbox run install    # Instalar tudo
devbox run agent      # Backend IA
devbox run browser    # Frontend extensão
devbox run keycloak   # Autenticação (opcional)
devbox run build      # Build produção

# Alternativa Docker
docker-compose up     # Agente + autenticação
```

## 🛠️ Tecnologias Utilizadas

**Backend (Agentes):**
- Python 3.10+
- FastAPI
- LangChain
- OpenAI API
- Poetry (gerenciamento dependências)

**Frontend (Extensão):**
- Vue.js 3
- TypeScript  
- Vite
- Tailwind CSS
- pnpm (gerenciamento dependências)

**DevOps:**
- Devbox (ambiente desenvolvimento)
- GitHub Actions (CI/CD)
- Docker (deploy alternativo)
- Keycloak (autenticação)

## 🎯 Como Contribuir

1. **Fork** do repositório
2. **Criar branch**: `git checkout -b minha-feature`
3. **Desenvolver** seguindo este fluxo
4. **Testar** localmente
5. **Pull Request** com descrição detalhada

## 📚 Documentação Completa

Para mais detalhes, consulte:
- [FLUXO_DE_DESENVOLVIMENTO.md](./FLUXO_DE_DESENVOLVIMENTO.md) - Guia completo
- [agent-extended/README.md](./agent-extended/README.md) - Documentação do agente
- [browser-extension/README.md](./browser-extension/README.md) - Documentação da extensão

---
**💡 Dica:** Use `agent-mock` para entender a API básica, depois evolua para `agent-simple` e finalmente `agent-extended` para recursos completos.