# Registro de Decisões Arquitetônicas (ADR) — OficinaPro

---

**Projeto:** OficinaPro  
**Grupo:** GarageOps  
**Data:** 16/11/2026  
**Versão:** 1.0

---

## 1. Objetivo

Este documento registra as principais decisões arquitetônicas relacionadas ao desenvolvimento do OficinaPro, plataforma SaaS destinada à gestão operacional e financeira de pequenas e médias oficinas mecânicas.

Cada ADR (Architecture Decision Record) apresenta o contexto da decisão, a solução proposta, as alternativas consideradas e as consequências previstas.

O objetivo é manter um histórico técnico que facilite a compreensão da arquitetura, a manutenção do sistema e a avaliação das escolhas realizadas durante o desenvolvimento.

As decisões registradas refletem a direção arquitetural consolidada nos documentos do projeto. Quando ainda não houver aprovação formal do grupo ou do professor, o status será identificado como **Proposta**.

## 2. Formato de cada ADR

Cada registro apresenta:

- **Contexto:** problema, necessidade ou restrição que motivou a decisão.
- **Decisão:** solução arquitetônica escolhida ou proposta.
- **Alternativas consideradas:** opções avaliadas e seus respectivos pontos positivos e negativos.
- **Consequências:** benefícios, limitações e riscos associados à decisão.

Os status utilizados são:

| Status | Significado |
|---|---|
| Proposta | Solução recomendada, ainda pendente de ratificação formal. |
| Aceita | Decisão formalmente aprovada pelo grupo. |
| Substituída | Decisão posteriormente substituída por outra ADR. |
| Depreciada | Decisão que deixou de ser recomendada ou necessária. |

---

## 3. Registro de Decisões

### ADR-001: Linguagem e Framework do Backend

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

O OficinaPro será desenvolvido utilizando uma arquitetura baseada em microsserviços. Essa abordagem exige uma tecnologia que permita organizar os serviços de maneira modular, manter contratos de API e facilitar a comunicação entre os componentes.

Também é importante considerar a produtividade da equipe, a facilidade de manutenção, a integração com ferramentas de testes e a execução da aplicação em contêineres.

A padronização tecnológica entre os microsserviços deverá reduzir a complexidade de desenvolvimento e manutenção.

#### Decisão

Propõe-se utilizar **Node.js, TypeScript e NestJS** no desenvolvimento dos quatro microsserviços do MVP:

- Auth Service.
- Work Order Service.
- Inventory Service.
- Billing Service.

O NestJS será utilizado para estruturar os módulos, controladores, serviços, validações e mecanismos de injeção de dependência.

TypeScript será utilizado para fornecer tipagem estática e padronização do código.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| Node.js + NestJS | Arquitetura modular, integração com TypeScript, ecossistema amplo e suporte a APIs REST. | Consumo de memória e desempenho inferior em determinadas operações intensivas de CPU. |
| Java + Spring Boot | Ecossistema maduro, recursos empresariais e ampla utilização no mercado. | Maior complexidade inicial e maior quantidade de configurações. |
| Go | Boa eficiência, baixo consumo de recursos e facilidade de distribuição de binários. | Curva de aprendizado e menor familiaridade potencial da equipe. |
| Python + FastAPI | Desenvolvimento rápido e estrutura simples para APIs. | Exige decisões adicionais para padronização arquitetural entre serviços. |

#### Consequências

- **Positivas:** padronização do backend, organização modular, integração com TypeScript e facilidade de criação de APIs e testes automatizados.
- **Negativas:** necessidade de controlar o consumo de memória e evitar operações intensivas de CPU no fluxo principal das requisições.
- **Riscos mitigados:** utilização de processamento assíncrono quando necessário, testes automatizados, validações de entrada e monitoramento dos recursos dos contêineres.

---

### ADR-002: Escolha do Banco de Dados

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

O OficinaPro precisa armazenar informações relacionadas a clientes, veículos, ordens de serviço, diagnósticos, peças, reservas, orçamentos e pagamentos.

Essas informações possuem relacionamentos e regras de integridade que exigem consistência transacional.

A arquitetura baseada em microsserviços também exige que cada serviço tenha responsabilidade exclusiva sobre seus próprios dados.

#### Decisão

Propõe-se utilizar **PostgreSQL** como banco de dados relacional dos quatro microsserviços, seguindo o padrão Database-per-Service.

A organização lógica será:

| Microsserviço | Banco de dados |
|---|---|
| Auth Service | `auth_db` |
| Work Order Service | `work_order_db` |
| Inventory Service | `inventory_db` |
| Billing Service | `billing_db` |

Para reduzir custos e complexidade no MVP, será avaliada a utilização de uma única instância PostgreSQL com bancos logicamente separados, credenciais específicas e permissões independentes.

Cada serviço será responsável por suas próprias tabelas, transações e migrações.

Não serão utilizadas chaves estrangeiras entre bancos pertencentes a microsserviços diferentes. As referências externas serão verificadas por APIs ou eventos.

Não será introduzido Redis como cache obrigatório no MVP, pois ainda não existe uma necessidade comprovada que justifique sua adoção.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| PostgreSQL | Suporte a transações ACID, relacionamentos, integridade referencial e consultas relacionais. | Exige planejamento para escalabilidade e administração dos bancos. |
| MongoDB | Flexibilidade de estrutura e armazenamento de documentos. | Menor adequação ao conjunto de relacionamentos e transações relacionais do projeto. |
| Amazon DynamoDB | Serviço gerenciado e escalabilidade automática. | Exige modelagem específica para padrões de consulta e pode aumentar a complexidade das relações entre dados. |
| Banco compartilhado entre todos os serviços | Menor complexidade inicial de infraestrutura. | Maior acoplamento entre microsserviços e risco de acesso indevido aos dados de outros domínios. |

#### Consequências

- **Positivas:** consistência transacional, modelagem relacional adequada e propriedade dos dados por microsserviço.
- **Negativas:** necessidade de sincronizar informações entre serviços por APIs ou eventos, sem utilizar transações distribuídas.
- **Riscos mitigados:** isolamento de credenciais, controle de permissões, migrações independentes, backups e monitoramento do banco de dados.

A utilização de uma instância compartilhada no MVP deverá ser reconhecida como uma simplificação de infraestrutura, não como compartilhamento de propriedade dos dados.

---

### ADR-003: Estratégia de Comunicação entre Microsserviços

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

Os microsserviços do OficinaPro precisarão trocar informações para executar processos que envolvem mais de um domínio.

Um exemplo é a aprovação de um orçamento que depende da reserva integral das peças no Inventory Service.

A comunicação exclusivamente síncrona criaria dependência temporal entre os serviços e poderia provocar falhas em cadeia quando um componente estivesse indisponível.

Por outro lado, operações que exigem resposta imediata ao usuário podem utilizar APIs síncronas.

#### Decisão

Propõe-se utilizar uma estratégia híbrida:

- **REST/JSON:** comunicação síncrona para operações que precisam de resposta imediata.
- **RabbitMQ/AMQP:** comunicação assíncrona para eventos e processamento entre microsserviços.

O RabbitMQ será utilizado para publicar e consumir eventos relacionados aos processos do OficinaPro.

Os eventos deverão possuir identificadores e informações de contexto, incluindo `tenant_id`, identificador da operação e dados necessários para rastreabilidade.

A comunicação assíncrona deverá utilizar:

- Transactional Outbox para registrar eventos junto às alterações locais.
- Inbox ou mecanismo equivalente para deduplicação de mensagens recebidas.
- Chaves de idempotência para evitar processamento duplicado.
- Tentativas controladas de reprocessamento.
- Dead Letter Queue (DLQ) para mensagens que não puderem ser processadas.
- Identificadores de correlação para rastrear operações distribuídas.

Não serão utilizadas transações distribuídas entre os bancos dos microsserviços.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| REST síncrono exclusivo | Simples de implementar e depurar. | Acoplamento temporal e maior risco de falhas em cadeia. |
| gRPC | Comunicação eficiente e contratos tipados. | Maior complexidade de configuração e depuração. |
| RabbitMQ | Comunicação assíncrona, filas, roteamento e desacoplamento entre serviços. | Exige gerenciamento de filas, mensagens duplicadas e falhas de processamento. |
| Amazon SQS | Serviço gerenciado e integração com AWS. | Não mantém o mesmo modelo de exchanges e roteamento do RabbitMQ. |
| Apache Kafka | Alta capacidade de processamento de eventos e retenção de mensagens. | Complexidade operacional superior à necessidade inicial do MVP. |

#### Consequências

- **Positivas:** redução do acoplamento temporal, maior flexibilidade na integração e possibilidade de reprocessamento de eventos.
- **Negativas:** maior complexidade na implementação, necessidade de lidar com consistência eventual e processamento duplicado.
- **Riscos mitigados:** Transactional Outbox, Inbox, idempotência, DLQ, monitoramento das filas e rastreamento por identificadores de correlação.

---

### ADR-004: Provedor de Nuvem e Serviços

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

O OficinaPro precisa ser executado em uma infraestrutura que permita disponibilizar a aplicação pela internet, hospedar os microsserviços, armazenar os dados e monitorar os recursos utilizados.

A escolha do provedor deve considerar a disponibilidade de serviços gerenciados, a integração com infraestrutura como código, o acesso acadêmico e os custos.

A utilização da AWS também está alinhada ao contexto de estudos e atividades de infraestrutura em nuvem do projeto.

#### Decisão

Propõe-se utilizar a **Amazon Web Services (AWS)** como provedora de nuvem.

Os principais serviços planejados são:

| Necessidade | Serviço AWS proposto |
|---|---|
| Rede privada | Amazon VPC |
| Distribuição de tráfego | Application Load Balancer |
| Execução dos contêineres | Amazon ECS com AWS Fargate |
| Armazenamento dos bancos | Amazon RDS for PostgreSQL |
| Mensageria | Amazon MQ com RabbitMQ |
| Imagens de contêineres | Amazon ECR |
| Hospedagem do frontend | Amazon S3 |
| Distribuição de conteúdo | Amazon CloudFront |
| Gerenciamento de segredos | AWS Secrets Manager |
| Logs e métricas | Amazon CloudWatch |
| Estado remoto do Terraform | Amazon S3 |

A seleção definitiva dos serviços e seus dimensionamentos dependerá da validação dos custos e da disponibilidade no ambiente acadêmico.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| AWS | Ecossistema amplo, serviços gerenciados, integração com Terraform e ambiente acadêmico disponível. | Curva de aprendizado e custos que podem aumentar conforme a utilização. |
| Google Cloud Platform | Serviços gerenciados e integração com ferramentas de desenvolvimento. | Necessidade de adaptação ao ambiente e aos recursos acadêmicos disponíveis. |
| Microsoft Azure | Integração com tecnologias Microsoft e serviços corporativos. | Necessidade de adaptação às ferramentas e configurações do provedor. |
| Infraestrutura própria | Controle direto sobre os recursos e configurações. | Exige administração de servidores, disponibilidade, segurança e manutenção. |

#### Consequências

- **Positivas:** utilização de serviços gerenciados, integração com Terraform e possibilidade de evolução da infraestrutura.
- **Negativas:** dependência de serviços específicos da AWS e necessidade de controle dos custos.
- **Riscos mitigados:** dimensionamento gradual, orçamento monitorado, uso de tags, separação de ambientes e revisão periódica dos recursos.

A utilização de recursos gerenciados não significa que a infraestrutura já esteja implantada. A implantação e a validação serão etapas posteriores.

---

### ADR-005: Estratégia de Containerização e Orquestração

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

Os microsserviços deverão funcionar de maneira padronizada nos ambientes de desenvolvimento, testes e produção.

A containerização permite empacotar cada serviço com suas dependências e configurações de execução, reduzindo diferenças entre ambientes.

Também será necessário escolher uma estratégia de execução dos contêineres em nuvem.

#### Decisão

Propõe-se utilizar **Docker** para containerização dos quatro microsserviços e do ambiente local.

O Docker Compose será utilizado para facilitar a execução conjunta dos serviços e de suas dependências durante o desenvolvimento.

Para a implantação em nuvem, propõe-se utilizar **Amazon ECS com AWS Fargate**, evitando a necessidade de administrar diretamente servidores EC2 ou um cluster Kubernetes.

As imagens dos serviços serão armazenadas no Amazon ECR.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| ECS Fargate | Execução gerenciada de contêineres sem administração direta de servidores. | Custos relacionados à quantidade de CPU e memória provisionadas. |
| Amazon EKS | Kubernetes gerenciado e ampla flexibilidade de orquestração. | Complexidade operacional e necessidade de conhecimentos adicionais. |
| EC2 com Docker | Maior controle sobre o ambiente de execução. | Necessidade de administrar servidores, atualizações e capacidade. |
| Docker Compose em produção | Simplicidade de configuração. | Menor adequação à operação distribuída e à escalabilidade do ambiente. |

#### Consequências

- **Positivas:** padronização da execução, facilidade de desenvolvimento local e simplificação da operação em nuvem.
- **Negativas:** dependência do modelo de execução do ECS e custos de recursos provisionados.
- **Riscos mitigados:** imagens versionadas, health checks, monitoramento, revisão de recursos e testes de execução dos contêineres.

---

### ADR-006: Estratégia de Multi-tenancy e Isolamento de Dados

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

O OficinaPro será uma plataforma SaaS utilizada por diferentes oficinas mecânicas.

Uma mesma instalação da aplicação deverá atender múltiplas oficinas sem permitir que usuários de uma oficina consultem ou alterem informações pertencentes a outra.

A arquitetura precisa oferecer isolamento lógico dos dados sem exigir, inicialmente, uma infraestrutura independente para cada oficina.

#### Decisão

Propõe-se utilizar uma estratégia de **multi-tenancy com isolamento lógico por `tenant_id`**.

Cada oficina será representada como um tenant.

Os registros pertencentes a uma oficina deverão conter o identificador do tenant, e as operações deverão respeitar o contexto autenticado.

As regras principais serão:

- O `tenant_id` deverá ser obtido e validado pelo backend a partir do contexto autenticado.
- O identificador enviado pelo frontend não deverá ser considerado suficiente para autorizar acesso.
- Consultas, alterações, eventos e relatórios deverão respeitar o tenant correspondente.
- Eventos de integração deverão transportar o identificador do tenant.
- CPF de clientes e placas de veículos deverão possuir unicidade dentro de cada tenant, não globalmente.
- Cada microsserviço deverá validar o contexto de tenant em suas próprias operações.
- A utilização de Row-Level Security (RLS) poderá ser avaliada como proteção adicional, desde que o contexto da conexão seja aplicado com segurança.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| Banco compartilhado com `tenant_id` | Menor custo inicial e infraestrutura simplificada. | Exige disciplina rigorosa na aplicação do isolamento em todas as consultas. |
| Banco separado por oficina | Maior isolamento de dados. | Maior custo e complexidade de provisionamento e manutenção. |
| Instância separada por oficina | Isolamento físico mais forte. | Custo elevado e complexidade operacional incompatível com a necessidade inicial do MVP. |

#### Consequências

- **Positivas:** permite atender múltiplas oficinas com uma infraestrutura compartilhada e facilita a expansão do SaaS.
- **Negativas:** exige validação consistente do tenant em todos os serviços e operações.
- **Riscos mitigados:** autorização no backend, testes automatizados de isolamento, controle de acesso, revisão de consultas e auditoria de operações relevantes.

---

### ADR-007: Reserva de Estoque e Aprovação de Orçamento

**Data:** 16/11/2026  
**Status:** Proposta — fluxo de negócio considerado validado para documentação.  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

O fluxo de orçamento e estoque do OficinaPro envolve diferentes microsserviços.

Quando o cliente aceita um orçamento que contém peças, o sistema precisa verificar se todas as quantidades solicitadas estão disponíveis antes de permitir a aprovação operacional e o início da execução.

Uma aprovação sem reserva prévia poderia permitir que duas ordens de serviço utilizassem simultaneamente a mesma quantidade disponível de peças.

Também seria incorreto tratar a reserva como uma baixa definitiva, pois as peças ainda podem não ter sido utilizadas.

#### Decisão

Propõe-se adotar uma estratégia de **reserva integral de estoque antes da aprovação definitiva do orçamento**.

O processo deverá funcionar da seguinte maneira:

1. O cliente aceita o orçamento.
2. O Billing Service registra a decisão do cliente.
3. O Billing Service solicita a reserva das peças ao Inventory Service.
4. O Inventory Service verifica a disponibilidade de todas as peças.
5. A reserva é realizada integralmente ou recusada.
6. Caso a reserva seja confirmada, o Billing Service efetiva a aprovação definitiva do orçamento.
7. O Work Order Service recebe o evento de aprovação e libera a execução operacional.
8. Quando uma peça for efetivamente utilizada, o Inventory Service converte a quantidade reservada em saída definitiva.
9. Peças reservadas que não forem utilizadas poderão ter a reserva liberada.
10. Peças perdidas ou inutilizadas deverão ser registradas como movimentações específicas.

A reserva deverá ocorrer integralmente no banco de dados do Inventory Service, utilizando mecanismos de controle de concorrência.

As operações deverão utilizar chaves de idempotência e mecanismos de publicação confiável de eventos.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| Aprovação sem reserva prévia | Fluxo inicial mais simples. | Risco de aprovar serviços sem disponibilidade real de peças. |
| Reserva parcial | Permite iniciar parte do processo. | Pode gerar execução incompleta e inconsistência entre orçamento e disponibilidade. |
| Reserva integral antes da aprovação definitiva | Impede liberação da execução sem disponibilidade de todas as peças. | Exige integração assíncrona e controle de concorrência. |
| Baixa definitiva no momento da reserva | Simplifica a contabilização de quantidade disponível. | Confunde reserva com consumo e exige correções quando peças não forem utilizadas. |

#### Consequências

- **Positivas:** maior consistência do estoque, prevenção de reservas acima da disponibilidade e bloqueio da execução quando as peças não puderem ser reservadas integralmente.
- **Negativas:** aumento da complexidade do fluxo entre Billing, Inventory e Work Order, além da necessidade de tratar resultados assíncronos.
- **Riscos mitigados:** transações locais no Inventory Service, controle de concorrência, idempotência, Outbox/Inbox, DLQ e registro de movimentações.

A decisão também estabelece a distinção entre:

- Quantidade física: peças efetivamente existentes.
- Quantidade reservada: peças comprometidas com reservas confirmadas.
- Quantidade disponível: quantidade física menos quantidade reservada.

A reserva não reduz imediatamente a quantidade física. A saída definitiva ocorrerá quando houver utilização efetiva da peça.

---

### ADR-008: Confiabilidade e Consistência dos Eventos

**Data:** 16/11/2026  
**Status:** Proposta  
**Autores:** Grupo GarageOps — responsáveis pela decisão a confirmar.

#### Contexto

A comunicação assíncrona entre microsserviços apresenta riscos de falha, como indisponibilidade temporária do broker, mensagens duplicadas e interrupção de um serviço durante o processamento.

O OficinaPro possui operações importantes que não podem depender exclusivamente de uma publicação direta de mensagem após uma transação no banco de dados.

Por exemplo, uma reserva de estoque precisa registrar seu resultado de maneira consistente, mesmo que o RabbitMQ esteja temporariamente indisponível.

#### Decisão

Propõe-se utilizar o padrão **Transactional Outbox** para registrar eventos na mesma transação local que modifica os dados do microsserviço responsável.

O serviço publicador deverá possuir um mecanismo responsável por consultar eventos pendentes e encaminhá-los ao RabbitMQ.

Os serviços consumidores deverão utilizar Inbox ou mecanismo equivalente para identificar mensagens já processadas.

As operações deverão considerar:

- Identificador único do evento.
- Chave de idempotência.
- Identificador do tenant.
- Identificador de correlação.
- Controle de tentativas.
- Tratamento de falhas permanentes e temporárias.
- DLQ para mensagens que excederem as tentativas previstas.
- Monitoramento de mensagens pendentes e falhas de processamento.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|---|---|---|
| Publicação direta após a transação | Implementação inicial simples. | Pode perder eventos se o processo falhar entre o commit e a publicação. |
| Transactional Outbox | Registra os dados e o evento na mesma transação local. | Exige mecanismo de publicação e limpeza dos eventos processados. |
| Event Sourcing | Mantém histórico de eventos como fonte principal de estado. | Complexidade elevada para o escopo inicial do projeto. |

#### Consequências

- **Positivas:** maior confiabilidade na publicação de eventos e possibilidade de recuperação após falhas temporárias.
- **Negativas:** necessidade de implementar mecanismos adicionais de persistência, publicação, deduplicação e monitoramento.
- **Riscos mitigados:** processamento idempotente, retentativas controladas, DLQ, rastreabilidade e procedimentos de recuperação.

A utilização do Outbox não elimina a possibilidade de mensagens duplicadas. Por isso, a idempotência dos consumidores continuará obrigatória.

---

## 4. Índice de ADRs

| ID | Título | Status | Data |
|---|---|---|---|
| ADR-001 | Linguagem e Framework do Backend | Proposta | 16/11/2026 |
| ADR-002 | Escolha do Banco de Dados | Proposta | 16/11/2026 |
| ADR-003 | Estratégia de Comunicação entre Microsserviços | Proposta | 16/11/2026 |
| ADR-004 | Provedor de Nuvem e Serviços | Proposta | 16/11/2026 |
| ADR-005 | Estratégia de Containerização e Orquestração | Proposta | 16/11/2026 |
| ADR-006 | Estratégia de Multi-tenancy e Isolamento de Dados | Proposta | 16/11/2026 |
| ADR-007 | Reserva de Estoque e Aprovação de Orçamento | Proposta — fluxo validado para documentação | 16/11/2026 |
| ADR-008 | Confiabilidade e Consistência dos Eventos | Proposta | 16/11/2026 |

---

## 5. Observações

- O documento contém oito ADRs, superando o mínimo de cinco recomendado pelo modelo oficial do professor.
- As decisões representam a direção arquitetural consolidada nos documentos do OficinaPro.
- Os status deverão ser atualizados para **Aceita** somente após a ratificação formal pelo grupo.
- Decisões que forem modificadas deverão manter seu histórico, sendo marcadas como substituídas quando uma nova ADR assumir seu lugar.
- A seleção da API externa ainda não foi formalizada e poderá gerar uma ADR específica.
- A decisão sobre API Gateway independente também poderá ser registrada em uma ADR adicional, caso seja adotada.
- A arquitetura de nuvem e as tecnologias propostas deverão ser validadas considerando disponibilidade acadêmica, custo e viabilidade de implantação.
- Este documento registra decisões planejadas; não representa evidência de implementação ou implantação dos recursos.

O formato deste documento é baseado na proposta de Michael Nygard, em *Documenting Architecture Decisions*.

Referência: [Documenting Architecture Decisions — Michael Nygard](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions).

---

## 6. Histórico de Revisões

| Versão | Data | Autor | Descrição da Alteração |
|---|---|---|---|
| 1.0 | 16/11/2026 | Grupo GarageOps | Versão inicial com oito registros de decisões arquitetônicas, incluindo backend, banco de dados, comunicação, nuvem, containerização, multi-tenancy, reserva de estoque e confiabilidade dos eventos. |

---

**Aprovado pelo grupo:**

___________________________________________________

**Data:** ___/___/_____

**Aprovado pelo professor orientador:**

___________________________________________________

**Data:** ___/___/_____
