# Dúvidas para validação com o professor — OficinaPro

> **Status:** 🔵 Pauta de alinhamento acadêmico — Grupo GarageOps
>
> **Objetivo:** organizar dúvidas técnicas, critérios de avaliação e decisões acadêmicas que dependem de validação com o professor orientador durante o desenvolvimento do OficinaPro.

---

## 1. Escopo e MVP

### 1.1 Escopo mínimo obrigatório

**Pergunta:**

O fluxo operacional definido pelo grupo para o MVP atende integralmente aos requisitos mínimos esperados para a disciplina?

**Contexto — Fluxo definido pelo grupo:**

```text
Cliente
  ↓
Veículo
  ↓
Ordem de Serviço (ABERTA)
  ↓
Diagnóstico (EM_DIAGNOSTICO)
  ↓
Orçamento (AGUARDANDO_APROVACAO)
  ├── Recusado (RECUSADA)
  │     ├── Encerramento definitivo da OS
  │     └── Elaboração de orçamento revisado
  │             ↓
  │       AGUARDANDO_APROVACAO
  │
  └── Aprovado (APROVADA)
          ↓
      Baixa automática das peças no saldo disponível
          ↓
      Execução do serviço (EM_EXECUCAO)
          ↓
      Aguardando pagamento (AGUARDANDO_PAGAMENTO)
          ↓
      Quitação financeira
          ↓
      OS concluída (CONCLUIDA)
          ↓
      Histórico de manutenção do veículo
```

Em caso de cancelamento após a aprovação, o tratamento das peças dependerá de sua condição física: peças não instaladas ou consumidas poderão retornar ao estoque mediante estorno; peças já instaladas ou consumidas deverão ser registradas como perda operacional ou descarte.

**Status:** 🔵 Pendente de validação.

### 1.2 Funcionalidades complementares

Notificações, relatórios gerenciais e dashboards analíticos podem permanecer classificados como funcionalidades pós-MVP, sem impacto negativo na avaliação da entrega principal?

**Status:** 🔵 Pendente de validação.

---

## 2. Arquitetura e Mensageria

### 2.1 Divisão de microsserviços

A arquitetura planejada contempla os seguintes serviços principais para o MVP:

- `Auth Service` — autenticação e controle de permissões (RBAC).
- `Work Order Service` — clientes, veículos, ordens de serviço e diagnóstico.
- `Inventory Service` — cadastro de peças, saldo e movimentações de estoque.
- `Billing Service` — orçamentos e controle financeiro da OS.

Os serviços de notificações e relatórios serão considerados possibilidades de evolução pós-MVP.

**Pergunta:**

Essa divisão e granularidade de microsserviços atendem adequadamente aos critérios arquiteturais da disciplina?

**Status:** 🔵 Pendente de validação.

### 2.2 Uso de RabbitMQ — Comunicação assíncrona

O grupo definiu o uso do RabbitMQ para desacoplar eventos críticos entre os microsserviços.

Um dos fluxos previstos é:

```text
Billing Service
      ↓
Evento: Orçamento aprovado
      ↓
RabbitMQ
      ↓
Inventory Service
      ↓
Baixa automática no saldo disponível
```

Também está previsto o tratamento de eventos relacionados ao cancelamento da OS, considerando a necessidade de estorno de peças ainda não instaladas ou consumidas e o registro de perda ou descarte quando já tiverem sido utilizadas.

**Pergunta:**

A combinação de APIs síncronas (REST) com mensageria assíncrona nesses fluxos específicos atende às expectativas de uma arquitetura distribuída e orientada a eventos?

**Status:** 🔵 Pendente de validação.

---

## 3. Banco de Dados

### 3.1 Estratégia de persistência

Considerando o ambiente de desenvolvimento e a entrega acadêmica, é obrigatório isolar uma instância ou database PostgreSQL por microsserviço, ou o uso de schemas separados dentro de uma mesma instância PostgreSQL é aceitável?

**Status:** 🔵 Pendente de validação.

### 3.2 Artefatos de dados

A entrega do Diagrama Entidade-Relacionamento (DER) e o versionamento do banco de dados por meio de migrations (TypeORM ou Prisma) são requisitos obrigatórios na documentação final?

**Status:** 🔵 Pendente de validação.

---

## 4. Testes e Qualidade

### 4.1 Tipos de testes e cobertura mínima

O grupo estabeleceu uma meta inicial de **60% de cobertura** de testes automatizados, utilizando Jest e priorizando as regras críticas de negócio.

Os testes deverão ser executados automaticamente no pipeline de CI.

**Pergunta:**

Essa meta de cobertura e o foco em testes unitários e de integração de API são suficientes para a avaliação, ou também serão exigidos testes End-to-End (E2E) no frontend?

**Status:** 🔵 Pendente de validação.

---

## 5. CI/CD, AWS e Infraestrutura

### 5.1 GitHub Actions e pipeline

O pipeline planejado contempla as seguintes etapas:

```text
Lint
  ↓
Testes automatizados
  ↓
Build
```

**Pergunta:**

Há alguma etapa adicional obrigatória, como análise estática com SonarQube ou build e publicação automática de imagens Docker?

**Status:** 🔵 Pendente de validação.

### 5.2 Provisionamento AWS e Terraform

O grupo prevê utilizar Terraform para modelagem da infraestrutura na AWS e Docker Compose para execução do ambiente local de desenvolvimento.

**Pergunta:**

É obrigatório manter o ambiente ativo e publicado na AWS no momento da apresentação final, ou a entrega dos scripts de Infraestrutura como Código (Terraform), acompanhada da execução local via Docker Compose, atende aos critérios da disciplina?

**Status:** 🔵 Pendente de validação.

---

## 6. Observabilidade

### 6.1 Prometheus e Grafana

**Pergunta:**

A implementação de coleta de métricas com Prometheus e dashboards no Grafana é obrigatória para a avaliação de infraestrutura e DevOps ou configura um diferencial opcional?

**Status:** 🔵 Pendente de validação.

---

## 7. Documentação e Avaliação Acadêmica

### 7.1 Entregáveis obrigatórios

Além do código-fonte e da documentação de requisitos já estruturada no repositório, incluindo MVP, Fluxos, Regras de Negócio, Histórias de Usuário e Critérios de Aceite:

**Pergunta:**

Quais diagramas UML e arquiteturais, como arquitetura, sequência, casos de uso e DER, são obrigatórios na entrega final?

**Status:** 🔵 Pendente de validação.

### 7.2 Avaliação individual e processo Git

O grupo utiliza branches organizadas por contexto (`main`, `develop`, `feature/*`, `fix/*` e `docs/*`), com revisão das alterações por Pull Requests.

**Pergunta:**

Como será mensurada a contribuição individual de cada integrante na nota final, considerando commits, branches, Pull Requests e participação no desenvolvimento?

**Status:** 🔵 Pendente de validação.

---

## 8. Decisões de Domínio já Resolvidas pelo Grupo

Os pontos abaixo faziam parte do levantamento inicial de dúvidas, mas já foram definidos pelo grupo e incorporados à documentação do OficinaPro. Portanto, não precisam ser apresentados como dúvidas de domínio na orientação.

- [x] **Segurança e perfis (RBAC):** autenticação de usuários e controle de acesso por perfil no MVP. Os perfis definidos são Atendente, Mecânico, Estoque e Financeiro.
- [x] **CPF único:** todo cliente deve possuir CPF válido e único no sistema, impedindo cadastros duplicados.
- [x] **Placa única:** todo veículo deve possuir placa única no sistema e estar associado a um cliente cadastrado.
- [x] **Orçamento recusado:** a OS poderá ser encerrada definitivamente ou receber um novo orçamento revisado, vinculado à mesma OS, sem perder o histórico da tratativa anterior (`RN-ORC-003`).
- [x] **Estoque insuficiente:** a aprovação do orçamento deverá ser bloqueada quando não houver quantidade suficiente das peças solicitadas no estoque (`RN-EST-002`).
- [x] **Baixa automática:** a baixa das quantidades previstas no orçamento ocorrerá no saldo disponível do estoque no momento da aprovação (`RN-EST-003`).
- [x] **Prevenção de duplicidade:** as movimentações de saída deverão estar vinculadas à OS e ao orçamento, impedindo baixas duplicadas para o mesmo item e orçamento.
- [x] **Cancelamento e estorno:** peças baixadas, mas ainda não instaladas ou consumidas, poderão retornar ao estoque disponível por meio de estorno.
- [x] **Peças utilizadas:** peças já instaladas ou consumidas não poderão retornar ao estoque comercializável, devendo ser registradas como perda operacional ou descarte.
- [x] **Rastreabilidade do estoque:** as movimentações originais, os estornos e os registros de perda deverão ser preservados.
- [x] **Pagamento:** o registro interno do pagamento integral no `Billing Service` será condição para a OS assumir o status `CONCLUIDA` (`RN-FIN-002`).
- [x] **Revisão do orçamento:** quando houver negociação após uma recusa, um novo orçamento poderá ser elaborado e encaminhado ao cliente, com retorno da OS para `AGUARDANDO_APROVACAO`.

**Status:** 🟢 Definido pelo grupo.

---

## 9. Pauta Prioritária para a Próxima Reunião

Resumo das questões que precisam de orientação do professor:

1. Validação do escopo do MVP e da divisão dos quatro microsserviços principais.
2. Obrigatoriedade de implantação ativa na AWS ou possibilidade de demonstração via Terraform e Docker Compose.
3. Suficiência da meta de 60% de cobertura de testes unitários e de integração no GitHub Actions.
4. Obrigatoriedade de Prometheus e Grafana.
5. Lista exata de diagramas e documentos exigidos na entrega final.
6. Critérios de avaliação da contribuição individual por meio de Git, commits e Pull Requests.

---

## 10. Registro das Respostas e Decisões Derivadas

As orientações recebidas durante as reuniões deverão ser registradas nesta tabela, indicando seus impactos nos requisitos, na arquitetura e nos demais documentos do projeto.

| Data | Pergunta / Tema | Orientação Recebida | Impacto / Documentos Afetados |
| --- | --- | --- | --- |
| — | — | — | — |

---

## 11. Histórico de Alterações

| Data | Alteração | Responsável |
| --- | --- | --- |
| 26/09/2026 | Criação da lista inicial de dúvidas. | Eloísa Brandão |
| 27/10/2026 | Consolidação da pauta acadêmica e revisão das dúvidas de domínio já discutidas pelo grupo. | Eloísa Brandão |
| 12/11/2026 | Atualização e ajustes da documentação: alinhamento das dúvidas acadêmicas à arquitetura e ao fluxo de mensageria definidos, revisão das decisões de domínio, correção dos perfis de acesso e inclusão das regras de baixa, estorno e tratamento de peças instaladas ou consumidas. | Eloísa Brandão |
