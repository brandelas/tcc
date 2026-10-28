# Critérios de Aceite — OficinaPro

> **Documento de trabalho — sujeito à validação do grupo e do professor.**
>
> Os critérios abaixo representam condições propostas para considerar uma funcionalidade atendida. Eles deverão ser revisados conforme as decisões do grupo e a definição final do MVP.

---

# 1. Objetivo

Os critérios de aceite têm como objetivo estabelecer condições claras para verificar se uma funcionalidade atende ao comportamento esperado antes de ser considerada concluída.

Uma funcionalidade só deverá ser considerada concluída quando:

- atender aos critérios definidos;
- respeitar as regras de negócio aplicáveis;
- não apresentar erros conhecidos que impeçam seu uso;
- possuir os testes definidos para sua implementação;
- passar pelas validações estabelecidas pelo grupo.

---

# 2. Cadastro de Clientes

## CA-CLI-001 — Cadastrar cliente

A funcionalidade será considerada aceita quando:

- [ ] for possível informar os dados definidos como obrigatórios;
- [ ] os dados forem validados;
- [ ] um cliente válido for persistido;
- [ ] o cliente puder ser posteriormente consultado;
- [ ] o sistema impedir duplicidades conforme regra definida.

**Status:** 🟡 Em discussão

---

## CA-CLI-002 — Consultar cliente

A funcionalidade será considerada aceita quando:

- [ ] for possível localizar um cliente;
- [ ] os dados cadastrados forem exibidos corretamente;
- [ ] os veículos associados puderem ser identificados;
- [ ] o sistema tratar corretamente cliente inexistente.

**Status:** 🟡 Em discussão

---

## CA-CLI-003 — Atualizar cliente

A funcionalidade será considerada aceita quando:

- [ ] dados permitidos puderem ser alterados;
- [ ] validações forem aplicadas;
- [ ] os dados atualizados forem persistidos;
- [ ] a alteração puder ser consultada posteriormente.

**Status:** 🟡 Em discussão

---

# 3. Cadastro de Veículos

## CA-VEI-001 — Cadastrar veículo

A funcionalidade será considerada aceita quando:

- [ ] for possível cadastrar um veículo;
- [ ] o veículo estiver associado a um cliente;
- [ ] os campos obrigatórios forem validados;
- [ ] o sistema impedir duplicidade conforme regra definida;
- [ ] o veículo puder ser consultado posteriormente.

**Status:** 🟡 Em discussão

---

## CA-VEI-002 — Consultar veículo

A funcionalidade será considerada aceita quando:

- [ ] for possível localizar o veículo;
- [ ] seus dados forem exibidos;
- [ ] seu cliente associado for identificado;
- [ ] seu histórico de manutenção puder ser acessado quando aplicável.

**Status:** 🟡 Em discussão

---

# 4. Ordem de Serviço

## CA-OS-001 — Abrir OS

A funcionalidade será considerada aceita quando:

- [ ] for possível selecionar cliente e veículo;
- [ ] os dados obrigatórios forem preenchidos;
- [ ] a OS receber um identificador único;
- [ ] a OS receber o status inicial definido;
- [ ] a OS puder ser consultada posteriormente.

**Status:** 🟡 Em discussão

---

## CA-OS-002 — Consultar OS

A funcionalidade será considerada aceita quando:

- [ ] for possível localizar uma OS;
- [ ] cliente e veículo forem exibidos;
- [ ] o status atual for exibido;
- [ ] diagnóstico, orçamento e informações relacionadas forem exibidos quando existentes.

**Status:** 🟡 Em discussão

---

## CA-OS-003 — Atualizar status da OS

A funcionalidade será considerada aceita quando:

- [ ] somente transições permitidas forem executadas;
- [ ] o status atual for atualizado corretamente;
- [ ] alterações relevantes forem registradas;
- [ ] usuários sem permissão não possam executar alterações restritas.

**Status:** 🟡 Em discussão

---

# 5. Diagnóstico

## CA-DIA-001 — Registrar diagnóstico

A funcionalidade será considerada aceita quando:

- [ ] o diagnóstico puder ser associado a uma OS;
- [ ] for possível registrar sua descrição;
- [ ] o responsável puder ser identificado quando essa regra for adotada;
- [ ] o diagnóstico puder ser consultado posteriormente.

**Status:** 🟡 Em discussão

---

# 6. Orçamento

## CA-ORC-001 — Criar orçamento

A funcionalidade será considerada aceita quando:

- [ ] serviços puderem ser adicionados;
- [ ] peças puderem ser adicionadas;
- [ ] quantidades puderem ser informadas;
- [ ] valores forem calculados corretamente;
- [ ] descontos sejam aplicados conforme regra;
- [ ] o total seja calculado corretamente;
- [ ] o orçamento fique associado à OS.

**Status:** 🟡 Em discussão

---

## CA-ORC-002 — Aprovar orçamento

A funcionalidade será considerada aceita quando:

- [ ] um usuário autorizado puder registrar a aprovação;
- [ ] a decisão ficar registrada;
- [ ] a OS assumir o estado correspondente;
- [ ] a aprovação puder ser consultada posteriormente.

**Status:** 🟡 Em discussão

---

## CA-ORC-003 — Recusar orçamento

A funcionalidade será considerada aceita quando:

- [ ] um usuário autorizado puder registrar a recusa;
- [ ] a decisão ficar registrada;
- [ ] a OS assumir o comportamento definido para uma recusa;
- [ ] a decisão puder ser consultada posteriormente.

**Status:** 🟡 Em discussão

---

# 7. Estoque

## CA-EST-001 — Cadastrar peça

A funcionalidade será considerada aceita quando:

- [ ] for possível cadastrar uma peça;
- [ ] quantidade e demais campos obrigatórios forem validados;
- [ ] a peça puder ser consultada;
- [ ] sua quantidade disponível estiver correta.

**Status:** 🟡 Em discussão

---

## CA-EST-002 — Registrar entrada de estoque

A funcionalidade será considerada aceita quando:

- [ ] for possível registrar entrada;
- [ ] a quantidade disponível for atualizada;
- [ ] a movimentação ficar registrada.

**Status:** 🟡 Em discussão

---

## CA-EST-003 — Registrar saída de estoque

A funcionalidade será considerada aceita quando:

- [ ] for possível registrar uma saída válida;
- [ ] o sistema impedir quantidade superior à disponível;
- [ ] a quantidade disponível for atualizada;
- [ ] a movimentação ficar registrada.

**Status:** 🟡 Em discussão

---

# 8. Financeiro

## CA-FIN-001 — Calcular total da OS

A funcionalidade será considerada aceita quando:

```text
Total = Serviços + Peças - Descontos
```

e:

- [ ] os valores forem calculados corretamente;
- [ ] o total estiver associado à OS;
- [ ] o valor apresentado corresponder aos itens registrados.

**Status:** 🟡 Em discussão

---

## CA-FIN-002 — Registrar pagamento

A funcionalidade será considerada aceita quando:

- [ ] for possível registrar um pagamento;
- [ ] o valor pago for armazenado;
- [ ] a forma de pagamento for armazenada quando aplicável;
- [ ] a data do pagamento for armazenada;
- [ ] o status financeiro for atualizado conforme regra.

**Status:** 🟡 Em discussão

---

# 9. Histórico de Manutenção

## CA-HIS-001 — Consultar histórico

A funcionalidade será considerada aceita quando:

- [ ] for possível acessar o histórico de um veículo;
- [ ] as OS anteriores forem listadas;
- [ ] as datas dos atendimentos forem apresentadas;
- [ ] os serviços realizados puderem ser identificados;
- [ ] as informações disponíveis sobre peças forem apresentadas.

**Status:** 🟡 Em discussão

---

# 10. Autenticação e Permissões

## CA-AUT-001 — Login

A funcionalidade será considerada aceita quando:

- [ ] usuários válidos conseguirem autenticar;
- [ ] credenciais inválidas forem rejeitadas;
- [ ] o acesso às funcionalidades protegidas exigir autenticação;
- [ ] a sessão/autenticação seguir a estratégia definida pelo projeto.

**Status:** 🟡 Em discussão

---

## CA-AUT-002 — Controle de permissões

A funcionalidade será considerada aceita quando:

- [ ] o usuário receber o perfil correspondente;
- [ ] funcionalidades restritas respeitarem as permissões;
- [ ] usuários sem permissão não conseguirem executar ações restritas.

**Status:** 🟡 Em discussão

---

# 11. Notificações

## CA-NOT-001 — Gerar notificação

A funcionalidade será considerada aceita quando:

- [ ] um evento configurado gerar a notificação correspondente;
- [ ] o destinatário correto for identificado;
- [ ] a notificação utilizar o canal definido;
- [ ] falhas de envio forem tratadas conforme regra definida.

**Status:** 🔵 Validar com professor

---

# 12. Relatórios

## CA-REL-001 — Consultar relatório

A funcionalidade será considerada aceita quando:

- [ ] o usuário autorizado conseguir acessar o relatório;
- [ ] os dados apresentados corresponderem aos dados registrados;
- [ ] os filtros definidos funcionarem corretamente.

**Status:** 🔵 Validar com professor

---

# 13. Critérios gerais de qualidade

Além dos critérios específicos, as funcionalidades do MVP deverão observar, quando aplicável:

## CA-GER-001 — Validação de dados

- [ ] Campos obrigatórios devem ser validados.
- [ ] Formatos inválidos devem ser rejeitados.
- [ ] Mensagens de erro devem permitir compreender o problema.

---

## CA-GER-002 — Persistência

- [ ] Dados válidos devem ser persistidos corretamente.
- [ ] Dados persistidos devem poder ser recuperados posteriormente.
- [ ] Relacionamentos devem manter integridade.

---

## CA-GER-003 — Autorização

- [ ] Ações restritas devem exigir a permissão adequada.
- [ ] Usuários não autorizados não devem conseguir executar operações protegidas.

---

## CA-GER-004 — Testes

- [ ] Funcionalidades críticas devem possuir testes.
- [ ] Testes devem validar cenários de sucesso.
- [ ] Testes devem validar cenários de erro quando aplicável.

---

## CA-GER-005 — Tratamento de erros

- [ ] Erros previsíveis devem ser tratados.
- [ ] O sistema não deve expor informações sensíveis.
- [ ] O usuário deve receber uma resposta adequada para erros esperados.

---

# 14. Definição de Pronto — proposta

Uma história de usuário poderá ser considerada **pronta para entrega** quando:

- [ ] os critérios de aceite forem atendidos;
- [ ] a implementação estiver integrada à branch definida pelo grupo;
- [ ] os testes previstos estiverem passando;
- [ ] não houver erro bloqueador conhecido;
- [ ] a documentação necessária estiver atualizada;
- [ ] a revisão de código definida pelo grupo tiver sido realizada.

**Status:** 🟡 Em discussão

---

# 15. Observação

Os critérios apresentados neste documento são uma base inicial para orientar desenvolvimento e testes.

Eles deverão ser ajustados quando o grupo definir o escopo definitivo do MVP e quando decisões de negócio ou técnicas forem tomadas.

Nenhum critério marcado como proposta deve ser tratado como requisito definitivo sem validação.

**Última atualização:** 27/10/2026 
**Responsável pelo levantamento:** Eloísa Brandão
