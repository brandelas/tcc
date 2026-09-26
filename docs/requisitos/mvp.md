# MVP — OficinaPro

> **Status:** 🟡 Em discussão
> **Responsável pela análise:** Product Owner / Requirements Analyst
> **Objetivo:** definir uma proposta inicial de escopo mínimo viável para o OficinaPro.
>
> Este documento apresenta uma **proposta de MVP**, não uma definição definitiva do escopo do projeto. Os itens poderão ser ajustados após discussão com a equipe e validação com o professor.

---

## 1. Objetivo do MVP

O MVP (Minimum Viable Product) do OficinaPro tem como objetivo disponibilizar um fluxo funcional de gestão de serviços de uma oficina mecânica, permitindo acompanhar uma ordem de serviço desde o cadastro do cliente e veículo até a conclusão do serviço e registro do pagamento.

A proposta é priorizar o fluxo operacional principal, evitando que funcionalidades complementares aumentem o escopo antes que o fluxo central esteja funcionando de ponta a ponta.

---

## 2. Fluxo principal proposto

O fluxo principal considerado para o MVP é:

```text
Cliente
   ↓
Veículo
   ↓
Ordem de Serviço
   ↓
Diagnóstico
   ↓
Orçamento
   ↓
Aprovação / Recusa
   ↓
Execução do Serviço
   ↓
Movimentação de Estoque
   ↓
Conclusão da OS
   ↓
Pagamento
   ↓
Histórico de Manutenção
```

Esse fluxo representa a principal jornada operacional que o sistema deverá suportar.

---

## 3. Funcionalidades propostas para o MVP

### 3.1 Clientes

O sistema deverá permitir:

* cadastrar cliente;
* consultar cliente;
* atualizar dados do cliente;
* identificar o cliente relacionado às ordens de serviço.

**Prioridade:** 🔴 Essencial

---

### 3.2 Veículos

O sistema deverá permitir:

* cadastrar veículo;
* relacionar veículo a um cliente;
* consultar dados do veículo;
* identificar o veículo relacionado à OS;
* consultar informações básicas necessárias para o atendimento.

**Prioridade:** 🔴 Essencial

---

### 3.3 Ordem de Serviço

O sistema deverá permitir:

* abrir uma ordem de serviço;
* relacionar a OS a um cliente e veículo;
* registrar informações iniciais do atendimento;
* consultar a OS;
* acompanhar o status da OS;
* atualizar o status conforme o andamento do serviço.

### Status propostos

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

Também deverá existir a possibilidade de encerramento por recusa:

```text
AGUARDANDO_APROVACAO
↓
RECUSADA
```

> Os status e suas transições são uma proposta inicial e deverão ser validados pela equipe.

**Prioridade:** 🔴 Essencial

---

### 3.4 Diagnóstico

O sistema deverá permitir:

* registrar o diagnóstico do veículo;
* associar o diagnóstico à OS;
* consultar o diagnóstico posteriormente;
* utilizar as informações do diagnóstico como base para o orçamento.

**Prioridade:** 🔴 Essencial

---

### 3.5 Orçamento

O sistema deverá permitir:

* criar orçamento relacionado à OS;
* registrar serviços;
* registrar peças;
* calcular o valor total;
* apresentar o orçamento para aprovação;
* registrar aprovação ou recusa.

**Prioridade:** 🔴 Essencial

---

### 3.6 Execução do serviço

Após a aprovação do orçamento, o sistema deverá permitir:

* iniciar a execução do serviço;
* acompanhar o andamento da OS;
* registrar informações necessárias para a conclusão;
* finalizar o serviço.

**Prioridade:** 🔴 Essencial

---

### 3.7 Estoque

O MVP deverá possuir uma versão básica de controle de estoque.

A proposta inclui:

* cadastro de peças;
* quantidade disponível;
* entrada de peças;
* saída de peças;
* associação da saída de peças à execução de uma OS;
* atualização do saldo de estoque.

### Regra principal proposta

Quando uma peça for utilizada em uma OS, sua quantidade deverá ser descontada do estoque.

Caso não exista quantidade suficiente disponível, o sistema deverá impedir ou sinalizar a operação, conforme regra que será definida pela equipe.

**Prioridade:** 🔴 Essencial

---

### 3.8 Pagamento

O sistema deverá permitir:

* calcular o valor final da OS;
* registrar o pagamento;
* identificar a situação do pagamento;
* relacionar o pagamento à OS.

A forma de pagamento e possíveis regras de pagamento deverão ser definidas posteriormente.

**Prioridade:** 🔴 Essencial

---

### 3.9 Histórico de manutenção

O sistema deverá permitir consultar o histórico de serviços realizados para determinado veículo.

O histórico deverá possibilitar a visualização, conforme o modelo definido pela equipe, de informações como:

* data do atendimento;
* número da OS;
* diagnóstico;
* serviços realizados;
* peças utilizadas;
* valor;
* situação da OS.

**Prioridade:** 🟡 Importante

---

### 3.10 Autenticação e permissões

A proposta inicial inclui:

* autenticação de usuários;
* controle de acesso;
* diferenciação de permissões conforme perfil.

Os perfis e suas permissões ainda precisam ser definidos pela equipe.

**Prioridade:** 🟡 Importante

---

## 4. Funcionalidades que podem ficar fora do MVP

Para evitar aumento excessivo de escopo, as seguintes funcionalidades podem ser tratadas como complementares ou pós-MVP:

### 4.1 Notificações

Possíveis funcionalidades:

* aviso de orçamento aguardando aprovação;
* aviso de aprovação ou recusa;
* aviso de serviço concluído;
* notificações relacionadas ao pagamento.

**Status:** 🔵 Validar com professor

---

### 4.2 Relatórios

Possíveis relatórios:

* faturamento;
* serviços realizados;
* peças utilizadas;
* estoque;
* quantidade de OS por período;
* histórico financeiro.

**Status:** 🔵 Validar com professor

---

### 4.3 Funcionalidades adicionais

Também poderão ser consideradas posteriormente:

* dashboard gerencial;
* indicadores;
* exportação de dados;
* integração com serviços externos;
* funcionalidades avançadas para clientes;
* recursos adicionais de comunicação.

**Status:** ⚪ Pós-MVP / conforme disponibilidade de tempo

---

## 5. Priorização inicial

| Funcionalidade             | Prioridade | Status                   |
| -------------------------- | ---------- | ------------------------ |
| Cadastro de clientes       | Essencial  | 🟡 Em discussão          |
| Cadastro de veículos       | Essencial  | 🟡 Em discussão          |
| Ordem de serviço           | Essencial  | 🟡 Em discussão          |
| Diagnóstico                | Essencial  | 🟡 Em discussão          |
| Orçamento                  | Essencial  | 🟡 Em discussão          |
| Aprovação/recusa           | Essencial  | 🟡 Em discussão          |
| Execução do serviço        | Essencial  | 🟡 Em discussão          |
| Controle básico de estoque | Essencial  | 🟡 Em discussão          |
| Pagamento                  | Essencial  | 🟡 Em discussão          |
| Histórico de manutenção    | Importante | 🟡 Em discussão          |
| Autenticação               | Importante | 🟡 Em discussão          |
| Notificações               | A definir  | 🔵 Validar com professor |
| Relatórios                 | A definir  | 🔵 Validar com professor |
| Dashboard                  | Pós-MVP    | ⚪ Pós-MVP                |
| Integrações externas       | Pós-MVP    | ⚪ Pós-MVP                |

---

## 6. Critérios gerais para considerar o MVP funcional

A proposta é considerar o MVP funcional quando o sistema conseguir executar o fluxo principal de forma integrada:

```text
Cliente
→ Veículo
→ OS
→ Diagnóstico
→ Orçamento
→ Aprovação
→ Execução
→ Estoque
→ Conclusão
→ Pagamento
→ Histórico
```

Além do fluxo funcional, deverão ser considerados:

* persistência dos dados;
* validação das informações;
* controle de acesso;
* tratamento de erros;
* testes automatizados definidos pelo projeto;
* documentação mínima necessária;
* execução do ambiente de desenvolvimento;
* integração entre os componentes necessários.

---

## 7. Dependências do MVP

A implementação do MVP depende da definição e/ou implementação de:

* modelo de dados;
* relacionamento entre clientes, veículos e OS;
* regras de negócio;
* arquitetura dos serviços;
* autenticação;
* comunicação entre serviços;
* banco de dados;
* ambiente de desenvolvimento;
* estratégia de testes;
* pipeline de CI/CD;
* infraestrutura de execução.

Esses itens poderão ser refinados conforme a equipe avançar na arquitetura técnica.

---

## 8. Questões que precisam de validação

Antes de considerar o MVP definido, recomenda-se discutir:

* O histórico de manutenção fará parte obrigatoriamente do MVP?
* Autenticação e diferentes perfis serão obrigatórios no MVP?
* Notificações fazem parte do MVP ou serão pós-MVP?
* Relatórios fazem parte do MVP?
* O pagamento será apenas um registro ou haverá alguma integração externa?
* Quais formas de pagamento serão suportadas?
* Quais perfis de usuário existirão?
* Quais permissões cada perfil terá?
* Quais status de OS serão obrigatórios?
* Uma OS recusada poderá ser reaberta?
* O estoque poderá ficar negativo?
* Como será tratada a falta de uma peça durante a execução?
* Quais funcionalidades são obrigatórias para a avaliação do TCC?
* Quais requisitos técnicos o professor espera que estejam efetivamente implementados?

---

## 9. Limite de escopo

A prioridade do projeto deverá ser entregar um fluxo principal funcional antes de ampliar o sistema com funcionalidades complementares.

Caso surjam novas funcionalidades durante o desenvolvimento, recomenda-se classificá-las como:

* 🔴 Necessária para o MVP;
* 🟡 Importante, mas pode aguardar;
* ⚪ Pós-MVP;
* 🔵 Necessita validação do professor.

Isso permite controlar o crescimento do escopo sem impedir novas ideias.

---

## 10. Histórico de alterações

| Data       | Alteração                          | Responsável   |
| ---------- | ---------------------------------- | ------------- |
| 2026-09-26 | Criação da proposta inicial de MVP | Product Owner |

> Este documento deverá ser atualizado conforme as decisões da equipe e as orientações recebidas durante as reuniões com o professor.
