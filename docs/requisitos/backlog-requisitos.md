# Backlog de Requisitos — OficinaPro

> **Documento de trabalho da análise de requisitos do OficinaPro.**
>
> Este documento é evolutivo e deve ser atualizado à medida que o projeto avança, novas decisões são tomadas pelo grupo e pontos relevantes são validados com o professor orientador.

---

## 1. Objetivo

O **OficinaPro** é um sistema de gestão operacional para oficinas mecânicas de pequeno e médio porte.

Seu objetivo central é organizar o fluxo operacional da oficina, desde o cadastro de clientes e veículos até a abertura e a conclusão da ordem de serviço (OS), incluindo o controle de peças, o fechamento financeiro e o histórico de manutenção.

Este documento registra:

- requisitos funcionais e não funcionais;
- regras de negócio;
- decisões de escopo;
- pontos em discussão;
- dúvidas para validação com o professor;
- funcionalidades planejadas para versões futuras.

---

## 2. Legenda de status

| Status | Significado |
| --- | --- |
| 🟢 **Definido** | Decisão estabelecida pelo grupo. |
| 🟡 **Em discussão** | Ponto que precisa ser discutido pelo grupo. |
| 🔴 **Pendente** | Informação ou decisão ainda não definida. |
| 🔵 **Validar com o professor** | Ponto a ser discutido durante a orientação. |
| ⚪ **Pós-MVP** | Funcionalidade planejada que não deve bloquear o MVP. |

---

## 3. Escopo inicial do MVP

O fluxo principal definido atualmente é:

```text
Cliente
  ↓
Veículo
  ↓
Ordem de serviço
  ↓
Diagnóstico
  ↓
Orçamento
  ↓
Aprovação ou recusa
  ↓
Execução do serviço
  ↓
Controle de peças e estoque
  ↓
Conclusão da OS
  ↓
Pagamento
  ↓
Histórico do veículo
```

**Status:** 🟢 Definido no planejamento inicial do projeto.

O MVP deverá priorizar a implementação funcional desse fluxo antes da expansão para funcionalidades complementares.

---

## 4. Gestão de clientes

### 4.1 Dados previstos

- [ ] Nome completo
- [ ] CPF
- [ ] Telefone
- [ ] E-mail
- [ ] Endereço

### 4.2 Requisitos a definir

- [ ] O CPF será obrigatório?
- [ ] O CPF deverá ser único?
- [ ] O telefone será obrigatório?
- [ ] O e-mail será obrigatório?
- [ ] O sistema permitirá a exclusão de clientes?
- [ ] Clientes com histórico deverão ser apenas inativados?
- [ ] Quais informações poderão ser alteradas após o cadastro?

### 4.3 Regras de negócio a definir

- [ ] Um cliente poderá possuir vários veículos.
- [ ] O sistema deverá evitar cadastros duplicados de clientes.
- [ ] O histórico relacionado ao cliente deverá ser preservado.

---

## 5. Gestão de veículos

### 5.1 Dados previstos

- [ ] Placa
- [ ] Marca
- [ ] Modelo
- [ ] Ano
- [ ] Cor
- [ ] Quilometragem

### 5.2 Requisitos a definir

- [ ] A placa será obrigatória?
- [ ] A placa deverá ser única?
- [ ] O cliente poderá possuir vários veículos?
- [ ] Um veículo poderá mudar de proprietário?
- [ ] Como o sistema deverá preservar o histórico após uma mudança de proprietário?
- [ ] Quais dados poderão ser alterados após o cadastro?

### 5.3 Regras de negócio

- [ ] Um veículo deverá estar associado a um cliente.
- [ ] O histórico de manutenção deverá permanecer associado ao veículo.

---

## 6. Ordem de serviço

### 6.1 Informações previstas

- [ ] Número da ordem de serviço
- [ ] Cliente
- [ ] Veículo
- [ ] Data de abertura
- [ ] Quilometragem
- [ ] Problema relatado pelo cliente
- [ ] Diagnóstico
- [ ] Serviços
- [ ] Peças
- [ ] Orçamento
- [ ] Status

### 6.2 Fluxo de status proposto

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

**Status:** 🟡 Em discussão.

### 6.3 Requisitos a definir

- [ ] Quem poderá abrir uma ordem de serviço?
- [ ] Quem poderá editar uma OS?
- [ ] Quais campos poderão ser alterados após a abertura?
- [ ] Uma OS poderá ser cancelada?
- [ ] O que acontecerá quando o orçamento for recusado?
- [ ] Uma OS recusada poderá receber um novo orçamento?
- [ ] Uma OS concluída poderá ser alterada?
- [ ] Quais perfis poderão visualizar uma OS?

---

## 7. Diagnóstico

### 7.1 Funcionalidades previstas

- [ ] Registrar o problema relatado pelo cliente.
- [ ] Registrar o diagnóstico realizado pelo mecânico.
- [ ] Registrar observações técnicas.
- [ ] Associar o diagnóstico à OS.

### 7.2 Requisitos a definir

- [ ] Quem poderá registrar o diagnóstico?
- [ ] O diagnóstico poderá ser editado?
- [ ] Será necessário registrar a data e o responsável pelo diagnóstico?
- [ ] O diagnóstico deverá ficar disponível no histórico do veículo?

---

## 8. Orçamento

### 8.1 Composição prevista

O orçamento deverá permitir a composição do valor da OS por meio de serviços, peças e descontos:

```text
Serviços + Peças - Descontos = Valor total
```

### 8.2 Requisitos a definir

- [ ] Quem poderá criar o orçamento?
- [ ] Quem poderá alterá-lo?
- [ ] Quem poderá registrar a aprovação?
- [ ] O cliente terá acesso direto ao orçamento?
- [ ] A aprovação será registrada pelo atendente?
- [ ] O orçamento poderá ser alterado após a aprovação?
- [ ] Alterações após a aprovação exigirão uma nova aprovação?
- [ ] Será necessário registrar a data e o responsável pela aprovação?

### 8.3 Regras de negócio

- [ ] A execução do serviço deverá depender da aprovação do orçamento, conforme o fluxo definido pelo grupo.
- [ ] O valor total deverá ser calculado automaticamente pelo sistema.

---

## 9. Estoque

### 9.1 Funcionalidades previstas

- [ ] Cadastro de peças
- [ ] Consulta de estoque
- [ ] Entrada de peças
- [ ] Saída de peças
- [ ] Registro de movimentações
- [ ] Definição de estoque mínimo
- [ ] Alerta de reposição

### 9.2 Regra já prevista

> O sistema não deverá permitir a utilização ou a baixa de quantidade superior ao estoque disponível.

### 9.3 Requisitos a definir

- [ ] Em qual momento a peça será retirada do estoque?
- [ ] A baixa ocorrerá na aprovação do orçamento?
- [ ] A baixa ocorrerá quando o mecânico registrar a utilização?
- [ ] O sistema terá reserva de peças?
- [ ] Uma peça poderá ser devolvida ao estoque?
- [ ] Será necessário registrar o responsável pela movimentação?
- [ ] O sistema deverá manter o histórico de movimentações?

---

## 10. Financeiro

### 10.1 Funcionalidades previstas

- [ ] Fechamento financeiro da OS
- [ ] Cálculo do valor total
- [ ] Aplicação de desconto
- [ ] Registro de pagamento
- [ ] Forma de pagamento
- [ ] Status financeiro

### 10.2 Requisitos a definir

- [ ] O sistema permitirá pagamento parcial?
- [ ] Quais formas de pagamento serão aceitas?
- [ ] Será possível registrar mais de um pagamento na mesma OS?
- [ ] Quem poderá registrar pagamentos?
- [ ] Será possível alterar um pagamento já registrado?
- [ ] Será necessário registrar um comprovante?
- [ ] A OS poderá ser concluída antes do pagamento integral?

---

## 11. Histórico de manutenção

### 11.1 Informações previstas

O histórico do veículo deverá permitir a consulta dos serviços realizados anteriormente.

Dados previstos:

- [ ] Data do serviço
- [ ] Ordem de serviço
- [ ] Diagnóstico
- [ ] Serviços realizados
- [ ] Peças utilizadas
- [ ] Valor da OS
- [ ] Informações de pagamento

### 11.2 Requisitos a definir

- [ ] O histórico será gerado automaticamente após a conclusão da OS?
- [ ] Quem poderá consultar o histórico?
- [ ] O histórico poderá ser alterado?
- [ ] Quais informações deverão ser exibidas na consulta?

---

## 12. Usuários, autenticação e permissões

O [README.md](../../README.md) prevê um serviço de autenticação responsável por usuários, perfis e permissões.

### 12.1 Perfis inicialmente considerados

- [ ] Administrador
- [ ] Atendente
- [ ] Mecânico
- [ ] Estoquista
- [ ] Financeiro

**Status:** 🟡 Em discussão.

### 12.2 Requisitos a definir

- [ ] Quais ações cada perfil poderá executar?
- [ ] Um usuário poderá possuir mais de um perfil?
- [ ] Quem poderá cadastrar usuários?
- [ ] Quem poderá alterar permissões?
- [ ] Haverá perfil específico para cliente no MVP?
- [ ] Quais informações serão necessárias no cadastro de usuário?

---

## 13. Requisitos não funcionais

Os seguintes requisitos deverão ser considerados durante o desenvolvimento.

### 13.1 Segurança

- [ ] Armazenar senhas utilizando hash seguro.
- [ ] Controlar o acesso por perfil.
- [ ] Validar as entradas fornecidas pelos usuários.
- [ ] Evitar a exposição de credenciais e tokens.
- [ ] Armazenar variáveis sensíveis em ambiente apropriado.

### 13.2 Qualidade

- [ ] Revisar o código por meio de Pull Requests.
- [ ] Implementar testes automatizados.
- [ ] Versionar o banco de dados por meio de migrations.
- [ ] Configurar pipeline de CI.
- [ ] Padronizar o código.
- [ ] Manter a documentação atualizada.

### 13.3 Observabilidade

- [ ] Registrar logs dos serviços.
- [ ] Coletar métricas operacionais.
- [ ] Monitorar a saúde dos serviços.
- [ ] Monitorar filas.

Ferramentas previstas no planejamento:

- Prometheus
- Grafana

**Status:** ⚪ Evolução do projeto.

---

## 14. Regras de negócio

As regras abaixo foram identificadas inicialmente a partir do fluxo e do planejamento do sistema.

| ID | Regra | Status |
| --- | --- | :---: |
| RN-001 | Uma ordem de serviço deve estar vinculada a um cliente e a um veículo. | 🟢 |
| RN-002 | Uma peça utilizada deve existir no estoque. | 🟢 |
| RN-003 | O sistema não deve permitir baixa superior à quantidade disponível em estoque. | 🟢 |
| RN-004 | O pagamento deve estar associado a uma ordem de serviço. | 🟢 |
| RN-005 | O histórico de manutenção deve estar associado ao veículo. | 🟢 |
| RN-006 | O fechamento financeiro deve considerar serviços, peças, descontos e pagamentos. | 🟢 |
| RN-007 | O fluxo de execução deverá considerar a aprovação do orçamento. | 🟡 |
| RN-008 | O comportamento de uma OS após a recusa do orçamento deverá ser definido. | 🔴 |

> As regras marcadas como 🟡 ou 🔴 devem ser refinadas antes da implementação definitiva.

---

## 15. Funcionalidades fora do escopo inicial do MVP

As funcionalidades abaixo fazem parte do planejamento de evolução do projeto, mas não devem bloquear a implementação do fluxo principal.

- [ ] Sistema avançado de notificações
- [ ] Relatórios gerenciais avançados
- [ ] Dashboards operacionais
- [ ] Portal completo do cliente
- [ ] Integrações externas
- [ ] Integrações reais de pagamento
- [ ] Monitoramento avançado
- [ ] Expansão da arquitetura para todos os microsserviços planejados
- [ ] Recursos adicionais definidos durante a evolução do projeto

**Status:** ⚪ Pós-MVP.

---

## 16. Arquitetura e decisões técnicas a validar

Algumas decisões técnicas já aparecem no planejamento do projeto, mas deverão ser acompanhadas durante a evolução do MVP.

- [ ] PostgreSQL como banco principal
- [ ] Docker Compose para o ambiente local
- [ ] RabbitMQ para mensageria
- [ ] Arquitetura baseada em microsserviços
- [ ] Backend com Node.js, TypeScript e NestJS
- [ ] Frontend com React, TypeScript e Vite
- [ ] Testes automatizados
- [ ] GitHub Actions para CI/CD
- [ ] Terraform para infraestrutura
- [ ] AWS para ambiente em nuvem

### Pontos para validação com o professor

- [ ] Qual é o nível de microsserviços esperado no MVP?
- [ ] RabbitMQ é necessário no MVP?
- [ ] Qual infraestrutura AWS será necessária para a avaliação?
- [ ] Quais são os critérios técnicos obrigatórios?
- [ ] As tecnologias são obrigatórias ou apenas recomendadas?
- [ ] Quais entregas técnicas são esperadas?
- [ ] Como o sistema deverá ser apresentado?

---

## 17. Dúvidas para os encontros com o professor

Esta seção deverá ser atualizada antes de cada encontro de orientação.

### Encontro 01

- [ ] Qual deve ser o limite definitivo do MVP?
- [ ] Quais funcionalidades são obrigatórias para a primeira entrega?
- [ ] A arquitetura de microsserviços deverá estar implementada no MVP?
- [ ] Quais partes da arquitetura poderão ser evoluídas posteriormente?
- [ ] Quais documentos acadêmicos deverão acompanhar o desenvolvimento?
- [ ] Existem requisitos técnicos específicos para a avaliação?

### Encontro 02

- [ ] A definir.

### Encontro 03

- [ ] A definir.

---

## 18. Backlog de decisões

| ID | Decisão | Status | Responsável | Observação |
| --- | --- | :---: | --- | --- |
| DEC-001 | Definir a obrigatoriedade do CPF. | 🔴 | Grupo | — |
| DEC-002 | Definir a política de exclusão ou inativação de clientes. | 🔴 | Grupo | — |
| DEC-003 | Definir a possibilidade de mudança de proprietário do veículo. | 🔴 | Grupo | — |
| DEC-004 | Definir o comportamento da OS após a recusa do orçamento. | 🔴 | Grupo | — |
| DEC-005 | Definir o responsável pela aprovação do orçamento. | 🔴 | Grupo | — |
| DEC-006 | Definir o momento da baixa de estoque. | 🔴 | Grupo | — |
| DEC-007 | Definir a possibilidade de pagamento parcial. | 🔴 | Grupo | — |
| DEC-008 | Definir os perfis e as permissões do MVP. | 🟡 | Grupo | — |
| DEC-009 | Validar o escopo do MVP com o professor. | 🔵 | Grupo | — |
| DEC-010 | Validar o nível de microsserviços esperado. | 🔵 | Grupo | — |

---

## 19. Histórico de decisões

As decisões importantes tomadas durante o desenvolvimento deverão ser registradas nesta seção.

| Data | ID | Decisão | Motivo | Participantes/validação |
| --- | --- | --- | --- | --- |
| — | — | — | — | — |

---

## 20. Histórico de alterações deste documento

| Data | Alteração | Responsável |
| --- | --- | --- |
| 26/09/2026 | Criação inicial do backlog de requisitos. | Heloisa |

---

## Observação

Este documento não substitui, por si só, a documentação acadêmica oficial do TCC.

Seu objetivo é servir como **documento vivo de trabalho do desenvolvimento**, permitindo que o grupo acompanhe as decisões, as pendências e a evolução dos requisitos do OficinaPro.

Decisões que alterem significativamente o escopo, a arquitetura ou o funcionamento do sistema deverão ser discutidas pelo grupo e, quando necessário, validadas com o professor orientador.
