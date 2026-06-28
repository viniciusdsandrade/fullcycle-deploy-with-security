# Secure Multi-Tenant API DevSecOps LGPD Cloud Lab

## Visao geral

Este documento consolida o apanhado do projeto **Secure Multi-Tenant API DevSecOps LGPD Cloud Lab**, uma PoC tecnica publica, reproduzivel e auditavel para demonstrar uma API backend multi-tenant com seguranca aplicada, privacidade por desenho, qualidade automatizada, arquitetura cloud de referencia em AWS e protecao de borda com Cloudflare.

O projeto simula uma plataforma B2B de gestao e revisao documental. Empresas diferentes usam a mesma aplicacao, mas seus dados precisam permanecer isolados por tenant. O sistema manipula documentos, revisoes, usuarios, eventos de auditoria e solicitacoes de titulares de dados inspiradas na LGPD.

Este projeto nao declara conformidade legal completa com a LGPD. A proposta e traduzir principios de privacidade, seguranca e governanca em controles tecnicos, documentacao, testes e evidencias.

## Objetivo da PoC

A PoC foi pensada para demonstrar dominio de backend Java/Kotlin em um contexto mais proximo de producao, combinando:

- isolamento entre tenants;
- autenticacao JWT;
- autorizacao por roles;
- prevencao contra IDOR;
- auditoria append-only;
- tratamento seguro de erros;
- minimizacao de dados pessoais;
- politicas simuladas de retencao e anonimizacao;
- atendimento a solicitacoes de titulares;
- resposta a incidente simulado;
- CI/CD com gates de seguranca;
- arquitetura AWS segura como referencia;
- protecao de API na borda com Cloudflare.

O foco nao e criar um CRUD generico, mas provar que seguranca, privacidade, cloud e CI/CD foram tratados como controles verificaveis no codigo, nos testes, nos pipelines, na infraestrutura e na documentacao.

## Pitch curto

### Portugues

Esta PoC demonstra uma API backend multi-tenant com seguranca aplicada, controles de privacidade inspirados na LGPD, quality gates automatizados, arquitetura cloud de referencia em AWS e protecao de borda com Cloudflare. O foco e isolamento de tenant, autorizacao por perfil, testes negativos de seguranca, auditoria, tratamento seguro de erros, protecao de dados pessoais, CI/CD seguro e mitigacao de abuso em APIs.

### Ingles

This project demonstrates a secure multi-tenant backend API with privacy-by-design controls, DevSecOps quality gates, AWS cloud security reference architecture, and Cloudflare edge protection. It focuses on tenant isolation, role-based access control, LGPD-inspired privacy controls, auditability, secure error handling, CI security checks, infrastructure hardening, and API abuse prevention.

## Perfil tecnico demonstrado

- Backend Java/Kotlin pleno forte.
- Modelagem de dominio e regras de negocio.
- APIs REST seguras.
- Persistencia com PostgreSQL e migrations versionadas.
- Testes unitarios, integracao e negativos de seguranca.
- Uso de Testcontainers para ambiente reproduzivel.
- Documentacao orientada a risco e evidencia.
- DevSecOps com GitHub Actions, SAST, dependency review, container scan e DAST.
- Referencia de arquitetura segura em AWS.
- Protecao de API na borda com Cloudflare.

## Stack principal

### Aplicacao

- Java 21 ou Kotlin.
- Spring Boot.
- Spring Security Resource Server.
- Spring Validation.
- Spring Data JPA ou JDBC.
- PostgreSQL.
- Flyway.
- Testcontainers.
- JUnit 5.
- RestAssured ou WebTestClient.
- ArchUnit.
- OpenAPI.
- Docker.
- Docker Compose.

### Seguranca e qualidade

- GitHub Actions.
- CodeQL.
- GitHub Dependency Review.
- Trivy.
- OWASP ZAP Baseline.
- JaCoCo.
- ArchUnit.
- OWASP ASVS como referencia parcial.

### AWS como referencia cloud

- Amazon API Gateway ou ALB.
- Amazon ECS Fargate ou AWS App Runner.
- Amazon RDS PostgreSQL.
- AWS Secrets Manager.
- AWS KMS.
- Amazon S3.
- AWS CloudTrail.
- Amazon CloudWatch.
- Amazon GuardDuty.
- AWS Security Hub.
- AWS WAF opcional.
- IAM Access Analyzer.

### Cloudflare como borda de API

- Cloudflare DNS.
- Cloudflare WAF.
- Cloudflare Rate Limiting Rules.
- Cloudflare API Shield.
- Cloudflare mTLS.
- Cloudflare Schema Validation.
- Protecoes contra abuso e automacao quando aplicavel.

## Modos de execucao

### Modo local obrigatorio

O projeto deve rodar localmente sem depender de servico pago. O ambiente local previsto usa:

- Docker Compose.
- PostgreSQL local.
- JWT fake/local para desenvolvimento.
- Testcontainers para testes de integracao.
- GitHub Actions para validacao automatizada.
- ZAP Baseline, Trivy e CodeQL como gates de seguranca.

### Modo cloud de referencia

AWS e Cloudflare entram como arquitetura de referencia e, quando possivel, como IaC. Nao e obrigatorio provisionar recursos pagos para validar a PoC, mas o repositorio deve mostrar como a arquitetura seria feita de forma segura.

## Arquitetura de referencia

```text
Client
  |
  v
Cloudflare
  - DNS
  - WAF
  - Rate Limiting
  - API Shield Schema Validation
  - mTLS para integracoes maquina-maquina
  |
  v
AWS Edge / Ingress
  - API Gateway ou ALB
  - TLS
  - Request limits
  |
  v
Backend API
  - Spring Boot
  - Spring Security
  - Tenant isolation
  - Validation
  - Audit logging
  |
  v
PostgreSQL / RDS
  - Dados transacionais
  - Dados de auditoria
  - Solicitacoes LGPD
  |
  v
AWS Security / Observability
  - CloudTrail
  - CloudWatch
  - GuardDuty
  - Security Hub
  - KMS
  - Secrets Manager
```

## Modelo de dominio

### Tenant

Representa uma empresa cliente da plataforma.

Campos principais:

- `id`
- `name`
- `status`
- `createdAt`

### UserAccount

Representa um usuario autenticavel dentro de um tenant.

Campos principais:

- `id`
- `tenantId`
- `email`
- `displayName`
- `role`
- `status`
- `createdAt`
- `updatedAt`

### Document

Representa um documento em fluxo de revisao.

Campos principais:

- `id`
- `tenantId`
- `ownerId`
- `title`
- `description`
- `status`
- `classification`
- `createdAt`
- `updatedAt`

### DocumentReview

Representa uma decisao de revisao sobre um documento.

Campos principais:

- `id`
- `tenantId`
- `documentId`
- `reviewerId`
- `decision`
- `reason`
- `createdAt`

### AuditEvent

Representa a trilha de auditoria da aplicacao.

Campos principais:

- `id`
- `tenantId`
- `actorUserId`
- `action`
- `resourceType`
- `resourceId`
- `outcome`
- `ipHash`
- `userAgentHash`
- `createdAt`

### PrivacyRequest

Representa uma solicitacao de titular de dados.

Campos principais:

- `id`
- `tenantId`
- `requesterUserId`
- `requestType`
- `status`
- `legalBasis`
- `decisionReason`
- `createdAt`
- `completedAt`

### DataProcessingRecord

Representa o registro de operacoes de tratamento de dados.

Campos principais:

- `id`
- `processingActivity`
- `dataCategory`
- `purpose`
- `legalBasis`
- `retentionPeriod`
- `securityControls`
- `createdAt`

## Classificacao de dados

| Campo | Categoria | Observacao |
|---|---|---|
| `email` | dado pessoal | Identifica ou pode identificar usuario |
| `displayName` | dado pessoal | Pode identificar pessoa natural |
| `document.title` | potencial dado pessoal | Pode conter informacao pessoal se mal usado |
| `document.description` | potencial dado pessoal | Deve ter limite e validacao |
| `tenantId` | dado organizacional | Nao e necessariamente pessoal isoladamente |
| `ipHash` | dado pessoal pseudonimizado | IP pode identificar ou tornar identificavel |
| `userAgentHash` | dado tecnico pseudonimizado | Usado para auditoria com minimizacao |
| `auditEvent` | registro sensivel operacional | Deve ter retencao e controle de acesso |

## Modulos funcionais

### 1. Documentos

O modulo de documentos cobre o fluxo operacional principal da plataforma:

- criacao de documentos;
- listagem conforme role e tenant;
- visualizacao segura;
- submissao para revisao;
- aprovacao;
- rejeicao com motivo obrigatorio;
- transicoes de status auditadas.

Regras centrais:

- documento nasce como `DRAFT`;
- `tenantId` e `ownerId` vem do token, nao do payload;
- owner so visualiza e submete documentos autorizados;
- reviewer so atua em documentos pendentes do proprio tenant;
- documento de outro tenant nao deve aparecer em listagem nem vazar por detalhe.

### 2. Usuarios e tenants

O modulo de usuarios e tenants organiza a separacao logica da plataforma:

- usuarios pertencem a um tenant;
- cada usuario possui role;
- todas as queries sensiveis filtram por tenant;
- payloads nao podem trocar o tenant real;
- dados de tenant A nunca devem ser retornados para tenant B.

### 3. Auditoria

O modulo de auditoria registra operacoes sensiveis e tentativas proibidas.

Eventos minimos:

- documento criado;
- documento submetido;
- documento aprovado;
- documento rejeitado;
- tentativa proibida de acesso;
- solicitacao LGPD criada;
- solicitacao LGPD aprovada;
- solicitacao LGPD rejeitada;
- simulacao de incidente;
- exportacao de dados pessoais.

Regras centrais:

- auditoria deve ser append-only;
- eventos de outro tenant nunca aparecem;
- auditoria nao deve armazenar token, senha, segredo ou payload sensivel completo;
- IP e User-Agent devem ser armazenados como hash quando aplicavel.

### 4. Privacidade e LGPD

O modulo de privacidade demonstra controles inspirados na LGPD:

- inventario de dados pessoais;
- classificacao de dados;
- finalidade por dado tratado;
- solicitacao de acesso;
- solicitacao de correcao;
- solicitacao de exclusao ou anonimizacao;
- politica de retencao;
- registro de base legal simulada;
- RIPD-lite;
- relatorio de incidente simulado.

Regras centrais:

- solicitacao de acesso nasce como `OPEN`;
- correcao deve restringir campos permitidos;
- exclusao nao e automatica;
- retencao pode impedir exclusao destrutiva;
- auditoria nao e apagada destrutivamente;
- anonimizacao remove dados pessoais diretos quando aplicavel;
- toda decisao relevante deve gerar auditoria.

### 5. DevSecOps

O modulo de DevSecOps define gates automatizados para aumentar confianca:

- build e testes com Gradle;
- testes negativos de seguranca;
- CodeQL para SAST;
- GitHub Dependency Review;
- Trivy para imagem Docker;
- OWASP ZAP Baseline para DAST;
- IaC scan opcional para `infra/aws` e `infra/cloudflare`;
- artifacts publicos de evidencia.

### 6. AWS

A camada AWS e uma arquitetura de referencia para operacao segura:

- IAM least privilege;
- Secrets Manager para segredos;
- KMS para criptografia;
- RDS PostgreSQL privado;
- CloudTrail para auditoria de conta;
- CloudWatch para logs, metricas e alarmes;
- GuardDuty para deteccao gerenciada;
- Security Hub para postura de seguranca;
- S3 seguro quando houver arquivos.

### 7. Cloudflare

A camada Cloudflare protege a API antes da origem:

- WAF para padroes obvios de ataque;
- bloqueio de metodos HTTP inesperados;
- rate limiting por endpoint;
- API Shield Schema Validation com OpenAPI;
- mTLS para integracoes maquina-maquina;
- estrategia para evitar bypass da origem;
- politica de logs de borda com minimizacao.

## Perfis de usuario

### DOCUMENT_OWNER

Pode:

- criar documento no proprio tenant;
- listar os proprios documentos;
- visualizar documento proprio;
- submeter documento para revisao;
- abrir solicitacao de privacidade sobre seus proprios dados.

Nao pode:

- revisar documento;
- acessar documento de outro tenant;
- alterar status de aprovacao diretamente;
- consultar auditoria global.

### REVIEWER

Pode:

- listar documentos pendentes no proprio tenant;
- aprovar documento do proprio tenant;
- rejeitar documento do proprio tenant;
- consultar historico de revisao dos documentos sob sua responsabilidade.

Nao pode:

- acessar documento de outro tenant;
- revisar documento finalizado;
- aprovar documento proprio;
- consultar solicitacoes LGPD de outro usuario sem permissao.

### TENANT_ADMIN

Pode:

- listar documentos do proprio tenant;
- visualizar auditoria do proprio tenant;
- visualizar solicitacoes LGPD do proprio tenant;
- responder solicitacoes LGPD simuladas;
- exportar relatorio de privacidade do tenant.

Nao pode:

- acessar dados de outro tenant;
- alterar eventos de auditoria;
- executar acao global;
- alterar tenant de usuario por payload.

### PRIVACY_OFFICER

Perfil opcional, mas recomendado.

Pode:

- consultar solicitacoes de titulares;
- registrar decisao sobre exclusao, correcao ou anonimizacao;
- gerar relatorio RIPD-lite;
- consultar inventario de tratamento;
- registrar avaliacao de incidente.

Nao pode:

- acessar documento de outro tenant sem justificativa;
- apagar auditoria;
- alterar historico de decisao.

## Endpoints previstos

### Documentos

```http
POST /api/documents
GET /api/documents
GET /api/documents/{documentId}
POST /api/documents/{documentId}/submit
POST /api/documents/{documentId}/reviews/approve
POST /api/documents/{documentId}/reviews/reject
```

### Auditoria

```http
GET /api/audit-events
GET /api/audit-events/{auditEventId}
```

### Privacidade e LGPD

```http
GET /api/privacy/me
POST /api/privacy/requests/access
POST /api/privacy/requests/correction
POST /api/privacy/requests/deletion
GET /api/privacy/requests
GET /api/privacy/requests/{requestId}
POST /api/privacy/requests/{requestId}/approve
POST /api/privacy/requests/{requestId}/reject
GET /api/privacy/data-processing-records
GET /api/privacy/ripd-lite
POST /api/privacy/incidents/simulate
GET /api/privacy/incidents/{incidentId}/report
```

### Operacao

```http
GET /actuator/health
GET /actuator/info
GET /actuator/prometheus
```

## Requisitos funcionais principais

| ID | Requisito | Resultado esperado |
|---|---|---|
| RF01 | Criar documento | `DOCUMENT_OWNER` cria documento `DRAFT` no proprio tenant e gera auditoria |
| RF02 | Listar documentos | Cada role lista somente documentos autorizados |
| RF03 | Visualizar documento | Acesso respeita role e tenant; erros nao vazam detalhe interno |
| RF04 | Submeter documento | Owner submete documento proprio em `DRAFT` para `PENDING_REVIEW` |
| RF05 | Aprovar documento | Reviewer aprova documento pendente do proprio tenant |
| RF06 | Rejeitar documento | Reviewer rejeita documento pendente com motivo obrigatorio |
| RF07 | Consultar auditoria | Admin ou privacy officer consulta auditoria apenas do proprio tenant |

## Requisitos de seguranca principais

| ID | Controle | Resultado esperado |
|---|---|---|
| RS01 | Autenticacao JWT | Endpoints protegidos exigem token valido com `sub`, `tenant_id` e `roles` |
| RS02 | Isolamento por tenant | Dados de um tenant nunca ficam acessiveis para outro tenant |
| RS03 | Protecao contra IDOR | IDs manipulados na URL ou filtros nao permitem acesso indevido |
| RS04 | Autorizacao por role | Cada endpoint valida explicitamente as roles permitidas |
| RS05 | Validacao de entrada | Payloads invalidos retornam erro controlado |
| RS06 | Tratamento seguro de erros | Respostas nao vazam stack trace, SQL, token, claims completas ou classes internas |
| RS07 | Auditoria segura | Acoes sensiveis e tentativas proibidas geram eventos minimizados |
| RS08 | Logs seguros | Logs nao contem `Authorization`, JWT, segredos ou payload completo |
| RS09 | Rate limiting | Excesso de requisicoes retorna `429` e endpoints sensiveis tem limite menor |
| RS10 | Headers de seguranca | API configura headers minimos e documenta TLS na borda |

## Requisitos de privacidade e LGPD

| ID | Controle | Artefato ou comportamento esperado |
|---|---|---|
| LGPD01 | Inventario de dados pessoais | `docs/DATA_INVENTORY.md` |
| LGPD02 | Minimizacao de dados | Documento nao armazena arquivo real; auditoria e logs sao minimizados |
| LGPD03 | Finalidade | `docs/PRIVACY_PURPOSES.md` |
| LGPD04 | Solicitacao de acesso | Workflow auditavel para exportar dados pessoais do usuario |
| LGPD05 | Solicitacao de correcao | Correcao restrita a campos permitidos |
| LGPD06 | Exclusao ou anonimizacao | Decisao nao destrutiva, com retencao simulada e auditoria preservada |
| LGPD07 | Retencao de dados | `docs/DATA_RETENTION_POLICY.md` |
| LGPD08 | RIPD-lite | `docs/RIPD_LITE.md` |
| LGPD09 | Registro de tratamento | `docs/DATA_PROCESSING_RECORDS.md` |
| LGPD10 | Incidente simulado | Endpoints e relatorio de incidente com impacto, contencao e evidencias |

## DevSecOps e CI/CD

Workflows previstos em `.github/workflows`:

- `ci.yml`: executa `./gradlew clean check`, cobrindo build, testes, integracao, seguranca, arquitetura e cobertura.
- `codeql.yml`: analise estatica de Java/Kotlin.
- `dependency-review.yml`: bloqueio de dependencias com vulnerabilidades high ou critical.
- `container-scan.yml`: build da imagem Docker e scan com Trivy.
- `dast.yml`: aplicacao local + OWASP ZAP Baseline.
- `iac-scan.yml`: scan opcional de IaC com Trivy config, Checkov ou tfsec.

Principios de pipeline:

- permissoes explicitas e minimas por job;
- falha objetiva quando um gate critico quebra;
- artifacts de relatorio para testes, cobertura, container scan e DAST;
- documentacao de excecoes quando houver risco aceito;
- branch protection recomendada para exigir checks antes do merge.

## Documentacao prevista

Documentos principais:

- `docs/THREAT_MODEL.md`
- `docs/ASVS_MAPPING.md`
- `docs/LGPD_CONTROLS.md`
- `docs/DATA_INVENTORY.md`
- `docs/DATA_RETENTION_POLICY.md`
- `docs/DATA_PROCESSING_RECORDS.md`
- `docs/PRIVACY_PURPOSES.md`
- `docs/RIPD_LITE.md`
- `docs/INCIDENT_RESPONSE_LGPD.md`
- `docs/LOGGING_POLICY.md`
- `docs/CI_SECURITY_GATES.md`
- `docs/AWS_SECURITY_MODEL.md`
- `docs/AWS_ARCHITECTURE.md`
- `docs/AWS_SECURITY_EVIDENCE.md`
- `docs/CLOUDFLARE_SECURITY_MODEL.md`
- `docs/CLOUDFLARE_RULES_EXAMPLES.md`
- `docs/BRANCH_PROTECTION.md`
- `docs/EVIDENCE.md`

ADRs previstos:

- `docs/adr/ADR-001-security-model.md`
- `docs/adr/ADR-002-tenant-isolation.md`
- `docs/adr/ADR-003-lgpd-privacy-controls.md`
- `docs/adr/ADR-004-ci-security-gates.md`
- `docs/adr/ADR-005-aws-reference-architecture.md`
- `docs/adr/ADR-006-cloudflare-edge-protection.md`

## Estrutura de repositorio prevista

```text
secure-multitenant-api-devsecops-lgpd-cloud-lab/
  README.md
  SECURITY.md
  docker-compose.yml
  Dockerfile
  build.gradle.kts ou pom.xml
  openapi.yaml

  docs/
    THREAT_MODEL.md
    ASVS_MAPPING.md
    LGPD_CONTROLS.md
    DATA_INVENTORY.md
    DATA_RETENTION_POLICY.md
    DATA_PROCESSING_RECORDS.md
    PRIVACY_PURPOSES.md
    RIPD_LITE.md
    INCIDENT_RESPONSE_LGPD.md
    LOGGING_POLICY.md
    CI_SECURITY_GATES.md
    AWS_SECURITY_MODEL.md
    AWS_ARCHITECTURE.md
    AWS_SECURITY_EVIDENCE.md
    CLOUDFLARE_SECURITY_MODEL.md
    CLOUDFLARE_RULES_EXAMPLES.md
    BRANCH_PROTECTION.md
    EVIDENCE.md

  docs/adr/
    ADR-001-security-model.md
    ADR-002-tenant-isolation.md
    ADR-003-lgpd-privacy-controls.md
    ADR-004-ci-security-gates.md
    ADR-005-aws-reference-architecture.md
    ADR-006-cloudflare-edge-protection.md

  infra/
    aws/
      README.md
      main.tf
      iam.tf
      rds.tf
      kms.tf
      secrets.tf
      cloudwatch.tf
      securityhub.tf
    cloudflare/
      README.md
      waf-rules.md
      rate-limits.md
      api-shield-schema-validation.md
      mtls.md

  .github/
    workflows/
      ci.yml
      codeql.yml
      dependency-review.yml
      container-scan.yml
      dast.yml
      iac-scan.yml

  src/
    main/
      java ou kotlin/
    test/
      java ou kotlin/
```

## Testes obrigatorios

### Testes unitarios

Devem cobrir:

- transicao de status;
- regra de owner;
- regra de reviewer;
- criacao de auditoria;
- decisao de solicitacao LGPD;
- anonimizacao.

### Testes de integracao

Devem usar Testcontainers com PostgreSQL e cobrir:

- migrations;
- persistencia;
- query filtrada por tenant;
- rollback;
- auditoria;
- solicitacoes de privacidade.

### Testes negativos de seguranca

Cenarios obrigatorios:

- usuario sem token recebe `401`;
- token invalido recebe `401`;
- usuario sem role recebe `403`;
- owner tenta aprovar documento e recebe `403`;
- reviewer do tenant A tenta revisar documento do tenant B e recebe `403` ou `404` seguro;
- admin do tenant A tenta consultar auditoria do tenant B e nao recebe dados;
- usuario envia `tenantId` no payload e nao troca o tenant real;
- UUID invalido retorna erro controlado;
- payload acima do limite retorna erro controlado;
- excesso de requisicoes retorna `429`;
- usuario tenta consultar solicitacao LGPD de outro tenant e falha;
- usuario tenta excluir auditoria e falha;
- erro interno simulado nao expoe stack trace;
- logs nao contem `Authorization`;
- documento de outro tenant nao aparece em listagem.

### Testes de privacidade

Cenarios obrigatorios:

- usuario cria solicitacao de acesso aos proprios dados;
- relatorio de acesso nao contem dados de outro tenant;
- solicitacao de correcao altera apenas campos permitidos;
- solicitacao de exclusao nao apaga auditoria;
- anonimizacao remove email e displayName quando aplicavel;
- retencao impede exclusao destrutiva quando ha justificativa simulada;
- evento de auditoria e criado para solicitacao LGPD;
- relatorio RIPD-lite lista riscos e controles;
- incidente simulado gera relatorio de avaliacao;
- exportacao de dados pessoais exige autorizacao.

### Testes arquiteturais

Devem usar ArchUnit para validar:

- controllers nao acessam repositories diretamente;
- dominio nao depende de infraestrutura;
- camada de aplicacao orquestra casos de uso;
- camada de seguranca e centralizada;
- modulo de privacidade nao altera auditoria destrutivamente;
- modulo cloud nao vaza dependencia para dominio.

## Modelo AWS esperado

O projeto deve documentar ou implementar em `infra/aws`:

- role da aplicacao com permissoes minimas;
- policy separada para leitura de secrets;
- policy separada para escrita de logs;
- ausencia de usuario root e policies amplas como `*:*`;
- uso de Secrets Manager para segredo de banco;
- uso de KMS para RDS, S3, Secrets Manager e logs sensiveis quando aplicavel;
- RDS PostgreSQL privado, sem acesso publico, com encryption at rest e backups;
- CloudTrail multi-region recomendado;
- CloudWatch para logs, metricas, alarmes e dashboard;
- GuardDuty para findings de seguranca;
- Security Hub com AWS Foundational Security Best Practices;
- S3 com bloqueio de acesso publico, criptografia, versionamento opcional e lifecycle.

## Modelo Cloudflare esperado

O projeto deve documentar ou implementar em `infra/cloudflare`:

- WAF para injection, metodos inesperados e endpoints sensiveis;
- rate limiting por familia de endpoint;
- bloqueio externo de `/actuator/*`;
- API Shield Schema Validation baseado em `openapi.yaml`;
- mTLS para `/api/internal/*` ou integracoes maquina-maquina;
- protecao da origem para impedir bypass da Cloudflare;
- cuidado com spoofing de headers;
- minimizacao e retencao de logs de borda.

Exemplo de limites por endpoint:

| Endpoint | Limite sugerido | Acao |
|---|---:|---|
| `/api/auth/*` | baixo | block ou challenge |
| `/api/privacy/requests/*` | medio | log ou challenge |
| `/api/documents` | medio | throttle |
| `/api/documents/*/reviews/*` | medio | throttle |
| `/actuator/*` | bloqueado externamente | block |

## Evidencias esperadas

O repositorio deve publicar evidencias em `docs/EVIDENCE.md`, incluindo:

- resultado de testes;
- resultado de testes negativos de seguranca;
- relatorios de cobertura;
- CodeQL configurado;
- Dependency Review configurado;
- Trivy configurado;
- ZAP Baseline configurado;
- threat model;
- mapeamento LGPD;
- modelo AWS;
- modelo Cloudflare;
- branch protection recomendada;
- limitacoes conhecidas.

## Criterios de aceite resumidos

A PoC deve ser considerada concluida apenas quando:

- a API sobe localmente com Docker Compose;
- banco usa migrations versionadas;
- endpoints protegidos exigem JWT;
- tenant vem do token, nao do payload;
- existem testes negativos de seguranca;
- usuario de um tenant nao acessa dados de outro;
- roles sao testadas;
- auditoria e persistida;
- erros nao vazam stack trace;
- logs nao expoem token;
- rate limiting funciona localmente;
- workflow de solicitacao LGPD existe;
- relatorio de acesso aos dados e gerado;
- anonimizacao funciona quando aplicavel;
- retencao impede exclusao destrutiva quando documentado;
- RIPD-lite existe;
- inventario de dados existe;
- relatorio de incidente simulado existe;
- GitHub Actions roda no PR;
- CodeQL esta configurado;
- Dependency Review esta configurado;
- Trivy esta configurado;
- ZAP Baseline esta configurado;
- modelo AWS esta documentado;
- modelo Cloudflare esta documentado;
- branch protection esta documentada;
- evidencias estao publicadas em `docs/EVIDENCE.md`;
- limitacoes estao declaradas.

## Nao objetivos

O projeto deve evitar:

- declarar conformidade legal completa com a LGPD;
- transformar a PoC em tutorial superficial de ferramentas;
- depender de AWS ou Cloudflare pagos para rodar localmente;
- implementar scanners redundantes sem criterio;
- commitar secrets;
- logar JWT, `Authorization` ou payload completo;
- aceitar `tenantId` do payload;
- apagar auditoria destrutivamente;
- confiar cegamente em headers de IP;
- expor `/actuator` publicamente;
- usar IAM com `*:*`;
- deixar bucket S3 publico.

## Valor de mercado

Esta PoC comunica uma tese forte para vagas backend Java/Kotlin pleno forte:

```text
Seguranca, privacidade, cloud e CI/CD nao sao camadas separadas.
Sao controles verificaveis que precisam existir no codigo, nos testes,
no pipeline, na infraestrutura e na documentacao.
```

Quando implementado, o projeto pode ser usado como repositorio fixado no GitHub para demonstrar maturidade em backend, seguranca de aplicacao, privacidade, DevSecOps e arquitetura cloud.

## Status atual deste documento

Este arquivo e um apanhado de escopo e funcionalidades esperadas da PoC. Ele nao substitui evidencias de implementacao. Cada controle descrito aqui deve ser confirmado posteriormente por codigo, testes, workflows, documentacao especifica ou artefatos publicados no repositorio.
