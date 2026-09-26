\# Fluxos do Sistema — OficinaPro



> \*\*Documento de trabalho — sujeito à validação do grupo e do professor.\*\*

>

> Este documento representa os fluxos funcionais inicialmente identificados para o OficinaPro. Os fluxos poderão ser alterados conforme as decisões do grupo, os requisitos do MVP e as orientações do professor.



\---



\# 1. Fluxo principal da oficina



O fluxo principal proposto para o OficinaPro é:



```text

Cliente

&#x20;  ↓

Veículo

&#x20;  ↓

Abertura da Ordem de Serviço

&#x20;  ↓

Diagnóstico

&#x20;  ↓

Elaboração do Orçamento

&#x20;  ↓

Aprovação do Orçamento

&#x20;  ├── Recusado → OS encerrada/recusada

&#x20;  │

&#x20;  └── Aprovado

&#x20;         ↓

&#x20;    Execução do Serviço

&#x20;         ↓

&#x20;    Utilização de Peças

&#x20;         ↓

&#x20;    Atualização do Estoque

&#x20;         ↓

&#x20;    Finalização da OS

&#x20;         ↓

&#x20;    Pagamento

&#x20;         ↓

&#x20;    Histórico de Manutenção

```



\*\*Status:\*\* 🟡 Em discussão



\*\*Pendências:\*\*



\* Validar o fluxo com o grupo.

\* Confirmar o momento da baixa de estoque.

\* Confirmar se o pagamento ocorre antes ou depois da conclusão da OS.

\* Definir o comportamento de uma OS com orçamento recusado.



\---



\# 2. Fluxo de cadastro de cliente



```text

Início

&#x20; ↓

Acessar cadastro de cliente

&#x20; ↓

Informar dados

&#x20; ↓

Validar informações

&#x20; ↓

Cliente já existe?

&#x20; ├── Sim → Informar/consultar cadastro existente

&#x20; │

&#x20; └── Não

&#x20;      ↓

&#x20;   Criar cliente

&#x20;      ↓

&#x20;     Fim

```



\### Regras propostas



\* O sistema deve validar os campos obrigatórios.

\* O sistema deve evitar duplicidade conforme as regras definidas para identificação do cliente.

\* Clientes existentes devem poder ser consultados antes de um novo cadastro.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 3. Fluxo de cadastro de veículo



```text

Início

&#x20; ↓

Selecionar cliente

&#x20; ↓

Cadastrar veículo

&#x20; ↓

Informar dados do veículo

&#x20; ↓

Validar informações

&#x20; ↓

Veículo já cadastrado?

&#x20; ├── Sim → Consultar/atualizar veículo

&#x20; │

&#x20; └── Não

&#x20;      ↓

&#x20;   Criar veículo

&#x20;      ↓

&#x20;     Fim

```



\### Regras propostas



\* O veículo deve estar associado a um cliente.

\* A placa poderá ser utilizada como identificador do veículo, conforme validação da regra pelo grupo.

\* O sistema deve evitar duplicidade de veículos.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 4. Fluxo de abertura de Ordem de Serviço



```text

Início

&#x20; ↓

Selecionar cliente

&#x20; ↓

Selecionar veículo

&#x20; ↓

Informar problema/solicitação

&#x20; ↓

Registrar data

&#x20; ↓

Criar OS

&#x20; ↓

Definir status inicial

&#x20; ↓

Fim

```



\### Resultado esperado



A ordem de serviço deve possuir uma identificação própria e ficar disponível para acompanhamento.



\*\*Status:\*\* 🟡 Em discussão



\---



\# 5. Fluxo de diagnóstico



```text

OS aberta

&#x20;  ↓

Veículo encaminhado para diagnóstico

&#x20;  ↓

Mecânico realiza avaliação

&#x20;  ↓

Registrar diagnóstico

&#x20;  ↓

Identificar serviços necessários

&#x20;  ↓

Identificar peças necessárias

&#x20;  ↓

Encaminhar informações para orçamento

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendências



\* Definir se uma OS pode possuir mais de um diagnóstico.

\* Definir se o diagnóstico pode ser alterado após o orçamento.

\* Definir quais usuários podem visualizar ou editar o diagnóstico.



\---



\# 6. Fluxo de elaboração do orçamento



```text

Diagnóstico

&#x20;  ↓

Identificar serviços

&#x20;  ↓

Identificar peças

&#x20;  ↓

Informar quantidades

&#x20;  ↓

Calcular valores

&#x20;  ↓

Aplicar desconto, se permitido

&#x20;  ↓

Calcular total

&#x20;  ↓

Enviar orçamento para aprovação

```



\### Cálculo proposto



```text

Valor dos serviços

&#x20;       +

Valor das peças

&#x20;       -

Descontos

&#x20;       =

Total da OS

```



\*\*Status:\*\* 🟡 Em discussão



\---



\# 7. Fluxo de aprovação do orçamento



```text

Orçamento criado

&#x20;      ↓

Aguardando aprovação

&#x20;      ↓

Decisão

&#x20; ├── Aprovado

&#x20; │      ↓

&#x20; │   OS pode prosseguir

&#x20; │

&#x20; └── Recusado

&#x20;        ↓

&#x20;     Registrar recusa

&#x20;        ↓

&#x20;     Definir encerramento/

&#x20;     alteração/revisão da OS

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendências



\* Definir quem pode registrar a aprovação.

\* Definir quem pode registrar a recusa.

\* Definir se um orçamento recusado poderá ser revisado.

\* Definir se uma nova versão do orçamento será criada.

\* Definir o status da OS após a recusa.



\---



\# 8. Fluxo de execução do serviço



```text

Orçamento aprovado

&#x20;      ↓

Iniciar execução

&#x20;      ↓

Realizar serviços

&#x20;      ↓

Utilizar peças necessárias

&#x20;      ↓

Registrar execução

&#x20;      ↓

Finalizar serviço

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendências



\* Definir como os serviços executados serão registrados.

\* Definir se será necessário registrar o mecânico responsável.

\* Definir quando a OS poderá ser considerada concluída.



\---



\# 9. Fluxo de estoque



\## Entrada de peças



```text

Início

&#x20; ↓

Registrar entrada

&#x20; ↓

Informar peça

&#x20; ↓

Informar quantidade

&#x20; ↓

Atualizar estoque

&#x20; ↓

Registrar movimentação

&#x20; ↓

Fim

```



\## Saída de peças



```text

Peça necessária

&#x20; ↓

Consultar estoque

&#x20; ↓

Quantidade suficiente?

&#x20; ├── Não → Impedir/alertar saída

&#x20; │

&#x20; └── Sim

&#x20;      ↓

&#x20;   Registrar saída

&#x20;      ↓

&#x20;   Atualizar quantidade

&#x20;      ↓

&#x20;   Registrar movimentação

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendência principal



Definir quando a utilização da peça será efetivamente descontada do estoque:



\* na aprovação do orçamento;

\* no início da execução;

\* durante a execução;

\* na conclusão da OS.



\---



\# 10. Fluxo de pagamento



```text

OS finalizada

&#x20;  ↓

Valor total definido

&#x20;  ↓

Registrar pagamento

&#x20;  ↓

Pagamento corresponde ao valor total?

&#x20;  ├── Sim → OS pode ser considerada quitada

&#x20;  │

&#x20;  └── Não → Definir tratamento

&#x20;             para pagamento parcial

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendências



\* Definir formas de pagamento.

\* Definir se haverá pagamento parcial.

\* Definir se haverá parcelamento.

\* Definir quando o pagamento será registrado.

\* Definir relação entre pagamento e encerramento da OS.



\---



\# 11. Fluxo de histórico de manutenção



```text

OS concluída

&#x20;   ↓

Registrar informações relevantes

&#x20;   ↓

Associar ao veículo

&#x20;   ↓

Atualizar histórico

&#x20;   ↓

Histórico disponível para consulta

```



\*\*Status:\*\* 🟡 Em discussão



O histórico deverá permitir consultar atendimentos anteriores relacionados ao veículo.



\---



\# 12. Fluxo de autenticação



```text

Início

&#x20; ↓

Usuário informa credenciais

&#x20; ↓

Validar credenciais

&#x20; ↓

Credenciais válidas?

&#x20; ├── Não → Informar erro

&#x20; │

&#x20; └── Sim

&#x20;      ↓

&#x20;   Identificar perfil

&#x20;      ↓

&#x20;   Liberar funcionalidades

&#x20;      ↓

&#x20;     Sistema

```



\*\*Status:\*\* 🟡 Em discussão



\### Pendências



\* Definir perfis.

\* Definir permissões por perfil.

\* Definir política de senha.

\* Definir recuperação de acesso.



\---



\# 13. Fluxo de notificações



```text

Evento relevante

&#x20;     ↓

Identificar tipo de evento

&#x20;     ↓

Verificar destinatários

&#x20;     ↓

Gerar notificação

&#x20;     ↓

Enviar pelo canal definido

```



\*\*Status:\*\* 🔵 Validar com professor



\### Possíveis eventos



\* Orçamento aguardando aprovação.

\* Orçamento aprovado.

\* Orçamento recusado.

\* OS concluída.

\* Pagamento pendente.



\### Pendências



\* Definir se notificações fazem parte do MVP.

\* Definir canais de comunicação.

\* Definir eventos obrigatórios.



\---



\# 14. Fluxo de exceções



Além do fluxo principal, o sistema deverá considerar situações como:



\### 14.1 Cliente inexistente



```text

Tentativa de abrir OS

&#x20;       ↓

Cliente não encontrado

&#x20;       ↓

Cadastrar cliente

&#x20;       ↓

Continuar abertura da OS

```



\*\*Status:\*\* 🟡 Em discussão



\### 14.2 Veículo inexistente



```text

Tentativa de abrir OS

&#x20;       ↓

Veículo não encontrado

&#x20;       ↓

Cadastrar veículo

&#x20;       ↓

Continuar abertura da OS

```



\*\*Status:\*\* 🟡 Em discussão



\### 14.3 Estoque insuficiente



```text

Peça necessária

&#x20;     ↓

Consultar estoque

&#x20;     ↓

Quantidade insuficiente

&#x20;     ↓

Informar indisponibilidade

&#x20;     ↓

Definir tratamento

```



\*\*Status:\*\* 🟡 Em discussão



\### 14.4 Orçamento recusado



```text

Orçamento recusado

&#x20;      ↓

Registrar decisão

&#x20;      ↓

Definir próximo estado da OS

```



\*\*Status:\*\* 🟡 Em discussão



\### 14.5 Pagamento incompleto



```text

Pagamento registrado

&#x20;      ↓

Valor inferior ao total

&#x20;      ↓

Registrar saldo

&#x20;      ↓

Definir status financeiro

```



\*\*Status:\*\* 🟡 Em discussão



\---



\# 15. Transições propostas da Ordem de Serviço



A seguinte sequência é uma proposta inicial:



```text

ABERTA

&#x20; ↓

EM\_DIAGNOSTICO

&#x20; ↓

AGUARDANDO\_APROVACAO

&#x20; ↓

APROVADA

&#x20; ↓

EM\_EXECUCAO

&#x20; ↓

AGUARDANDO\_PAGAMENTO

&#x20; ↓

CONCLUIDA

```



Fluxo alternativo:



```text

AGUARDANDO\_APROVACAO

&#x20;       ↓

&#x20;    RECUSADA

```



\*\*Status:\*\* 🟡 Em discussão



As transições definitivas deverão ser validadas pelo grupo.



\---



\# 16. Fluxo geral resumido



```text

┌──────────────┐

│    CLIENTE   │

└──────┬───────┘

&#x20;      ↓

┌──────────────┐

│   VEÍCULO    │

└──────┬───────┘

&#x20;      ↓

┌──────────────┐

│     OS       │

└──────┬───────┘

&#x20;      ↓

┌──────────────┐

│ DIAGNÓSTICO  │

└──────┬───────┘

&#x20;      ↓

┌──────────────┐

│  ORÇAMENTO   │

└──────┬───────┘

&#x20;      ↓

&#x20;  APROVAÇÃO?

&#x20;   ↙     ↘

&#x20; NÃO       SIM

&#x20;  ↓         ↓

RECUSADA  EXECUÇÃO

&#x20;            ↓

&#x20;         ESTOQUE

&#x20;            ↓

&#x20;         FINALIZAÇÃO

&#x20;            ↓

&#x20;         PAGAMENTO

&#x20;            ↓

&#x20;        HISTÓRICO

```



\---



\# 17. Pontos que precisam de validação



Os principais pontos que ainda não devem ser considerados decisões definitivas são:



1\. Status oficiais da OS.

2\. Transições permitidas entre status.

3\. Momento da baixa de estoque.

4\. Comportamento após recusa do orçamento.

5\. Momento do pagamento.

6\. Possibilidade de pagamento parcial.

7\. Perfis e permissões.

8\. Notificações no MVP.

9\. Relatórios no MVP.

10\. Nível de detalhamento dos microserviços.

11\. Necessidade de mensageria para cada fluxo.

12\. Requisitos técnicos obrigatórios definidos pelo professor.



\---



\# 18. Observação final



Este documento descreve o funcionamento esperado do sistema com base nos requisitos atualmente levantados.



Ele não representa uma decisão unilateral da Product Owner. Alterações que impactem o escopo, arquitetura, implementação ou entregas acadêmicas deverão ser discutidas pelo grupo e, quando necessário, validadas com o professor.



\*\*Última atualização:\*\* 26/09/2026

\*\*Responsável pelo levantamento:\*\* Heloísa Brandão



