# Histórias de Usuário — OficinaPro

> **Documento de trabalho — validado pelo grupo GarageOps.**
>
> Este documento transforma os requisitos levantados no backlog em histórias de usuário que orientam o desenvolvimento do OficinaPro, refletindo as regras de negócio oficiais definidas para o MVP.

---

## 1. Legenda

| Status | Significado |
| --- | --- |
| 🟢 **Definido** | Entendimento já estabelecido |
| 🟡 **Em discussão** | Proposta ainda não validada pelo grupo |
| 🔵 **Validar com professor** | Depende de orientação/validação acadêmica |
| 🔴 **Pendente** | Informação ou decisão ainda necessária |
| ⚪ **Pós-MVP** | Possível funcionalidade futura |

---

## 2. Clientes

## HU-CLI-001 — Cadastrar cliente

**Como** atendente

**Quero** cadastrar um cliente

**Para** manter seus dados disponíveis para utilização no sistema.

### Critérios de aceite

- [ ] Permitir informar nome do cliente.
- [ ] Permitir informar CPF (obrigatório e único).
- [ ] Permitir informar telefone.
- [ ] Permitir informar e-mail.
- [ ] Permitir informar endereço.
- [ ] Validar os campos obrigatórios definidos pelo sistema.
- [ ] Impedir cadastro duplicado com base no CPF.

**Status:** 🟢 Definido

---

## HU-CLI-002 — Consultar cliente

**Como** atendente

**Quero** consultar os dados de um cliente

**Para** localizar rapidamente suas informações e seus veículos.

### Critérios de aceite

- [ ] Permitir buscar cliente por nome.
- [ ] Permitir buscar cliente por CPF.
- [ ] Exibir os dados cadastrados.
- [ ] Exibir os veículos associados ao cliente.

**Status:** 🟢 Definido

---

## HU-CLI-003 — Atualizar cliente

**Como** atendente

**Quero** atualizar os dados de um cliente

**Para** manter suas informações atualizadas.

### Critérios de aceite

- [ ] Permitir alterar dados permitidos.
- [ ] Manter a identificação do cliente.
- [ ] Validar os campos alterados.

**Status:** 🟢 Definido

---

## 3. Veículos

## HU-VEI-001 — Cadastrar veículo

**Como** atendente

**Quero** cadastrar um veículo vinculado a um cliente

**Para** registrar os veículos atendidos pela oficina.

### Critérios de aceite

- [ ] Informar placa (identificador único).
- [ ] Informar marca.
- [ ] Informar modelo.
- [ ] Informar ano.
- [ ] Informar cor.
- [ ] Informar quilometragem.
- [ ] Associar o veículo a um cliente cadastrado.
- [ ] Validar os campos obrigatórios.

**Status:** 🟢 Definido

---

## HU-VEI-002 — Consultar veículo

**Como** atendente

**Quero** consultar os dados de um veículo

**Para** verificar suas informações e histórico de manutenção.

### Critérios de aceite

- [ ] Permitir localizar o veículo pela placa.
- [ ] Exibir os dados do veículo.
- [ ] Exibir o proprietário associado.
- [ ] Permitir acessar o histórico de manutenções.

**Status:** 🟢 Definido

---

## 4. Ordem de Serviço

## HU-OS-001 — Abrir ordem de serviço

**Como** atendente

**Quero** abrir uma ordem de serviço para um veículo

**Para** registrar formalmente um atendimento da oficina.

### Critérios de aceite

- [ ] Selecionar cliente.
- [ ] Selecionar veículo.
- [ ] Registrar data de abertura.
- [ ] Registrar descrição inicial do problema.
- [ ] Gerar identificação única da ordem de serviço.
- [ ] Definir o status inicial como `ABERTA`.

**Status:** 🟢 Definido

---

## HU-OS-002 — Consultar ordem de serviço

**Como** usuário autorizado

**Quero** consultar uma ordem de serviço

**Para** acompanhar seu andamento.

### Critérios de aceite

- [ ] Localizar a OS pelo identificador.
- [ ] Exibir cliente e veículo.
- [ ] Exibir status atual na esteira de andamento.
- [ ] Exibir diagnóstico.
- [ ] Exibir orçamento.
- [ ] Exibir serviços e peças relacionados.
- [ ] Exibir informações financeiras pertinentes.

**Status:** 🟢 Definido

---

## HU-OS-003 — Atualizar status da OS

**Como** usuário autorizado

**Quero** atualizar o status de uma ordem de serviço

**Para** representar corretamente sua etapa atual.

### Status oficiais adotados

```text
ABERTA
EM_DIAGNOSTICO
AGUARDANDO_APROVACAO
APROVADA
EM_EXECUCAO
AGUARDANDO_PAGAMENTO
CONCLUIDA
RECUSADA
CANCELADA
```

### Critérios de aceite

- [ ] Permitir somente transições de status previstas nas regras de negócio.
- [ ] Permitir o retorno para `AGUARDANDO_APROVACAO` quando houver um novo orçamento revisado após uma recusa.
- [ ] Permitir o encerramento definitivo da OS como `CANCELADA` ou `RECUSADA`, conforme o tratamento adotado.

**Status:** 🟢 Definido

---

## 5. Diagnóstico

## HU-DIA-001 — Registrar diagnóstico

**Como** mecânico

**Quero** registrar o diagnóstico do veículo

**Para** documentar os problemas identificados e orientar o orçamento.

### Critérios de aceite

- [ ] Permitir registrar descrição do diagnóstico técnico.
- [ ] Associar o diagnóstico à OS (status muda para `EM_DIAGNOSTICO`).
- [ ] Registrar o responsável pelo diagnóstico.
- [ ] Permitir consultar o diagnóstico posteriormente.

**Status:** 🟢 Definido

---

## 6. Orçamento

## HU-ORC-001 — Elaborar orçamento

**Como** usuário autorizado

**Quero** elaborar um orçamento para a OS

**Para** apresentar ao cliente os serviços e peças necessários.

### Critérios de aceite

- [ ] Adicionar serviços.
- [ ] Adicionar peças.
- [ ] Informar quantidades.
- [ ] Calcular valores automaticamente.
- [ ] Calcular o valor total: `(Serviços + Peças) - Descontos`.
- [ ] Permitir aplicar desconto conforme regra definida.
- [ ] Associar o orçamento à OS (status muda para `AGUARDANDO_APROVACAO`).

**Status:** 🟢 Definido

---

## HU-ORC-002 — Aprovar ou recusar orçamento

**Como** usuário autorizado

**Quero** registrar a aprovação ou recusa do orçamento

**Para** definir se o serviço poderá prosseguir.

### Critérios de aceite

- [ ] Registrar aprovação (`APROVADA`) ou recusa (`RECUSADA`).
- [ ] Registrar data da decisão.
- [ ] Atualizar o status da OS com base na decisão.
- [ ] Em caso de recusa, permitir o encerramento da OS ou a geração de um orçamento revisado vinculado à mesma OS, permitindo seu retorno para `AGUARDANDO_APROVACAO` sem perder o histórico anterior.
- [ ] Manter o registro detalhado de todas as tratativas.

**Status:** 🟢 Definido

**Pendência:**

- Definir formalmente quem possui permissão de perfil para aprovar orçamentos.

---

## 7. Estoque

## HU-EST-001 — Cadastrar peça

**Como** responsável pelo estoque

**Quero** cadastrar uma peça

**Para** controlar os itens disponíveis na oficina.

### Critérios de aceite

- [ ] Registrar nome/descrição da peça.
- [ ] Registrar quantidade disponível em estoque.
- [ ] Registrar valor unitário.
- [ ] Permitir consultar o estoque.

**Status:** 🟢 Definido

---

## HU-EST-002 — Atualizar estoque

**Como** responsável pelo estoque

**Quero** registrar entradas e saídas de peças

**Para** manter a quantidade disponível atualizada.

### Critérios de aceite

- [ ] Registrar entrada de peça.
- [ ] Registrar saída de peça.
- [ ] Atualizar quantidade disponível.
- [ ] Registrar a movimentação no log de estoque.
- [ ] Impedir saída superior ao estoque disponível.

**Status:** 🟢 Definido

---

## HU-EST-003 — Baixar peças vinculadas ao orçamento aprovado

**Como** sistema

**Quero** registrar a saída e controlar as peças vinculadas a uma OS

**Para** manter o inventário atualizado e garantir a rastreabilidade das movimentações.

### Critérios de aceite

- [ ] Efetivar a baixa automática das peças no estoque no momento da aprovação formal do orçamento (`APROVADA`).
- [ ] Registrar cada movimentação de saída vinculando-a à respectiva OS e ao orçamento aprovado.
- [ ] Atualizar a quantidade disponível em estoque após a baixa.
- [ ] Bloquear a aprovação do orçamento caso não haja quantidade suficiente de alguma peça em estoque.
- [ ] Impedir que a mesma aprovação gere mais de uma baixa para as mesmas peças, evitando duplicidade de movimentações.
- [ ] Em caso de cancelamento da OS após a aprovação, verificar a condição física das peças antes de realizar o estorno.
- [ ] Quando as peças ainda não tiverem sido instaladas ou consumidas, realizar o estorno da movimentação de saída e devolver as quantidades correspondentes ao estoque disponível.
- [ ] Quando as peças já tiverem sido instaladas ou consumidas, não devolvê-las ao estoque comercializável. Registrar a ocorrência como perda operacional ou descarte.
- [ ] Preservar o registro da movimentação de saída original e do respectivo estorno, perda ou descarte, garantindo a rastreabilidade das operações.

**Status:** 🟢 Definido

---

## 8. Financeiro

## HU-FIN-001 — Calcular valor da OS

**Como** sistema

**Quero** calcular o valor total da ordem de serviço

**Para** apresentar corretamente o valor devido.

### Regra aplicada

```text
Total = Serviços + Peças - Descontos
```

**Status:** 🟢 Definido

---

## HU-FIN-002 — Registrar pagamento

**Como** usuário autorizado

**Quero** registrar o pagamento de uma OS

**Para** manter o controle financeiro do atendimento.

### Critérios de aceite

- [ ] Registrar valor pago.
- [ ] Registrar forma de pagamento.
- [ ] Registrar data do pagamento.
- [ ] Atualizar o status financeiro e permitir a quitação total para transicionar a OS para `CONCLUIDA`.
- [ ] Permitir consultar o pagamento registrado.

**Status:** 🟢 Definido

---

## 9. Histórico de Manutenção

## HU-HIS-001 — Consultar histórico do veículo

**Como** atendente

**Quero** consultar o histórico de manutenção de um veículo

**Para** verificar os serviços realizados anteriormente.

### Critérios de aceite

- [ ] Listar ordens de serviço anteriores associadas ao veículo.
- [ ] Exibir datas dos atendimentos.
- [ ] Exibir serviços realizados.
- [ ] Exibir informações relevantes sobre peças utilizadas e histórico financeiro.

**Status:** 🟢 Definido

---

## 10. Usuários e permissões

## HU-AUT-001 — Autenticar usuário

**Como** usuário do sistema

**Quero** realizar login

**Para** acessar as funcionalidades permitidas para meu perfil.

**Status:** 🟢 Definido

---

## HU-AUT-002 — Controlar permissões

**Como** responsável pela gestão de acessos

**Quero** definir permissões de acesso

**Para** restringir funcionalidades de acordo com o papel de cada usuário.

### Perfis adotados

- Atendente
- Mecânico
- Estoque
- Financeiro

**Status:** 🟢 Definido

**Pendência:**

- Mapear granularmente as permissões exatas de cada tela por perfil.

---

## 11. Notificações

## HU-NOT-001 — Notificar eventos da OS

**Como** usuário

**Quero** receber notificações sobre eventos relevantes da OS

**Para** acompanhar mudanças importantes no atendimento.

**Status:** 🔵 Validar com professor

---

## 12. Relatórios

## HU-REL-001 — Consultar informações operacionais

**Como** usuário autorizado

**Quero** consultar informações consolidadas da oficina

**Para** acompanhar a operação.

**Status:** 🔵 Validar com professor

---

## 13. Funcionalidades fora do escopo inicial

- Integração com sistemas externos de pagamento.
- Notificações avançadas por canais externos.
- Relatórios gerenciais complexos.
- Dashboard analítico avançado.

**Status:** ⚪ Pós-MVP

---

## 14. Observações

Este documento reflete as histórias de usuário validadas pelo grupo **GarageOps** para o MVP do OficinaPro.

**Última atualização:** 11/11/2026

**Responsável:** Eloísa Brandão (Product Owner)
