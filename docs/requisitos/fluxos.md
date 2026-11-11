# Fluxos do Sistema — OficinaPro

**Documento de trabalho — sujeito à validação do grupo e do professor.**

> Este documento representa os fluxos funcionais inicialmente identificados para o OficinaPro. Os fluxos poderão ser alterados conforme as decisões do grupo, os requisitos do MVP e as orientações do professor.

---

# 1. Fluxo principal da oficina

O fluxo principal proposto para o OficinaPro é:

```text
Cliente
   ↓
Veículo
   ↓
Abertura da Ordem de Serviço
   ↓
Diagnóstico
   ↓
Elaboração do Orçamento
   ↓
Aprovação do Orçamento
   ├── Recusado → OS encerrada/recusada
   │
   └── Aprovado
          ↓
     Execução do Serviço
          ↓
     Utilização de Peças
          ↓
     Atualização do Estoque
          ↓
     Finalização da OS
          ↓
     Pagamento
          ↓
     Histórico de Manutenção
```

**Status:** 🟡 Em discussão

**Pendências:**

- Validar o fluxo com o grupo.
- Confirmar o momento da baixa de estoque.
- Confirmar se o pagamento ocorre antes ou depois da conclusão da OS.
- Definir o comportamento de uma OS com orçamento recusado.

---

# 2. Fluxo de cadastro de cliente

```text
Início
  ↓
Acessar cadastro de cliente
  ↓
Informar dados
  ↓
Validar informações
  ↓
Cliente já existe?
  ├── Sim → Informar/consultar cadastro existente
  │
  └── Não
       ↓
    Criar cliente
       ↓
      Fim
```

### Regras propostas

- O sistema deve validar os campos obrigatórios.
- O sistema deve evitar duplicidade conforme as regras definidas para identificação do cliente.
- Clientes existentes devem poder ser consultados antes de um novo cadastro.

**Status:** 🟡 Em discussão

---

# 3. Fluxo de cadastro de veículo

```text
Início
  ↓
Selecionar cliente
  ↓
Cadastrar veículo
  ↓
Informar dados do veículo
  ↓
Validar informações
  ↓
Veículo já cadastrado?
  ├── Sim → Consultar/atualizar veículo
  │
  └── Não
       ↓
    Criar veículo
       ↓
      Fim
```

### Regras propostas

- O veículo deve estar associado a um cliente.
- A placa poderá ser utilizada como identificador do veículo, conforme validação da regra pelo grupo.
- O sistema deve evitar duplicidade de veículos.

**Status:** 🟡 Em discussão

---

# 4. Fluxo de abertura de Ordem de Serviço

```text
Início
  ↓
Selecionar cliente
  ↓
Selecionar veículo
  ↓
Informar problema/solicitação
  ↓
Registrar data
  ↓
Criar OS
  ↓
Definir status inicial
  ↓
Fim
```

### Resultado esperado

A ordem de serviço deve possuir uma identificação própria e ficar disponível para acompanhamento.

**Status:** 🟡 Em discussão

---

# 5. Fluxo de diagnóstico

```text
OS aberta
   ↓
Veículo encaminhado para diagnóstico
   ↓
Mecânico realiza avaliação
   ↓
Registrar diagnóstico
   ↓
Identificar serviços necessários
   ↓
Identificar peças necessárias
   ↓
Encaminhar informações para orçamento
```

**Status:** 🟡 Em discussão

### Pendências

- Definir se uma OS pode possuir mais de um diagnóstico.
- Definir se o diagnóstico pode ser alterado após o orçamento.
- Definir quais usuários podem visualizar ou editar o diagnóstico.

---

# 6. Fluxo de elaboração do orçamento

```text
Diagnóstico
   ↓
Identificar serviços
   ↓
Identificar peças
   ↓
Informar quantidades
   ↓
Calcular valores
   ↓
Aplicar desconto, se permitido
   ↓
Calcular total
   ↓
Enviar orçamento para aprovação
```

### Cálculo proposto

```text
Valor dos serviços
        +
Valor das peças
        -
Descontos
        =
Total da OS
```

**Status:** 🟡 Em discussão

---

# 7. Fluxo de aprovação do orçamento

```text
Orçamento criado
       ↓
Aguardando aprovação
       ↓
Decisão
  ├── Aprovado
  │      ↓
  │   OS pode prosseguir
  │
  └── Recusado
         ↓
      Registrar recusa
         ↓
      Definir encerramento/
      alteração/revisão da OS
```

**Status:** 🟡 Em discussão

### Pendências

- Definir quem pode registrar a aprovação.
- Definir quem pode registrar a recusa.
- Definir se um orçamento recusado poderá ser revisado.
- Definir se uma nova versão do orçamento será criada.
- Definir o status da OS após a recusa.

---

# 8. Fluxo de execução do serviço

```text
Orçamento aprovado
       ↓
Iniciar execução
       ↓
Realizar serviços
       ↓
Utilizar peças necessárias
       ↓
Registrar execução
       ↓
Finalizar serviço
```

**Status:** 🟡 Em discussão

### Pendências

- Definir como os serviços executados serão registrados.
- Definir se será necessário registrar o mecânico responsável.
- Definir quando a OS poderá ser considerada concluída.

---

# 9. Fluxo de estoque

## Entrada de peças

```text
Início
  ↓
Registrar entrada
  ↓
Informar peça
  ↓
Informar quantidade
  ↓
Atualizar estoque
  ↓
Registrar movimentação
  ↓
Fim
```

## Saída de peças

```text
Peça necessária
  ↓
Consultar estoque
  ↓
Quantidade suficiente?
  ├── Não → Impedir/alertar saída
  │
  └── Sim
       ↓
    Registrar saída
       ↓
    Atualizar quantidade
       ↓
    Registrar movimentação
```

**Status:** 🟡 Em discussão

### Pendência principal

Definir quando a utilização da peça será efetivamente descontada do estoque:

- Na aprovação do orçamento;
- No início da execução;
- Durante a execução;
- Na conclusão da OS.

---

# 10. Fluxo de pagamento

```text
OS finalizada
   ↓
Valor total definido
   ↓
Registrar pagamento
   ↓
Pagamento corresponde ao valor total?
   ├── Sim → OS pode ser considerada quitada
   │
   └── Não → Definir tratamento
              para pagamento parcial
```

**Status:** 🟡 Em discussão

### Pendências

- Definir formas de pagamento.
- Definir se haverá pagamento parcial.
- Definir se haverá parcelamento.
- Definir quando o pagamento será registrado.
- Definir relação entre pagamento e encerramento da OS.

---

# 11. Fluxo de histórico de manutenção

```text
OS concluída
    ↓
Registrar informações relevantes
    ↓
Associar ao veículo
    ↓
Atualizar histórico
    ↓
Histórico disponível para consulta
```

**Status:** 🟡 Em discussão

O histórico deverá permitir consultar atendimentos anteriores relacionados ao veículo.

---

# 12. Fluxo de autenticação

```text
Início
  ↓
Usuário informa credenciais
  ↓
Validar credenciais
  ↓
Credenciais válidas?
  ├── Não → Informar erro
  │
  └── Sim
       ↓
    Identificar perfil
       ↓
    Liberar funcionalidades
       ↓
      Sistema
```

**Status:** 🟡 Em discussão

### Pendências

- Definir perfis.
- Definir permissões por perfil.
- Definir política de senha.
- Definir recuperação de acesso.

---

# 13. Fluxo de notificações

```text
Evento relevante
      ↓
Identificar tipo de evento
      ↓
Verificar destinatários
      ↓
Gerar notificação
      ↓
Enviar pelo canal definido
```

**Status:** 🔵 Validar com professor

### Possíveis eventos

- Orçamento aguardando aprovação.
- Orçamento aprovado.
- Orçamento recusado.
- OS concluída.
- Pagamento pendente.

### Pendências

- Definir se notificações fazem parte do MVP.
- Definir canais de comunicação.
- Definir eventos obrigatórios.

---

# 14. Fluxo de exceções

Além do fluxo principal, o sistema deverá considerar situações como:

### 14.1 Cliente inexistente

```text
Tentativa de abrir OS
        ↓
Cliente não encontrado
        ↓
Cadastrar cliente
        ↓
Continuar abertura da OS
```

**Status:** 🟡 Em discussão

### 14.2 Veículo inexistente

```text
Tentativa de abrir OS
        ↓
Veículo não encontrado
        ↓
Cadastrar veículo
        ↓
Continuar abertura da OS
```

**Status:** 🟡 Em discussão

### 14.3 Estoque insuficiente

```text
Peça necessária
      ↓
Consultar estoque
      ↓
Quantidade insuficiente
      ↓
Informar indisponibilidade
      ↓
Definir tratamento
```

**Status:** 🟡 Em discussão

### 14.4 Orçamento recusado

```text
Orçamento recusado
       ↓
Registrar decisão
       ↓
Definir próximo estado da OS
```

**Status:** 🟡 Em discussão

### 14.5 Pagamento incompleto

```text
Pagamento registrado
       ↓
Valor inferior ao total
       ↓
Registrar saldo
       ↓
Definir status financeiro
```

**Status:** 🟡 Em discussão

---

# 15. Transições propostas da Ordem de Serviço

A seguinte sequência é uma proposta inicial:

```text
ABERTA
  ↓
EM_DIAGNOSTICO
  ↓
AGUARDANDO_APROVACAO
  ↓
APROVADA
  ↓
EM_EXECUCAO
  ↓
AGUARDANDO_PAGAMENTO
  ↓
CONCLUIDA
```

Fluxo alternativo:

```text
AGUARDANDO_APROVACAO
        ↓
     RECUSADA
```

**Status:** 🟡 Em discussão

As transições definitivas deverão ser validadas pelo grupo.

---

# 16. Fluxo geral resumido

```text
┌──────────────┐
│    CLIENTE   │
└──────┬───────┘
       ↓
┌──────────────┐
│   VEÍCULO    │
└──────┬───────┘
       ↓
┌──────────────┐
│     OS       │
└──────┬───────┘
       ↓
┌──────────────┐
│ DIAGNÓSTICO  │
└──────┬───────┘
       ↓
┌──────────────┐
│  ORÇAMENTO   │
└──────┬───────┘
       ↓
   APROVAÇÃO?
    ↙     ↘
  NÃO       SIM
   ↓         ↓
RECUSADA  EXECUÇÃO
             ↓
          ESTOQUE
             ↓
          FINALIZAÇÃO
             ↓
          PAGAMENTO
             ↓
         HISTÓRICO
```

---

# 17. Pontos que precisam de validação

Os principais pontos que ainda não devem ser considerados decisões definitivas são:

1. Status oficiais da OS.
2. Transições permitidas entre status.
3. Momento da baixa de estoque.
4. Comportamento após recusa do orçamento.
5. Momento do pagamento.
6. Possibilidade de pagamento parcial.
7. Perfis e permissões.
8. Notificações no MVP.
9. Relatórios no MVP.
10. Nível de detalhamento dos microserviços.
11. Necessidade de mensageria para cada fluxo.
12. Requisitos técnicos obrigatórios definidos pelo professor.

---

# 18. Observação final

Este documento descreve o funcionamento esperado do sistema com base nos requisitos atualmente levantados.

Ele não representa uma decisão unilateral da Product Owner. Alterações que impactem o escopo, arquitetura, implementação ou entregas acadêmicas deverão ser discutidas pelo grupo e, quando necessário, validadas com o professor.

**Última atualização:** 11/11/2026

**Responsável pelo levantamento:** Eloísa Brandão
