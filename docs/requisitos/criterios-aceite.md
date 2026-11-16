# Critérios de Aceite — OficinaPro

> **Documento oficial de trabalho — Grupo GarageOps**
>
> Este documento estabelece as condições objetivas de aceitação das funcionalidades do OficinaPro, alinhadas às regras de negócio, às histórias de usuário, ao backlog e aos fluxos operacionais definidos para o projeto.
>
> Os critérios descrevem o comportamento esperado do sistema. Sua implementação e validação deverão ser comprovadas por testes e evidências durante o desenvolvimento.

---

# 1. Objetivo

Os critérios de aceite têm como objetivo estabelecer condições claras e verificáveis para determinar se uma funcionalidade atende ao comportamento esperado antes de ser considerada concluída.

Uma funcionalidade só deverá ser considerada concluída quando:

- atender aos critérios de aceite definidos;
- respeitar as regras de negócio aplicáveis;
- preservar o isolamento dos dados entre oficinas;
- não apresentar erros conhecidos que impeçam seu uso;
- possuir os testes previstos para sua implementação;
- passar pelas validações estabelecidas pelo grupo.

**Legenda de status:**

| Status | Significado |
|---|---|
| 🟢 Definido | Critério definido para o escopo do projeto |
| 🟡 Em definição | Depende de detalhamento ou decisão complementar |
| 🔵 Fora do MVP / validar | Funcionalidade prevista para evolução ou que depende de validação de escopo |

---

# 2. Isolamento entre Oficinas (Multi-tenancy)

## CA-TEN-001 — Isolar os dados por oficina

A funcionalidade será considerada aceita quando:

- [ ] Cada oficina cadastrada no SaaS possuir um identificador de tenant próprio.
- [ ] Os registros pertencentes a uma oficina estiverem associados ao seu respectivo `tenant_id`.
- [ ] O contexto do tenant for obtido e validado pelo backend a partir da autenticação do usuário.
- [ ] O sistema não confiar exclusivamente em um `tenant_id` enviado pelo frontend para autorizar operações.
- [ ] Consultas, cadastros, atualizações e exclusões respeitarem o tenant autenticado.
- [ ] Um usuário de uma oficina não conseguir consultar, alterar ou excluir registros pertencentes a outra oficina.
- [ ] Eventos e operações entre microsserviços preservarem a identificação do tenant.
- [ ] A mesma pessoa ou veículo puder possuir cadastros em oficinas diferentes, sem conflito de unicidade entre tenants.
- [ ] Tentativas de acesso cruzado entre oficinas forem bloqueadas e registradas de forma apropriada.

**Status:** 🟢 Definido

---

# 3. Cadastro de Clientes

## CA-CLI-001 — Cadastrar cliente

A funcionalidade será considerada aceita quando:

- [ ] For obrigatório informar nome, CPF, telefone, e-mail e endereço.
- [ ] O CPF passar por validação de formato e dígitos verificadores.
- [ ] O CPF for único dentro da oficina (tenant) correspondente.
- [ ] O sistema impedir cadastros duplicados de clientes com o mesmo CPF dentro da mesma oficina.
- [ ] O sistema permitir que o mesmo CPF esteja cadastrado em oficinas diferentes.
- [ ] O cliente for associado automaticamente ao tenant autenticado.
- [ ] Um cliente válido for persistido corretamente.

**Status:** 🟢 Definido

---

## CA-CLI-002 — Consultar cliente

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar um cliente por nome ou CPF.
- [ ] Os dados cadastrados forem exibidos corretamente.
- [ ] Os veículos associados ao cliente puderem ser identificados.
- [ ] A consulta retornar somente clientes pertencentes à oficina autenticada.

**Status:** 🟢 Definido

---

## CA-CLI-003 — Atualizar cliente

A funcionalidade será considerada aceita quando:

- [ ] Os dados permitidos puderem ser alterados.
- [ ] As validações aplicáveis forem executadas durante a atualização.
- [ ] Os dados atualizados forem persistidos corretamente.
- [ ] A alteração do CPF respeitar a regra de unicidade dentro do tenant.
- [ ] A atualização não permitir modificar registros de outra oficina.
- [ ] Clientes com histórico operacional não forem excluídos fisicamente, devendo ser desativados quando aplicável.

**Status:** 🟢 Definido

---

# 4. Cadastro de Veículos

## CA-VEI-001 — Cadastrar veículo

A funcionalidade será considerada aceita quando:

- [ ] O veículo estiver obrigatoriamente associado a um cliente cadastrado na mesma oficina.
- [ ] A placa passar por validação de formato e unicidade dentro do tenant.
- [ ] O sistema impedir o cadastro de veículos com placas já cadastradas na mesma oficina.
- [ ] O sistema permitir que a mesma placa esteja cadastrada em oficinas diferentes.
- [ ] Marca, modelo, ano, cor e quilometragem forem registrados corretamente.
- [ ] O veículo for associado ao tenant autenticado.
- [ ] A quilometragem registrada respeitar as validações definidas para o campo.

**Status:** 🟢 Definido

---

## CA-VEI-002 — Consultar veículo

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar o veículo pela placa.
- [ ] Seus dados e seu cliente associado forem exibidos corretamente.
- [ ] Seu histórico de manutenção puder ser acessado.
- [ ] A consulta retornar somente veículos pertencentes à oficina autenticada.

**Status:** 🟢 Definido

---

# 5. Ordem de Serviço

## CA-OS-001 — Abrir OS

A funcionalidade será considerada aceita quando:

- [ ] For possível selecionar um cliente e um veículo previamente cadastrados na mesma oficina.
- [ ] O problema relatado pelo cliente for registrado.
- [ ] A data de abertura e a quilometragem do veículo forem registradas.
- [ ] A OS receber um identificador único.
- [ ] A OS iniciar com o status `ABERTA`.
- [ ] A OS for associada ao tenant autenticado.
- [ ] O sistema impedir a associação de clientes ou veículos pertencentes a outra oficina.

**Status:** 🟢 Definido

---

## CA-OS-002 — Consultar OS

A funcionalidade será considerada aceita quando:

- [ ] For possível localizar uma OS pelo seu identificador.
- [ ] Os dados do cliente, do veículo e o status operacional atual da OS forem exibidos.
- [ ] As informações de diagnóstico e orçamento vinculadas à OS puderem ser consultadas.
- [ ] O histórico de alterações e decisões relacionadas à OS puder ser identificado.
- [ ] As informações financeiras puderem ser consultadas separadamente do status operacional.
- [ ] A consulta retornar somente OS pertencentes à oficina autenticada.

**Status:** 🟢 Definido

---

## CA-OS-003 — Atualizar status da OS

A funcionalidade será considerada aceita quando:

- [ ] O sistema respeitar as transições de status permitidas pelas regras de negócio.
- [ ] O fluxo principal contemplar as etapas `ABERTA`, `EM_DIAGNOSTICO`, `AGUARDANDO_APROVACAO`, `AGUARDANDO_RESERVA`, `EM_EXECUCAO` e `CONCLUIDA`.
- [ ] O status `CANCELADA` puder ser aplicado quando o cancelamento for permitido.
- [ ] A aceitação do orçamento pelo cliente não liberar diretamente a execução dos serviços.
- [ ] Após a aceitação do orçamento, a OS puder assumir `AGUARDANDO_RESERVA` enquanto o inventário processa a solicitação.
- [ ] A OS somente avançar para a etapa de execução após a confirmação da reserva integral das peças necessárias e a aprovação operacional correspondente.
- [ ] A recusa do orçamento puder resultar no encerramento da OS ou na elaboração de um orçamento revisado, conforme as regras definidas.
- [ ] Uma OS com orçamento revisado puder retornar para `AGUARDANDO_APROVACAO`.
- [ ] Uma OS concluída operacionalmente não precisar estar financeiramente quitada.
- [ ] Transições inválidas forem bloqueadas pelo sistema.
- [ ] As alterações de status forem registradas no histórico da OS.

**Status:** 🟢 Definido

---

# 6. Diagnóstico

## CA-DIA-001 — Registrar diagnóstico

A funcionalidade será considerada aceita quando:

- [ ] O diagnóstico técnico realizado pelo mecânico puder ser associado à OS correspondente.
- [ ] As informações registradas puderem ser consultadas posteriormente.
- [ ] O registro do diagnóstico atualizar o andamento da OS para `EM_DIAGNOSTICO`, conforme o fluxo definido.
- [ ] O diagnóstico estiver associado à oficina correspondente.
- [ ] Somente usuários autorizados puderem registrar ou alterar informações técnicas.

**Status:** 🟢 Definido

---

# 7. Orçamento

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
- [ ] O orçamento estiver associado ao tenant da OS correspondente.
- [ ] Os valores e quantidades forem validados antes da persistência.

**Status:** 🟢 Definido

---

## CA-ORC-002 — Registrar aceitação do orçamento e concluir aprovação operacional

A funcionalidade será considerada aceita quando:

- [ ] Um usuário autorizado puder registrar a decisão do cliente sobre o orçamento.
- [ ] A decisão de aceitação ou recusa ficar registrada e vinculada ao orçamento e à OS.
- [ ] O registro da decisão armazenar o responsável pelo registro, a data, o horário e o resultado.
- [ ] A aceitação do cliente iniciar a solicitação de reserva das peças necessárias no Inventory Service.
- [ ] A OS assumir o estado `AGUARDANDO_RESERVA` enquanto a reserva estiver em processamento.
- [ ] A aprovação operacional do orçamento ocorrer somente após a confirmação de reserva integral das peças necessárias.
- [ ] A aprovação operacional ficar registrada com responsável, data, horário e decisão.
- [ ] A execução dos serviços permanecer bloqueada enquanto a aprovação operacional não estiver concluída.
- [ ] A execução permanecer bloqueada caso a reserva seja recusada, falhe ou não consiga atender integralmente às quantidades solicitadas.
- [ ] A confirmação da reserva e a aprovação operacional não gerarem movimentações duplicadas no inventário.
- [ ] A aprovação e seus eventos associados forem processados de maneira idempotente.

**Status:** 🟢 Definido

---

## CA-ORC-003 — Recusar orçamento e permitir revisão

A funcionalidade será considerada aceita quando:

- [ ] A recusa do orçamento (`RECUSADA`) puder ser registrada com o motivo da decisão.
- [ ] O sistema permitir o encerramento definitivo da OS ou a elaboração de um orçamento revisado.
- [ ] Um novo orçamento revisado puder ser vinculado à mesma OS.
- [ ] A OS puder retornar para `AGUARDANDO_APROVACAO` para que o cliente avalie a nova proposta.
- [ ] O orçamento anterior e sua decisão permanecerem disponíveis no histórico, sem serem sobrescritos.
- [ ] Uma eventual reserva anterior for liberada quando a revisão ou recusa exigir sua liberação.
- [ ] A liberação das reservas for registrada no inventário e não provocar aumento indevido do estoque físico.

**Status:** 🟢 Definido

---

# 8. Estoque

## CA-EST-001 — Cadastrar peça e verificar saldo

A funcionalidade será considerada aceita quando:

- [ ] For possível cadastrar peças com suas respectivas quantidades e valores unitários.
- [ ] O sistema permitir consultar o estoque físico, as quantidades reservadas e o saldo disponível.
- [ ] O saldo disponível for calculado pela fórmula:

```text
Estoque disponível = Estoque físico - Estoque reservado
```

- [ ] O sistema consultar a disponibilidade das peças necessárias para o orçamento.
- [ ] A solicitação de reserva for recusada quando a quantidade solicitada for superior ao saldo disponível.
- [ ] O sistema não permitir saldo disponível negativo.
- [ ] As consultas e operações de estoque respeitarem o tenant autenticado.

**Status:** 🟢 Definido

---

## CA-EST-002 — Reservar peças automaticamente após aceitação do orçamento

A funcionalidade será considerada aceita quando:

- [ ] A aceitação do orçamento iniciar uma solicitação de reserva das quantidades de peças previstas.
- [ ] A reserva for realizada no Inventory Service, que será o responsável pelos registros e pela consistência do inventário.
- [ ] A reserva de todas as peças da solicitação ocorrer integralmente ou não ocorrer, sem reserva parcial.
- [ ] O sistema verificar a disponibilidade antes de confirmar a reserva.
- [ ] As operações utilizarem mecanismos de controle de concorrência para impedir que solicitações simultâneas reservem a mesma quantidade disponível.
- [ ] A operação de reserva for transacional dentro do banco de dados do Inventory Service.
- [ ] Cada solicitação possuir uma chave de idempotência para impedir processamento duplicado.
- [ ] A mesma solicitação não gerar reservas ou movimentações duplicadas quando for recebida mais de uma vez.
- [ ] A reserva reduzir o saldo disponível sem reduzir imediatamente o estoque físico.
- [ ] Cada quantidade reservada possuir rastreabilidade em relação à peça, à OS, ao orçamento e à movimentação correspondente.
- [ ] A condição física das peças ou unidades reservadas puder ser acompanhada, inicialmente como `PENDENTE_INSTALACAO`.
- [ ] A confirmação da reserva for comunicada aos serviços interessados.
- [ ] Uma reserva não confirmada impedir a aprovação operacional e o início da execução.
- [ ] Falhas na reserva não deixarem o inventário em estado parcialmente atualizado.

**Status:** 🟢 Definido

---

## CA-EST-003 — Converter reserva em consumo definitivo

A funcionalidade será considerada aceita quando:

- [ ] A utilização de uma peça reservada puder ser registrada durante a execução da OS.
- [ ] A peça utilizada tiver sua condição física atualizada para `INSTALADA` ou `CONSUMIDA`, conforme o caso.
- [ ] A conversão da reserva em consumo definitivo reduzir corretamente o estoque físico e a quantidade reservada.
- [ ] A operação não reduzir novamente o saldo disponível de uma peça que já estava reservada.
- [ ] A movimentação de consumo ficar vinculada à reserva original e à OS correspondente.
- [ ] O sistema impedir que a mesma reserva seja consumida mais de uma vez.
- [ ] O histórico da reserva e da movimentação definitiva permanecer disponível para auditoria.

**Status:** 🟢 Definido

---

## CA-EST-004 — Liberar peças reservadas não utilizadas

A funcionalidade será considerada aceita quando:

- [ ] Uma peça reservada e não utilizada puder ter sua reserva liberada.
- [ ] A liberação ocorrer por meio de uma movimentação auditável.
- [ ] A quantidade liberada deixar de compor o estoque reservado.
- [ ] O saldo disponível for atualizado corretamente após a liberação.
- [ ] O estoque físico não for aumentado pela liberação, pois a peça já permanecia fisicamente no inventário.
- [ ] A condição física da peça for verificada antes de seu retorno ao saldo comercializável.
- [ ] A mesma quantidade não puder ser liberada mais de uma vez.
- [ ] A movimentação de liberação permanecer vinculada à reserva original e à OS correspondente.

**Status:** 🟢 Definido

---

## CA-EST-005 — Cancelar OS e tratar perdas de estoque

A funcionalidade será considerada aceita quando:

- [ ] O cancelamento de uma OS com orçamento aprovado ou peças reservadas for identificado pelo sistema.
- [ ] O sistema consultar obrigatoriamente a condição física registrada para cada peça ou unidade associada à OS.
- [ ] Peças com condição física `PENDENTE_INSTALACAO` puderem ter sua reserva liberada, desde que estejam intactas e em condições de reutilização.
- [ ] A liberação atualizar corretamente a quantidade reservada e o saldo disponível, sem aumentar o estoque físico.
- [ ] Peças cuja condição física esteja marcada como `INSTALADA` tiverem o retorno ao estoque comercializável bloqueado.
- [ ] Peças cuja condição física esteja marcada como `INSTALADA` ou `CONSUMIDA` gerarem o registro correspondente de consumo, perda operacional ou descarte, conforme a situação.
- [ ] Peças danificadas ou sem condições de reutilização não retornarem ao estoque disponível.
- [ ] A movimentação original permanecer inalterada, mantendo o histórico completo de auditoria.
- [ ] A movimentação de liberação, consumo ou perda ficar vinculada à movimentação original e à OS correspondente.
- [ ] O sistema impedir que a mesma quantidade seja liberada e registrada como perda simultaneamente.
- [ ] O sistema impedir que a mesma movimentação seja liberada, consumida ou registrada como perda mais de uma vez.
- [ ] O cancelamento e o tratamento das reservas forem processados de forma consistente, sem deixar quantidades presas ou duplicadas.

**Status:** 🟢 Definido

---

# 9. Financeiro

## CA-FIN-001 — Calcular total da OS

A funcionalidade será considerada aceita quando:

```text
Total = Serviços + Peças - Descontos
```

- [ ] Os valores dos serviços e das peças forem considerados no cálculo.
- [ ] Os descontos aplicáveis forem subtraídos corretamente.
- [ ] O total for exibido corretamente na OS.
- [ ] Os valores calculados corresponderem ao orçamento aprovado.
- [ ] O cálculo respeitar as regras de precisão e arredondamento monetário definidas para o sistema.

**Status:** 🟢 Definido

---

## CA-FIN-002 — Registrar pagamento e acompanhar quitação

A funcionalidade será considerada aceita quando:

- [ ] For possível registrar pagamentos referentes à OS.
- [ ] O valor pago e o saldo financeiro pendente forem atualizados corretamente.
- [ ] O status financeiro refletir a situação real do pagamento.
- [ ] O sistema distinguir os estados financeiros `PENDENTE`, `PARCIAL` e `QUITADA`.
- [ ] Pagamentos parciais forem registrados sem indicar quitação total.
- [ ] A quitação ocorrer somente quando o valor total devido estiver integralmente pago.
- [ ] A OS puder assumir o status operacional `CONCLUIDA` independentemente de sua quitação financeira.
- [ ] A situação financeira permanecer consultável após a conclusão operacional da OS.
- [ ] Os pagamentos estiverem vinculados à OS e ao tenant correspondente.
- [ ] O sistema impedir registros duplicados de pagamento para uma mesma operação.

**Status:** 🟢 Definido

---

# 10. Histórico de Manutenção

## CA-HIS-001 — Consultar histórico

A funcionalidade será considerada aceita quando:

- [ ] For possível acessar o histórico de manutenção de um veículo pela placa.
- [ ] As OS anteriores forem listadas corretamente.
- [ ] Os diagnósticos, orçamentos, peças utilizadas e valores pagos puderem ser consultados.
- [ ] As informações históricas forem preservadas mesmo após o encerramento ou cancelamento de uma OS.
- [ ] As movimentações de reserva, consumo, liberação e perda puderem ser rastreadas quando relacionadas à OS.
- [ ] O histórico respeitar o isolamento de dados entre oficinas.

**Status:** 🟢 Definido

---

# 11. Autenticação e Permissões

## CA-AUT-001 — Login e autorização

A funcionalidade será considerada aceita quando:

- [ ] Usuários cadastrados puderem realizar autenticação no sistema.
- [ ] As credenciais forem armazenadas utilizando mecanismo seguro de hash de senha.
- [ ] O acesso às funcionalidades respeitar os perfis e as permissões estabelecidos: Atendente, Mecânico, Estoque e Financeiro.
- [ ] Usuários sem permissão forem impedidos de executar ações restritas.
- [ ] A autenticação identificar a oficina à qual o usuário pertence.
- [ ] O usuário não conseguir acessar dados de outro tenant por alteração de parâmetros, identificadores ou requisições.
- [ ] Operações críticas validarem a autorização no backend, não apenas na interface.

**Status:** 🟢 Definido

---

# 12. Notificações

## CA-NOT-001 — Gerar notificação

A funcionalidade será considerada aceita quando:

- [ ] Os eventos definidos para a funcionalidade gerarem as notificações correspondentes.
- [ ] As notificações forem associadas ao evento e ao usuário destinatário, quando aplicável.
- [ ] As notificações respeitarem o tenant correspondente.
- [ ] Falhas no envio de uma notificação não provocarem inconsistência nos registros operacionais.
- [ ] O processamento de eventos não gerar notificações duplicadas indevidamente.

**Status:** 🔵 Fora do MVP / validar

---

# 13. Relatórios

## CA-REL-001 — Consultar relatório

A funcionalidade será considerada aceita quando:

- [ ] Um usuário autorizado puder acessar informações consolidadas da operação.
- [ ] Os dados apresentados corresponderem aos registros existentes no sistema.
- [ ] Os relatórios respeitarem o isolamento de dados entre oficinas.
- [ ] Os indicadores utilizarem critérios de cálculo documentados.
- [ ] Os valores apresentados puderem ser rastreados até seus registros de origem.

**Status:** 🔵 Fora do MVP / validar

---

# 14. Critérios Gerais de Qualidade

## CA-GER-001 — Validação de dados

- [ ] Os campos obrigatórios forem validados.
- [ ] Dados inválidos forem rejeitados pelo sistema.
- [ ] As mensagens de erro forem compreensíveis para o usuário.
- [ ] As validações críticas forem realizadas no backend.
- [ ] Os dados recebidos forem validados antes de serem persistidos ou utilizados em operações entre serviços.

## CA-GER-002 — Persistência e integridade

- [ ] Os dados válidos forem persistidos corretamente nos bancos de dados PostgreSQL definidos para os serviços.
- [ ] Cada microsserviço mantiver a responsabilidade sobre seus próprios dados.
- [ ] As chaves estrangeiras forem utilizadas para garantir integridade referencial dentro do banco de dados de cada serviço.
- [ ] Referências a registros pertencentes a outros microsserviços forem validadas por APIs ou eventos, sem dependência de chaves estrangeiras entre bancos de serviços distintos.
- [ ] As operações de atualização e exclusão respeitarem as regras de integridade e rastreabilidade.
- [ ] Operações críticas não deixarem registros parcialmente atualizados em caso de falha.
- [ ] O isolamento entre tenants for mantido em todas as operações de persistência.

## CA-GER-003 — Autorização

- [ ] As ações restritas exigirem permissão adequada conforme o perfil do usuário.
- [ ] O sistema impedir acessos não autorizados.
- [ ] O sistema impedir acesso a dados pertencentes a outros tenants.
- [ ] A autorização for validada no backend em todas as operações protegidas.

## CA-GER-004 — Testes

- [ ] As funcionalidades críticas possuírem testes automatizados.
- [ ] Os testes contemplarem cenários de sucesso, erro, limite e concorrência, quando aplicável.
- [ ] Os testes de integração verificarem a comunicação entre os microsserviços envolvidos.
- [ ] Os testes contemplarem o isolamento entre oficinas.
- [ ] Os testes de estoque verificarem reserva integral, ausência de reserva parcial, idempotência e prevenção de consumo duplicado.
- [ ] Os testes definidos para a funcionalidade forem executados e aprovados.
- [ ] A cobertura global de testes automatizados atingir pelo menos 60% do código-fonte total dos microsserviços, conforme o critério estabelecido para o projeto.

## CA-GER-005 — Tratamento de erros

- [ ] Erros previsíveis forem tratados adequadamente.
- [ ] Mensagens de erro não expuserem dados sensíveis, credenciais ou informações internas desnecessárias.
- [ ] Falhas em operações críticas não deixarem os registros em estados inconsistentes.
- [ ] Falhas de comunicação entre serviços puderem ser identificadas e tratadas.
- [ ] Operações que possam ser repetidas utilizarem mecanismos de idempotência quando necessário.

## CA-GER-006 — Consistência de eventos entre microsserviços

- [ ] Eventos de operações críticas forem persistidos de maneira confiável antes de sua publicação.
- [ ] O padrão Transactional Outbox for utilizado nos fluxos em que a gravação de dados e a publicação de eventos precisarem permanecer consistentes.
- [ ] Consumidores tratarem mensagens repetidas de forma idempotente.
- [ ] O processamento de eventos preservar identificadores de correlação e de tenant.
- [ ] Falhas temporárias de comunicação permitirem novas tentativas sem duplicar efeitos de negócio.
- [ ] Mensagens que não puderem ser processadas forem tratadas por mecanismo de recuperação, como fila de mensagens não processadas (DLQ), quando aplicável.
- [ ] A comunicação assíncrona não for considerada concluída apenas porque uma mensagem foi enviada; o resultado do processamento deverá ser confirmado pelo serviço responsável.

## CA-GER-007 — Observabilidade

- [ ] Os serviços disponibilizarem registros de execução que permitam identificar erros e operações relevantes.
- [ ] Logs não armazenarem senhas, tokens ou dados sensíveis desnecessários.
- [ ] Operações críticas puderem ser rastreadas por identificadores de correlação.
- [ ] Métricas de disponibilidade e funcionamento dos serviços puderem ser coletadas.
- [ ] A arquitetura de monitoramento contemplar Prometheus e Grafana, conforme os requisitos técnicos do projeto.

**Status dos critérios gerais:** 🟢 Definido

---

# 15. Definição de Pronto (DoD)

Uma história de usuário será considerada pronta para entrega quando:

- [ ] Todos os critérios de aceite aplicáveis forem atendidos.
- [ ] A implementação estiver integrada à branch correspondente do repositório.
- [ ] Os testes automatizados definidos para a funcionalidade estiverem passando.
- [ ] Os testes de integração necessários estiverem executados e aprovados.
- [ ] A meta global de cobertura de testes de no mínimo 60% do código-fonte total dos microsserviços for atendida, conforme o critério de medição do projeto.
- [ ] Não houver erros bloqueadores conhecidos.
- [ ] A documentação necessária estiver atualizada.
- [ ] As alterações respeitarem as regras de multi-tenancy e segurança.
- [ ] O Pull Request tiver sido revisado pelo grupo GarageOps.
- [ ] As evidências de validação estiverem disponíveis para conferência.

**Status:** 🟢 Definido

---

# 16. Observações de Escopo e Validação

1. Os critérios deste documento descrevem o comportamento esperado, não comprovam que as funcionalidades já estejam implementadas.
2. A cobertura mínima de 60% deverá ser medida sobre o código-fonte total dos microsserviços, e não isoladamente por uma única funcionalidade.
3. A implementação de notificações e relatórios permanece fora do escopo inicial do MVP, sujeita à validação do grupo e do professor.
4. A separação dos serviços deverá respeitar a arquitetura definida para o MVP, mantendo a propriedade dos dados em cada microsserviço.
5. Alterações nos critérios de aceite deverão ser refletidas também nas histórias de usuário, regras de negócio, fluxos e backlog, evitando divergências entre os documentos.
6. A integração com API externa deverá possuir critérios específicos quando a API escolhida e sua finalidade forem formalmente definidas.

---

**Última atualização:** 16/11/2026

**Responsável:** Eloísa Brandão — Product Owner
