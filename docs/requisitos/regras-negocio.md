# Regras de Negócio — OficinaPro

> **Documento oficial de trabalho — Grupo GarageOps**
>
> Este documento consolida as regras de negócio que regem o comportamento e as validações do sistema OficinaPro para o MVP do Projeto Experimental (5º Semestre ADS - UniFAAT).

---

## 1. Legenda de Status

| Status | Significado |
| ---------------------------- | ----------------------------------------------- |
| 🟢 **Definida** | Regra validada e adotada pelo grupo para o MVP. |
| 🟡 **Em discussão** | Proposta em análise. |
| 🔵 **Validar com professor** | Depende de orientação acadêmica. |
| ⚪ **Pós-MVP** | Regra ou funcionalidade futura. |

---

## 2. Clientes

### RN-CLI-001 — Identificação Única por CPF

Todo cliente cadastrado no sistema deve possuir um CPF válido e obrigatório, que atua como chave de negócio única para evitar duplicidade de cadastros.

**Status:** 🟢 Definida

### RN-CLI-002 — Prevenção de Duplicidade

O sistema deve validar os dados informados no momento do cadastro e recusar a persistência caso o CPF já exista na base de dados.

**Status:** 🟢 Definida

### RN-CLI-003 — Inativação de Cliente

Caso um cliente possua ordens de serviço ou histórico vinculado, ele não deve ser excluído fisicamente do banco de dados, devendo ser aplicada a inativação lógica quando necessário.

**Status:** 🟢 Definida

---

## 3. Veículos

### RN-VEI-001 — Associação Obrigatória

Todo veículo cadastrado deve estar obrigatoriamente vinculado a um cliente cadastrado no sistema.

**Status:** 🟢 Definida

### RN-VEI-002 — Placa como Identificador

A **placa** do veículo é o identificador único de negócio, sendo de preenchimento obrigatório e validada pelo sistema.

**Status:** 🟢 Definida

### RN-VEI-003 — Preservação do Histórico

As informações de manutenção e ordens de serviço concluídas devem permanecer permanentemente associadas ao veículo.

**Status:** 🟢 Definida

### RN-VEI-004 — Transferência de Propriedade

Caso um veículo mude de proprietário, o vínculo com o cliente anterior pode ser atualizado para o novo cliente, mantendo intacto todo o histórico de manutenções anteriores do veículo.

**Status:** 🟢 Definida

---

## 4. Ordem de Serviço (OS)

### RN-OS-001 — Identificador Único da OS

Cada ordem de serviço gerada deve possuir um identificador único e sequencial no sistema.

**Status:** 🟢 Definida

### RN-OS-002 — Vínculo de Origem

Uma ordem de serviço deve obrigatoriamente estar vinculada a um cliente e a um veículo existente.

**Status:** 🟢 Definida

### RN-OS-003 — Esteira de Status e Tratamento de Recusa

A OS deve seguir o fluxo operacional definido para o MVP, respeitando as transições permitidas entre seus estados.

O fluxo principal deve seguir a sequência:

```text
ABERTA
  ↓
EM_DIAGNOSTICO
  ↓
AGUARDANDO_APROVACAO
  ↓
APROVADA
  ↓
EM_EXECUCAO
  ↓
AGUARDANDO_PAGAMENTO
  ↓
CONCLUIDA
```

Quando o cliente recusar o orçamento (`RECUSADA`), a OS não deve ser encerrada automaticamente. Nesse caso, poderá ocorrer uma das seguintes situações:

- **Revisão do orçamento:** um novo orçamento poderá ser gerado e vinculado à mesma OS, preservando o histórico da tratativa anterior. Após a negociação, a OS poderá retornar ao estado `AGUARDANDO_APROVACAO` para uma nova apreciação do cliente.
- **Encerramento definitivo:** caso o cliente desista do serviço, a OS poderá ser encerrada como `CANCELADA` ou `RECUSADA`, conforme o tratamento adotado pelo sistema.

**Status:** 🟢 Definida

### RN-OS-004 — Bloqueio de Transições Inválidas

O sistema deve impedir a alteração arbitrária de status, permitindo apenas as transições lógicas mapeadas na esteira oficial do MVP, incluindo o retorno para `AGUARDANDO_APROVACAO` quando houver um novo orçamento revisado.

**Status:** 🟢 Definida

---

## 5. Diagnóstico

### RN-DIA-001 — Vínculo Obrigatório com a OS

Todo laudo de diagnóstico técnico registrado pelo mecânico deve estar atrelado diretamente a uma ordem de serviço.

**Status:** 🟢 Definida

---

## 6. Orçamento

### RN-ORC-001 — Composição Automática do Valor

O valor total do orçamento deve ser calculado automaticamente pelo sistema aplicando a fórmula oficial:

```text
Valor Total = (Soma dos Serviços + Soma das Peças) - Descontos Autorizados
```

**Status:** 🟢 Definida

### RN-ORC-002 — Dependência de Aprovação

A execução física dos serviços na oficina (`EM_EXECUCAO`) e a baixa de estoque das peças vinculadas ao orçamento dependem obrigatoriamente da aprovação formal do orçamento pelo cliente (`APROVADA`).

O sistema não deve permitir o início da execução nem realizar a baixa das peças antes da aprovação formal.

**Status:** 🟢 Definida

### RN-ORC-003 — Tratamento de Recusa

Quando um orçamento for recusado (`RECUSADA`), a OS poderá ser encerrada definitivamente ou ter um novo orçamento revisado gerado para nova apreciação do cliente, sem perder o histórico da tratativa anterior.

O novo orçamento deve permanecer vinculado à mesma OS, preservando o registro do orçamento recusado e da decisão anterior do cliente.

Caso o cliente desista definitivamente do serviço, a OS poderá ser encerrada como `CANCELADA` ou `RECUSADA`, conforme o tratamento adotado pelo sistema.

**Status:** 🟢 Definida

---

## 7. Estoque

### RN-EST-001 — Existência Prévia da Peça

Nenhuma peça pode ser utilizada em uma OS sem estar previamente cadastrada no módulo de estoque (`Inventory Service`).

**Status:** 🟢 Definida

### RN-EST-002 — Bloqueio por Estoque Insuficiente

O sistema não deve permitir a utilização ou baixa de quantidade superior ao saldo disponível em estoque, bloqueando a operação em caso de ruptura.

**Status:** 🟢 Definida

### RN-EST-003 — Baixa de Peças na Aprovação do Orçamento

A baixa física das peças vinculadas ao orçamento deve ocorrer de forma automatizada no momento em que o cliente aprovar formalmente o orçamento (`APROVADA`).

A movimentação de saída deve estar vinculada à respectiva ordem de serviço e ao orçamento aprovado, permitindo rastrear a origem da baixa.


O sistema deve:

- Atualizar a quantidade disponível em estoque no momento da aprovação.
- Registrar a movimentação de saída das peças, identificando a OS e o orçamento correspondente.
- Impedir que a mesma aprovação gere mais de uma baixa para as mesmas peças, evitando duplicidade de movimentações.
- Impedir a aprovação do orçamento quando não houver quantidade suficiente de peças em estoque.
- Em caso de cancelamento da OS após a aprovação do orçamento, realizar o estorno da movimentação de saída das peças no estoque, independentemente do momento em que ocorrer o cancelamento, preservando o registro da movimentação original e do respectivo estorno para garantir a rastreabilidade das operações.

**Status:** 🟢 Definida

---

## 8. Financeiro

### RN-FIN-001 — Vínculo com a OS

Todo registro de pagamento ou quitação financeira deve estar associado obrigatoriamente a uma ordem de serviço válida.

**Status:** 🟢 Definida

### RN-FIN-002 — Quitação para Conclusão

Para que uma OS atinja o status final de `CONCLUIDA`, o valor total devido deve ser integralmente quitado e registrado no Billing Service.

**Status:** 🟢 Definida

---

## 9. Histórico de Manutenção

### RN-HIS-001 — Imutabilidade dos Registros

Os dados consolidados no histórico de manutenção de um veículo não devem ser apagados ou corrompidos por alterações cadastrais secundárias, garantindo auditoria completa.

**Status:** 🟢 Definida

---

## 10. Autenticação e Permissões

### RN-AUT-001 — Autenticação Obrigatória

O acesso a qualquer funcionalidade operacional do sistema exige autenticação prévia via Auth Service utilizando credenciais válidas e hash seguro de senha.

**Status:** 🟢 Definida

### RN-AUT-002 — Controle por Perfis

O sistema deve restringir o acesso a telas e rotas de API com base nas permissões atribuídas aos perfis do MVP: **Atendente, Mecânico, Estoque e Financeiro**.

**Status:** 🟢 Definida

---

## 11. Integridade e Rastreabilidade

### RN-DAT-001 — Integridade Referencial

O banco de dados relacional (PostgreSQL) deve garantir restrições de integridade referencial (`Foreign Keys` e `Constraints`) entre Clientes, Veículos, Ordens de Serviço, Estoque e Pagamentos.

**Status:** 🟢 Definida

---

**Última atualização:** 11/11/2026

**Responsável:** Eloísa Brandão (Product Owner)
