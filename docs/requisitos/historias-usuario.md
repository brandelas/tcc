# Histórias de Usuário — OficinaPro

> **Documento de trabalho — Grupo GarageOps**
>
> Este documento transforma os requisitos levantados no backlog em histórias de usuário que orientam o desenvolvimento do OficinaPro, considerando as regras de negócio definidas para o MVP.
>
> As histórias descrevem as necessidades dos usuários e os comportamentos esperados do sistema. Seus critérios de aceite devem orientar a implementação e os testes funcionais.

---

## 1. Legenda

| Status | Significado |
|---|---|
| 🟢 **Definido** | Entendimento estabelecido como direção do MVP. |
| 🟡 **Em discussão** | Proposta ainda não validada pelo grupo. |
| 🔵 **Validar com professor** | Depende de orientação ou validação acadêmica. |
| 🔴 **Pendente** | Informação ou decisão ainda necessária. |
| ⚪ **Pós-MVP** | Possível funcionalidade futura. |

---

## 2. Oficinas e isolamento de dados (Multi-tenancy)

## HU-TEN-001 — Acessar os dados da própria oficina

**Como** usuário vinculado a uma oficina

**Quero** acessar somente os dados da oficina à qual tenho autorização

**Para** manter a privacidade e o isolamento das informações de cada estabelecimento.

### Critérios de aceite

- [ ] Associar os registros operacionais à oficina correspondente (`tenant_id`).
- [ ] Identificar a oficina a partir do contexto autenticado do usuário.
- [ ] Impedir que o usuário consulte ou altere dados de outra oficina sem autorização.
- [ ] Aplicar o isolamento nas consultas, cadastros, alterações e movimentações.
- [ ] Validar o vínculo do usuário com a oficina antes de executar operações.
- [ ] Não confiar exclusivamente em um `tenant_id` enviado pelo frontend.
- [ ] Permitir que oficinas diferentes cadastrem clientes com o mesmo CPF.
- [ ] Permitir que oficinas diferentes cadastrem veículos com a mesma placa.

**Status:** 🟢 Definido

---

## 3. Clientes

## HU-CLI-001 — Cadastrar cliente

**Como** atendente

**Quero** cadastrar um cliente

**Para** manter seus dados disponíveis para utilização no sistema.

### Critérios de aceite

- [ ] Permitir informar nome do cliente.
- [ ] Permitir informar CPF válido e obrigatório.
- [ ] Permitir informar telefone.
- [ ] Permitir informar e-mail.
- [ ] Permitir informar endereço.
- [ ] Validar os campos obrigatórios definidos pelo sistema.
- [ ] Impedir cadastro duplicado com base no CPF dentro da mesma oficina.
- [ ] Permitir que o mesmo CPF exista em oficinas diferentes.
- [ ] Associar o cadastro ao tenant autenticado.
- [ ] Impedir que um usuário cadastre clientes em uma oficina à qual não possui vínculo autorizado.

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
- [ ] Exibir somente clientes pertencentes à oficina do usuário autenticado.
- [ ] Impedir a consulta de clientes de outros tenants sem autorização.

**Status:** 🟢 Definido

---

## HU-CLI-003 — Atualizar cliente

**Como** atendente

**Quero** atualizar os dados de um cliente

**Para** manter suas informações atualizadas.

### Critérios de aceite

- [ ] Permitir alterar os dados cadastrais autorizados.
- [ ] Manter a identificação do cliente.
- [ ] Validar os campos alterados.
- [ ] Impedir alterações em registros pertencentes a outra oficina.
- [ ] Preservar os vínculos com ordens de serviço e histórico.
- [ ] Permitir inativação lógica quando o cliente não puder mais ser utilizado em novos atendimentos, sem apagar seu histórico.

**Status:** 🟢 Definido

---

## 4. Veículos

## HU-VEI-001 — Cadastrar veículo

**Como** atendente

**Quero** cadastrar um veículo vinculado a um cliente

**Para** registrar os veículos atendidos pela oficina.

### Critérios de aceite

- [ ] Informar placa obrigatória.
- [ ] Informar marca.
- [ ] Informar modelo.
- [ ] Informar ano.
- [ ] Informar cor.
- [ ] Informar quilometragem.
- [ ] Associar o veículo a um cliente cadastrado na mesma oficina.
- [ ] Validar os campos obrigatórios.
- [ ] Impedir duplicidade de placa dentro da mesma oficina.
- [ ] Permitir que a mesma placa exista em oficinas diferentes.
- [ ] Associar o veículo ao tenant correspondente.

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
- [ ] Exibir somente veículos pertencentes à oficina do usuário autenticado.

**Status:** 🟢 Definido

---

## 5. Ordem de Serviço

## HU-OS-001 — Abrir ordem de serviço

**Como** atendente

**Quero** abrir uma ordem de serviço para um veículo

**Para** registrar formalmente um atendimento da oficina.

### Critérios de aceite

- [ ] Selecionar cliente.
- [ ] Selecionar veículo pertencente ao cliente selecionado.
- [ ] Validar se cliente e veículo pertencem à mesma oficina.
- [ ] Registrar data de abertura.
- [ ] Registrar descrição inicial do problema.
- [ ] Gerar identificação única da ordem de serviço.
- [ ] Definir o status inicial como `ABERTA`.
- [ ] Associar a OS ao tenant correspondente.

**Status:** 🟢 Definido

---

## HU-OS-002 — Consultar ordem de serviço

**Como** usuário autorizado

**Quero** consultar uma ordem de serviço

**Para** acompanhar seu andamento.

### Critérios de aceite

- [ ] Localizar a OS pelo identificador.
- [ ] Exibir cliente e veículo.
- [ ] Exibir o status operacional atual.
- [ ] Exibir diagnóstico.
- [ ] Exibir orçamento e suas versões.
- [ ] Exibir decisões do cliente sobre os orçamentos.
- [ ] Exibir serviços e peças relacionados.
- [ ] Exibir informações de reserva e utilização das peças.
- [ ] Exibir informações financeiras pertinentes separadamente do status operacional.
- [ ] Restringir a consulta às OS da oficina autorizada do usuário.

**Status:** 🟢 Definido

---

## HU-OS-003 — Atualizar status da OS

**Como** usuário autorizado

**Quero** atualizar o status de uma ordem de serviço

**Para** representar corretamente sua etapa operacional.

### Estados operacionais previstos

```text
ABERTA
EM_DIAGNOSTICO
AGUARDANDO_APROVACAO
AGUARDANDO_RESERVA
EM_EXECUCAO
CONCLUIDA
CANCELADA
```

### Critérios de aceite

- [ ] Permitir somente transições previstas nas regras de negócio.
- [ ] Impedir a transição para `EM_EXECUCAO` sem aprovação operacional do orçamento e confirmação da reserva integral das peças.
- [ ] Permitir o retorno para `AGUARDANDO_APROVACAO` quando houver um novo orçamento revisado após uma recusa.
- [ ] Permitir a transição para `AGUARDANDO_RESERVA` após a aceitação do orçamento pelo cliente e o envio da solicitação de reserva.
- [ ] Manter a OS bloqueada para execução caso a reserva seja recusada.
- [ ] Permitir o encerramento definitivo da OS como `CANCELADA`, respeitando o tratamento das reservas existentes.
- [ ] Permitir a conclusão operacional como `CONCLUIDA` quando os serviços forem encerrados e as reservas estiverem devidamente tratadas.
- [ ] Não exigir quitação financeira para concluir operacionalmente a OS.
- [ ] Preservar o histórico das mudanças de estado.

**Status:** 🟢 Definido

**Observação:** `APROVADA` e `RECUSADA` são decisões relacionadas ao orçamento, e não estados operacionais da OS. A situação financeira também deve ser acompanhada separadamente.

---

## 6. Diagnóstico

## HU-DIA-001 — Registrar diagnóstico

**Como** mecânico

**Quero** registrar o diagnóstico do veículo

**Para** documentar os problemas identificados e orientar o orçamento.

### Critérios de aceite

- [ ] Permitir registrar descrição do diagnóstico técnico.
- [ ] Associar o diagnóstico à OS.
- [ ] Atualizar o status da OS para `EM_DIAGNOSTICO`, respeitando as transições permitidas.
- [ ] Registrar o responsável pelo diagnóstico.
- [ ] Permitir consultar o diagnóstico posteriormente.
- [ ] Restringir a operação às OS pertencentes à oficina autorizada.

**Status:** 🟢 Definido

---

## 7. Orçamento

## HU-ORC-001 — Elaborar orçamento

**Como** usuário autorizado

**Quero** elaborar um orçamento para a OS

**Para** apresentar ao cliente os serviços e peças necessários.

### Critérios de aceite

- [ ] Adicionar serviços.
- [ ] Adicionar peças.
- [ ] Informar quantidades.
- [ ] Informar valores dos itens.
- [ ] Calcular valores automaticamente.
- [ ] Calcular o valor total: `(Serviços + Peças) - Descontos autorizados`.
- [ ] Permitir aplicar desconto conforme regra definida.
- [ ] Associar o orçamento à OS.
- [ ] Atualizar o status operacional da OS para `AGUARDANDO_APROVACAO`.
- [ ] Preservar versões anteriores quando houver revisão do orçamento.

**Status:** 🟢 Definido

---

## HU-ORC-002 — Registrar aceitação ou recusa do orçamento

**Como** usuário autorizado

**Quero** registrar a decisão do cliente sobre o orçamento

**Para** definir se o atendimento poderá prosseguir.

### Critérios de aceite

- [ ] Registrar a decisão formal do cliente: aceitação ou recusa.
- [ ] Registrar a data e a hora da decisão.
- [ ] Registrar o responsável pelo registro da decisão.
- [ ] Preservar o histórico das decisões anteriores.
- [ ] Em caso de recusa, permitir o encerramento da OS ou a elaboração de um orçamento revisado vinculado à mesma OS.
- [ ] Permitir que um orçamento revisado retorne para `AGUARDANDO_APROVACAO`.
- [ ] Em caso de aceitação, encaminhar a solicitação de reserva integral das peças ao Inventory Service.
- [ ] Atualizar o status da OS para `AGUARDANDO_RESERVA` enquanto aguarda o resultado da reserva.
- [ ] Não liberar a execução apenas com a aceitação do cliente.
- [ ] Registrar a aprovação operacional do orçamento somente após a confirmação da reserva integral.

**Status:** 🟢 Definido

**Observação:** A aceitação do cliente e a aprovação operacional do orçamento são etapas distintas. A permissão para registrar decisões deverá respeitar o perfil autorizado.

---

## 8. Estoque

## HU-EST-001 — Cadastrar peça

**Como** responsável pelo estoque

**Quero** cadastrar uma peça

**Para** controlar os itens disponíveis na oficina.

### Critérios de aceite

- [ ] Registrar nome ou descrição da peça.
- [ ] Registrar quantidade física inicial, quando aplicável.
- [ ] Registrar valor unitário.
- [ ] Associar a peça à oficina correspondente.
- [ ] Permitir consultar o estoque.
- [ ] Impedir acesso a peças pertencentes a outra oficina.

**Status:** 🟢 Definido

---

## HU-EST-002 — Registrar movimentações de estoque

**Como** responsável pelo estoque

**Quero** registrar entradas e acompanhar movimentações de peças

**Para** manter o controle das quantidades físicas e disponíveis na oficina.

### Critérios de aceite

- [ ] Registrar entrada de peças.
- [ ] Registrar a quantidade recebida.
- [ ] Atualizar o estoque físico após a entrada.
- [ ] Registrar a movimentação no histórico de estoque.
- [ ] Permitir consultar o estoque físico, reservado e disponível.
- [ ] Calcular o estoque disponível considerando as reservas ativas.
- [ ] Impedir que uma saída definitiva seja registrada acima da quantidade física existente.
- [ ] Preservar o histórico das movimentações.
- [ ] Restringir as operações ao tenant correspondente.

**Regra de cálculo**

```text
Estoque Disponível = Estoque Físico - Estoque Reservado
```

**Status:** 🟢 Definido

---

## HU-EST-003 — Reservar, utilizar ou liberar peças vinculadas ao orçamento

**Como** sistema

**Quero** controlar a reserva e a utilização das peças vinculadas a uma OS

**Para** garantir a disponibilidade dos itens, evitar saídas duplicadas e preservar a rastreabilidade do estoque.

### Critérios de aceite

- [ ] Solicitar a reserva das peças após a aceitação do orçamento pelo cliente.
- [ ] Validar a disponibilidade de todas as peças solicitadas no Inventory Service.
- [ ] Efetivar a reserva de todas as peças ou recusar a solicitação integralmente.
- [ ] Não manter reserva parcial decorrente de uma solicitação recusada.
- [ ] Impedir que a aprovação operacional do orçamento ocorra antes da confirmação da reserva integral.
- [ ] Atualizar a quantidade reservada e o saldo disponível após uma reserva confirmada.
- [ ] Manter o estoque físico inalterado no momento da reserva.
- [ ] Registrar cada reserva vinculando-a à respectiva OS, orçamento e tenant.
- [ ] Utilizar controle de concorrência para impedir que solicitações simultâneas reservem a mesma quantidade disponível.
- [ ] Utilizar chave de idempotência para impedir que o reprocessamento da mesma solicitação gere reservas duplicadas.
- [ ] Converter a reserva em saída definitiva quando a peça for efetivamente utilizada.
- [ ] Reduzir o estoque físico e a quantidade reservada de maneira consistente durante a conversão.
- [ ] Liberar a reserva de peças que não forem utilizadas e estiverem em condições de reutilização.
- [ ] Não aumentar o estoque físico ao liberar uma reserva, pois a peça não havia sido retirada fisicamente.
- [ ] Não devolver ao estoque comercializável peças instaladas ou consumidas.
- [ ] Registrar perdas, avarias ou descartes sem devolver indevidamente as quantidades ao saldo disponível.
- [ ] Preservar a movimentação original e registrar operações de liberação, consumo, perda ou descarte separadamente.
- [ ] Impedir que a mesma reserva seja consumida ou liberada mais de uma vez.

**Status:** 🟢 Definido

---

## 9. Financeiro

## HU-FIN-001 — Calcular valor da OS

**Como** sistema

**Quero** calcular o valor total da ordem de serviço

**Para** apresentar corretamente o valor devido.

### Regra aplicada

```text
Total = Serviços + Peças - Descontos autorizados
```

### Critérios de aceite

- [ ] Somar os valores dos serviços.
- [ ] Somar os valores das peças.
- [ ] Aplicar descontos autorizados.
- [ ] Apresentar o valor total calculado.
- [ ] Manter o valor relacionado ao orçamento correspondente.

**Status:** 🟢 Definido

---

## HU-FIN-002 — Registrar pagamento

**Como** usuário autorizado do perfil Financeiro

**Quero** registrar o pagamento de uma OS

**Para** manter o controle financeiro do atendimento.

### Critérios de aceite

- [ ] Registrar valor pago.
- [ ] Registrar forma de pagamento.
- [ ] Registrar data do pagamento.
- [ ] Associar o pagamento à OS e ao tenant correspondente.
- [ ] Atualizar a situação financeira conforme os valores recebidos.
- [ ] Permitir identificar valores pendentes, pagamentos parciais e quitação total.
- [ ] Permitir consultar os pagamentos registrados.
- [ ] Preservar o histórico de pagamentos.
- [ ] Não exigir quitação integral para que a OS seja considerada operacionalmente `CONCLUIDA`.
- [ ] Manter a situação financeira independente do estado operacional da OS.

### Situações financeiras previstas

| Situação | Significado |
|---|---|
| `PENDENTE` | Valor ainda não pago ou sem pagamento registrado. |
| `PARCIAL` | Parte do valor devido foi paga. |
| `QUITADA` | Valor total devido foi pago. |

**Status:** 🟢 Definido

---

## 10. Histórico de Manutenção

## HU-HIS-001 — Consultar histórico do veículo

**Como** atendente

**Quero** consultar o histórico de manutenção de um veículo

**Para** verificar os serviços realizados anteriormente.

### Critérios de aceite

- [ ] Listar ordens de serviço associadas ao veículo.
- [ ] Exibir datas dos atendimentos.
- [ ] Exibir serviços realizados.
- [ ] Exibir diagnósticos registrados.
- [ ] Exibir orçamentos e decisões relevantes.
- [ ] Exibir informações sobre peças utilizadas.
- [ ] Exibir informações financeiras pertinentes, separadas da situação operacional.
- [ ] Preservar o histórico mesmo após alterações de proprietário.
- [ ] Restringir a consulta aos dados da oficina autorizada.

**Status:** 🟢 Definido

---

## 11. Usuários e permissões

## HU-AUT-001 — Autenticar usuário

**Como** usuário do sistema

**Quero** realizar login

**Para** acessar as funcionalidades permitidas para meu perfil e minha oficina.

### Critérios de aceite

- [ ] Permitir autenticação com credenciais válidas.
- [ ] Rejeitar credenciais inválidas.
- [ ] Identificar o usuário autenticado.
- [ ] Identificar a oficina à qual o usuário possui vínculo autorizado.
- [ ] Disponibilizar o contexto de autenticação necessário para os demais serviços.
- [ ] Não expor senhas ou credenciais em registros operacionais.

**Status:** 🟢 Definido

---

## HU-AUT-002 — Controlar permissões

**Como** responsável pela gestão de acessos da oficina

**Quero** controlar as permissões dos usuários

**Para** restringir funcionalidades de acordo com o papel de cada usuário.

### Perfis adotados

- **Atendente:** atividades de atendimento e cadastro de clientes, veículos e ordens de serviço, conforme permissões estabelecidas.
- **Mecânico:** consulta e atualização de informações técnicas, diagnósticos e execução dos serviços.
- **Estoque:** cadastro, consulta e controle de peças, reservas e movimentações de estoque.
- **Financeiro:** consulta e registro de pagamentos e informações financeiras.

### Critérios de aceite

- [ ] Associar usuários aos perfis definidos.
- [ ] Restringir funcionalidades conforme as permissões do perfil.
- [ ] Validar permissões no backend.
- [ ] Impedir que a interface seja a única camada de controle de acesso.
- [ ] Validar também o vínculo do usuário com o tenant.
- [ ] Impedir que um usuário acesse dados de outra oficina sem autorização.

**Status:** 🟢 Definido

**Pendente:** detalhar a matriz de permissões por operação e endpoint, conforme a divisão de responsabilidades definida pelo grupo.

---

## 12. Notificações

## HU-NOT-001 — Notificar eventos da OS

**Como** usuário

**Quero** receber notificações sobre eventos relevantes da OS

**Para** acompanhar mudanças importantes no atendimento.

### Possíveis eventos

- Orçamento aceito ou recusado.
- Reserva de peças confirmada ou recusada.
- Execução iniciada.
- OS concluída.

**Status:** ⚪ Pós-MVP

---

## 13. Relatórios

## HU-REL-001 — Consultar informações operacionais

**Como** usuário autorizado

**Quero** consultar informações consolidadas da oficina

**Para** acompanhar a operação.

### Possíveis informações

- Quantidade de ordens de serviço.
- Situação operacional das OS.
- Situação financeira dos atendimentos.
- Posição de estoque físico, reservado e disponível.

**Status:** ⚪ Pós-MVP — escopo e prioridade a validar com o grupo e o professor.

---

## 14. Funcionalidades fora do escopo inicial

As funcionalidades abaixo não integram o escopo essencial do MVP, podendo ser avaliadas em etapas futuras:

- Integração com sistemas externos de pagamento.
- Notificações avançadas por canais externos.
- Relatórios gerenciais complexos.
- Dashboard analítico avançado.
- Gestão de planos, assinaturas e cobrança das oficinas pelo uso do SaaS.
- Decomposição adicional dos serviços de notificação e relatórios, caso a evolução do produto justifique.

**Status:** ⚪ Pós-MVP

---

## 15. Observações e alinhamento documental

Este documento descreve as histórias de usuário do OficinaPro e deve permanecer alinhado aos seguintes documentos:

- `backlog-requisitos.md`: requisitos funcionais e não funcionais.
- `regras-negocio.md`: regras e restrições do domínio.
- `fluxos.md`: sequência dos processos operacionais e exceções.
- `criterios-aceite.md`: validações e cenários de teste.
- `glossario.md`: definições dos termos utilizados.

### Decisões refletidas nesta revisão

1. Cada oficina possui isolamento de dados por tenant.
2. CPF e placa são únicos dentro da oficina, não globalmente.
3. A aceitação do cliente inicia a solicitação de reserva, mas não libera diretamente a execução.
4. A reserva de peças é integral: todas as peças são reservadas ou nenhuma é reservada.
5. A reserva não representa saída física definitiva.
6. A utilização da peça converte a reserva em saída definitiva.
7. Peças não utilizadas podem ter sua reserva liberada quando estiverem em condições de reutilização.
8. A situação financeira é independente da conclusão operacional da OS.
9. Os perfis operacionais do MVP são Atendente, Mecânico, Estoque e Financeiro.

**Última atualização:** 16/11/2026

**Responsável pela organização documental: Eloísa Brandão** Grupo GarageOps
