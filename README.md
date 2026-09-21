# OficinaPro

Sistema de gestão operacional para oficinas mecânicas, desenvolvido pelo grupo **GarageOps** como parte do Projeto Experimental do curso de Análise e Desenvolvimento de Sistemas da UniFAAT.

Este repositório é destinado ao desenvolvimento técnico do sistema: código-fonte, ambiente local, banco de dados, infraestrutura, testes automatizados, pipelines e configurações de execução.

A documentação acadêmica oficial do TCC fica no fork do repositório institucional do professor.

---

## Visão Geral

O **OficinaPro** tem como objetivo centralizar o fluxo operacional de oficinas mecânicas de pequeno e médio porte.

O sistema cobre o processo desde a chegada do cliente até o fechamento financeiro da ordem de serviço:

1. cadastro do cliente;
2. cadastro do veículo;
3. abertura da ordem de serviço;
4. registro de diagnóstico;
5. criação e aprovação de orçamento;
6. execução do serviço;
7. baixa de peças no estoque;
8. fechamento financeiro;
9. registro de pagamento;
10. histórico de manutenção do veículo.

A proposta do sistema não é criar apenas cadastros isolados. O foco é representar um fluxo real de oficina, com regras de negócio, rastreabilidade, consistência de dados, integração externa e ambiente preparado para evolução em microsserviços.

---

## Grupo GarageOps

| Integrante | Responsabilidade Principal |
|---|---|
| Heloisa | Product Owner e Analista de Requisitos |
| Thiago | Desenvolvedor Backend e Integrações |
| Matheus | Desenvolvedor Frontend e UX |
| Gabriel | DevOps, Banco de Dados e Qualidade |

---

## Escopo Técnico do Repositório

Este repositório deve conter:

- código-fonte do backend;
- código-fonte do frontend;
- scripts e migrations de banco de dados;
- configuração de ambiente local;
- arquivos Docker;
- configurações de mensageria;
- testes automatizados;
- pipelines de CI/CD;
- infraestrutura como código;
- documentação técnica de execução.

Este repositório não substitui os documentos acadêmicos obrigatórios. Alterações nos documentos oficiais devem ser feitas no repositório de documentação.

---

## Arquitetura Planejada

O OficinaPro será estruturado em microsserviços, separando responsabilidades por domínio de negócio.

| Serviço | Responsabilidade |
|---|---|
| Auth Service | Autenticação, usuários, perfis e permissões |
| Customer Vehicle Service | Clientes, veículos e histórico de manutenção |
| Work Order Service | Ordens de serviço, diagnóstico, orçamento, aprovação e execução |
| Inventory Service | Peças, estoque, movimentações e alertas de reposição |
| Billing Service | Fechamento financeiro, pagamentos e status financeiro |
| Notification Service | Notificações relacionadas a orçamento, conclusão e cobrança |
| Reporting Service | Indicadores operacionais e relatórios gerenciais |

A separação em serviços será evoluída de forma incremental. No início, o foco é validar o domínio, a modelagem, o ambiente local e o fluxo principal do MVP.

---

## Fluxo Principal do Sistema

```text
Cliente chega na oficina
        ↓
Atendente cadastra cliente e veículo
        ↓
Atendente abre uma ordem de serviço
        ↓
Mecânico registra diagnóstico
        ↓
Sistema gera orçamento
        ↓
Cliente aprova ou recusa o orçamento
        ↓
Se aprovado, serviço é executado
        ↓
Peças utilizadas baixam do estoque
        ↓
Ordem de serviço é concluída
        ↓
Financeiro registra pagamento
        ↓
Histórico do veículo é atualizado
```

---

## Tecnologias Planejadas

| Área | Tecnologia |
|---|---|
| Backend | Node.js, TypeScript, NestJS |
| Frontend | React, TypeScript, Vite |
| Banco de Dados | PostgreSQL |
| Mensageria | RabbitMQ |
| Containers | Docker, Docker Compose |
| Testes Backend | Jest, Supertest |
| Testes Frontend | React Testing Library |
| CI/CD | GitHub Actions |
| Infraestrutura | Terraform |
| Cloud | AWS |
| Monitoramento | Prometheus, Grafana |

---

## Decisão de Banco de Dados

O banco principal escolhido para o OficinaPro é o **PostgreSQL**.

A escolha foi feita porque o sistema possui um núcleo transacional forte, com dados altamente relacionados e regras que exigem consistência.

Exemplos:

- uma ordem de serviço precisa estar vinculada a um cliente e a um veículo;
- um orçamento precisa estar vinculado a uma ordem de serviço;
- uma peça utilizada precisa existir no estoque;
- a baixa de estoque precisa ocorrer de forma consistente;
- um pagamento precisa estar vinculado a uma ordem de serviço;
- o histórico de manutenção do veículo não pode ficar inconsistente;
- o fechamento financeiro depende da soma correta de serviços, peças, descontos e pagamentos.

Por esses motivos, o PostgreSQL é mais adequado para o núcleo principal do sistema do que uma solução NoSQL. O modelo relacional oferece integridade referencial, transações ACID, constraints, relacionamentos, índices e consultas estruturadas.

Soluções NoSQL podem ser úteis em cenários de grande volume documental, dados pouco estruturados ou leitura altamente distribuída, mas não são a melhor escolha para o núcleo operacional e financeiro do OficinaPro.

---

## Ambiente Local

O ambiente local deve ser padronizado com Docker Compose para garantir que todos os integrantes trabalhem com a mesma configuração.

Serviços previstos no ambiente local:

| Serviço | Finalidade |
|---|---|
| PostgreSQL | Banco de dados principal |
| Adminer | Interface web para acesso ao banco |
| RabbitMQ | Mensageria entre serviços |
| RabbitMQ Management | Interface web para visualizar filas e mensagens |

---

## Pré-requisitos

Antes de rodar o projeto, instale:

- Git;
- Docker Desktop;
- Node.js LTS;
- npm ou pnpm;
- VS Code ou IDE equivalente.

Verifique as instalações:

```bash
git --version
docker --version
docker compose version
node --version
npm --version
```

---

## Configuração Inicial

Clone o repositório:

```bash
git clone URL_DO_REPOSITORIO
cd NOME_DO_REPOSITORIO
```

Crie o arquivo de ambiente local:

```bash
cp .env.example .env
```

No Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Suba os containers:

```bash
docker compose up -d
```

Verifique se os serviços estão ativos:

```bash
docker compose ps
```

---

## Serviços Locais

| Serviço | Endereço | Usuário | Senha |
|---|---|---|---|
| PostgreSQL | localhost:5432 | oficinapro | definida no `.env` |
| Adminer | http://localhost:8080 | oficinapro | definida no `.env` |
| RabbitMQ | localhost:5672 | garageops | definida no `.env` |
| RabbitMQ Management | http://localhost:15672 | garageops | definida no `.env` |

---

## Acesso ao Banco pelo Adminer

Acesse:

```text
http://localhost:8080
```

Use os seguintes dados:

```text
Sistema: PostgreSQL
Servidor: postgres
Usuário: oficinapro
Senha: definida no arquivo .env
Banco de dados: oficinapro
```

Observação: no Adminer, o servidor deve ser `postgres`, pois esse é o nome do serviço dentro da rede do Docker Compose. Para acessar o banco diretamente pela máquina local, use `localhost`.

---

## Comandos Úteis

Subir o ambiente:

```bash
docker compose up -d
```

Parar o ambiente:

```bash
docker compose down
```

Listar containers:

```bash
docker compose ps
```

Ver logs do PostgreSQL:

```bash
docker compose logs postgres
```

Ver logs do RabbitMQ:

```bash
docker compose logs rabbitmq
```

Reiniciar os serviços:

```bash
docker compose restart
```

Apagar volumes locais e recriar o ambiente:

```bash
docker compose down -v
docker compose up -d
```

Use `docker compose down -v` com cuidado, pois ele apaga os dados locais do banco.

---

## Estrutura Planejada do Repositório

```text
.
├── backend/
│   ├── auth-service/
│   ├── customer-vehicle-service/
│   ├── work-order-service/
│   ├── inventory-service/
│   ├── billing-service/
│   └── notification-service/
├── frontend/
├── database/
│   ├── migrations/
│   ├── seeds/
│   └── init.sql
├── infra/
│   ├── terraform/
│   └── monitoring/
├── docs/
├── docker-compose.yml
├── .env.example
└── README.md
```

A estrutura pode evoluir conforme o projeto amadurecer, mas alterações relevantes devem ser discutidas pelo grupo antes de serem aplicadas.

---

## Padrão de Branches

A branch principal deve permanecer estável.

Padrão recomendado:

```text
main
develop
feature/nome-da-tarefa
fix/nome-do-ajuste
docs/nome-da-documentacao
```

Exemplos:

```text
feature/docker-compose
feature/modelagem-banco
feature/auth-service
feature/work-order-service
feature/frontend-login
fix/correcao-env
docs/atualiza-readme
```

---

## Fluxo de Trabalho

1. Atualizar a branch local:

```bash
git pull origin develop
```

2. Criar uma branch para a tarefa:

```bash
git checkout -b feature/nome-da-tarefa
```

3. Fazer as alterações.

4. Verificar os arquivos modificados:

```bash
git status
```

5. Criar commit com mensagem objetiva:

```bash
git add .
git commit -m "Adiciona configuração inicial do Docker Compose"
```

6. Enviar a branch:

```bash
git push origin feature/nome-da-tarefa
```

7. Abrir Pull Request para revisão do grupo.

---

## Padrão de Commits

Use mensagens claras e no presente.

Exemplos:

```text
Adiciona configuração inicial do Docker Compose
Cria estrutura inicial de migrations
Implementa autenticação com JWT
Corrige conexão com PostgreSQL
Atualiza documentação de setup local
```

Evite mensagens vagas como:

```text
ajustes
update
arrumando coisas
teste
```

---

## Qualidade

O projeto deve manter os seguintes critérios:

- código revisado via Pull Request;
- ambiente local documentado;
- banco de dados versionado por migrations;
- testes automatizados para regras de negócio;
- cobertura mínima de 60%;
- pipeline de CI com build e testes;
- padronização de código;
- logs e métricas para monitoramento;
- documentação atualizada junto com mudanças relevantes.

---

## Testes

A estratégia de testes será definida por camada:

| Camada | Tipo de Teste |
|---|---|
| Backend | Testes unitários e de integração |
| Frontend | Testes de componentes e fluxos principais |
| Banco de Dados | Validação de migrations e integridade |
| Integrações | Testes com mocks ou ambiente sandbox |
| Pipeline | Execução automatizada em Pull Requests |

Regras críticas devem ter prioridade nos testes:

- autenticação e autorização;
- abertura de ordem de serviço;
- aprovação de orçamento;
- baixa de estoque;
- bloqueio de uso de peça sem estoque;
- cálculo de fechamento financeiro;
- registro de pagamento.

---

## Variáveis de Ambiente

As variáveis devem ficar no arquivo `.env`, criado localmente a partir do `.env.example`.

O arquivo `.env` não deve ser versionado.

Exemplo de variáveis esperadas:

```env
POSTGRES_DB=oficinapro
POSTGRES_USER=oficinapro
POSTGRES_PASSWORD=oficinapro123
POSTGRES_PORT=5432

DATABASE_URL=postgresql://oficinapro:oficinapro123@localhost:5432/oficinapro

RABBITMQ_USER=garageops
RABBITMQ_PASSWORD=garageops123
RABBITMQ_PORT=5672
RABBITMQ_MANAGEMENT_PORT=15672
```

---

## Segurança

Cuidados mínimos obrigatórios:

- não versionar `.env`;
- não commitar senhas reais;
- não expor tokens em logs;
- usar hash seguro para senhas;
- validar permissões por perfil;
- revisar Pull Requests antes de merge;
- usar ambiente sandbox para integrações de pagamento.

---

## Observabilidade

O projeto deverá evoluir para expor métricas operacionais dos serviços.

Métricas esperadas:

- tempo de resposta das APIs;
- quantidade de requisições;
- taxa de erro;
- saúde dos serviços;
- consumo de recursos;
- status de filas;
- eventos de negócio relevantes.

Ferramentas planejadas:

- Prometheus para coleta de métricas;
- Grafana para dashboards.

---

## Status Atual

Projeto em fase inicial de estruturação técnica.

Prioridades imediatas:

1. estruturar ambiente local com Docker Compose;
2. configurar PostgreSQL e Adminer;
3. configurar RabbitMQ;
4. definir modelagem inicial do banco;
5. criar base do backend;
6. criar base do frontend;
7. configurar pipeline inicial de CI.

---

## Observação

Este repositório é privado e destinado aos integrantes do grupo GarageOps. O acesso deve ser concedido apenas aos membros do grupo e, se necessário, ao professor orientador.
