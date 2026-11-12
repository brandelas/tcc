# Backlog de Requisitos — OficinaPro

> **Documento de trabalho da análise de requisitos do OficinaPro — Grupo GarageOps.**
>
> Este documento é evolutivo e deve ser atualizado à medida que o projeto avança, novas decisões são tomadas pelo grupo e pontos relevantes são validados com o professor orientador.

---

## 1. Objetivo

O **OficinaPro** é um sistema de gestão operacional para oficinas mecânicas de pequeno e médio porte.

Seu objetivo central é organizar o fluxo operacional da oficina, desde o cadastro de clientes e veículos até a abertura e a conclusão da ordem de serviço (OS), incluindo diagnóstico, orçamento, controle de peças, fechamento financeiro e histórico de manutenção.

Este documento registra:

- requisitos funcionais e não funcionais;
- regras de negócio;
- decisões de escopo;
- pontos em discussão;
- dúvidas para validação com o professor;
- funcionalidades planejadas para versões futuras.

---

## 2. Legenda de status

| Status | Significado |
| --- | --- |
| 🟢 **Definido** | Decisão estabelecida pelo grupo. |
| 🟡 **Em discussão** | Ponto que precisa ser discutido pelo grupo. |
| 🔴 **Pendente** | Informação ou decisão ainda não definida. |
| 🔵 **Validar com o professor** | Ponto a ser discutido durante a orientação. |
| ⚪ **Pós-MVP** | Funcionalidade planejada que não deve bloquear o MVP. |

---

## 3. Escopo inicial do MVP

O fluxo operacional principal definido atualmente é:

```text
Cliente
  ↓
Veículo
  ↓
Ordem de Serviço (ABERTA)
  ↓
Diagnóstico (EM_DIAGNOSTICO)
  ↓
Orçamento (AGUARDANDO_APROVACAO)
  ├── Recusado (RECUSADA)
  │     ├── Encerramento definitivo da OS
  │     └── Elaboração de orçamento revisado
  │             ↓
  │       AGUARDANDO_APROVACAO
  │
  └── Aprovado (APROVADA)
          ↓
      Baixa automática das peças no saldo disponível
          ↓
      Execução do serviço (EM_EXECUCAO)
          ↓
      Aguardando pagamento (AGUARDANDO_PAGAMENTO)
          ↓
      Quitação financeira
          ↓
      OS concluída (CONCLUIDA)
          ↓
      Histórico de manutenção do veículo
```

**Observação sobre o estoque:** a baixa ocorre no momento da aprovação do orçamento, retirando as quantidades correspondentes do saldo disponível. Essa movimentação não significa, necessariamente, que as peças já foram fisicamente instaladas ou consumidas.

Em caso de cancelamento posterior à aprovação, o tratamento depende da condição física das peças:

- peças ainda não instaladas ou consumidas podem retornar ao estoque disponível mediante estorno;
- peças já instaladas ou consumidas não podem retornar ao estoque comercializável e devem ser registradas como perda operacional ou descarte.

**Status:** 🟢 Definido para o MVP.

---

## 4. Gestão de clientes

### 4.1 Dados previstos

- [x] Nome completo.
- [x] CPF — obrigatório e único.
- [x] Telefone.
- [x] E-mail.
- [x] Endereço.

### 4.2 Regras de negócio adotadas

- **RN-CLI-001:** Todo cliente deve possuir CPF válido e único no sistema, utilizado como identificador para impedir cadastros duplicados.
- Clientes com histórico associado não devem ser excluídos fisicamente, mas podem ser inativados, preservando seus registros e vínculos históricos.

**Status:** 🟢 Definido.

---

## 5. Gestão de veículos

### 5.1 Dados previstos

- [x] Placa — identificador único.
- [x] Marca.
- [x] Modelo.
- [x] Ano.
- [x] Cor.
- [x] Quilometragem.

### 5.2 Regras de negócio adotadas

- **RN-VEI-001:** Todo veículo deve estar obrigatoriamente associado a um cliente cadastrado.
- **RN-VEI-002:** A placa deve ser única no sistema e utilizada como identificador do veículo.
- O histórico de manutenção permanece associado ao veículo, mesmo em caso de transferência de propriedade.

**Status:** 🟢 Definido.

---

## 6. Ordem de Serviço

### 6.1 Informações previstas

- Número da ordem de serviço (identificador único).
- Cliente e veículo associados.
- Data de abertura e quilometragem.
- Problema relatado pelo cliente.
- Diagnóstico técnico.
- Serviços, peças e orçamento.
- Status atual da OS.
- Histórico de alterações e decisões.

### 6.2 Esteira de status oficial do MVP

```text
ABERTA
  ↓
EM_DIAGNOSTICO
  ↓
AGUARDANDO_APROVACAO
  ├── RECUSADA
  │     ├── Encerramento definitivo
  │     └── Revisão do orçamento
  │             ↓
  │       AGUARDANDO_APROVACAO
  │
  └── APROVADA
          ↓
      EM_EXECUCAO
          ↓
      AGUARDANDO_PAGAMENTO
          ↓
      CONCLUIDA
```

A OS também poderá assumir o status `CANCELADA`, conforme as regras de cancelamento aplicáveis.

### 6.3 Regras de negócio adotadas

- **RN-OS-001:** A OS deve possuir identificador único e estar vinculada a um cliente e a um veículo cadastrados.
- **RN-OS-002:** A OS deve respeitar as transições de status permitidas pelo fluxo operacional definido para o MVP.
- **RN-OS-003:** Uma OS com orçamento recusado poderá ser encerrada definitivamente ou receber um novo orçamento revisado, mantendo o histórico da tratativa anterior.
- **RN-OS-004:** A OS poderá retornar para `AGUARDANDO_APROVACAO` quando um orçamento revisado for encaminhado ao cliente.
- **RN-OS-005:** A OS somente poderá avançar para a execução após a aprovação formal do orçamento.

**Status:** 🟢 Definido.

---

## 7. Diagnóstico

### 7.1 Funcionalidades previstas

- [x] Registrar o problema relatado pelo cliente.
- [x] Registrar o diagnóstico técnico realizado pelo mecânico.
- [x] Associar o diagnóstico diretamente à OS.
- [x] Permitir a consulta posterior das informações registradas.

### 7.2 Regra de negócio adotada

- **RN-DIA-001:** O diagnóstico deve estar associado à OS correspondente e integrar o histórico do atendimento.

O registro do diagnóstico deve respeitar o fluxo de status da OS, permitindo sua identificação na etapa `EM_DIAGNOSTICO`.

**Status:** 🟢 Definido.

---

## 8. Orçamento

### 8.1 Composição prevista

O orçamento calcula o valor total da OS considerando serviços, peças e descontos:

```text
(Serviços + Peças) - Descontos = Valor total
```

### 8.2 Regras de negócio adotadas

- **RN-ORC-001:** O valor total do orçamento deve ser calculado automaticamente com base nos serviços, nas peças e nos descontos aplicáveis.
- **RN-ORC-002:** A execução dos serviços e a baixa das peças no estoque dependem da aprovação formal do orçamento (`APROVADA`).
- **RN-ORC-003:** Quando um orçamento for recusado (`RECUSADA`), a OS poderá ser encerrada definitivamente ou receber um novo orçamento revisado para apreciação do cliente, sem perder o histórico da tratativa anterior.

### 8.3 Revisão de orçamento

Quando houver negociação ou alteração dos serviços e peças após uma recusa:

- um novo orçamento deverá ser gerado e vinculado à mesma OS;
- o orçamento anterior deverá permanecer registrado;
- a OS deverá retornar para `AGUARDANDO_APROVACAO`;
- a nova proposta deverá passar por uma nova decisão do cliente.

**Status:** 🟢 Definido.

---

## 9. Estoque

### 9.1 Funcionalidades previstas

- Cadastro de peças.
- Consulta de peças e saldo disponível.
- Registro de entradas e saídas.
- Verificação de disponibilidade antes da aprovação do orçamento.
- Registro das movimentações vinculadas às respectivas OS e orçamentos.
- Controle de estornos e perdas operacionais.

### 9.2 Regras de negócio adotadas

- **RN-EST-001:** As peças devem possuir cadastro e quantidade controlada no inventário.
- **RN-EST-002:** O sistema deve bloquear a aprovação do orçamento quando a quantidade solicitada de uma peça for superior ao saldo disponível.
- **RN-EST-003:** A aprovação do orçamento (`APROVADA`) deve disparar automaticamente a baixa das quantidades previstas no orçamento no saldo disponível do estoque.
- **RN-EST-004:** As movimentações de saída devem estar vinculadas à OS e ao orçamento correspondente, impedindo baixas duplicadas para o mesmo item e orçamento.
- **RN-EST-005:** Em caso de cancelamento de uma OS após a aprovação do orçamento, peças ainda não instaladas ou consumidas poderão retornar ao estoque disponível mediante estorno.
- **RN-EST-006:** Peças já instaladas ou consumidas não poderão retornar ao estoque comercializável. Nesses casos, deverão ser registradas como perda operacional ou descarte.
- **RN-EST-007:** As movimentações originais de saída, os estornos e os registros de perda devem ser preservados para garantir a rastreabilidade do estoque.

### 9.3 Tratamento do estoque em caso de cancelamento

O sistema deverá diferenciar duas situações:

| Condição da peça | Tratamento |
| --- | --- |
| Baixada no momento da aprovação, mas ainda não instalada ou consumida | Estornar a movimentação e devolver a quantidade ao estoque disponível. |
| Já instalada ou consumida | Não devolver ao estoque comercializável; registrar como perda operacional ou descarte. |

A baixa automática na aprovação representa a retirada da quantidade do saldo disponível, mas não confirma, por si só, o consumo físico da peça.

**Status:** 🟢 Definido.

---

## 10. Financeiro

### 10.1 Funcionalidades previstas

- Cálculo do valor total da OS.
- Registro do pagamento associado à OS.
- Controle da quitação financeira.
- Atualização do status da OS após a confirmação do pagamento integral.

### 10.2 Regras de negócio adotadas

- **RN-FIN-001:** O valor total da OS deve considerar serviços, peças e descontos aplicáveis.
- **RN-FIN-002:** A OS somente poderá assumir o status `CONCLUIDA` após a confirmação da quitação financeira integral.

**Status:** 🟢 Definido.

---

## 11. Histórico de manutenção

### 11.1 Informações previstas

O histórico do veículo deverá consolidar as informações das ordens de serviço anteriores, incluindo:

- identificadores das OS;
- datas dos atendimentos;
- diagnósticos e laudos;
- orçamentos e decisões do cliente;
- serviços realizados;
- peças utilizadas;
- valores e informações de pagamento;
- status e encerramento das ordens de serviço.

O histórico deverá permanecer associado ao veículo e disponível para consulta, inclusive após o encerramento ou cancelamento de uma OS.

**Status:** 🟢 Definido.

---

## 12. Usuários, autenticação e permissões

### 12.1 Perfis adotados no MVP

- Atendente.
- Mecânico.
- Estoque.
- Financeiro.

### 12.2 Regras de acesso

- O sistema deverá autenticar os usuários cadastrados.
- O acesso às funcionalidades deverá respeitar as permissões atribuídas a cada perfil.
- Usuários não autorizados não poderão executar ações restritas.
- As credenciais deverão ser armazenadas utilizando hash seguro.

O controle de acesso será realizado por meio do Auth Service e de permissões baseadas em perfil (RBAC).

**Status:** 🟢 Definido.

---

## 13. Requisitos não funcionais

### 13.1 Segurança

- Armazenamento de senhas utilizando hash seguro.
- Controle de acesso baseado em perfis (RBAC).
- Validação de entradas e proteção de informações sensíveis.
- Tratamento adequado de erros, sem exposição de dados sensíveis.

### 13.2 Qualidade e DevOps

- Revisão de código por Pull Requests.
- Testes automatizados.
- Meta de cobertura de testes de pelo menos 60%, conforme o escopo de medição definido para o projeto.
- Versionamento de banco de dados via migrations.
- Pipeline de CI/CD configurado no GitHub Actions.
- Infraestrutura modelada via Terraform e hospedada na AWS.

**Status:** 🟢 Definido como diretriz técnica do projeto.

---

## 14. Regras de negócio consolidadas

| ID | Regra | Status |
| --- | --- | --- |
| RN-CLI-001 | Todo cliente deve possuir CPF válido e único no sistema. | 🟢 |
| RN-VEI-001 | Todo veículo deve estar associado a um cliente cadastrado. | 🟢 |
| RN-VEI-002 | A placa deve ser única no sistema. | 🟢 |
| RN-OS-001 | A OS deve possuir identificador único e estar vinculada a cliente e veículo. | 🟢 |
| RN-OS-002 | A OS deve respeitar as transições de status permitidas. | 🟢 |
| RN-OS-003 | Orçamentos recusados podem ser encerrados ou revisados, preservando o histórico. | 🟢 |
| RN-OS-004 | A OS pode retornar para `AGUARDANDO_APROVACAO` após a elaboração de orçamento revisado. | 🟢 |
| RN-OS-005 | A execução depende da aprovação formal do orçamento. | 🟢 |
| RN-DIA-001 | O diagnóstico deve estar associado à OS correspondente. | 🟢 |
| RN-ORC-001 | O valor total do orçamento deve ser calculado automaticamente. | 🟢 |
| RN-ORC-002 | A execução e a baixa de peças dependem da aprovação formal do orçamento. | 🟢 |
| RN-ORC-003 | A recusa permite encerramento ou revisão do orçamento, mantendo o histórico. | 🟢 |
| RN-EST-001 | As peças devem possuir cadastro e quantidade controlada no inventário. | 🟢 |
| RN-EST-002 | A aprovação deve ser bloqueada quando não houver quantidade suficiente em estoque. | 🟢 |
| RN-EST-003 | A baixa automática ocorre na aprovação do orçamento. | 🟢 |
| RN-EST-004 | As baixas devem ser vinculadas à OS e ao orçamento, sem duplicidade. | 🟢 |
| RN-EST-005 | Peças não instaladas ou consumidas podem retornar ao estoque mediante estorno. | 🟢 |
| RN-EST-006 | Peças instaladas ou consumidas não retornam ao estoque comercializável. | 🟢 |
| RN-EST-007 | As movimentações de estoque devem preservar a rastreabilidade. | 🟢 |
| RN-FIN-001 | O total da OS considera serviços, peças e descontos. | 🟢 |
| RN-FIN-002 | A OS somente será concluída após a quitação financeira integral. | 🟢 |

---

## 15. Funcionalidades fora do escopo inicial do MVP

As funcionalidades abaixo estão planejadas para versões futuras e não devem bloquear a entrega do MVP:

- Sistema avançado de notificações externas (SMS e WhatsApp).
- Relatórios gerenciais e dashboards analíticos avançados.
- Portal completo do cliente.
- Integração com gateways de pagamento reais, além de eventuais simulações locais.

**Status:** ⚪ Pós-MVP.

---

## 16. Arquitetura e decisões técnicas

As tecnologias e ferramentas previstas para o projeto são:

- PostgreSQL como banco de dados relacional.
- Docker Compose para o ambiente local de desenvolvimento.
- RabbitMQ para mensageria assíncrona entre microsserviços.
- Arquitetura de microsserviços com NestJS e TypeScript.
- Frontend em React com Vite.
- GitHub Actions para integração e entrega contínuas.
- Terraform para modelagem da infraestrutura.
- AWS como plataforma de hospedagem.

**Status:** 🟢 Definido como direcionamento técnico do projeto.

---

## 17. Dúvidas para os encontros com o professor

### Encontro 01 — Validação acadêmica e técnica

- [ ] O nível de microsserviços e mensageria adotado atende às expectativas da disciplina?
- [ ] A meta de cobertura de testes de pelo menos 60% está alinhada ao rigor acadêmico esperado?
- [ ] A divisão das funcionalidades entre os microsserviços está adequada ao escopo do MVP?

**Status:** 🔵 Validar com o professor.

---

## 18. Backlog de decisões

| ID | Decisão | Status | Responsável | Observação |
| --- | --- | --- | --- | --- |
| DEC-001 | Obrigatoriedade e unicidade do CPF. | 🟢 | Grupo | Definido. |
| DEC-002 | Inativação lógica de clientes com histórico. | 🟢 | Grupo | Definido. |
| DEC-003 | Associação do histórico de manutenção ao veículo. | 🟢 | Grupo | Definido. |
| DEC-004 | Placa única para identificação do veículo. | 🟢 | Grupo | Definido. |
| DEC-005 | Tratamento de orçamento recusado, com possibilidade de revisão ou encerramento. | 🟢 | Grupo | Definido. |
| DEC-006 | Baixa automática de estoque no momento da aprovação do orçamento. | 🟢 | Grupo | Definido. |
| DEC-007 | Estorno de peças não instaladas ou consumidas após cancelamento. | 🟢 | Grupo | Definido. |
| DEC-008 | Registro de perda ou descarte para peças já instaladas ou consumidas. | 🟢 | Grupo | Definido. |
| DEC-009 | Perfis de acesso do MVP: Atendente, Mecânico, Estoque e Financeiro. | 🟢 | Grupo | Definido. |

---

## 19. Histórico de alterações deste documento

| Data | Alteração | Responsável |
| --- | --- | --- |
| 26/09/2026 | Criação inicial do backlog de requisitos. | Eloísa Brandão |
| 27/10/2026 | Atualização do escopo do MVP e consolidação de regras de negócio e decisões técnicas. | Eloísa Brandão |
| 12/11/2026 | Revisão do fluxo operacional, padronização dos perfis, atualização das regras de orçamento e detalhamento da baixa, do estorno e do tratamento de peças consumidas ou instaladas. | Eloísa Brandão |

---

## Observação

Este documento não substitui, por si só, a documentação acadêmica oficial do TCC.

Seu objetivo é servir como **documento vivo de trabalho do desenvolvimento**, permitindo que o grupo acompanhe as decisões, as pendências e a evolução dos requisitos do OficinaPro.

As decisões registradas como definidas representam o entendimento consolidado no documento e devem permanecer alinhadas às demais documentações do projeto, especialmente às Regras de Negócio, às Histórias de Usuário e aos Critérios de Aceite.
