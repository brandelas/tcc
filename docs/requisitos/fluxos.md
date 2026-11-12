# Fluxos do Sistema — OficinaPro

> **Documento de trabalho — Grupo GarageOps**
>
> Este documento consolida os fluxos operacionais e de exceção do MVP do OficinaPro, alinhados à esteira oficial da Ordem de Serviço e às regras de negócio de aprovação de orçamento, movimentação de estoque e rastreabilidade física das peças definidas pelo grupo.

---

## 1. Fluxo principal da oficina (MVP)

O fluxo operacional principal do OficinaPro segue a sequência abaixo:

```text
Cliente (CPF único)
   ↓
Veículo (Placa única)
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
   └── Aprovado
          ↓
      Verificar disponibilidade das peças
          ↓
      Baixa lógica das peças no saldo disponível
      (Inventory Service)
          ↓
      Execução do serviço (EM_EXECUCAO)
          ↓
      Registrar a utilização física das peças
          ↓
      Aguardando pagamento (AGUARDANDO_PAGAMENTO)
          ↓
      Quitação financeira integral (Billing Service)
          ↓
      OS concluída (CONCLUIDA)
          ↓
      Atualização do histórico de manutenção do veículo
```

**Status:** 🟢 Definido pelo grupo

**Observação:** A aprovação e a recusa são decisões sobre o orçamento, não status da OS. A aprovação permite a continuidade do atendimento e dispara a baixa lógica do estoque. A condição física das peças é registrada separadamente durante a execução. A OS somente é concluída após a quitação integral.

---

## 2. Fluxo de cadastro de cliente

```text
Início
  ↓
Acessar cadastro de cliente
  ↓
Informar dados obrigatórios
(Nome, CPF, Telefone, E-mail e Endereço)
  ↓
Validar formato e unicidade do CPF (RN-CLI-001)
  ↓
CPF já cadastrado?
  ├── Sim → Bloquear duplicidade e apresentar cadastro existente
  │
  └── Não
       ↓
   Persistir cliente
       ↓
      Fim
```

**Status:** 🟢 Definido pelo grupo

---

## 3. Fluxo de cadastro de veículo

```text
Início
  ↓
Selecionar cliente existente (RN-VEI-001)
  ↓
Informar dados do veículo
(Placa, Marca, Modelo, Ano, Cor e Quilometragem)
  ↓
Validar unicidade da placa (RN-VEI-002)
  ↓
Placa já cadastrada?
  ├── Sim → Bloquear duplicidade e consultar o veículo existente
  │
  └── Não
       ↓
   Persistir veículo vinculado ao cliente
       ↓
      Fim
```

**Status:** 🟢 Definido pelo grupo

---

## 4. Fluxo de abertura de Ordem de Serviço

```text
Início
  ↓
Selecionar cliente e veículo cadastrados
  ↓
Informar problema relatado e quilometragem atual
  ↓
Gerar identificador único da OS
  ↓
Atribuir status inicial: ABERTA
  ↓
Persistir OS
  ↓
Fim
```

**Status:** 🟢 Definido pelo grupo

---

## 5. Fluxo de diagnóstico

```text
OS com status ABERTA
   ↓
Mecânico inicia avaliação técnica
   ↓
OS assume status: EM_DIAGNOSTICO
   ↓
Registrar laudo técnico e observações vinculadas à OS (RN-DIA-001)
   ↓
Identificar serviços e peças necessários
   ↓
Encaminhar informações para elaboração do orçamento
```

**Status:** 🟢 Definido pelo grupo

---

## 6. Fluxo de elaboração do orçamento

```text
Diagnóstico registrado
   ↓
Selecionar serviços e peças com respectivas quantidades
   ↓
Consultar disponibilidade das peças no estoque
   ↓
Calcular total automaticamente:
(Serviços + Peças) - Descontos (RN-ORC-001)
   ↓
Vincular orçamento à OS
   ↓
OS assume status: AGUARDANDO_APROVACAO
   ↓
Enviar orçamento para decisão do cliente
```

**Status:** 🟢 Definido pelo grupo

**Observação:** A consulta de disponibilidade durante a elaboração do orçamento não substitui a validação obrigatória no momento da aprovação. Caso não exista quantidade suficiente, a aprovação deve ser bloqueada (RN-EST-002).

---

## 7. Fluxo de aprovação ou recusa do orçamento

```text
OS em AGUARDANDO_APROVACAO
       ↓
Registrar decisão formal do cliente
  ├── Aprovado
  │      ↓
  │   Verificar novamente a disponibilidade das peças (RN-EST-002)
  │      ↓
  │   Estoque suficiente?
  │      ├── Não → Bloquear aprovação
  │      └── Sim
  │           ↓
  │       Registrar aprovação do orçamento
  │           ↓
  │       Disparar baixa lógica do estoque (RN-EST-003)
  │           ↓
  │       Registrar movimentação vinculada à OS e ao orçamento (RN-EST-004)
  │           ↓
  │       Liberar OS para execução
  │
  └── Recusado
         ↓
      Registrar recusa do orçamento
         ↓
      Escolher tratativa (RN-ORC-003):
         ├── Encerrar definitivamente a OS
         └── Elaborar novo orçamento revisado
                ↓
             Preservar orçamento e decisão anteriores
                ↓
             Vincular novo orçamento à mesma OS
                ↓
             OS retorna para AGUARDANDO_APROVACAO
```

**Status:** 🟢 Definido pelo grupo

---

## 8. Fluxo de execução do serviço e rastreabilidade física

```text
OS com orçamento formalmente aprovado
       ↓
Verificar se a baixa lógica foi realizada
       ↓
Mecânico inicia o atendimento
       ↓
OS assume status: EM_EXECUCAO
       ↓
Identificar as peças vinculadas à OS
       ↓
Registrar a condição física de cada peça
       ├── PENDENTE_INSTALACAO
       │      ↓
       │   Peça baixada logicamente,
       │   mas ainda não instalada ou consumida
       │
       └── INSTALADA/CONSUMIDA
              ↓
           Peça fisicamente utilizada
           no serviço ou veículo
       ↓
Finalizar execução técnica
       ↓
OS assume status: AGUARDANDO_PAGAMENTO
```

**Status:** 🟢 Definido pelo grupo

**Observação:** A condição física deve ser acompanhada por peça ou item da movimentação, e não apenas pela OS inteira. Isso permite tratar corretamente situações em que parte das peças foi utilizada e outra parte permaneceu intacta.

---

## 9. Fluxo de estoque (Entrada, Baixa, Consumo Físico e Estorno)

### 9.1. Entrada de peças

```text
Início
   ↓
Informar peça e quantidade
   ↓
Validar dados da movimentação
   ↓
Incrementar saldo disponível
   ↓
Registrar movimentação de entrada
   ↓
Fim
```

### 9.2. Baixa lógica na aprovação

```text
Orçamento submetido à aprovação
   ↓
Verificar saldo disponível (RN-EST-002)
   ├── Insuficiente
   │      ↓
   │   Bloquear aprovação
   │
   └── Suficiente
          ↓
      Registrar aprovação do orçamento
          ↓
      Subtrair quantidade do saldo disponível (RN-EST-003)
          ↓
      Registrar movimentação de saída
      vinculada à OS e ao orçamento (RN-EST-004)
          ↓
      Registrar condição física inicial:
      PENDENTE_INSTALACAO
          ↓
      Impedir baixa duplicada
```

**Observação:** A baixa lógica reduz o saldo disponível, mas não confirma que a peça já foi instalada ou consumida fisicamente.

### 9.3. Atualização da condição física durante a execução

```text
Peça com baixa lógica registrada
   ↓
Mecânico utiliza a peça no serviço?
   ├── Não
   │    ↓
   │  Manter condição: PENDENTE_INSTALACAO
   │
   └── Sim
        ↓
      Atualizar condição:
      INSTALADA/CONSUMIDA
        ↓
      Registrar a utilização vinculada à OS
```

### 9.4. Estorno e tratamento em caso de cancelamento

```text
OS cancelada após aprovação do orçamento
   ↓
Consultar condição física de cada peça baixada
   ├── PENDENTE_INSTALACAO (RN-EST-005)
   │      ↓
   │   Verificar se a peça permanece íntegra
   │      ↓
   │   Registrar estorno vinculado à saída original
   │      ↓
   │   Devolver quantidade ao estoque disponível
   │
   └── INSTALADA/CONSUMIDA (RN-EST-006)
          ↓
       Bloquear retorno ao estoque comercial
          ↓
       Registrar perda operacional ou descarte
```

**Regras de controle:**

- Toda movimentação deve manter vínculo com a OS e o orçamento correspondentes.
- A condição física deve ser registrada individualmente para cada peça ou item movimentado.
- O estorno deve estar relacionado à movimentação de saída original.
- Uma mesma baixa não pode ser estornada ou tratada como perda mais de uma vez.
- Peças instaladas ou consumidas não podem retornar ao saldo comercial disponível.
- Peças ainda não utilizadas somente podem retornar ao estoque disponível se estiverem em condições de reutilização.

**Status:** 🟢 Definido pelo grupo

---

## 10. Fluxo de pagamento e quitação

```text
OS em AGUARDANDO_PAGAMENTO
   ↓
Registrar pagamento no Billing Service
(Valor, Data e Forma de Pagamento)
   ↓
O valor acumulado dos pagamentos quita o total da OS? (RN-FIN-002)
   ├── Não
   │      ↓
   │   Manter OS em AGUARDANDO_PAGAMENTO
   │
   └── Sim
          ↓
      Confirmar quitação financeira integral
          ↓
      OS assume status: CONCLUIDA
          ↓
      Atualizar histórico de manutenção do veículo
```

**Status:** 🟢 Definido pelo grupo

---

## 11. Fluxo de histórico de manutenção

```text
Atendimento encerrado
(CONCLUIDA ou CANCELADA)
    ↓
Consolidar informações do atendimento:
- Diagnósticos e laudos
- Orçamentos e decisões do cliente
- Serviços realizados
- Peças e movimentações de estoque
- Condição física das peças
- Registros financeiros
- Status final da OS
    ↓
Manter vínculo com o veículo
    ↓
Disponibilizar informações para consulta futura
```

**Status:** 🟢 Definido pelo grupo

**Observação:** O histórico deve preservar os registros do atendimento, inclusive quando houver recusa de orçamento, revisão de orçamento ou cancelamento da OS. O encerramento de um atendimento não deve apagar seu histórico.

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
   Identificar perfil do usuário
       ↓
   Verificar permissões associadas ao perfil
       ↓
   Liberar acesso às funcionalidades autorizadas
```

**Perfis definidos pelo grupo:**

- Atendente
- Mecânico
- Estoque
- Financeiro

**Status:** 🟢 Definido pelo grupo

---

## 13. Fluxo de notificações (Pós-MVP)

```text
Evento relevante
(ex: Orçamento aprovado ou OS concluída)
      ↓
Identificar destinatário e canal
      ↓
Preparar notificação
      ↓
Disparar notificação
```

**Status:** ⚪ Pós-MVP — eventual necessidade mínima a validar com o professor.

---

## 14. Esteira oficial de status da Ordem de Serviço

A esteira principal de status da OS é:

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
  └── Orçamento aprovado
         ↓
      Baixa lógica do estoque
         ↓
      EM_EXECUCAO
         ↓
      AGUARDANDO_PAGAMENTO
         ↓
      CONCLUIDA
```

**Fluxo de exceção:** A OS também pode assumir o status `CANCELADA` antes da conclusão, respeitando as regras de estoque aplicáveis:

- `RN-EST-005`: estorno de peças não instaladas e não consumidas, desde que permaneçam em condições de reutilização.
- `RN-EST-006`: registro de perda operacional ou descarte de peças instaladas ou consumidas.

**Status:** 🟢 Definido pelo grupo

---

**Última atualização:** 12/11/2026

**Grupo:** GarageOps
