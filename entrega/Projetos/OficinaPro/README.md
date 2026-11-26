# OficinaPro

## Grupo

**GarageOps**

## Integrantes

| # | Nome Completo | RA | Papel Principal |
|---|---------------|-----|-----------------|
| 1 | Gabriel Carneiro da Silva | 6325300 | DevOps, Banco de Dados e Qualidade |
| 2 | Thiago Marques Bueno | 6325304 | Desenvolvedor Backend e Integrações |
| 3 | Matheus Gabriel Correa Braga Viana | 6325053 | Desenvolvedor Frontend e UX |
| 4 | Eloisa do Santos Brandão | 2325096 | Product Owner e Analista de Requisitos |

## Descrição do Projeto

O **OficinaPro** é uma plataforma **SaaS multi-tenant** de gestão operacional para oficinas mecânicas de pequeno e médio porte. O sistema permite que múltiplas oficinas utilizem a mesma aplicação em nuvem, mantendo seus dados isolados por oficina (*tenant*).

Cada oficina terá seus próprios usuários, clientes, veículos, ordens de serviço, pagamentos e indicadores. Uma oficina não poderá visualizar, alterar ou acessar dados de outra oficina. Esse isolamento lógico será tratado como requisito central de segurança e arquitetura. O MVP não controlará estoque; itens de peça e seus valores serão informados manualmente em cada orçamento.

O sistema apoia o fluxo de atendimento de uma oficina: cadastro da oficina, cadastro de usuários, cadastro do cliente, cadastro do veículo, abertura da ordem de serviço, diagnóstico técnico, elaboração e aprovação de orçamento, execução do reparo, fechamento financeiro e registro do histórico de manutenção.

O problema central que o projeto resolve é a falta de integração entre atendimento, oficina e financeiro em oficinas que ainda utilizam papel, planilhas e mensagens avulsas. Essas falhas geram retrabalho, perda financeira, baixa rastreabilidade e dificuldade de gestão. Como proposta SaaS, o OficinaPro também resolve outro problema: permitir que pequenas oficinas acessem uma solução profissional sem precisar manter infraestrutura própria.

O diferencial do OficinaPro é tratar a ordem de serviço como um processo de negócio completo dentro de uma arquitetura SaaS. Uma aprovação de orçamento altera o estado da ordem; o fechamento financeiro considera os serviços executados, itens de peça informados no orçamento, descontos e pagamentos; e cada atendimento alimenta o histórico do veículo. Tudo isso deve ocorrer com isolamento de dados por oficina.

## Papéis e acesso

Todos utilizam a mesma aplicação web. A área de navegação e as ações disponíveis variam conforme o papel ativo, e o servidor valida a permissão em cada operação.

| Papel | Responsabilidade principal |
|---|---|
| Administrador da plataforma GarageOps | Cadastrar, editar, suspender e excluir oficinas, conhecendo o cadastro delas (nome, identificador, identidade visual e situação); acompanhar saúde global e métricas agregadas. Não consulta, pesquisa, exporta nem altera conteúdo operacional (clientes, veículos, ordens, orçamentos, valores, mensagens e pagamentos) pela interface ou API. |
| Administrador da oficina | Gerenciar usuários e papéis apenas na própria oficina e acompanhar sua operação. |
| Atendente | Cadastrar clientes e veículos locais, abrir ordens e apoiar orçamento e comunicação. |
| Mecânico | Registrar diagnóstico e execução de serviços autorizados após aprovação do orçamento vigente. |
| Financeiro | Registrar pagamentos e acompanhar valores pendentes e fechamento da ordem. |
| Cliente | Acessar seus próprios vínculos, veículos e ordens, conversar e aprovar ou recusar orçamentos. |

O administrador da plataforma não tem uma função de suporte que abra os dados do tenant. Os testes devem provar a negativa de acesso por UI e API. O administrador da oficina também está limitado ao próprio tenant. O cliente tem uma identidade GarageOps global, mas cada oficina preserva seus próprios veículos e atendimentos.

## Fluxo principal e cenário de demonstração

Atendimento cadastra cliente e veículo e abre a ordem; mecânico registra diagnóstico; oficina envia orçamento versionado; cliente autenticado decide e pode conversar e solicitar revisão; a execução só começa após aprovação da versão vigente; financeiro registra pagamentos; histórico permanece consultável conforme o vínculo e o papel.

A demonstração deve usar cinco oficinas fictícias com equipes de tamanhos diferentes, um cliente vinculado a duas oficinas e outro vinculado às cinco. Os cadastros e atendimentos permanecem separados por oficina, mesmo quando a identidade GarageOps é a mesma.

**Fora do MVP:** estoque interno (peças são lançadas manualmente no orçamento), landing page comercial externa e aplicativo nativo para lojas. Pagamento real também fica fora do escopo acadêmico atual.

## Tecnologias Planejadas

- **Backend:** Node.js, TypeScript e NestJS
- **Frontend:** React, TypeScript e Vite
- **Banco de Dados:** PostgreSQL
- **Mensageria:** RabbitMQ
- **Cloud:** AWS
- **Infraestrutura como Código:** Terraform
- **CI/CD:** GitHub Actions
- **Monitoramento:** Prometheus e Grafana
- **Testes:** Jest e Supertest (backend); Vitest e React Testing Library (frontend)
- **Ambiente Local:** Docker e Docker Compose

## Microsserviços

No MVP do 4º semestre, os domínios estão agrupados em três microsserviços NestJS implantáveis de forma independente, cada um com banco PostgreSQL próprio (Documento 07, ADR-007):

| Serviço no MVP | Domínios lógicos | Responsabilidade |
|---|---|---|
| `identity` | Tenant e Auth | Oficinas, autenticação, vínculos e papéis, equipe, convites e console da plataforma |
| `customers` | Customer Vehicle | Clientes, veículos e histórico por tenant; integrações ViaCEP e FIPE |
| `workorders` | Work Order e, provisoriamente, Billing | Ordens, diagnóstico, orçamento versionado, decisão, conversa, execução e pagamentos |

Planejados para o 5º semestre: Billing Service (separado de `workorders`), Notification Service, Audit Service e Reporting Service, integrados por eventos no RabbitMQ.

## Documentos

| # | Documento | Situação |
|---|---|---|
| 01 | [Solicitação do Sistema](01-solicitacao-do-sistema.md) | Entregue; revisado em 25/11/2026 |
| 02 | [Requisitos Funcionais e Não Funcionais](02-requisitos-funcionais-nao-funcionais.md) | Entregue; revisado em 25/11/2026 |
| 03 | [Diagrama de Fluxo de Dados](03-diagrama-fluxo-de-dados.md) | Entregue; revisado em 25/11/2026 |
| 04 | [Modelo Entidade-Relacionamento](04-modelo-entidade-relacionamento.md) | Entregue; revisado em 25/11/2026 |
| 05 | [Arquitetura em Nuvem](05-arquitetura-nuvem.md) | Versão 1.0 para revisão |
| 06 | [C4 Model](06-c4-model.md) | Versão 1.0 para revisão |
| 07 | [Registro de Decisões Arquitetônicas](07-registro-decisoes-arquitetonicas-adr.md) | Versão 1.0 para revisão |
| 08 | [Planejamento de Sprints e Tarefas](08-planejamento-sprints-tarefas.md) | Versão 1.0 para revisão |

## Integrações Externas Previstas

- **ViaCEP:** preenchimento do endereço a partir do CEP, com preenchimento manual se a API falhar.
- **API FIPE:** escolha padronizada de marca, modelo e ano no cadastro do veículo, com preenchimento manual como alternativa.

O pagamento é feito no local e registrado pelo financeiro. Pagamento online por provedor (inclusive em sandbox) fica como evolução futura.

## Decisões Técnicas Centrais

| Decisão | Justificativa |
|---------|---------------|
| SaaS multi-tenant | Permite atender várias oficinas na mesma plataforma, com custo menor e operação centralizada |
| PostgreSQL | Garante consistência transacional, integridade referencial e boa modelagem para dados relacionais |
| Isolamento por `tenant_id` | Impede que dados de uma oficina sejam acessados por outra |
| Microsserviços | Separa domínios como autenticação, ordens, financeiro e notificações |
| RabbitMQ | Permite comunicação assíncrona para eventos como notificações e atualizações financeiras, quando aplicável |
| Terraform | Garante infraestrutura reproduzível e evita dependência de configuração manual |
| Prometheus e Grafana | Permitem observar saúde, erros, latência e comportamento dos serviços |
