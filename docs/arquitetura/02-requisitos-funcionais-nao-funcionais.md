# Documento de Requisitos Funcionais e Não Funcionais

---

**Projeto:** OficinaPro  
**Grupo:** GarageOps  
**Data:** 16/11/2026  
**Versão:** 1.0

---

## 1. Introdução

Este documento apresenta os requisitos funcionais e não funcionais do sistema OficinaPro, estabelecendo as funcionalidades que deverão ser disponibilizadas e os critérios de qualidade, segurança, integridade e infraestrutura que deverão orientar seu desenvolvimento.

O OficinaPro é uma plataforma SaaS de gestão operacional destinada a oficinas mecânicas de pequeno e médio porte. O sistema busca centralizar o gerenciamento de clientes, veículos, ordens de serviço, diagnósticos, orçamentos, estoque, pagamentos e histórico de manutenção.

A solução será estruturada inicialmente em quatro microsserviços principais:

- **Auth Service:** autenticação, usuários, tenants, perfis e permissões.
- **Work Order Service:** clientes, veículos, ordens de serviço, diagnósticos e orçamentos.
- **Inventory Service:** cadastro de peças, estoque, reservas e movimentações.
- **Billing Service:** registros financeiros e pagamentos relacionados às ordens de serviço.

O sistema deverá atender diferentes oficinas, mantendo o isolamento dos dados de cada estabelecimento por meio de uma arquitetura multi-tenant.

Os requisitos deste documento estão relacionados às regras de negócio, histórias de usuário e critérios de aceite mantidos na documentação complementar do projeto.

### 1.1 Legenda de Prioridade

| Prioridade | Significado |
|---|---|
| **Alta** | Essencial para o MVP ou para o atendimento de requisito acadêmico obrigatório. |
| **Média** | Importante para o produto, mas pode ser entregue após as funcionalidades essenciais do MVP. |
| **Baixa** | Funcionalidade desejável, planejada para evolução futura ou condicionada à disponibilidade de tempo. |

### 1.2 Observação sobre os critérios de aceite

Os critérios apresentados nas tabelas resumem as condições necessárias para validar cada requisito. Os cenários detalhados permanecem documentados em `criterios-aceite.md`.

---

## 2. Requisitos Funcionais (RF)

Os requisitos funcionais descrevem as funcionalidades e operações que o OficinaPro deverá disponibilizar aos seus usuários.

### 2.1 Gestão de oficinas e isolamento de dados

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-001 | O sistema deve identificar cada oficina contratante por um identificador único de tenant. | Cada oficina deve possuir seu próprio identificador, associado aos registros operacionais correspondentes. | Alta | Auth Service |
| RF-002 | O sistema deve associar os registros operacionais ao tenant responsável. | Clientes, veículos, OS, orçamentos, peças, movimentações e pagamentos devem respeitar o tenant correspondente. | Alta | Todos os serviços |
| RF-003 | O sistema deve impedir que usuários consultem ou alterem dados de outras oficinas. | Uma requisição de usuário autenticado deve retornar ou modificar somente dados autorizados do seu tenant. | Alta | Todos os serviços |

### 2.2 Clientes

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-004 | O sistema deve permitir cadastrar clientes com nome completo, CPF, telefone, e-mail e endereço. | Os campos obrigatórios devem ser validados e o CPF deve possuir formato e dígitos verificadores válidos. | Alta | Work Order Service |
| RF-005 | O sistema deve impedir o cadastro duplicado de clientes com o mesmo CPF na mesma oficina. | O sistema deve rejeitar CPF já cadastrado no tenant, permitindo o mesmo CPF em oficinas diferentes. | Alta | Work Order Service |
| RF-006 | O sistema deve permitir consultar clientes por nome ou CPF. | A consulta deve apresentar os dados cadastrais e os veículos associados, respeitando o tenant autenticado. | Alta | Work Order Service |
| RF-007 | O sistema deve permitir atualizar os dados cadastrais permitidos de um cliente. | A atualização deve validar os dados e preservar os registros históricos associados. | Alta | Work Order Service |
| RF-008 | O sistema deve permitir inativar clientes que possuam histórico de atendimento. | A inativação não deve excluir fisicamente o cadastro nem apagar ordens de serviço e históricos vinculados. | Alta | Work Order Service |

### 2.3 Veículos

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-009 | O sistema deve permitir cadastrar veículos vinculados a clientes. | O cadastro deve contemplar placa, marca, modelo, ano, cor e quilometragem, vinculando o veículo a um cliente do mesmo tenant. | Alta | Work Order Service |
| RF-010 | O sistema deve impedir o cadastro duplicado de placas na mesma oficina. | A placa deve ser obrigatória e única dentro do tenant correspondente. | Alta | Work Order Service |
| RF-011 | O sistema deve permitir consultar veículos e seus dados cadastrais. | A consulta deve apresentar as informações do veículo e permitir identificar seu proprietário atual e histórico de manutenção. | Alta | Work Order Service |
| RF-012 | O sistema deve preservar o histórico do veículo em caso de transferência de propriedade. | A alteração do proprietário atual não deve apagar os vínculos históricos de atendimentos e proprietários anteriores. | Alta | Work Order Service |

### 2.4 Ordens de Serviço e Diagnóstico

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-013 | O sistema deve permitir abrir uma Ordem de Serviço vinculada a um cliente e a um veículo. | A OS deve possuir identificador único, data de abertura, quilometragem registrada e problema relatado, iniciando com status `ABERTA`. | Alta | Work Order Service |
| RF-014 | O sistema deve permitir consultar uma Ordem de Serviço. | A consulta deve apresentar cliente, veículo, status operacional, diagnóstico, orçamento e histórico de alterações, respeitando o tenant. | Alta | Work Order Service |
| RF-015 | O sistema deve controlar as transições de estado operacional da Ordem de Serviço. | O sistema deve respeitar as transições permitidas entre `ABERTA`, `EM_DIAGNOSTICO`, `AGUARDANDO_APROVACAO`, `AGUARDANDO_RESERVA`, `EM_EXECUCAO`, `CONCLUIDA` e `CANCELADA`. | Alta | Work Order Service |
| RF-016 | O sistema deve permitir registrar o diagnóstico técnico associado à OS. | O diagnóstico deve permanecer vinculado à ordem correspondente e integrar seu histórico. | Alta | Work Order Service |
| RF-017 | O sistema deve permitir consultar os registros de diagnóstico de uma OS. | Os dados técnicos registrados devem permanecer disponíveis para consulta por usuários autorizados. | Alta | Work Order Service |

### 2.5 Orçamentos e aprovação

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-018 | O sistema deve permitir elaborar orçamentos com serviços, peças, quantidades e valores. | O orçamento deve estar vinculado à OS e calcular o total considerando serviços, peças e descontos autorizados. | Alta | Work Order Service |
| RF-019 | O sistema deve permitir registrar a decisão do cliente sobre o orçamento. | A aceitação ou recusa deve ser registrada com responsável pelo registro, data, horário e resultado. | Alta | Work Order Service |
| RF-020 | O sistema deve solicitar a reserva integral das peças necessárias após a aceitação do cliente. | A solicitação deve ser encaminhada ao Inventory Service, e a OS deve aguardar o resultado da reserva. | Alta | Work Order Service / Inventory Service |
| RF-021 | O sistema deve liberar a aprovação operacional do orçamento somente após a confirmação da reserva integral. | A aprovação definitiva deve possuir registro de responsável, data, horário e decisão. A execução deve permanecer bloqueada antes dessa confirmação. | Alta | Work Order Service / Inventory Service |
| RF-022 | O sistema deve permitir registrar a recusa de um orçamento e elaborar uma versão revisada. | A versão anterior e a decisão do cliente devem permanecer no histórico, sem sobrescrita. | Alta | Work Order Service |
| RF-023 | O sistema deve impedir a execução de serviços sem aprovação operacional e reserva confirmada. | Tentativas de iniciar a execução antes das condições exigidas devem ser bloqueadas pelo backend. | Alta | Work Order Service / Inventory Service |

### 2.6 Estoque e movimentações

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-024 | O sistema deve permitir cadastrar peças e consultar seus saldos. | O cadastro deve permitir identificar as peças e consultar estoque físico, reservado e disponível. | Alta | Inventory Service |
| RF-025 | O sistema deve permitir registrar entradas de peças no estoque. | As entradas devem atualizar o estoque físico e manter registro da movimentação. | Alta | Inventory Service |
| RF-026 | O sistema deve calcular o estoque disponível considerando as quantidades físicas e reservadas. | O saldo disponível deve corresponder ao estoque físico menos o estoque reservado. | Alta | Inventory Service |
| RF-027 | O sistema deve realizar reservas integrais e atômicas de peças vinculadas a orçamentos aceitos. | Se qualquer item solicitado estiver indisponível, nenhuma quantidade da solicitação deverá permanecer reservada parcialmente. | Alta | Inventory Service |
| RF-028 | O sistema deve controlar a concorrência nas solicitações de reserva. | Solicitações simultâneas não devem comprometer a mesma quantidade disponível de forma indevida. | Alta | Inventory Service |
| RF-029 | O sistema deve impedir o processamento duplicado de solicitações de reserva e movimentações. | Cada solicitação deve possuir identificador único ou chave de idempotência, impedindo efeitos duplicados em reprocessamentos. | Alta | Inventory Service |
| RF-030 | O sistema deve converter reservas confirmadas em saídas definitivas quando as peças forem efetivamente utilizadas. | A conversão deve atualizar o estoque físico e o reservado de maneira consistente, sem duplicidade. | Alta | Inventory Service |
| RF-031 | O sistema deve permitir liberar reservas de peças não utilizadas. | A liberação deve devolver a quantidade à disponibilidade sem aumentar indevidamente o estoque físico. | Alta | Inventory Service |
| RF-032 | O sistema deve registrar perdas, descartes e estornos de estoque. | Cada movimentação deve possuir registro rastreável, respeitando a condição física da peça e as regras de reversão. | Alta | Inventory Service |
| RF-033 | O sistema deve manter histórico das movimentações de estoque. | Reservas, entradas, consumos, liberações, estornos e perdas devem permanecer consultáveis e associados ao contexto da operação. | Alta | Inventory Service |

### 2.7 Financeiro e histórico de manutenção

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-034 | O sistema deve calcular o valor total da OS. | O cálculo deve considerar serviços, peças e descontos autorizados, apresentando o valor devido. | Alta | Work Order Service |
| RF-035 | O sistema deve permitir registrar pagamentos vinculados a uma OS. | O registro deve armazenar valor, data, forma de pagamento e informações necessárias à rastreabilidade. | Alta | Billing Service |
| RF-036 | O sistema deve controlar a situação financeira da OS independentemente do estado operacional. | O sistema deve distinguir pagamentos pendentes, parciais e quitados, sem exigir quitação para concluir operacionalmente a OS. | Alta | Billing Service |
| RF-037 | O sistema deve permitir consultar o histórico de manutenção de um veículo. | O histórico deve apresentar ordens de serviço, datas, diagnósticos, orçamentos, serviços e peças efetivamente utilizados, respeitando as permissões e o tenant. | Alta | Work Order Service |

### 2.8 Autenticação e permissões

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-038 | O sistema deve autenticar usuários antes de permitir acesso às funcionalidades protegidas. | Usuários devem apresentar credenciais válidas para acessar os recursos autorizados. | Alta | Auth Service |
| RF-039 | O sistema deve controlar permissões de acesso conforme os perfis definidos. | As ações devem ser autorizadas no backend de acordo com as permissões atribuídas ao usuário. | Alta | Auth Service |
| RF-040 | O sistema deve identificar o usuário responsável por operações relevantes. | Decisões de orçamento, alterações de OS e movimentações relevantes devem permitir identificar o responsável pela operação. | Alta | Auth Service / Serviços de domínio |

Os perfis previstos no MVP são:

- Atendente.
- Mecânico.
- Estoque.
- Financeiro.

A matriz detalhada de permissões de cada perfil deverá ser consolidada durante o detalhamento funcional. Não está previsto um perfil operacional genérico denominado Administrador da Oficina.

### 2.9 Integrações externas e funcionalidades futuras

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|---|---|---|---|---|
| RF-041 | O sistema deve consumir pelo menos uma API externa relevante ao domínio de oficinas mecânicas, conforme requisito acadêmico do projeto. | A integração deverá ser documentada, testada e possuir tratamento de erros e indisponibilidade. A API e a funcionalidade específicas ainda deverão ser formalizadas pelo grupo. | Alta | Serviço responsável pela integração — a definir |
| RF-042 | O sistema poderá gerar notificações relacionadas a eventos relevantes da OS. | As notificações deverão ser associadas aos eventos correspondentes e respeitar o tenant. | Baixa | Notification Service — pós-MVP |
| RF-043 | O sistema poderá disponibilizar relatórios e indicadores operacionais. | Os relatórios deverão respeitar o isolamento entre oficinas e utilizar critérios de cálculo documentados. | Baixa | Reporting Service — pós-MVP |

---

## 3. Requisitos Não Funcionais (RNF)

Os requisitos não funcionais definem características de qualidade, segurança, confiabilidade, manutenção e infraestrutura esperadas para o OficinaPro.

### 3.1 Segurança

| ID | Descrição | Métrica | Meta |
|---|---|---|---|
| RNF-001 | O sistema deve armazenar senhas utilizando mecanismo seguro de hash apropriado para senhas. | Verificação do mecanismo de armazenamento | Senhas não devem ser armazenadas em texto puro. |
| RNF-002 | O sistema deve aplicar controle de acesso baseado em permissões (RBAC). | Validação de autorização no backend | Todas as operações protegidas devem verificar as permissões do usuário. |
| RNF-003 | O sistema deve isolar os dados entre oficinas. | Testes de isolamento multi-tenant | Usuários não devem acessar dados de outros tenants sem autorização. |
| RNF-004 | O sistema deve validar entradas e impedir operações não autorizadas. | Testes de validação e autorização | Dados inválidos e operações não autorizadas devem ser rejeitados. |
| RNF-005 | O sistema não deve expor credenciais, tokens ou informações sensíveis em logs. | Inspeção de logs e testes de segurança | Nenhuma credencial ou segredo deve ser registrado indevidamente. |
| RNF-006 | O sistema deve utilizar mecanismos seguros para configuração e gerenciamento de segredos. | Inspeção de configuração e repositório | Segredos não devem ser versionados diretamente no código-fonte. |

### 3.2 Integridade e confiabilidade

| ID | Descrição | Métrica | Meta |
|---|---|---|---|
| RNF-007 | Cada microsserviço deve manter responsabilidade sobre seus próprios dados. | Verificação da arquitetura e persistência | Não deve haver acesso direto ao banco de dados de outro microsserviço. |
| RNF-008 | As operações transacionais locais devem preservar a consistência dos dados. | Testes de integridade e transações | Falhas não devem deixar operações locais críticas parcialmente persistidas. |
| RNF-009 | As operações de reserva de estoque devem possuir controle de concorrência. | Testes concorrentes de reserva | Não deve ocorrer reserva superior à quantidade disponível. |
| RNF-010 | As solicitações e os eventos devem possuir mecanismos de idempotência. | Testes de reprocessamento | Repetições não devem produzir efeitos de negócio duplicados. |
| RNF-011 | A publicação de eventos relacionados a operações persistidas deve ser confiável. | Verificação do padrão de integração | Utilização de Transactional Outbox nos fluxos aplicáveis. |
| RNF-012 | O consumo de eventos deve impedir o processamento duplicado. | Testes de consumidores e mensagens repetidas | Utilização de Inbox ou mecanismo equivalente de deduplicação. |
| RNF-013 | O sistema deve tratar falhas de comunicação e mensagens não processadas. | Testes de falhas, retentativas e recuperação | Operações devem possuir tratamento de erros e mecanismos de recuperação, incluindo DLQ quando aplicável. |
| RNF-014 | As operações entre microsserviços devem preservar rastreabilidade e contexto. | Verificação de identificadores de correlação e tenant | Eventos e operações relevantes devem manter identificadores de correlação e `tenant_id`. |

### 3.3 Manutenibilidade e qualidade

| ID | Descrição | Métrica | Meta |
|---|---|---|---|
| RNF-015 | O projeto deve possuir testes automatizados para as funcionalidades críticas. | Execução dos testes automatizados | Fluxos críticos devem possuir testes unitários e de integração adequados. |
| RNF-016 | O projeto deve atingir a cobertura mínima de testes exigida academicamente. | Cobertura global sobre o código-fonte dos microsserviços | Mínimo de 60% de cobertura global, considerando o total do código-fonte dos microsserviços. |
| RNF-017 | O projeto deve utilizar versionamento de banco de dados por migrations. | Verificação dos arquivos e execução das migrations | Alterações estruturais do banco devem ser versionadas e reproduzíveis. |
| RNF-018 | O código deve passar por revisão antes de ser incorporado à branch principal. | Pull Requests revisados | Alterações devem ser submetidas ao fluxo de revisão definido pelo grupo. |
| RNF-019 | As APIs e os contratos entre serviços devem possuir documentação técnica suficiente para integração e manutenção. | Documentação dos contratos e endpoints | Os contratos utilizados pelos serviços devem estar documentados e atualizados. |

### 3.4 Infraestrutura e implantação

| ID | Descrição | Métrica | Meta |
|---|---|---|---|
| RNF-020 | O ambiente local deve ser padronizado por contêineres. | Configuração Docker e Docker Compose | Os serviços e dependências locais devem possuir configuração reproduzível. |
| RNF-021 | O projeto deve possuir pipeline de integração contínua e entrega/implantação contínua. | Pipeline no GitHub Actions | O pipeline deverá executar as verificações e etapas de automação definidas para o projeto. |
| RNF-022 | A infraestrutura deverá ser definida como código. | Recursos gerenciados por Terraform | Os recursos de infraestrutura previstos para implantação deverão ser declarados em Terraform. |
| RNF-023 | A solução deverá ser implantada em ambiente de nuvem conforme os requisitos acadêmicos. | Evidência de implantação | Os serviços previstos para a entrega deverão estar implantados e acessíveis no ambiente definido pelo grupo. |
| RNF-024 | O sistema deverá disponibilizar mecanismos de observabilidade. | Logs, métricas e dashboards | A solução deverá contemplar Prometheus e Grafana para monitoramento dos serviços. |

### 3.5 Desempenho, disponibilidade e escalabilidade

O backlog atual não estabelece metas quantitativas de latência, throughput, disponibilidade ou escalabilidade automática. Portanto, esses valores não serão presumidos nesta versão.

| ID | Descrição | Métrica | Meta |
|---|---|---|---|
| RNF-025 | O sistema deverá permitir acompanhar o desempenho e o comportamento operacional dos serviços. | Tempo de resposta, quantidade de requisições e taxa de erros | Métricas deverão ser disponibilizadas para observação e análise. |
| RNF-026 | A arquitetura deverá permitir a evolução independente dos serviços conforme suas responsabilidades. | Separação de domínios e dependências | Cada microsserviço deverá manter responsabilidade definida e comunicação por contratos. |
| RNF-027 | A indisponibilidade de dependências externas não deverá corromper os dados internos já persistidos. | Testes de falha de integração | Erros externos deverão ser tratados sem comprometer indevidamente a integridade das operações internas. |

As metas quantitativas de desempenho e disponibilidade poderão ser definidas futuramente, após avaliação do ambiente de implantação e validação do grupo.

---

## 4. Rastreabilidade

A matriz relaciona os requisitos funcionais aos requisitos não funcionais que influenciam sua implementação e validação.

| RF | RNFs Relacionados |
|---|---|
| RF-001 | RNF-002, RNF-003, RNF-014 |
| RF-002 | RNF-003, RNF-007, RNF-008, RNF-014 |
| RF-003 | RNF-002, RNF-003, RNF-004 |
| RF-004 | RNF-003, RNF-004, RNF-008 |
| RF-005 | RNF-003, RNF-004, RNF-008 |
| RF-006 | RNF-002, RNF-003, RNF-004 |
| RF-007 | RNF-003, RNF-004, RNF-008 |
| RF-008 | RNF-003, RNF-008 |
| RF-009 | RNF-003, RNF-004, RNF-008 |
| RF-010 | RNF-003, RNF-004, RNF-008 |
| RF-011 | RNF-002, RNF-003, RNF-004 |
| RF-012 | RNF-008, RNF-014 |
| RF-013 | RNF-002, RNF-003, RNF-008 |
| RF-014 | RNF-002, RNF-003, RNF-008, RNF-014 |
| RF-015 | RNF-008, RNF-010, RNF-014 |
| RF-016 | RNF-002, RNF-003, RNF-008 |
| RF-017 | RNF-002, RNF-003, RNF-008 |
| RF-018 | RNF-004, RNF-008 |
| RF-019 | RNF-002, RNF-008, RNF-014 |
| RF-020 | RNF-007, RNF-008, RNF-010, RNF-011, RNF-014 |
| RF-021 | RNF-007, RNF-008, RNF-009, RNF-010, RNF-011, RNF-012, RNF-014 |
| RF-022 | RNF-008, RNF-014 |
| RF-023 | RNF-002, RNF-008, RNF-009 |
| RF-024 | RNF-003, RNF-004, RNF-008 |
| RF-025 | RNF-007, RNF-008, RNF-014 |
| RF-026 | RNF-008, RNF-009 |
| RF-027 | RNF-008, RNF-009, RNF-010, RNF-011, RNF-012 |
| RF-028 | RNF-008, RNF-009, RNF-015 |
| RF-029 | RNF-008, RNF-010, RNF-012 |
| RF-030 | RNF-008, RNF-009, RNF-010, RNF-012 |
| RF-031 | RNF-008, RNF-010, RNF-014 |
| RF-032 | RNF-008, RNF-010, RNF-014 |
| RF-033 | RNF-008, RNF-014 |
| RF-034 | RNF-004, RNF-008 |
| RF-035 | RNF-002, RNF-003, RNF-008, RNF-014 |
| RF-036 | RNF-008, RNF-014 |
| RF-037 | RNF-002, RNF-003, RNF-008, RNF-014 |
| RF-038 | RNF-001, RNF-002, RNF-005, RNF-006 |
| RF-039 | RNF-002, RNF-003, RNF-004 |
| RF-040 | RNF-002, RNF-008, RNF-014 |
| RF-041 | RNF-004, RNF-013, RNF-027 |
| RF-042 | RNF-003, RNF-010, RNF-014 |
| RF-043 | RNF-002, RNF-003, RNF-008 |

---

## 5. Glossário

| Termo | Definição |
|---|---|
| SaaS | Modelo de disponibilização de software como serviço, permitindo acesso à aplicação sem instalação individual completa pelo contratante. |
| Tenant | Representação de uma oficina dentro da plataforma, utilizada para identificar e isolar seus dados. |
| Multi-tenancy | Arquitetura em que diferentes oficinas utilizam a mesma plataforma, com isolamento lógico dos respectivos dados. |
| Ordem de Serviço (OS) | Registro que organiza o atendimento, diagnóstico, orçamento, execução e conclusão de um serviço realizado pela oficina. |
| RF | Requisito Funcional: descreve uma funcionalidade que o sistema deve oferecer. |
| RNF | Requisito Não Funcional: descreve características de qualidade, segurança, desempenho ou infraestrutura. |
| RBAC | Controle de acesso baseado em papéis ou perfis de usuário. |
| Microsserviço | Serviço independente responsável por um domínio ou conjunto delimitado de funcionalidades. |
| Database-per-Service | Estratégia em que cada microsserviço é responsável por seus próprios dados e não acessa diretamente o banco de outro serviço. |
| RabbitMQ | Sistema de mensageria utilizado para comunicação assíncrona entre serviços. |
| Idempotência | Propriedade que impede que a repetição de uma operação produza efeitos duplicados. |
| Transactional Outbox | Padrão que registra eventos na mesma transação dos dados de negócio, permitindo publicação confiável posterior. |
| Inbox | Mecanismo utilizado para registrar mensagens recebidas e evitar processamento duplicado. |
| DLQ | Dead Letter Queue: fila utilizada para mensagens que não puderam ser processadas normalmente. |
| Estoque físico | Quantidade de peças efetivamente existente na oficina. |
| Estoque reservado | Quantidade comprometida com ordens de serviço, mas ainda não utilizada. |
| Estoque disponível | Quantidade que pode ser comprometida em novas operações, calculada pela diferença entre estoque físico e reservado. |
| Reserva integral | Operação que confirma todas as quantidades solicitadas ou rejeita a solicitação inteira, sem reserva parcial. |
| Consumo definitivo | Saída de estoque correspondente à utilização efetiva da peça. |
| Cobertura de testes | Percentual do código-fonte exercitado pelos testes automatizados, conforme a métrica adotada pelo projeto. |
| Terraform | Ferramenta de infraestrutura como código utilizada para declarar e provisionar recursos de infraestrutura. |
| Prometheus | Ferramenta de coleta e armazenamento de métricas. |
| Grafana | Plataforma utilizada para visualização de métricas e criação de dashboards. |

---

## 6. Histórico de Revisões

| Versão | Data | Autor | Descrição da Alteração |
|---|---|---|---|
| 1.0 | 16/11/2026 | Grupo GarageOps | Criação da versão inicial dos requisitos funcionais, não funcionais e matriz de rastreabilidade com base na documentação de trabalho do OficinaPro. |

---

**Aprovado por:**

___________________________________________________

Professor(a) Orientador(a)

Data: ___/___/2026

---

> **Observação:** Os requisitos deste documento representam o comportamento planejado para o OficinaPro. Sua implementação deverá ser comprovada por código, testes e evidências de execução. As metas quantitativas de desempenho e disponibilidade não foram estabelecidas nesta versão. A escolha da API externa e o detalhamento da matriz de permissões permanecem pendentes de formalização.
