# Glossário — OficinaPro

> **Documento histórico — não usar na entrega.** Este arquivo foi escrito para o modelo anterior do OficinaPro, com controle de estoque, reserva de peças e perfil de estoque, retirados do escopo pelo grupo em 13/11/2026. Ele não reflete os serviços, papéis e fluxos atuais. A versão vigente está em [`entrega/Projetos/OficinaPro/`](../../entrega/Projetos/OficinaPro/README.md) (Documento 02 (seção 6)). Mantido apenas como registro do levantamento.

> **Documento de trabalho — Grupo GarageOps**
>
> **Objetivo:** padronizar os principais termos utilizados no projeto OficinaPro.
>
> Este documento serve como referência para requisitos, código, documentação, reuniões e comunicação entre os integrantes da equipe. As definições representam as regras de domínio já estabelecidas pelo grupo. Detalhes técnicos ainda sujeitos à validação do professor devem ser tratados como propostas.

---

## 1. Cliente

Pessoa que contrata os serviços da oficina, identificada pelo CPF, que deve ser único no sistema. Um cliente pode possuir um ou mais veículos associados.

## 2. Veículo

Automóvel pertencente ou associado a um cliente. É identificado de forma única pela placa e possui um histórico de manutenções vinculado aos atendimentos registrados na oficina.

## 3. Ordem de Serviço (OS)

Registro principal utilizado para controlar o ciclo de vida de um atendimento na oficina. Relaciona cliente, veículo, diagnóstico, orçamento, peças, serviços, pagamentos e o status atual do atendimento.

## 4. Status da OS

Indica a etapa atual da ordem de serviço dentro do fluxo operacional do MVP.

| Status | Significado |
| --- | --- |
| `ABERTA` | OS criada pelo atendente, vinculando cliente, veículo e problema relatado. |
| `EM_DIAGNOSTICO` | Veículo sob avaliação técnica do mecânico. |
| `AGUARDANDO_APROVACAO` | Orçamento enviado e aguardando decisão formal do cliente. |
| `EM_EXECUCAO` | Orçamento aprovado e serviços mecânicos em realização. |
| `AGUARDANDO_PAGAMENTO` | Serviços concluídos, aguardando a quitação financeira. |
| `CONCLUIDA` | Pagamento integral registrado e atendimento encerrado. |
| `CANCELADA` | Atendimento cancelado, respeitando as regras de movimentação de estoque e preservação do histórico. |

**Observação:** `APROVADA` e `RECUSADA` representam decisões sobre o orçamento, não status da OS. A aprovação permite a continuidade do atendimento; a recusa pode resultar no encerramento da OS ou na elaboração de um orçamento revisado vinculado à mesma OS.

## 5. Diagnóstico

Avaliação técnica registrada pelo mecânico e associada a uma OS. Descreve os problemas identificados no veículo e serve de base para a elaboração do orçamento.

## 6. Orçamento

Composição financeira dos serviços e das peças necessários para o atendimento. Seu valor é calculado pela fórmula:

`(Serviços + Peças) - Descontos`

O orçamento fica vinculado à OS e deve ser submetido à decisão formal do cliente antes do início da execução.

## 7. Aprovação e Recusa de Orçamento

Registro da decisão formal do cliente sobre um orçamento.

- **Aprovado:** autoriza a execução dos serviços e dispara a baixa automática das quantidades correspondentes no saldo disponível do estoque.
- **Recusado:** impede a execução daquele orçamento. A OS pode ser encerrada ou receber um orçamento revisado, mantendo o vínculo com a mesma OS e preservando o histórico das versões e decisões anteriores.

Quando um orçamento revisado é enviado, a OS retorna ao status `AGUARDANDO_APROVACAO`.

## 8. Serviço

Atividade realizada pela oficina durante a execução de uma OS, como troca de óleo, alinhamento ou revisão.

## 9. Peça

Componente utilizado na manutenção ou no reparo de um veículo, cadastrado e controlado pelo módulo de estoque.

## 10. Estoque

Módulo responsável pelo controle quantitativo das peças da oficina, registrando entradas, baixas, estornos e demais movimentações.

O controle deve permitir identificar as quantidades disponíveis e manter a rastreabilidade das movimentações relacionadas às ordens de serviço.

## 11. Entrada de Estoque

Movimentação que aumenta a quantidade disponível de uma peça no inventário, geralmente em razão de novas aquisições ou reposições.

## 12. Baixa de Estoque

Movimentação automática que reduz a quantidade disponível de uma peça no inventário no momento da aprovação formal do orçamento.

A baixa representa a reserva ou retirada da quantidade do saldo disponível para aquela OS. Não significa, necessariamente, que a peça já tenha sido instalada ou consumida fisicamente.

## 13. Estorno de Estoque

Movimentação que reverte uma baixa anterior e devolve a quantidade ao saldo disponível do estoque.

É aplicável quando uma OS aprovada é cancelada e as peças baixadas ainda não foram instaladas nem consumidas, permanecendo disponíveis para utilização.

O estorno deve manter o vínculo com a movimentação original e impedir devoluções duplicadas.

## 14. Perda ou Descarte de Peças

Registro de peças que foram instaladas, consumidas ou que não podem mais retornar ao estoque comercial após o cancelamento de uma OS.

Nesses casos, não deve ocorrer estorno para o saldo disponível. A movimentação original deve ser preservada, acompanhada do registro correspondente de perda ou descarte.

## 15. Pagamento

Registro financeiro associado a uma OS, contendo informações como valor, data e forma de pagamento. A quitação integral é necessária para que o atendimento seja considerado concluído.

## 16. Histórico de Manutenção

Registro consolidado dos atendimentos anteriores de um veículo, incluindo diagnósticos, serviços, peças utilizadas, valores e pagamentos.

O histórico deve permanecer vinculado ao veículo, mesmo quando houver alteração de proprietário.

## 17. Usuário

Pessoa que possui credenciais de acesso ao OficinaPro e está vinculada a um perfil de permissões.

## 18. Perfil (RBAC)

Conjunto de permissões que determina quais funcionalidades e operações podem ser acessadas por cada usuário.

Os perfis definidos pelo grupo para o MVP são:

- **Atendente:** responsável pelas atividades de atendimento e cadastro relacionadas ao fluxo da OS.
- **Mecânico:** responsável pelo diagnóstico e pela execução dos serviços autorizados.
- **Estoque:** responsável pelo controle e pelas movimentações de peças.
- **Financeiro:** responsável pelos registros e controles de pagamento.

Não há um perfil de Administrador definido para o MVP.

## 19. Autenticação

Processo de verificação da identidade de um usuário para permitir seu acesso ao sistema. Envolve a validação das credenciais, incluindo o tratamento seguro das senhas.

## 20. Autorização

Mecanismo que verifica se um usuário autenticado possui as permissões necessárias para executar determinada ação ou acessar uma funcionalidade ou endpoint.

## 21. Permissão

Regra que define quais ações podem ser executadas por cada perfil, como cadastrar clientes, registrar diagnósticos, movimentar estoque ou registrar pagamentos.

## 22. Microsserviço

Serviço independente responsável por um domínio específico da aplicação, com responsabilidades delimitadas.

Os quatro microsserviços previstos na proposta de arquitetura do MVP são:

- `Auth Service`: autenticação e controle de perfis.
- `Work Order Service`: clientes, veículos, ordens de serviço e diagnósticos.
- `Inventory Service`: cadastro de peças e controle de estoque.
- `Billing Service`: orçamentos e operações financeiras.

A divisão e a comunicação entre os serviços ainda devem considerar as orientações e a validação do professor.

## 23. API

Interface de comunicação que permite a integração entre diferentes partes do sistema. A proposta do OficinaPro utiliza APIs REST para a comunicação entre o frontend e os serviços do backend.

## 24. Endpoint

Rota específica de uma API utilizada para realizar uma operação no sistema.

Exemplo: `POST /ordens-servico`.

## 25. Evento

Registro de que uma alteração ou ação ocorreu no sistema e pode ser comunicada a outros serviços.

Exemplo: `ORCAMENTO_APROVADO`, evento que pode sinalizar ao serviço de estoque a necessidade de realizar a baixa das peças vinculadas ao orçamento.

## 26. Mensageria

Mecanismo de comunicação assíncrona utilizado para permitir a troca de mensagens e eventos entre serviços, reduzindo o acoplamento direto entre eles.

## 27. RabbitMQ

Message broker considerado na arquitetura do OficinaPro para gerenciar a troca de mensagens assíncronas entre microsserviços.

Sua utilização e os fluxos específicos de comunicação ainda dependem da validação técnica do professor.

## 28. Banco de Dados

Sistema responsável pelo armazenamento persistente das informações do OficinaPro. A tecnologia prevista para o projeto é o PostgreSQL.

## 29. Migração (Migration)

Mecanismo versionado utilizado para controlar alterações na estrutura do banco de dados, como criação ou modificação de tabelas, colunas e relacionamentos.

## 30. Frontend

Camada de interface com a qual os usuários interagem. A tecnologia prevista para o desenvolvimento é React com TypeScript e Vite.

## 31. Backend

Camada responsável pelo processamento das requisições, aplicação das regras de negócio, comunicação entre serviços e acesso aos dados. A tecnologia prevista é Node.js com NestJS e TypeScript.

## 32. Teste Automatizado

Teste executado por ferramentas de software para verificar se determinadas funcionalidades ou partes do código apresentam o comportamento esperado.

O Jest é a ferramenta prevista para os testes automatizados do projeto.

## 33. Cobertura de Testes

Métrica que indica a proporção do código-fonte executada durante os testes automatizados.

A meta estabelecida para o projeto é de cobertura mínima de 60%, conforme o escopo de medição definido para a aplicação.

## 34. CI/CD

Práticas de integração e entrega contínuas que automatizam etapas do desenvolvimento, como análise, execução de testes e construção da aplicação.

O projeto prevê o uso do GitHub Actions para automatizar essas etapas.

## 35. Infraestrutura como Código (IaC)

Abordagem de gerenciamento e provisionamento de infraestrutura por meio de arquivos de configuração versionados.

O Terraform é a ferramenta prevista para essa finalidade no OficinaPro.

## 36. Docker e Docker Compose

Tecnologias utilizadas para criar e executar ambientes isolados por meio de contêineres.

O Docker Compose permite configurar e orquestrar os serviços necessários ao ambiente local de desenvolvimento, como banco de dados, ferramentas de administração e serviços da aplicação.

## 37. Adminer

Ferramenta com interface web utilizada para visualizar e administrar bancos de dados, incluindo o PostgreSQL.

## 38. Observabilidade

Conjunto de práticas e recursos utilizados para acompanhar o funcionamento e a saúde da aplicação por meio de logs, métricas e outros indicadores.

A necessidade e o nível de implementação de ferramentas específicas de observabilidade ainda dependem da validação do professor.

## 39. MVP (Minimum Viable Product)

Produto Mínimo Viável. Corresponde à versão inicial do OficinaPro que contempla as funcionalidades essenciais para demonstrar e validar o fluxo principal de gestão de uma oficina.

## 40. Requisito Funcional e Não Funcional

- **Requisito Funcional (RF):** descreve uma funcionalidade ou comportamento que o sistema deve oferecer, como cadastrar um cliente ou registrar uma ordem de serviço.
- **Requisito Não Funcional (RNF):** descreve critérios de qualidade ou restrições técnicas, como segurança, desempenho, disponibilidade e cobertura de testes.

## 41. História de Usuário (US)

Descrição de uma necessidade do sistema sob a perspectiva de quem utilizará a funcionalidade.

Formato utilizado:

`Como [usuário], quero [ação], para [benefício].`

## 42. Critério de Aceite (CA)

Condição objetiva e verificável utilizada para determinar se uma funcionalidade atende ao comportamento esperado e pode ser considerada concluída.

## 43. Regra de Negócio (RN)

Diretriz ou restrição que determina como o sistema deve se comportar diante de situações específicas do domínio da oficina.

Exemplo: a execução de serviços depende da aprovação formal do orçamento.

## 44. Product Owner (PO)

Papel responsável por organizar e priorizar o backlog, acompanhar as necessidades do produto e promover o alinhamento dos requisitos de negócio com a equipe.

---

**Última atualização:** 12/11/2026

**Responsável pela organização do documento:** Eloísa Brandão

**Grupo:** GarageOps
