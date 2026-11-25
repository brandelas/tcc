# OficinaPro — GarageOps

Plataforma web SaaS multi-tenant para gestão operacional de oficinas mecânicas de pequeno e médio porte, desenvolvida pelo grupo **GarageOps** no Projeto Experimental do curso de Análise e Desenvolvimento de Sistemas da UniFAAT.

Este fork (`brandelas/tcc`) é o espaço de trabalho de requisitos e de revisão da documentação. O código do sistema e o README principal do produto ficam no repositório de origem, [`gcdsofc/tcc`](https://github.com/gcdsofc/tcc), pasta `mvp/`.

---

## Onde está cada coisa

| Conteúdo | Local | Situação |
|---|---|---|
| Os 8 documentos oficiais + README do grupo, prontos para a PR ao orientador | [`entrega/Projetos/OficinaPro/`](entrega/Projetos/OficinaPro/README.md) | **Fonte de verdade** desta revisão (25/11/2026) |
| Documentos oficiais já entregues (01 a 04) | Fork do repositório oficial, branch `docs/garageops-entrega-3-dfd`, pasta `Projetos/OficinaPro` | Substituídos pelas versões revisadas em `entrega/` |
| Código do MVP (NestJS, React, PostgreSQL, Docker Compose) | [`gcdsofc/tcc`](https://github.com/gcdsofc/tcc), pasta `mvp/` | Ainda não sincronizado com este fork |
| Dúvidas para o orientador e decisões pendentes do grupo | [`docs/requisitos/duvidas-professor.md`](docs/requisitos/duvidas-professor.md) | Atualizado em 25/11/2026 |
| Versão de 16/11/2026 dos 8 documentos (`docs/arquitetura/01` a `08`) e demais arquivos de `docs/requisitos/` | `docs/` | **Históricos.** Descrevem o modelo anterior com estoque e não devem ser usados na entrega |

Para entregar: copiar o conteúdo de `entrega/Projetos/OficinaPro/` para a pasta `Projetos/OficinaPro/` do fork do repositório oficial e abrir a PR com o título `[GarageOps] - Entrega N - Descrição`, alterando apenas essa pasta.

---

## O sistema

O OficinaPro permite que várias oficinas usem a mesma aplicação, cada uma vendo somente seus próprios usuários, clientes, veículos, ordens e pagamentos.

**Fluxo principal:** a oficina cadastra cliente e veículo (com ViaCEP e FIPE) → abre a ordem → o mecânico registra o diagnóstico → a oficina envia um orçamento versionado, com peças digitadas manualmente → o cliente aprova ou recusa a versão → a execução só começa após a aprovação da versão vigente → a ordem fica pronta para retirada → o financeiro registra os pagamentos → a ordem é encerrada quando quitada.

**Papéis:** administrador da plataforma (gerencia oficinas, sem acesso aos dados delas), administrador da oficina, atendente, mecânico, financeiro e cliente.

**Fora do MVP:** estoque (catálogo, saldo e reservas de peças), pagamento online, envio real de SMS/e-mail, aplicativo nativo.

---

## Arquitetura

| Item | Implementado no MVP | Planejado |
|---|---|---|
| Microsserviços | `identity`, `customers` e `workorders` (NestJS + TypeScript), uma imagem Docker por serviço | Billing, Notification, Audit e Reporting no 5º semestre |
| Banco | PostgreSQL 16, um banco lógico por serviço | Amazon RDS com usuário por serviço |
| Frontend | React 18 + Vite 6 (JavaScript) | TypeScript (proposta) |
| Integrações externas | ViaCEP e API FIPE, com preenchimento manual em caso de falha | Provedor de envio do código de convite |
| Mensageria | — | RabbitMQ |
| Ambiente local | Docker Compose | — |
| Nuvem e IaC | — | AWS (ECS Fargate, RDS, ALB) com Terraform |
| CI/CD | — | GitHub Actions |
| Testes | Jest, Supertest e testes de integração (`smoke`, `platform-check`, `team-check`) | Cobertura global ≥ 60% verificada no CI |
| Monitoramento | — | Prometheus e Grafana |

Detalhes nos Documentos 05 (nuvem), 06 (C4) e 07 (ADR).

---

## Grupo GarageOps

| Integrante | RA | Responsabilidade principal |
|---|---|---|
| Eloisa do Santos Brandão | 2325096 | Product Owner e Analista de Requisitos |
| Thiago Marques Bueno | 6325304 | Backend e Integrações |
| Matheus Gabriel Correa Braga Viana | 6325053 | Frontend e UX |
| Gabriel Carneiro da Silva | 6325300 | DevOps, Banco de Dados e Qualidade |

---

## Histórico deste README

As versões anteriores deste arquivo (a mais recente de 16/11/2026, commit `2736319`) descreviam o modelo com controle de estoque, Inventory Service e reserva de peças. Ela foi substituída em 25/11/2026 para refletir as decisões do grupo de 13/11 e 22/11/2026 e continua disponível no histórico do Git.
