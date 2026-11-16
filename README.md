# OficinaPro

**Grupo:** GarageOps  
**Curso:** Análise e Desenvolvimento de Sistemas — UniFAAT  
**Projeto Experimental:** 2027/1  
**Status:** Documentação técnica em elaboração e revisão; implementação do MVP a desenvolver.

---

## 1. Visão Geral

O **OficinaPro** é uma plataforma SaaS de gestão operacional destinada a oficinas mecânicas de pequeno e médio porte.

O sistema tem como objetivo centralizar e organizar o fluxo de atendimento das oficinas, desde o cadastro de clientes e veículos até a abertura da Ordem de Serviço (OS), diagnóstico, elaboração de orçamento, aprovação, execução dos serviços, controle de estoque, pagamentos e histórico de manutenção.

A proposta busca reduzir problemas comuns em oficinas que utilizam papel, planilhas, mensagens avulsas e controles separados, como perda de informações, inconsistências financeiras, falta de rastreabilidade e descontrole de peças.

O diferencial técnico do projeto está na implementação de regras de negócio integradas, arquitetura baseada em microsserviços, isolamento de dados por oficina, comunicação assíncrona, infraestrutura como código, testes automatizados e monitoramento.

## 2. Integrantes

| # | Nome Completo | RA | Papel Principal |
|---|---|---|---|
| 1 | Gabriel Carneiro da Silva | 6325300 | DevOps, Banco de Dados e Qualidade |
| 2 | Thiago Marques Bueno | 6325304 | Desenvolvedor Backend e Integrações |
| 3 | Matheus Gabriel Correa Braga Viana | 6325053 | Desenvolvedor Frontend e UX |
| 4 | Eloisa do Santos Brandão | 2325096 | Product Owner e Analista de Requisitos |

A distribuição de papéis e responsabilidades será formalizada pelo grupo no planejamento de sprints.

## 3. Escopo do Projeto

O OficinaPro será desenvolvido para atender às principais necessidades operacionais de uma oficina mecânica.

### Funcionalidades centrais

- Gestão de oficinas e isolamento de dados por tenant.
- Cadastro e consulta de clientes.
- Cadastro e consulta de veículos.
- Abertura e acompanhamento de ordens de serviço.
- Registro de diagnósticos.
- Elaboração, revisão e decisão de orçamentos.
- Reserva integral de peças.
- Controle de estoque físico, reservado e disponível.
- Registro de consumo, liberação e perdas de peças.
- Registro de pagamentos e acompanhamento da situação financeira.
- Histórico de manutenção por veículo.
- Autenticação e controle de permissões por perfil.

### Funcionalidades previstas para evolução

Notificações avançadas, relatórios gerenciais, dashboards analíticos e gestão de assinaturas SaaS poderão ser desenvolvidos em etapas posteriores, conforme priorização do grupo.

## 4. Arquitetura Planejada

O projeto utilizará arquitetura de microsserviços, com responsabilidades separadas por domínio de negócio.

Para o MVP, estão previstos quatro serviços principais:

| Serviço | Responsabilidade |
|---|---|
| **Auth Service** | Autenticação, usuários, tenants, perfis e permissões. |
| **Work Order Service** | Clientes, veículos, ordens de serviço, diagnósticos e informações operacionais. |
| **Inventory Service** | Peças, saldos de estoque, reservas, movimentações, consumo e perdas. |
| **Billing Service** | Orçamentos, itens de orçamento, decisões, pagamentos e situação financeira. |

Os limites dos serviços poderão ser revisados pelo grupo conforme a evolução do projeto, mantendo a propriedade dos dados e a separação das responsabilidades.

### 4.1 Comunicação entre serviços

A estratégia planejada combina:

- **REST/JSON:** comunicação síncrona para consultas e operações que necessitam de resposta imediata.
- **RabbitMQ/AMQP:** comunicação assíncrona para eventos e integração entre serviços.

Para confiabilidade, estão previstos mecanismos como:

- Transactional Outbox;
- Inbox ou mecanismo equivalente de deduplicação;
- Chaves de idempotência;
- Retentativas controladas;
- Dead Letter Queue (DLQ);
- Identificadores de correlação e rastreabilidade.

### 4.2 Banco de dados

O banco de dados escolhido é o **PostgreSQL**, devido às necessidades transacionais e relacionais do sistema.

A arquitetura prevê a estratégia **Database-per-Service**, na qual cada microsserviço é responsável exclusivamente por seus próprios dados.

Para o MVP, está sendo considerada a utilização de bancos lógicos separados em uma mesma instância gerenciada, sujeita à validação técnica e acadêmica. Não serão utilizadas chaves estrangeiras físicas entre bancos pertencentes a microsserviços diferentes.

## 5. Fluxo Principal do Sistema

O fluxo operacional principal foi definido da seguinte maneira:

```text
Cliente
   ↓
Cadastro ou consulta do cliente e veículo
   ↓
Abertura da Ordem de Serviço
   ↓
Diagnóstico
   ↓
Elaboração do orçamento
   ↓
Decisão do cliente
   ├── Recusa
   │     ↓
   │   Encerramento ou revisão do orçamento
   │
   └── Aceitação
         ↓
       Solicitação de reserva das peças
         ↓
       Inventory verifica a disponibilidade integral
         ├── Reserva recusada
         │     ↓
         │   Execução bloqueada
         │
         └── Reserva confirmada
               ↓
             Aprovação operacional
               ↓
             Execução do serviço
               ↓
             Consumo definitivo das peças utilizadas
               ↓
             Liberação das reservas não utilizadas
               ↓
             Conclusão operacional da OS
               ↓
             Atualização do histórico do veículo
               ↓
             Acompanhamento financeiro e pagamentos
```

### 5.1 Regras fundamentais do fluxo

- A aceitação do orçamento pelo cliente não representa, sozinha, a aprovação operacional definitiva.
- A execução depende da confirmação da reserva integral das peças necessárias.
- A reserva não representa consumo definitivo nem reduz imediatamente a quantidade física do estoque.
- A saída definitiva ocorre quando a peça é efetivamente utilizada.
- Peças reservadas e não utilizadas podem ter sua reserva liberada.
- Peças perdidas ou inutilizadas devem ser registradas como perda ou descarte.
- A situação operacional da OS é independente da situação financeira.

### 5.2 Estados operacionais e financeiros

Estados operacionais previstos para a OS:

- `ABERTA`
- `EM_DIAGNOSTICO`
- `AGUARDANDO_APROVACAO`
- `AGUARDANDO_RESERVA`
- `EM_EXECUCAO`
- `CONCLUIDA`
- `CANCELADA`

Estados financeiros previstos:

- `PENDENTE`
- `PARCIAL`
- `QUITADA`

As decisões `APROVADA` e `RECUSADA` pertencem ao orçamento, e não representam estados operacionais da OS.

## 6. Tecnologias Planejadas

| Área | Tecnologia |
|---|---|
| Backend | Node.js, TypeScript e NestJS |
| Frontend | React, TypeScript e Vite |
| Banco de dados | PostgreSQL |
| Mensageria | RabbitMQ |
| Containers | Docker e Docker Compose |
| Testes backend | Jest e Supertest |
| Testes frontend | React Testing Library |
| CI/CD | GitHub Actions |
| Infraestrutura como código | Terraform |
| Cloud | AWS |
| Monitoramento | Prometheus e Grafana |

As tecnologias representam a arquitetura planejada. Sua implementação e validação prática deverão ser registradas conforme o desenvolvimento avançar.

## 7. Integração Externa

O Termo de Requisitos e Aceite do Projeto Experimental exige pelo menos uma API externa relevante ao domínio do sistema.

As alternativas inicialmente consideradas são:

| API | Finalidade |
|---|---|
| FIPE | Consulta de informações e valores de referência de veículos. |
| ViaCEP | Consulta de endereço a partir do CEP, auxiliando o cadastro de clientes. |

**Status:** escolha pendente de formalização pelo grupo e validação da integração.

A documentação não considera essas integrações implementadas até que existam código, testes e evidências de funcionamento.

## 8. Documentação do Projeto

A documentação está organizada em duas áreas.

### 8.1 Documentos técnicos obrigatórios

Localização: `docs/arquitetura/`

| Arquivo | Documento |
|---|---|
| [01-solicitacao-do-sistema.md](docs/arquitetura/01-solicitacao-do-sistema.md) | Solicitação do Sistema |
| [02-requisitos-funcionais-nao-funcionais.md](docs/arquitetura/02-requisitos-funcionais-nao-funcionais.md) | Requisitos Funcionais e Não Funcionais |
| [03-diagrama-fluxo-de-dados.md](docs/arquitetura/03-diagrama-fluxo-de-dados.md) | Diagrama de Fluxo de Dados |
| [04-modelo-entidade-relacionamento.md](docs/arquitetura/04-modelo-entidade-relacionamento.md) | Modelo Entidade-Relacionamento |
| [05-arquitetura-nuvem.md](docs/arquitetura/05-arquitetura-nuvem.md) | Desenho de Arquitetura em Nuvem |
| [06-c4-model.md](docs/arquitetura/06-c4-model.md) | C4 Model |
| [07-registro-decisoes-arquitetonicas-adr.md](docs/arquitetura/07-registro-decisoes-arquitetonicas-adr.md) | Registro de Decisões Arquitetônicas |
| [08-planejamento-sprints-tarefas.md](docs/arquitetura/08-planejamento-sprints-tarefas.md) | Planejamento de Sprints e Tarefas |

### 8.2 Documentação complementar

Localização: `docs/requisitos/`

| Arquivo | Finalidade |
|---|---|
| [backlog-requisitos.md](docs/requisitos/backlog-requisitos.md) | Organização dos requisitos e funcionalidades. |
| [criterios-aceite.md](docs/requisitos/criterios-aceite.md) | Critérios verificáveis para validação das funcionalidades. |
| [duvidas-professor.md](docs/requisitos/duvidas-professor.md) | Questões pendentes de orientação acadêmica. |
| [fluxos.md](docs/requisitos/fluxos.md) | Detalhamento dos fluxos de negócio. |
| [glossario.md](docs/requisitos/glossario.md) | Definições dos termos utilizados no projeto. |
| [historias-usuario.md](docs/requisitos/historias-usuario.md) | Histórias de usuário e critérios de aceite. |
| [mvp.md](docs/requisitos/mvp.md) | Escopo funcional e técnico do MVP. |
| [regras-negocio.md](docs/requisitos/regras-negocio.md) | Regras que orientam o comportamento do sistema. |

Os documentos complementares apoiam a especificação e a rastreabilidade do projeto. Eles não substituem os oito documentos técnicos exigidos pelo professor.

## 9. Requisitos Acadêmicos e Qualidade

Conforme o Termo de Requisitos e Aceite do Projeto Experimental, o desenvolvimento deverá contemplar:

- Arquitetura baseada em microsserviços.
- Pelo menos uma API externa relevante integrada.
- Aplicação implantada em provedor de nuvem de grande porte.
- Infraestrutura provisionada por Terraform.
- CI/CD com GitHub Actions, incluindo automação de build e deploy dos microsserviços.
- Testes automatizados unitários ou de integração.
- Cobertura mínima global de 60% sobre o código-fonte dos microsserviços.
- Prometheus para coleta de métricas.
- Grafana para dashboards e acompanhamento operacional.
- Docker e Docker Compose para ambiente local e contingência de demonstração.

A stack de monitoramento deverá estar funcional e acessível no momento da defesa.

## 10. Organização do Repositório de Trabalho

A estrutura atual de documentação está organizada da seguinte maneira:

```text
.
├── README.md
└── docs/
    ├── arquitetura/
    │   ├── 01-solicitacao-do-sistema.md
    │   ├── 02-requisitos-funcionais-nao-funcionais.md
    │   ├── 03-diagrama-fluxo-de-dados.md
    │   ├── 04-modelo-entidade-relacionamento.md
    │   ├── 05-arquitetura-nuvem.md
    │   ├── 06-c4-model.md
    │   ├── 07-registro-decisoes-arquitetonicas-adr.md
    │   └── 08-planejamento-sprints-tarefas.md
    └── requisitos/
        ├── backlog-requisitos.md
        ├── criterios-aceite.md
        ├── duvidas-professor.md
        ├── fluxos.md
        ├── glossario.md
        ├── historias-usuario.md
        ├── mvp.md
        └── regras-negocio.md
```

### 10.1 Estrutura exigida no repositório oficial do professor

Para a submissão no repositório oficial `UniFAAT-projeto-experimental-2027-1`, o grupo deverá organizar os entregáveis na pasta `GarageOps/`, conforme as instruções do README oficial.

A estrutura de submissão deverá conter o README do grupo e os oito documentos numerados diretamente dentro da pasta:

```text
GarageOps/
├── README.md
├── 01-solicitacao-do-sistema.md
├── 02-requisitos-funcionais-nao-funcionais.md
├── 03-diagrama-fluxo-de-dados.md
├── 04-modelo-entidade-relacionamento.md
├── 05-arquitetura-nuvem.md
├── 06-c4-model.md
├── 07-registro-decisoes-arquitetonicas-adr.md
└── 08-planejamento-sprints-tarefas.md
```

Os documentos complementares poderão permanecer organizados em `docs/requisitos/` no repositório de trabalho e ser incluídos na pasta oficial caso o grupo considere adequado ou o professor solicite.

**Importante:** o README da raiz do repositório oficial do professor, o termo de aceite e os templates originais não devem ser alterados. As Pull Requests para o repositório oficial devem modificar somente a pasta `GarageOps/`.

## 11. Status Atual do Projeto

<badge color="success">Documentação numerada elaborada</badge>

| Frente | Situação |
|---|---|
| Oito documentos técnicos | Arquivos criados e em revisão. |
| Documentação complementar | Arquivos criados e em revisão. |
| Arquitetura de microsserviços | Proposta documentada. |
| Modelo de dados | Proposta documentada. |
| Fluxo de reserva de estoque | Regras consolidadas na documentação. |
| API externa | Escolha e integração pendentes. |
| Distribuição de responsabilidades | Pendente de definição pelo grupo. |
| Estimativa de custos AWS | Pendente de dimensionamento. |
| Ambiente Docker Compose | Configuração a implementar e validar. |
| Backend e frontend | Implementação a desenvolver. |
| Infraestrutura Terraform | Implementação e validação pendentes. |
| CI/CD | Configuração pendente. |
| Testes automatizados | Implementação e medição pendentes. |
| Prometheus e Grafana | Configuração pendente. |
| Pasta oficial `GarageOps/` | Organização e submissão no repositório do professor pendentes. |

A existência de documentação arquitetural não representa comprovação de que os serviços, recursos de nuvem ou funcionalidades já estejam implementados.

## 12. Próximas Atividades

1. Revisar a consistência entre os oito documentos e a documentação complementar.
2. Confirmar com o grupo a distribuição de responsabilidades.
3. Formalizar a escolha da API externa.
4. Validar a arquitetura, a persistência e a viabilidade dos recursos AWS.
5. Atualizar as estimativas e o cronograma das sprints.
6. Preparar a estrutura de entrega `GarageOps/` no repositório oficial.
7. Iniciar a implementação e registrar evidências de testes e funcionamento.

---

**Grupo GarageOps — OficinaPro**  
Projeto Experimental — Análise e Desenvolvimento de Sistemas  
UniFAAT — 2027/1
