\# Regras de Negócio — OficinaPro



> \*\*Documento de trabalho — sujeito à validação do grupo e do professor.\*\*

>

> As regras abaixo representam o levantamento inicial das regras de negócio identificadas para o OficinaPro. Regras marcadas como proposta ou em discussão ainda não são decisões definitivas do grupo.



\---



\# 1. Legenda



| Status                   | Significado                                |

| ------------------------ | ------------------------------------------ |

| 🟢 Definida              | Regra validada pelo grupo                  |

| 🟡 Em discussão          | Proposta ainda não validada                |

| 🔵 Validar com professor | Depende de orientação acadêmica            |

| 🔴 Pendente              | Informação necessária para definir a regra |

| ⚪ Pós-MVP                | Regra relacionada a funcionalidade futura  |



\---



\# 2. Clientes



\## RN-CLI-001 — Identificação do cliente



Cada cliente deve possuir uma identificação única no sistema.



\*\*Proposta:\*\* utilizar CPF como identificador de negócio.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* validar se CPF será obrigatório e se poderá ser utilizado como identificador único.



\---



\## RN-CLI-002 — Cadastro duplicado



O sistema não deve permitir a criação de cadastros duplicados conforme os critérios de identificação definidos.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir quais campos serão utilizados para identificar duplicidade.



\---



\## RN-CLI-003 — Inativação de cliente



Caso um cliente não possa ser excluído devido à existência de histórico de atendimentos, deverá ser avaliada a utilização de inativação em vez de exclusão física.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 3. Veículos



\## RN-VEI-001 — Associação entre veículo e cliente



Todo veículo cadastrado deve estar associado a um cliente.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-VEI-002 — Identificação do veículo



A placa poderá ser utilizada como identificador de negócio do veículo.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* validar tratamento de placas inválidas, antigas ou alterações cadastrais.



\---



\## RN-VEI-003 — Histórico do veículo



As informações de manutenção devem permanecer associadas ao veículo para permitir a consulta de atendimentos anteriores.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-VEI-004 — Alteração de proprietário



Caso um veículo seja associado a outro proprietário, o histórico de manutenção deverá ser preservado.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir como a alteração será registrada.



\---



\# 4. Ordem de Serviço



\## RN-OS-001 — Identificação da OS



Cada ordem de serviço deve possuir um identificador único.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-OS-002 — Associação da OS



Uma ordem de serviço deve estar vinculada a um cliente e a um veículo.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-OS-003 — Status da OS



A ordem de serviço deve possuir um status que represente sua etapa atual no processo.



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



\---



\## RN-OS-004 — Transição de status



A OS não deve poder assumir qualquer status de forma arbitrária. As mudanças deverão respeitar as transições permitidas pelo fluxo definido.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir a matriz de transições válida.



\---



\## RN-OS-005 — Registro de alterações



Alterações relevantes no estado da OS deverão ser registradas para permitir rastreabilidade.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 5. Diagnóstico



\## RN-DIA-001 — Diagnóstico associado à OS



Todo diagnóstico registrado deve estar associado a uma ordem de serviço.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-DIA-002 — Responsável pelo diagnóstico



O sistema poderá registrar o usuário responsável pelo diagnóstico.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 6. Orçamento



\## RN-ORC-001 — Orçamento associado à OS



Um orçamento deve estar associado a uma ordem de serviço.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-ORC-002 — Composição do orçamento



O valor do orçamento deve considerar os serviços e as peças relacionadas à OS.



\*\*Regra proposta:\*\*



```text

Total = Serviços + Peças - Descontos

```



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-ORC-003 — Aprovação do orçamento



A execução dos serviços deverá depender da aprovação do orçamento, conforme o fluxo definido para o sistema.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-ORC-004 — Recusa do orçamento



Quando um orçamento for recusado, a decisão deverá ser registrada e a OS deverá assumir o comportamento definido pelo grupo.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir se a OS será encerrada, cancelada, revisada ou permanecerá aguardando alguma ação.



\---



\## RN-ORC-005 — Alteração após aprovação



Alterações em um orçamento aprovado deverão respeitar uma regra específica de revisão ou nova aprovação.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir se será permitido alterar diretamente um orçamento aprovado ou se será necessária uma nova versão.



\---



\# 7. Estoque



\## RN-EST-001 — Quantidade disponível



O estoque deve manter a quantidade disponível de cada peça.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-EST-002 — Saída superior ao estoque



O sistema não deve permitir uma saída de peças superior à quantidade disponível.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-EST-003 — Registro de movimentações



Entradas e saídas de peças devem gerar registros de movimentação.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-EST-004 — Baixa de peças utilizadas



As peças utilizadas em uma OS devem ser descontadas do estoque no momento definido pela regra de negócio.



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir se a baixa ocorre:



\* na aprovação do orçamento;

\* no início da execução;

\* durante a execução;

\* na conclusão da OS.



\---



\# 8. Financeiro



\## RN-FIN-001 — Valor total



O valor total da OS deve corresponder à soma dos serviços e peças, descontados eventuais descontos autorizados.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-FIN-002 — Registro de pagamento



Um pagamento deve estar associado a uma ordem de serviço.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-FIN-003 — Valor pago



O sistema deve registrar o valor efetivamente pago.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-FIN-004 — Pagamento parcial



Caso pagamentos parciais sejam permitidos, o sistema deverá controlar o valor pago e o saldo restante.



\*\*Status:\*\* 🔵 Validar com professor



\*\*Pendência:\*\* confirmar se pagamento parcial fará parte do MVP.



\---



\## RN-FIN-005 — Parcelamento



Caso o parcelamento seja permitido, o sistema deverá registrar as informações necessárias para acompanhamento das parcelas.



\*\*Status:\*\* ⚪ Pós-MVP



\*\*Pendência:\*\* validar necessidade para o MVP.



\---



\# 9. Histórico de manutenção



\## RN-HIS-001 — Preservação do histórico



Os atendimentos concluídos devem permanecer associados ao veículo para consulta futura.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-HIS-002 — Integridade histórica



Alterações cadastrais posteriores não devem apagar indevidamente informações históricas das ordens de serviço já registradas.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 10. Usuários e permissões



\## RN-AUT-001 — Autenticação



Usuários devem ser autenticados antes de acessar funcionalidades protegidas.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-AUT-002 — Controle de acesso



O acesso às funcionalidades deve respeitar as permissões associadas ao perfil do usuário.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-AUT-003 — Perfis



Perfis inicialmente propostos:



\* Administrador

\* Atendente

\* Mecânico

\* Estoque

\* Financeiro



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendência:\*\* definir quais funcionalidades cada perfil poderá acessar.



\---



\# 11. Notificações



\## RN-NOT-001 — Eventos notificáveis



Eventos relevantes do sistema poderão gerar notificações.



Exemplos:



\* Orçamento aguardando aprovação.

\* Orçamento aprovado.

\* Orçamento recusado.

\* OS concluída.

\* Pagamento pendente.



\*\*Status:\*\* 🔵 Validar com professor



\---



\# 12. Integridade dos dados



\## RN-DAT-001 — Relacionamentos



Os registros relacionados devem manter integridade entre suas entidades.



Exemplos:



```text

Cliente

&#x20; └── Veículos

&#x20;      └── Ordens de Serviço

&#x20;           ├── Diagnóstico

&#x20;           ├── Orçamento

&#x20;           ├── Serviços

&#x20;           ├── Peças

&#x20;           └── Pagamentos

```



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-DAT-002 — Identificadores



Entidades principais devem possuir identificadores únicos.



\*\*Status:\*\* 🟡 Em discussão



\---



\## RN-DAT-003 — Dados obrigatórios



O sistema deve impedir o registro de entidades quando informações obrigatórias não forem fornecidas.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 13. Rastreabilidade



\## RN-TRA-001 — Registro de alterações importantes



Operações relevantes deverão permitir identificar o momento e, quando aplicável, o usuário responsável pela alteração.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 14. Regras ainda não definidas



As seguintes decisões deverão ser discutidas pelo grupo:



1\. Campos obrigatórios de cliente.

2\. Critério de identificação única do cliente.

3\. Exclusão ou inativação de clientes.

4\. Critério de identificação única do veículo.

5\. Tratamento de alteração de proprietário.

6\. Status oficiais da OS.

7\. Transições permitidas entre status.

8\. Comportamento após recusa do orçamento.

9\. Alteração de orçamento aprovado.

10\. Momento da baixa do estoque.

11\. Pagamento parcial.

12\. Parcelamento.

13\. Perfis de usuário.

14\. Permissões de cada perfil.

15\. Notificações no MVP.

16\. Relatórios no MVP.

17\. Regras adicionais necessárias para o funcionamento dos microserviços.



\---



\# 15. Validação pelo grupo



As regras deste documento deverão ser revisadas pelo grupo antes de serem consideradas definitivas.



Quando uma regra for aprovada, seu status poderá ser alterado de:



```text

🟡 Em discussão

```



para:



```text

🟢 Definida

```



Quando depender de orientação do professor:



```text

🔵 Validar com professor

```



\---



\# 16. Observação



Este documento tem como objetivo apoiar a análise de requisitos e a comunicação entre os integrantes do projeto.



A Product Owner/Analista de Requisitos é responsável pelo levantamento e organização das regras, mas decisões que impactem o escopo, arquitetura, desenvolvimento ou entregas acadêmicas devem ser discutidas pelo grupo e, quando necessário, validadas pelo professor.



\*\*Última atualização:\*\* 26/09/2026

\*\*Responsável pelo levantamento:\*\* Heloísa Brandão



