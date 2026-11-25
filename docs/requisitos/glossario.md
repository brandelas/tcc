# Glossário — OficinaPro

> **Documento histórico — não usar na entrega.** Esta versão (revisada em 16/11/2026) segue o modelo com controle de estoque, reserva de peças e Inventory Service. O grupo retirou o estoque do escopo em 13/11/2026 e, em 22/11/2026, adotou o MVP com os serviços `identity`, `customers` e `workorders`. A versão vigente está em [`entrega/Projetos/OficinaPro/`](../../entrega/Projetos/OficinaPro/README.md) (Documento 02 (seção 6)). Mantido como registro do levantamento.

> **Documento oficial de trabalho — Grupo GarageOps**
>
> **Objetivo:** padronizar os principais termos utilizados no projeto OficinaPro.
>
> Este documento serve como referência para requisitos, código, documentação, reuniões e comunicação entre os integrantes da equipe. As definições representam as regras de domínio e as decisões técnicas estabelecidas para o projeto. Detalhes de implementação ainda sujeitos à validação do professor devem ser tratados como propostas, não como funcionalidades já implementadas.

---

## 1. Cliente

Pessoa que contrata os serviços da oficina e possui cadastro no OficinaPro.

É identificada pelo CPF, que deve ser único dentro da oficina (tenant), permitindo que a mesma pessoa possua cadastro em oficinas diferentes.

Um cliente pode possuir um ou mais veículos associados.

## 2. Veículo

Automóvel pertencente ou associado a um cliente cadastrado na oficina.

É identificado pela placa, que deve ser única dentro do tenant correspondente. Possui informações como marca, modelo, ano, cor e quilometragem.

Seu histórico de manutenções permanece vinculado ao veículo, mesmo quando houver alteração de proprietário.

## 3. Ordem de Serviço (OS)

Registro principal utilizado para controlar o ciclo de vida de um atendimento na oficina.

Relaciona cliente, veículo, diagnóstico, orçamento, peças, serviços e informações financeiras. Possui um status operacional que representa a etapa atual do atendimento.

A situação financeira da OS é acompanhada separadamente de seu status operacional.

## 4. Status da OS

Indica a etapa atual da ordem de serviço dentro do fluxo operacional do MVP.

| Status | Significado |
|---|---|
| `ABERTA` | OS criada pelo atendente, vinculando cliente, veículo e problema relatado. |
| `EM_DIAGNOSTICO` | Veículo sob avaliação técnica do mecânico. |
| `AGUARDANDO_APROVACAO` | Orçamento enviado e aguardando decisão formal do cliente. |
| `AGUARDANDO_RESERVA` | Cliente aceitou o orçamento e o sistema está aguardando a confirmação da reserva integral das peças necessárias. |
| `EM_EXECUCAO` | Reserva confirmada e aprovação operacional concluída; serviços mecânicos em realização. |
| `CONCLUIDA` | Serviços e procedimentos operacionais da OS finalizados. A quitação financeira é acompanhada separadamente. |
| `CANCELADA` | Atendimento cancelado, respeitando as regras de movimentação de estoque e preservação do histórico. |

**Observação:** `APROVADA` e `RECUSADA` representam decisões sobre o orçamento, não status da OS. A aprovação operacional permite a continuidade do atendimento após a confirmação da reserva. A recusa pode resultar no encerramento da OS ou na elaboração de um orçamento revisado vinculado à mesma OS.

## 5. Diagnóstico

Avaliação técnica registrada pelo mecânico e associada a uma OS.

Descreve os problemas identificados no veículo e serve de base para a elaboração do orçamento.

## 6. Orçamento

Composição financeira dos serviços e das peças necessários para o atendimento.

Seu valor é calculado pela fórmula:

`(Serviços + Peças) - Descontos`

O orçamento fica vinculado à OS e deve ser submetido à decisão formal do cliente antes do início da execução.

## 7. Aceitação do Orçamento

Registro da decisão do cliente de concordar com os valores e serviços apresentados em determinado orçamento.

A aceitação inicia o processo de solicitação de reserva das peças necessárias. Não representa, isoladamente, a aprovação operacional definitiva nem libera a execução dos serviços.

## 8. Aprovação Operacional do Orçamento

Confirmação registrada pelo sistema após a aceitação do cliente e a confirmação da reserva integral das peças necessárias.

Representa a autorização para a continuidade operacional do atendimento e a liberação da execução dos serviços.

Deve manter o registro do responsável, da data, do horário e da decisão.

## 9. Recusa de Orçamento

Registro da decisão do cliente de não aceitar os valores ou serviços apresentados.

Impede a execução daquele orçamento. A OS pode ser encerrada ou receber um orçamento revisado, mantendo o vínculo com a mesma OS e preservando o histórico das versões e decisões anteriores.

Quando um orçamento revisado é enviado, a OS retorna ao status `AGUARDANDO_APROVACAO`.

## 10. Serviço

Atividade realizada pela oficina durante a execução de uma OS, como troca de óleo, alinhamento ou revisão.

## 11. Peça

Componente utilizado na manutenção ou no reparo de um veículo, cadastrado e controlado pelo módulo de estoque.

Cada peça possui informações de identificação, quantidade e condição física, quando aplicável.

## 12. Estoque

Módulo responsável pelo controle quantitativo e pela rastreabilidade das peças da oficina.

Registra entradas, reservas, consumos definitivos, liberações, perdas, descartes e demais movimentações.

O controle diferencia estoque físico, estoque reservado e estoque disponível.

## 13. Estoque Físico

Quantidade de peças que existem fisicamente no inventário da oficina.

Uma peça reservada continua fazendo parte do estoque físico enquanto não for efetivamente consumida, instalada, perdida ou descartada.

## 14. Estoque Reservado

Quantidade de peças comprometida com uma ou mais ordens de serviço, mas que ainda não foi consumida definitivamente.

A reserva reduz o saldo disponível, mas não reduz imediatamente o estoque físico.

## 15. Estoque Disponível

Quantidade de peças que pode ser comprometida em novas operações de reserva.

É calculada pela fórmula:

`Estoque disponível = Estoque físico - Estoque reservado`

O sistema não deve permitir que o saldo disponível fique negativo.

## 16. Entrada de Estoque

Movimentação que aumenta a quantidade física de uma peça no inventário, geralmente em razão de novas aquisições ou reposições.

A entrada deve possuir registro rastreável e estar associada à oficina correspondente.

## 17. Reserva de Estoque

Operação que compromete determinada quantidade de peças disponíveis para uma OS, sem representar consumo físico definitivo.

A reserva deve ser realizada integralmente: todas as quantidades solicitadas são reservadas ou nenhuma é reservada.

É responsabilidade do Inventory Service controlar a disponibilidade, a concorrência e a integridade das reservas.

## 18. Baixa de Estoque

Termo genérico utilizado para descrever uma movimentação que reduz a quantidade disponível para novas operações.

No OficinaPro, deve-se distinguir a reserva da baixa definitiva por consumo:

- **Reserva:** reduz o estoque disponível, mantendo a quantidade no estoque físico.
- **Consumo definitivo:** registra a utilização efetiva da peça, reduzindo o estoque físico e encerrando a quantidade reservada correspondente.

A expressão isolada "baixa de estoque" deve ser evitada em requisitos e documentação quando não estiver claro qual dessas operações está sendo descrita.

## 19. Consumo Definitivo de Estoque

Movimentação que representa a utilização efetiva de uma peça anteriormente reservada.

Quando uma peça é instalada ou consumida, a operação reduz a quantidade física e a quantidade reservada correspondentes, impedindo que o mesmo item seja contabilizado duas vezes.

O consumo deve permanecer vinculado à reserva original e à OS correspondente.

## 20. Liberação de Reserva

Operação que desfaz o compromisso de uma quantidade previamente reservada, permitindo que ela volte a compor o estoque disponível.

É aplicável quando a peça reservada não foi utilizada e permanece em condições de reutilização.

A liberação reduz o estoque reservado, mas não aumenta o estoque físico, pois a peça já estava fisicamente no inventário.

## 21. Estorno de Estoque

Movimentação de reversão associada a uma movimentação anterior.

No contexto de reservas, o estorno pode representar a liberação de quantidades comprometidas, desde que a condição física das peças permita seu retorno ao saldo disponível.

O estorno deve manter o vínculo com a movimentação original e impedir reversões duplicadas.

**Observação:** estorno não significa necessariamente devolução de uma peça ao estoque. O tratamento depende da natureza da movimentação original e da condição física do item.

## 22. Perda ou Descarte de Peças

Registro de peças que foram instaladas, consumidas, danificadas ou que não podem mais retornar ao estoque comercializável.

Nesses casos, não deve ocorrer liberação para o saldo disponível.

A movimentação original deve ser preservada, acompanhada do registro correspondente de consumo, perda ou descarte.

## 23. Condição Física da Peça

Informação que representa a situação material de uma peça durante o processo de reserva e utilização.

Permite distinguir peças ainda reservadas e disponíveis para instalação daquelas que já foram instaladas, consumidas, danificadas ou descartadas.

A condição física deve ser considerada nas operações de liberação, consumo e cancelamento de OS.

## 24. Controle de Concorrência

Mecanismo utilizado para impedir que operações simultâneas provoquem inconsistências nos dados.

No Inventory Service, deve impedir que duas solicitações reservem simultaneamente a mesma quantidade disponível.

A implementação poderá utilizar transações, bloqueios de registros e outras estratégias adequadas ao banco de dados.

## 25. Idempotência

Propriedade de uma operação que permite seu processamento repetido sem produzir efeitos duplicados.

No OficinaPro, é necessária para evitar que uma mesma solicitação, mensagem ou tentativa de processamento gere reservas, consumos, pagamentos ou movimentações duplicadas.

Uma chave de idempotência pode identificar cada solicitação única.

## 26. Pagamento

Registro financeiro associado a uma OS, contendo informações como valor, data e forma de pagamento.

O pagamento pode ocorrer integralmente ou em parcelas, conforme as regras financeiras definidas para o sistema.

A quitação financeira não determina, por si só, o status operacional da OS.

## 27. Status Financeiro

Indica a situação dos pagamentos relacionados à OS, independentemente de seu status operacional.

| Status | Significado |
|---|---|
| `PENDENTE` | Nenhum pagamento foi registrado ou ainda existe valor integral pendente. |
| `PARCIAL` | Foram registrados pagamentos, mas ainda existe saldo financeiro a quitar. |
| `QUITADA` | O valor total devido foi integralmente pago. |

Uma OS pode estar operacionalmente `CONCLUIDA` e permanecer financeiramente `PENDENTE` ou `PARCIAL`.

## 28. Histórico de Manutenção

Registro consolidado dos atendimentos anteriores de um veículo, incluindo diagnósticos, serviços, peças utilizadas, valores e pagamentos.

O histórico deve permanecer vinculado ao veículo, mesmo quando houver alteração de proprietário.

## 29. Usuário

Pessoa que possui credenciais de acesso ao OficinaPro e está vinculada a uma oficina e a um perfil de permissões.

O acesso aos dados deve respeitar o tenant ao qual o usuário pertence.

## 30. Perfil (RBAC)

Conjunto de permissões que determina quais funcionalidades e operações podem ser acessadas por cada usuário.

Os perfis definidos pelo grupo para o MVP são:

- **Atendente:** responsável pelas atividades de atendimento e cadastro relacionadas ao fluxo da OS.
- **Mecânico:** responsável pelo diagnóstico e pela execução dos serviços autorizados.
- **Estoque:** responsável pelo controle e pelas movimentações de peças.
- **Financeiro:** responsável pelos registros e controles de pagamento.

Não há um perfil genérico de Administrador da Oficina definido para o MVP.

## 31. Autenticação

Processo de verificação da identidade de um usuário para permitir seu acesso ao sistema.

Envolve a validação das credenciais e o tratamento seguro das senhas.

## 32. Autorização

Mecanismo que verifica se um usuário autenticado possui as permissões necessárias para executar determinada ação ou acessar uma funcionalidade ou endpoint.

A autorização deve ser validada no backend.

## 33. Permissão

Regra que define quais ações podem ser executadas por cada perfil, como cadastrar clientes, registrar diagnósticos, movimentar estoque ou registrar pagamentos.

## 34. SaaS (Software as a Service)

Modelo de disponibilização de software como serviço, no qual diferentes clientes utilizam uma aplicação compartilhada, com seus dados e acessos devidamente isolados.

O OficinaPro adota esse modelo para atender diferentes oficinas por meio de uma plataforma comum.

## 35. Tenant

Representação lógica de uma oficina cadastrada no OficinaPro.

Cada tenant possui seus próprios usuários e registros operacionais, como clientes, veículos, ordens de serviço e estoque.

O isolamento entre tenants é obrigatório: uma oficina não pode consultar ou modificar os dados de outra.

## 36. Multi-tenancy

Arquitetura que permite que diferentes oficinas utilizem a mesma aplicação, mantendo seus dados e permissões isolados.

No OficinaPro, as operações devem respeitar o contexto do tenant autenticado.

O identificador do tenant deve ser validado pelo backend e propagado nas operações e nos eventos entre microsserviços.

## 37. Microsserviço

Serviço independente responsável por um domínio específico da aplicação, com responsabilidades delimitadas e propriedade sobre seus próprios dados.

Os quatro microsserviços previstos na proposta de arquitetura do MVP são:

- `Auth Service`: autenticação e controle de perfis.
- `Work Order Service`: clientes, veículos, ordens de serviço e diagnósticos.
- `Inventory Service`: cadastro de peças e controle de estoque.
- `Billing Service`: orçamentos e operações financeiras.

A divisão e a comunicação entre os serviços devem respeitar os limites de responsabilidade definidos para cada domínio.

## 38. Database per Service

Padrão arquitetural no qual cada microsserviço possui responsabilidade exclusiva sobre seus próprios dados.

Um serviço não deve acessar diretamente o banco de dados de outro serviço. A comunicação deve ocorrer por APIs ou eventos.

Para o MVP, a separação poderá ser implementada inicialmente por bancos lógicos distintos, conforme a infraestrutura e a validação técnica do projeto.

## 39. API

Interface de comunicação que permite a integração entre diferentes partes do sistema.

A proposta do OficinaPro utiliza APIs REST para a comunicação entre o frontend e os serviços do backend.

## 40. Endpoint

Rota específica de uma API utilizada para realizar uma operação no sistema.

Exemplo:

`POST /ordens-servico`

## 41. Evento

Registro de que uma alteração ou ação ocorreu no sistema e pode ser comunicada a outros serviços.

Exemplo: `RESERVA_CONFIRMADA`, evento que informa aos serviços interessados que as peças necessárias para determinada OS foram reservadas integralmente.

Os eventos devem carregar informações suficientes para identificar o tenant e correlacionar a operação de origem.

## 42. Mensageria

Mecanismo de comunicação assíncrona utilizado para permitir a troca de mensagens e eventos entre serviços, reduzindo o acoplamento direto entre eles.

## 43. RabbitMQ

Message broker considerado na arquitetura do OficinaPro para gerenciar a troca de mensagens assíncronas entre microsserviços.

Sua utilização e os fluxos específicos de comunicação devem seguir a arquitetura definida para o projeto.

## 44. Transactional Outbox

Padrão de confiabilidade utilizado para garantir que uma alteração no banco de dados e o registro do evento correspondente sejam persistidos na mesma transação local.

O evento é publicado posteriormente por um processo responsável pela leitura da Outbox.

No OficinaPro, esse padrão é previsto para operações críticas que precisam manter consistência entre a persistência dos dados e a comunicação com outros serviços.

## 45. Inbox

Mecanismo utilizado pelo consumidor de mensagens para registrar mensagens já processadas e evitar que o recebimento repetido de um mesmo evento produza efeitos duplicados.

Pode ser implementado por meio de uma tabela ou estrutura equivalente, utilizando identificadores únicos de mensagens.

## 46. DLQ (Dead Letter Queue)

Fila destinada a receber mensagens que não puderam ser processadas após as tentativas de recuperação configuradas.

Permite identificar falhas persistentes, investigar suas causas e realizar procedimentos controlados de recuperação.

## 47. Identificador de Correlação (Correlation ID)

Identificador utilizado para relacionar requisições, operações e eventos pertencentes a um mesmo fluxo de negócio.

Permite rastrear uma operação que percorre diferentes microsserviços, facilitando diagnóstico e auditoria.

## 48. Banco de Dados

Sistema responsável pelo armazenamento persistente das informações do OficinaPro.

A tecnologia prevista para o projeto é o PostgreSQL.

Cada microsserviço deve manter a responsabilidade sobre seus próprios dados.

## 49. Migração (Migration)

Mecanismo versionado utilizado para controlar alterações na estrutura do banco de dados, como criação ou modificação de tabelas, colunas e relacionamentos.

Permite manter a evolução do banco de dados rastreável e reproduzível entre ambientes.

## 50. Frontend

Camada de interface com a qual os usuários interagem.

A tecnologia prevista para o desenvolvimento é React com TypeScript e Vite.

## 51. Backend

Camada responsável pelo processamento das requisições, aplicação das regras de negócio, autorização, comunicação entre serviços e acesso aos dados.

A tecnologia prevista é Node.js com NestJS e TypeScript.

## 52. Teste Automatizado

Teste executado por ferramentas de software para verificar se determinadas funcionalidades ou partes do código apresentam o comportamento esperado.

Os testes podem incluir verificações unitárias, de integração e de ponta a ponta, conforme a necessidade de cada funcionalidade.

O Jest é a ferramenta prevista para os testes automatizados do projeto.

## 53. Cobertura de Testes

Métrica que indica a proporção do código-fonte executada durante os testes automatizados.

A meta estabelecida para o projeto é de cobertura mínima de 60% do código-fonte total dos microsserviços, conforme o critério de medição definido para a aplicação.

A cobertura deverá considerar o conjunto dos microsserviços, de acordo com a exigência acadêmica do projeto.

## 54. CI/CD

Práticas de integração e entrega contínuas que automatizam etapas do desenvolvimento, como análise estática, execução de testes, construção e disponibilização da aplicação.

O projeto prevê o uso do GitHub Actions para automatizar essas etapas.

## 55. Infraestrutura como Código (IaC)

Abordagem de gerenciamento e provisionamento de infraestrutura por meio de arquivos de configuração versionados.

O Terraform é a ferramenta prevista para essa finalidade no OficinaPro.

## 56. Docker e Docker Compose

Tecnologias utilizadas para criar e executar ambientes isolados por meio de contêineres.

O Docker Compose permite configurar e orquestrar os serviços necessários ao ambiente local de desenvolvimento, como banco de dados, ferramentas de administração e serviços da aplicação.

## 57. Adminer

Ferramenta com interface web utilizada para visualizar e administrar bancos de dados, incluindo o PostgreSQL.

## 58. Observabilidade

Conjunto de práticas e recursos utilizados para acompanhar o funcionamento e a saúde da aplicação por meio de logs, métricas e rastreamento de operações.

A arquitetura do projeto prevê o uso de Prometheus e Grafana para monitoramento e visualização de métricas.

## 59. Prometheus

Ferramenta de monitoramento e coleta de métricas de sistemas e aplicações.

É prevista para coletar indicadores de funcionamento dos microsserviços e disponibilizar dados para acompanhamento operacional.

## 60. Grafana

Plataforma de visualização e análise de métricas, utilizada para construir painéis de monitoramento.

No OficinaPro, é prevista para apresentar indicadores coletados pelo Prometheus.

## 61. MVP (Minimum Viable Product)

Produto Mínimo Viável. Corresponde à versão inicial do OficinaPro que contempla as funcionalidades essenciais para demonstrar e validar o fluxo principal de gestão de uma oficina.

O MVP prioriza o fluxo de cadastro, atendimento, diagnóstico, orçamento, reserva de peças, execução e acompanhamento financeiro.

## 62. Requisito Funcional e Não Funcional

- **Requisito Funcional (RF):** descreve uma funcionalidade ou comportamento que o sistema deve oferecer, como cadastrar um cliente ou registrar uma ordem de serviço.
- **Requisito Não Funcional (RNF):** descreve critérios de qualidade ou restrições técnicas, como segurança, desempenho, disponibilidade e cobertura de testes.

## 63. História de Usuário (HU)

Descrição de uma necessidade do sistema sob a perspectiva de quem utilizará a funcionalidade.

Formato utilizado:

`Como [usuário], quero [ação], para [benefício].`

## 64. Critério de Aceite (CA)

Condição objetiva e verificável utilizada para determinar se uma funcionalidade atende ao comportamento esperado e pode ser considerada concluída.

## 65. Regra de Negócio (RN)

Diretriz ou restrição que determina como o sistema deve se comportar diante de situações específicas do domínio da oficina.

Exemplo: a execução dos serviços depende da aceitação do orçamento, da reserva integral das peças necessárias e da aprovação operacional.

## 66. Product Owner (PO)

Papel responsável por organizar e priorizar o backlog, acompanhar as necessidades do produto e promover o alinhamento dos requisitos de negócio com a equipe.

---

**Última atualização:** 16/11/2026

**Responsável pela organização do documento:** Eloísa Brandão

**Grupo:** GarageOps
