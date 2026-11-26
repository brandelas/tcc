# Registro de Decisões Arquitetônicas (ADR)

---

**Projeto:** OficinaPro — Plataforma SaaS Multi-Tenant para Gestão de Oficinas Mecânicas
**Grupo:** GarageOps
**Data:** 26/11/2026
**Versão:** 1.2

---

## 1. Objetivo

Este documento registra as decisões técnicas relevantes do OficinaPro, com contexto, decisão, alternativas consideradas e consequências. Cada ADR indica seu status:

- **Aceita:** decisão do grupo já registrada nos Documentos 01 a 04, no README do grupo ou implementada no MVP.
- **Proposta:** recomendação técnica ainda sem confirmação do grupo. Ela orienta o planejamento, mas pode mudar.

A data de cada ADR é a data em que a decisão foi tomada ou, quando essa data não está registrada, a data deste registro.

---

## 2. Formato de cada ADR

Cada ADR segue o formato do template oficial: título, data, status, autores, contexto, decisão, alternativas consideradas (com prós e contras) e consequências (positivas e negativas).

---

## 3. Registro de Decisões

### ADR-001: Linguagem e Framework do Backend

**Data:** 22/11/2026
**Status:** Aceita
**Autores:** GarageOps — base técnica de Thiago Marques Bueno; migração do MVP por Gabriel Carneiro da Silva

#### Contexto

O backend precisa de vários microsserviços pequenos, com regras de autorização por tenant em todas as rotas, contratos claros entre serviços e testes automatizados. O MVP começou em Express com JavaScript. Em paralelo, foi construída uma base em NestJS com TypeScript que exige declarar quem pode acessar cada rota e padroniza os erros.

#### Decisão

Usar **Node.js, TypeScript e NestJS** em todos os microsserviços. Em 22/11/2026 o grupo decidiu migrar o MVP para essa base. A migração manteve as rotas e respostas, foi validada pelos testes de integração (`smoke` e `platform-check`) e renomeou o papel `owner` para `tenant_admin`.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Node.js + NestJS + TypeScript | Tipagem; módulos, guards e injeção de dependência; mesma linguagem do frontend | Mais estrutura que Express; consumo de memória maior que Go |
| Node.js + Express + JavaScript | Simples; foi a primeira versão do MVP | Sem tipagem nem padrão de organização; autorização dependia de disciplina manual |
| Java + Spring Boot | Robusto e maduro | Curva de aprendizado e inicialização mais lenta em contêiner; nova linguagem para o grupo |
| Go | Binários leves e rápidos | Ecossistema web menor; nova linguagem para o grupo |

#### Consequências

**Positivas:**
- Rotas bloqueadas por padrão: toda rota declara `@TenantEndpoint`, `@AccountEndpoint`, `@PlatformEndpoint` ou `@Public`.
- Contratos e regras com tipagem, o que facilita testes unitários com Jest e de rota com Supertest.
- Uma biblioteca comum (`libs/common`) para token, guards, erros e chamadas internas, compilada dentro de cada imagem.

**Negativas:**
- A biblioteca comum cria acoplamento de código entre os serviços: mudanças nela exigem novo build de todos.
- Os módulos novos precisam seguir o padrão da base; código antigo em Express não deve ser reaproveitado sem adaptação.

---

### ADR-002: Escolha do Banco de Dados

**Data:** 24/09/2026 (PostgreSQL); 22/11/2026 (banco por serviço no MVP)
**Status:** Aceita
**Autores:** GarageOps — área de Banco de Dados (Gabriel Carneiro da Silva)

#### Contexto

O domínio é relacional e transacional: oficina, identidades, vínculos, clientes, veículos, ordens, versões de orçamento, decisões e pagamentos têm relações fortes. Aprovações e pagamentos não podem ficar em estado parcial (RNF-011), e toda consulta precisa ser filtrada por tenant (RNF-004). A justificativa técnica completa está no Documento 01 (seção 3.6) e no Documento 04 (seção 2).

#### Decisão

Usar **PostgreSQL** com **um banco lógico por microsserviço** (`identitydb`, `customersdb` e `workordersdb`). Cada serviço é o único dono do seu banco; outros serviços acessam esses dados somente por API ou evento. No ambiente local, os três bancos ficam em uma única instância PostgreSQL 16. Na nuvem, a proposta é uma instância RDS com os mesmos bancos lógicos e um usuário de banco por serviço (Documento 05).

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| PostgreSQL, banco lógico por serviço em uma instância | ACID, restrições, índices compostos com `tenant_id`; autonomia por serviço com custo de uma instância | Falha da instância afeta todos os serviços; exige disciplina de credenciais por banco |
| PostgreSQL, uma instância por serviço | Isolamento total de falhas e recursos | Custo multiplicado no ambiente acadêmico |
| Banco único com tabelas compartilhadas | Consultas entre domínios simples | Viola a independência dos microsserviços (RNF-006) |
| MongoDB | Esquema flexível | Sem integridade referencial; o domínio não precisa de esquema flexível |

#### Consequências

**Positivas:**
- Integridade dentro de cada serviço, inclusive com chave estrangeira composta (`tenant_id`, `customer_id`) entre veículo e cliente.
- Unicidade por oficina: telefone do cliente e placa do veículo são únicos dentro do tenant, não globalmente.

**Negativas:**
- Não há transação única entre serviços. Fluxos que cruzam domínios precisam de validação por API ou consistência eventual por eventos (ADR-003).
- No MVP local, os três serviços usam o mesmo usuário de banco. Na nuvem, cada serviço deve ter usuário próprio com acesso somente ao seu banco.
- As tabelas do MVP são criadas pelo próprio serviço ao iniciar. Migrations versionadas (RNF-013) ainda precisam ser criadas.

---

### ADR-003: Estratégia de Comunicação entre Microsserviços

**Data:** 25/11/2026
**Status:** Proposta (a parte síncrona já está implementada no MVP)
**Autores:** GarageOps — área de Backend e Integrações (Thiago Marques Bueno)

#### Contexto

No MVP, as chamadas entre serviços são poucas e precisam de resposta imediata: validar que cliente e veículo pertencem ao tenant ao abrir uma ordem, vincular o perfil do cliente na ativação do convite e apagar os dados de uma oficina excluída. Os requisitos também preveem eventos assíncronos (RNF-008) para notificações (RF-029), auditoria (RF-032) e relatórios (RF-030, RF-031), que ainda não existem.

#### Decisão

1. **Síncrono (implementado):** chamadas HTTP internas em `/internal/*`, autenticadas por segredo de serviço e bloqueadas na entrada pública. O tenant é sempre repassado a partir do token verificado.
2. **Assíncrono (proposto):** **RabbitMQ** para eventos que não precisam de resposta imediata, começando por `ordem.orcamento_enviado`, `ordem.decisao_registrada`, `ordem.mensagem_criada`, `ordem.status_alterado` e `pagamento.registrado`, consumidos pelos serviços de notificação e de auditoria.
Eventos propostos para o primeiro ciclo (versão 1 de cada contrato):

| Evento | Produtor | Consumidores | Conteúdo mínimo além do envelope |
|---|---|---|---|
| `ordem.orcamento_enviado` | workorders | Notification, Audit | ordem, versão do orçamento, total |
| `ordem.decisao_registrada` | workorders | Notification, Audit | ordem, versão, decisão, autor |
| `ordem.mensagem_criada` | workorders | Notification | ordem, tipo de autor (cliente ou equipe) |
| `ordem.status_alterado` | workorders | Notification, Audit, Reporting | ordem, status anterior e novo |
| `pagamento.registrado` | workorders (Billing após a separação) | Audit, Reporting | ordem, valor, forma de pagamento |

O envelope de todo evento tem `event_id`, `tipo`, `versao`, `tenant_id`, `correlation_id` e `ocorrido_em`. Eventos não carregam texto de mensagens nem dados pessoais do cliente; o consumidor que precisar do conteúdo consulta a API do serviço dono, com autorização.

3. **Confiabilidade proporcional ao MVP:** todo evento leva `event_id`, `tenant_id`, `correlation_id`, tipo, versão e data; os consumidores gravam o `event_id` processado e ignoram duplicados (idempotência); mensagens que falham são reprocessadas até 3 vezes, com intervalo crescente, e depois vão para uma *dead letter queue* para análise. O padrão *transactional outbox* será usado nos produtores em que perder o evento cause inconsistência (por exemplo, auditoria de pagamento).

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| REST síncrono + RabbitMQ para eventos | Simples onde a resposta é necessária; desacoplamento nos efeitos colaterais | Duas formas de integração para manter |
| Somente REST síncrono | Mais simples | Notificação e auditoria ficariam acopladas e falhas se propagariam para o fluxo principal |
| Somente mensageria | Desacoplamento máximo | Complexidade desnecessária para validações que precisam de resposta imediata |
| Amazon SQS/SNS | Gerenciado, sem broker para operar | Sem equivalente local idêntico; o grupo já previu RabbitMQ nos requisitos (RNF-016) |

#### Consequências

**Positivas:**
- O fluxo principal da ordem não depende da disponibilidade de notificação e auditoria.
- O mesmo RabbitMQ roda no Docker Compose e na nuvem.

**Negativas:**
- Exige RabbitMQ no ambiente local (RNF-016) e um broker na AWS: Amazon MQ ou contêiner (pendente, Documento 05).
- Idempotência, DLQ e outbox precisam de testes próprios.
- **Pendente de decisão do grupo:** a lista final de eventos e se o Amazon MQ será usado na AWS.

---

### ADR-004: Provedor de Nuvem e Serviços

**Data:** 25/11/2026 (registro; AWS consta no Documento 01)
**Status:** Aceita
**Autores:** GarageOps — área de DevOps (Gabriel Carneiro da Silva)

#### Contexto

O termo de aceite exige deploy em nuvem de grande porte, infraestrutura em Terraform e proíbe depender apenas de PaaS/BaaS simplificados. O Documento 01 já define a AWS como provedor.

#### Decisão

Usar a **AWS**, com a região **us-east-1** proposta por custo (Documento 05). Toda a infraestrutura será criada por **Terraform**, com estado remoto em S3 e lock em DynamoDB.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| AWS | Líder de mercado; Terraform maduro; serviços gerenciados para contêineres, banco e mensageria | Custos crescem se o ambiente ficar ligado o tempo todo |
| GCP | Boa experiência com contêineres | Menor familiaridade do grupo |
| Azure | Créditos acadêmicos | Menor familiaridade do grupo |

#### Consequências

**Positivas:**
- Atende aos critérios C4 e C5 do termo de aceite quando o ambiente for provisionado.

**Negativas:**
- Custo estimado de cerca de US$ 160 por mês com o ambiente ligado 24 horas (Documento 05, seção 6); por isso o ambiente deve ficar ligado só quando for usado.
- **Pendente:** definir a conta AWS usada (própria ou acadêmica), o que afeta permissões IAM e OIDC.

---

### ADR-005: Estratégia de Containerização e Orquestração

**Data:** 25/11/2026
**Status:** Proposta
**Autores:** GarageOps — área de DevOps (Gabriel Carneiro da Silva)

#### Contexto

Cada serviço do MVP já tem a própria imagem Docker e roda localmente com Docker Compose, que também é a contingência da banca. O termo de aceite exige build e deploy automatizados dos microsserviços.

#### Decisão

Na AWS, executar cada serviço como um serviço **ECS Fargate** próprio, com a mesma imagem usada localmente, publicada no ECR pelo GitHub Actions. A entrada pública é um Application Load Balancer com as mesmas regras de caminho do nginx local. Manter o `docker-compose.yml` funcional até a banca.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| ECS Fargate | Sem servidores para administrar; deploy independente por serviço | Custo por vCPU e memória |
| EKS (Kubernetes) | Padrão de mercado | Plano de controle pago e operação complexa para o tamanho do projeto |
| EC2 com Docker Compose | Barato e igual ao ambiente local | Deploy manual ou sob medida; não demonstra implantação independente |

#### Consequências

**Positivas:**
- Cada microsserviço é implantado e escalado de forma independente (RNF-006).
- A mesma imagem roda localmente e na nuvem.

**Negativas:**
- NAT Gateway e ALB representam boa parte do custo fixo.
- As chamadas internas precisam de descoberta de serviço (Cloud Map) em vez dos nomes do Docker Compose.

---

### ADR-006: Integrações com APIs Externas

**Data:** 21/11/2026
**Status:** Aceita
**Autores:** GarageOps

#### Contexto

O termo de aceite exige pelo menos uma API externa relevante para a regra de negócio. O cadastro do cliente precisa de endereço e o cadastro do veículo precisa de marca, modelo e ano padronizados para o histórico.

#### Decisão

Integrar **ViaCEP** no cadastro do cliente (RF-009) e **API FIPE** no cadastro do veículo (RF-010). As duas consultas passam pelo serviço `customers` (o navegador não chama as APIs diretamente), têm tempo limite e permitem preenchimento manual em caso de falha (RNF-044). A FIPE tem cache de 24 horas por causa do limite diário da API pública. As duas integrações já funcionam no MVP.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| ViaCEP + FIPE | Relevantes ao cadastro; públicas e sem custo | Disponibilidade e limites fora do controle do grupo |
| Provedor de pagamento (sandbox) | Demonstraria fluxo financeiro online | Fora do escopo: o pagamento é presencial (RF-028) |
| Envio de SMS/WhatsApp | Necessário ao convite real | Custo e cadastro em provedor; continua pendente |

#### Consequências

**Positivas:**
- Atende ao critério C2 com integrações ligadas ao negócio e já demonstráveis.

**Negativas:**
- Os testes automatizados precisam simular as APIs para não depender da internet.
- A FIPE pública pode limitar requisições; o cache e o preenchimento manual reduzem o impacto.

---

### ADR-007: Decomposição em Microsserviços

**Data:** 22/11/2026
**Status:** Aceita para o MVP; separação dos demais domínios proposta
**Autores:** GarageOps

#### Contexto

Os Documentos 02 a 04 definem domínios lógicos: Tenant, Auth, Customer Vehicle, Work Order, Billing, Notification, Reporting e Audit. Implantar todos desde o início aumentaria custo e esforço sem benefício no MVP.

#### Decisão

No MVP, três microsserviços implantáveis de forma independente, cada um com banco próprio:

| Serviço | Domínios lógicos |
|---|---|
| `identity` | Tenant e Auth |
| `customers` | Customer Vehicle |
| `workorders` | Work Order e, provisoriamente, pagamentos e saldo (Billing) |

**Proposta para o 5º semestre:** separar Billing (pagamentos e fechamento financeiro) e criar Notification e Audit como consumidores de eventos (ADR-003); Reporting depois, se houver tempo. A ordem e o momento dessa separação dependem de decisão do grupo.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Três serviços agora, separação incremental | Entrega viável; limites claros entre domínios | Billing fica temporariamente dentro de `workorders` |
| Oito serviços desde o início | Arquitetura-alvo completa | Custo e esforço altos para o 4º semestre |
| Monólito modular | Mais simples | Não atende à exigência de microsserviços |

#### Consequências

**Positivas:**
- Atende ao critério C1 no MVP com serviços, bancos e imagens independentes.

**Negativas:**
- Separar Billing exige migrar a tabela `payments` para um banco próprio e trocar a consulta de saldo por API ou evento.

---

### ADR-008: Isolamento Multi-Tenant

**Data:** 24/09/2026
**Status:** Aceita
**Autores:** GarageOps — regra de negócio revisada por Eloisa do Santos Brandão

#### Contexto

Todas as oficinas usam a mesma aplicação e infraestrutura (RNF-001, RNF-022). Uma oficina não pode acessar, alterar nem inferir dados de outra. Uma pessoa pode ter vínculos com várias oficinas e o cliente usa uma identidade GarageOps única.

#### Decisão

Isolamento lógico por **`tenant_id`** em todas as entidades operacionais. O tenant ativo vem somente do token emitido pelo `identity` após a seleção de uma oficina com vínculo ativo; nunca do corpo, da URL ou de cabeçalho enviado pelo navegador. Toda consulta e alteração filtra pelo tenant ativo, e registros de outro tenant retornam como não encontrados. O administrador da plataforma usa um tipo de sessão próprio, sem tenant, e é recusado pelos serviços de clientes e ordens.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Coluna `tenant_id` com filtro no servidor | Uma estrutura para todas as oficinas; custo baixo | Depende de disciplina no código e de testes |
| Esquema por oficina | Isolamento mais forte no banco | Migrations e operação multiplicadas por oficina |
| Banco por oficina | Isolamento máximo | Inviável em custo e operação para o projeto |

#### Consequências

**Positivas:**
- Já comprovado no MVP pelo teste de integração `smoke`, que tenta ler a ordem de outra oficina e recebe 404.

**Negativas:**
- Uma consulta sem filtro por tenant vaza dados. Por isso os testes de isolamento devem rodar no CI (RNF-027).
- *Row Level Security* do PostgreSQL fica como reforço possível, sem decisão.

---

### ADR-009: Identidade Global, Autenticação e Papéis por Vínculo

**Data:** 22/11/2026
**Status:** Aceita
**Autores:** GarageOps — Thiago Marques Bueno (autenticação e token) e Matheus Gabriel Correa Braga Viana (acesso por papel na interface)

#### Contexto

A mesma pessoa pode ser membro de equipe em uma oficina e cliente em outra. Os papéis valem por vínculo, não para a pessoa toda. O administrador da plataforma não pode usar seu papel para ler dados das oficinas.

#### Decisão

- Cada pessoa tem **uma única identidade GarageOps**, que pode ter vínculos com várias oficinas. Cada vínculo tem papel e situação próprios, e os dados operacionais continuam separados por oficina (RF-033, RF-035). O cliente ativa o vínculo por convite validado com código (RF-034).
- O `identity` é o **único emissor** de tokens JWT (HS256, emissor verificado, validade máxima de 8 horas, RNF-033). Os demais serviços apenas verificam.
- O token tem `scope`: `account` (conta logada sem oficina), `tenant` (com `activeTenantId` e papéis do vínculo) ou `platform`.
- Papéis de oficina: `tenant_admin`, `attendant`, `mechanic`, `finance` e `customer`. Senhas com hash bcrypt (RNF-032).
- Rotas de equipe e de convite conferem o papel no banco a cada chamada, para que uma pessoa rebaixada ou desativada perca o acesso imediatamente.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| JWT próprio com `scope` | Simples; sem dependência externa | Revogação só no vencimento, exceto onde o papel é conferido no banco |
| Amazon Cognito ou Keycloak | Recursos prontos (MFA, recuperação de conta) | Mais um componente para integrar e operar |
| Sessão no servidor | Revogação imediata | Estado compartilhado entre serviços |

#### Consequências

**Positivas:**
- O tenant ativo e os papéis viajam em um token verificado em todos os serviços.

**Negativas:**
- Quem estava em uma oficina suspensa ou excluída mantém acesso até o token vencer (até 8 horas), limitação registrada no README do MVP. O sistema final deve revogar sessões ou consultar a situação da oficina a cada chamada.
- **Pendente de decisão do grupo:** permitir mais de um papel para a mesma pessoa na mesma oficina; recuperação de conta.

---

### ADR-010: Frontend e Navegação por Papel

**Data:** 26/11/2026
**Status:** Aceita
**Autores:** GarageOps — área de Frontend e UX (Matheus Gabriel Correa Braga Viana)

#### Contexto

Equipe, cliente e administrador da plataforma usam a mesma aplicação web, com navegação conforme o papel ativo. A primeira versão do MVP usava React e Vite com JavaScript (JSX). Em 26/11/2026 o frontend foi refeito em TypeScript, com nova identidade visual, e incorporado ao repositório do sistema.

#### Decisão

Uma **SPA em React com TypeScript e Vite**, servida por nginx no contêiner `web`, com áreas e ações exibidas conforme o papel ativo. Os tipos das respostas da API ficam em um único módulo, e as chamadas aos serviços ficam separadas por domínio (`identity`, `customers` e `workorders`). A interface esconde ações não permitidas, mas a autorização real é sempre feita pelo servidor (RNF-034). Testes de interface com Vitest e React Testing Library.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| React + TypeScript + Vite | Build rápido; tipos alinhados aos contratos da API; mesma linguagem do backend | Exige organizar rotas e estado manualmente; tipos mantidos à mão enquanto não houver OpenAPI |
| React + Vite com JavaScript | Primeira versão do MVP; menos configuração | Mudanças de contrato da API só aparecem em tempo de execução |
| Next.js | Renderização no servidor | Mais um servidor Node para operar; sem necessidade de SEO |
| Angular | Estrutura completa | Curva de aprendizado e sem uso prévio no projeto |

#### Consequências

**Positivas:**
- Uma única aplicação para todos os papéis, sem duplicar telas.

**Negativas:**
- Os tipos da API são escritos à mão; até existir o contrato OpenAPI (RNF-030), backend e frontend podem divergir sem aviso.
- A cobertura dos testes de interface ainda é pequena e precisa crescer até a banca.

---

### ADR-011: Qualidade, Entrega e Observabilidade

**Data:** 25/11/2026 (registro)
**Status:** Aceita (ferramentas exigidas pelo termo de aceite); detalhes de implementação propostos
**Autores:** GarageOps — área de DevOps, Banco de Dados e Qualidade (Gabriel Carneiro da Silva)

#### Contexto

O termo de aceite exige GitHub Actions para build e deploy dos microsserviços, testes automatizados com cobertura global mínima de 60%, Terraform e Prometheus com Grafana funcionando na banca. Hoje o MVP tem testes unitários (Jest no backend; Vitest e React Testing Library no frontend), testes de integração (`smoke`, `platform-check`, `team-check`) e o script `test:cov`, mas não tem workflows, `/metrics` nem Terraform.

#### Decisão

- **CI no GitHub Actions** em todo Pull Request: lint, build, testes e cobertura global somada de todos os serviços, com o limite de 60% verificado automaticamente (RNF-028) e merge bloqueado em caso de falha (RNF-025).
- **CD** na branch principal: imagens no ECR, `terraform apply` e atualização dos serviços ECS (Documento 05).
- **Observabilidade:** endpoint `/metrics` em cada serviço com contagem de requisições, erros e latência, sem dados de clientes ou conteúdo das oficinas; Prometheus e Grafana em contêineres; logs estruturados no CloudWatch.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| GitHub Actions + Terraform + Prometheus/Grafana | Exigência do termo de aceite; integração com o repositório | Pipeline e stack a construir do zero |
| CloudWatch sem Prometheus/Grafana | Gerenciado | Não atende ao critério C8 |

#### Consequências

**Positivas:**
- Os critérios C5 a C8 passam a ter um plano verificável (Documento 08).

**Negativas:**
- A cobertura atual não foi medida em relatório publicado; não se pode afirmar que já atinge 60%.

---

### ADR-012: Escopo sem Estoque Interno

**Data:** 13/11/2026
**Status:** Aceita
**Autores:** GarageOps — regra de negócio: Eloisa do Santos Brandão

#### Contexto

As versões iniciais previam um serviço de estoque com catálogo, saldo, reserva e baixa de peças. A premissa revista pelo grupo é que muitas oficinas pequenas compram as peças de autopeças parceiras a cada atendimento, sem manter estoque próprio relevante.

#### Decisão

O MVP **não controla estoque**. Peças entram como itens manuais do orçamento (descrição, quantidade e valor), sem catálogo, saldo, reserva ou movimentação (RF-015). Os requisitos RF-020 a RF-024 foram retirados e seus identificadores não são reutilizados.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Peças manuais no orçamento | Simples; reflete a operação das oficinas-alvo | Sem controle de saldo nem alerta de reposição |
| Estoque com reserva integral e baixa | Controle completo de peças | Mais um serviço e um fluxo distribuído de reserva, sem necessidade comprovada no MVP |

#### Consequências

**Positivas:**
- O fluxo da ordem fica menor e testável, sem coordenação entre serviços para reservar peças.

**Negativas:**
- Oficinas com estoque próprio não são atendidas nesta versão. Um serviço de estoque pode voltar como evolução, com novo ADR.

---

### ADR-013: Orçamento Versionado com Decisão do Cliente

**Data:** 09/11/2026
**Status:** Aceita
**Autores:** GarageOps — regra de negócio: Eloisa do Santos Brandão

#### Contexto

A oficina precisa comprovar o que o cliente aprovou e manter o histórico da negociação quando o orçamento é revisado ou recusado.

#### Decisão

Cada envio de orçamento cria uma **nova versão imutável**. O cliente autenticado aprova ou recusa uma versão específica, e a decisão guarda autor, data, hora e motivo, quando informado (RF-016, RF-037). Uma nova versão substitui a pendente (`superseded`). A execução só é liberada pela versão vigente aprovada (RF-017), e a recusa não encerra a ordem: a oficina pode revisar ou encerrar por recusa definitiva (RF-038).

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Versões imutáveis com decisão por versão | Prova do que foi aprovado; histórico completo | Mais registros por ordem |
| Orçamento único editável | Simples | Perde a evidência do que o cliente aprovou |

#### Consequências

**Positivas:**
- Serviço adicional ou divergente exige nova versão aprovada, o que protege oficina e cliente.

**Negativas:**
- A interface precisa mostrar claramente qual versão está vigente.

---

### ADR-014: Padronização e Comparação de Telefone

**Data:** 26/11/2026
**Status:** Aceita
**Autores:** GarageOps — regra de dados aprovada pelo grupo; implementação no backend (Thiago Marques Bueno e Gabriel Carneiro da Silva)

#### Contexto

O telefone identifica o cliente e é usado no convite de acesso (RF-007, RF-034). Até esta decisão, cada serviço gravava e comparava o número exatamente como foi digitado. Assim, `+5511991000301`, `(11) 99100-0301` e `011 99100-0301` eram tratados como pessoas diferentes, o que permitia cadastrar o mesmo cliente duas vezes na mesma oficina e oferecer a criação de uma segunda conta a quem já tinha uma. A unicidade de telefone por oficina (ADR-002) só funcionava quando o número era digitado sempre no mesmo formato.

#### Decisão

Padronizar o telefone em um **formato canônico único**, aplicado **antes de gravar e antes de comparar** nos três serviços:

- Uma única função compartilhada (`normalizePhone`, em `libs/common`) define a regra: mantêm-se apenas os dígitos; zeros à esquerda são removidos (cobre o `0` de longa distância e o `00` internacional); números com 10 ou 11 dígitos (DDD mais número) recebem o código do país `55`; outros tamanhos permanecem como estão. Exemplo: `+55 (11) 99100-0301`, `(11) 99100-0301` e `0055 11 991000301` tornam-se `5511991000301`.
- A normalização é aplicada no cadastro de cliente, nos convites e na criação e no vínculo de conta e de equipe. O mesmo número em formatos diferentes passa a ser tratado como a mesma pessoa, com o mesmo erro `409` já existente para duplicidade.
- Os dados de demonstração são gravados no formato canônico. Uma migração converte os telefones já existentes ao subir o serviço; quando dois perfis da mesma oficina convergem para o mesmo número, nenhum é alterado ou mesclado e fica um aviso no log para a oficina revisar.
- No frontend, o número é apenas exibido formatado (por exemplo, `+55 (11) 99100-0301`); o valor enviado à API continua sendo somente dígitos.

#### Alternativas Consideradas

| Alternativa | Prós | Contras |
|-------------|------|---------|
| Função única de normalização nos três serviços | Uma só regra; comparação consistente; sem duplicidade por formato | A regra precisa cobrir casos de borda (fixo, celular, código do país) e ter testes |
| Normalizar em cada serviço separadamente | Sem dependência comum | Risco de regras divergentes para o mesmo dado |
| Não normalizar (comportamento anterior) | Nenhuma mudança | Mantém o defeito: mesmo cliente duas vezes e segunda conta indevida |
| Guardar como digitado e normalizar só na comparação | Preserva o formato original | Dois registros com o mesmo número canônico coexistiriam no banco |

#### Consequências

**Positivas:**
- A mesma pessoa é reconhecida mesmo digitando o telefone de outra forma, no cadastro e no convite.
- A restrição de unicidade por oficina (ADR-002) passa a barrar o mesmo número em qualquer formato, com o `409` de sempre.
- Coberta por testes: unitários da função (`phone.spec.ts`) e de integração no sistema no ar (`bugfix-check`), que tentam recadastrar e convidar o mesmo número em formatos diferentes.

**Negativas:**
- Um telefone sem nenhum dígito (por exemplo, um cliente cadastrado sem telefone) é reduzido a vazio; o tratamento desse caso deve ser confirmado pelo grupo.
- A migração pode deixar, de propósito, dois perfis antigos que convergem para o mesmo número intactos, dependentes de revisão manual pela oficina (via log).
- Implementado no repositório do sistema em 26/11/2026 (Pull Request #2, com *merge* feito pelo Gabriel). O tratamento do telefone vazio continua pendente de confirmação do grupo.

---

## 4. Índice de ADRs

| ID | Título | Status | Data |
|----|--------|--------|------|
| ADR-001 | Linguagem e Framework do Backend | Aceita | 22/11/2026 |
| ADR-002 | Escolha do Banco de Dados | Aceita | 24/09/2026 e 22/11/2026 |
| ADR-003 | Comunicação entre Microsserviços | Proposta (síncrono implementado) | 25/11/2026 |
| ADR-004 | Provedor de Nuvem e Serviços | Aceita | 25/11/2026 |
| ADR-005 | Containerização e Orquestração | Proposta | 25/11/2026 |
| ADR-006 | Integrações com APIs Externas | Aceita | 21/11/2026 |
| ADR-007 | Decomposição em Microsserviços | Aceita (MVP); separação proposta | 22/11/2026 |
| ADR-008 | Isolamento Multi-Tenant | Aceita | 24/09/2026 |
| ADR-009 | Identidade Global, Autenticação e Papéis por Vínculo | Aceita | 22/11/2026 |
| ADR-010 | Frontend e Navegação por Papel | Aceita | 26/11/2026 |
| ADR-011 | Qualidade, Entrega e Observabilidade | Aceita; detalhes propostos | 25/11/2026 |
| ADR-012 | Escopo sem Estoque Interno | Aceita | 13/11/2026 |
| ADR-013 | Orçamento Versionado com Decisão do Cliente | Aceita | 09/11/2026 |
| ADR-014 | Padronização e Comparação de Telefone | Aceita | 26/11/2026 |

---

## 5. Observações

- ADRs com status **Proposta** precisam ser discutidos pelo grupo. Quando forem confirmados, o status muda para Aceita e a data é atualizada. Uma decisão que substitua outra gera novo ADR com status Aceita, e o anterior passa a Substituída.
- O grupo deve conferir os autores indicados em cada ADR. Eles foram preenchidos conforme a divisão combinada nos guias de cada integrante: ADR-001 e ADR-003 (Thiago), ADR-002, ADR-004 e ADR-005 (Gabriel), frontend e acesso por papel (Matheus), regras de negócio revisadas pela PO (Eloisa). O Matheus consolida o documento.
- O contrato OpenAPI das rotas (RNF-030) ainda não existe e deve acompanhar os ADR-003 e ADR-009.

---

## 6. Histórico de Revisões

| Versão | Data | Autor | Descrição da Alteração |
|--------|------|-------|------------------------|
| 1.0 | 25/11/2026 | Eloisa (revisão) e Matheus (validação) | Versão inicial com ADRs 001 a 013, separando decisões aceitas de propostas; inclui as decisões de escopo já tomadas (sem estoque, identidade global, orçamento versionado) e a tabela de eventos do ADR-003 |
| 1.1 | 26/11/2026 | Eloisa (revisão) e Matheus (validação) | ADR-010 atualizado: frontend em React com TypeScript, após o redesign incorporado ao MVP em 26/11/2026 |
| 1.2 | 26/11/2026 | Eloisa (revisão) e Matheus (consolidação) | Inclusão do ADR-014 (padronização de telefone nos três serviços); índice e cabeçalho atualizados. Ajuste posterior: referência ao RF-007 e registro do merge da implementação |

---

**Aprovado por:**

___________________________________________________
Professor(a) Orientador(a)

Data: ___/___/2026
