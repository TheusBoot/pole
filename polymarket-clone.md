# 🔮 PredictChain — Plataforma de Mercado de Predição Descentralizada

> Clone inspirado no Polymarket, construído sobre blockchain (Polygon), com contratos auditados, oráculo de resolução e interface Web3 moderna.

---

## 📋 Sumário

1. [Visão Geral](#visão-geral)
2. [Arquitetura do Sistema](#arquitetura-do-sistema)
3. [Stack Tecnológica](#stack-tecnológica)
4. [Módulos do Projeto](#módulos-do-projeto)
5. [Smart Contracts](#smart-contracts)
6. [Sistema de Oráculos](#sistema-de-oráculos)
7. [Roadmap](#roadmap)
8. [Estimativas de Tempo e Custo](#estimativas-de-tempo-e-custo)
9. [Riscos e Mitigações](#riscos-e-mitigações)
10. [Regulação e Compliance](#regulação-e-compliance)
11. [Time Necessário](#time-necessário)

---

## 🌐 Visão Geral

**PredictChain** é uma plataforma de mercados de predição descentralizada onde usuários podem criar e negociar contratos de resultado binário (Sim/Não) sobre eventos do mundo real — esportes, política, economia, cripto e mais.

### Diferencial
- Totalmente descentralizado (non-custodial)
- Resolução de mercados via oráculo com sistema de disputas
- Liquidez via CLOB (Central Limit Order Book) on-chain
- Pagamentos em USDC (stablecoin)
- Interface Web3 moderna com MetaMask / WalletConnect

---

## 🏗️ Arquitetura do Sistema

```
┌─────────────────────────────────────────────────────────┐
│                      FRONTEND (Next.js)                 │
│     Interface de usuário, carteira, gráficos, ordens    │
└────────────────────────┬────────────────────────────────┘
                         │ REST / GraphQL / WebSocket
┌────────────────────────▼────────────────────────────────┐
│                    BACKEND / API                        │
│   Node.js — Indexação, orderbook off-chain, notificações│
└────────────┬───────────────────────┬────────────────────┘
             │                       │
┌────────────▼──────────┐ ┌──────────▼──────────────────┐
│  SUBGRAPH (The Graph) │ │     BANCO DE DADOS           │
│  Indexação on-chain   │ │  PostgreSQL + Redis          │
└────────────┬──────────┘ └─────────────────────────────┘
             │
┌────────────▼──────────────────────────────────────────┐
│              BLOCKCHAIN — POLYGON                      │
│                                                        │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐  │
│  │ MarketFactory│  │  CLOB/AMM    │  │  Oracle     │  │
│  │  Contract    │  │  Contract    │  │  Contract   │  │
│  └──────────────┘  └──────────────┘  └─────────────┘  │
└───────────────────────────────────────────────────────┘
```

---

## 🛠️ Stack Tecnológica

### Frontend
| Tecnologia | Uso |
|---|---|
| Next.js 14 (App Router) | Framework principal |
| TypeScript | Tipagem estática |
| TailwindCSS | Estilização |
| Wagmi + Viem | Integração Web3 |
| RainbowKit | UI de conexão de carteira |
| Recharts / TradingView | Gráficos de preços |
| React Query | Cache e estado de servidor |

### Backend
| Tecnologia | Uso |
|---|---|
| Node.js + Fastify | API REST |
| PostgreSQL | Dados de usuários e histórico |
| Redis | Cache e filas |
| The Graph | Indexação de eventos on-chain |
| WebSocket (Socket.io) | Atualizações em tempo real |
| Bull MQ | Filas de processamento |

### Blockchain / Web3
| Tecnologia | Uso |
|---|---|
| Solidity ^0.8.20 | Smart Contracts |
| Hardhat | Ambiente de desenvolvimento |
| Polygon (PoS) | Rede principal (baixas taxas) |
| OpenZeppelin | Contratos base auditados |
| Chainlink / UMA | Oráculo de resolução |
| USDC | Token de liquidação |

### DevOps
| Tecnologia | Uso |
|---|---|
| Docker + Docker Compose | Containers |
| GitHub Actions | CI/CD |
| AWS / GCP | Infraestrutura cloud |
| Vercel | Deploy do frontend |

---

## 📦 Módulos do Projeto

### 1. 🎨 Frontend (`/apps/web`)
- [ ] Landing page e marketing
- [ ] Listagem e busca de mercados
- [ ] Página de detalhe do mercado (gráfico, orderbook, posições)
- [ ] Interface de criação de mercado
- [ ] Dashboard do usuário (posições, histórico, P&L)
- [ ] Integração de carteira (MetaMask, WalletConnect, Coinbase Wallet)
- [ ] Notificações em tempo real

### 2. ⚙️ Backend API (`/apps/api`)
- [ ] Autenticação via assinatura de carteira (SIWE)
- [ ] Endpoints de mercados (CRUD)
- [ ] Orderbook off-chain para matching de ordens
- [ ] Webhook de eventos on-chain
- [ ] Sistema de notificações
- [ ] Admin panel para moderação

### 3. ⛓️ Smart Contracts (`/packages/contracts`)
- [ ] `MarketFactory.sol` — Criação de mercados
- [ ] `ConditionalTokens.sol` — Tokens de shares (Sim/Não)
- [ ] `CTFExchange.sol` — CLOB para negociação
- [ ] `UmaOracle.sol` — Integração com oráculo
- [ ] `FeeController.sol` — Coleta de taxas da plataforma
- [ ] Scripts de deploy e testes

### 4. 📊 Indexer (`/apps/indexer`)
- [ ] Subgraph no The Graph para indexação
- [ ] Eventos: MarketCreated, OrderFilled, MarketResolved
- [ ] Queries GraphQL para frontend

### 5. 🔮 Oracle Service (`/apps/oracle`)
- [ ] Integração com UMA Protocol
- [ ] Sistema de proposta e disputa de resultados
- [ ] Validação automática de eventos verificáveis
- [ ] Painel de curadoria manual

---

## 📜 Smart Contracts

### MarketFactory.sol
```solidity
// Cria novos mercados de predição
function createMarket(
    string calldata question,      // "O BTC vai superar $100k em 2025?"
    uint256 resolutionTime,        // Timestamp de resolução
    address oracle,                // Endereço do oráculo
    uint256 initialLiquidity      // Liquidez inicial em USDC
) external returns (address marketAddress);
```

### ConditionalTokens.sol
```solidity
// Baseado no padrão Gnosis Conditional Tokens
// Cada mercado gera 2 tokens: YES e NO
// YES + NO = 1 USDC (sempre somam 100%)
function splitPosition(
    IERC20 collateralToken,
    bytes32 parentCollectionId,
    bytes32 conditionId,
    uint256[] calldata partition,
    uint256 amount
) external;
```

### Fluxo de uma aposta
```
1. Usuário aprova USDC para o contrato
2. Deposita USDC → recebe tokens YES + NO
3. Vende os tokens que não quer no CLOB
4. Após resolução, tokens vencedores valem 1 USDC cada
5. Usuário resgata tokens → recebe USDC
```

---

## 🔮 Sistema de Oráculos

### Processo de Resolução (via UMA)

```
[Mercado expira]
       ↓
[Proposer submete resultado + bond em USDC]
       ↓
[Período de disputa: 48h]
       ↓ (sem disputa)              ↓ (com disputa)
[Resultado confirmado]        [Votação dos UMA holders]
       ↓                                ↓
[Mercado resolvido]           [Resultado por maioria]
       ↓
[Vencedores podem resgatar tokens]
```

### Tipos de Resolução
| Tipo | Exemplos | Método |
|---|---|---|
| Automático | Preço de cripto, placar de jogo | Chainlink / API verificada |
| Curadoria | Eventos políticos, notícias | UMA + votação |
| Disputa | Casos ambíguos | UMA Optimistic Oracle |

---

## 🗺️ Roadmap

### Fase 1 — MVP (Meses 1–4)
- [ ] Smart contracts básicos (MarketFactory + ConditionalTokens)
- [ ] Frontend: listagem e detalhe de mercados
- [ ] Integração de carteira
- [ ] Deploy na testnet (Mumbai)
- [ ] Testes unitários dos contratos

### Fase 2 — Beta (Meses 5–8)
- [ ] CLOB exchange (orderbook on-chain)
- [ ] Integração com UMA Oracle
- [ ] Backend API completo
- [ ] Indexer (The Graph)
- [ ] Auditoria de segurança dos contratos
- [ ] Deploy na mainnet Polygon

### Fase 3 — Produção (Meses 9–12)
- [ ] Programa de liquidez / market makers
- [ ] App mobile (React Native)
- [ ] SDK público para criadores de mercado
- [ ] Governança descentralizada (DAO)
- [ ] Expansão para outras redes (Base, Arbitrum)

### Fase 4 — Escala (Meses 13–18)
- [ ] Mercados contínuos (não apenas binários)
- [ ] API para traders algorítmicos
- [ ] Parcerias com provedores de dados
- [ ] Licenciamento e estrutura legal

---

## 💰 Estimativas de Tempo e Custo

### Tempo de Desenvolvimento

| Módulo | Dificuldade | Tempo Estimado |
|---|---|---|
| Frontend | ⭐⭐⭐ Média | 3–4 meses |
| Backend / API | ⭐⭐⭐⭐ Alta | 3–5 meses |
| Smart Contracts | ⭐⭐⭐⭐⭐ Muito Alta | 4–6 meses |
| Oracle Integration | ⭐⭐⭐⭐⭐ Muito Alta | 2–3 meses |
| Indexer | ⭐⭐⭐ Média | 1–2 meses |
| DevOps / Infra | ⭐⭐⭐ Média | 1–2 meses |
| **Total** | | **12–18 meses** |

### Custos Estimados

| Item | Custo Estimado (USD) |
|---|---|
| Equipe dev (12 meses) | $200k – $500k |
| Auditoria de contratos | $50k – $200k |
| Infraestrutura cloud | $2k – $10k/mês |
| Taxas legais e compliance | $30k – $100k |
| Bug bounty program | $20k – $50k |
| **Total MVP** | **~$300k – $860k** |

---

## ⚠️ Riscos e Mitigações

| Risco | Impacto | Probabilidade | Mitigação |
|---|---|---|---|
| Bug em smart contract | 🔴 Crítico | Média | Auditoria + bug bounty |
| Oráculo manipulado | 🔴 Crítico | Baixa | UMA dispute system |
| Falta de liquidez | 🟠 Alto | Alta | Incentivos para market makers |
| Hack / exploit | 🔴 Crítico | Baixa | Auditoria + pause mechanism |
| Regulação adversa | 🟠 Alto | Média | Estrutura offshore + geo-block |
| Competição | 🟡 Médio | Alta | Diferenciais de UX e nichos |

---

## ⚖️ Regulação e Compliance

> **Aviso:** Plataformas de mercado de predição operam em zona cinzenta legal em muitos países.

### Considerações Legais
- **EUA**: Regulado pela CFTC como derivativo — Polymarket foi multado em $1.4M em 2022 e bloqueou usuários americanos
- **Brasil**: CVM e Banco Central ainda não têm regulação clara para DeFi/mercados de predição
- **Estratégia recomendada**: Estrutura offshore (ex: Ilhas Cayman), geo-blocking de jurisdições restritas, KYC para grandes volumes

### Medidas de Compliance
- [ ] KYC/AML para depósitos acima de threshold
- [ ] Geo-blocking de IPs restritos (EUA, países sancionados)
- [ ] Termos de serviço e política de privacidade
- [ ] Parceria com escritório jurídico especializado em DeFi
- [ ] Auditoria legal antes do lançamento na mainnet

---

## 👥 Time Necessário

| Cargo | Qtd | Responsabilidade |
|---|---|---|
| Solidity Engineer | 2 | Smart contracts, testes, deploy |
| Backend Engineer | 2 | API, indexer, infra |
| Frontend Engineer | 2 | UI/UX, integração Web3 |
| DevOps Engineer | 1 | CI/CD, cloud, monitoramento |
| Smart Contract Auditor | Externo | Auditoria de segurança |
| Advogado DeFi | Externo | Compliance e estrutura legal |
| Product Manager | 1 | Roadmap e priorização |

---

## 📁 Estrutura de Pastas

```
predictchain/
├── apps/
│   ├── web/                  # Frontend Next.js
│   ├── api/                  # Backend API
│   ├── indexer/              # The Graph subgraph
│   └── oracle/               # Oracle service
├── packages/
│   ├── contracts/            # Smart Contracts Solidity
│   │   ├── src/
│   │   │   ├── MarketFactory.sol
│   │   │   ├── ConditionalTokens.sol
│   │   │   ├── CTFExchange.sol
│   │   │   └── UmaOracle.sol
│   │   ├── test/
│   │   └── scripts/
│   ├── sdk/                  # SDK público
│   └── ui/                   # Componentes compartilhados
├── docs/                     # Documentação técnica
├── docker-compose.yml
└── README.md
```

---

## 🔗 Referências e Recursos

- [Polymarket — Docs técnicas](https://docs.polymarket.com)
- [UMA Protocol](https://docs.umaproject.org)
- [Gnosis Conditional Tokens](https://docs.gnosis.io/conditionaltokens)
- [The Graph — Subgraph development](https://thegraph.com/docs)
- [OpenZeppelin Contracts](https://docs.openzeppelin.com/contracts)
- [Chainlink Data Feeds](https://docs.chain.link/data-feeds)
- [Wagmi — React Hooks for Web3](https://wagmi.sh)

---

*Documento gerado em: Abril 2026*  
*Versão: 1.0.0*
