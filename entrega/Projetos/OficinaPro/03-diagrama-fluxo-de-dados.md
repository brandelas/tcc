# Diagrama de Fluxo de Dados (DFD)

---

**Projeto:** OficinaPro — Plataforma SaaS Multi-Tenant para Gestão de Oficinas Mecânicas
**Grupo:** GarageOps
**Data:** 25/11/2026
**Versão:** 1.5 — atualizada conforme decisões do grupo

---

## 1. Objetivo e escopo

Este documento descreve a entrada, o processamento, o armazenamento e a saída dos dados do OficinaPro. O DFD parte dos requisitos do Documento 02 e detalha identidade global, vínculos por oficina, convite do cliente, histórico autorizado, ordem de serviço, versões de orçamento, conversa entre cliente e oficina, financeiro e notificações. O sistema não controla estoque; peças são itens informados manualmente no orçamento.

A identidade global GarageOps com vínculos independentes por oficina faz parte do escopo definido. A pessoa pode usar uma conta para acessar suas oficinas autorizadas; cada oficina mantém seus próprios dados operacionais. Um vínculo do cliente com uma oficina não dá à oficina acesso aos dados de outra.

Os processos são domínios funcionais. O DFD não afirma que cada processo já é um microsserviço separado; a divisão de serviços, interfaces e propriedade de dados será detalhada nos documentos de arquitetura e ADR.

## 2. Notação

| Símbolo | Significado |
|---|---|
| Retângulo | Entidade externa que troca dados com o sistema |
| Círculo | Processo que valida, transforma ou encaminha dados |
| Cilindro | Armazenamento de dados conceitual |
| Fila | Canal de eventos assíncronos |
| Seta | Fluxo identificado e descrito nos dicionários |

## 3. DFD nível 0 — contexto

```mermaid
flowchart LR
    PLATFORM[Administrador da plataforma]
    STAFF[Funcionários da oficina]
    CUSTOMER[Cliente GarageOps]
    EXT[APIs externas<br/>ViaCEP / FIPE]
    MSG[Canal de envio de código<br/>para ativação do convite]
    APP((OficinaPro<br/>SaaS multi-tenant))

    PLATFORM -->|Cadastro, edição, suspensão<br/>e exclusão de oficinas| APP
    APP -->|Cadastro das oficinas, saúde dos<br/>serviços e métricas agregadas| PLATFORM
    STAFF -->|Credenciais, cadastros, ordens,<br/>diagnósticos e orçamentos| APP
    APP -->|Ordens, permissões, indicadores<br/>e confirmações| STAFF
    CUSTOMER -->|Autenticação, aceite de convite,<br/>decisões e mensagens| APP
    APP -->|Históricos autorizados, orçamentos,<br/>mensagens e atualizações| CUSTOMER
    APP -->|CEP e consulta de<br/>marca, modelo e ano| EXT
    EXT -->|Endereço e dados de<br/>referência do veículo| APP
    APP -->|Código de ativação para contato<br/>registrado no convite| MSG
    MSG -->|Resultado do envio| APP
```

O canal de envio de código está representado de forma genérica. O provedor, o custo e o mecanismo de envio precisam ser decididos antes da implementação; o código recebido não substitui a autenticação da conta.

O administrador da plataforma cadastra, edita, suspende e exclui oficinas e, por isso, conhece o cadastro delas (nome, identificador, identidade visual (cor) e situação (ativa ou suspensa)). Fora isso, o fluxo de administração limita-se à saúde global e a métricas agregadas; clientes, veículos, diagnósticos, ordens, orçamentos, valores, mensagens e pagamentos não são acessíveis por sua interface ou API. Na exclusão de uma oficina, cada serviço apaga os próprios dados sem devolvê-los ao console.

## 4. DFD nível 1 — processos e armazenamentos

```mermaid
flowchart LR
    PLATFORM[Administrador da plataforma]
    STAFF[Funcionários]
    CUSTOMER[Cliente]
    EXT[APIs externas]
    MSG[Canal de código]

    P1((1. Gerenciar tenants e convites))
    P2((2. Autenticar e autorizar identidades))
    P3((3. Gerenciar clientes e veículos))
    P4((4. Gerenciar ordens, diagnósticos,<br/>orçamentos e conversas))
    P5((5. Gerenciar financeiro))
    P6((6. Notificar usuários))
    P7((7. Relatórios e auditoria))

    D1[(D1 Tenants e convites)]
    D2[(D2 Identidades e vínculos)]
    D3[(D3 Perfis locais, clientes e veículos)]
    D4[(D4 Ordens, diagnósticos,<br/>versões de orçamento e mensagens)]
    D5[(D5 Pagamentos e fechamentos)]
    D6[(D6 Auditoria e eventos)]
    MQ[(RabbitMQ / eventos)]

    PLATFORM --> P1
    STAFF --> P1
    P1 <--> D1
    P1 <--> D2
    P1 --> MSG
    MSG --> P1

    STAFF --> P2
    CUSTOMER --> P2
    P2 <--> D2
    P2 -->|Validação de vínculo e tenant ativo| P1

    STAFF --> P3
    CUSTOMER --> P3
    P3 <--> D3
    P3 -->|Consultar endereço / referência| EXT
    EXT --> P3
    P3 -->|Valida acesso ao perfil associado| P2

    STAFF --> P4
    CUSTOMER --> P4
    P4 <--> D4
    P4 -->|Valida cliente, veículo e tenant| P3
    P4 --> MQ

    STAFF --> P5
    P5 <--> D5
    P5 -->|Consulta ordem e itens aprovados/executados| P4

    MQ --> P6
    P6 --> CUSTOMER
    P6 --> STAFF

    P1 -->|Eventos de ações críticas| MQ
    P2 -->|Eventos de ações críticas| MQ
    P3 -->|Eventos de ações críticas| MQ
    P5 -->|Eventos financeiros| MQ
    MQ --> P7
    P7 <--> D6
    STAFF --> P7
    P7 -->|Consultas autorizadas por API| P4
    P7 -->|Consultas autorizadas por API| P5
    P7 -->|Indicadores e relatórios do tenant| STAFF
```

O contexto do tenant é validado no servidor. O diagrama não representa acesso direto de um processo ao banco de outro microsserviço; consultas entre domínios ocorrem por interfaces (API) ou eventos. Relatórios e auditoria (processo 7) mantêm armazenamento próprio, alimentado por eventos, e consultam os demais domínios somente por API.

**Situação no MVP:** os processos 1 e 2 estão implementados no serviço `identity`; o processo 3, no serviço `customers`; os processos 4 e 5 (registro de pagamentos e saldo), no serviço `workorders`. A comunicação entre eles é síncrona por API interna. Os processos 6 e 7 e o uso de RabbitMQ estão planejados para o 5º semestre (Documento 07, ADR-003).

## 5. DFD nível 2 — convite e identidade global

```mermaid
flowchart TD
    STAFF[Funcionário autorizado da oficina]
    CUSTOMER[Cliente]
    SEND[Canal de envio do código]
    P1((1.1 Cadastrar perfil local<br/>e iniciar convite))
    P2((1.2 Gerar convite de uso único<br/>e enviar código))
    P3((1.3 Autenticar ou criar identidade<br/>e validar código))
    P4((1.4 Criar vínculo com papel<br/>de cliente no tenant))
    P5((1.5 Autorizar consulta<br/>dos dados associados))
    D1[(Tenant / convite)]
    D2[(Identidade GarageOps / vínculo)]
    D3[(Perfil local / veículos)]

    STAFF -->|Dados do cliente e contato| P1
    P1 --> D3
    P1 -->|Tenant, contato e referência do cliente| P2
    P2 --> D1
    P2 -->|Link de ativação sem dados do cliente| CUSTOMER
    P2 -->|Código temporário| SEND
    SEND --> CUSTOMER
    CUSTOMER -->|Link, código e autenticação| P3
    P3 <--> D2
    P3 -->|Identidade verificada| P4
    P4 --> D2
    P4 -->|Vínculo ativo| P5
    P5 -->|Históricos daquele cliente e tenant| CUSTOMER
    P5 --> D3
```

Possuir o link não autentica a pessoa nem exibe dados. O convite precisa ser limitado no tempo, de uso único, revogável e associado ao contato registrado. A conta autenticada recebe somente um vínculo autorizado; não recebe acesso geral a todos os tenants.

## 6. DFD nível 2 — ordem, recusa, conversa e revisão de orçamento

```mermaid
flowchart TD
    STAFF[Atendente / mecânico autorizado]
    CUSTOMER[Cliente autenticado]
    P1((2.1 Abrir ordem e registrar problema))
    P2((2.2 Registrar diagnóstico))
    P3((2.3 Criar versão do orçamento))
    P4((2.4 Enviar orçamento e notificar))
    P5((2.5 Registrar decisão do cliente))
    P6((2.6 Trocar mensagens vinculadas<br/>à ordem))
    P7((2.7 Criar nova versão revisada))
    P8((2.8 Executar somente versão aprovada))
    P9((2.9 Encerrar por recusa definitiva<br/>ou marcar pronta para retirada))
    D1[(Cliente / veículo por tenant)]
    D2[(Ordem, diagnóstico e versões<br/>imutáveis de orçamento)]
    D3[(Mensagens e decisões)]
    MQ[(Eventos / notificações)]

    STAFF -->|Cliente, veículo e problema| P1
    D1 -.->|Validação por API do serviço<br/>de clientes e veículos| P1
    P1 --> D2
    STAFF -->|Diagnóstico| P2
    P2 --> D2
    STAFF -->|Serviços, peças e valores propostos| P3
    P3 --> D2
    P3 --> P4
    P4 -->|Orçamento disponível| CUSTOMER
    P4 --> MQ
    CUSTOMER -->|Aprovar ou recusar versão| P5
    P5 --> D2
    P5 --> D3
    P5 --> MQ
    CUSTOMER -->|Mensagem sobre a ordem| P6
    STAFF -->|Resposta / esclarecimento| P6
    P6 --> D3
    P6 --> MQ
    STAFF -->|Ajustes negociados| P7
    P7 --> D2
    P7 --> P4
    P5 -->|Versão aprovada| P8
    STAFF -->|Serviços executados e itens efetivamente cobrados| P8
    P8 --> D2
    P5 -->|Recusa definitiva solicitada| P9
    STAFF -->|Encerramento com motivo quando informado| P9
    P9 --> D2
    P9 -->|Evento de encerramento| MQ
```

Regras do fluxo: recusar uma versão não apaga a ordem nem autoriza execução. Uma revisão gera nova versão e preserva a anterior. O cliente precisa aprovar a versão vigente. Itens de peça no orçamento são informados manualmente e não geram controle de saldo ou movimentação de estoque. Se desistir definitivamente, a oficina encerra a ordem como recusada; essa ação é auditada e não pode gerar execução ou cobrança de serviço não autorizado.

## 7. Dicionário resumido de fluxos

| ID | Fluxo | Origem → destino | Conteúdo e regra principal |
|---|---|---|---|
| F-001 | Cadastro de tenant | Plataforma / representante → Tenant Service | Dados da oficina e identificação do tenant |
| F-002 | Credenciais | Usuário → Auth Service | Credenciais; nunca incluir senha em logs |
| F-003 | Identidade e tenant ativo | Auth Service → serviços | Identidade, vínculo, papel e tenant validado no servidor |
| F-004 | Convite | Oficina → cliente | Link de ativação sem acesso direto a dados |
| F-005 | Código de ativação | Canal de envio → cliente | Código temporário enviado ao contato registrado |
| F-006 | Aceite do convite | Cliente → Auth/Tenant Services | Autenticação, código validado e aceite explícito do vínculo |
| F-007 | Perfil e veículos | Oficina/cliente → Customer Vehicle Service | Dados limitados ao tenant e aos perfis autorizados |
| F-008 | Ordem e diagnóstico | Funcionário → Work Order Service | Problema relatado, veículo, diagnóstico e responsável |
| F-009 | Versão de orçamento | Oficina → Work Order Service → cliente | Itens, quantidades, valores, versão e estado de aprovação |
| F-010 | Decisão de orçamento | Cliente → Work Order Service | Aprovação/recusa, identidade, data/hora, versão e motivo quando informado |
| F-011 | Mensagem da ordem | Cliente / funcionário → Work Order Service | Texto, autor, data/hora e identificador da ordem |
| F-012 | Revisão de orçamento | Funcionário → Work Order Service → cliente | Nova versão; versões anteriores preservadas |
| F-013 | Item de peça no orçamento | Funcionário → Work Order Service | Descrição, quantidade e valores informados manualmente; sem consulta ou movimentação de estoque |
| F-014 | Encerramento por recusa | Funcionário autorizado → Work Order Service | Responsável, data/hora, motivo opcional; sem execução ou cobrança de serviço não autorizado |
| F-015 | Notificação | Notification Service → usuário | Aviso de orçamento, nova mensagem e mudança relevante |
| F-016 | Integração externa | Serviços de domínio ↔ API externa | Consulta de endereço pelo CEP (ViaCEP) ou de marca, modelo e ano do veículo (FIPE) |

## 8. Armazenamentos conceituais e responsáveis

| ID | Conteúdo conceitual | Responsabilidade prevista |
|---|---|---|
| D-001 | Tenants, convites e configuração da oficina | Tenant Service |
| D-002 | Identidades, credenciais e vínculos por tenant | Auth Service |
| D-003 | Perfis locais de cliente, veículos e histórico associado | Customer Vehicle Service |
| D-004 | Ordens, diagnósticos, versões de orçamento, decisões e mensagens | Work Order Service |
| D-005 | Fechamentos e pagamentos | Billing Service |
| D-006 | Auditoria e eventos operacionais | Audit Service |
| MQ-001 | Eventos assíncronos | RabbitMQ, conforme decisões de arquitetura |

Os armazenamentos são conceituais. O MER e o documento de arquitetura deverão definir entidades, propriedade e interfaces sem permitir acesso direto ao banco de outro serviço.

## 9. Histórico de revisões

| Versão | Data | Autor | Descrição |
|---|---|---|---|
| 1.0 | 24/09/2026 | Gabriel | Versão inicial do DFD SaaS multi-tenant |
| 1.1 | 09/11/2026 | Gabriel | Atualização proposta para identidade global, convites, acesso do cliente, conversa por ordem e revisão de orçamento; pendente validação do grupo |
| 1.2 | 13/11/2026 | Gabriel | Remoção de estoque interno, descrição de peças manuais no orçamento e restrição de acesso do administrador da plataforma ao conteúdo dos tenants |
| 1.3 | 20/11/2026 | Gabriel | Fluxos do administrador da plataforma: gestão do cadastro das oficinas, sem acesso ao conteúdo operacional |
| 1.4 | 21/11/2026 | Gabriel | Integrações externas limitadas a ViaCEP e FIPE; remoção da cobrança de teste do fluxo financeiro |
| 1.5 | 25/11/2026 | Eloisa (revisão) e Gabriel (validação) | Revisão de consistência: relatórios e auditoria sem leitura direta de armazenamentos de outros serviços; validação de cliente e veículo por API no nível 2; correspondência com os serviços do MVP |

---

**Aprovado por:**

___________________________________________________  
Professor(a) Orientador(a)

Data: ___/___/2026
