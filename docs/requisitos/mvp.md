# MVP (Minimum Viable Product) — OficinaPro

> **Documento histórico — não usar na entrega.** Esta versão (revisada em 16/11/2026) segue o modelo com controle de estoque, reserva de peças e Inventory Service. O grupo retirou o estoque do escopo em 13/11/2026 e, em 22/11/2026, adotou o MVP com os serviços `identity`, `customers` e `workorders`. A versão vigente está em [`entrega/Projetos/OficinaPro/`](../../entrega/Projetos/OficinaPro/README.md) (Documento 01 (seção 5)). Mantido como registro do levantamento.

> **Status:** 🟢 Escopo funcional definido pelo grupo GarageOps, sujeito à validação contínua
>
> **Responsável pela organização:** Eloísa Brandão — Product Owner
>
> **Objetivo:** estabelecer o escopo mínimo viável do OficinaPro para demonstrar a viabilidade da solução e a integração inicial dos microsserviços, conforme as exigências do Projeto Experimental de ADS — UniFAAT.

---

## 1. Objetivo do MVP

O MVP do OficinaPro tem como objetivo entregar um fluxo operacional integrado para oficinas mecânicas de pequeno e médio porte.

A primeira versão deverá demonstrar que o sistema consegue organizar o atendimento de uma oficina desde o cadastro do cliente e do veículo até a abertura da ordem de serviço, diagnóstico, elaboração e aceitação do orçamento, reserva de peças, execução do serviço e acompanhamento financeiro.

O produto deverá demonstrar, além das operações básicas de cadastro, a aplicação de regras de negócio relevantes, principalmente:

- isolamento dos dados entre diferentes oficinas;
- controle de acesso conforme os perfis dos usuários;
- aprovação operacional condicionada à reserva integral das peças;
- controle de concorrência no estoque;
- prevenção de movimentações duplicadas;
- rastreabilidade das operações;
- comunicação consistente entre microsserviços.

O MVP será desenvolvido como uma primeira versão funcional e evolutiva. A arquitetura deverá permitir a ampliação de funcionalidades sem comprometer os limites de responsabilidade dos serviços.

## 2. Público-alvo

O OficinaPro será direcionado a:

- oficinas mecânicas independentes;
- oficinas de pequeno e médio porte;
- auto centers;
- pequenos centros automotivos.

O sistema busca reduzir a dependência de controles manuais, planilhas e ferramentas desconectadas, centralizando as informações operacionais da oficina.

## 3. Fluxo Principal do MVP

O fluxo central deverá funcionar de forma integrada, respeitando a sequência de decisões e movimentações abaixo:

```text
Cliente
   ↓
Veículo
   ↓
Ordem de Serviço (ABERTA)
   ↓
Diagnóstico Técnico (EM_DIAGNOSTICO)
   ↓
Elaboração do Orçamento
   ↓
Aguardando decisão do cliente (AGUARDANDO_APROVACAO)
   ↓
Aceitação ou recusa do orçamento
   ↓
Se aceito: solicitação de reserva das peças
   ↓
Aguardando confirmação da reserva (AGUARDANDO_RESERVA)
   ↓
Reserva integral confirmada pelo Inventory Service
   ↓
Aprovação operacional do orçamento
   ↓
Execução do Serviço (EM_EXECUCAO)
   ↓
Registro do consumo das peças efetivamente utilizadas
   ↓
Conclusão operacional da OS (CONCLUIDA)
   ↓
Registro e acompanhamento dos pagamentos
   ↓
Atualização do histórico de manutenção
```

### 3.1 Regras fundamentais do fluxo

1. A aceitação do orçamento pelo cliente não representa, isoladamente, autorização para iniciar a execução.
2. A aceitação inicia a solicitação de reserva das peças necessárias.
3. A reserva deve ser integral: todas as peças solicitadas são reservadas ou nenhuma é reservada.
4. Se a reserva não puder ser confirmada, a OS não poderá avançar para a execução.
5. A aprovação operacional somente ocorrerá após a confirmação da reserva integral.
6. A utilização efetiva de uma peça converte a respectiva quantidade reservada em consumo definitivo.
7. Peças reservadas e não utilizadas poderão ter suas reservas liberadas, desde que estejam em condições de reutilização.
8. Peças danificadas, instaladas ou consumidas não poderão retornar indevidamente ao estoque disponível.
9. A conclusão operacional da OS será independente da quitação financeira.
10. O histórico deverá preservar os registros do atendimento, das decisões e das movimentações de estoque.

---

## 4. Funcionalidades Essenciais — Escopo do MVP

### 4.1 Clientes

- Cadastro de clientes com nome, CPF, telefone, e-mail e endereço.
- Validação do CPF.
- CPF único dentro de cada oficina (tenant).
- Consulta de clientes por nome ou CPF.
- Atualização dos dados cadastrais permitidos.
- Desativação de cadastros quando houver necessidade de preservar o histórico.

**Prioridade:** 🔴 Essencial

### 4.2 Veículos

- Cadastro de veículos vinculado obrigatoriamente a um cliente da mesma oficina.
- Identificação pela placa.
- Placa única dentro de cada oficina (tenant).
- Registro de marca, modelo, ano, cor e quilometragem.
- Consulta de veículos pela placa.
- Preservação do histórico de manutenção mesmo em caso de mudança de proprietário.

**Prioridade:** 🔴 Essencial

### 4.3 Ordem de Serviço (OS)

- Abertura de OS vinculando cliente e veículo.
- Registro do problema relatado pelo cliente.
- Identificador único para cada OS.
- Consulta e acompanhamento do status operacional.
- Histórico das alterações e decisões.
- Isolamento das ordens de serviço por oficina.

A esteira operacional oficial será:

`ABERTA` → `EM_DIAGNOSTICO` → `AGUARDANDO_APROVACAO` → `AGUARDANDO_RESERVA` → `EM_EXECUCAO` → `CONCLUIDA`.

O status `CANCELADA` será utilizado quando o atendimento for encerrado por cancelamento permitido pelas regras de negócio.

As decisões `APROVADA` e `RECUSADA` pertencem ao orçamento e não representam status operacionais da OS.

**Prioridade:** 🔴 Essencial

### 4.4 Diagnóstico

- Registro da avaliação técnica realizada pelo mecânico.
- Associação obrigatória do diagnóstico à OS correspondente.
- Consulta das informações técnicas registradas.
- Preservação do diagnóstico no histórico do atendimento.

**Prioridade:** 🔴 Essencial

### 4.5 Orçamento

- Composição de serviços e peças.
- Registro de quantidades e valores.
- Cálculo automático do valor total:

```text
Total = Serviços + Peças - Descontos
```

- Registro da decisão do cliente: aceitação ou recusa.
- Registro do responsável, data, horário e decisão.
- Possibilidade de elaboração de orçamento revisado, preservando as versões anteriores.
- Bloqueio da execução enquanto a reserva integral e a aprovação operacional não forem confirmadas.

**Prioridade:** 🔴 Essencial

### 4.6 Execução e Estoque

- Cadastro de peças e controle de quantidades.
- Registro de entradas e movimentações.
- Consulta de estoque físico, reservado e disponível.
- Solicitação de reserva após a aceitação do orçamento.
- Reserva integral das quantidades necessárias.
- Bloqueio da aprovação operacional quando não houver estoque disponível suficiente.
- Controle de concorrência para evitar reservas simultâneas indevidas.
- Uso de chaves de idempotência para impedir movimentações duplicadas.
- Registro do consumo definitivo das peças efetivamente utilizadas.
- Liberação de reservas de peças não utilizadas e em condições de reutilização.
- Registro de perdas, danos e descartes.
- Histórico rastreável das movimentações.

O controle do inventário será responsabilidade do Inventory Service.

**Prioridade:** 🔴 Essencial

### 4.7 Financeiro e Pagamento

- Cálculo do valor total associado à OS.
- Registro de pagamentos vinculados à OS.
- Consulta do valor pago e do saldo pendente.
- Acompanhamento do status financeiro.
- Separação entre conclusão operacional e quitação financeira.

Os status financeiros previstos são:

- `PENDENTE`;
- `PARCIAL`;
- `QUITADA`.

O MVP deverá contemplar o registro dos pagamentos e a atualização correta desses estados. Integrações com gateways de pagamento reais ou em sandbox não são consideradas automaticamente parte do escopo inicial.

**Prioridade:** 🔴 Essencial

### 4.8 Histórico de Manutenção

- Consulta dos atendimentos anteriores vinculados ao veículo.
- Exibição das OS, diagnósticos, serviços e peças utilizados.
- Consulta dos valores e informações financeiras relacionados.
- Preservação do histórico mesmo após mudança de proprietário.
- Rastreabilidade das movimentações de estoque relacionadas aos atendimentos.

**Prioridade:** 🔴 Essencial

### 4.9 Autenticação e Permissões

- Autenticação centralizada pelo Auth Service.
- Armazenamento seguro de senhas por meio de hash.
- Controle de acesso baseado em perfis (RBAC).
- Perfis previstos para o MVP:
  - Atendente;
  - Mecânico;
  - Estoque;
  - Financeiro.
- Associação dos usuários às respectivas oficinas.
- Isolamento de dados entre tenants.
- Validação das permissões no backend.

**Prioridade:** 🔴 Essencial

### 4.10 Integração com API Externa

O projeto deverá consumir pelo menos uma API externa relevante para o domínio de oficinas mecânicas, conforme exigência do termo oficial de aceite do Projeto Experimental.

As possibilidades já mencionadas na descrição do OficinaPro são:

- **ViaCEP:** consulta de endereço a partir do CEP, auxiliando o preenchimento do cadastro de clientes.
- **API FIPE:** consulta de informações e valores de referência de veículos.

A integração escolhida deverá:

- possuir finalidade relacionada às necessidades do OficinaPro;
- ser documentada tecnicamente;
- ter seu comportamento de sucesso e falha validado;
- tratar indisponibilidade da API externa sem comprometer indevidamente os cadastros e operações internas;
- possuir sua decisão registrada na documentação de arquitetura (ADR).

**Prioridade:** 🔴 Essencial para atendimento ao requisito acadêmico.

**Status da escolha:** 🟡 Pendente de formalização pelo grupo.

---

## 5. Arquitetura Funcional do MVP

A proposta inicial de arquitetura do MVP será organizada em quatro microsserviços principais.

| Microsserviço | Responsabilidades no MVP |
|---|---|
| Auth Service | Autenticação, usuários, perfis e autorização. |
| Work Order Service | Clientes, veículos, OS, diagnósticos e orçamentos. |
| Inventory Service | Cadastro de peças, entradas, reservas, consumo, liberações e perdas. |
| Billing Service | Valores da OS, registros de pagamento e situação financeira. |

### 5.1 Responsabilidade dos serviços

Cada microsserviço será responsável por seu próprio domínio e seus dados.

A comunicação entre serviços deverá ocorrer por APIs e eventos, sem acesso direto ao banco de dados de outro microsserviço.

A proposta técnica prevê:

- Backend: Node.js, TypeScript e NestJS.
- Frontend: React, TypeScript e Vite.
- Banco de dados: PostgreSQL.
- Mensageria: RabbitMQ.
- Contêineres: Docker e Docker Compose.
- Testes: Jest e ferramentas complementares adequadas.
- Versionamento: Git e GitHub.

### 5.2 Estratégia de evolução dos serviços

O README de referência do projeto também apresenta serviços adicionais, como Customer Vehicle Service e Notification Service.

Para evitar uma decomposição excessiva no primeiro momento, o MVP manterá clientes e veículos no domínio do Work Order Service.

A evolução poderá separar esses domínios em serviços independentes quando houver justificativa técnica e validação do grupo.

Da mesma forma, notificações avançadas e relatórios gerenciais não serão obrigatórios na primeira versão.

---

## 6. Requisitos Técnicos e de Qualidade

O MVP deverá demonstrar a viabilidade técnica da arquitetura e das principais regras de negócio.

### 6.1 Persistência e integridade

- Utilização de PostgreSQL.
- Separação lógica dos dados por microsserviço.
- Integridade referencial dentro dos limites de cada serviço.
- Referências entre serviços por APIs ou eventos.
- Isolamento dos registros por tenant.
- Migrações de banco de dados versionadas.

### 6.2 Comunicação entre microsserviços

- Comunicação síncrona por APIs quando necessária.
- Comunicação assíncrona por mensageria nos fluxos apropriados.
- Identificação dos eventos por identificadores únicos.
- Tratamento idempotente de mensagens repetidas.
- Identificação do tenant e correlação das operações.
- Uso do padrão Transactional Outbox nas operações críticas em que a persistência e a publicação de eventos precisarem permanecer consistentes.
- Estratégias de repetição e recuperação de falhas.

### 6.3 Segurança

- Senhas armazenadas com hash seguro.
- Autorização validada no backend.
- Isolamento entre oficinas.
- Validação dos dados recebidos.
- Proteção de informações sensíveis.
- Segredos e credenciais fora do código-fonte.

### 6.4 Testes

- Testes unitários para regras de negócio críticas.
- Testes de integração entre serviços.
- Testes de isolamento entre tenants.
- Testes de concorrência e idempotência no inventário.
- Testes do fluxo completo de atendimento.
- Meta global mínima de cobertura de 60% do código-fonte total dos microsserviços, conforme o termo oficial do projeto.

**Observação:** o termo oficial exige a cobertura mínima global considerando todos os microsserviços. A meta não deve ser interpretada como 60% apenas das regras críticas ou de um serviço isolado.

---

## 7. Funcionalidades Fora do Escopo Inicial (Pós-MVP)

Para evitar ampliação descontrolada do escopo e permitir a entrega das funcionalidades essenciais, os itens abaixo ficam classificados como posteriores ou secundários.

| Funcionalidade | Classificação | Observação |
|---|---|---|
| Notificações avançadas por SMS e WhatsApp | Pós-MVP | Depende de integração com provedores externos. |
| Notification Service independente | Pós-MVP | Pode ser separado quando houver justificativa arquitetural. |
| Relatórios gerenciais avançados | Pós-MVP | Indicadores e dashboards analíticos complexos. |
| Customer Vehicle Service independente | Evolução arquitetural | Clientes e veículos permanecem inicialmente no Work Order Service. |
| Integração com gateway de pagamento real | Pós-MVP | O registro financeiro básico será realizado no MVP. |
| Automação de cobranças e conciliação bancária | Pós-MVP | Não essencial para demonstrar o fluxo inicial. |
| Gestão de assinatura e cobrança SaaS das oficinas | Pós-MVP | A cobrança pelo uso da plataforma não faz parte do fluxo financeiro das OS. |
| Aplicativo mobile nativo | Pós-MVP | A primeira versão prioriza a aplicação web responsiva. |
| Inteligência artificial para diagnóstico | Pós-MVP | Não é necessária para validar o fluxo principal. |
| Relatórios preditivos e análise avançada de dados | Pós-MVP | Evolução futura do produto. |

---

## 8. Matriz de Priorização Consolidada

| Funcionalidade | Prioridade | Status |
|---|---|---|
| Cadastro de clientes | Essencial | 🟢 Definido |
| Cadastro de veículos | Essencial | 🟢 Definido |
| Abertura e gestão de OS | Essencial | 🟢 Definido |
| Diagnóstico técnico | Essencial | 🟢 Definido |
| Elaboração de orçamento | Essencial | 🟢 Definido |
| Aceitação e recusa de orçamento | Essencial | 🟢 Definido |
| Reserva integral de estoque | Essencial | 🟢 Definido |
| Consumo, liberação e perda de peças | Essencial | 🟢 Definido |
| Controle de concorrência e idempotência | Essencial | 🟢 Definido |
| Registro e acompanhamento financeiro | Essencial | 🟢 Definido |
| Histórico de manutenção | Essencial | 🟢 Definido |
| Autenticação e perfis | Essencial | 🟢 Definido |
| Isolamento multi-tenant | Essencial | 🟢 Definido |
| Integração com API externa relevante | Essencial | 🟡 Escolha a formalizar |
| Comunicação integrada entre microsserviços | Essencial | 🟢 Definido |
| Notificações avançadas | Secundário | 🔵 Pós-MVP |
| Relatórios gerenciais avançados | Secundário | 🔵 Pós-MVP |
| Gestão de assinatura SaaS | Secundário | 🔵 Pós-MVP |

---

## 9. Critérios de Sucesso do MVP

O MVP será considerado funcionalmente validado quando o grupo GarageOps conseguir demonstrar:

1. Cadastro de uma oficina e autenticação de seus usuários.
2. Isolamento dos dados entre oficinas diferentes.
3. Cadastro de clientes e veículos.
4. Abertura de uma OS vinculada ao cliente e ao veículo.
5. Registro de diagnóstico técnico.
6. Elaboração de orçamento com cálculo automático.
7. Registro da aceitação ou recusa pelo cliente.
8. Solicitação e confirmação de reserva integral das peças.
9. Bloqueio da execução quando a reserva não puder ser confirmada.
10. Aprovação operacional após a reserva integral.
11. Execução da OS e registro do consumo efetivo das peças.
12. Liberação correta das peças não utilizadas e tratamento das perdas.
13. Conclusão operacional independente da quitação financeira.
14. Registro de pagamento e atualização do status financeiro.
15. Consulta do histórico de manutenção do veículo.
16. Comunicação funcional entre os microsserviços envolvidos.
17. Integração demonstrável com pelo menos uma API externa relevante.
18. Testes automatizados para as principais regras de negócio e fluxos críticos.

A validação deverá ser acompanhada de evidências, como testes executados, registros de execução, demonstração funcional e documentação atualizada.

---

## 10. Cronograma Acadêmico e Evolução do Produto

Conforme o Termo de Requisitos e Aceite do Projeto Experimental da UniFAAT, o desenvolvimento está distribuído em dois semestres.

| Etapa | Entregas esperadas |
|---|---|
| **4º Semestre** | Entrega dos oito documentos técnicos exigidos e de um MVP funcional, demonstrando a viabilidade da proposta e a integração inicial dos microsserviços. |
| **5º Semestre** | Desenvolvimento completo da solução, implantação em nuvem, infraestrutura provisionada via Terraform, CI/CD automatizado, cobertura mínima de testes, monitoramento funcional e defesa perante a banca. |

### 10.1 Entregas do 4º semestre

- Documentação técnica exigida pelo professor.
- MVP funcional.
- Fluxo principal demonstrável.
- Integração inicial entre microsserviços.
- Evidências de validação das funcionalidades essenciais.

### 10.2 Evolução no 5º semestre

- Ampliação e estabilização das funcionalidades.
- Implantação em provedor de nuvem de grande porte, como AWS, GCP ou Azure.
- Provisionamento automatizado com Terraform.
- Pipeline de CI/CD com GitHub Actions.
- Cobertura global mínima de 60% do código-fonte total dos microsserviços.
- Monitoramento funcional com Prometheus e Grafana.
- Demonstração da aplicação em ambiente de nuvem.
- Preparação e realização da defesa perante a banca.

---

## 11. Decisões e Pendências de Validação

As definições abaixo devem ser acompanhadas para evitar que o escopo seja interpretado como integralmente implementado antes da validação técnica.

| Item | Situação | Encaminhamento |
|---|---|---|
| Quatro microsserviços principais no MVP | 🟢 Definido | Manter a divisão inicial por domínio. |
| Clientes e veículos no Work Order Service | 🟢 Definido | Avaliar separação futura apenas se houver justificativa. |
| Reserva integral antes da execução | 🟢 Definido | Aplicar no fluxo e nos testes de estoque. |
| Separação entre status operacional e financeiro | 🟢 Definido | Manter estados independentes. |
| Multi-tenancy | 🟢 Definido | Garantir isolamento em todas as operações. |
| Integração externa | 🟡 Pendente | Formalizar a API escolhida e registrar a decisão em ADR. |
| Estratégia de bancos por microsserviço | 🟡 Detalhamento técnico | Definir a configuração de bancos lógicos e infraestrutura. |
| Matriz detalhada de permissões por perfil | 🟡 Pendente | Validar as permissões com o grupo. |
| Notificações avançadas | 🔵 Pós-MVP | Avaliar em etapa posterior. |
| Relatórios gerenciais avançados | 🔵 Pós-MVP | Avaliar em etapa posterior. |

---

## 12. Referências e Alinhamento Documental

Este documento deve permanecer alinhado aos seguintes artefatos do projeto:

- `backlog-requisitos.md`
- `historias-usuario.md`
- `criterios-aceite.md`
- `regras-negocio.md`
- `fluxos.md`
- `glossario.md`
- `03-diagrama-fluxo-de-dados.md`
- `04-modelo-entidade-relacionamento.md`
- `05-arquitetura-nuvem.md`
- `06-c4-model.md`

As alterações no escopo do MVP devem ser refletidas nos documentos relacionados, evitando divergências entre requisitos, regras, arquitetura e implementação.

---

**Última atualização:** 16/11/2026

**Responsável:** Eloísa Brandão — Product Owner

**Grupo:** GarageOps
