# Regras de Negócio — OficinaPro

> **Documento oficial de trabalho — Grupo GarageOps**
>
> Este documento consolida as regras de negócio que regem o comportamento, as validações e as restrições do sistema OficinaPro para o MVP do Projeto Experimental de Análise e Desenvolvimento de Sistemas — UniFAAT.
>
> As regras aqui descritas devem orientar a implementação dos serviços, os critérios de aceite e os testes automatizados.

---

## 1. Legenda de Status

| Status | Significado |
|---|---|
| 🟢 **Definida** | Regra adotada como diretriz do MVP. |
| 🟡 **Em discussão** | Proposta em análise pelo grupo. |
| 🔵 **Validar com professor** | Depende de orientação acadêmica. |
| ⚪ **Pós-MVP** | Regra ou funcionalidade prevista para evolução futura. |

---

## 2. Multi-tenancy e Isolamento de Oficinas

### RN-TEN-001 — Identificação da Oficina

Cada oficina cadastrada no OficinaPro deve possuir um identificador próprio (`tenant_id`), utilizado para identificar e separar os dados pertencentes a cada estabelecimento.

**Status:** 🟢 Definida

### RN-TEN-002 — Isolamento de Dados

Os dados operacionais de uma oficina devem ser acessíveis exclusivamente aos usuários autorizados daquela oficina. Uma oficina não pode consultar, alterar ou excluir dados pertencentes a outro tenant.

**Status:** 🟢 Definida

### RN-TEN-003 — Identificação do Tenant pelo Contexto Autenticado

O sistema deve identificar a oficina do usuário a partir do contexto autenticado e autorizado. O backend não deve confiar exclusivamente em um `tenant_id` enviado pelo frontend para determinar a qual oficina uma operação pertence.

**Status:** 🟢 Definida

### RN-TEN-004 — Escopo das Operações

Consultas, cadastros, alterações, exclusões lógicas, relatórios e movimentações devem respeitar o tenant ao qual os registros pertencem.

**Status:** 🟢 Definida

### RN-TEN-005 — Unicidade por Oficina

As regras de unicidade de dados operacionais, como CPF de cliente e placa de veículo, devem ser aplicadas dentro do tenant correspondente, e não globalmente entre todas as oficinas.

**Status:** 🟢 Definida

### RN-TEN-006 — Identificação do Tenant em Eventos

Eventos publicados entre microserviços devem carregar a identificação do tenant de origem, além dos identificadores necessários para rastrear a operação.

**Status:** 🟢 Definida

### RN-TEN-007 — Validação de Acesso entre Oficinas

O sistema deve validar se o usuário autenticado possui vínculo e permissão para operar na oficina associada ao recurso solicitado. A tentativa de acesso a dados de outro tenant deve ser negada.

**Status:** 🟢 Definida

---

## 3. Clientes

### RN-CLI-001 — Identificação por CPF

Todo cliente cadastrado deve possuir CPF válido e obrigatório. O CPF será utilizado como identificador de negócio para evitar cadastros duplicados dentro da mesma oficina.

**Status:** 🟢 Definida

### RN-CLI-002 — Prevenção de Duplicidade por Tenant

O sistema deve verificar se o CPF informado já está cadastrado na mesma oficina. Caso exista um cadastro correspondente, a criação de um novo registro com o mesmo CPF deve ser impedida.

Um mesmo CPF poderá existir em oficinas diferentes, pois os dados são isolados por tenant.

**Status:** 🟢 Definida

### RN-CLI-003 — Inativação de Cliente

Caso um cliente possua ordens de serviço ou histórico vinculado, ele não deve ser excluído fisicamente do banco de dados. Quando necessário, deverá ser realizada sua inativação lógica, preservando os registros históricos.

**Status:** 🟢 Definida

### RN-CLI-004 — Associação do Cliente à Oficina

Todo cliente deve estar associado a um tenant. Um usuário de uma oficina não poderá consultar ou modificar o cadastro de um cliente pertencente a outra oficina.

**Status:** 🟢 Definida

---

## 4. Veículos

### RN-VEI-001 — Associação Obrigatória

Todo veículo cadastrado deve estar obrigatoriamente vinculado a um cliente existente na mesma oficina.

**Status:** 🟢 Definida

### RN-VEI-002 — Placa como Identificador de Negócio

A placa do veículo é de preenchimento obrigatório e deve ser validada pelo sistema. Não poderá existir mais de um cadastro ativo com a mesma placa dentro da mesma oficina.

Uma mesma placa poderá estar cadastrada em oficinas diferentes.

**Status:** 🟢 Definida

### RN-VEI-003 — Preservação do Histórico

As informações de manutenção e as ordens de serviço vinculadas a um veículo devem permanecer associadas ao veículo, mesmo quando ocorrerem alterações cadastrais ou mudanças de proprietário.

**Status:** 🟢 Definida

### RN-VEI-004 — Transferência de Propriedade

Caso um veículo mude de proprietário, o vínculo com o cliente atual poderá ser atualizado para o novo cliente, desde que este esteja cadastrado na mesma oficina.

A alteração não deve apagar o histórico de manutenções e ordens de serviço anteriores.

**Status:** 🟢 Definida

### RN-VEI-005 — Isolamento por Oficina

Um veículo deve pertencer a uma única oficina no contexto do cadastro operacional. Sua consulta e alteração devem respeitar o tenant ao qual está associado.

**Status:** 🟢 Definida

---

## 5. Ordem de Serviço (OS)

### RN-OS-001 — Identificador Único da OS

Cada ordem de serviço deve possuir um identificador único. A identificação sequencial, quando utilizada, deve respeitar o contexto da oficina e não pode provocar duplicidade de registros.

**Status:** 🟢 Definida

### RN-OS-002 — Vínculo de Origem

Toda ordem de serviço deve estar vinculada a um cliente e a um veículo cadastrados na mesma oficina.

**Status:** 🟢 Definida

### RN-OS-003 — Estados Operacionais da OS

A OS deve seguir o fluxo operacional definido para o MVP, respeitando as transições permitidas entre seus estados.

O fluxo principal deverá contemplar os seguintes estados:

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

O estado `AGUARDANDO_RESERVA` representa a etapa em que o sistema aguarda a confirmação da reserva das peças necessárias pelo Inventory Service.

A recusa do orçamento (`RECUSADA`) representa uma decisão sobre o orçamento, e não um estado operacional equivalente à conclusão da OS. Quando houver recusa, a oficina poderá revisar o orçamento ou encerrar a OS por desistência do cliente.

A situação financeira da OS deve ser controlada separadamente de seu estado operacional.

**Status:** 🟢 Definida

### RN-OS-004 — Bloqueio de Transições Inválidas

O sistema deve impedir alterações arbitrárias de estado. Somente transições previstas no fluxo operacional poderão ser realizadas.

Quando um orçamento for revisado após uma recusa, a OS poderá retornar à etapa de aprovação, preservando o histórico das decisões anteriores.

**Status:** 🟢 Definida

### RN-OS-005 — Bloqueio de Execução sem Aprovação e Reserva

A OS não poderá avançar para `EM_EXECUCAO` enquanto o orçamento não estiver formalmente aprovado pelo sistema e a reserva de todas as peças necessárias não tiver sido confirmada.

**Status:** 🟢 Definida

### RN-OS-006 — Conclusão Operacional Independente do Financeiro

A OS poderá atingir o estado operacional `CONCLUIDA` quando os serviços previstos tiverem sido executados e encerrados conforme os registros da oficina.

A conclusão operacional não depende da quitação integral do valor devido. A situação financeira deverá permanecer registrada separadamente no Billing Service.

**Status:** 🟢 Definida

---

## 6. Diagnóstico

### RN-DIA-001 — Vínculo Obrigatório com a OS

Todo diagnóstico técnico registrado pelo mecânico deve estar vinculado a uma ordem de serviço válida e pertencente à mesma oficina.

**Status:** 🟢 Definida

---

## 7. Orçamento

### RN-ORC-001 — Composição Automática do Valor

O valor total do orçamento deve ser calculado automaticamente pelo sistema, considerando os serviços, as peças e os descontos autorizados.

```text
Valor Total = (Soma dos Serviços + Soma das Peças) - Descontos Autorizados
```

**Status:** 🟢 Definida

### RN-ORC-002 — Aprovação e Reserva Obrigatória antes da Execução

A aprovação do orçamento pelo cliente não deve, isoladamente, liberar a execução dos serviços.

Após a aceitação do cliente, o sistema deverá solicitar ao Inventory Service a reserva de todas as peças necessárias. A aprovação operacional do orçamento e a liberação da OS para execução somente poderão ocorrer após a confirmação da reserva integral.

O sistema não deve permitir o início da execução antes da confirmação da aprovação e da reserva.

**Status:** 🟢 Definida

### RN-ORC-003 — Tratamento de Recusa

Quando um orçamento for recusado pelo cliente, a decisão deverá ser registrada e preservada no histórico da OS.

A oficina poderá gerar um novo orçamento revisado, mantendo o vínculo com a mesma OS e preservando as versões e decisões anteriores.

Caso o cliente desista definitivamente do serviço, a OS poderá ser encerrada por cancelamento, respeitando as regras de liberação de reservas eventualmente existentes.

**Status:** 🟢 Definida

### RN-ORC-004 — Aprovação Condicionada à Reserva

O orçamento somente poderá ser considerado aprovado pelo sistema após a confirmação de que todas as peças solicitadas foram reservadas com sucesso pelo Inventory Service.

A aceitação do cliente e a aprovação operacional do orçamento são etapas distintas e devem ser registradas de forma rastreável.

**Status:** 🟢 Definida

### RN-ORC-005 — Tratamento de Reserva Recusada

Caso o Inventory Service informe que não há quantidade suficiente para reservar todas as peças solicitadas, a aprovação operacional do orçamento deverá permanecer bloqueada.

A oficina poderá providenciar reposição de estoque, revisar as peças e os valores do orçamento com nova autorização do cliente ou cancelar a solicitação.

O sistema não deve realizar reserva parcial quando a solicitação exigir a reserva integral dos itens.

**Status:** 🟢 Definida

---

## 8. Estoque

### RN-EST-001 — Existência Prévia da Peça

Nenhuma peça poderá ser reservada ou utilizada em uma OS sem estar previamente cadastrada no módulo de estoque (`Inventory Service`).

**Status:** 🟢 Definida

### RN-EST-002 — Bloqueio por Estoque Insuficiente

O sistema não deve permitir a reserva ou utilização de quantidade superior ao saldo disponível da peça.

O saldo disponível deve considerar as quantidades físicas existentes e as quantidades já comprometidas por reservas ativas.

```text
Estoque Disponível = Estoque Físico - Estoque Reservado
```

**Status:** 🟢 Definida

### RN-EST-003 — Reserva de Peças após Aceitação do Cliente

Após a aceitação do orçamento pelo cliente, o sistema deverá solicitar ao Inventory Service a reserva das quantidades necessárias de cada peça.

A reserva reduz o saldo disponível para novas solicitações, mas não representa uma saída física definitiva do estoque.

**Status:** 🟢 Definida

### RN-EST-004 — Rastreabilidade das Movimentações

Toda reserva, liberação, consumo, perda ou descarte de peças deve gerar registro rastreável no Inventory Service.

As movimentações devem permitir identificar, quando aplicável, o tenant, a peça, a quantidade, a OS, o orçamento relacionado, a operação realizada e sua data e hora.

**Status:** 🟢 Definida

### RN-EST-005 — Liberação de Peças Não Utilizadas

Quando uma peça reservada não for utilizada na execução da OS, sua reserva deverá ser liberada.

A liberação deve devolver a quantidade ao saldo disponível, sem aumentar o estoque físico, pois a peça não havia sido retirada fisicamente do estoque.

**Status:** 🟢 Definida

### RN-EST-006 — Tratamento de Peças Instaladas ou Consumidas

Peças instaladas ou efetivamente consumidas durante a execução não poderão retornar ao estoque comercializável como se estivessem disponíveis.

A saída física deverá ser registrada como consumo definitivo. Quando houver perda, avaria ou descarte, a ocorrência deverá ser registrada conforme sua condição e o procedimento adotado pela oficina.

**Status:** 🟢 Definida

### RN-EST-007 — Histórico das Movimentações

O sistema deve preservar o histórico das movimentações de estoque. Uma movimentação original não deverá ser apagada para representar uma correção.

Estornos, liberações, perdas e descartes deverão ser registrados como operações próprias, vinculadas à movimentação de origem quando aplicável.

**Status:** 🟢 Definida

### RN-EST-008 — Reserva Integral e Atômica

A solicitação de reserva de um orçamento deverá ser processada integralmente no Inventory Service.

Todas as peças e quantidades solicitadas deverão ser reservadas com sucesso ou nenhuma reserva deverá ser efetivada.

Caso qualquer item não possua quantidade disponível suficiente, a operação deverá ser recusada integralmente, sem manter reservas parciais decorrentes daquela solicitação.

**Status:** 🟢 Definida

### RN-EST-009 — Controle de Concorrência

As operações de reserva deverão utilizar mecanismos de controle de concorrência no banco de dados do Inventory Service, impedindo que solicitações simultâneas comprometam a mesma quantidade disponível.

A validação do saldo e a efetivação da reserva deverão ocorrer dentro de uma transação consistente.

**Status:** 🟢 Definida

### RN-EST-010 — Idempotência das Solicitações

Cada solicitação de reserva deverá possuir uma chave de idempotência, de modo que o reprocessamento da mesma solicitação não gere reservas ou movimentações duplicadas.

O sistema deverá reconhecer solicitações já processadas e retornar o resultado correspondente, sem repetir seus efeitos.

**Status:** 🟢 Definida

### RN-EST-011 — Conversão da Reserva em Saída Definitiva

Quando uma peça reservada for efetivamente utilizada na execução da OS, sua quantidade deverá ser convertida em saída definitiva do estoque.

Essa operação deverá reduzir o estoque físico e a quantidade reservada de maneira consistente, sem contabilizar a mesma peça duas vezes como saída.

**Status:** 🟢 Definida

---

## 9. Financeiro

### RN-FIN-001 — Vínculo com a OS

Todo registro de pagamento, recebimento ou quitação financeira referente a um serviço deverá estar associado a uma ordem de serviço válida e pertencente à mesma oficina.

**Status:** 🟢 Definida

### RN-FIN-002 — Situação Financeira Independente

A situação financeira deverá ser controlada separadamente do estado operacional da OS.

O Billing Service deverá registrar se os valores estão pendentes, parcialmente pagos ou quitados, sem impedir que uma OS operacionalmente concluída permaneça com pagamento pendente.

**Status:** 🟢 Definida

### RN-FIN-003 — Rastreabilidade dos Pagamentos

Os pagamentos registrados deverão manter vínculo com a OS correspondente e possuir informações suficientes para identificar o valor, a data, a forma de pagamento e a situação do registro.

**Status:** 🟢 Definida

---

## 10. Histórico de Manutenção

### RN-HIS-001 — Imutabilidade dos Registros Históricos

Os dados consolidados no histórico de manutenção de um veículo não devem ser apagados ou corrompidos por alterações cadastrais secundárias.

Alterações de cliente, proprietário ou informações cadastrais não devem remover o vínculo histórico entre o veículo e suas ordens de serviço anteriores.

**Status:** 🟢 Definida

---

## 11. Autenticação e Permissões

### RN-AUT-001 — Autenticação Obrigatória

O acesso às funcionalidades operacionais do sistema exige autenticação prévia por meio do Auth Service, utilizando credenciais válidas e armazenamento seguro de senhas por algoritmo apropriado de hash.

**Status:** 🟢 Definida

### RN-AUT-002 — Controle por Perfis

O sistema deve restringir o acesso às funcionalidades e rotas de API conforme as permissões atribuídas aos perfis definidos para o MVP:

- **Atendente:** atividades de atendimento e cadastro de clientes, veículos e ordens de serviço, conforme permissões estabelecidas.
- **Mecânico:** consulta e atualização de informações técnicas, diagnósticos e execução dos serviços.
- **Estoque:** cadastro, consulta e controle de peças, reservas e movimentações de estoque.
- **Financeiro:** consulta e registro de pagamentos e informações financeiras.

As permissões devem ser verificadas no backend, não apenas por meio da ocultação de telas no frontend.

**Status:** 🟢 Definida

### RN-AUT-003 — Restrição de Acesso por Tenant

Além das permissões do perfil, toda operação deverá verificar se o usuário possui autorização para acessar os dados da oficina correspondente.

**Status:** 🟢 Definida

---

## 12. Integridade, Comunicação e Rastreabilidade entre Serviços

### RN-DAT-001 — Integridade Referencial Local

Cada microserviço deve garantir a integridade referencial dos dados sob sua responsabilidade por meio de restrições locais no próprio banco de dados.

Como cada serviço possui sua própria base de dados, não deverão ser utilizadas chaves estrangeiras físicas entre bancos pertencentes a microserviços diferentes.

As referências a registros de outros serviços deverão ser mantidas por identificadores e validadas por meio de APIs ou eventos.

**Status:** 🟢 Definida

### RN-DAT-002 — Responsabilidade sobre os Dados

Cada microserviço deve ser responsável pela persistência e pelas regras de negócio dos dados que administra.

Um serviço não deverá acessar diretamente as tabelas ou o banco de dados de outro serviço para consultar ou modificar informações.

**Status:** 🟢 Definida

### RN-DAT-003 — Consistência das Operações Locais

As operações que alteram dados relacionados dentro de um mesmo microserviço deverão utilizar transações locais para preservar a consistência dos registros.

**Status:** 🟢 Definida

### RN-DAT-004 — Publicação Confiável de Eventos

Quando uma operação de negócio precisar persistir dados e publicar um evento, o serviço deverá adotar um mecanismo que evite a perda da publicação entre a confirmação da transação e o envio da mensagem.

A implementação deverá utilizar o padrão Transactional Outbox ou mecanismo equivalente aprovado na arquitetura.

**Status:** 🟢 Definida

### RN-DAT-005 — Processamento Idempotente de Eventos

Os consumidores de eventos deverão ser capazes de reconhecer mensagens já processadas, evitando a repetição indevida de efeitos de negócio em caso de reentrega.

O processamento deverá considerar identificadores únicos de eventos e mecanismos de deduplicação, como Inbox ou solução equivalente.

**Status:** 🟢 Definida

### RN-DAT-006 — Rastreabilidade das Operações

As operações relevantes entre microserviços deverão utilizar identificadores de correlação e de requisição, permitindo acompanhar o fluxo de uma operação distribuída.

Os registros de rastreabilidade não devem expor senhas, tokens ou outros dados sensíveis.

**Status:** 🟢 Definida

---

## 13. Observabilidade e Qualidade

### RN-OBS-001 — Registro de Eventos Operacionais

Os serviços devem registrar informações relevantes para diagnóstico e auditoria, incluindo falhas, alterações de estado e operações críticas.

Os registros devem respeitar o isolamento por tenant e não devem armazenar credenciais ou segredos.

**Status:** 🟢 Definida

### RN-OBS-002 — Monitoramento dos Serviços

A arquitetura deverá prever mecanismos de monitoramento da disponibilidade e do comportamento dos microserviços, incluindo métricas e indicadores operacionais.

A implementação de Prometheus e Grafana deverá atender aos critérios acadêmicos definidos para o projeto.

**Status:** 🟢 Definida

---

## 14. Regras de Evolução do Produto

### RN-EVO-001 — Separação entre Assinatura SaaS e Pagamentos de OS

Os pagamentos realizados pelos clientes da oficina por serviços e peças não devem ser confundidos com a cobrança da assinatura do próprio OficinaPro.

A gestão de planos, assinaturas e cobrança das oficinas pelo uso do SaaS deverá ser tratada separadamente e poderá ser implementada em uma etapa posterior.

**Status:** ⚪ Pós-MVP

### RN-EVO-002 — Evolução da Decomposição dos Serviços

A arquitetura poderá evoluir com a separação de responsabilidades hoje agrupadas nos serviços do MVP, desde que a mudança preserve os limites de domínio, a propriedade dos dados e as regras de comunicação entre microserviços.

**Status:** ⚪ Pós-MVP

---

**Última atualização:** 16/11/2026

**Responsável pela organização documental: Eloísa Brandão** Grupo GarageOps

**Observação:** Este documento consolida as regras de negócio adotadas como direção de trabalho para o MVP. Decisões que dependam de validação acadêmica ou de implementação deverão ser registradas nos documentos correspondentes, sem serem apresentadas como funcionalidades já implementadas.
