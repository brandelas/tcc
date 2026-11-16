# Backlog de Requisitos — OficinaPro

> **Documento de trabalho da análise de requisitos do OficinaPro — Grupo GarageOps.**
>
> Este documento é evolutivo e consolida os requisitos funcionais e não funcionais, as regras de negócio, as decisões de escopo e as diretrizes técnicas do OficinaPro.
>
> As definições devem permanecer alinhadas às demais documentações do projeto, especialmente às Histórias de Usuário, aos Fluxos, às Regras de Negócio, aos Critérios de Aceite, ao Glossário, ao MVP e aos documentos de arquitetura.

---

## 1. Objetivo

O **OficinaPro** é um sistema SaaS de gestão operacional destinado a oficinas mecânicas de pequeno e médio porte.

Seu objetivo é organizar o fluxo operacional da oficina, desde o cadastro de clientes e veículos até a abertura, execução e conclusão da ordem de serviço (OS), incluindo diagnóstico, orçamento, aprovação do cliente, reserva e utilização de peças, controle financeiro e histórico de manutenção.

Como plataforma SaaS multiempresa, o sistema deverá permitir que diferentes oficinas utilizem a mesma aplicação, mantendo o isolamento lógico e a confidencialidade dos dados de cada contratante.

Este documento registra:

- requisitos funcionais e não funcionais;
- regras de negócio;
- decisões de escopo;
- diretrizes técnicas;
- funcionalidades previstas para o MVP;
- funcionalidades planejadas para versões futuras;
- pontos que ainda dependem de definição ou validação.

---

## 2. Legenda de status

| Status | Significado |
|---|---|
| 🟢 **Definido** | Decisão estabelecida como referência do projeto. |
| 🟡 **Em discussão** | Proposta que ainda precisa de discussão ou confirmação do grupo. |
| 🔴 **Pendente** | Informação ou decisão ainda não definida. |
| 🔵 **Validar com o professor** | Ponto que depende de orientação acadêmica. |
| ⚪ **Pós-MVP** | Funcionalidade planejada para evolução futura, sem bloquear o MVP. |

---

## 3. Escopo inicial do MVP

O MVP deverá entregar um fluxo operacional integrado, contemplando:

```text
Oficina (Tenant)
  ↓
Usuário autenticado e autorizado
  ↓
Cliente
  ↓
Veículo
  ↓
Ordem de Serviço (ABERTA)
  ↓
Diagnóstico (EM_DIAGNOSTICO)
  ↓
Orçamento (AGUARDANDO_APROVACAO)
  ↓
Decisão do cliente
  ├── RECUSADO
  │     ├── Encerramento da OS
  │     └── Elaboração de orçamento revisado
  │             ↓
  │       Nova apreciação do cliente
  │
  └── ACEITO
          ↓
      Solicitação de reserva de peças
          ↓
      Inventory Service valida disponibilidade
          ├── Reserva recusada
          │      ↓
          │   Orçamento permanece sem aprovação definitiva
          │   e execução bloqueada
          │
          └── Reserva confirmada
                 ↓
             Orçamento aprovado definitivamente
                 ↓
             Execução do serviço
                 ↓
             Utilização efetiva das peças
                 ↓
             Conversão das reservas em saídas definitivas
                 ↓
             Conclusão operacional da OS
                 ↓
             Registro e atualização do histórico
                 ↓
             Controle financeiro independente
                 ↓
             Quitação financeira (quando realizada)
```

**Regra fundamental do estoque:** a aprovação definitiva do orçamento depende da confirmação da reserva integral das peças necessárias. A reserva reduz a quantidade disponível para novas operações, mas não representa consumo físico ou baixa definitiva.

A baixa definitiva ocorre somente quando a peça é efetivamente utilizada. Peças reservadas e não utilizadas devem ter sua reserva liberada, desde que permaneçam em condições de reutilização.

**Status:** 🟢 Definido como referência funcional.

---

## 4. Gestão de oficinas e isolamento multi-tenant

### 4.1 Conceito

Cada oficina contratante deverá ser representada por um identificador único de tenant.

Os dados operacionais de cada oficina deverão estar associados ao respectivo tenant, impedindo que usuários de uma oficina consultem ou alterem informações pertencentes a outra.

### 4.2 Regras adotadas

- **RN-TEN-001:** Cada oficina contratante deve possuir um identificador único de tenant.
- **RN-TEN-002:** Todo registro operacional pertencente a uma oficina deve estar associado ao seu respectivo `tenant_id`.
- **RN-TEN-003:** O sistema deve aplicar o isolamento dos dados no backend, utilizando o contexto do tenant associado à autenticação e à autorização do usuário.
- **RN-TEN-004:** O sistema não deve confiar exclusivamente em um `tenant_id` informado pelo frontend ou enviado livremente em uma requisição.
- **RN-TEN-005:** Consultas, inclusões, alterações, exclusões lógicas e operações de estoque devem respeitar o tenant autenticado.
- **RN-TEN-006:** Referências entre registros de diferentes serviços devem validar a correspondência do tenant antes de permitir uma operação.
- **RN-TEN-007:** CPF e placa devem possuir unicidade dentro do tenant, permitindo que diferentes oficinas mantenham cadastros independentes de clientes e veículos.

**Status:** 🟢 Diretriz funcional e de segurança do SaaS.

---

## 5. Gestão de clientes

### 5.1 Dados previstos

- Nome completo.
- CPF — obrigatório, válido e único por tenant.
- Telefone.
- E-mail.
- Endereço.

### 5.2 Regras de negócio adotadas

- **RN-CLI-001:** Todo cliente deve possuir CPF válido e obrigatório, único dentro do tenant ao qual está vinculado.
- **RN-CLI-002:** O sistema deve impedir cadastros duplicados de clientes com o mesmo CPF dentro da mesma oficina.
- **RN-CLI-003:** Clientes com ordens de serviço ou histórico associado não devem ser excluídos fisicamente; quando necessário, devem ser inativados.
- Os dados cadastrais devem permanecer vinculados à oficina responsável pelo cadastro.
- A inativação não deve apagar o histórico de atendimentos.

**Status:** 🟢 Definido.

---

## 6. Gestão de veículos

### 6.1 Dados previstos

- Placa.
- Marca.
- Modelo.
- Ano.
- Cor.
- Quilometragem.

### 6.2 Regras de negócio adotadas

- **RN-VEI-001:** Todo veículo deve estar obrigatoriamente associado a um cliente cadastrado no mesmo tenant.
- **RN-VEI-002:** A placa deve ser obrigatória e única dentro do tenant.
- **RN-VEI-003:** O histórico de manutenção deve permanecer associado ao veículo, mesmo após o encerramento de uma OS ou a transferência de propriedade.
- **RN-VEI-004:** Em caso de transferência de propriedade, o vínculo com o proprietário atual poderá ser atualizado, preservando o histórico dos proprietários e atendimentos anteriores.

**Status:** 🟢 Definido.

---

## 7. Ordem de Serviço

### 7.1 Informações previstas

- Identificador único da OS.
- Cliente e veículo associados.
- Data de abertura.
- Quilometragem registrada na abertura.
- Problema relatado pelo cliente.
- Diagnóstico técnico.
- Serviços previstos.
- Orçamentos e respectivas versões.
- Peças vinculadas.
- Histórico de alterações e decisões.
- Situação operacional.
- Situação financeira relacionada.

### 7.2 Estados operacionais de referência

A OS deverá possuir uma esteira de estados operacionais independente da situação financeira:

```text
ABERTA
  ↓
EM_DIAGNOSTICO
  ↓
AGUARDANDO_APROVACAO
  ↓
AGUARDANDO_RESERVA
  ↓
EM_EXECUCAO
  ↓
CONCLUIDA
```

A OS também poderá assumir `CANCELADA`, conforme as regras aplicáveis.

Os estados `APROVADA` e `RECUSADA` representam decisões do orçamento, não estados operacionais da OS.

A situação financeira deverá ser controlada separadamente pelo Billing Service.

### 7.3 Regras de negócio adotadas

- **RN-OS-001:** Cada OS deve possuir identificador único dentro do contexto da oficina.
- **RN-OS-002:** A OS deve estar vinculada a um cliente e a um veículo pertencentes ao mesmo tenant.
- **RN-OS-003:** A OS deve respeitar as transições operacionais permitidas.
- **RN-OS-004:** Uma OS com orçamento recusado poderá receber um novo orçamento revisado ou ser encerrada, preservando o histórico anterior.
- **RN-OS-005:** A execução somente poderá começar após a aprovação definitiva do orçamento e a confirmação da reserva de estoque necessária.
- **RN-OS-006:** A conclusão operacional da OS não deve ser confundida com a quitação financeira. A situação financeira deverá ser registrada independentemente.
- A OS cancelada deverá preservar os registros de diagnóstico, orçamento, movimentações de estoque e decisões anteriores.

**Status:** 🟢 Definido como referência funcional.

---

## 8. Diagnóstico

### 8.1 Funcionalidades previstas

- Registrar o problema relatado pelo cliente.
- Registrar o diagnóstico técnico realizado pelo mecânico.
- Associar o diagnóstico diretamente à OS.
- Permitir consulta posterior dos dados registrados.

### 8.2 Regra de negócio

- **RN-DIA-001:** Todo diagnóstico técnico deve estar associado à OS correspondente e integrar seu histórico.

O registro deverá respeitar as permissões do usuário e a situação operacional da OS.

**Status:** 🟢 Definido.

---

## 9. Orçamento

### 9.1 Composição prevista

O orçamento deverá calcular o valor total considerando serviços, peças e descontos autorizados:

```text
Valor Total = (Serviços + Peças) - Descontos
```

### 9.2 Regras de negócio

- **RN-ORC-001:** O valor total deverá ser calculado automaticamente com base nos serviços, nas peças e nos descontos autorizados.
- **RN-ORC-002:** A execução dos serviços depende da aprovação definitiva do orçamento, que somente poderá ocorrer após a confirmação da reserva integral das peças necessárias.
- **RN-ORC-003:** Um orçamento recusado poderá ser encerrado ou revisado, preservando o histórico da versão anterior e da decisão do cliente.
- **RN-ORC-004:** A decisão de aprovação ou recusa deverá registrar o responsável ou a origem da decisão, a data, o horário e o resultado.
- **RN-ORC-005:** A aceitação do cliente não representa, isoladamente, aprovação definitiva no sistema. A aprovação definitiva depende do resultado positivo da reserva de estoque.

### 9.3 Revisão de orçamento

Quando houver alteração de serviços, peças, quantidades ou valores após uma recusa:

- deverá ser gerada uma nova versão do orçamento;
- a versão anterior deverá permanecer registrada;
- a nova versão deverá estar vinculada à mesma OS;
- a nova proposta deverá ser submetida à apreciação do cliente;
- uma nova aceitação deverá iniciar uma nova tentativa de reserva, quando aplicável.

**Status:** 🟢 Definido.

---

## 10. Estoque

### 10.1 Funcionalidades previstas

- Cadastro de peças.
- Consulta de peças e quantidades.
- Registro de entradas.
- Controle de quantidade física, reservada e disponível.
- Solicitação e confirmação de reservas.
- Registro de consumo e saídas definitivas.
- Liberação de reservas não utilizadas.
- Registro de estornos, perdas e descartes.
- Consulta do histórico de movimentações.

### 10.2 Conceitos de estoque

| Conceito | Significado |
|---|---|
| Estoque físico | Quantidade de peças efetivamente existente na oficina. |
| Estoque reservado | Quantidade comprometida com OS que possuem reserva confirmada, mas ainda não foi utilizada. |
| Estoque disponível | Quantidade que pode ser comprometida em novas operações. |
| Consumo | Utilização efetiva da peça, convertendo a reserva em saída definitiva. |
| Liberação de reserva | Retorno de uma quantidade reservada à disponibilidade, sem movimentação de entrada física. |
| Estorno | Movimentação corretiva que reverte uma saída definitiva, quando o item puder retornar ao estoque comercializável. |
| Perda operacional | Registro de peça que não pode retornar ao estoque comercializável. |

A quantidade disponível deverá ser calculada considerando a quantidade física e a quantidade reservada:

```text
Estoque Disponível = Estoque Físico - Estoque Reservado
```

### 10.3 Regras de negócio

- **RN-EST-001:** Toda peça utilizada em uma OS deve estar previamente cadastrada no Inventory Service.
- **RN-EST-002:** O sistema deve impedir reservas ou saídas definitivas superiores à quantidade disponível.
- **RN-EST-003:** Após a aceitação do orçamento pelo cliente, o sistema deverá solicitar a reserva integral das quantidades previstas. A aprovação definitiva dependerá da confirmação da reserva pelo Inventory Service.
- **RN-EST-004:** As movimentações de estoque devem estar vinculadas ao tenant, à OS e ao orçamento correspondentes, impedindo duplicidade de operações.
- **RN-EST-005:** Em caso de cancelamento, as peças reservadas e não utilizadas poderão ter suas reservas liberadas, desde que permaneçam em condições de reutilização.
- **RN-EST-006:** Peças efetivamente instaladas ou consumidas não poderão retornar ao estoque comercializável por meio de liberação de reserva. Devem permanecer registradas como consumo ou saída definitiva.
- **RN-EST-007:** As movimentações originais, reservas, consumos, liberações, estornos e perdas devem ser preservados para garantir rastreabilidade.
- **RN-EST-008:** A reserva deverá ser integral e atômica: se qualquer peça estiver indisponível, nenhuma quantidade da solicitação poderá permanecer reservada parcialmente.
- **RN-EST-009:** O Inventory Service deverá utilizar mecanismos de controle de concorrência para impedir que solicitações simultâneas comprometam a mesma quantidade disponível.
- **RN-EST-010:** Cada solicitação de reserva deverá possuir identificador único e mecanismo de idempotência, impedindo que reprocessamentos gerem reservas ou movimentações duplicadas.
- **RN-EST-011:** A confirmação de uso de uma peça deverá converter sua quantidade reservada em saída definitiva, atualizando o estoque físico e o estoque reservado de forma consistente.

### 10.4 Fluxo de reserva

```text
Cliente aceita o orçamento
   ↓
Billing Service registra a aceitação
   ↓
Solicita reserva ao Inventory Service
   ↓
Inventory Service valida tenant,
disponibilidade e concorrência
   ↓
Todas as peças disponíveis?
   ├── Não
   │    ↓
   │  Não efetivar reserva parcial
   │    ↓
   │  Registrar recusa da solicitação
   │    ↓
   │  Manter orçamento sem aprovação definitiva
   │  e bloquear execução
   │
   └── Sim
        ↓
      Reservar todas as quantidades
        ↓
      Confirmar resultado
        ↓
      Billing Service aprova definitivamente
        ↓
      Work Order Service libera execução
```

### 10.5 Tratamento de indisponibilidade

Quando a reserva for recusada, a execução deverá permanecer bloqueada.

A oficina poderá:

- aguardar reposição do estoque;
- alterar o orçamento e as quantidades;
- substituir peças, quando tecnicamente adequado;
- solicitar nova autorização do cliente para a proposta alterada;
- cancelar o atendimento.

Uma nova tentativa deverá utilizar uma solicitação identificável, sem duplicar reservas anteriores.

### 10.6 Utilização e devolução

Durante a execução:

- cada peça ou unidade deverá possuir rastreabilidade de sua condição;
- peças efetivamente utilizadas deverão ser convertidas em saídas definitivas;
- peças reservadas e não utilizadas deverão ter suas reservas liberadas;
- peças danificadas ou sem condição de reutilização deverão gerar registro de perda ou descarte;
- nenhuma operação poderá estornar ou liberar a mesma quantidade mais de uma vez.

**Status:** 🟢 Definido como referência funcional.

---

## 11. Financeiro

### 11.1 Funcionalidades previstas

- Cálculo do valor total da OS.
- Registro de pagamentos associados à OS.
- Consulta de pagamentos.
- Controle do saldo pendente.
- Atualização da situação financeira.

### 11.2 Regras de negócio

- **RN-FIN-001:** Todo pagamento deve estar associado a uma OS válida e ao tenant correspondente.
- **RN-FIN-002:** A situação financeira deverá ser controlada independentemente do estado operacional da OS.
- O sistema deverá distinguir pagamentos pendentes, parciais e quitados.
- A quitação deverá ocorrer quando o total dos pagamentos válidos corresponder ao valor devido.
- Registros de pagamento deverão preservar valor, data, forma de pagamento e histórico de alterações.
- O cancelamento ou estorno financeiro deverá possuir registro próprio e rastreável.

### 11.3 Separação entre financeiro da oficina e assinatura SaaS

Os pagamentos realizados pelos clientes da oficina referem-se às ordens de serviço e pertencem ao domínio do Billing Service.

A cobrança da assinatura do OficinaPro pela utilização da plataforma é uma operação comercial distinta e não deverá ser confundida com os pagamentos das OS.

**Status:** 🟢 Definido como diretriz de domínio.

---

## 12. Histórico de manutenção

### 12.1 Informações previstas

O histórico do veículo deverá consolidar:

- identificadores das OS;
- datas dos atendimentos;
- diagnósticos e laudos;
- orçamentos e suas versões;
- decisões do cliente;
- serviços realizados;
- peças efetivamente utilizadas;
- movimentações relevantes de estoque;
- valores e informações de pagamento;
- estados operacionais e financeiros.

### 12.2 Regra de negócio

- **RN-HIS-001:** Os registros históricos não devem ser apagados ou corrompidos por alterações cadastrais secundárias, garantindo rastreabilidade e preservação do histórico do veículo.

O histórico deverá respeitar o tenant responsável pelo registro e as permissões do usuário autenticado.

**Status:** 🟢 Definido.

---

## 13. Usuários, autenticação e permissões

### 13.1 Perfis previstos no MVP

- Atendente.
- Mecânico.
- Estoque.
- Financeiro.

Não está previsto um perfil operacional genérico denominado Administrador da Oficina. As permissões deverão ser distribuídas de acordo com as responsabilidades dos perfis definidos.

### 13.2 Regras de acesso

- O sistema deverá autenticar os usuários antes de permitir acesso às funcionalidades operacionais.
- As permissões deverão ser verificadas no backend.
- O acesso deverá considerar o perfil e o tenant ao qual o usuário está vinculado.
- Usuários não autorizados não poderão executar ações restritas.
- As senhas deverão ser armazenadas utilizando algoritmo de hash apropriado para senhas.
- Credenciais, tokens e informações sensíveis não deverão ser registrados em logs.
- O sistema deverá permitir identificar o usuário responsável por operações relevantes.

**Status:** 🟢 Definido.

---

## 14. Requisitos não funcionais

### 14.1 Segurança

- Armazenamento de senhas com hash seguro e mecanismo adequado de proteção.
- Controle de acesso baseado em permissões (RBAC).
- Isolamento dos dados entre tenants.
- Validação de entradas e proteção contra operações não autorizadas.
- Proteção de informações sensíveis.
- Uso de variáveis de ambiente ou mecanismos seguros de gerenciamento de segredos.
- Registro de operações relevantes para auditoria, sem exposição de credenciais.

### 14.2 Integridade e confiabilidade

- Transações locais consistentes em cada microsserviço.
- Controle de concorrência nas operações de reserva de estoque.
- Idempotência nas solicitações e no consumo de eventos.
- Uso de Transactional Outbox para assegurar a publicação confiável de eventos relacionados a alterações persistidas.
- Uso de Inbox ou mecanismo equivalente para evitar processamento duplicado.
- Tratamento de falhas, retentativas e mensagens não processadas.
- Ausência de dependências por chaves estrangeiras entre bancos de microsserviços distintos.

### 14.3 Qualidade e DevOps

- Revisão de código por Pull Requests.
- Testes automatizados.
- Cobertura mínima de testes de 60% sobre o código-fonte total do projeto, considerando globalmente os microsserviços, conforme o critério acadêmico.
- Versionamento de banco de dados via migrations.
- Pipeline de CI/CD configurado no GitHub Actions.
- Infraestrutura como Código utilizando Terraform.
- Implantação em ambiente de nuvem, conforme os requisitos da disciplina.
- Monitoramento por Prometheus e Grafana.
- Logs e indicadores que permitam acompanhar a saúde dos serviços.

### 14.4 Disponibilidade e evolução

- Separação de responsabilidades por domínio.
- Comunicação síncrona por APIs REST quando houver necessidade de resposta imediata.
- Comunicação assíncrona por RabbitMQ para eventos e processos desacoplados.
- Possibilidade de evolução independente dos microsserviços.
- Tratamento de indisponibilidade de dependências externas sem corromper os dados já persistidos.

**Status:** 🟢 Diretrizes técnicas do projeto e requisitos acadêmicos.

---

## 15. Funcionalidades fora do escopo inicial do MVP

As funcionalidades abaixo poderão ser desenvolvidas em versões futuras:

- Notificações avançadas por SMS e WhatsApp.
- Portal completo do cliente.
- Relatórios gerenciais e dashboards analíticos avançados.
- Integrações com gateways de pagamento reais.
- Gestão avançada de assinaturas e planos comerciais.
- Automação de cobrança recorrente.
- Mecanismos avançados de escalabilidade e recuperação.
- Aplicativo móvel dedicado.

**Observação:** o fato de uma funcionalidade estar fora do MVP não elimina a necessidade de considerar sua compatibilidade com a arquitetura SaaS.

**Status:** ⚪ Pós-MVP.

---

## 16. Arquitetura e decisões técnicas

### 16.1 Tecnologias previstas

- PostgreSQL como banco de dados relacional.
- Docker e Docker Compose para ambiente local.
- RabbitMQ para mensageria assíncrona.
- NestJS e TypeScript para os serviços de backend.
- React, TypeScript e Vite para o frontend.
- GitHub Actions para CI/CD.
- Terraform para infraestrutura como código.
- AWS como plataforma de hospedagem.

### 16.2 Microsserviços de referência do MVP

O escopo arquitetural funcional do MVP deverá considerar quatro serviços principais:

| Serviço | Responsabilidade |
|---|---|
| Auth Service | Autenticação, usuários, tenants, perfis e permissões. |
| Work Order Service | Clientes, veículos, histórico, ordens de serviço e diagnóstico. |
| Inventory Service | Cadastro de peças, estoque, reservas, consumo e movimentações. |
| Billing Service | Orçamentos, decisões, pagamentos e situação financeira. |

O README do projeto também apresenta Notification Service, Reporting Service e Customer Vehicle Service como serviços previstos na evolução da arquitetura.

Para evitar divergência documental, esses serviços adicionais deverão ser identificados como decomposição ou evolução futura, caso não façam parte da implantação dos quatro serviços principais do MVP.

### 16.3 Estratégia de dados

- Cada microsserviço deverá ser responsável pelos próprios dados.
- Será adotada a diretriz Database-per-Service.
- Os bancos poderão compartilhar a mesma infraestrutura física no MVP, mantendo separação lógica por serviço.
- Não deverão existir chaves estrangeiras físicas entre bancos de microsserviços diferentes.
- Referências entre serviços deverão ser validadas por contratos de API ou eventos.
- Os registros operacionais deverão possuir `tenant_id`.

### 16.4 Comunicação e eventos

RabbitMQ será utilizado para comunicação assíncrona entre serviços.

O fluxo de reserva deverá considerar, no mínimo, os eventos:

- `RESERVA_SOLICITADA`;
- `RESERVA_CONFIRMADA`;
- `RESERVA_RECUSADA`;
- `ORCAMENTO_APROVADO`;
- eventos de consumo, liberação ou estorno de estoque, conforme os contratos definidos.

Os eventos deverão conter identificadores de correlação e o contexto do tenant, além dos dados necessários ao processamento.

### 16.5 Confiabilidade

As operações que alteram o estado do banco e precisam publicar eventos deverão utilizar o padrão Transactional Outbox.

Os consumidores deverão possuir mecanismos de idempotência, tratamento de duplicidade e recuperação de falhas.

O processamento de reserva deverá ocorrer em uma transação local no Inventory Service, sem depender de transações distribuídas entre serviços.

**Status:** 🟢 Diretrizes técnicas de referência do MVP.

---

## 17. Integração com API externa

A disciplina exige uma integração com serviço externo relevante ao negócio.

A integração deverá:

- possuir finalidade funcional claramente documentada;
- ser consumida pelo backend, quando envolver credenciais ou regras de negócio;
- possuir tratamento de indisponibilidade e erros;
- validar os dados retornados antes de persistir informações;
- evitar dependência que impeça o funcionamento das funcionalidades essenciais.

**Definição da API e funcionalidade correspondente:** 🔴 Pendente de formalização.

A escolha da API deverá considerar utilidade real para a oficina e viabilidade de implementação no MVP. Não deverá ser registrada como concluída antes de sua definição e integração comprovada.

---

## 18. Dúvidas e pontos para validação acadêmica

Os pontos abaixo deverão ser levados ao professor quando não estiverem esclarecidos pelos documentos oficiais da disciplina:

- [ ] A organização dos quatro serviços principais do MVP e a evolução futura dos demais serviços está adequada?
- [ ] A integração externa escolhida atende ao critério acadêmico de relevância?
- [ ] A estratégia de demonstração da cobertura global de testes está adequada ao critério de 60%?
- [ ] A documentação de arquitetura e os diagramas atendem ao nível de detalhamento esperado?

**Status:** 🔵 Validar com o professor, quando necessário.

---

## 19. Backlog de decisões

| ID | Decisão | Status | Observação |
|---|---|---|---|
| DEC-001 | Obrigatoriedade e unicidade do CPF por oficina. | 🟢 | Definido. |
| DEC-002 | Inativação lógica de clientes com histórico. | 🟢 | Definido. |
| DEC-003 | Associação do histórico de manutenção ao veículo. | 🟢 | Definido. |
| DEC-004 | Placa única por oficina para identificação do veículo. | 🟢 | Definido. |
| DEC-005 | Tratamento de orçamento recusado, com possibilidade de revisão ou encerramento. | 🟢 | Definido. |
| DEC-006 | Reserva integral após aceitação do cliente, com aprovação definitiva condicionada à confirmação da reserva. | 🟢 | Referência funcional validada. |
| DEC-007 | Liberação de reservas de peças não utilizadas, quando estiverem em condições de reutilização. | 🟢 | Definido. |
| DEC-008 | Registro de consumo, perda ou descarte de peças efetivamente utilizadas ou sem condição de reutilização. | 🟢 | Definido. |
| DEC-009 | Perfis de acesso do MVP: Atendente, Mecânico, Estoque e Financeiro. | 🟢 | Definido. |
| DEC-010 | Isolamento lógico dos dados por tenant. | 🟢 | Diretriz de segurança do SaaS. |
| DEC-011 | Separação entre estado operacional da OS e situação financeira. | 🟢 | Referência de domínio. |
| DEC-012 | Quatro microsserviços principais no escopo arquitetural do MVP. | 🟢 | Os serviços adicionais permanecem como evolução prevista. |

---

## 20. Histórico de alterações deste documento

| Data | Alteração | Responsável |
|---|---|---|
| 26/09/2026 | Criação inicial do backlog de requisitos. | Eloísa Brandão |
| 27/10/2026 | Atualização do escopo do MVP e consolidação de regras de negócio e decisões técnicas. | Eloísa Brandão |
| 12/11/2026 | Revisão do fluxo operacional, padronização dos perfis e detalhamento do estoque. | Eloísa Brandão |
| 16/11/2026 | Revisão da reserva integral de estoque, separação entre consumo e reserva, estados operacionais e financeiros, isolamento multi-tenant e diretrizes de confiabilidade do SaaS. | Eloísa Brandão |

---

## Observação final

Este documento é a base viva de requisitos e decisões do OficinaPro. Ele não substitui os entregáveis acadêmicos formais exigidos pela disciplina.

Os requisitos aqui registrados deverão ser rastreáveis nas Histórias de Usuário, nos Fluxos, nas Regras de Negócio, nos Critérios de Aceite, no Glossário, no MVP e nos documentos de arquitetura.

A documentação deverá distinguir claramente:

- decisões funcionais adotadas;
- diretrizes técnicas;
- requisitos acadêmicos;
- funcionalidades do MVP;
- funcionalidades futuras;
- integrações ainda não definidas;
- implementação efetivamente comprovada.

A existência de uma diretriz neste documento não significa que sua implementação já tenha sido concluída ou validada por testes.
