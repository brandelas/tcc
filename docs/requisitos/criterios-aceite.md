# Critérios de Aceite — OficinaPro

> **Documento histórico — não usar na entrega.** Este arquivo foi escrito para o modelo anterior do OficinaPro, com controle de estoque, reserva de peças e perfil de estoque, retirados do escopo pelo grupo em 13/11/2026. Ele não reflete os serviços, papéis e fluxos atuais. A versão vigente está em [`entrega/Projetos/OficinaPro/`](../../entrega/Projetos/OficinaPro/README.md) (Documento 02). Mantido apenas como registro do levantamento.

> **Documento oficial de trabalho — Grupo GarageOps**
>
> Este documento estabelece as condições objetivas de aceitação para as funcionalidades do MVP do OficinaPro, alinhadas às regras de negócio, às histórias de usuário e ao fluxo operacional definido para o projeto.

---

# 1. Objetivo

Os critérios de aceite têm como objetivo estabelecer condições claras para verificar se uma funcionalidade atende ao comportamento esperado antes de ser considerada concluída.

Uma funcionalidade só deverá ser considerada concluída quando:

- atender aos critérios de aceite definidos;
- respeitar as regras de negócio aplicáveis;
- não apresentar erros conhecidos que impeçam seu uso;
- possuir os testes previstos para sua implementação;
- passar pelas validações estabelecidas pelo grupo.

---

# 2. Cadastro de Clientes

## CA-CLI-001 — Cadastrar cliente

A funcionalidade será considerada aceita quando:

- [ ] For obrigatório informar nome, CPF, telefone, e-mail e endereço.
- [ ] O CPF passar por validação de formato e unicidade no sistema.
- [ ] O sistema impedir cadastros duplicados com base no CPF.
- [ ] Um cliente válido for persistido corretamente.

**Status:** 🟢 Definido

---

## CA-CLI-002 — Consultar cliente

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar um cliente por nome ou CPF.
- [ ] Os dados cadastrados forem exibidos corretamente.
- [ ] Os veículos associados ao cliente puderem ser identificados.

**Status:** 🟢 Definido

---

## CA-CLI-003 — Atualizar cliente

A funcionalidade será considerada aceita quando:

- [ ] Dados permitidos puderem ser alterados.
- [ ] As validações aplicáveis forem executadas durante a atualização.
- [ ] Os dados atualizados forem persistidos corretamente.
- [ ] A alteração do CPF respeitar a regra de unicidade.

**Status:** 🟢 Definido

---

# 3. Cadastro de Veículos

## CA-VEI-001 — Cadastrar veículo

A funcionalidade será considerada aceita quando:

- [ ] O veículo estiver obrigatoriamente associado a um cliente cadastrado.
- [ ] A placa passar por validação de formato e unicidade no sistema.
- [ ] O sistema impedir o cadastro de veículos com placas já cadastradas.
- [ ] Marca, modelo, ano, cor e quilometragem forem registrados corretamente.

**Status:** 🟢 Definido

---

## CA-VEI-002 — Consultar veículo

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar o veículo pela placa.
- [ ] Seus dados e seu cliente associado forem exibidos corretamente.
- [ ] Seu histórico de manutenção puder ser acessado.

**Status:** 🟢 Definido

---

# 4. Ordem de Serviço

## CA-OS-001 — Abrir OS

A funcionalidade será considerada aceita quando:

- [ ] For possível selecionar um cliente e um veículo previamente cadastrados.
- [ ] O problema relatado pelo cliente for registrado.
- [ ] A data de abertura e a quilometragem do veículo forem registradas.
- [ ] A OS receber um identificador único.
- [ ] A OS iniciar com o status `ABERTA`.

**Status:** 🟢 Definido

---

## CA-OS-002 — Consultar OS

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar uma OS pelo seu identificador.
- [ ] Os dados do cliente, do veículo e o status atual da OS forem exibidos.
- [ ] As informações de diagnóstico e orçamento vinculadas à OS puderem ser consultadas.
- [ ] O histórico de alterações e decisões relacionadas à OS puder ser identificado.

**Status:** 🟢 Definido

---

## CA-OS-003 — Atualizar status da OS

A funcionalidade será considerada aceita quando:

- [ ] O sistema respeitar as transições de status permitidas pelas regras de negócio.
- [ ] O fluxo principal contemplar as etapas `ABERTA`, `EM_DIAGNOSTICO`, `AGUARDANDO_APROVACAO`, `EM_EXECUCAO`, `AGUARDANDO_PAGAMENTO` e `CONCLUIDA`.
- [ ] A aprovação ou recusa do orçamento atualizar os estados correspondentes da OS.
- [ ] Uma OS com orçamento recusado puder ser encerrada definitivamente ou retornar para `AGUARDANDO_APROVACAO` quando houver geração de orçamento revisado.
- [ ] Uma OS cancelada assumir o status `CANCELADA`.
- [ ] Transições inválidas forem bloqueadas pelo sistema.

**Status:** 🟢 Definido

---

# 5. Diagnóstico

## CA-DIA-001 — Registrar diagnóstico

A funcionalidade será considerada aceita quando:

- [ ] O diagnóstico técnico realizado pelo mecânico puder ser associado à OS correspondente.
- [ ] As informações registradas puderem ser consultadas posteriormente.
- [ ] O registro do diagnóstico atualizar o andamento da OS para `EM_DIAGNOSTICO`, conforme o fluxo definido.

**Status:** 🟢 Definido

---

# 6. Orçamento

## CA-ORC-001 — Criar orçamento

A funcionalidade será considerada aceita quando:

- [ ] Serviços e peças puderem ser adicionados ao orçamento com suas respectivas quantidades e valores.
- [ ] O valor total for calculado automaticamente pela fórmula:

```text
Total = Serviços + Peças - Descontos
```

- [ ] O orçamento ficar associado à OS correspondente.
- [ ] A OS assumir o status `AGUARDANDO_APROVACAO` após o encaminhamento do orçamento ao cliente.
- [ ] O orçamento puder ser consultado posteriormente.

**Status:** 🟢 Definido

---

## CA-ORC-002 — Aprovar orçamento

A funcionalidade será considerada aceita quando:

- [ ] Um usuário autorizado puder registrar a aprovação do orçamento (`APROVADA`).
- [ ] A decisão de aprovação ficar registrada e vinculada ao orçamento e à OS.
- [ ] A OS assumir o estado correspondente à aprovação.
- [ ] A aprovação disparar a baixa automática das quantidades previstas no orçamento no saldo disponível do estoque.
- [ ] A movimentação de saída ficar vinculada à OS e ao orçamento correspondente.
- [ ] A execução dos serviços depender de um orçamento formalmente aprovado.

**Status:** 🟢 Definido

---

## CA-ORC-003 — Recusar orçamento e permitir revisão

A funcionalidade será considerada aceita quando:

- [ ] A recusa do orçamento (`RECUSADA`) puder ser registrada com o motivo da decisão.
- [ ] O sistema permitir o encerramento definitivo da OS ou a elaboração de um orçamento revisado.
- [ ] Um novo orçamento revisado puder ser vinculado à mesma OS.
- [ ] A OS puder retornar para `AGUARDANDO_APROVACAO` para que o cliente avalie a nova proposta.
- [ ] O orçamento anterior e sua decisão permanecerem disponíveis no histórico, sem serem sobrescritos.

**Status:** 🟢 Definido

---

# 7. Estoque

## CA-EST-001 — Cadastrar peça e verificar saldo

A funcionalidade será considerada aceita quando:

- [ ] For possível cadastrar peças com suas respectivas quantidades e valores unitários.
- [ ] O sistema consultar a disponibilidade das peças necessárias para o orçamento.
- [ ] A aprovação do orçamento for bloqueada quando a quantidade solicitada for superior à disponibilidade no inventário.
- [ ] O saldo disponível das peças puder ser consultado.

**Status:** 🟢 Definido

---

## CA-EST-002 — Baixa lógica automática na aprovação e rastreio da condição física

A funcionalidade será considerada aceita quando:

- [ ] A aprovação do orçamento (`APROVADA`) disparar a baixa lógica automática das quantidades de peças previstas no orçamento, reduzindo o saldo disponível do estoque.
- [ ] A movimentação de saída ficar registrada e vinculada à OS e ao orçamento correspondente.
- [ ] Cada peça ou unidade baixada tiver sua condição física registrada individualmente, inicialmente como `PENDENTE_INSTALACAO`.
- [ ] O sistema impedir baixas duplicadas para a mesma movimentação, OS e orçamento.
- [ ] O saldo disponível do inventário for atualizado corretamente após a movimentação.
- [ ] A baixa no saldo disponível for tratada como uma reserva lógica, não sendo interpretada isoladamente como confirmação de instalação ou consumo físico imediato da peça.

**Status:** 🟢 Definido

---

## CA-EST-003 — Estorno condicionado ao estado físico e tratamento de perdas em caso de cancelamento

A funcionalidade será considerada aceita quando:

- [ ] O cancelamento de uma OS com orçamento aprovado for identificado pelo sistema.
- [ ] O sistema consultar obrigatoriamente a condição física registrada para cada peça ou unidade associada à OS.
- [ ] Peças com condição física `PENDENTE_INSTALACAO` puderem retornar ao estoque disponível por meio de uma movimentação auditável de estorno, desde que estejam intactas e em condições de reutilização.
- [ ] O estorno atualizar corretamente o saldo disponível do inventário.
- [ ] Peças cuja condição física esteja marcada como `INSTALADA` tiverem o estorno para comercialização bloqueado pelo sistema.
- [ ] Peças cuja condição física esteja marcada como `INSTALADA` ou `CONSUMIDA` gerarem obrigatoriamente um registro de perda operacional ou descarte, sem retorno ao estoque comercializável.
- [ ] A movimentação original de saída permanecer inalterada, mantendo o histórico completo de auditoria, sem exclusão ou sobreposição de registros.
- [ ] A movimentação de estorno ou o registro de perda operacional ficar vinculado à movimentação original e à OS correspondente.
- [ ] O sistema impedir que a mesma quantidade de uma movimentação de saída seja estornada e registrada como perda simultaneamente.
- [ ] O sistema impedir que uma mesma movimentação de saída seja estornada ou registrada como perda mais de uma vez.

**Status:** 🟢 Definido

---

# 8. Financeiro

## CA-FIN-001 — Calcular total da OS

A funcionalidade será considerada aceita quando:

```text
Total = Serviços + Peças - Descontos
```

- [ ] Os valores dos serviços e das peças forem considerados no cálculo.
- [ ] Os descontos aplicáveis forem subtraídos corretamente.
- [ ] O total for exibido corretamente na OS.

**Status:** 🟢 Definido

---

## CA-FIN-002 — Registrar pagamento e quitação

A funcionalidade será considerada aceita quando:

- [ ] For possível registrar o pagamento referente à OS.
- [ ] O valor pago e o status financeiro forem atualizados corretamente.
- [ ] A OS permanecer em `AGUARDANDO_PAGAMENTO` enquanto houver saldo pendente.
- [ ] A OS transicionar para `CONCLUIDA` somente após a quitação total.

**Status:** 🟢 Definido

---

# 9. Histórico de Manutenção

## CA-HIS-001 — Consultar histórico

A funcionalidade será considerada aceita quando:

- [ ] For possível acessar o histórico de manutenção de um veículo pela placa.
- [ ] As OS anteriores forem listadas corretamente.
- [ ] Os diagnósticos, orçamentos, peças utilizadas e valores pagos puderem ser consultados.
- [ ] As informações históricas forem preservadas mesmo após o encerramento ou cancelamento de uma OS.

**Status:** 🟢 Definido

---

# 10. Autenticação e Permissões

## CA-AUT-001 — Login e autorização

A funcionalidade será considerada aceita quando:

- [ ] Usuários cadastrados puderem realizar autenticação no sistema.
- [ ] As credenciais forem armazenadas utilizando hash seguro.
- [ ] O acesso às funcionalidades respeitar os perfis e as permissões estabelecidos: Atendente, Mecânico, Estoque e Financeiro.
- [ ] Usuários sem permissão forem impedidos de executar ações restritas.

**Status:** 🟢 Definido

---

# 11. Notificações

## CA-NOT-001 — Gerar notificação

A funcionalidade será considerada aceita quando:

- [ ] Os eventos definidos para o MVP gerarem as notificações correspondentes.
- [ ] As notificações forem associadas ao evento e ao usuário destinatário, quando aplicável.

**Status:** 🔵 Validar com professor

---

# 12. Relatórios

## CA-REL-001 — Consultar relatório

A funcionalidade será considerada aceita quando:

- [ ] Um usuário autorizado puder acessar informações consolidadas da operação.
- [ ] Os dados apresentados corresponderem aos registros existentes no sistema.

**Status:** 🔵 Validar com professor

---

# 13. Critérios Gerais de Qualidade

## CA-GER-001 — Validação de dados

- [ ] Os campos obrigatórios forem validados.
- [ ] Dados inválidos forem rejeitados pelo sistema.
- [ ] As mensagens de erro forem compreensíveis para o usuário.

## CA-GER-002 — Persistência e integridade

- [ ] Os dados válidos forem persistidos corretamente no PostgreSQL.
- [ ] A integridade referencial entre as entidades for mantida.
- [ ] As operações de atualização e exclusão respeitarem as regras de integridade e rastreabilidade.

## CA-GER-003 — Autorização

- [ ] As ações restritas exigirem permissão adequada conforme o perfil do usuário.
- [ ] O sistema impedir acessos não autorizados.

## CA-GER-004 — Testes

- [ ] As funcionalidades críticas possuírem testes automatizados.
- [ ] Os testes contemplarem cenários de sucesso e de erro.
- [ ] Os testes definidos para a funcionalidade forem executados e aprovados.

## CA-GER-005 — Tratamento de erros

- [ ] Erros previsíveis forem tratados adequadamente.
- [ ] Mensagens de erro não expuserem dados sensíveis.
- [ ] Falhas em operações críticas não deixarem os registros em estados inconsistentes.

---

# 14. Definição de Pronto (DoD)

Uma história de usuário será considerada pronta para entrega quando:

- [ ] Todos os critérios de aceite aplicáveis forem atendidos.
- [ ] A implementação estiver integrada à branch correspondente do repositório.
- [ ] Os testes automatizados definidos para a funcionalidade estiverem passando.
- [ ] A meta de cobertura de testes estabelecida para o projeto (mínimo de 60%) for atendida, conforme o escopo de medição definido.
- [ ] Não houver erros bloqueadores conhecidos.
- [ ] A documentação necessária estiver atualizada.
- [ ] O Pull Request tiver sido revisado pelo grupo GarageOps.

**Status:** 🟢 Definido

---

**Última atualização:** 11/11/2026

**Responsável:** Eloísa Brandão — Product Owner
