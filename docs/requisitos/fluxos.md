# Fluxos do Sistema — OficinaPro

> **Documento de trabalho — Grupo GarageOps**
>
> Este documento consolida os fluxos operacionais e de exceção do MVP do OficinaPro, considerando as regras de negócio de aprovação de orçamento, reserva de estoque, rastreabilidade física das peças, isolamento entre oficinas e separação entre conclusão operacional e situação financeira.
>
> Os fluxos representam o comportamento esperado do sistema. Sua descrição não significa que todas as funcionalidades já estejam implementadas.

---

## 1. Fluxo principal da oficina (MVP)

O fluxo operacional principal do OficinaPro segue a sequência abaixo:

```text
Oficina (Tenant)
   ↓
Cliente cadastrado na oficina
   ↓
Veículo vinculado ao cliente
   ↓
Abertura da Ordem de Serviço (ABERTA)
   ↓
Diagnóstico Técnico (EM_DIAGNOSTICO)
   ↓
Elaboração do Orçamento (AGUARDANDO_APROVACAO)
   ↓
Decisão formal do cliente
   ├── Recusado
   │      ↓
   │   Escolher tratativa:
   │      ├── Encerramento definitivo da OS
   │      └── Elaboração de orçamento revisado
   │              ↓
   │        AGUARDANDO_APROVACAO
   │
   └── Aceito
          ↓
      Solicitar reserva integral das peças
          ↓
      Inventory Service valida disponibilidade
          ↓
      Todas as peças podem ser reservadas?
          ├── Não
          │     ↓
          │  Recusar reserva integralmente
          │     ↓
          │  Manter aprovação operacional bloqueada
          │     ↓
          │  Repor estoque, revisar orçamento
          │  ou cancelar a solicitação
          │
          └── Sim
                ↓
            Confirmar reserva das peças
                ↓
            Aprovar orçamento no sistema
                ↓
            OS liberada para execução (EM_EXECUCAO)
                ↓
            Registrar utilização física das peças
                ↓
            Liberar peças reservadas não utilizadas
                ↓
            Finalizar execução técnica
                ↓
            OS concluída operacionalmente (CONCLUIDA)
                ↓
            Atualizar histórico de manutenção do veículo
```

A situação financeira acompanha um fluxo independente:

```text
Orçamento aprovado
   ↓
Registrar valores devidos
   ↓
Registrar pagamentos no Billing Service
   ↓
Atualizar situação financeira
   ├── PENDENTE
   ├── PARCIAL
   └── QUITADA
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A aceitação do cliente e a aprovação operacional do orçamento são etapas distintas.
- A aceitação do cliente inicia a solicitação de reserva, mas não libera imediatamente a execução.
- A reserva reduz o saldo disponível, mas não representa saída física definitiva.
- A execução só pode começar após a confirmação da reserva integral e a aprovação operacional.
- A conclusão operacional da OS não depende da quitação financeira.
- A recusa do orçamento é uma decisão sobre o orçamento, não um estado operacional equivalente à conclusão da OS.

---

## 2. Fluxo de cadastro de cliente

```text
Início
  ↓
Usuário autenticado acessa o cadastro de cliente
  ↓
Identificar oficina (tenant) pelo contexto autenticado
  ↓
Informar dados obrigatórios
(Nome, CPF, Telefone, E-mail e Endereço)
  ↓
Validar formato do CPF
  ↓
Verificar unicidade do CPF dentro da oficina (RN-CLI-001 e RN-CLI-002)
  ↓
CPF já cadastrado na mesma oficina?
  ├── Sim → Bloquear duplicidade e apresentar cadastro existente
  │
  └── Não
       ↓
   Associar cliente ao tenant (RN-CLI-004)
       ↓
   Persistir cliente
       ↓
      Fim
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- O CPF deve ser válido e obrigatório.
- A unicidade do CPF é aplicada dentro da oficina, não globalmente.
- Oficinas diferentes podem cadastrar clientes com o mesmo CPF.
- O cadastro deve respeitar o isolamento de dados entre tenants.

---

## 3. Fluxo de cadastro de veículo

```text
Início
  ↓
Identificar oficina (tenant)
  ↓
Selecionar cliente existente na mesma oficina (RN-VEI-001)
  ↓
Informar dados do veículo
(Placa, Marca, Modelo, Ano, Cor e Quilometragem)
  ↓
Validar formato da placa
  ↓
Verificar unicidade da placa dentro da oficina (RN-VEI-002)
  ↓
Placa já cadastrada na mesma oficina?
  ├── Sim → Bloquear duplicidade e consultar veículo existente
  │
  └── Não
       ↓
   Vincular veículo ao cliente selecionado
       ↓
   Associar veículo ao tenant
       ↓
   Persistir veículo
       ↓
      Fim
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- O veículo deve estar vinculado a um cliente da mesma oficina.
- A placa deve ser única dentro do tenant.
- Oficinas diferentes podem cadastrar veículos com a mesma placa.
- Alterações de proprietário devem preservar o histórico de manutenção (RN-VEI-003 e RN-VEI-004).

---

## 4. Fluxo de abertura de Ordem de Serviço

```text
Início
  ↓
Usuário autenticado acessa a abertura de OS
  ↓
Identificar oficina (tenant)
  ↓
Selecionar cliente e veículo cadastrados na mesma oficina
  ↓
Informar problema relatado e quilometragem atual
  ↓
Validar vínculo entre cliente e veículo
  ↓
Gerar identificador único da OS
  ↓
Atribuir status inicial: ABERTA
  ↓
Persistir OS no Work Order Service
  ↓
Fim
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A OS deve possuir identificador único (RN-OS-001).
- O cliente e o veículo devem pertencer à mesma oficina (RN-OS-002).
- O sistema deve impedir associação de registros pertencentes a tenants diferentes.

---

## 5. Fluxo de diagnóstico

```text
OS com status ABERTA
   ↓
Mecânico autorizado acessa a OS
   ↓
OS assume status: EM_DIAGNOSTICO
   ↓
Realizar avaliação técnica
   ↓
Registrar laudo técnico e observações vinculadas à OS (RN-DIA-001)
   ↓
Identificar serviços e peças necessários
   ↓
Encaminhar informações para elaboração do orçamento
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- O diagnóstico deve permanecer vinculado à OS.
- O registro deve respeitar as permissões do perfil Mecânico.
- As informações do diagnóstico devem permanecer disponíveis no histórico do atendimento.

---

## 6. Fluxo de elaboração do orçamento

```text
Diagnóstico registrado
   ↓
Selecionar serviços e peças necessários
   ↓
Informar quantidades e valores
   ↓
Consultar disponibilidade atual das peças
   ↓
Calcular total automaticamente:
(Serviços + Peças) - Descontos autorizados (RN-ORC-001)
   ↓
Vincular orçamento à OS
   ↓
OS assume status: AGUARDANDO_APROVACAO
   ↓
Apresentar orçamento ao cliente
   ↓
Aguardar decisão formal
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A consulta de disponibilidade durante a elaboração é apenas informativa.
- O saldo poderá mudar antes da aceitação do orçamento.
- A reserva efetiva deve ocorrer somente após a aceitação do cliente.
- A disponibilidade deverá ser validada novamente durante a solicitação de reserva (RN-ORC-004 e RN-EST-008).

---

## 7. Fluxo de aprovação ou recusa do orçamento

```text
OS em AGUARDANDO_APROVACAO
       ↓
Registrar decisão formal do cliente
       ↓
Decisão do cliente?
  ├── Recusado
  │      ↓
  │   Registrar recusa e preservar histórico
  │      ↓
  │   Escolher tratativa (RN-ORC-003):
  │      ├── Encerrar definitivamente a OS
  │      └── Elaborar novo orçamento revisado
  │              ↓
  │          Preservar orçamento e decisão anteriores
  │              ↓
  │          Vincular novo orçamento à mesma OS
  │              ↓
  │          Retornar para AGUARDANDO_APROVACAO
  │
  └── Aceito
         ↓
      Registrar aceitação do cliente
         ↓
      OS assume status: AGUARDANDO_RESERVA
         ↓
      Work Order Service solicita reserva ao Inventory Service
         ↓
      Inventory Service valida todas as peças e quantidades
         ↓
      Todas as peças possuem saldo disponível?
         ├── Não
         │      ↓
         │   Recusar a reserva integralmente
         │      ↓
         │   Registrar resultado da solicitação
         │      ↓
         │   Manter aprovação operacional bloqueada
         │      ↓
         │   Repor estoque, revisar orçamento
         │   ou cancelar a solicitação
         │
         └── Sim
                ↓
            Efetivar reserva integral em transação local
                ↓
            Confirmar resultado ao Work Order Service
                ↓
            Registrar aprovação operacional do orçamento
                ↓
            Liberar OS para EM_EXECUCAO
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A aceitação do cliente deve ser registrada separadamente da aprovação operacional.
- A reserva deve ser integral: todas as peças são reservadas ou nenhuma é reservada (RN-EST-008).
- A aprovação operacional não pode ser registrada antes da confirmação da reserva (RN-ORC-002 e RN-ORC-004).
- Caso a reserva seja recusada, a OS não pode avançar para execução.
- O resultado da solicitação deve ser rastreável, inclusive em caso de falha ou reprocessamento.

---

## 8. Fluxo de execução do serviço e rastreabilidade física

```text
OS com orçamento aprovado e reserva confirmada
       ↓
Verificar confirmação da reserva
       ↓
Mecânico autorizado inicia atendimento
       ↓
OS assume status: EM_EXECUCAO
       ↓
Consultar peças reservadas para a OS
       ↓
Registrar a condição de cada item
       ├── RESERVADA / PENDENTE_INSTALACAO
       │      ↓
       │   Peça permanece comprometida,
       │   mas ainda não foi utilizada
       │
       ├── UTILIZADA / INSTALADA / CONSUMIDA
       │      ↓
       │   Converter reserva em saída definitiva
       │   (RN-EST-011)
       │
       └── NÃO UTILIZADA
              ↓
           Liberar reserva
           (RN-EST-005)
       ↓
Registrar serviços efetivamente executados
       ↓
Finalizar execução técnica
       ↓
Verificar se todas as peças reservadas
foram utilizadas ou liberadas
       ↓
Encerrar etapa operacional
       ↓
OS assume status: CONCLUIDA
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A condição física deve ser acompanhada por peça ou item da reserva, não apenas pela OS inteira.
- A reserva não significa que a peça foi instalada ou consumida.
- A utilização física deve gerar a saída definitiva correspondente.
- Peças não utilizadas devem ter sua reserva liberada.
- Peças perdidas, avariadas ou descartadas devem receber tratamento específico, sem retorno indevido ao estoque comercial.
- A OS só poderá ser concluída operacionalmente quando a execução estiver encerrada e as reservas vinculadas tiverem sido devidamente tratadas.

---

## 9. Fluxo de estoque (Entrada, Reserva, Consumo, Liberação e Perda)

### 9.1. Entrada de peças

```text
Início
   ↓
Usuário autorizado acessa o Inventory Service
   ↓
Identificar oficina (tenant)
   ↓
Informar peça e quantidade recebida
   ↓
Validar cadastro e quantidade
   ↓
Registrar entrada no estoque físico
   ↓
Atualizar saldo disponível
   ↓
Registrar movimentação de entrada
   ↓
Fim
```

**Observação:** A entrada deve incrementar o estoque físico e, consequentemente, o saldo disponível, desde que não exista outra restrição operacional sobre as unidades recebidas.

### 9.2. Reserva integral de peças

```text
Orçamento aceito pelo cliente
   ↓
Work Order Service solicita reserva
   ↓
Inventory Service recebe solicitação
   ↓
Validar tenant, itens e quantidades
   ↓
Verificar saldo disponível de todos os itens
   ↓
Todas as peças possuem saldo suficiente?
   ├── Não
   │      ↓
   │   Não efetivar nenhuma reserva
   │      ↓
   │   Registrar resultado de reserva recusada
   │      ↓
   │   Informar resultado ao Work Order Service
   │
   └── Sim
          ↓
      Iniciar transação no banco do Inventory Service
          ↓
      Aplicar controle de concorrência
      sobre os itens solicitados (RN-EST-009)
          ↓
      Revalidar saldo disponível
          ↓
      Reservar todas as quantidades
          ↓
      Registrar reservas e movimentações
          ↓
      Confirmar transação
          ↓
      Publicar resultado da reserva
          ↓
      Informar confirmação ao Work Order Service
```

**Observações:**

- A reserva deve ser executada integralmente no banco de dados do Inventory Service.
- Se qualquer item não possuir saldo suficiente, nenhuma quantidade daquela solicitação deverá permanecer reservada.
- A validação e a efetivação devem ocorrer dentro de uma transação consistente.
- O controle de concorrência deve impedir que solicitações simultâneas comprometam a mesma quantidade disponível.
- A chave de idempotência deve impedir que a mesma solicitação gere reservas duplicadas (RN-EST-010).

### 9.3. Conversão da reserva em saída definitiva

```text
Peça possui reserva ativa
   ↓
Mecânico utiliza a peça na execução?
   ├── Não
   │      ↓
   │   Manter reserva enquanto a OS estiver em execução
   │      ↓
   │   Se não for mais necessária, liberar reserva
   │
   └── Sim
          ↓
      Confirmar utilização física
          ↓
      Validar reserva e quantidade
          ↓
      Registrar consumo definitivo
          ↓
      Reduzir estoque físico
          ↓
      Reduzir quantidade reservada
          ↓
      Registrar movimentação de saída
          ↓
      Atualizar condição do item para UTILIZADA
```

**Observação:** A conversão da reserva em saída definitiva deve reduzir o estoque físico e a quantidade reservada de forma consistente, sem realizar uma segunda baixa sobre o saldo disponível.

### 9.4. Liberação de peças não utilizadas

```text
Peça possui reserva ativa
   ↓
Peça não foi utilizada?
   ↓
Verificar condição física
   ↓
Peça permanece íntegra e reutilizável?
   ├── Sim
   │      ↓
   │   Registrar liberação da reserva
   │      ↓
   │   Reduzir quantidade reservada
   │      ↓
   │   Devolver quantidade ao saldo disponível
   │      ↓
   │   Preservar estoque físico
   │
   └── Não
          ↓
      Registrar ocorrência de perda,
      avaria ou descarte conforme o caso
          ↓
      Impedir retorno ao estoque comercial
```

**Observação:** A liberação de uma reserva não aumenta o estoque físico, pois a peça continuou fisicamente no estoque durante a reserva.

### 9.5. Cancelamento e tratamento de exceções

```text
OS cancelada ou orçamento substituído
   ↓
Consultar reservas vinculadas à OS
   ↓
Existem reservas ativas?
   ├── Não
   │      ↓
   │   Encerrar tratativa de estoque
   │
   └── Sim
          ↓
      Consultar condição de cada item
          ├── RESERVADA / PENDENTE_INSTALACAO
          │      ↓
          │   Verificar integridade da peça
          │      ↓
          │   Se reutilizável, liberar reserva
          │   (RN-EST-005)
          │
          ├── UTILIZADA / INSTALADA / CONSUMIDA
          │      ↓
          │   Manter saída definitiva registrada
          │
          └── PERDIDA / AVARIADA / DESCARTADA
                 ↓
              Registrar perda ou descarte
              sem retorno ao saldo comercial
              (RN-EST-006)
```

**Regras de controle:**

- Toda movimentação deve manter vínculo com o tenant e, quando aplicável, com a OS e o orçamento correspondentes.
- A condição física deve ser registrada individualmente para cada peça ou item movimentado.
- A liberação deve estar relacionada à reserva original.
- Uma mesma reserva não pode ser consumida ou liberada mais de uma vez.
- Peças instaladas ou consumidas não podem retornar ao saldo comercial disponível.
- Peças ainda não utilizadas somente podem retornar ao saldo disponível se estiverem em condições de reutilização.
- Correções devem gerar registros próprios, preservando o histórico original (RN-EST-007).

**Status:** 🟢 Definido pelo grupo

---

## 10. Fluxo de pagamento e situação financeira

```text
Orçamento aprovado
   ↓
Registrar valor devido no Billing Service
   ↓
Aguardar pagamento do cliente
   ↓
Registrar pagamento
(Valor, Data e Forma de Pagamento)
   ↓
Validar pagamento e atualizar saldo financeiro
   ↓
Valor acumulado quita o total devido?
   ├── Não
   │      ↓
   │   Situação financeira: PARCIAL
   │   ou PENDENTE, conforme os valores recebidos
   │
   └── Sim
          ↓
      Confirmar quitação financeira
          ↓
      Situação financeira: QUITADA
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- O registro financeiro pertence ao Billing Service.
- A situação financeira não deve alterar automaticamente o estado operacional da OS.
- Uma OS operacionalmente concluída poderá permanecer com situação financeira `PENDENTE` ou `PARCIAL`.
- A quitação integral deve ser registrada sem apagar os pagamentos anteriores.
- Os pagamentos devem permanecer vinculados à OS e ao tenant correspondente.

---

## 11. Fluxo de histórico de manutenção

```text
Atendimento atualizado ou encerrado
   ↓
Consolidar informações relacionadas ao veículo:
- Diagnósticos e laudos
- Orçamentos e versões
- Decisões do cliente
- Serviços executados
- Peças reservadas e utilizadas
- Liberações e movimentações de estoque
- Registros financeiros
- Situação operacional da OS
   ↓
Manter vínculo com o veículo
   ↓
Preservar histórico de alterações e decisões
   ↓
Disponibilizar informações para consulta futura
```

**Status:** 🟢 Definido pelo grupo

**Observações:**

- O histórico deve preservar registros mesmo quando houver recusa de orçamento, revisão de orçamento ou cancelamento da OS.
- A alteração do proprietário do veículo não deve apagar o histórico de manutenção.
- A conclusão operacional e a situação financeira devem ser apresentadas como informações distintas.
- O histórico deve respeitar o isolamento entre oficinas.

---

## 12. Fluxo de autenticação e permissões (RBAC)

```text
Início
  ↓
Usuário informa credenciais no Auth Service
  ↓
Validar credenciais
  ├── Inválidas → Rejeitar acesso
  │
  └── Válidas
       ↓
   Identificar usuário e contexto de oficina
       ↓
   Validar vínculo do usuário com o tenant
       ↓
   Identificar perfil e permissões
       ↓
   Liberar acesso às funcionalidades autorizadas
       ↓
   Cada serviço valida autorização e tenant
       ↓
   Executar operação permitida
```

**Perfis definidos para o MVP:**

- **Atendente:** atividades de atendimento e cadastro de clientes, veículos e ordens de serviço, conforme permissões estabelecidas.
- **Mecânico:** consulta e atualização de informações técnicas, diagnósticos e execução dos serviços.
- **Estoque:** cadastro, consulta e controle de peças, reservas e movimentações de estoque.
- **Financeiro:** consulta e registro de pagamentos e informações financeiras.

**Status:** 🟢 Definido pelo grupo

**Observações:**

- A autorização deve ser verificada no backend.
- A interface não deve ser considerada a única camada de proteção.
- A permissão do perfil não substitui a validação de vínculo com o tenant.
- O usuário não poderá acessar dados de outra oficina sem autorização.

---

## 13. Fluxo de notificações (Pós-MVP)

```text
Evento relevante publicado
(ex.: orçamento aprovado ou OS concluída)
      ↓
Serviço de notificações recebe o evento
      ↓
Identificar destinatário e canal
      ↓
Preparar notificação
      ↓
Enviar notificação
      ↓
Registrar resultado do envio
```

**Status:** ⚪ Pós-MVP — eventual necessidade mínima a validar com o professor.

---

## 14. Esteira oficial de status da Ordem de Serviço

A esteira principal dos estados operacionais da OS é:

```text
ABERTA
  ↓
EM_DIAGNOSTICO
  ↓
AGUARDANDO_APROVACAO
  ├── Orçamento recusado
  │      ├── Encerramento definitivo da OS
  │      └── Revisão do orçamento
  │              ↓
  │        AGUARDANDO_APROVACAO
  │
  └── Orçamento aceito pelo cliente
         ↓
      AGUARDANDO_RESERVA
         ├── Reserva recusada
         │      ↓
         │   Permanecer bloqueada
         │      ↓
         │   Reposição, revisão ou cancelamento
         │
         └── Reserva confirmada
                ↓
            Aprovação operacional do orçamento
                ↓
            EM_EXECUCAO
                ↓
            CONCLUIDA
```

A OS também poderá assumir o estado `CANCELADA` quando o atendimento for encerrado antes da conclusão operacional, respeitando as regras de liberação de reservas e rastreabilidade de estoque.

**Estados operacionais previstos:**

| Estado | Significado |
|---|---|
| `ABERTA` | OS criada e aguardando início do diagnóstico. |
| `EM_DIAGNOSTICO` | Avaliação técnica em andamento. |
| `AGUARDANDO_APROVACAO` | Orçamento apresentado e aguardando decisão do cliente. |
| `AGUARDANDO_RESERVA` | Cliente aceitou o orçamento e o sistema aguarda a confirmação da reserva integral das peças. |
| `EM_EXECUCAO` | Orçamento aprovado operacionalmente e execução autorizada. |
| `CONCLUIDA` | Serviços encerrados operacionalmente e reservas tratadas. |
| `CANCELADA` | Atendimento encerrado sem conclusão operacional. |

**Situação financeira independente:**

| Estado financeiro | Significado |
|---|---|
| `PENDENTE` | Nenhum pagamento ou valor ainda não quitado. |
| `PARCIAL` | Parte do valor devido foi paga. |
| `QUITADA` | Valor total devido foi pago. |

**Regras de controle:**

- `APROVADA` e `RECUSADA` são decisões do orçamento, não estados operacionais da OS.
- `AGUARDANDO_PAGAMENTO` não integra a esteira operacional.
- A OS pode estar `CONCLUIDA` e financeiramente `PENDENTE` ou `PARCIAL`.
- A OS não pode entrar em `EM_EXECUCAO` sem aprovação operacional e reserva confirmada.
- O cancelamento deve acionar a verificação e a tratativa das reservas existentes.
- As transições devem respeitar as regras RN-OS-003, RN-OS-004, RN-OS-005 e RN-OS-006.

**Status:** 🟢 Definido pelo grupo

---

## 15. Fluxo de comunicação e confiabilidade entre microserviços

As operações que envolvem mais de um microserviço devem respeitar a propriedade dos dados e a consistência local de cada serviço.

### 15.1. Solicitação de reserva

```text
Work Order Service
   ↓
Registrar solicitação e identificador de correlação
   ↓
Enviar comando de reserva ao Inventory Service
   ↓
Inventory Service recebe solicitação
   ↓
Validar tenant, itens e quantidades
   ↓
Executar reserva em transação local
   ↓
Registrar resultado e evento na Outbox
   ↓
Publicar evento de resultado
   ↓
Work Order Service recebe resultado
   ↓
Registrar resultado de forma idempotente
   ↓
Aprovar orçamento operacional
ou manter bloqueio em caso de recusa
```

### 15.2. Tratamento de mensagens duplicadas

```text
Consumidor recebe evento
   ↓
Verificar identificador do evento
   ↓
Evento já processado?
   ├── Sim → Ignorar efeitos duplicados
   │
   └── Não
        ↓
     Processar evento
        ↓
     Registrar processamento
        ↓
     Confirmar mensagem
```

### 15.3. Falhas e reprocessamento

```text
Falha na publicação ou consumo
   ↓
Identificar operação e mensagem
   ↓
Aplicar política de retentativa
   ↓
Reprocessar de forma idempotente
   ↓
Falha persistente?
   ├── Não → Continuar fluxo normal
   │
   └── Sim
        ↓
     Encaminhar para mecanismo de
     tratamento de mensagens não processadas
        ↓
     Monitorar e permitir recuperação controlada
```

**Status:** 🟢 Definido como diretriz arquitetural

**Observações:**

- Cada microserviço deve persistir e controlar seus próprios dados.
- A comunicação entre serviços não deve depender de acesso direto ao banco de dados de outro serviço.
- O padrão Transactional Outbox deve reduzir o risco de perda de eventos após a confirmação de uma transação.
- Os consumidores devem processar mensagens de forma idempotente.
- As mensagens devem carregar tenant e identificadores de correlação necessários para rastreabilidade.
- O fluxo não pressupõe transação distribuída entre os bancos dos microserviços.

---

**Última atualização:** 16/11/2026

**Grupo:** GarageOps
