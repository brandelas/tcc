# Documento de Solicitação do Sistema

> **Documento histórico — não usar na entrega.** Esta versão (revisada em 16/11/2026) segue o modelo com controle de estoque, reserva de peças e Inventory Service. O grupo retirou o estoque do escopo em 13/11/2026 e, em 22/11/2026, adotou o MVP com os serviços `identity`, `customers` e `workorders`. A versão vigente está em [`entrega/Projetos/OficinaPro/`](../../entrega/Projetos/OficinaPro/README.md) (Documento 01). Mantido como registro do levantamento.

---

**Projeto:** OficinaPro  
**Grupo:** GarageOps  
**Data:** 16/11/2026  
**Versão:** 1.0

---

## 1. Visão Geral do Projeto

O **OficinaPro** é um sistema de gestão operacional destinado a oficinas mecânicas de pequeno e médio porte, desenvolvido com o objetivo de centralizar e organizar os principais processos administrativos e operacionais de uma oficina.

A plataforma pretende acompanhar o fluxo de atendimento desde o cadastro do cliente e de seu veículo até a abertura e conclusão da Ordem de Serviço (OS), incluindo diagnóstico, elaboração e aprovação de orçamento, controle de peças e estoque, execução dos serviços e acompanhamento financeiro.

O sistema será desenvolvido com uma arquitetura baseada em microsserviços, utilizando tecnologias como Node.js, TypeScript, NestJS, React, PostgreSQL e RabbitMQ. A solução será planejada para operar como um Software as a Service (SaaS), permitindo que diferentes oficinas utilizem a plataforma com isolamento de seus respectivos dados.

O público-alvo inicial compreende oficinas mecânicas independentes, centros automotivos e pequenos estabelecimentos do setor de manutenção veicular.

## 2. Problema Identificado

Oficinas mecânicas de pequeno e médio porte podem utilizar ferramentas distintas e processos pouco integrados para controlar suas atividades, como planilhas, documentos físicos, aplicativos de mensagens e registros manuais.

Essa fragmentação dificulta a centralização das informações e o acompanhamento do histórico de atendimento de cada cliente e veículo. Também pode gerar inconsistências entre ordens de serviço, diagnósticos, orçamentos, peças utilizadas e pagamentos.

Entre os principais problemas identificados estão:

- **Informações descentralizadas:** dados de clientes, veículos e serviços podem ficar distribuídos em diferentes ferramentas e documentos.
- **Dificuldade de rastreabilidade:** o acompanhamento das etapas de uma Ordem de Serviço pode depender de registros manuais e consultas a diferentes fontes.
- **Falhas no controle de estoque:** a ausência de integração entre orçamento, reserva de peças e utilização efetiva pode ocasionar divergências nas quantidades disponíveis.
- **Inconsistências financeiras:** a falta de integração entre serviços executados, peças utilizadas e pagamentos pode dificultar a conferência dos valores.
- **Histórico de manutenção limitado:** a consulta aos serviços anteriormente realizados em determinado veículo pode exigir a recuperação manual de registros.

Esses problemas afetam proprietários, atendentes, mecânicos e responsáveis pelo estoque e financeiro, podendo provocar retrabalho, perda de informações, atrasos no atendimento e dificuldades na tomada de decisões.

Não foram utilizados, nesta versão, levantamentos estatísticos ou percentuais de mercado. Os problemas descritos representam as necessidades operacionais consideradas na concepção da solução e poderão ser complementados por entrevistas ou validações com profissionais do setor.

## 3. Justificativa

A proposta do OficinaPro surgiu da necessidade de organizar e integrar os processos essenciais de uma oficina mecânica em uma única plataforma, reduzindo a dependência de controles isolados e proporcionando maior rastreabilidade das operações.

A centralização das informações permite que os diferentes responsáveis acompanhem o andamento das atividades e consultem os registros necessários para executar suas funções.

Um diferencial importante da solução será o tratamento integrado do orçamento e do estoque. A aprovação de um orçamento pelo cliente não deverá liberar automaticamente a execução: o sistema deverá solicitar a reserva das peças necessárias e confirmar a disponibilidade integral antes de liberar operacionalmente o serviço. Esse processo busca evitar reservas parciais, conflitos de estoque e utilização de peças já comprometidas com outros atendimentos.

A escolha de uma arquitetura baseada em microsserviços permitirá separar responsabilidades por domínios de negócio, facilitando a manutenção e a evolução independente dos componentes. A utilização de mensageria possibilitará a comunicação assíncrona entre serviços, enquanto o PostgreSQL será utilizado para persistência dos dados transacionais.

O modelo SaaS também permitirá estruturar a solução para atender diferentes oficinas, mantendo o isolamento dos dados de cada estabelecimento.

O projeto está alinhado aos objetivos do Projeto Experimental, contemplando desenvolvimento de software, integração entre serviços, persistência de dados, infraestrutura em nuvem, automação, testes e monitoramento. Essas capacidades serão desenvolvidas progressivamente, respeitando as entregas previstas para cada etapa acadêmica.

## 4. Valor Agregado

| Benefício | Descrição |
|---|---|
| Centralização das informações | Reunir os dados de clientes, veículos, ordens de serviço, orçamentos, peças e pagamentos em uma plataforma. |
| Organização operacional | Permitir o acompanhamento das etapas da Ordem de Serviço, desde sua abertura até a conclusão. |
| Rastreabilidade | Manter registros das alterações, decisões de aprovação, movimentações de estoque e operações realizadas. |
| Controle de estoque | Diferenciar quantidades físicas, reservadas e disponíveis, reduzindo inconsistências e conflitos de utilização. |
| Segurança operacional | Impedir a liberação da execução antes da aprovação do orçamento e da confirmação da reserva integral das peças necessárias. |
| Acompanhamento financeiro | Registrar valores e pagamentos relacionados às ordens de serviço, mantendo o estado financeiro separado do estado operacional. |
| Histórico de manutenção | Facilitar a consulta aos serviços realizados nos veículos atendidos pela oficina. |
| Organização de responsabilidades | Disponibilizar perfis de acesso e permissões compatíveis com as atividades dos usuários. |
| Evolução tecnológica | Utilizar uma arquitetura preparada para a evolução incremental dos serviços e funcionalidades. |
| Modelo SaaS | Estruturar a plataforma para atender diferentes oficinas com isolamento de dados por estabelecimento. |

## 5. Escopo do Projeto

### 5.1 O que ESTÁ no escopo

O MVP do OficinaPro contemplará as funcionalidades essenciais para validar o fluxo operacional de uma oficina mecânica.

- [ ] Autenticação de usuários e controle de acesso por perfil.
- [ ] Cadastro e gerenciamento de clientes.
- [ ] Cadastro e gerenciamento de veículos vinculados aos clientes.
- [ ] Abertura e acompanhamento de Ordens de Serviço.
- [ ] Registro de diagnóstico e descrição dos serviços necessários.
- [ ] Elaboração de orçamentos com serviços e peças.
- [ ] Registro da decisão do cliente sobre o orçamento, incluindo responsável e data/hora.
- [ ] Solicitação e confirmação de reserva integral das peças necessárias antes da liberação operacional.
- [ ] Controle de estoque, incluindo entradas, reservas, utilização efetiva, liberação de reservas e registro de perdas.
- [ ] Controle das etapas de execução e conclusão da Ordem de Serviço.
- [ ] Registro de valores e pagamentos relacionados às ordens de serviço.
- [ ] Consulta ao histórico de manutenção dos veículos.
- [ ] Isolamento dos dados por oficina, considerando o modelo multi-tenant.
- [ ] Comunicação entre os serviços por APIs e mensageria, conforme as necessidades da arquitetura.
- [ ] Testes automatizados e documentação técnica dos componentes desenvolvidos.

Para a primeira versão, a arquitetura será organizada em quatro serviços principais:

| Serviço | Responsabilidade no MVP |
|---|---|
| Auth Service | Autenticação, usuários, perfis e permissões. |
| Work Order Service | Clientes, veículos, ordens de serviço, diagnósticos, orçamentos e execução. |
| Inventory Service | Cadastro de peças, controle de estoque, reservas e movimentações. |
| Billing Service | Controle financeiro das ordens de serviço e registro de pagamentos. |

A separação dos domínios poderá evoluir conforme a maturidade do projeto e as necessidades identificadas durante o desenvolvimento.

### 5.2 O que NÃO está no escopo

As funcionalidades abaixo não fazem parte do MVP inicial, podendo ser avaliadas em versões futuras:

- Aplicativo mobile nativo para Android ou iOS.
- Sistema avançado de notificações automatizadas por SMS, WhatsApp ou outros canais.
- Serviço independente de relatórios e Business Intelligence (BI).
- Dashboards gerenciais avançados e análises preditivas.
- Integração com múltiplos fornecedores de peças e plataformas comerciais.
- Integração completa com sistemas fiscais e emissão de documentos fiscais eletrônicos.
- Gestão de folha de pagamento e recursos humanos.
- Gestão de filiais com estruturas organizacionais avançadas.
- Automação de compras e reposição de estoque junto a fornecedores.
- Inteligência artificial para diagnóstico automático de falhas veiculares.
- Gestão de assinaturas e cobrança recorrente dos planos SaaS.
- Expansão para outros segmentos além da gestão de oficinas mecânicas.

As integrações externas que forem necessárias ao MVP deverão ser selecionadas e documentadas durante a definição arquitetural. A escolha definitiva das APIs ainda deverá ser formalizada pelo grupo.

## 6. Stakeholders

| Papel | Nome / Perfil | Responsabilidade |
|---|---|---|
| Product Owner / Analista de Requisitos | Heloisa (nome completo a confirmar) | Organização dos requisitos, priorização do backlog e acompanhamento das necessidades do projeto. |
| Desenvolvedor Backend e Integrações | Thiago (nome completo a confirmar) | Desenvolvimento dos serviços de backend e integrações. |
| Desenvolvedor Frontend e UX | Matheus (nome completo a confirmar) | Desenvolvimento da interface e experiência de utilização. |
| DevOps, Banco de Dados e Qualidade | Gabriel (nome completo a confirmar) | Ambiente de execução, banco de dados, infraestrutura e qualidade técnica. |
| Usuários finais | Proprietários, atendentes, mecânicos e responsáveis pelo estoque e financeiro | Utilização do sistema de acordo com as responsabilidades e permissões de cada perfil. |
| Professor(a) Orientador(a) | A confirmar | Acompanhamento acadêmico, orientação e validação das entregas do Projeto Experimental. |

## 7. Premissas e Restrições

### Premissas

- O projeto será desenvolvido como parte do Projeto Experimental do curso de Análise e Desenvolvimento de Sistemas da UniFAAT.
- O grupo utilizará controle de versão e revisão de alterações por meio de Git e Pull Requests.
- O ambiente local será padronizado com Docker e Docker Compose.
- O PostgreSQL será utilizado como banco de dados relacional.
- A comunicação assíncrona entre serviços será realizada por meio de RabbitMQ.
- O sistema será estruturado para atender diferentes oficinas, mantendo o isolamento dos dados por estabelecimento.
- Os integrantes terão acesso aos recursos necessários para desenvolver, testar e documentar a solução.
- As integrações externas necessárias serão selecionadas considerando disponibilidade, documentação e adequação ao escopo.
- As funcionalidades serão implementadas de forma incremental, priorizando o fluxo operacional principal do MVP.

### Restrições

- O desenvolvimento deverá respeitar os prazos e critérios definidos pelo cronograma acadêmico do Projeto Experimental.
- A infraestrutura em nuvem deverá considerar os recursos e limites de utilização disponibilizados no ambiente acadêmico.
- A solução deverá atender aos requisitos técnicos e documentais estabelecidos pelo professor orientador.
- O projeto deverá utilizar tecnologias compatíveis com a arquitetura e os conhecimentos técnicos do grupo.
- A implementação deverá respeitar os princípios de segurança, isolamento de dados e controle de acesso.
- A cobertura mínima de testes exigida para a etapa de desenvolvimento deverá ser de 60%, conforme os critérios do Projeto Experimental.
- As funcionalidades deverão ser priorizadas de acordo com a capacidade de desenvolvimento do grupo e com os objetivos do MVP.

## 8. Integrantes do Grupo

| # | Nome Completo | RA | Papel Principal |
|---|---|---|---|
| 1 | Eloisa dos Santos Brandão | 2325096 | Product Owner e Analista de Requisitos |
| 2 | Thiago Marques Bueno  | 6325304 | Desenvolvedor Backend e Integrações |
| 3 | Matheus Gabriel Correa Braga Viana | 6325053 | Desenvolvedor Frontend e UX |
| 4 | Gabriel Carneiro da Silva | 6325300 | DevOps, Banco de Dados e Qualidade |

---

**Aprovado por:**

___________________________________________________  
Professor(a) Orientador(a)

Data: ___/___/2026

---

> **Observação sobre o documento:** Esta é a versão inicial elaborada a partir da descrição do OficinaPro e das responsabilidades registradas no README do grupo. Os dados de identificação dos integrantes e do orientador deverão ser confirmados antes da entrega formal. As tecnologias, os serviços e as funcionalidades descritos como planejados não representam, por si só, comprovação de implementação.
