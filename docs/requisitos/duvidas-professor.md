# Dúvidas para validação com o professor — OficinaPro

> **Status:** 🔵 Validar com professor
> **Objetivo:** organizar dúvidas, decisões acadêmicas e pontos técnicos que precisam de orientação ou validação durante o desenvolvimento do OficinaPro.
>
> Este documento não representa decisões definitivas. Ele funciona como uma pauta contínua para as reuniões com o professor.

---

## 1. Escopo e MVP

### 1.1 Escopo mínimo obrigatório

**Pergunta:**

Quais funcionalidades são obrigatórias para que o OficinaPro seja considerado um MVP válido para o TCC?

**Contexto:**

A equipe elaborou uma proposta inicial contendo:

```text
Cliente
→ Veículo
→ Ordem de Serviço
→ Diagnóstico
→ Orçamento
→ Aprovação
→ Execução
→ Estoque
→ Conclusão
→ Pagamento
→ Histórico
```

**Status:** 🔵 Pendente de validação

---

### 1.2 Funcionalidades complementares

Notificações, relatórios, dashboard e outras funcionalidades deverão fazer parte do MVP ou podem ser consideradas funcionalidades pós-MVP?

**Status:** 🔵 Pendente de validação

---

### 1.3 Alteração do escopo

Caso novas necessidades sejam identificadas durante o desenvolvimento, qual deverá ser o processo para inclusão ou retirada de funcionalidades do escopo?

**Status:** 🔵 Pendente de validação

---

## 2. Arquitetura

### 2.1 Microserviços

A documentação inicial apresenta uma arquitetura baseada em microserviços:

```text
Auth
Customer Vehicle
Work Order
Inventory
Billing
Notification
Reporting
```

**Pergunta:**

A utilização de todos esses microserviços é obrigatória ou a equipe pode ajustar a divisão conforme a complexidade e o escopo do MVP?

**Status:** 🔵 Pendente de validação

---

### 2.2 Quantidade de serviços

Existe uma quantidade mínima ou máxima de microserviços esperada para o projeto?

**Status:** 🔵 Pendente de validação

---

### 2.3 Comunicação entre serviços

A comunicação entre os serviços deverá obrigatoriamente utilizar RabbitMQ ou o uso de mensageria poderá ser restrito a determinados fluxos?

**Status:** 🔵 Pendente de validação

---

### 2.4 Comunicação síncrona e assíncrona

É esperado que a equipe demonstre uma combinação de comunicação síncrona por API e comunicação assíncrona por eventos/mensagens?

**Status:** 🔵 Pendente de validação

---

## 3. Banco de dados

### 3.1 Modelo de dados

É necessário entregar um diagrama entidade-relacionamento (DER/ERD) como parte da documentação do TCC?

**Status:** 🔵 Pendente de validação

---

### 3.2 Banco por serviço

Considerando a arquitetura de microserviços, é necessário utilizar um banco de dados separado por serviço ou um PostgreSQL compartilhado é aceitável para o projeto acadêmico?

**Status:** 🔵 Pendente de validação

---

### 3.3 Migrations

Existe alguma ferramenta de migrations recomendada ou obrigatória?

**Status:** 🔵 Pendente de validação

---

## 4. Testes

### 4.1 Tipos de testes

Quais tipos de testes são obrigatórios?

Possibilidades:

* testes unitários;
* testes de integração;
* testes de API;
* testes de frontend;
* testes end-to-end.

**Status:** 🔵 Pendente de validação

---

### 4.2 Cobertura mínima

O README apresenta uma meta inicial de 60% de cobertura.

**Pergunta:**

A cobertura mínima de 60% deve ser mantida como requisito obrigatório ou essa meta pode ser alterada?

**Status:** 🔵 Pendente de validação

---

### 4.3 Testes no CI

Todos os testes deverão obrigatoriamente ser executados no pipeline de CI?

**Status:** 🔵 Pendente de validação

---

## 5. CI/CD

### 5.1 GitHub Actions

O uso do GitHub Actions é obrigatório?

**Status:** 🔵 Pendente de validação

---

### 5.2 Pipeline

Quais etapas mínimas devem existir no pipeline?

Proposta inicial:

```text
Push / Pull Request
        ↓
Instalação das dependências
        ↓
Lint / validações
        ↓
Testes
        ↓
Build
```

**Status:** 🔵 Pendente de validação

---

### 5.3 Deploy

É necessário realizar deploy da aplicação em ambiente AWS ou basta demonstrar a infraestrutura e o processo de provisionamento?

**Status:** 🔵 Pendente de validação

---

## 6. AWS e infraestrutura

### 6.1 AWS

A utilização de serviços AWS é obrigatória para a entrega final?

**Status:** 🔵 Pendente de validação

---

### 6.2 Terraform

A infraestrutura deverá obrigatoriamente ser provisionada por Terraform?

**Status:** 🔵 Pendente de validação

---

### 6.3 Ambiente

Quais componentes precisam obrigatoriamente estar disponíveis em ambiente remoto?

Exemplos:

* backend;
* frontend;
* banco de dados;
* RabbitMQ;
* observabilidade.

**Status:** 🔵 Pendente de validação

---

## 7. Segurança

### 7.1 Autenticação

A autenticação deverá fazer parte obrigatoriamente do MVP?

**Status:** 🔵 Pendente de validação

---

### 7.2 Perfis

É necessário implementar diferentes perfis de usuário?

Exemplos:

* administrador;
* atendente;
* mecânico;
* gestor.

**Status:** 🔵 Pendente de validação

---

### 7.3 Controle de acesso

É esperado que a equipe implemente autorização por perfil/permissão em todas as operações?

**Status:** 🔵 Pendente de validação

---

## 8. Mensageria e eventos

### 8.1 RabbitMQ

Quais fluxos devem utilizar RabbitMQ para demonstrar efetivamente o uso de mensageria?

Possibilidades:

```text
OS criada
Orçamento aprovado
Peça utilizada
Pagamento registrado
OS concluída
Notificação enviada
```

**Status:** 🔵 Pendente de validação

---

### 8.2 Eventos

É necessário implementar uma arquitetura orientada a eventos ou basta utilizar RabbitMQ em alguns fluxos específicos?

**Status:** 🔵 Pendente de validação

---

## 9. Observabilidade

### 9.1 Logs

Qual nível de logging é esperado para a avaliação?

**Status:** 🔵 Pendente de validação

---

### 9.2 Prometheus e Grafana

A utilização de Prometheus e Grafana é obrigatória ou apenas uma sugestão técnica presente no planejamento?

**Status:** 🔵 Pendente de validação

---

### 9.3 Métricas

Quais métricas seriam relevantes ou esperadas para o projeto?

**Status:** 🔵 Pendente de validação

---

## 10. Documentação

### 10.1 Documentação técnica

Quais documentos são obrigatórios para a entrega?

Possibilidades:

* arquitetura;
* requisitos;
* histórias de usuário;
* regras de negócio;
* critérios de aceite;
* DER;
* diagramas de sequência;
* documentação de APIs;
* manual de execução;
* manual do usuário.

**Status:** 🔵 Pendente de validação

---

### 10.2 Diagramas

Quais diagramas deverão ser apresentados?

Possibilidades:

* arquitetura;
* casos de uso;
* sequência;
* entidade-relacionamento;
* fluxo do sistema;
* infraestrutura.

**Status:** 🔵 Pendente de validação

---

### 10.3 Apresentação

Quais evidências deverão ser apresentadas na avaliação final?

**Status:** 🔵 Pendente de validação

---

## 11. Processo de desenvolvimento

### 11.1 Branches

A estratégia atual considera:

```text
main
develop
feature/*
fix/*
docs/*
```

**Pergunta:**

Essa estratégia atende às expectativas do projeto ou existe algum fluxo Git obrigatório?

**Status:** 🔵 Pendente de validação

---

### 11.2 Pull Requests

Todos os trabalhos deverão passar por Pull Request antes de serem integrados à branch principal?

**Status:** 🔵 Pendente de validação

---

### 11.3 Code Review

É necessário que todos os integrantes realizem revisão de código?

**Status:** 🔵 Pendente de validação

---

## 12. Entregas acadêmicas

### 12.1 Entregas intermediárias

Quais partes do projeto deverão ser apresentadas durante as etapas intermediárias?

**Status:** 🔵 Pendente de validação

---

### 12.2 Critérios de avaliação

Quais critérios serão utilizados para avaliar:

* requisitos;
* arquitetura;
* código;
* testes;
* infraestrutura;
* documentação;
* apresentação?

**Status:** 🔵 Pendente de validação

---

### 12.3 Participação individual

Como será avaliada a contribuição individual de cada integrante em um projeto desenvolvido em grupo?

**Status:** 🔵 Pendente de validação

---

## 13. Questões sobre o domínio da oficina

### 13.1 Orçamento recusado

Quando o cliente recusar um orçamento, a OS deverá:

* ser encerrada;
* permanecer aberta;
* permitir novo orçamento;
* seguir outro fluxo?

**Status:** 🔵 Pendente de validação da regra com a equipe.

---

### 13.2 Estoque insuficiente

Quando uma peça necessária não estiver disponível, o sistema deverá:

* impedir a execução;
* permitir estoque negativo;
* gerar uma pendência;
* permitir substituição da peça?

**Status:** 🔵 Pendente de validação da regra com a equipe.

---

### 13.3 Pagamento

O projeto precisa integrar algum gateway ou sistema externo de pagamento ou basta registrar o pagamento no sistema?

**Status:** 🔵 Pendente de validação

---

### 13.4 Notificações ao cliente

É necessário implementar notificações reais para o cliente ou basta demonstrar o mecanismo internamente?

**Status:** 🔵 Pendente de validação

---

## 14. Perguntas prioritárias para a próxima reunião

Para evitar uma reunião excessivamente extensa, estas são as perguntas que mais impactam o planejamento inicial:

1. **Qual é exatamente o escopo obrigatório do MVP?**
2. **Quais funcionalidades podem ficar para pós-MVP?**
3. **A arquitetura de microserviços proposta é obrigatória ou pode ser ajustada?**
4. **RabbitMQ precisa ser utilizado em quais fluxos?**
5. **AWS e Terraform precisam estar efetivamente funcionando em produção/homologação?**
6. **Quais tipos de testes são obrigatórios?**
7. **A meta de 60% de cobertura é obrigatória?**
8. **Quais diagramas e documentos precisam ser entregues?**
9. **Prometheus/Grafana são obrigatórios?**
10. **Como será avaliada a contribuição individual dos integrantes?**

---

## 15. Registro das respostas

As respostas obtidas nas reuniões deverão ser registradas abaixo.

| Data | Pergunta/tema | Orientação recebida | Impacto no projeto |
| ---- | ------------- | ------------------- | ------------------ |
| —    | —             | —                   | —                  |

---

## 16. Decisões derivadas das orientações

Quando uma orientação do professor resultar em uma mudança concreta no projeto, registrar aqui.

| Data | Decisão | Origem | Documentos afetados |
| ---- | ------- | ------ | ------------------- |
| —    | —       | —      | —                   |

---

## 17. Histórico de alterações

| Data       | Alteração                           | Responsável   |
| ---------- | ----------------------------------- | ------------- |
| 2026-09-26 | Criação da lista inicial de dúvidas | Product Owner |

> Este documento deve ser atualizado após cada reunião relevante com o professor.
