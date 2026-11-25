# Dúvidas para o orientador e decisões pendentes — OficinaPro

**Grupo:** GarageOps
**Atualizado em:** 25/11/2026

Este documento separa três coisas: o que já foi respondido pelos documentos oficiais do curso, o que o próprio grupo precisa decidir e as poucas perguntas que realmente precisam ser levadas ao orientador. A versão anterior (revisada em 16/11/2026, commit `2736319`) foi escrita para o modelo com estoque e listava perguntas que os documentos oficiais já respondem; ela continua no histórico do Git.

---

## 1. Já respondido pelos documentos oficiais

Fontes: `aceite-projeto-experimental.md`, `termo-aceite-banca-5semestre.md` e `README.md` do repositório oficial.

| Tema | Resposta oficial |
|---|---|
| Deploy na nuvem é obrigatório? | Sim. O sistema deve estar rodando em AWS, GCP ou Azure na defesa. Docker Compose só é aceito como contingência em caso de falha de internet, sem penalidade |
| Terraform é obrigatório? | Sim. Criar recursos de forma exclusivamente manual gera penalidade |
| Como é medida a cobertura de 60%? | Sobre o código-fonte total, de forma global, considerando todos os microsserviços. Testes unitários ou de integração |
| Prometheus e Grafana são obrigatórios? | Sim, funcionais e acessíveis na defesa |
| Quais documentos entregar? | Os 8 documentos dos templates, mais o README do grupo |
| O que se entrega em cada semestre? | 4º: os 8 documentos e um MVP funcional com integração inicial dos microsserviços. 5º: sistema completo em nuvem, testes, monitoramento e banca |
| Como abrir as PRs? | Uma PR por entrega, alterando apenas a pasta do grupo, com título `[Nome do Grupo] - Entrega N - Descrição` |
| Quantos encontros no 5º semestre? | No mínimo 4, com intervalo máximo de 5 semanas entre eles |

## 2. Decisões internas do grupo (não são perguntas ao orientador)

| Tema | Situação | Onde está registrado |
|---|---|---|
| Uma pessoa pode ter mais de um papel na mesma oficina? | Pendente | Documento 02, seção 5.1; ADR-009 |
| Provedor de envio do código de convite (SMS, e-mail ou WhatsApp) | Pendente; simulado no MVP | Documento 02, seção 5.1 |
| Quando separar Billing, Notification, Audit e Reporting | Proposta no ADR-007; ordem a confirmar | Documento 07, ADR-007; Documento 08 |
| Lista de eventos do RabbitMQ e broker na AWS (Amazon MQ ou contêiner) | Proposta | Documento 07, ADR-003; Documento 05, seção 7 |
| TypeScript no frontend | Proposta | Documento 07, ADR-010 |
| Região AWS e domínio | Proposta: us-east-1; domínio a definir | Documento 05, seção 7 |
| Identificador de login, recuperação de conta e troca de telefone | Pendente | Documento 04, seção 8 |
| Edição e exclusão de mensagens; retenção dos registros | Pendente | Documento 04, seção 8 |
| Metodologia, duração da sprint e ferramenta de quadro | Proposta: Scrum adaptado, 2 semanas, GitHub Projects | Documento 08 |

Decisões já tomadas pelo grupo e que não devem voltar à pauta: estoque fora do MVP (13/11/2026); administrador da plataforma sem acesso aos dados das oficinas (13/11 e 20/11/2026); ViaCEP e FIPE como APIs externas e pagamento presencial (21/11/2026); MVP em NestJS + TypeScript com três serviços e papel `tenant_admin` (22/11/2026).

## 3. Perguntas para o orientador

1. **Estrutura da pasta.** O README oficial pede a pasta do grupo na raiz do repositório, mas os grupos, inclusive o GarageOps, usam `Projetos/<nome>/`. Podemos manter `Projetos/OficinaPro/`?
2. **Entrega dos documentos 05 a 08.** Podem ir em uma única PR ou deve ser uma PR por documento, como no cronograma sugerido?
3. **Conta AWS.** Há conta acadêmica ou créditos disponíveis para o 5º semestre? Se for AWS Academy, ela permite criar papéis IAM, OIDC com o GitHub Actions e Amazon MQ?
4. **Calendário.** Quais são as datas de entrega do 4º semestre e o calendário do 5º, para fecharmos os períodos das sprints no Documento 08?

## 4. Registro das respostas

| Data | Pergunta | Orientação recebida | Documentos afetados |
|---|---|---|---|
| | | | |
