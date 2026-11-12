# MVP (Minimum Viable Product) — OficinaPro

> **Status:** 🟢 Definido pelo grupo GarageOps  
> **Responsável pela análise:** Eloísa Brandão (Product Owner)  
> **Objetivo:** Estabelecer o escopo mínimo viável e obrigatório para a primeira entrega e validação do sistema OficinaPro no Projeto Experimental (5º Semestre ADS - UniFAAT).

---

## 1. Objetivo do MVP

O MVP do OficinaPro tem como objetivo entregar um fluxo operacional ponta a ponta funcional para oficinas mecânicas de pequeno e médio porte.

O sistema deve suportar toda a jornada do cliente e do veículo, desde a abertura da ordem de serviço até a quitação financeira e atualização do histórico de manutenção, garantindo consistência transacional e aderência aos requisitos técnicos exigidos pela disciplina.

---

## 2. Fluxo Principal do MVP

O fluxo central que o sistema obrigatoriamente deve executar de forma integrada é:

```text
Cliente
   ↓
Veículo
   ↓
Ordem de Serviço (ABERTA)
   ↓
Diagnóstico Técnico (EM_DIAGNOSTICO)
   ↓
Elaboração do Orçamento (AGUARDANDO_APROVACAO)
   ↓
Aprovação ou Recusa (APROVADA / RECUSADA)
   ↓
Execução do Serviço (EM_EXECUCAO)
   ↓
Baixa de Peças no Estoque (Inventory Service)
   ↓
Quitação Financeira (AGUARDANDO_PAGAMENTO → CONCLUIDA)
   ↓
Atualização do Histórico de Manutenção do Veículo
```

---

## 3. Funcionalidades Essenciais (Escopo do MVP)

### 3.1 Clientes

- Cadastro obrigatório com nome, CPF (único e validado), telefone, e-mail e endereço.
- Consulta e atualização de dados cadastrais.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.2 Veículos

- Cadastro vinculado obrigatoriamente a um cliente.
- Utilização da **placa** como identificador único.
- Registro de marca, modelo, ano, cor e quilometragem.
- Preservação do histórico de manutenção em caso de mudança de proprietário.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.3 Ordem de Serviço (OS)

- Abertura de OS vinculando cliente e veículo.
- Acompanhamento por meio da esteira oficial de status:

  `ABERTA` → `EM_DIAGNOSTICO` → `AGUARDANDO_APROVACAO` → `APROVADA` / `RECUSADA` → `EM_EXECUCAO` → `AGUARDANDO_PAGAMENTO` → `CONCLUIDA`.

- **Prioridade:** 🔴 Essencial (Definido).

### 3.4 Diagnóstico

- Registro da avaliação técnica realizada pelo mecânico, associada à OS.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.5 Orçamento

- Composição de serviços e peças com cálculo automático do valor total: `(Serviços + Peças) - Descontos`.
- Registro de aprovação ou recusa por parte do cliente.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.6 Execução e Estoque

- Controle de peças cadastradas com quantidade disponível.
- Registro de entradas e saídas (movimentações).
- Baixa automática de peças no estoque vinculada à aprovação/início da execução da OS.
- Bloqueio de saída caso o estoque seja insuficiente.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.7 Financeiro e Pagamento

- Registro de pagamento associado à OS para quitação e encerramento (`CONCLUIDA`).
- **Prioridade:** 🔴 Essencial (Definido).

### 3.8 Histórico de Manutenção

- Consulta consolidada de todos os atendimentos anteriores vinculados ao veículo.
- **Prioridade:** 🔴 Essencial (Definido).

### 3.9 Autenticação e Permissões

- Autenticação via Auth Service com hash seguro de senha.
- Controle de acesso baseado em perfis (Atendente, Mecânico, Estoque e Financeiro).
- **Prioridade:** 🔴 Essencial (Definido).

---

## 4. Funcionalidades Fora do Escopo Inicial (Pós-MVP / Secundárias)

Para evitar estouro de escopo e garantir a entrega dentro do semestre, os itens abaixo ficam classificados como opcionais ou secundários:

- Notificações avançadas por canais externos (SMS/WhatsApp).
- Relatórios gerenciais complexos e dashboards analíticos avançados.
- Pagamentos parciais / parcelados complexos.
- Integrações com gateways de pagamento reais (simulado localmente).

---

## 5. Matriz de Priorização Consolidada

| Funcionalidade | Prioridade | Status |
|---|---|---|
| Cadastro de Clientes (CPF único) | Essencial | 🟢 Definido |
| Cadastro de Veículos (Placa única) | Essencial | 🟢 Definido |
| Abertura e Gestão de OS (Esteira de Status) | Essencial | 🟢 Definido |
| Diagnóstico Técnico | Essencial | 🟢 Definido |
| Orçamento (Cálculo automático) | Essencial | 🟢 Definido |
| Aprovação / Recusa de Orçamento | Essencial | 🟢 Definido |
| Controle de Estoque e Baixa Automática | Essencial | 🟢 Definido |
| Financeiro (Quitação e Fechamento) | Essencial | 🟢 Definido |
| Histórico de Manutenção | Essencial | 🟢 Definido |
| Autenticação e Perfis (Auth Service) | Essencial | 🟢 Definido |
| Notificações / Alertas | Secundário | 🔵 Validar com Professor |
| Relatórios Gerenciais | Pós-MVP | ⚪ Pós-MVP |

---

## 6. Critérios de Sucesso do MVP

O MVP será considerado concluído com sucesso quando o grupo GarageOps conseguir demonstrar a execução integrada de ponta a ponta:

1. Ambiente local rodando via Docker Compose (PostgreSQL, Adminer, RabbitMQ).
2. Backend em NestJS estruturado em microsserviços comunicando-se de forma consistente.
3. Frontend em React consumindo as APIs.
4. Testes automatizados atingindo a meta de cobertura (≥ 60%) nas regras críticas.
5. Pipeline de CI/CD configurado no GitHub Actions e infraestrutura modelada via Terraform.

---

**Última atualização:** 11/11/2026

**Responsável:** Eloísa Brandão (Product Owner)
