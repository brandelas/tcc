\# Backlog de Requisitos — OficinaPro



> \*\*Documento de trabalho da análise de requisitos do OficinaPro.\*\*

>

> Este documento é evolutivo e deve ser atualizado conforme o projeto avança, novas decisões são tomadas pelo grupo e pontos relevantes são validados com o professor orientador.



\---



\## 1. Objetivo



O \*\*OficinaPro\*\* é um sistema de gestão operacional para oficinas mecânicas de pequeno e médio porte.



O sistema tem como objetivo centralizar o fluxo operacional da oficina, desde o cadastro do cliente e veículo até a abertura e conclusão da ordem de serviço, controle de peças, fechamento financeiro e histórico de manutenção.



Este documento tem como finalidade registrar:



\* requisitos funcionais e não funcionais;

\* regras de negócio;

\* decisões de escopo;

\* pontos ainda em discussão;

\* dúvidas para validação com o professor;

\* funcionalidades planejadas para versões futuras.



\---



\## 2. Legenda de Status



| Status                       | Significado                                               |

| ---------------------------- | --------------------------------------------------------- |

| 🟢 \*\*Definido\*\*              | Decisão já estabelecida pelo grupo                        |

| 🟡 \*\*Em discussão\*\*          | Ponto que precisa ser discutido pelo grupo                |

| 🔴 \*\*Pendente\*\*              | Informação ou decisão ainda não definida                  |

| 🔵 \*\*Validar com professor\*\* | Ponto que deve ser discutido durante orientação           |

| ⚪ \*\*Pós-MVP\*\*                | Funcionalidade planejada, mas que não deve bloquear o MVP |



\---



\# 3. Escopo Inicial do MVP



O fluxo principal definido atualmente é:



```text

Cliente

&#x20;  ↓

Veículo

&#x20;  ↓

Ordem de Serviço

&#x20;  ↓

Diagnóstico

&#x20;  ↓

Orçamento

&#x20;  ↓

Aprovação / Recusa

&#x20;  ↓

Execução do serviço

&#x20;  ↓

Controle de peças / Estoque

&#x20;  ↓

Conclusão da OS

&#x20;  ↓

Pagamento

&#x20;  ↓

Histórico do veículo

```



\*\*Status:\*\* 🟢 Definido no planejamento inicial do projeto.



O MVP deverá priorizar a implementação funcional desse fluxo antes da expansão para funcionalidades complementares.



\---



\# 4. Gestão de Clientes



\## 4.1 Dados previstos



\* \[ ] Nome completo

\* \[ ] CPF

\* \[ ] Telefone

\* \[ ] E-mail

\* \[ ] Endereço



\## 4.2 Requisitos a definir



\* \[ ] O CPF será obrigatório?

\* \[ ] O CPF deverá ser único?

\* \[ ] O telefone será obrigatório?

\* \[ ] O e-mail será obrigatório?

\* \[ ] O sistema permitirá exclusão de clientes?

\* \[ ] Clientes com histórico deverão ser apenas inativados?

\* \[ ] Quais informações poderão ser alteradas após o cadastro?



\## 4.3 Regras de negócio a definir



\* \[ ] Um cliente poderá possuir vários veículos.

\* \[ ] Um cliente não deverá possuir cadastros duplicados.

\* \[ ] O histórico relacionado ao cliente deverá ser preservado.



\---



\# 5. Gestão de Veículos



\## 5.1 Dados previstos



\* \[ ] Placa

\* \[ ] Marca

\* \[ ] Modelo

\* \[ ] Ano

\* \[ ] Cor

\* \[ ] Quilometragem



\## 5.2 Requisitos a definir



\* \[ ] A placa será obrigatória?

\* \[ ] A placa deverá ser única?

\* \[ ] O cliente poderá possuir vários veículos?

\* \[ ] Um veículo poderá mudar de proprietário?

\* \[ ] Como o sistema deverá preservar o histórico após uma mudança de proprietário?

\* \[ ] Quais dados poderão ser alterados após o cadastro?



\## 5.3 Regras de negócio



\* \[ ] Um veículo deverá estar associado a um cliente.

\* \[ ] O histórico de manutenção deverá permanecer associado ao veículo.



\---



\# 6. Ordem de Serviço



\## 6.1 Informações previstas



\* \[ ] Número da ordem de serviço

\* \[ ] Cliente

\* \[ ] Veículo

\* \[ ] Data de abertura

\* \[ ] Quilometragem

\* \[ ] Problema relatado pelo cliente

\* \[ ] Diagnóstico

\* \[ ] Serviços

\* \[ ] Peças

\* \[ ] Orçamento

\* \[ ] Status



\## 6.2 Fluxo de status proposto



```text

ABERTA

&#x20;  ↓

EM\_DIAGNOSTICO

&#x20;  ↓

AGUARDANDO\_APROVACAO

&#x20;  ↓

APROVADA

&#x20;  ↓

EM\_EXECUCAO

&#x20;  ↓

AGUARDANDO\_PAGAMENTO

&#x20;  ↓

CONCLUIDA

```



Fluxo alternativo:



```text

AGUARDANDO\_APROVACAO

&#x20;  ↓

RECUSADA

```



\*\*Status:\*\* 🟡 Em discussão.



\## 6.3 Requisitos a definir



\* \[ ] Quem poderá abrir uma ordem de serviço?

\* \[ ] Quem poderá editar uma OS?

\* \[ ] Quais campos poderão ser alterados após a abertura?

\* \[ ] Uma OS poderá ser cancelada?

\* \[ ] O que acontecerá quando o orçamento for recusado?

\* \[ ] Uma OS recusada poderá receber um novo orçamento?

\* \[ ] Uma OS concluída poderá ser alterada?

\* \[ ] Quais perfis poderão visualizar uma OS?



\---



\# 7. Diagnóstico



\## 7.1 Funcionalidades previstas



\* \[ ] Registrar problema relatado pelo cliente

\* \[ ] Registrar diagnóstico realizado pelo mecânico

\* \[ ] Registrar observações técnicas

\* \[ ] Associar diagnóstico à OS



\## 7.2 Requisitos a definir



\* \[ ] Quem poderá registrar o diagnóstico?

\* \[ ] O diagnóstico poderá ser editado?

\* \[ ] Será necessário registrar data e responsável pelo diagnóstico?

\* \[ ] O diagnóstico deverá ficar disponível no histórico do veículo?



\---



\# 8. Orçamento



\## 8.1 Composição prevista



O orçamento deverá permitir a composição do valor da OS por meio de:



```text

Serviços

\+

Peças

\-

Descontos

=

Valor total

```



\## 8.2 Requisitos a definir



\* \[ ] Quem poderá criar o orçamento?

\* \[ ] Quem poderá alterá-lo?

\* \[ ] Quem poderá registrar a aprovação?

\* \[ ] O cliente terá acesso direto ao orçamento?

\* \[ ] A aprovação será registrada pelo atendente?

\* \[ ] O orçamento poderá ser alterado após aprovação?

\* \[ ] Alterações após aprovação exigirão nova aprovação?

\* \[ ] Será necessário registrar data e responsável pela aprovação?



\## 8.3 Regras de negócio



\* \[ ] O serviço deverá depender da aprovação do orçamento, conforme fluxo definido pelo grupo.

\* \[ ] O valor total deverá ser calculado automaticamente pelo sistema.



\---



\# 9. Estoque



\## 9.1 Funcionalidades previstas



\* \[ ] Cadastro de peças

\* \[ ] Consulta de estoque

\* \[ ] Entrada de peças

\* \[ ] Saída de peças

\* \[ ] Registro de movimentações

\* \[ ] Definição de estoque mínimo

\* \[ ] Alerta de reposição



\## 9.2 Regra já prevista



> O sistema não deverá permitir a utilização ou baixa de quantidade superior ao estoque disponível.



\## 9.3 Requisitos a definir



\* \[ ] Em qual momento a peça será retirada do estoque?

\* \[ ] Na aprovação do orçamento?

\* \[ ] Quando o mecânico registrar a utilização?

\* \[ ] O sistema terá reserva de peças?

\* \[ ] Uma peça poderá ser devolvida ao estoque?

\* \[ ] Será necessário registrar o responsável pela movimentação?

\* \[ ] O sistema deverá registrar histórico de movimentações?



\---



\# 10. Financeiro



\## 10.1 Funcionalidades previstas



\* \[ ] Fechamento financeiro da OS

\* \[ ] Cálculo do valor total

\* \[ ] Aplicação de desconto

\* \[ ] Registro de pagamento

\* \[ ] Forma de pagamento

\* \[ ] Status financeiro



\## 10.2 Requisitos a definir



\* \[ ] O sistema permitirá pagamento parcial?

\* \[ ] Quais formas de pagamento serão aceitas?

\* \[ ] Será possível registrar mais de um pagamento na mesma OS?

\* \[ ] Quem poderá registrar pagamentos?

\* \[ ] Será possível alterar um pagamento já registrado?

\* \[ ] Será necessário registrar comprovante?

\* \[ ] A OS poderá ser concluída antes do pagamento integral?



\---



\# 11. Histórico de Manutenção



\## 11.1 Informações previstas



O histórico do veículo deverá permitir consultar informações de serviços realizados anteriormente.



Dados previstos:



\* \[ ] Data do serviço

\* \[ ] Ordem de serviço

\* \[ ] Diagnóstico

\* \[ ] Serviços realizados

\* \[ ] Peças utilizadas

\* \[ ] Valor da OS

\* \[ ] Informações de pagamento



\## 11.2 Requisitos a definir



\* \[ ] O histórico será gerado automaticamente após a conclusão da OS?

\* \[ ] Quem poderá consultar o histórico?

\* \[ ] O histórico poderá ser alterado?

\* \[ ] Quais informações deverão ser exibidas na consulta?



\---



\# 12. Usuários, Autenticação e Permissões



O README prevê um serviço de autenticação responsável por usuários, perfis e permissões.



\## 12.1 Perfis inicialmente considerados



\* \[ ] Administrador

\* \[ ] Atendente

\* \[ ] Mecânico

\* \[ ] Estoquista

\* \[ ] Financeiro



\*\*Status:\*\* 🟡 Em discussão.



\## 12.2 Requisitos a definir



\* \[ ] Quais ações cada perfil poderá executar?

\* \[ ] Um usuário poderá possuir mais de um perfil?

\* \[ ] Quem poderá cadastrar usuários?

\* \[ ] Quem poderá alterar permissões?

\* \[ ] Haverá perfil específico para cliente no MVP?

\* \[ ] Quais informações serão necessárias no cadastro de usuário?



\---



\# 13. Requisitos Não Funcionais



Os seguintes requisitos deverão ser considerados durante o desenvolvimento.



\## 13.1 Segurança



\* \[ ] Senhas armazenadas utilizando hash seguro.

\* \[ ] Controle de acesso por perfil.

\* \[ ] Validação das entradas fornecidas pelos usuários.

\* \[ ] Não exposição de credenciais e tokens.

\* \[ ] Variáveis sensíveis armazenadas em ambiente apropriado.



\## 13.2 Qualidade



\* \[ ] Código revisado por Pull Request.

\* \[ ] Testes automatizados.

\* \[ ] Banco versionado por migrations.

\* \[ ] Pipeline de CI.

\* \[ ] Padronização de código.

\* \[ ] Documentação atualizada.



\## 13.3 Observabilidade



\* \[ ] Logs dos serviços.

\* \[ ] Métricas operacionais.

\* \[ ] Monitoramento da saúde dos serviços.

\* \[ ] Monitoramento de filas.



Ferramentas previstas no planejamento:



\* Prometheus

\* Grafana



\*\*Status:\*\* ⚪ Evolução do projeto.



\---



\# 14. Regras de Negócio



As regras abaixo foram identificadas inicialmente a partir do fluxo e do planejamento do sistema.



| ID     | Regra                                                                            | Status |

| ------ | -------------------------------------------------------------------------------- | ------ |

| RN-001 | Uma ordem de serviço deve estar vinculada a um cliente e a um veículo.           | 🟢     |

| RN-002 | Uma peça utilizada deve existir no estoque.                                      | 🟢     |

| RN-003 | O sistema não deve permitir baixa superior à quantidade disponível em estoque.   | 🟢     |

| RN-004 | O pagamento deve estar associado a uma ordem de serviço.                         | 🟢     |

| RN-005 | O histórico de manutenção deve estar associado ao veículo.                       | 🟢     |

| RN-006 | O fechamento financeiro deve considerar serviços, peças, descontos e pagamentos. | 🟢     |

| RN-007 | O fluxo de execução deverá considerar a aprovação do orçamento.                  | 🟡     |

| RN-008 | O comportamento de uma OS após recusa do orçamento deverá ser definido.          | 🔴     |



> As regras marcadas como 🟡 ou 🔴 devem ser refinadas antes da implementação definitiva.



\---



\# 15. Funcionalidades Fora do Escopo Inicial do MVP



As funcionalidades abaixo fazem parte do planejamento de evolução do projeto, mas não devem bloquear a implementação do fluxo principal.



\* \[ ] Sistema avançado de notificações

\* \[ ] Relatórios gerenciais avançados

\* \[ ] Dashboards operacionais

\* \[ ] Portal completo do cliente

\* \[ ] Integrações externas

\* \[ ] Integrações reais de pagamento

\* \[ ] Monitoramento avançado

\* \[ ] Expansão da arquitetura para todos os microsserviços planejados

\* \[ ] Recursos adicionais definidos durante a evolução do projeto



\*\*Status:\*\* ⚪ Pós-MVP.



\---



\# 16. Arquitetura e Decisões Técnicas a Validar



Algumas decisões técnicas já aparecem no planejamento do projeto, mas deverão ser acompanhadas durante a evolução do MVP.



\* \[ ] PostgreSQL como banco principal

\* \[ ] Docker Compose para ambiente local

\* \[ ] RabbitMQ para mensageria

\* \[ ] Arquitetura baseada em microsserviços

\* \[ ] Backend com Node.js, TypeScript e NestJS

\* \[ ] Frontend com React, TypeScript e Vite

\* \[ ] Testes automatizados

\* \[ ] GitHub Actions para CI/CD

\* \[ ] Terraform para infraestrutura

\* \[ ] AWS para ambiente de cloud



\### Pontos para validação com o professor



\* \[ ] O nível de microsserviços esperado no MVP.

\* \[ ] Necessidade de RabbitMQ no MVP.

\* \[ ] Infraestrutura AWS necessária para a avaliação.

\* \[ ] Critérios técnicos obrigatórios.

\* \[ ] Tecnologias obrigatórias ou apenas recomendadas.

\* \[ ] Entregas técnicas esperadas.

\* \[ ] Forma de apresentação do sistema.



\---



\# 17. Dúvidas para os Encontros com o Professor



Esta seção deverá ser atualizada antes de cada encontro de orientação.



\## Encontro 01



\* \[ ] Qual deve ser o limite definitivo do MVP?

\* \[ ] Quais funcionalidades são obrigatórias para a primeira entrega?

\* \[ ] A arquitetura de microsserviços deverá estar implementada no MVP?

\* \[ ] Quais partes da arquitetura podem ser evoluídas posteriormente?

\* \[ ] Quais documentos acadêmicos precisam acompanhar o desenvolvimento?

\* \[ ] Existem requisitos técnicos específicos para a avaliação?



\## Encontro 02



\* \[ ] A definir.



\## Encontro 03



\* \[ ] A definir.



\---



\# 18. Backlog de Decisões



| ID      | Decisão                                              | Status | Responsável | Observação |

| ------- | ---------------------------------------------------- | ------ | ----------- | ---------- |

| DEC-001 | Definir obrigatoriedade do CPF                       | 🔴     | Grupo       | —          |

| DEC-002 | Definir política de exclusão/inativação de clientes  | 🔴     | Grupo       | —          |

| DEC-003 | Definir mudança de proprietário do veículo           | 🔴     | Grupo       | —          |

| DEC-004 | Definir comportamento da OS após recusa do orçamento | 🔴     | Grupo       | —          |

| DEC-005 | Definir responsável pela aprovação do orçamento      | 🔴     | Grupo       | —          |

| DEC-006 | Definir momento da baixa de estoque                  | 🔴     | Grupo       | —          |

| DEC-007 | Definir possibilidade de pagamento parcial           | 🔴     | Grupo       | —          |

| DEC-008 | Definir perfis e permissões do MVP                   | 🟡     | Grupo       | —          |

| DEC-009 | Validar escopo do MVP com o professor                | 🔵     | Grupo       | —          |

| DEC-010 | Validar nível de microsserviços esperado             | 🔵     | Grupo       | —          |



\---



\# 19. Histórico de Decisões



As decisões importantes tomadas durante o desenvolvimento devem ser registradas nesta seção.



| Data | ID | Decisão | Motivo | Participantes/Validação |

| ---- | -- | ------- | ------ | ----------------------- |

| —    | —  | —       | —      | —                       |



\---



\# 20. Histórico de Alterações deste Documento



| Data       | Alteração                                | Responsável |

| ---------- | ---------------------------------------- | ----------- |

| 26/09/2026 | Criação inicial do backlog de requisitos | Heloisa     |



\---



\## Observação



Este documento não representa sozinho a documentação acadêmica oficial do TCC.



Seu objetivo é servir como \*\*documento vivo de trabalho do desenvolvimento\*\*, permitindo que o grupo acompanhe as decisões, pendências e evolução dos requisitos do OficinaPro.



Decisões que alterem significativamente o escopo, arquitetura ou funcionamento do sistema devem ser discutidas pelo grupo e, quando necessário, validadas com o professor orientador.



