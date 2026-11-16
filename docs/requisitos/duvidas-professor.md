# Dúvidas para validação com o professor — OficinaPro

> **Grupo:** GarageOps  
> **Projeto:** OficinaPro  
> **Data da revisão:** 16/11/2026  
> **Status:** Pauta atualizada para orientação acadêmica  
>
> **Objetivo:** registrar questões técnicas e acadêmicas que ainda dependem de orientação do professor, diferenciando-as dos requisitos já estabelecidos no Termo de Requisitos e Aceite do Projeto Experimental e das decisões de domínio já consolidadas pelo grupo.

---

## 1. Questões já respondidas pelo termo oficial

Os itens abaixo foram retirados da pauta de dúvidas porque o documento oficial do Projeto Experimental já estabelece as exigências correspondentes.

| Tema | Exigência oficial | Situação no OficinaPro |
|---|---|---|
| Arquitetura | A solução deve utilizar arquitetura de microsserviços. | Arquitetura planejada com quatro serviços principais no MVP. |
| API externa | O sistema deve consumir pelo menos uma API externa relevante ao domínio. | Integração prevista, mas a API ainda precisa ser escolhida e formalizada. |
| Banco de dados | A tecnologia deve ser tecnicamente justificada. | PostgreSQL escolhido como banco relacional, com estratégia Database-per-Service. |
| Cloud | A aplicação deve ser implantada em provedor de nuvem de grande porte. | AWS definida como proposta arquitetural. |
| Infraestrutura como código | O provisionamento deve ser automatizado com Terraform. | Terraform previsto na arquitetura. |
| CI/CD | Deve haver esteira com GitHub Actions e automação de build e deploy dos microsserviços. | GitHub Actions previsto. |
| Testes | Devem existir testes automatizados unitários ou de integração, com cobertura global mínima de 60%. | Meta registrada nos requisitos não funcionais. |
| Monitoramento | Prometheus e Grafana devem estar funcionais e acessíveis durante a defesa. | Ferramentas previstas na arquitetura. |
| Documentação | São exigidos os oito documentos técnicos listados na seção 6 do termo. | Os oito arquivos foram elaborados e estão em revisão. |
| MVP | A entrega do 4º semestre inclui os oito documentos e um MVP funcional. | Escopo do MVP documentado separadamente. |
| Defesa | A demonstração deve ocorrer em ambiente de nuvem; Docker Compose é a contingência em caso de falha de conectividade. | Estratégia de implantação e contingência a desenvolver. |

**Referência:** `aceite-projeto-experimental.md`, especialmente as seções 3 a 8 do repositório oficial do professor.

---

## 2. Arquitetura e divisão de microsserviços

### 2.1 Validação da divisão dos serviços

**Contexto:**

A arquitetura do MVP foi organizada em quatro microsserviços:

- **Auth Service:** autenticação, usuários, perfis e permissões.
- **Work Order Service:** clientes, veículos, ordens de serviço, diagnósticos e informações operacionais.
- **Inventory Service:** cadastro de peças, saldos, reservas e movimentações de estoque.
- **Billing Service:** orçamentos, itens de orçamento, pagamentos e situação financeira das ordens de serviço.

Notificações e relatórios foram mantidos como possibilidades de evolução pós-MVP, sem serviços independentes obrigatórios na primeira versão.

**Pergunta:**

A divisão proposta, especialmente a concentração de clientes e veículos no Work Order Service e de orçamentos e pagamentos no Billing Service, é adequada para o escopo do Projeto Experimental?

**Status:** Pendente de orientação.

### 2.2 Comunicação entre microsserviços

**Contexto:**

O grupo prevê comunicação síncrona por APIs REST para operações que precisam de resposta imediata e comunicação assíncrona por RabbitMQ para eventos entre serviços.

Também foram documentados mecanismos de confiabilidade, como Transactional Outbox, Inbox ou mecanismo equivalente, idempotência, retentativas e Dead Letter Queue (DLQ).

**Pergunta:**

A combinação de REST e RabbitMQ, acompanhada desses mecanismos de confiabilidade, está adequada ao nível de complexidade esperado para o projeto, especialmente no fluxo de reserva de estoque?

**Status:** Pendente de orientação.

---

## 3. Banco de Dados e persistência

### 3.1 Database-per-Service

**Contexto:**

O projeto utiliza PostgreSQL e adota a separação lógica dos dados por microsserviço, evitando que um serviço acesse diretamente as tabelas pertencentes a outro.

Para controlar os custos e a complexidade do MVP, está sendo considerada a utilização de uma mesma instância Amazon RDS PostgreSQL, com bancos lógicos separados para cada serviço.

**Pergunta:**

A utilização de uma única instância PostgreSQL com bancos de dados separados por microsserviço atende ao princípio Database-per-Service para a avaliação acadêmica, desde que cada serviço mantenha propriedade exclusiva sobre seu banco e não existam acessos diretos entre bancos?

**Status:** Pendente de orientação.

### 3.2 Modelagem e integridade dos dados

**Contexto:**

O MER apresenta uma visão conceitual integrada do sistema e diagramas lógicos separados por serviço.

As referências entre microsserviços são tratadas por identificadores e contratos de API/eventos, sem chaves estrangeiras físicas entre bancos de serviços diferentes.

**Pergunta:**

A apresentação do MER integrado acompanhada dos modelos lógicos separados por microsserviço é suficiente para demonstrar a modelagem de dados, ou o professor espera algum formato adicional?

**Status:** Pendente de orientação.

---

## 4. Integração com API externa

### 4.1 Escolha da API

**Contexto:**

O termo oficial exige pelo menos uma API externa relevante ao domínio do projeto.

Foram consideradas inicialmente as seguintes alternativas:

- **FIPE:** consulta de informações e valores de referência de veículos.
- **ViaCEP:** consulta de endereço a partir do CEP, auxiliando o cadastro de clientes.

A escolha ainda não foi formalizada em uma ADR e não há integração implementada ou comprovada nesta versão documental.

**Perguntas:**

1. A integração com a API FIPE ou com a ViaCEP, considerando a finalidade de cada uma no OficinaPro, atende ao requisito de relevância para o domínio?
2. Uma única API externa efetivamente integrada e demonstrável é suficiente para cumprir o requisito do termo?

**Status:** Pendente de orientação e de escolha formal pelo grupo.

---

## 5. AWS, Terraform e viabilidade de implantação

### 5.1 Disponibilidade dos serviços na AWS

**Contexto:**

A arquitetura proposta utiliza AWS, com serviços planejados como ECS Fargate, Amazon RDS PostgreSQL, Amazon MQ, S3, CloudFront, ECR e recursos de rede e segurança.

A implantação deverá ser automatizada com Terraform, conforme o termo oficial.

**Pergunta:**

Caso o ambiente acadêmico AWS disponibilizado aos alunos não permita utilizar algum dos serviços previstos — por restrição de permissões, disponibilidade ou custo —, o grupo poderá adaptar a arquitetura utilizando serviços equivalentes ou outro provedor de nuvem de grande porte?

**Status:** Pendente de orientação.

### 5.2 Demonstração e custos

**Contexto:**

O termo exige aplicação em nuvem durante a defesa e permite Docker Compose como contingência em caso de falha de conectividade. O grupo pretende dimensionar a infraestrutura para o contexto acadêmico e evitar recursos desnecessários.

**Pergunta:**

Existe alguma orientação institucional sobre limites de custo, créditos acadêmicos ou serviços AWS recomendados para a implantação do projeto?

**Status:** Pendente de orientação.

---

## 6. Testes e qualidade

### 6.1 Testes End-to-End

**Contexto:**

O termo oficial exige testes automatizados unitários ou de integração e cobertura mínima de 60% sobre o código-fonte total, considerando globalmente os microsserviços.

O grupo prevê utilizar Jest e Supertest no backend e React Testing Library no frontend.

**Pergunta:**

Além dos testes unitários e de integração exigidos pelo termo, serão cobrados testes End-to-End (E2E) que simulem a utilização completa do sistema pela interface?

**Status:** Pendente de orientação.

### 6.2 Medição da cobertura

**Contexto:**

O requisito de cobertura mínima é global, considerando todos os microsserviços.

**Pergunta:**

Há alguma ferramenta ou formato específico esperado para comprovar a cobertura global mínima de 60%, ou relatórios consolidados das ferramentas de teste utilizadas serão suficientes?

**Status:** Pendente de orientação.

---

## 7. Equipe e avaliação acadêmica

### 7.1 Distribuição de papéis e responsabilidades

**Contexto:**

O grupo possui quatro integrantes. O planejamento de sprints apresenta as frentes de produto, arquitetura/backend, frontend, DevOps e qualidade, mas a distribuição nominal das responsabilidades ainda não foi formalizada.

**Pergunta:**

O professor recomenda alguma distribuição mínima de papéis ou responsabilidades para os integrantes, considerando o porte e a arquitetura do projeto?

**Status:** Pendente de orientação e de definição pelo grupo.

### 7.2 Avaliação individual

**Contexto:**

O grupo utiliza Git e pretende organizar o desenvolvimento por branches, commits e Pull Requests, com revisão das alterações.

**Pergunta:**

Como será avaliada a contribuição individual de cada integrante, considerando commits, Pull Requests, participação nas reuniões, documentação e desenvolvimento das funcionalidades?

**Status:** Pendente de orientação.

---

## 8. Decisões de domínio consolidadas pelo grupo

Os pontos abaixo já foram incorporados à documentação funcional e não devem ser apresentados como dúvidas em aberto, salvo se o professor solicitar alguma alteração.

### 8.1 Oficinas, clientes e veículos

- [x] Cada oficina será representada por um tenant.
- [x] Os dados operacionais deverão ser isolados por tenant.
- [x] O backend deverá identificar o tenant pelo contexto autenticado, sem confiar em um `tenant_id` informado livremente pelo frontend.
- [x] CPF será único por oficina, e não globalmente entre todas as oficinas.
- [x] Placa de veículo será única por oficina.
- [x] O histórico de manutenção deverá permanecer vinculado ao veículo.

### 8.2 Perfis de acesso

Os perfis operacionais previstos para o MVP são:

- [x] Atendente.
- [x] Mecânico.
- [x] Estoque.
- [x] Financeiro.

Não está previsto um perfil genérico denominado “Administrador da Oficina”. A matriz detalhada de permissões deverá ser consolidada durante o detalhamento funcional.

### 8.3 Ordem de Serviço e orçamento

- [x] A OS terá estados operacionais próprios: `ABERTA`, `EM_DIAGNOSTICO`, `AGUARDANDO_APROVACAO`, `AGUARDANDO_RESERVA`, `EM_EXECUCAO`, `CONCLUIDA` e `CANCELADA`.
- [x] `APROVADA` e `RECUSADA` são decisões do orçamento, não estados operacionais da OS.
- [x] A aceitação do orçamento pelo cliente é diferente da aprovação operacional definitiva.
- [x] A decisão do cliente deverá registrar responsável, data, horário e resultado.
- [x] Um orçamento recusado poderá ser revisado, preservando o histórico da decisão anterior.

### 8.4 Reserva e movimentação de estoque

O fluxo definido para o projeto é:

```text
Cliente aceita o orçamento
        ↓
Billing registra a decisão do cliente
        ↓
Solicitação de reserva ao Inventory
        ↓
Inventory verifica todas as peças
        ↓
Reserva integral ou recusa integral
        ↓
Se confirmada:
aprovação operacional do orçamento
        ↓
Liberação da execução da OS
        ↓
Peça efetivamente utilizada:
conversão da reserva em saída definitiva
```

Regras consolidadas:

- [x] A reserva deve ser integral: todas as peças solicitadas são reservadas ou nenhuma é reservada.
- [x] A reserva deve ocorrer no banco de dados do Inventory Service.
- [x] O controle de concorrência deve impedir reservas acima da quantidade disponível.
- [x] Chaves de idempotência devem impedir processamento duplicado.
- [x] Estoque físico, reservado e disponível são conceitos distintos.
- [x] A reserva não representa consumo definitivo e não reduz imediatamente o estoque físico.
- [x] A saída definitiva ocorre quando a peça é efetivamente utilizada.
- [x] Peças reservadas e não utilizadas podem ter a reserva liberada.
- [x] Peças perdidas ou inutilizadas devem ser registradas como perda ou descarte, sem retorno indevido ao estoque comercializável.
- [x] Movimentações originais, estornos e perdas devem manter rastreabilidade.

### 8.5 Situação operacional e financeira

- [x] A conclusão operacional da OS é independente da quitação financeira.
- [x] O Billing Service mantém a situação financeira separada, com estados previstos como `PENDENTE`, `PARCIAL` e `QUITADA`.
- [x] O registro de pagamento não deverá alterar indevidamente o estado operacional da OS.
- [x] A gestão de assinaturas da plataforma SaaS é distinta dos pagamentos realizados pelos clientes das oficinas e poderá ser tratada em etapa posterior.

**Status:** decisões incorporadas à documentação do projeto. A ratificação formal dos registros arquiteturais deverá ser feita pelo grupo.

---

## 9. Pauta prioritária para a próxima orientação

Para aproveitar o tempo da reunião, recomenda-se priorizar:

1. Validação da divisão dos quatro microsserviços do MVP.
2. Aceitação da estratégia Database-per-Service com bancos lógicos separados na mesma instância PostgreSQL.
3. Escolha e validação da API externa (FIPE ou ViaCEP).
4. Viabilidade dos serviços AWS no ambiente acadêmico e alternativas permitidas.
5. Expectativa de testes E2E e comprovação da cobertura global de 60%.
6. Critérios de avaliação individual e distribuição das responsabilidades.

---

## 10. Registro das respostas e decisões derivadas

As respostas do professor deverão ser registradas nesta tabela. Quando uma orientação alterar o escopo, os requisitos ou a arquitetura, os documentos relacionados deverão ser atualizados.

| Data | Pergunta / Tema | Orientação recebida | Decisão adotada | Documentos afetados |
|---|---|---|---|---|
| — | — | — | — | — |

---

## 11. Histórico de alterações

| Data | Alteração | Responsável |
|---|---|---|
| 26/09/2026 | Criação da lista inicial de dúvidas. | Eloísa Brandão |
| 27/10/2026 | Consolidação da pauta acadêmica e revisão das dúvidas de domínio. | Eloísa Brandão |
| 12/11/2026 | Atualização das dúvidas de arquitetura, mensageria, perfis e estoque. | Eloísa Brandão |
| 16/11/2026 | Revisão conforme o termo oficial e os documentos atuais do OficinaPro; remoção de perguntas já respondidas e correção do fluxo de orçamento, reserva, execução e situação financeira. | Grupo GarageOps — organização documental |

---

**Observação:** este arquivo é uma pauta de orientação. Uma pergunta registrada como pendente não significa que o requisito correspondente seja opcional. As exigências expressas no termo oficial continuam obrigatórias, independentemente das respostas às questões complementares.
