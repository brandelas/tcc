\# Histórias de Usuário — OficinaPro



> \*\*Documento de trabalho — sujeito à validação do grupo e do professor.\*\*

>

> Este documento transforma os requisitos levantados no backlog em histórias de usuário que poderão orientar o desenvolvimento do OficinaPro.

>

> Histórias, critérios e regras marcados como proposta ou pendência não representam decisões definitivas do grupo.



\---



\## 1. Legenda



| Status                   | Significado                               |

| ------------------------ | ----------------------------------------- |

| 🟢 Definido              | Entendimento já estabelecido              |

| 🟡 Em discussão          | Proposta ainda não validada pelo grupo    |

| 🔵 Validar com professor | Depende de orientação/validação acadêmica |

| 🔴 Pendente              | Informação ou decisão ainda necessária    |

| ⚪ Pós-MVP                | Possível funcionalidade futura            |



\---



\# 2. Clientes



\## HU-CLI-001 — Cadastrar cliente



\*\*Como\*\* atendente

\*\*Quero\*\* cadastrar um cliente

\*\*Para\*\* manter seus dados disponíveis para utilização no sistema.



\### Critérios de aceite — proposta



\* \[ ] Permitir informar nome do cliente.

\* \[ ] Permitir informar CPF.

\* \[ ] Permitir informar telefone.

\* \[ ] Permitir informar e-mail.

\* \[ ] Permitir informar endereço.

\* \[ ] Validar os campos obrigatórios definidos pelo sistema.

\* \[ ] Impedir cadastro duplicado conforme regra que será definida.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Definir quais campos serão obrigatórios.

\* Definir regra para CPF duplicado.

\* Definir se cliente poderá ser excluído ou apenas inativado.



\---



\## HU-CLI-002 — Consultar cliente



\*\*Como\*\* atendente

\*\*Quero\*\* consultar os dados de um cliente

\*\*Para\*\* localizar rapidamente suas informações e seus veículos.



\### Critérios de aceite — proposta



\* \[ ] Permitir buscar cliente por nome.

\* \[ ] Permitir buscar cliente por CPF.

\* \[ ] Exibir os dados cadastrados.

\* \[ ] Exibir os veículos associados ao cliente.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-CLI-003 — Atualizar cliente



\*\*Como\*\* atendente

\*\*Quero\*\* atualizar os dados de um cliente

\*\*Para\*\* manter suas informações atualizadas.



\### Critérios de aceite — proposta



\* \[ ] Permitir alterar dados permitidos.

\* \[ ] Manter a identificação do cliente.

\* \[ ] Validar os campos alterados.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 3. Veículos



\## HU-VEI-001 — Cadastrar veículo



\*\*Como\*\* atendente

\*\*Quero\*\* cadastrar um veículo vinculado a um cliente

\*\*Para\*\* registrar os veículos atendidos pela oficina.



\### Critérios de aceite — proposta



\* \[ ] Informar placa.

\* \[ ] Informar marca.

\* \[ ] Informar modelo.

\* \[ ] Informar ano.

\* \[ ] Informar cor.

\* \[ ] Informar quilometragem.

\* \[ ] Associar o veículo a um cliente.

\* \[ ] Validar os campos obrigatórios.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Definir quais campos são obrigatórios.

\* Definir regra de unicidade da placa.

\* Definir como será tratada a mudança de proprietário.



\---



\## HU-VEI-002 — Consultar veículo



\*\*Como\*\* atendente

\*\*Quero\*\* consultar os dados de um veículo

\*\*Para\*\* verificar suas informações e histórico de manutenção.



\### Critérios de aceite — proposta



\* \[ ] Permitir localizar o veículo pela placa.

\* \[ ] Exibir os dados do veículo.

\* \[ ] Exibir o proprietário associado.

\* \[ ] Permitir acessar o histórico de manutenções.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 4. Ordem de Serviço



\## HU-OS-001 — Abrir ordem de serviço



\*\*Como\*\* atendente

\*\*Quero\*\* abrir uma ordem de serviço para um veículo

\*\*Para\*\* registrar formalmente um atendimento da oficina.



\### Critérios de aceite — proposta



\* \[ ] Selecionar cliente.

\* \[ ] Selecionar veículo.

\* \[ ] Registrar data de abertura.

\* \[ ] Registrar descrição inicial do problema.

\* \[ ] Gerar identificação da ordem de serviço.

\* \[ ] Definir o status inicial da OS.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\*



\* Definir quais informações serão obrigatórias na abertura.



\---



\## HU-OS-002 — Consultar ordem de serviço



\*\*Como\*\* usuário autorizado

\*\*Quero\*\* consultar uma ordem de serviço

\*\*Para\*\* acompanhar seu andamento.



\### Critérios de aceite — proposta



\* \[ ] Localizar a OS pelo identificador.

\* \[ ] Exibir cliente e veículo.

\* \[ ] Exibir status atual.

\* \[ ] Exibir diagnóstico.

\* \[ ] Exibir orçamento.

\* \[ ] Exibir serviços e peças relacionados.

\* \[ ] Exibir informações financeiras pertinentes.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-OS-003 — Atualizar status da OS



\*\*Como\*\* usuário autorizado

\*\*Quero\*\* atualizar o status de uma ordem de serviço

\*\*Para\*\* representar corretamente sua etapa atual.



\### Status inicialmente propostos



```text

ABERTA

EM\_DIAGNOSTICO

AGUARDANDO\_APROVACAO

APROVADA

EM\_EXECUCAO

AGUARDANDO\_PAGAMENTO

CONCLUIDA

RECUSADA

```



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Validar os status com o grupo.

\* Validar a necessidade de todos os estados.

\* Definir quais transições são permitidas.



\---



\# 5. Diagnóstico



\## HU-DIA-001 — Registrar diagnóstico



\*\*Como\*\* mecânico

\*\*Quero\*\* registrar o diagnóstico do veículo

\*\*Para\*\* documentar os problemas identificados e orientar o orçamento.



\### Critérios de aceite — proposta



\* \[ ] Permitir registrar descrição do diagnóstico.

\* \[ ] Associar o diagnóstico à OS.

\* \[ ] Registrar o responsável pelo diagnóstico.

\* \[ ] Permitir consultar o diagnóstico posteriormente.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 6. Orçamento



\## HU-ORC-001 — Elaborar orçamento



\*\*Como\*\* usuário autorizado

\*\*Quero\*\* elaborar um orçamento para a OS

\*\*Para\*\* apresentar ao cliente os serviços e peças necessários.



\### Critérios de aceite — proposta



\* \[ ] Adicionar serviços.

\* \[ ] Adicionar peças.

\* \[ ] Informar quantidades.

\* \[ ] Calcular valores.

\* \[ ] Calcular o valor total.

\* \[ ] Permitir aplicar desconto conforme regra definida.

\* \[ ] Associar o orçamento à OS.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-ORC-002 — Aprovar ou recusar orçamento



\*\*Como\*\* usuário autorizado

\*\*Quero\*\* registrar a aprovação ou recusa do orçamento

\*\*Para\*\* definir se o serviço poderá prosseguir.



\### Critérios de aceite — proposta



\* \[ ] Registrar aprovação ou recusa.

\* \[ ] Registrar data da decisão.

\* \[ ] Atualizar o status da OS.

\* \[ ] Manter o registro da decisão.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Definir quem pode aprovar.

\* Definir comportamento da OS após recusa.

\* Definir se o orçamento poderá ser alterado após aprovação.



\---



\# 7. Estoque



\## HU-EST-001 — Cadastrar peça



\*\*Como\*\* responsável pelo estoque

\*\*Quero\*\* cadastrar uma peça

\*\*Para\*\* controlar os itens disponíveis na oficina.



\### Critérios de aceite — proposta



\* \[ ] Registrar nome/descrição da peça.

\* \[ ] Registrar quantidade disponível.

\* \[ ] Registrar valor.

\* \[ ] Permitir consultar o estoque.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-EST-002 — Atualizar estoque



\*\*Como\*\* responsável pelo estoque

\*\*Quero\*\* registrar entradas e saídas de peças

\*\*Para\*\* manter a quantidade disponível atualizada.



\### Critérios de aceite — proposta



\* \[ ] Registrar entrada de peça.

\* \[ ] Registrar saída de peça.

\* \[ ] Atualizar quantidade disponível.

\* \[ ] Registrar a movimentação.

\* \[ ] Impedir saída superior ao estoque disponível.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-EST-003 — Baixar peças utilizadas



\*\*Como\*\* sistema

\*\*Quero\*\* registrar a saída das peças utilizadas em uma OS

\*\*Para\*\* manter o estoque atualizado.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência principal:\*\*



\* Definir em qual momento a baixa do estoque ocorre.



\---



\# 8. Financeiro



\## HU-FIN-001 — Calcular valor da OS



\*\*Como\*\* sistema

\*\*Quero\*\* calcular o valor total da ordem de serviço

\*\*Para\*\* apresentar corretamente o valor devido.



\### Regra proposta



```text

Total = Serviços + Peças - Descontos

```



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-FIN-002 — Registrar pagamento



\*\*Como\*\* usuário autorizado

\*\*Quero\*\* registrar o pagamento de uma OS

\*\*Para\*\* manter o controle financeiro do atendimento.



\### Critérios de aceite — proposta



\* \[ ] Registrar valor pago.

\* \[ ] Registrar forma de pagamento.

\* \[ ] Registrar data do pagamento.

\* \[ ] Atualizar o status financeiro.

\* \[ ] Permitir consultar o pagamento registrado.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Definir formas de pagamento.

\* Definir se haverá pagamento parcial.

\* Definir se haverá parcelamento.



\---



\# 9. Histórico de Manutenção



\## HU-HIS-001 — Consultar histórico do veículo



\*\*Como\*\* atendente

\*\*Quero\*\* consultar o histórico de manutenção de um veículo

\*\*Para\*\* verificar os serviços realizados anteriormente.



\### Critérios de aceite — proposta



\* \[ ] Listar ordens de serviço anteriores.

\* \[ ] Exibir datas dos atendimentos.

\* \[ ] Exibir serviços realizados.

\* \[ ] Exibir informações relevantes sobre peças utilizadas.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 10. Usuários e permissões



\## HU-AUT-001 — Autenticar usuário



\*\*Como\*\* usuário do sistema

\*\*Quero\*\* realizar login

\*\*Para\*\* acessar as funcionalidades permitidas para meu perfil.



\*\*Status:\*\* 🟡 Em discussão



\---



\## HU-AUT-002 — Controlar permissões



\*\*Como\*\* administrador

\*\*Quero\*\* definir permissões de acesso

\*\*Para\*\* restringir funcionalidades de acordo com o papel de cada usuário.



\### Perfis inicialmente propostos



\* Administrador

\* Atendente

\* Mecânico

\* Estoque

\* Financeiro



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\*



\* Validar necessidade e permissões de cada perfil.



\---



\# 11. Notificações



\## HU-NOT-001 — Notificar eventos da OS



\*\*Como\*\* usuário

\*\*Quero\*\* receber notificações sobre eventos relevantes da OS

\*\*Para\*\* acompanhar mudanças importantes no atendimento.



\### Exemplos de eventos



\* Orçamento aguardando aprovação.

\* Orçamento aprovado.

\* Orçamento recusado.

\* OS concluída.

\* Pagamento pendente.



\*\*Status:\*\* 🔵 Validar com professor



\*\*Pendência:\*\*



\* Definir quais notificações fazem parte do MVP.

\* Definir canal de notificação.



\---



\# 12. Relatórios



\## HU-REL-001 — Consultar informações operacionais



\*\*Como\*\* administrador

\*\*Quero\*\* consultar informações consolidadas da oficina

\*\*Para\*\* acompanhar a operação.



\*\*Status:\*\* 🔵 Validar com professor



\*\*Pendência:\*\*



\* Definir se relatórios fazem parte do MVP.

\* Definir quais indicadores serão necessários.



\---



\# 13. Funcionalidades fora do escopo inicial



As funcionalidades abaixo são possibilidades futuras e não devem ser consideradas parte do MVP até validação:



\* Integração com sistemas externos.

\* Notificações por canais adicionais.

\* Relatórios avançados.

\* Dashboard analítico avançado.

\* Funcionalidades não relacionadas ao fluxo principal da oficina.



\*\*Status:\*\* ⚪ Pós-MVP



\---



\# 14. Observações



Este documento representa o levantamento inicial das histórias de usuário do OficinaPro.



As histórias e critérios marcados como proposta deverão ser discutidos e validados pelo grupo. Decisões que dependam de orientação acadêmica deverão ser levadas aos encontros com o professor.



A implementação deve considerar o MVP validado pelo grupo e pelo professor, evitando desenvolver funcionalidades ainda não priorizadas.



