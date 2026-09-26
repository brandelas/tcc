# Glossário — OficinaPro

> **Status:** 🟡 Em discussão
> **Objetivo:** padronizar os principais termos utilizados no projeto OficinaPro.
>
> Este documento serve como referência para requisitos, código, documentação, reuniões e comunicação entre os integrantes da equipe. Termos poderão ser ajustados conforme decisões do grupo e orientações do professor.

---

## 1. Cliente

Pessoa que solicita ou contrata serviços da oficina.

Um cliente pode possuir um ou mais veículos cadastrados no sistema.

---

## 2. Veículo

Automóvel pertencente ou associado a um cliente.

O cadastro poderá conter informações como:

* placa;
* marca;
* modelo;
* ano;
* quilometragem;
* informações adicionais.

Os campos obrigatórios ainda deverão ser definidos pela equipe.

---

## 3. Ordem de Serviço (OS)

Registro principal utilizado para controlar um atendimento ou serviço realizado pela oficina.

A OS relaciona informações como:

* cliente;
* veículo;
* diagnóstico;
* orçamento;
* serviços;
* peças;
* pagamento;
* status;
* histórico do atendimento.

---

## 4. Status da OS

Indica a etapa atual da ordem de serviço dentro do fluxo operacional.

### Status propostos

| Status                 | Significado                                  |
| ---------------------- | -------------------------------------------- |
| `ABERTA`               | OS criada e aguardando início do diagnóstico |
| `EM_DIAGNOSTICO`       | Veículo está em processo de diagnóstico      |
| `AGUARDANDO_APROVACAO` | Orçamento aguardando decisão do cliente      |
| `APROVADA`             | Orçamento aprovado                           |
| `RECUSADA`             | Orçamento recusado                           |
| `EM_EXECUCAO`          | Serviço sendo realizado                      |
| `AGUARDANDO_PAGAMENTO` | Serviço concluído e aguardando pagamento     |
| `CONCLUIDA`            | Atendimento finalizado                       |

> **Status:** 🟡 Proposta. As transições e estados definitivos deverão ser validados pela equipe.

---

## 5. Diagnóstico

Avaliação técnica realizada sobre o veículo para identificar problemas, necessidades de manutenção ou serviços recomendados.

O diagnóstico serve como base para a elaboração do orçamento.

---

## 6. Orçamento

Documento ou registro que apresenta os serviços e peças necessários para realizar o atendimento, juntamente com seus respectivos valores.

O orçamento deverá estar associado a uma OS.

---

## 7. Aprovação

Registro da decisão do cliente em relação ao orçamento apresentado.

Na proposta atual, o orçamento poderá ser:

* aprovado;
* recusado.

A regra para alteração ou reabertura de um orçamento recusado ainda deverá ser definida.

---

## 8. Serviço

Atividade realizada pela oficina durante a execução de uma OS.

Exemplos:

* troca de óleo;
* alinhamento;
* troca de pastilhas;
* revisão;
* manutenção de componentes.

O cadastro e o detalhamento dos serviços ainda deverão ser definidos pela equipe.

---

## 9. Peça

Componente ou item utilizado durante a realização de um serviço.

Uma peça pode estar cadastrada no estoque e ser associada a uma OS quando utilizada.

---

## 10. Estoque

Controle da quantidade disponível de peças utilizadas pela oficina.

O estoque deverá considerar, no mínimo:

* cadastro da peça;
* quantidade disponível;
* entradas;
* saídas;
* movimentações relacionadas às OS.

---

## 11. Entrada de estoque

Movimentação que aumenta a quantidade disponível de determinada peça.

Pode ocorrer, por exemplo, após a chegada de novas peças à oficina.

---

## 12. Saída de estoque

Movimentação que reduz a quantidade disponível de uma peça.

No fluxo principal, a saída poderá ocorrer quando uma peça for utilizada durante a execução de uma OS.

---

## 13. Saldo de estoque

Quantidade disponível de determinada peça após considerar suas entradas e saídas.

A regra para permitir ou impedir estoque negativo deverá ser definida pela equipe.

---

## 14. Pagamento

Registro financeiro associado a uma ordem de serviço.

A proposta inicial é registrar:

* valor;
* situação;
* data;
* forma de pagamento, caso definida.

Integrações com sistemas externos de pagamento não fazem parte da proposta inicial do MVP.

---

## 15. Histórico de manutenção

Registro dos atendimentos e serviços realizados em determinado veículo.

O histórico deverá permitir consultar informações relevantes de serviços anteriores.

---

## 16. Usuário

Pessoa que possui acesso ao sistema OficinaPro.

Um usuário poderá possuir um determinado perfil de acesso.

---

## 17. Perfil

Conjunto de permissões associado a um usuário.

Os perfis definitivos ainda deverão ser definidos pela equipe.

Exemplos possíveis:

* administrador;
* atendente;
* mecânico;
* gestor.

> **Status:** 🔵 Validar com a equipe.

---

## 18. Autenticação

Processo utilizado para verificar a identidade de um usuário antes de permitir o acesso ao sistema.

A implementação técnica poderá utilizar login e senha ou outra estratégia definida pela equipe.

---

## 19. Autorização

Processo que determina quais funcionalidades e informações um usuário autenticado pode acessar ou modificar.

A autorização deverá estar relacionada aos perfis e permissões definidos no projeto.

---

## 20. Permissão

Regra que determina uma ação que determinado usuário ou perfil pode realizar.

Exemplos:

* visualizar clientes;
* cadastrar clientes;
* editar veículos;
* criar OS;
* alterar status;
* consultar estoque;
* registrar pagamento.

---

## 21. Microserviço

Serviço independente responsável por uma parte específica do domínio da aplicação.

A arquitetura proposta inicialmente considera:

* Auth;
* Customer Vehicle;
* Work Order;
* Inventory;
* Billing;
* Notification;
* Reporting.

> **Status:** 🟡 Arquitetura proposta. A divisão definitiva deverá ser validada tecnicamente pela equipe.

---

## 22. API

Interface utilizada para permitir a comunicação entre sistemas ou componentes de software.

No OficinaPro, as APIs poderão ser utilizadas pelo frontend para acessar funcionalidades disponibilizadas pelo backend.

---

## 23. Endpoint

Endereço específico de uma API utilizado para realizar determinada operação.

Exemplos conceituais:

```text
GET    /clientes
POST   /clientes
GET    /veiculos
POST   /ordens-servico
```

Os endpoints reais serão definidos durante a implementação.

---

## 24. Evento

Registro de que algo aconteceu no sistema e que pode ser relevante para outros componentes.

Exemplos:

```text
OS_CRIADA
ORCAMENTO_APROVADO
PECA_UTILIZADA
PAGAMENTO_REGISTRADO
OS_CONCLUIDA
```

A utilização e o formato definitivo dos eventos ainda deverão ser definidos.

---

## 25. Mensageria

Mecanismo utilizado para comunicação assíncrona entre componentes ou serviços.

O projeto prevê o uso de RabbitMQ, mas a necessidade e a forma de utilização deverão ser definidas na arquitetura.

---

## 26. RabbitMQ

Sistema de mensageria utilizado para transmitir mensagens entre componentes da aplicação.

No OficinaPro, poderá ser utilizado para comunicação assíncrona entre microserviços e processamento de eventos.

---

## 27. Banco de dados

Sistema responsável pelo armazenamento persistente das informações do OficinaPro.

O projeto prevê inicialmente a utilização do PostgreSQL.

---

## 28. Migração

Arquivo ou mecanismo utilizado para controlar alterações estruturais no banco de dados.

Exemplos:

* criação de tabela;
* alteração de coluna;
* criação de índice;
* alteração de relacionamento.

As ferramentas utilizadas para migrations serão definidas durante a implementação.

---

## 29. Frontend

Parte da aplicação responsável pela interface utilizada pelos usuários.

A proposta técnica prevê:

* React;
* TypeScript;
* Vite.

---

## 30. Backend

Parte da aplicação responsável pelas regras de negócio, APIs, persistência e comunicação com outros serviços.

A proposta técnica prevê:

* Node.js;
* TypeScript;
* NestJS.

---

## 31. Teste automatizado

Teste executado por ferramentas de software para verificar se determinada funcionalidade apresenta o comportamento esperado.

O projeto prevê a utilização de ferramentas como:

* Jest;
* Supertest;
* React Testing Library.

---

## 32. Cobertura de testes

Métrica utilizada para indicar quanto do código está sendo executado pelos testes automatizados.

O README atual apresenta uma meta inicial de **60% de cobertura**.

Essa meta poderá ser revisada conforme orientação do professor e evolução do projeto.

---

## 33. CI/CD

Conjunto de práticas e automações relacionadas à integração, validação e entrega do software.

No projeto, está prevista a utilização do GitHub Actions.

---

## 34. Infraestrutura como Código (IaC)

Abordagem utilizada para definir e gerenciar infraestrutura por meio de arquivos de configuração.

O projeto prevê a utilização do Terraform.

---

## 35. Docker

Tecnologia utilizada para empacotar e executar aplicações em ambientes isolados chamados containers.

No OficinaPro, Docker poderá ser utilizado para facilitar a execução local dos serviços.

---

## 36. Docker Compose

Ferramenta utilizada para definir e executar múltiplos containers relacionados a uma aplicação.

A proposta inicial prevê serviços como:

```text
PostgreSQL
Adminer
RabbitMQ
RabbitMQ Management
```

---

## 37. Adminer

Interface web utilizada para visualizar e administrar bancos de dados.

No ambiente local do OficinaPro, poderá ser utilizado para acessar o PostgreSQL.

---

## 38. Observabilidade

Conjunto de práticas e ferramentas utilizadas para acompanhar o comportamento da aplicação.

A proposta técnica menciona:

* logs;
* métricas;
* Prometheus;
* Grafana.

A implementação efetiva deverá ser definida pela equipe.

---

## 39. MVP

**Minimum Viable Product — Produto Mínimo Viável.**

Versão inicial do sistema que contém o conjunto mínimo de funcionalidades necessário para validar o fluxo principal do projeto.

No OficinaPro, a proposta de MVP está documentada em:

```text
docs/requisitos/mvp.md
```

---

## 40. Requisito funcional

Define algo que o sistema deve ser capaz de realizar.

Exemplo:

> O sistema deve permitir cadastrar um cliente.

---

## 41. Requisito não funcional

Define uma característica, restrição ou condição relacionada ao funcionamento do sistema.

Exemplos:

* segurança;
* desempenho;
* disponibilidade;
* usabilidade;
* manutenibilidade;
* observabilidade.

---

## 42. História de usuário

Descrição de uma necessidade do ponto de vista de quem utiliza o sistema.

Formato adotado:

> Como [tipo de usuário], quero [ação], para [benefício].

As histórias de usuário estão documentadas em:

```text
docs/requisitos/historias-usuario.md
```

---

## 43. Critério de aceite

Condição utilizada para determinar se uma funcionalidade atende ao comportamento esperado.

Os critérios de aceite estão documentados em:

```text
docs/requisitos/criterios-aceite.md
```

---

## 44. Regra de negócio

Regra que representa uma condição ou comportamento específico do domínio da oficina.

Exemplo:

> Uma peça utilizada em uma OS deve gerar uma movimentação de saída de estoque.

As regras de negócio estão documentadas em:

```text
docs/requisitos/regras-negocio.md
```

---

## 45. Product Owner (PO)

Papel responsável por representar e organizar as necessidades do produto, priorizar o backlog e facilitar a comunicação entre necessidades do negócio e equipe de desenvolvimento.

No contexto deste projeto, o papel de Product Owner é exercido pela integrante responsável pela análise e organização dos requisitos.

---

##
