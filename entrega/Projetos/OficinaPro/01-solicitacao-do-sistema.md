# Documento de Solicitação do Sistema

---

**Projeto:** OficinaPro
**Grupo:** GarageOps  
**Data:** 25/11/2026  
**Versão:** 1.6

---

## 1. Visão Geral do Projeto

O **OficinaPro** é uma plataforma **SaaS multi-tenant** para gestão operacional de oficinas mecânicas de pequeno e médio porte. A proposta é permitir que várias oficinas utilizem a mesma aplicação em nuvem, cada uma com seus próprios dados, usuários, clientes, veículos, ordens de serviço, pagamentos e indicadores. O MVP não terá controle interno de estoque; peças poderão ser incluídas manualmente como itens dos orçamentos, considerando a parceria de muitas oficinas com autopeças.

No modelo SaaS, a oficina não precisa instalar o sistema localmente nem manter infraestrutura própria. Ela acessa a plataforma pela web, cadastra sua operação e passa a utilizar o sistema como serviço. Internamente, cada oficina será tratada como um **tenant**, ou seja, uma unidade lógica de isolamento dentro da plataforma.

O sistema cobre o processo de negócio formado por cadastro da oficina, cadastro de usuários, cadastro de clientes, cadastro de veículos, abertura de ordem de serviço, diagnóstico técnico, elaboração de orçamento, aprovação ou recusa do orçamento, execução do serviço, fechamento financeiro, registro de pagamento e histórico de manutenção do veículo. Peças e seus valores são informados manualmente no orçamento, sem catálogo, saldo ou movimentação de estoque.

O público-alvo são oficinas independentes, auto centers e pequenos centros automotivos que precisam melhorar controle, rastreabilidade e organização operacional, mas que normalmente não possuem estrutura técnica ou orçamento para implantar e manter uma solução corporativa própria.

A solução será desenvolvida como aplicação web com backend organizado em microsserviços, frontend responsivo, PostgreSQL como banco de dados principal, RabbitMQ para comunicação assíncrona, integração com APIs externas, CI/CD com GitHub Actions, infraestrutura em nuvem provisionada por Terraform, testes automatizados e monitoramento com Prometheus e Grafana.

---

## 2. Problema Identificado

Muitas oficinas pequenas ainda controlam sua operação com ferramentas desconectadas: papel, planilhas, mensagens de WhatsApp, anotações manuais e controles financeiros separados. Esse modelo funciona enquanto a operação é muito pequena, mas se torna frágil quando existem vários veículos em atendimento, orçamentos pendentes, pagamentos parciais e histórico de manutenção a consultar.

Além disso, pequenas oficinas geralmente não possuem equipe técnica para instalar, configurar, atualizar e manter sistemas próprios. Mesmo quando existe interesse em digitalizar a operação, a barreira de entrada pode ser alta: custo de implantação, manutenção, backup, segurança, atualização e suporte.

### 2.1 Qual é o problema?

O problema principal é a ausência de um fluxo integrado entre atendimento, oficina e financeiro, somada à dificuldade de pequenas oficinas adotarem uma solução tecnológica profissional sem infraestrutura própria.

Uma ordem de serviço passa por várias etapas e cada etapa depende da anterior. Quando esse processo fica espalhado em ferramentas diferentes, a oficina perde rastreabilidade e aumenta a chance de erro. Quando cada oficina precisa operar sistemas isolados, a manutenção técnica também se torna mais cara e menos viável.

Exemplos de falhas comuns:

- orçamento aprovado sem registro formal;
- divergência entre os itens de peça orçados e os valores registrados na ordem de serviço;
- cálculo manual incorreto do valor final;
- dificuldade para saber quais ordens estão aguardando aprovação;
- perda do histórico de manutenção de um veículo;
- ausência de indicadores básicos de faturamento e produtividade;
- falta de evidência sobre quem alterou status, valores ou itens da ordem;
- dificuldade de manter backup, atualização e segurança em soluções locais.

### 2.2 Quem é afetado?

| Perfil Afetado | Impacto |
|----------------|---------|
| Dono ou gerente da oficina | Perde visão sobre faturamento, produtividade e serviços pendentes |
| Atendente | Precisa consultar várias fontes para entender a situação do cliente e do veículo |
| Mecânico | Recebe informações incompletas sobre diagnóstico, aprovação e serviços autorizados |
| Responsável financeiro | Calcula valores e pagamentos com risco de divergência |
| Cliente da oficina | Recebe atendimento menos transparente e pode não ter histórico confiável do veículo |


### 2.3 Qual é o impacto atual?

| Dimensão | Impacto |
|----------|---------|
| Operacional | Retrabalho, demora no atendimento e dificuldade para acompanhar status das ordens |
| Financeiro | Perda por cobrança incorreta, item de orçamento não registrado, desconto mal aplicado ou pagamento não conciliado |
| Qualidade | Baixa padronização do atendimento e ausência de evidência sobre decisões tomadas |
| Gestão | Pouca visibilidade sobre serviços mais realizados, receita por período e histórico dos veículos |


---

## 3. Justificativa

O OficinaPro é relevante porque uma oficina mecânica não opera apenas com cadastros. O trabalho real depende de um processo encadeado: oficina, usuários, cliente, veículo, ordem de serviço, diagnóstico, orçamento, aprovação, execução, cobrança e pagamento. Quando esses elementos não são integrados, o sistema se limita a armazenar dados, mas não ajuda a controlar o negócio.

A natureza SaaS do OficinaPro é parte central da proposta: várias oficinas utilizam a mesma plataforma em nuvem, com custo operacional menor, atualização centralizada, monitoramento comum e evolução contínua do produto. Essa arquitetura também exige decisões técnicas maduras, principalmente em segurança, isolamento de dados e desenho multi-tenant.

### 3.1 Existe solução no mercado? Qual é a lacuna?

Existem soluções de gestão para oficinas, ERPs genéricos e sistemas de ordem de serviço. Porém, muitas alternativas são amplas demais para pequenas oficinas ou exigem configuração pesada para o fluxo de atendimento e financeiro.

A lacuna que o OficinaPro busca atender é uma solução SaaS focada no fluxo operacional da oficina, com onboarding simples e separação segura entre oficinas. O objetivo não é competir com grandes ERPs, mas demonstrar uma plataforma tecnicamente consistente, com regras de negócio claras, rastreabilidade, isolamento multi-tenant e integração entre módulos essenciais.

### 3.2 Qual é a oportunidade de melhoria?

A oportunidade está em transformar a ordem de serviço no centro do processo e transformar a oficina em um tenant da plataforma. No OficinaPro, cada oficina terá sua própria operação isolada, enquanto a plataforma compartilha infraestrutura, código e serviços.

Essa abordagem reduz perda de informação, permite automatizar regras que hoje dependem de controle manual e cria base para evolução como produto real: novos tenants podem ser cadastrados sem criar uma nova instalação do sistema.

### 3.3 Por que o projeto não é apenas um CRUD?

O sistema terá cadastros, como qualquer aplicação de negócio, mas seu valor está nas regras, consequências e restrições entre as entidades:

- uma oficina (*tenant*) possui seus próprios usuários, clientes, veículos e ordens;
- uma oficina não pode acessar dados de outra oficina;
- uma ordem de serviço não pode avançar para execução sem orçamento aprovado;
- itens de peça podem ser descritos manualmente em cada orçamento, sem controle de saldo no sistema;
- o fechamento financeiro depende da composição de serviços, peças, descontos e pagamentos;
- o status financeiro da ordem depende dos pagamentos registrados;
- o histórico do veículo é formado automaticamente a partir das ordens concluídas;
- ações críticas precisam ser rastreáveis por usuário, tenant e data.

Essas regras tornam o projeto mais complexo do que um cadastro simples e justificam arquitetura multi-tenant, banco relacional, testes automatizados, observabilidade e controle de infraestrutura.

### 3.4 Arquitetura SaaS multi-tenant

O OficinaPro é especificado como uma plataforma SaaS multi-tenant para representar um cenário profissional de mercado: múltiplas oficinas utilizam a mesma aplicação e a mesma infraestrutura base, mantendo isolamento lógico dos dados por tenant.

Essa decisão traz desafios técnicos importantes e relevantes para o Projeto Experimental:

- controle de tenant em autenticação e autorização;
- inclusão de `tenant_id` nas entidades de negócio;
- consultas sempre filtradas por tenant;
- testes automatizados para impedir vazamento de dados entre oficinas;
- monitoramento de uma plataforma compartilhada;
- infraestrutura em nuvem compatível com crescimento gradual;
- necessidade de logs e auditoria com identificação do tenant.

### 3.5 Alinhamento com as exigências do Projeto Experimental

| Exigência | Aplicação no OficinaPro |
|-----------|--------------------------|
| Arquitetura de microsserviços | Separação por domínios: tenants, autenticação, clientes/veículos, ordens de serviço, financeiro, notificações e relatórios. No MVP, três microsserviços implantáveis de forma independente (identidade, clientes/veículos e ordens), cada um com banco próprio; os demais domínios serão separados conforme o Documento 07 (ADR) |
| Integração com API externa | ViaCEP (endereço do cliente pelo CEP) e API FIPE (marca, modelo e ano do veículo) |
| Banco de dados com justificativa | PostgreSQL para consistência transacional, integridade referencial, relacionamentos fortes e isolamento por `tenant_id` |
| Deploy em nuvem | Publicação em AWS, respeitando a exigência de provedor de nuvem |
| Infraestrutura como Código | Terraform para provisionar recursos de forma reprodutível |
| CI/CD | GitHub Actions para build, testes e deploy |
| Testes automatizados | Validação de regras críticas, incluindo aprovação de orçamento, fechamento financeiro e isolamento entre tenants |
| Monitoramento | Prometheus e Grafana para métricas, dashboards e acompanhamento da saúde dos serviços |

### 3.6 Justificativa do banco de dados

O PostgreSQL é o banco principal do projeto porque o domínio do OficinaPro é relacional, transacional e multi-tenant. Oficina, usuários, clientes, veículos, ordens de serviço, orçamentos e pagamentos possuem relacionamentos fortes e precisam manter consistência.

Um banco NoSQL poderia ser útil em cenários de dados muito flexíveis ou documentos independentes, mas não é a melhor escolha para o núcleo operacional e financeiro do OficinaPro. O sistema precisa de integridade referencial, transações ACID, constraints, índices e consultas relacionais para evitar inconsistências como pagamento sem ordem, orçamento associado à oficina incorreta, histórico de veículo incompleto ou dados de uma oficina associados a outra.

No modelo multi-tenant, o PostgreSQL também permite adotar estratégias de isolamento por `tenant_id`, constraints compostas e, futuramente, políticas de Row Level Security se o grupo optar por reforçar a segurança no nível do banco.

---

## 4. Valor Agregado

| Benefício | Descrição |
|-----------|-----------|
| Modelo SaaS | Oficinas acessam o sistema pela web, sem instalar ou manter infraestrutura própria |
| Isolamento por oficina | Cada tenant possui seus próprios dados, usuários, ordens e indicadores |
| Rastreabilidade operacional | Cada ordem de serviço mantém o histórico das etapas, responsáveis e decisões |
| Controle financeiro | O fechamento considera serviços, itens de peças orçados manualmente, descontos, pagamentos e status financeiro |
| Redução de erro manual | Regras automatizadas reduzem dependência de planilhas e anotações |
| Histórico do veículo | Serviços concluídos alimentam um histórico consultável por veículo |
| Gestão mais clara | Indicadores permitem acompanhar ordens abertas, concluídas, faturamento e serviços realizados |
| Base técnica profissional | O projeto demonstra SaaS, multi-tenancy, microsserviços, mensageria, banco relacional, CI/CD, testes, IaC e observabilidade |
| Evolução por módulos | A arquitetura permite evoluir módulos sem comprometer toda a plataforma |

---

## 5. Escopo do Projeto

### 5.1 O que ESTÁ no escopo

#### MVP do 4º semestre

> Itens marcados com [x] estão demonstrados no protótipo do MVP (repositório do sistema, pasta `mvp/`, versão de 22/11/2026), com dados fictícios. A demonstração não equivale a produto pronto para operação real; cobertura, segurança e observabilidade continuam como metas do 5º semestre.

- [x] Cadastro de oficina (*tenant*) com dados básicos.
- [x] Autenticação de usuários vinculados a uma oficina.
- [x] Controle básico de acesso por perfil.
- [x] Isolamento lógico dos dados por `tenant_id`.
- [x] Cadastro de clientes por oficina.
- [x] Cadastro de veículos vinculados aos clientes da oficina.
- [x] Endereço do cliente preenchido pelo CEP com ViaCEP, com preenchimento manual se a API falhar.
- [x] Marca, modelo e ano do veículo padronizados pela API FIPE, com preenchimento manual como alternativa.
- [x] Abertura de ordem de serviço dentro do contexto da oficina.
- [x] Controle de status da ordem de serviço.
- [x] Registro de diagnóstico técnico.
- [x] Criação de orçamento com serviços e peças.
- [x] Aprovação ou recusa de orçamento.
- [x] Bloqueio de execução sem orçamento aprovado.
- [x] Fechamento financeiro da ordem de serviço, com pagamento feito no local e registrado pelo financeiro.
- [x] Histórico de manutenção por veículo.
- [x] Ambiente local padronizado com Docker Compose.
- [x] Testes automatizados para regras críticas e isolamento entre tenants.
- [x] Cenário de demonstração com cinco oficinas de teste e equipes de tamanhos diferentes; incluir um cliente vinculado a duas oficinas e outro vinculado às cinco, mantendo os dados operacionais de cada oficina isolados.

#### Evolução para o 5º semestre

- [ ] Configurações específicas por oficina.
- [ ] Notificações ao cliente.
- [ ] Dashboard operacional por oficina.
- [ ] Relatórios gerenciais por tenant.
- [ ] Auditoria detalhada de ações críticas.
- [ ] Deploy em AWS.
- [ ] Provisionamento de infraestrutura com Terraform.
- [ ] CI/CD com GitHub Actions.
- [ ] Monitoramento com Prometheus e Grafana.
- [ ] Cobertura de testes mínima de 60%.

### 5.2 O que NÃO está no escopo

- Marketplace público de assinatura com cobrança recorrente real.
- Gestão comercial de planos pagos do SaaS em produção.
- Emissão oficial de nota fiscal eletrônica.
- Integração real com bancos ou transações financeiras em produção.
- Pagamento online por provedor de pagamento, inclusive em ambiente sandbox. No escopo atual o cliente paga no local e o financeiro registra o pagamento; o pagamento online fica como evolução futura.
- Aplicativo móvel nativo publicado em lojas.
- Controle contábil completo.
- Folha de pagamento ou comissão de mecânicos.
- Compra automática de peças com fornecedores.
- Integração com seguradoras.
- Diagnóstico automático de defeitos por inteligência artificial.
- Substituição completa de um ERP empresarial.

---

## 6. Stakeholders

| Papel | Nome / Perfil | Responsabilidade |
|-------|---------------|-----------------|
| Product Owner | Eloisa do Santos Brandão | Definição de prioridades, validação funcional e alinhamento com a proposta |
| Administrador da plataforma | Equipe GarageOps | Cadastrar, editar, suspender e excluir oficinas, conhecendo o cadastro delas (nome, identificador, identidade visual e situação); acompanhar disponibilidade, estado técnico e métricas agregadas; não consultar nem alterar conteúdo operacional das oficinas |
| Usuário final — administrador da oficina | Dono ou gerente da oficina | Acompanhar ordens, faturamento e indicadores da própria oficina |
| Usuário final — atendente | Profissional que recebe o cliente | Cadastrar cliente, veículo e abrir ordem de serviço |
| Usuário final — mecânico | Profissional técnico da oficina | Registrar diagnóstico e serviços executados; consultar os itens de peça autorizados no orçamento |
| Usuário final — responsável financeiro | Pessoa responsável por cobrança e pagamento | Fechar valores, registrar pagamentos e acompanhar pendências |
| Usuário final — cliente da oficina | Proprietário do veículo | Aprovar orçamento e acompanhar informações do atendimento |
| Orientador | Alexandre Tavares | Acompanhamento técnico e acadêmico |
| Banca avaliadora | Profissionais de TI | Avaliação técnica do projeto |

---

## 7. Premissas e Restrições

### Premissas

- O grupo terá acesso a ambiente AWS ou equivalente para deploy no 5º semestre.
- As APIs externas escolhidas (ViaCEP e FIPE) são públicas e gratuitas e podem ficar indisponíveis; o sistema sempre permite preenchimento manual.
- O pagamento é feito presencialmente na oficina e registrado no sistema pelo financeiro.
- O código-fonte ficará em repositório privado separado do repositório acadêmico de documentação.
- A infraestrutura será descrita com Terraform.
- A aplicação poderá ser executada localmente via Docker Compose como contingência.
- O grupo adotará Pull Requests e revisão mínima entre integrantes para alterações relevantes.
- O isolamento entre oficinas será tratado como requisito obrigatório desde a modelagem do banco e das APIs.

### Restrições

- O orçamento será limitado a recursos gratuitos, free tier ou créditos acadêmicos.
- O prazo seguirá o cronograma do Projeto Experimental.
- O MVP deverá priorizar o fluxo principal SaaS: tenant, usuário, cliente, veículo, ordem, orçamento, execução e fechamento.
- A cobertura mínima exigida será de 60% sobre o código-fonte.
- O deploy final não poderá depender exclusivamente de plataformas simplificadas como Vercel, Heroku, Firebase ou Supabase como backend principal.
- Não haverá integração com provedores de pagamento no escopo atual.
- O projeto não realizará cobrança recorrente real de assinaturas SaaS no escopo acadêmico.

---

## 8. Integrantes do Grupo

| # | Nome Completo | RA | Papel Principal |
|---|---------------|-----|-----------------|
| 1 | Gabriel Carneiro da Silva | 6325300 | DevOps, Banco de Dados e Qualidade |
| 2 | Thiago Marques Bueno | 6325304 | Desenvolvedor Backend e Integrações |
| 3 | Matheus Gabriel Correa Braga Viana | 6325053 | Desenvolvedor Frontend e UX |
| 4 | Eloisa do Santos Brandão | 2325096 | Product Owner e Analista de Requisitos |

---

## 9. Histórico de Revisões

| Versão | Data | Autor | Descrição da Alteração |
|--------|------|-------|------------------------|
| 1.0 | 17/09/2026 | Gabriel | Versão inicial da solicitação do sistema |
| 1.1 | 21/09/2026 | Gabriel | Revisão de escopo, justificativa técnica, MVP, premissas e alinhamento com requisitos do Projeto Experimental |
| 1.2 | 24/09/2026 | Gabriel | Especificação da arquitetura SaaS multi-tenant, com tenant, isolamento de dados e requisitos técnicos associados |
| 1.3 | 13/11/2026 | Gabriel | Remoção do controle interno de estoque, inclusão de itens de peça manuais no orçamento e definição de acesso restrito do administrador da plataforma |
| 1.4 | 20/11/2026 | Gabriel | Administrador da plataforma passa a cadastrar e manter as oficinas, com acesso ao cadastro delas e sem acesso ao conteúdo operacional |
| 1.5 | 21/11/2026 | Gabriel | ViaCEP e FIPE passam a fazer parte do MVP; pagamento feito no local e pagamento online (sandbox) movido para evolução futura |
| 1.6 | 25/11/2026 | Eloisa (revisão) e Gabriel (validação) | Revisão de consistência com os documentos 02 a 08 e com o protótipo do MVP: cabeçalho de versão, itens do MVP já demonstrados, serviços implantados no MVP e papel de administrador da oficina |

---

**Aprovado por:**

___________________________________________________  
Professor(a) Orientador(a)

Data: ___/___/2026
