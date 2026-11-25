# Documento de Requisitos Funcionais e Não Funcionais

---

**Projeto:** OficinaPro 
**Grupo:** GarageOps  
**Data:** 25/11/2026  
**Versão:** 1.9 (decisões atualizadas pelo grupo; sujeita à validação do orientador)

---

## 1. Introdução

Este documento especifica os requisitos funcionais e não funcionais do **OficinaPro**, uma plataforma **SaaS multi-tenant** para gestão operacional de oficinas mecânicas de pequeno e médio porte.

O sistema será utilizado por múltiplas oficinas na mesma aplicação em nuvem. Cada oficina será tratada como um **tenant**, com dados, usuários, clientes, veículos, ordens de serviço, pagamentos e indicadores isolados logicamente. O isolamento entre tenants é requisito central da solução, pois uma oficina não pode acessar, consultar, alterar ou inferir dados de outra oficina. O MVP não controla saldo nem movimentação de estoque; peças podem ser informadas manualmente como itens do orçamento, pois a oficina poderá obtê-las de autopeças parceiras.

Os requisitos funcionais descrevem os comportamentos que o sistema deve oferecer aos usuários e administradores da plataforma: cadastro de oficina, autenticação, controle de acesso, clientes, veículos, ordens de serviço, diagnóstico, orçamento, financeiro, notificações, relatórios e auditoria.

Os requisitos não funcionais descrevem os atributos de qualidade e as exigências técnicas do projeto: arquitetura SaaS multi-tenant, microsserviços, banco de dados, isolamento de dados, infraestrutura local, cloud, Terraform, CI/CD, testes, segurança, desempenho e observabilidade.

---

## 2. Critério de Priorização

| Prioridade | Critério |
|------------|----------|
| Alta | Essencial para o MVP SaaS, para o fluxo principal da oficina, para isolamento multi-tenant ou para atender requisito obrigatório do Projeto Experimental |
| Média | Importante para valor de produto e evolução da plataforma, mas não bloqueia a primeira demonstração funcional |
| Baixa | Desejável, complementar ou dependente de maturidade posterior do projeto |

---

## 3. Requisitos Funcionais (RF)

Os requisitos funcionais descrevem **o que** o sistema deve fazer do ponto de vista dos usuários da plataforma e das oficinas.

### 3.0 Módulos lógicos e serviços implantados

A coluna **Módulo/Microsserviço** indica o domínio lógico responsável pelo requisito. No MVP, esses domínios estão agrupados em três microsserviços implantáveis de forma independente, cada um com banco PostgreSQL próprio. A separação dos demais domínios está registrada no Documento 07 (ADR-007).

| Domínio lógico | Serviço implantado no MVP | Situação |
|---|---|---|
| Tenant Service e Auth Service | `identity` | Implementado no protótipo |
| Customer Vehicle Service | `customers` (inclui ViaCEP e FIPE) | Implementado no protótipo |
| Work Order Service | `workorders` | Implementado no protótipo |
| Billing Service | `workorders` (pagamentos e saldo da ordem) | Implementado provisoriamente dentro de `workorders`; separação planejada |
| Notification Service, Reporting Service e Audit Service | — | Planejados para o 5º semestre |

Os identificadores RF-020 a RF-024 pertenciam ao controle interno de estoque, retirado do escopo em 13/11/2026 (versão 1.4). Eles não são reutilizados, para preservar a rastreabilidade.

### 3.1 Tenant, Acesso e Identidade

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|----|-----------|-------------------|------------|---------------------|
| RF-001 | O sistema deve permitir que o administrador da plataforma cadastre uma oficina como tenant | O administrador da plataforma informa nome, identificador único e cor de identificação, e o sistema cria um tenant ativo; documento, contato e endereço da oficina são completados pelo administrador da oficina e não aparecem no console da plataforma | Alta | Tenant Service |
| RF-002 | O sistema deve criar um usuário administrador para o tenant | O cadastro inicial da oficina cria ou vincula o administrador da oficina responsável pelo primeiro acesso; se o e-mail informado já tiver identidade GarageOps, o sistema reaproveita essa conta e cria apenas o vínculo; a conta de plataforma não pode receber esse vínculo | Alta | Tenant Service / Auth Service |
| RF-003 | O sistema deve autenticar uma identidade GarageOps e validar os vínculos ativos do usuário com oficinas | Após autenticação, a pessoa só pode selecionar oficinas nas quais possua vínculo ativo; a sessão de trabalho identifica o tenant selecionado e o servidor valida esse vínculo em toda operação | Alta | Auth Service |
| RF-004 | O sistema deve controlar permissões por papel e por tenant | Administrador da oficina, atendente, mecânico, financeiro e cliente recebem ações e áreas de navegação compatíveis com suas responsabilidades; permissões são validadas pelo servidor no tenant ativo e não se estendem a outra oficina | Alta | Auth Service |
| RF-005 | O sistema deve permitir gerenciamento de usuários da oficina | Administrador da oficina pode convidar, listar, atribuir papéis de oficina e inativar vínculos somente no próprio tenant; não pode conceder papel de administrador da plataforma nem administrar usuários ou dados de outras oficinas; remover um vínculo não exclui a identidade global | Alta | Auth Service |
| RF-006 | O sistema deve impedir acesso a tenant sem vínculo ativo e autorização | Usuário não consegue consultar, criar, alterar ou excluir dados de tenant no qual não possua vínculo ativo e permissões compatíveis, mesmo que altere identificadores enviados à API | Alta | Todos os serviços |
| RF-039 | O administrador da plataforma deve gerenciar as oficinas sem acesso aos dados de negócio dos tenants | A conta de administrador da plataforma pode cadastrar, editar, suspender e excluir oficinas e visualizar o cadastro delas (nome, identificador, identidade visual (cor) e situação (ativa ou suspensa)), a saúde global da plataforma e métricas agregadas, como quantidade de contas por oficina; suspender impede o acesso de equipe e clientes sem apagar dados; excluir exige confirmação e remove os dados da oficina em todos os serviços sem exibi-los; não pode consultar, pesquisar, exportar ou alterar clientes, veículos, diagnósticos, ordens, orçamentos, mensagens ou pagamentos, pela interface ou API | Alta | Auth Service / Todos os serviços |
| RF-033 | O sistema deve permitir que uma identidade GarageOps possua vínculos com mais de uma oficina | Um usuário pode aceitar convites de diferentes tenants e alternar entre eles; cada vínculo possui status e papel próprios, e os dados operacionais permanecem isolados por tenant | Alta | Auth Service / Tenant Service |
| RF-034 | O sistema deve permitir convite de cliente para ativação de acesso à plataforma | A oficina envia convite ao número de telefone registrado; o link apenas inicia a ativação e não mostra dados; depois de validar o código enviado ao número convidado, a pessoa vê o nome e o e-mail cadastrados pela oficina, que não podem ser alterados na ativação (o e-mail só é informado quando a oficina não o cadastrou); se o telefone já tiver conta GarageOps, o vínculo é aceito entrando com essa conta, sem criar outra; um perfil já ligado a uma conta não pode ser transferido para outra; a oficina não gera convite para cliente que já tem acesso ao portal dela | Alta | Tenant Service / Auth Service |

### 3.2 Clientes e Veículos

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|----|-----------|-------------------|------------|---------------------|
| RF-007 | O sistema deve permitir cadastro de um perfil de cliente por oficina associado opcionalmente a uma identidade GarageOps | Atendente cadastra cliente vinculado ao tenant atual; após aceitar convite, o perfil local é associado à identidade global verificada; registros de outro tenant não aparecem em consultas da oficina | Alta | Customer Vehicle Service |
| RF-008 | O sistema deve permitir cadastro de veículos vinculados a clientes do tenant | Cada veículo deve estar vinculado a um cliente do mesmo tenant; placa, marca, modelo e ano devem ser registrados | Alta | Customer Vehicle Service |
| RF-009 | O sistema deve consultar endereço por CEP | Ao informar CEP válido, o sistema consulta ViaCEP e preenche endereço; falha na API deve permitir preenchimento manual | Média | Customer Vehicle Service |
| RF-010 | O sistema deve padronizar marca, modelo e ano do veículo com a API FIPE | No cadastro do veículo, a pessoa escolhe marca, modelo e ano em listas obtidas da FIPE e o sistema guarda os nomes e o código FIPE; se a API estiver indisponível ou o veículo não constar, os dados podem ser digitados manualmente | Média | Customer Vehicle Service |
| RF-011 | O sistema deve manter histórico de manutenção por veículo | Usuários autorizados da oficina visualizam o histórico do tenant; o cliente autenticado visualiza somente os veículos e atendimentos associados ao seu perfil nas oficinas às quais possui vínculo ativo, identificando a oficina de origem | Alta | Customer Vehicle Service |
| RF-035 | O sistema deve disponibilizar ao cliente seus veículos e históricos autorizados | Após autenticação, o cliente pode alternar entre oficinas vinculadas e consultar seus próprios veículos, ordens e decisões; cada registro identifica sua oficina de origem e nenhuma oficina recebe acesso aos dados de outra | Alta | Customer Vehicle Service / Auth Service |

### 3.3 Ordem de Serviço

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|----|-----------|-------------------|------------|---------------------|
| RF-012 | O sistema deve permitir abertura de ordem de serviço | Atendente cria ordem vinculada a cliente e veículo do mesmo tenant, com descrição inicial do problema, data de entrada e status inicial "aberta" | Alta | Work Order Service |
| RF-013 | O sistema deve controlar o ciclo de status da ordem de serviço | A ordem deve seguir as transições aberta → em diagnóstico → aguardando aprovação → aprovada → em execução → pronta para retirada → encerrada, além de encerrada por recusa a partir de qualquer estado não encerrado; transições inválidas devem ser bloqueadas. Recusa de uma versão de orçamento mantém a ordem aguardando nova versão ou decisão e não a encerra automaticamente | Alta | Work Order Service |
| RF-014 | O sistema deve permitir registro de diagnóstico técnico | Mecânico registra diagnóstico, observações técnicas e serviços recomendados em uma ordem aberta ou em diagnóstico do mesmo tenant | Alta | Work Order Service |
| RF-015 | O sistema deve permitir criação de orçamento para a ordem de serviço | Orçamento deve conter serviços e itens de peça informados manualmente, com descrição, quantidade, valor unitário, descontos permitidos e valor total calculado; o registro de peça no orçamento não consulta nem altera estoque | Alta | Work Order Service |
| RF-016 | O sistema deve permitir que o cliente aprove ou recuse uma versão do orçamento | O sistema registra decisão, data e hora, versão correspondente e identidade autenticada do responsável. Em caso de recusa, registra o motivo quando informado; a recusa mantém a ordem ativa, no estado aguardando aprovação, para conversa, revisão ou encerramento | Alta | Work Order Service |
| RF-017 | O sistema deve bloquear a execução sem aprovação da versão vigente do orçamento | A ordem não pode entrar em execução nem receber lançamento de serviço ou item executado antes da aprovação da versão vigente; versões recusadas ou substituídas não autorizam execução | Alta | Work Order Service |
| RF-018 | O sistema deve registrar serviços executados conforme a versão aprovada | Mecânico registra serviços realizados, responsável, data e observações somente após aprovação da versão vigente; serviço adicional ou divergente exige nova versão aprovada antes da execução | Alta | Work Order Service |
| RF-019 | O sistema deve concluir tecnicamente a ordem de serviço | Ordem só pode ser marcada como pronta para retirada quando possuir diagnóstico, orçamento aprovado e serviços executados registrados; a conclusão técnica não depende da quitação financeira | Alta | Work Order Service |
| RF-036 | O sistema deve manter conversa entre cliente e oficina vinculada à ordem | Cliente autenticado e funcionários autorizados do mesmo tenant podem enviar mensagens textuais na conversa da ordem; cada mensagem registra autor e data/hora, permanece no histórico e não é exibida a outros tenants | Alta | Work Order Service / Notification Service |
| RF-037 | O sistema deve permitir criação e reenvio de versões revisadas do orçamento | Após conversa ou recusa, usuário autorizado cria nova versão com serviços, peças e valores revisados; versões anteriores e decisões permanecem consultáveis; a nova versão fica aguardando decisão do cliente | Alta | Work Order Service |
| RF-038 | O sistema deve permitir encerramento da ordem por recusa definitiva do serviço | Usuário autorizado encerra a ordem como recusada, registrando responsável, data/hora e justificativa quando informada; a ordem permanece no histórico e não pode gerar execução ou cobrança de serviço não autorizado após o encerramento | Alta | Work Order Service |

### 3.4 Financeiro

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|----|-----------|-------------------|------------|---------------------|
| RF-025 | O sistema deve calcular fechamento financeiro da ordem | Valor final considera serviços executados, itens de peça aprovados e efetivamente cobrados, descontos, acréscimos e pagamentos registrados na ordem do tenant | Alta | Billing Service |
| RF-026 | O sistema deve registrar pagamentos da ordem de serviço | O cliente paga presencialmente na oficina; o usuário financeiro registra forma de pagamento, valor, data, status e observação; pagamento deve estar vinculado a uma ordem existente do mesmo tenant | Alta | Billing Service |
| RF-027 | O sistema deve atualizar status financeiro conforme pagamentos | Ordem deve ficar pendente, parcialmente paga ou paga de acordo com o total aprovado dos serviços e itens manuais de peça cobrados, e os pagamentos registrados; pagamento acima do saldo é recusado; a ordem pronta para retirada só pode ser encerrada quando estiver quitada | Alta | Billing Service |
| RF-028 | Pagamento online por provedor de pagamento (evolução futura) | Fora do escopo atual: o pagamento é feito no local e registrado conforme RF-026; a integração com provedor de pagamento, inclusive em sandbox, só entra com nova decisão do grupo | Baixa | Billing Service |

### 3.5 Gestão, Comunicação e Auditoria

| ID | Descrição | Critério de Aceite | Prioridade | Módulo/Microsserviço |
|----|-----------|-------------------|------------|---------------------|
| RF-029 | O sistema deve notificar eventos operacionais e novas mensagens ao cliente | Cliente autenticado recebe aviso dentro da plataforma sobre orçamento novo ou revisado, decisão registrada, nova mensagem e mudança relevante da ordem | Média | Notification Service |
| RF-030 | O sistema deve apresentar dashboard operacional por oficina | Administrador da oficina visualiza ordens abertas, ordens atrasadas, faturamento do período e serviços mais realizados apenas do próprio tenant | Média | Reporting Service |
| RF-031 | O sistema deve gerar relatórios gerenciais por tenant | Administrador da oficina filtra ordens, pagamentos e faturamento por período, sempre limitado à própria oficina | Média | Reporting Service |
| RF-032 | O sistema deve registrar auditoria de ações críticas | Alterações em orçamento, status da ordem, pagamento e dados de tenant registram usuário, tenant, data, ação e valores relevantes | Média | Audit Service |

---

## 4. Requisitos Não Funcionais (RNF)

Os requisitos não funcionais descrevem **como** o sistema deve ser construído, executado, testado, operado e mantido.

### 4.1 Arquitetura SaaS Multi-Tenant

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-001 | O sistema deve operar como plataforma SaaS multi-tenant | Modelo de uso | Múltiplas oficinas devem utilizar a mesma aplicação com isolamento lógico por tenant; a demonstração deve incluir cinco oficinas distintas, com equipes de tamanhos diferentes, pelo menos um cliente vinculado a duas oficinas e um cliente vinculado às cinco |
| RNF-002 | Toda entidade operacional deve estar associada ao tenant proprietário; identidade global é uma exceção controlada | Presença de `tenant_id` | Clientes locais, veículos, ordens, mensagens, pagamentos e relatórios devem possuir vínculo com tenant; identidade global e vínculos são tratados separadamente |
| RNF-003 | O tenant ativo deve ser definido a partir de vínculo autenticado | Origem e validação do contexto | A pessoa pode selecionar somente tenant com vínculo ativo; o servidor valida identidade, vínculo e papel em cada operação e não confia em `tenant_id` livre enviado pelo cliente |
| RNF-004 | Consultas devem ser filtradas por tenant no servidor | Isolamento de dados | Nenhuma consulta de dados de negócio deve retornar registros de outro tenant |
| RNF-005 | O sistema deve possuir testes automatizados de isolamento multi-tenant | Casos de teste | Testes devem provar que usuário de uma oficina não acessa dados de outra |

### 4.2 Microsserviços e Comunicação

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-006 | O sistema deve ser estruturado em microsserviços executáveis e implantáveis independentemente | Unidade de execução e deploy | Cada microsserviço definido deve poder ser iniciado e implantado sem exigir a implantação conjunta dos demais, e deve comunicar-se com outros serviços por interfaces versionadas e documentadas, como APIs ou eventos; acesso direto ao banco de outro serviço não é permitido |
| RNF-007 | Cada microsserviço deve possuir responsabilidade bem definida | Coesão do serviço | Um serviço não deve concentrar regras de domínios não relacionados |
| RNF-008 | A comunicação assíncrona deve ser usada para eventos entre domínios | Uso de mensageria | Eventos como notificação, decisão de orçamento ou atualização financeira devem poder trafegar via RabbitMQ quando aplicável |

### 4.3 Banco de Dados e Consistência

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-009 | O banco principal deve ser PostgreSQL | Tecnologia definida | PostgreSQL deve ser usado para o núcleo transacional e multi-tenant do sistema |
| RNF-010 | O banco deve garantir integridade referencial | Chaves estrangeiras e constraints | Tenant, usuário, cliente, veículo, ordem, orçamento e pagamento não devem ficar sem vínculo obrigatório |
| RNF-011 | Operações críticas devem preservar consistência transacional | Transações ACID | Aprovação de orçamento, fechamento financeiro e registro de pagamento devem evitar estados parciais |
| RNF-012 | As tabelas multi-tenant devem possuir índices adequados | Índices por tenant | Consultas frequentes devem considerar `tenant_id` em filtros e índices |
| RNF-013 | Scripts ou migrations de banco devem ser versionados | Controle de versão | Alterações estruturais do banco devem estar registradas no repositório do sistema |

### 4.4 Infraestrutura Local

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-014 | O ambiente local deve ser padronizado com Docker Compose | Execução local | Integrante do grupo deve conseguir subir dependências locais com um comando documentado |
| RNF-015 | O ambiente local deve incluir PostgreSQL | Container disponível | Banco local deve iniciar com nome, usuário, senha e porta definidos por `.env` |
| RNF-016 | O ambiente local deve incluir RabbitMQ | Container disponível | Fila local e interface de gerenciamento devem estar disponíveis para desenvolvimento |
| RNF-017 | O repositório do sistema deve possuir `.env.example` | Arquivo de referência | Variáveis obrigatórias devem estar documentadas sem expor senhas reais |
| RNF-018 | O sistema deve possuir documentação de setup local | README técnico | Um novo integrante deve conseguir configurar o ambiente seguindo o README do repositório do sistema |

### 4.5 Cloud e Infraestrutura como Código

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-019 | O sistema deve ser implantado em provedor de nuvem aceito pelo Projeto Experimental | Ambiente AWS, GCP ou Azure | Ambiente de demonstração deve estar acessível em nuvem no período da banca |
| RNF-020 | A infraestrutura deve ser provisionada com Terraform | IaC versionada | Recursos de nuvem necessários ao deploy devem ser descritos em código |
| RNF-021 | A criação manual de recursos deve ser evitada | Reprodutibilidade | Ambiente deve poder ser recriado a partir dos arquivos de infraestrutura |
| RNF-022 | A arquitetura de cloud deve suportar múltiplos tenants | Ambiente compartilhado | A infraestrutura deve hospedar a plataforma sem exigir uma instalação separada por oficina |
| RNF-023 | O projeto deve manter alternativa local de contingência | Docker Compose | Em caso de falha de conectividade, serviços principais devem poder ser demonstrados localmente |

### 4.6 CI/CD

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-024 | O projeto deve possuir pipeline de CI com GitHub Actions | Pipeline automatizado | Pull Requests devem executar instalação, build e testes |
| RNF-025 | O pipeline deve impedir integração de código quebrado | Status checks | Build ou testes com falha devem bloquear merge na branch principal |
| RNF-026 | O deploy deve ser automatizado | Pipeline de CD | Deploy em ambiente de demonstração deve ser executado por workflow versionado |
| RNF-027 | O pipeline deve validar regras críticas de multi-tenancy | Testes em CI | Testes de isolamento por tenant devem rodar no pipeline |

### 4.7 Testes e Qualidade

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-028 | O projeto deve possuir testes automatizados com cobertura global mínima | Cobertura do código-fonte total | Cobertura mínima de 60%, calculada globalmente sobre o código-fonte de todos os microsserviços; o relatório de cobertura deve ser gerado e o limite validado automaticamente no pipeline de CI |
| RNF-029 | Regras críticas devem possuir testes específicos | Casos de teste | Aprovação e recusa de orçamento, bloqueio de execução sem aprovação, fechamento financeiro e isolamento por tenant devem ser testados |
| RNF-030 | APIs devem possuir documentação técnica | OpenAPI/Swagger ou equivalente | Endpoints principais devem estar documentados |
| RNF-031 | O código deve seguir padrão de formatação e lint | Execução no pipeline | Código fora do padrão deve ser identificado automaticamente |

### 4.8 Segurança

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-032 | Senhas devem ser armazenadas com hash seguro | Algoritmo de hash | Uso de bcrypt, Argon2 ou equivalente |
| RNF-033 | Tokens de autenticação devem expirar | Tempo de expiração | JWT deve expirar em no máximo 8 horas |
| RNF-034 | Rotas protegidas devem validar autenticação, vínculo, papel e tenant ativo | Controle de acesso | 100% das rotas restritas devem validar identidade autenticada, vínculo ativo e permissões correspondentes à oficina selecionada |
| RNF-035 | Dados sensíveis não devem ser expostos em logs ou respostas | Revisão de payloads e logs | Senhas, tokens, segredos e dados de outro tenant não devem aparecer em respostas ou logs |
| RNF-036 | A aplicação deve evitar escalação horizontal de privilégios entre tenants | Teste de autorização | Usuário autenticado não deve conseguir alterar identificadores para acessar dados de outra oficina |
| RNF-046 | Convites de acesso não podem conceder acesso apenas por possuir o link | Validação e expiração | Link de convite não exibe dados nem autentica; é de uso único, expira conforme prazo configurado e exige autenticação e validação de código enviado ao contato convidado; tentativas são limitadas e envio, aceite, expiração e revogação são registrados |
| RNF-047 | A identidade global deve manter autorização delimitada por tenant | Teste de autorização | Toda operação valida vínculo ativo, papel e tenant no servidor; alternar o tenant ativo não permite consultar dados de outro tenant sem vínculo |
| RNF-048 | Conversas e históricos devem respeitar autorização e rastreabilidade | Controle de acesso | Mensagens e ordens só podem ser consultadas por cliente associado e usuários autorizados do tenant proprietário; alterações de orçamento e encerramento permanecem auditáveis |
| RNF-049 | O papel de administrador da plataforma não deve conceder acesso a dados de negócio dos tenants | Teste negativo de autorização | Tentativas pela interface e API de consultar, pesquisar, exportar ou alterar conteúdo de clientes, veículos, diagnósticos, ordens, orçamentos, valores, mensagens e pagamentos devem ser negadas; console limita-se ao cadastro das oficinas (nome, identificador, identidade visual (cor) e situação (ativa ou suspensa)), saúde global e métricas agregadas |

### 4.9 Observabilidade

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-037 | Serviços devem expor health check | Endpoint de saúde | Cada serviço principal deve indicar status operacional |
| RNF-038 | Serviços devem expor métricas para monitoramento | Endpoint de métricas | Métricas de requisição, erro e tempo de resposta devem ser coletáveis |
| RNF-039 | O ambiente deve possuir Prometheus e Grafana | Stack de monitoramento | Métricas devem estar visíveis em dashboards durante a demonstração final |
| RNF-040 | Eventos críticos devem gerar logs estruturados | Logs por evento | Erros de pagamento, falhas de integração e bloqueios de acesso entre tenants devem ser rastreáveis |
| RNF-041 | Logs devem identificar tenant sem expor dados sensíveis | Correlação operacional | Logs técnicos devem permitir investigação por tenant sem revelar informações sigilosas |

### 4.10 Desempenho e Disponibilidade

| ID | Descrição | Métrica | Meta |
|----|-----------|---------|------|
| RNF-042 | APIs principais devem responder em tempo adequado para uso operacional | Latência P95 | Até 800 ms em ambiente de demonstração |
| RNF-043 | O dashboard deve carregar em tempo aceitável | Tempo de carregamento | Até 3 segundos para indicadores principais do tenant |
| RNF-044 | Falhas em APIs externas devem ser tratadas sem derrubar o fluxo principal | Tratamento de erro | Sistema deve apresentar mensagem controlada e permitir continuidade manual quando possível |
| RNF-045 | O ambiente de demonstração deve manter disponibilidade adequada | Uptime | Mínimo de 99% no período final de acompanhamento |

---

## 5. Rastreabilidade

Matriz de rastreabilidade entre requisitos funcionais e não funcionais:

| RF | RNFs Relacionados |
|----|-------------------|
| RF-001 | RNF-001, RNF-002, RNF-009, RNF-010 |
| RF-002 | RNF-001, RNF-003, RNF-032, RNF-034 |
| RF-003 | RNF-003, RNF-032, RNF-033, RNF-034, RNF-035 |
| RF-004 | RNF-003, RNF-034, RNF-036 |
| RF-005 | RNF-003, RNF-030, RNF-034, RNF-047 |
| RF-006 | RNF-001, RNF-002, RNF-004, RNF-005, RNF-027, RNF-036, RNF-047 |
| RF-039 | RNF-034, RNF-035, RNF-049 |
| RF-007 | RNF-002, RNF-004, RNF-010, RNF-030, RNF-042 |
| RF-008 | RNF-002, RNF-004, RNF-010, RNF-030, RNF-042 |
| RF-009 | RNF-044 |
| RF-010 | RNF-044 |
| RF-011 | RNF-002, RNF-004, RNF-010, RNF-042 |
| RF-012 | RNF-002, RNF-004, RNF-010, RNF-028, RNF-029 |
| RF-013 | RNF-010, RNF-011, RNF-028, RNF-029 |
| RF-014 | RNF-002, RNF-004, RNF-010 |
| RF-015 | RNF-010, RNF-011, RNF-028, RNF-029 |
| RF-016 | RNF-010, RNF-011, RNF-028, RNF-029, RNF-034 |
| RF-017 | RNF-011, RNF-028, RNF-029, RNF-034, RNF-036 |
| RF-018 | RNF-010, RNF-011, RNF-028, RNF-029 |
| RF-019 | RNF-011, RNF-028, RNF-029 |
| RF-025 | RNF-010, RNF-011, RNF-028, RNF-029 |
| RF-026 | RNF-010, RNF-034, RNF-035 |
| RF-027 | RNF-010, RNF-011, RNF-028, RNF-029 |
| RF-028 | RNF-044 |
| RF-029 | RNF-008, RNF-038, RNF-040 |
| RF-030 | RNF-004, RNF-038, RNF-039, RNF-043 |
| RF-031 | RNF-004, RNF-030, RNF-042 |
| RF-032 | RNF-035, RNF-040, RNF-041 |
| RF-033 | RNF-001, RNF-003, RNF-004, RNF-005, RNF-047 |
| RF-034 | RNF-032, RNF-033, RNF-034, RNF-035, RNF-046 |
| RF-035 | RNF-002, RNF-003, RNF-004, RNF-005, RNF-047 |
| RF-036 | RNF-004, RNF-035, RNF-040, RNF-048 |
| RF-037 | RNF-010, RNF-011, RNF-034, RNF-048 |
| RF-038 | RNF-010, RNF-011, RNF-034, RNF-040, RNF-048 |

---

## 5.1 Decisões de escopo pendentes de validação

Os itens abaixo ainda não devem ser tratados como requisitos aprovados. Devem ser discutidos pelo grupo e alinhados com o orientador antes de detalhar telas, permissões ou arquitetura:

| Tema | Questão pendente |
|---|---|
| Acúmulo de papéis | Se uma pessoa pode exercer mais de um papel na mesma oficina e como o sistema apresentará a troca de contexto |
| Provedor de envio | Canal e provedor para o código de convite e para notificações fora da plataforma (SMS, e-mail ou WhatsApp); no MVP o código é simulado no ambiente local |
| Separação dos domínios restantes | Momento de separar Billing, Notification, Reporting e Audit em serviços próprios e quais fluxos usarão RabbitMQ (proposta no Documento 07, ADR-003 e ADR-007) |

Itens resolvidos desde a versão 1.5: as APIs externas são ViaCEP e FIPE (RF-009 e RF-010); a composição das cinco oficinas de demonstração está definida no README do repositório do sistema; o MVP implanta três serviços com banco próprio por serviço (seção 3.0).

Estas pendências não alteram o escopo já definido de não implementar controle interno de estoque. Itens de peça podem ser inseridos manualmente no orçamento, sem catálogo, saldo, movimentação ou alerta de reposição.

## 6. Glossário

| Termo | Definição |
|-------|-----------|
| API externa | Serviço de terceiros consumido pelo sistema, como ViaCEP e FIPE |
| Auditoria | Registro de ações relevantes realizadas no sistema, incluindo usuário, tenant, data e dados alterados |
| CI/CD | Integração e entrega contínuas para automatizar build, testes e deploy |
| Docker Compose | Ferramenta para executar localmente múltiplos serviços em containers |
| Fechamento financeiro | Consolidação de serviços, itens manuais de peça cobrados, descontos e pagamentos de uma ordem de serviço |
| Health check | Verificação técnica que indica se um serviço está operacional |
| Infraestrutura como Código | Prática de declarar recursos de infraestrutura em arquivos versionados, como Terraform |
| Mensageria | Comunicação assíncrona entre serviços por meio de filas ou eventos |
| Microsserviço | Serviço independente responsável por um domínio específico do sistema |
| Multi-tenant | Modelo em que múltiplos clientes utilizam a mesma aplicação com isolamento lógico de dados |
| MVP | Produto mínimo viável que demonstra o fluxo principal e o valor essencial do sistema |
| Observabilidade | Capacidade de acompanhar saúde, erros, métricas e comportamento do sistema |
| Ordem de Serviço | Registro central do atendimento de um veículo, desde abertura até fechamento |
| Orçamento | Proposta versionada de serviços e itens de peça informados manualmente, associada a uma ordem de serviço |
| RabbitMQ | Ferramenta de mensageria planejada para comunicação assíncrona entre serviços |
| SaaS | Software como serviço, acessado pela internet sem instalação própria pelo cliente |
| Sandbox | Ambiente de testes de uma API externa, sem efeito real em produção |
| Tenant | Unidade lógica de cliente dentro da plataforma; no OficinaPro, representa uma oficina |
| Identidade GarageOps | Conta global de uma pessoa, que pode possuir vínculos autorizados com mais de um tenant |
| Vínculo | Associação entre uma identidade e um tenant, com papel e estado de acesso próprios |
| Versão de orçamento | Registro imutável de uma proposta enviada ao cliente; revisões geram novas versões e preservam as anteriores |
| Terraform | Ferramenta usada para provisionar infraestrutura como código |

---

## 7. Histórico de Revisões

| Versão | Data | Autor | Descrição da Alteração |
|--------|------|-------|------------------------|
| 1.0 | 17/09/2026 | Gabriel | Versão inicial dos requisitos funcionais e não funcionais |
| 1.1 | 21/09/2026 | Gabriel | Revisão profissional de prioridades, critérios de aceite, infraestrutura, qualidade, observabilidade e rastreabilidade |
| 1.2 | 24/09/2026 | Gabriel | Especificação dos requisitos SaaS multi-tenant, incluindo tenant, isolamento de dados, segurança e infraestrutura associada |
| 1.3 | 09/11/2026 | Gabriel | Proposta de identidade global com vínculos por tenant, convite validado, portal do cliente, conversa por ordem e revisão versionada de orçamento; pendente validação do grupo |
| 1.4 | 13/11/2026 | Gabriel | Revisão do escopo para remover controle interno de estoque; peças permanecem como itens manuais do orçamento; inclusão de decisões de produto pendentes de validação |
| 1.5 | 13/11/2026 | Gabriel | Definição das responsabilidades dos papéis e restrição explícita de acesso do administrador da plataforma ao conteúdo operacional dos tenants |
| 1.6 | 20/11/2026 | Gabriel | RF-001, RF-002, RF-039 e RNF-049: administrador da plataforma cadastra, edita, suspende e exclui oficinas e conhece o cadastro delas; o conteúdo operacional continua inacessível |
| 1.7 | 21/11/2026 | Gabriel | RF-010 detalha o uso da FIPE; RF-026 registra pagamento no local; RF-028 (pagamento online) passa a evolução futura |
| 1.8 | 22/11/2026 | Gabriel | RF-034: código antes de mostrar dados, nome e e-mail vindos do cadastro da oficina, sem conta duplicada por telefone e sem transferência de perfil já vinculado |
| 1.9 | 25/11/2026 | GarageOps | Revisão de consistência: seção 3.0 com domínios lógicos e serviços do MVP; nota sobre RF-020 a RF-024; estados da ordem em RF-013 alinhados ao fluxo aprovado; conclusão técnica independente da quitação (RF-019, RF-027); papel de administrador da oficina em RF-030 e RF-031; atualização das pendências |

---

**Aprovado por:**

___________________________________________________  
Professor(a) Orientador(a)

Data: ___/___/2026
