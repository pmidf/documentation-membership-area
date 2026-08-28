# Plataforma de Membros PMI-DF — Documento de Produto e Backlog

**Versão:** 1.0
**Data:** 27/08/2026
**Papel responsável:** Product Owner
**Status:** Backlog inicial para refinamento com o time técnico e a diretoria do capítulo

---

## Sumário

1. [Visão do produto](#1-visão-do-produto)
2. [Análise crítica da ideia](#2-análise-crítica-da-ideia-o-que-sustenta-e-o-que-precisa-de-decisão)
3. [Atores, papéis e matriz de permissões](#3-atores-papéis-e-matriz-de-permissões)
4. [Arquitetura funcional e modelo de dados](#4-arquitetura-funcional-e-modelo-de-dados)
5. [Integrações externas](#5-integrações-externas)
6. [Mapa de épicos](#6-mapa-de-épicos-renomeados)
7. [Backlog detalhado por épico](#7-backlog-detalhado-por-épico)
   - [ÉP-01 — Identidade, Acesso e Validação de Filiação](#ép-01--identidade-acesso-e-validação-de-filiação)
   - [ÉP-02 — Perfil do Membro e Diretório da Comunidade](#ép-02--perfil-do-membro-e-diretório-da-comunidade)
   - [ÉP-03 — Voluntariado e Organograma do Capítulo](#ép-03--voluntariado-e-organograma-do-capítulo)
   - [ÉP-04 — Clube de Vantagens do Filiado](#ép-04--clube-de-vantagens-do-filiado)
   - [ÉP-05 — Agenda de Eventos e Trilha de Participação](#ép-05--agenda-de-eventos-e-trilha-de-participação)
   - [ÉP-06 — Hub de Conteúdo Exclusivo e Conhecimento PMI](#ép-06--hub-de-conteúdo-exclusivo-e-conhecimento-pmi)
   - [ÉP-07 — Vitrine de Oportunidades de Carreira](#ép-07--vitrine-de-oportunidades-de-carreira)
   - [ÉP-08 — Reconhecimento: Badges e Ranking de Engajamento](#ép-08--reconhecimento-badges-e-ranking-de-engajamento)
   - [ÉP-09 — Experiência Freemium e Conversão para Filiação](#ép-09--experiência-freemium-e-conversão-para-filiação)
   - [ÉP-10 — Console Administrativo, Auditoria e LGPD](#ép-10--console-administrativo-auditoria-e-lgpd)
8. [Matriz consolidada de CRUDs](#8-matriz-consolidada-de-cruds)
9. [Requisitos não funcionais](#9-requisitos-não-funcionais)
10. [Roadmap sugerido](#10-roadmap-sugerido-por-incrementos)
11. [Riscos, premissas e questões em aberto](#11-riscos-premissas-e-questões-em-aberto)
12. [Glossário](#12-glossário)

---

## 1. Visão do produto

### 1.1 Declaração de visão

> **Para** profissionais de gerenciamento de projetos do Distrito Federal
> **Que** buscam desenvolvimento, reconhecimento e conexão com uma comunidade ativa
> **A** Plataforma de Membros PMI-DF
> **É** um ambiente digital único do capítulo
> **Que** concentra benefícios, conteúdo exclusivo, agenda de eventos, oportunidades de carreira e reconhecimento por engajamento
> **Diferente de** grupos de WhatsApp, newsletters e páginas soltas de rede social
> **O produto** conecta a identidade de filiação do PMI Global ao valor entregue localmente, tornando visível e mensurável o retorno da filiação.

### 1.2 Objetivos de negócio e métricas

| #    | Objetivo                                  | Métrica primária (KPI)                                    | Meta inicial sugerida                |
| ---- | ----------------------------------------- | --------------------------------------------------------- | ------------------------------------ |
| OB-1 | Aumentar a base de filiados do capítulo   | Taxa de conversão Pessoa → Filiado na plataforma          | 5% dos cadastros freemium em 6 meses |
| OB-2 | Aumentar a retenção/renovação de filiação | Taxa de renovação anual dos filiados ativos na plataforma | +10 p.p. vs. base atual              |
| OB-3 | Engajar a comunidade em eventos           | Nº médio de eventos por filiado/ano                       | 3 eventos/filiado/ano                |
| OB-4 | Tornar o valor da filiação tangível       | Nº de resgates de benefícios por filiado ativo            | 1,5 resgates/filiado/ano             |
| OB-5 | Formar base de dados própria do capítulo  | Nº de cadastros com perfil completo (≥80% dos campos)     | 60% da base                          |

### 1.3 Escopo do MVP (proposta)

**Dentro:** cadastro/login, validação de filiação, perfil, bloqueio freemium, clube de benefícios, agenda de eventos com sincronização Sympla, hub de conteúdo, vitrine de vagas, badges por tempo de filiação, console administrativo.

**Fora do MVP (backlog futuro):** ranking gamificado avançado, missões comemorativas com submissão manual, organograma público interativo, mentoria, fórum/comunidade, emissão de certificados de PDU.

---

## 2. Análise crítica da ideia (o que sustenta e o que precisa de decisão)

Antes de escrever histórias, registro a leitura de produto sobre o material coletado. Estes pontos devem ser validados com a diretoria porque mudam o desenho técnico.

### 2.1 Pontos fortes da proposta

- **A filiação já é o modelo de negócio.** A plataforma não precisa inventar monetização: ela precisa tornar visível o valor de algo que já é pago. Isso simplifica muito o produto — o "paywall" é uma consequência natural, não um artifício.
- **A validação via base do PMI é o coração do sistema.** Ter uma fonte de verdade externa (PMI Global) evita que o capítulo mantenha manualmente um cadastro de filiação, que é o maior custo operacional desse tipo de plataforma.
- **A escolha de integrar com Sympla em vez de construir gestão de inscrições** é acertada. O capítulo continua operando onde já opera e a plataforma vira a camada de identidade e histórico.
- **Gamificação ancorada em fatos verificáveis** (tempo de filiação, presença em eventos) é mais sustentável que gamificação por pontos arbitrários.

### 2.2 Pontos de atenção que exigem decisão

| #   | Observação                                                                                                                                                      | Risco se ignorado                                                    | Recomendação                                                                                                                                                                                      |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A-1 | **"ATS de vagas" não é um ATS.** O requisito descreve um mural de vagas somente leitura, sem candidatura, funil ou triagem.                                     | Expectativa desalinhada com a diretoria; escopo inflado.             | Renomear para _Vitrine de Oportunidades_. Manter o modelo de dados preparado para receber candidatura em fase futura.                                                                             |
| A-2 | **E-mails do Sympla informados pelo próprio usuário sem verificação.** Qualquer pessoa poderia declarar o e-mail de outra e herdar o histórico de participação. | Fraude no ranking de gamificação e vazamento de histórico alheio.    | Exigir verificação de posse (double opt-in) de todo e-mail alternativo antes de vinculá-lo a participações. **Bloqueante para o ÉP-08.**                                                          |
| A-3 | **A filiação expira.** O requisito trata "é filiado" como um estado binário permanente.                                                                         | Ex-filiados mantendo acesso a benefícios pagos.                      | Modelar filiação com `data_inicio`, `data_expiracao` e `status`, com job diário de reconciliação e downgrade automático.                                                                          |
| A-4 | **Dados sensíveis coletados no cadastro** (gênero, data de nascimento, escolaridade, local de residência).                                                      | Exposição LGPD; abandono do cadastro por fricção.                    | Tornar opcionais os campos demográficos, com base legal de consentimento explícito e finalidade declarada. Só e-mail, senha e nome são obrigatórios no cadastro.                                  |
| A-5 | **YouTube "privado" não é controle de acesso.** Vídeo não listado é acessível por qualquer pessoa com a URL.                                                    | Conteúdo exclusivo vazando para não filiados.                        | Nunca expor a URL no HTML/API para não filiados. Servir via endpoint autorizado que retorna o embed apenas para sessão de filiado, com `origin` restrito. Aceitar que é mitigação, não blindagem. |
| A-6 | **Badge por tempo de filiação depende do histórico do PMI.** Se a API só retorna o status atual, não há como calcular 5 ou 10 anos.                             | Badges de marco impossíveis de conceder automaticamente.             | Confirmar se o relatório do PMI traz `member since`. Se não trouxer, prever concessão manual pelo admin como fallback.                                                                            |
| A-7 | **Freemium sem prévia real não converte.** Bloquear a página inteira com um cadeado gera frustração, não desejo.                                                | Baixa conversão, contradizendo o OB-1.                               | Mostrar título, imagem e primeiras linhas de cada item, borrando apenas o valor final (código do cupom, vídeo, texto de resgate).                                                                 |
| A-8 | **"Admin" como papel único e absoluto.** Um único nível de administrador com poder de editar tudo em uma organização com dezenas de voluntários rotativos.      | Erro operacional e ausência de rastreabilidade nas trocas de gestão. | Modelar papéis administrativos granulares (Editor de Conteúdo, Gestor de Eventos, Super Admin) desde o modelo de dados, mesmo que a UI do MVP só exponha um perfil.                               |
| A-9 | **Ranking público de participação.** Expor nomes em ranking é tratamento de dado pessoal com potencial de constrangimento.                                      | Reclamações e pedidos de remoção.                                    | Participação no ranking é _opt-in_ (já previsto no requisito de perfil) e o opt-out deve remover o usuário da exibição, não apenas parar de pontuar.                                              |

### 2.3 Decisão de arquitetura de produto derivada da análise

O requisito original tratava "Pessoa/Filiado" como tipos de usuário. Recomendo tratar como **um único tipo de usuário (Pessoa) com atributos de estado**:

- `Pessoa` é sempre a entidade base;
- `Filiado` é um **estado temporal e verificável** (`status_filiacao`), não uma tabela separada;
- `Voluntário` é um **vínculo organizacional** (cargo + diretoria + mandato), independente da filiação;
- `Admin` é um **papel de acesso** (RBAC), independente dos dois anteriores.

Isso evita duplicação de registros, resolve o problema da expiração (A-3) e permite que uma pessoa seja simultaneamente filiada, voluntária e administradora sem conflito de modelagem.

---

## 3. Atores, papéis e matriz de permissões

### 3.1 Atores

| Ator           | Definição                                                                      | Como se torna                                                      |
| -------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------ |
| **Visitante**  | Não autenticado.                                                               | Estado inicial.                                                    |
| **Pessoa**     | Cadastrada e autenticada, sem filiação ativa. Acesso freemium.                 | Cadastro na plataforma.                                            |
| **Filiado**    | Pessoa com filiação PMI ativa e validada.                                      | Validação automática (ThoughtSpot) ou concessão manual pelo Admin. |
| **Voluntário** | Pessoa com vínculo ativo em uma diretoria do capítulo. Aparece no organograma. | Designação manual pelo Admin.                                      |
| **Admin**      | Pessoa com papel administrativo. Opera os CRUDs de conteúdo e a curadoria.     | Concessão por um Super Admin.                                      |

### 3.2 Matriz de permissões (RBAC)

Legenda: **L** = Ler · **P** = Prévia bloqueada (freemium) · **E** = Escrever/Gerir · **—** = Sem acesso

| Recurso                              | Visitante | Pessoa | Filiado |      Admin      |
| ------------------------------------ | :-------: | :----: | :-----: | :-------------: |
| Página institucional / login         |     L     |   L    |    L    |        L        |
| Próprio perfil                       |     —     |   E    |    E    |        E        |
| Perfil de terceiros (dados públicos) |     —     |   L    |    L    |       L+E       |
| Clube de benefícios (lista)          |     P     |   P    |    L    |        E        |
| Código/instrução de resgate          |     —     |   —    |    L    |        E        |
| Agenda de eventos                    |     L     |   L    |    L    |        E        |
| Histórico próprio de participação    |     —     |   L    |    L    |        L        |
| Hub de conteúdo (catálogo)           |     P     |   P    |    L    |        E        |
| Player de vídeo exclusivo            |     —     |   —    |    L    |        E        |
| Links curados PMI Global             |     P     |   P    |    L    |        E        |
| Vitrine de vagas                     |     L     |   L    |    L    |        E        |
| Badges próprios                      |     —     |   —    |    L    |        E        |
| Ranking de engajamento               |     —     |   L    |    L    |        E        |
| Organograma                          |     L     |   L    |    L    |        E        |
| Console administrativo               |     —     |   —    |    —    |        E        |
| Trilha de auditoria                  |     —     |   —    |    —    | L (Super Admin) |

> **Regra transversal RN-000:** toda verificação de permissão ocorre no **backend**, na camada de serviço. Ocultar elementos no frontend é experiência do usuário, nunca controle de acesso.

---

## 4. Arquitetura funcional e modelo de dados

### 4.1 Módulos do sistema

```
┌──────────────────────────────────────────────────────────────┐
│                     APLICAÇÃO WEB (SPA)                      │
│   Área Pública  │  Área do Membro  │  Console Administrativo │
└──────────────────────────┬───────────────────────────────────┘
                           │ HTTPS / REST + JWT
┌──────────────────────────┴───────────────────────────────────┐
│                        API BACKEND                           │
│  Auth │ Perfil │ Benefícios │ Eventos │ Conteúdo │ Vagas │   │
│  Badges │ Organograma │ Admin │ Auditoria                    │
├──────────────────────────────────────────────────────────────┤
│         CAMADA DE INTEGRAÇÃO (adapters + filas)              │
│  ThoughtSpot/PMI │ Sympla │ Google OAuth │ SMTP │ Storage    │
├──────────────────────────────────────────────────────────────┤
│      PostgreSQL      │      Redis (cache/fila)      │  S3    │
└──────────────────────────────────────────────────────────────┘
```

### 4.2 Entidades principais

#### Núcleo de identidade

**`pessoa`** — entidade base de todo usuário
| Campo | Tipo | Notas |
|-------|------|-------|
| `id` | UUID PK | |
| `nome_completo` | varchar(160) | obrigatório |
| `email_principal` | citext UNIQUE | obrigatório, login |
| `email_verificado_em` | timestamptz | null até double opt-in |
| `senha_hash` | text | Argon2id; null se conta só Google |
| `telefone` | varchar(20) | E.164, opcional |
| `data_nascimento` | date | opcional (LGPD) |
| `genero` | varchar(40) | opcional, lista + "prefiro não informar" |
| `escolaridade` | varchar(40) | opcional, enum |
| `uf_residencia` / `cidade_residencia` | varchar | opcional |
| `pmi_id` | varchar(20) | ID do PMI declarado no cadastro |
| `status_filiacao` | enum | `NAO_FILIADO`, `PENDENTE_VALIDACAO`, `ATIVO`, `EXPIRADO`, `CONCEDIDO_MANUALMENTE` |
| `filiacao_inicio` / `filiacao_expiracao` | date | vindos do PMI |
| `filiacao_validada_em` | timestamptz | última reconciliação bem-sucedida |
| `foto_url` | text | |
| `mini_bio` | varchar(600) | |
| `aberto_a_oportunidades` | boolean | default false |
| `opt_in_gamificacao` | boolean | default false |
| `opt_in_email_marketing` | boolean | default false |
| `criado_em` / `atualizado_em` / `excluido_em` | timestamptz | soft delete |

**`identidade_externa`** — login social
`id`, `pessoa_id` FK, `provedor` (`GOOGLE`), `subject_id`, `email_provedor`, `vinculado_em`. Único por (`provedor`, `subject_id`).

**`email_alternativo`** — e-mails usados no Sympla
`id`, `pessoa_id` FK, `email` citext, `verificado_em`, `token_verificacao_hash`, `expira_em`. Único global para evitar que duas pessoas reivindiquem o mesmo e-mail.

**`papel`** / **`pessoa_papel`** — RBAC
`papel`: `SUPER_ADMIN`, `ADMIN_CONTEUDO`, `ADMIN_EVENTOS`, `ADMIN_BENEFICIOS`. `pessoa_papel` guarda `concedido_por` e `concedido_em`.

**`consentimento`** — LGPD
`id`, `pessoa_id`, `tipo` (`TERMOS`, `PRIVACIDADE`, `MARKETING`, `GAMIFICACAO`, `DADOS_DEMOGRAFICOS`), `versao_documento`, `aceito`, `data`, `ip`, `user_agent`.

#### Organização

**`diretoria`**: `id`, `nome`, `sigla`, `descricao`, `ordem_exibicao`, `diretoria_pai_id` (auto-relacionamento para hierarquia), `ativa`.
**`cargo`**: `id`, `nome`, `nivel` (`DIRETOR`, `COORDENADOR`, `VOLUNTARIO`), `ordem_exibicao`.
**`vinculo_voluntario`**: `id`, `pessoa_id`, `diretoria_id`, `cargo_id`, `gestao` (ex.: "2026"), `data_inicio`, `data_fim` (null = ativo), `exibir_organograma`.

#### Benefícios

**`beneficio`**: `id`, `titulo`, `legenda`, `descricao_longa`, `imagem_url`, `categoria`, `parceiro_nome`, `tipo_resgate` (`CUPOM_UNICO`, `CUPOM_COMPARTILHADO`, `INSTRUCAO_TEXTO`), `codigo_compartilhado`, `instrucao_resgate`, `url_parceiro`, `vigencia_inicio`, `vigencia_fim`, `limite_resgates_por_pessoa`, `status` (`RASCUNHO`, `PUBLICADO`, `ARQUIVADO`), `criado_por`.
**`cupom`**: `id`, `beneficio_id`, `codigo`, `status` (`DISPONIVEL`, `RESERVADO`, `CONSUMIDO`), `pessoa_id`, `consumido_em`.
**`resgate_beneficio`**: `id`, `beneficio_id`, `pessoa_id`, `cupom_id`, `resgatado_em`, `status_filiacao_no_resgate` (snapshot para auditoria), `ip`.

#### Eventos

**`evento`**: `id`, `titulo`, `legenda`, `descricao`, `data_inicio`, `data_fim`, `formato` (`PRESENCIAL`, `ONLINE`, `HIBRIDO`), `local`, `url_inscricao`, `imagem_url`, `origem` (`MANUAL`, `SYMPLA`), `sympla_event_id`, `visibilidade` (`PUBLICO`, `EXCLUSIVO_FILIADO`), `status`, `sincronizado_em`.
**`participacao_evento`**: `id`, `evento_id`, `pessoa_id` (nullable até conciliar), `email_participante`, `nome_participante`, `status_presenca` (`INSCRITO`, `CHECKIN`, `AUSENTE`), `origem_dado`, `sympla_participant_id`, `conciliado_em`, `conciliado_por`. Único por (`evento_id`, `email_participante`).
**`sincronizacao_sympla`** — log de execução: `id`, `tipo`, `iniciado_em`, `finalizado_em`, `status`, `registros_lidos`, `registros_novos`, `erros_json`.

#### Conteúdo

**`conteudo`**: `id`, `titulo`, `descricao`, `tipo` (`VIDEO_YOUTUBE`, `ARTIGO_URL`, `DOCUMENTO`, `LINK_PMI_GLOBAL`), `url`, `youtube_video_id`, `thumbnail_url`, `categoria_id`, `nivel_acesso` (`PUBLICO`, `EXCLUSIVO_FILIADO`), `duracao_segundos`, `publicado_em`, `status`, `destaque`.
**`categoria_conteudo`**: `id`, `nome`, `slug`, `ordem`.
**`visualizacao_conteudo`**: `id`, `conteudo_id`, `pessoa_id`, `visualizado_em`, `progresso_percentual`.

#### Vagas

**`vaga`**: `id`, `titulo`, `empresa_nome`, `empresa_logo_url`, `descricao`, `email_recrutador`, `nome_recrutador`, `modalidade` (`PRESENCIAL`, `REMOTO`, `HIBRIDO`), `nivel`, `localidade`, `faixa_salarial`, `slug` UNIQUE, `data_publicacao`, `data_expiracao`, `status` (`RASCUNHO`, `PUBLICADA`, `EXPIRADA`, `ARQUIVADA`), `criado_por`.

#### Gamificação

**`badge`**: `id`, `nome`, `descricao`, `criterio_texto`, `imagem_url`, `tipo` (`MARCO_FILIACAO`, `COMEMORATIVO`, `VOLUNTARIADO`, `PARTICIPACAO`), `modo_concessao` (`AUTOMATICO`, `SUBMISSAO_MANUAL`), `regra_json`, `exige_evidencia`, `instrucao_evidencia`, `vigencia_inicio`, `vigencia_fim`, `status`.
**`badge_concedido`**: `id`, `badge_id`, `pessoa_id`, `concedido_em`, `origem` (`AUTOMATICO`, `APROVACAO_ADMIN`), `concedido_por`, `submissao_id`. Único por (`badge_id`, `pessoa_id`).
**`submissao_badge`**: `id`, `badge_id`, `pessoa_id`, `texto_submissao`, `arquivo_url`, `url_evidencia`, `status` (`PENDENTE`, `EM_ANALISE`, `APROVADA`, `REPROVADA`), `avaliado_por`, `avaliado_em`, `justificativa_avaliacao`.
**`pontuacao_engajamento`**: `id`, `pessoa_id`, `periodo` (ex.: `2026`), `pontos`, `eventos_participados`, `atualizado_em`.

#### Transversal

**`log_auditoria`**: `id`, `pessoa_id_ator`, `acao`, `entidade`, `entidade_id`, `dados_antes_json`, `dados_depois_json`, `ip`, `user_agent`, `criado_em`. Append-only.
**`integracao_log`**: `id`, `sistema` (`THOUGHTSPOT`, `SYMPLA`, `GOOGLE`), `operacao`, `request_resumo`, `http_status`, `duracao_ms`, `sucesso`, `mensagem_erro`.

---

## 5. Integrações externas

> ⚠️ **Nota de confiabilidade:** os endpoints abaixo refletem o padrão público conhecido de cada serviço. **Antes da sprint de integração, é obrigatória uma spike técnica** para confirmar versão da API, escopos e limites de taxa junto à documentação vigente e ao contrato do PMI-DF com cada fornecedor.

### 5.1 PMI Global via ThoughtSpot — validação de filiação

**Finalidade:** confirmar se a pessoa possui filiação ativa no PMI e obter a data de início da filiação (necessária para os badges de marco).

**Padrão de integração:** o ThoughtSpot é uma plataforma de analytics, não um serviço de identidade. Ele expõe dados de um _liveboard_/_answer_ pré-construído com a lista de filiados do capítulo. A integração portanto é de **consulta a relatório**, não de autenticação.

**Fluxo técnico:**

```
1. Autenticação de serviço
   POST {thoughtspot_host}/api/rest/2.0/auth/token/full
   Body: { username, secret_key ou password, org_id, validity_time_in_sec }
   → retorna bearer token de curta duração (cachear em Redis com TTL < validade)

2. Consulta ao relatório de filiados do capítulo
   POST {thoughtspot_host}/api/rest/2.0/searchdata
   Body: { query_string: "[member_id] = '<pmi_id>'", logical_table_identifier: "<id_do_worksheet>" }
   → retorna colunas: member_id, first_name, last_name, email, member_status,
     member_since, expiration_date, chapter_code

3. Normalização e persistência
   → grava status_filiacao, filiacao_inicio, filiacao_expiracao, filiacao_validada_em
```

**Estratégia recomendada — sincronização em lote + consulta pontual:**

| Modo                     | Quando roda                                      | Por quê                                                                                                                                                                                                |
| ------------------------ | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Batch diário** (03:00) | Job agendado                                     | Baixa o _dump_ completo dos filiados do capítulo `chapter_code = PMI-DF`, faz _upsert_ em tabela espelho `pmi_filiado_espelho` e reconcilia todos os status. Reduz drasticamente o número de chamadas. |
| **Consulta pontual**     | No cadastro, quando a pessoa declara ser filiada | Feedback imediato ao usuário. Com _fallback_: se a API falhar, status vira `PENDENTE_VALIDACAO` e o batch resolve.                                                                                     |

**Regras de resiliência:**

- Timeout de 8s, 3 tentativas com _backoff_ exponencial;
- _Circuit breaker_: após 5 falhas consecutivas, para de chamar por 10 min e usa a tabela espelho;
- **Nunca** derrubar o acesso de um filiado por falha de integração — só rebaixar status após confirmação positiva de expiração;
- Casamento por `pmi_id` (prioritário) e, em fallback, por `email` normalizado (lowercase, trim);
- Todo _payload_ trafega e é armazenado apenas com os campos necessários (minimização LGPD).

### 5.2 Sympla — eventos e participantes

**Finalidade:** popular a agenda automaticamente e alimentar o histórico de participação e o ranking.

**Autenticação:** token de API do organizador enviado em header (`s_token`). Guardar em cofre de segredos, nunca no código ou no frontend.

**Endpoints previstos:**

| Operação                      | Endpoint (a confirmar)                                                 | Uso                                  |
| ----------------------------- | ---------------------------------------------------------------------- | ------------------------------------ |
| Listar eventos do organizador | `GET https://api.sympla.com.br/public/v4/events?published=true&page=N` | Sincronizar agenda                   |
| Detalhe do evento             | `GET /public/v4/events/{event_id}`                                     | Enriquecer dados                     |
| Listar participantes          | `GET /public/v4/events/{event_id}/participants?page=N`                 | Histórico e pontuação                |
| Listar pedidos                | `GET /public/v4/events/{event_id}/orders`                              | Conciliação financeira (fora do MVP) |

**Job de sincronização:**

```
Frequência: eventos = a cada 6h | participantes = a cada 1h nas 48h em torno do evento,
            depois 1x/dia por 7 dias, depois encerra

Algoritmo de participantes:
  para cada participante retornado:
    email_norm ← normalizar(participante.email)
    pessoa ← buscar por pessoa.email_principal = email_norm
             OU email_alternativo.email = email_norm AND verificado_em IS NOT NULL
    upsert participacao_evento (evento_id, email_participante) com:
      pessoa_id ← pessoa?.id (pode ser null → fica órfã para conciliação manual)
      status_presenca ← mapear(participante.checkin_status)
    se pessoa_id preenchido e status = CHECKIN → publicar evento de domínio
       ParticipacaoConfirmada → consumidor recalcula pontuacao_engajamento
```

**Regras críticas:**

- Idempotência por `(evento_id, email_participante)` — reprocessar não duplica;
- Participação órfã (e-mail sem pessoa cadastrada) **é preservada**: se a pessoa se cadastrar depois com aquele e-mail, o vínculo retroage automaticamente. Este é um gancho forte de conversão (ÉP-09);
- Paginação obrigatória com controle de _rate limit_ (respeitar `429` com `Retry-After`);
- Log completo em `sincronizacao_sympla` para auditoria e reprocessamento.

### 5.3 Google — login social

- **Protocolo:** OAuth 2.0 + OpenID Connect, fluxo _Authorization Code with PKCE_;
- **Escopos:** `openid`, `email`, `profile` (nada além disso);
- **Vinculação de contas:** se o `email` do Google já existir em `pessoa`, **não criar duplicata** — solicitar a senha atual para vincular a identidade, ou enviar e-mail de confirmação de vínculo. Vincular silenciosamente é um vetor de sequestro de conta;
- Contas criadas só via Google têm `senha_hash = null`; se pedirem "recuperar senha", o fluxo deve orientar o login social ou permitir a definição de uma primeira senha via e-mail verificado.

### 5.4 YouTube — conteúdo exclusivo

- Vídeos configurados como **não listados** no canal do PMI-DF;
- A URL/`video_id` **nunca** é retornada em endpoint público;
- `GET /api/conteudos/{id}/player` valida a sessão, confirma `status_filiacao = ATIVO` e só então devolve o `embed_url`;
- Restringir o domínio de incorporação nas configurações do canal;
- Registrar `visualizacao_conteudo` a cada acesso concedido.

---

## 6. Mapa de épicos (renomeados)

| ID    | Nome original                | **Nome proposto**                                   | Justificativa da renomeação                                                                                               |
| ----- | ---------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| ÉP-01 | Autenticação                 | **Identidade, Acesso e Validação de Filiação**      | O épico não é só autenticar: ele estabelece _quem_ é a pessoa perante o PMI. A validação de filiação é o núcleo de valor. |
| ÉP-02 | Perfil de usuário            | **Perfil do Membro e Diretório da Comunidade**      | Reforça que o perfil serve à comunidade, não é só uma ficha cadastral.                                                    |
| ÉP-03 | _(embutido em perfil)_       | **Voluntariado e Organograma do Capítulo**          | Extraído do perfil: é outro domínio (governança do capítulo), com ciclo de vida e regras próprias.                        |
| ÉP-04 | Clube de benefícios          | **Clube de Vantagens do Filiado**                   | Nomeia o público e o benefício percebido; "vantagens" é o vocabulário do associado.                                       |
| ÉP-05 | Calendário/Agenda de eventos | **Agenda de Eventos e Trilha de Participação**      | Incorpora o histórico, que é o ativo de maior valor (alimenta gamificação e prova de engajamento).                        |
| ÉP-06 | Área de conteúdo exclusivo   | **Hub de Conteúdo Exclusivo e Conhecimento PMI**    | "Hub" comunica a centralização (conteúdo próprio + redirecionamento ao PMI Global).                                       |
| ÉP-07 | ATS de vagas                 | **Vitrine de Oportunidades de Carreira**            | Corrige a expectativa: não há funil de recrutamento, é um mural curado (ver A-1).                                         |
| ÉP-08 | Gamificação                  | **Reconhecimento: Badges e Ranking de Engajamento** | "Reconhecimento" comunica o propósito; "gamificação" é o meio, não o fim.                                                 |
| ÉP-09 | _(novo)_                     | **Experiência Freemium e Conversão para Filiação**  | O bloqueio freemium aparecia diluído em vários requisitos. É um mecanismo transversal com KPI próprio (OB-1).             |
| ÉP-10 | _(novo)_                     | **Console Administrativo, Auditoria e LGPD**        | Os CRUDs administrativos e a conformidade legal precisam de espaço próprio no backlog, senão viram dívida.                |

---

## 7. Backlog detalhado por épico

**Convenções usadas:**

- **Prioridade (MoSCoW):** `Must` · `Should` · `Could` · `Won't (agora)`
- **Estimativa:** Story Points (Fibonacci), a ser recalibrada em _planning poker_ com o time
- Toda história tem os critérios de aceite em formato **Dado / Quando / Então**
- **DoR global:** história com critérios de aceite escritos, dependências mapeadas, protótipo (quando houver UI) e regra de negócio validada com a diretoria
- **DoD global:** código revisado, testes automatizados (unitário + integração dos caminhos críticos), verificação de permissão no backend, log de auditoria quando aplicável, deploy em homologação e aceite do PO

---

### ÉP-01 — Identidade, Acesso e Validação de Filiação

**Objetivo:** permitir que qualquer pessoa crie conta com baixa fricção e que a plataforma determine com confiança, e de forma contínua, quem é filiado ao PMI.

**Valor:** sem esta camada, nenhum outro épico consegue diferenciar Pessoa de Filiado — ou seja, nenhum outro épico entrega valor.

**Entidades:** `pessoa`, `identidade_externa`, `consentimento`, `sessao`, `token_recuperacao`, `pmi_filiado_espelho`.

#### US-01.01 — Cadastro de conta com e-mail e senha

> **Como** visitante interessado na comunidade do PMI-DF
> **Quero** criar uma conta informando meus dados
> **Para** acessar a área de membros da plataforma

**Critérios de aceite**

- **Dado** que estou na tela de cadastro, **quando** informo nome completo, e-mail e senha válidos e aceito os termos, **então** minha conta é criada com `status_filiacao = NAO_FILIADO` e recebo e-mail de verificação.
- **Dado** que informo um e-mail já cadastrado, **quando** submeto, **então** recebo mensagem neutra ("Se este e-mail estiver disponível, você receberá instruções") e **não** é revelado se a conta existe.
- **Dado** que informo senha com menos de 10 caracteres ou presente em lista de senhas vazadas, **quando** submeto, **então** o cadastro é recusado com orientação clara.
- **Dado** que deixo em branco telefone, data de nascimento, gênero, escolaridade e residência, **quando** submeto, **então** o cadastro é concluído normalmente (campos opcionais).

**Regras de negócio**

- RN-01.01.1 — Obrigatórios: `nome_completo`, `email_principal`, `senha`, aceite de Termos e Política de Privacidade.
- RN-01.01.2 — Opcionais e sujeitos a consentimento específico: `telefone`, `data_nascimento`, `genero`, `escolaridade`, `cidade/uf_residencia`. Cada bloco exibe a finalidade da coleta.
- RN-01.01.3 — `genero` deve incluir "Prefiro não informar".
- RN-01.01.4 — Conta nasce sem verificação; funcionalidades de escrita ficam limitadas até `email_verificado_em` ser preenchido.

**Notas técnicas**

- `POST /api/auth/registro` → 201 com `{ pessoa_id, requer_verificacao: true }`.
- Hash da senha com **Argon2id** (fallback bcrypt cost ≥ 12). Nunca armazenar senha reversível.
- _Rate limit_: 5 tentativas/IP/hora no endpoint de registro; CAPTCHA (hCaptcha/Turnstile) após a 3ª.
- Validação de e-mail com regex conservador + verificação de MX opcional.
- Persistir registros em `consentimento` para cada bloco aceito, com versão do documento, IP e user-agent.
- Normalizar e-mail (lowercase + trim) antes de gravar; coluna `citext`.

**Dependências:** — · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-01.02 — Declaração de filiação no cadastro

> **Como** visitante que já é filiado ao PMI
> **Quero** informar meu PMI ID durante o cadastro
> **Para** ter meu acesso de filiado liberado imediatamente

**Critérios de aceite**

- **Dado** que marco "Já sou filiado ao PMI", **quando** o formulário se expande, **então** vejo o campo obrigatório de PMI ID e a orientação de onde encontrá-lo em `pmi.org`.
- **Dado** que informo um PMI ID válido e vinculado ao capítulo PMI-DF, **quando** concluo o cadastro, **então** meu `status_filiacao` fica `ATIVO` e vejo a mensagem de boas-vindas de filiado.
- **Dado** que informo um PMI ID válido mas **sem** vínculo com o capítulo PMI-DF, **quando** concluo, **então** a conta é criada como `NAO_FILIADO` e recebo orientação sobre como adicionar o PMI-DF como capítulo na minha filiação.
- **Dado** que a integração com o PMI está indisponível, **quando** concluo o cadastro, **então** fico como `PENDENTE_VALIDACAO`, com aviso de que a confirmação ocorrerá em até 24h, e o batch resolve.

**Regras de negócio**

- RN-01.02.1 — Declarar-se filiado **nunca** concede acesso sozinho; o acesso depende da confirmação na base do PMI ou de concessão manual do Admin.
- RN-01.02.2 — `PENDENTE_VALIDACAO` recebe permissões de `Pessoa` (nunca de `Filiado`) enquanto não confirmado.
- RN-01.02.3 — O nome retornado pelo PMI deve ter similaridade mínima (Levenshtein normalizado ≥ 0,7) com o nome informado; abaixo disso, marcar para revisão manual do Admin.

**Notas técnicas**

- `POST /api/filiacao/validar` `{ pmi_id, email }` → `{ status, filiacao_inicio, filiacao_expiracao, chapter_code }`.
- Adapter `ThoughtSpotClient` isolado atrás de interface `ValidadorFiliacao`, permitindo _mock_ em testes e troca de fornecedor.
- Toda chamada registrada em `integracao_log`.

**Dependências:** US-01.01, US-01.07 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-01.03 — Login com e-mail e senha

> **Como** pessoa cadastrada
> **Quero** entrar com meu e-mail e senha
> **Para** acessar minha área de membro

**Critérios de aceite**

- **Dado** credenciais válidas, **quando** faço login, **então** recebo `access_token` (15 min) e `refresh_token` (7 dias, httpOnly) e sou levado à home do membro.
- **Dado** credenciais inválidas, **quando** tento entrar, **então** vejo mensagem genérica de erro, sem indicar qual campo falhou.
- **Dado** 5 tentativas falhas consecutivas, **quando** tento novamente, **então** a conta entra em bloqueio temporário progressivo (1, 5, 15 min) e recebo e-mail de alerta.

**Notas técnicas**

- `POST /api/auth/login`, `POST /api/auth/refresh`, `POST /api/auth/logout`.
- `refresh_token` rotativo com detecção de reuso (invalida toda a família de tokens).
- Claims mínimas no JWT: `sub`, `roles`, `status_filiacao`, `exp`. **O status no token tem TTL curto justamente para refletir rebaixamentos rápido.**
- Comparação de senha em tempo constante mesmo quando o usuário não existe (evita _user enumeration_ por timing).

**Dependências:** US-01.01 · **Prioridade:** Must · **Estimativa:** 5 SP

---

#### US-01.04 — Entrar com o Google

> **Como** visitante
> **Quero** criar conta e entrar usando minha conta Google
> **Para** não preencher formulários longos nem gerenciar mais uma senha

**Critérios de aceite**

- **Dado** que clico em "Entrar com o Google" e autorizo, **quando** o e-mail não existe na base, **então** a conta é criada já com e-mail verificado e sou direcionado a completar o perfil.
- **Dado** que o e-mail do Google já existe como conta com senha, **quando** autorizo, **então** **não** entro automaticamente: recebo a solicitação de confirmar a vinculação (senha atual ou e-mail de confirmação).
- **Dado** que já vinculei o Google antes, **quando** entro, **então** o login é direto.

**Regras de negócio**

- RN-01.04.1 — Vinculação automática sem confirmação é proibida (risco de sequestro de conta).
- RN-01.04.2 — Uma pessoa pode ter no máximo uma identidade Google vinculada.

**Notas técnicas**

- Fluxo Authorization Code + PKCE; validar `state` e `nonce`; verificar assinatura do `id_token` contra o JWKS do Google.
- Chave única em `identidade_externa (provedor, subject_id)` — usar o `sub` do Google, **não** o e-mail, como identificador estável.
- `GET /api/auth/google/iniciar` e `GET /api/auth/google/callback`.

**Dependências:** US-01.01 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-01.05 — Recuperação de senha por e-mail

> **Como** pessoa que esqueceu a senha
> **Quero** redefini-la por e-mail
> **Para** recuperar o acesso sem suporte humano

**Critérios de aceite**

- **Dado** que solicito recuperação, **quando** submeto qualquer e-mail, **então** vejo sempre a mesma mensagem neutra de confirmação.
- **Dado** que recebo o link, **quando** o utilizo dentro de 60 minutos, **então** consigo definir nova senha e todas as minhas sessões ativas são encerradas.
- **Dado** que o link já foi usado ou expirou, **quando** o acesso, **então** vejo erro claro e a opção de solicitar novo link.

**Notas técnicas**

- `POST /api/auth/recuperacao` e `POST /api/auth/recuperacao/confirmar`.
- Token de 32 bytes aleatórios; **armazenar apenas o hash SHA-256**; uso único; expiração 60 min.
- Invalidar todos os `refresh_token` da pessoa após redefinição e notificar por e-mail.
- _Rate limit_ 3 solicitações/e-mail/hora.

**Dependências:** US-01.01 · **Prioridade:** Must · **Estimativa:** 5 SP

---

#### US-01.06 — Verificação de e-mail (double opt-in)

> **Como** plataforma
> **Quero** confirmar a posse do e-mail informado
> **Para** garantir comunicação confiável e impedir uso de e-mails de terceiros

**Critérios de aceite**

- **Dado** que me cadastrei, **quando** clico no link do e-mail em até 72h, **então** `email_verificado_em` é preenchido e ganho acesso completo ao meu nível.
- **Dado** que não verifiquei, **quando** tento resgatar benefício, submeter badge ou vincular e-mail Sympla, **então** sou bloqueado com CTA para reenviar a verificação.

**Notas técnicas**

- `POST /api/auth/verificar-email/reenviar`, `GET /api/auth/verificar-email?token=`.
- Contas criadas via Google nascem verificadas (`email_verified` do `id_token` = true).

**Dependências:** US-01.01 · **Prioridade:** Must · **Estimativa:** 3 SP

---

#### US-01.07 — Reconciliação diária do status de filiação

> **Como** capítulo PMI-DF
> **Quero** que o status de filiação de toda a base seja reconciliado diariamente com o PMI
> **Para** que benefícios exclusivos sejam entregues apenas a quem tem filiação vigente

**Critérios de aceite**

- **Dado** que o job roda às 03:00, **quando** conclui, **então** todas as pessoas com `pmi_id` têm `filiacao_validada_em` atualizado e o resumo é registrado.
- **Dado** que um filiado consta como expirado na base do PMI, **quando** o job processa, **então** seu status vira `EXPIRADO`, ele perde acesso exclusivo e recebe e-mail com orientação de renovação.
- **Dado** que uma pessoa `PENDENTE_VALIDACAO` é encontrada como ativa, **quando** o job processa, **então** vira `ATIVO`, recebe e-mail de boas-vindas e dispara a concessão do badge de filiação.
- **Dado** que a integração falha, **quando** o job executa, **então** **nenhum** status é rebaixado, o erro é registrado e o admin é notificado.

**Regras de negócio**

- RN-01.07.1 — Rebaixamento só ocorre com resposta positiva e explícita de expiração. Ausência de dado ≠ expiração.
- RN-01.07.2 — Status `CONCEDIDO_MANUALMENTE` é imune ao rebaixamento automático (com data de revisão obrigatória).
- RN-01.07.3 — Toda transição de status gera registro em `log_auditoria` com origem.

**Notas técnicas**

- Job idempotente, com _lock_ distribuído (Redis) para não executar em paralelo em múltiplas instâncias.
- Processamento em lotes de 500 com _upsert_ em `pmi_filiado_espelho`.
- Endpoint administrativo `POST /api/admin/filiacao/reconciliar` para execução manual sob demanda.
- Alerta se a variação de filiados ativos entre execuções ultrapassar ±15% (indício de falha na origem, não de churn real).

**Dependências:** US-01.02 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-01.08 — Gestão de sessões e logout

> **Como** pessoa autenticada
> **Quero** encerrar minha sessão e ver onde estou conectado
> **Para** proteger minha conta em dispositivos compartilhados

**Critérios de aceite**

- **Dado** que clico em sair, **quando** confirmo, **então** o refresh token é revogado e sou redirecionado à home pública.
- **Dado** que acesso "Segurança" no perfil, **quando** a página carrega, **então** vejo dispositivo, navegador, IP aproximado e último acesso de cada sessão ativa, com opção de encerrar individualmente ou todas.

**Dependências:** US-01.03 · **Prioridade:** Should · **Estimativa:** 5 SP

---

### ÉP-02 — Perfil do Membro e Diretório da Comunidade

**Objetivo:** transformar o cadastro em identidade profissional dentro da comunidade e capturar as preferências que alimentam os demais módulos.

**Entidades:** `pessoa`, `email_alternativo`, `consentimento`, `visualizacao_conteudo`.

#### US-02.01 — Editar meus dados pessoais

> **Como** pessoa cadastrada
> **Quero** editar meus dados pessoais
> **Para** manter meu perfil correto e atualizado

**Critérios de aceite**

- **Dado** que estou em "Meu perfil", **quando** altero telefone, cidade/UF, escolaridade, data de nascimento ou gênero e salvo, **então** os dados são persistidos e vejo confirmação.
- **Dado** que altero o e-mail principal, **quando** salvo, **então** o novo e-mail entra como pendente e só se torna efetivo após verificação, com notificação enviada também ao e-mail antigo.
- **Dado** que sou filiado, **quando** visualizo os campos `nome` e `pmi_id`, **então** eles aparecem em modo leitura, com nota de que vêm da base do PMI.

**Notas técnicas**

- `GET /api/perfil`, `PATCH /api/perfil` (atualização parcial).
- Campos sincronizados do PMI são _read-only_ na aplicação para evitar divergência com a fonte de verdade.
- Toda alteração gera `log_auditoria` (entidade `pessoa`).

**CRUD:** Create (US-01.01) · **Read** · **Update** · Delete (US-10.04)

**Dependências:** US-01.03 · **Prioridade:** Must · **Estimativa:** 5 SP

---

#### US-02.02 — Foto de perfil e mini bio

> **Como** membro da comunidade
> **Quero** adicionar uma foto e uma mini bio
> **Para** ser reconhecido pelos demais membros do capítulo

**Critérios de aceite**

- **Dado** que seleciono uma imagem JPG/PNG/WebP de até 5 MB, **quando** confirmo o recorte quadrado, **então** a foto é salva e exibida em até 2 segundos.
- **Dado** que envio arquivo com extensão ou tipo MIME não permitido, **quando** submeto, **então** o upload é recusado com mensagem clara.
- **Dado** que escrevo mini bio de até 600 caracteres, **quando** salvo, **então** o texto é armazenado já sanitizado (sem HTML executável).
- **Dado** que removo minha foto, **quando** confirmo, **então** volta o avatar padrão com minhas iniciais.

**Notas técnicas**

- Upload direto ao object storage via **URL pré-assinada** (`POST /api/perfil/foto/upload-url`), evitando trafegar binário pela API.
- Validar _magic bytes_ do arquivo, não apenas a extensão. Remover metadados EXIF (podem conter geolocalização).
- Gerar 3 tamanhos (48px, 128px, 400px) por processamento assíncrono; servir via CDN.
- Sanitização da bio no backend (allowlist), nunca só no frontend.

**Dependências:** US-02.01 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-02.03 — Sinalizar disponibilidade para oportunidades

> **Como** profissional em transição de carreira
> **Quero** sinalizar que estou aberto a oportunidades
> **Para** ser encontrado por recrutadores e pela diretoria do capítulo

**Critérios de aceite**

- **Dado** que ativo "Aberto a oportunidades", **quando** salvo, **então** um selo discreto aparece no meu perfil.
- **Dado** que ativo a opção, **quando** leio a explicação, **então** entendo exatamente quem consegue ver essa informação.
- **Dado** que desativo, **quando** salvo, **então** o selo desaparece imediatamente de todas as visualizações.

**Regras de negócio**

- RN-02.03.1 — Padrão desativado (_privacy by default_).
- RN-02.03.2 — No MVP, a informação é visível a Admins e aos demais membros autenticados. Nunca a visitantes não autenticados.

**Dependências:** US-02.01 · **Prioridade:** Should · **Estimativa:** 2 SP

---

#### US-02.04 — Cadastrar e verificar e-mails alternativos do Sympla

> **Como** membro que se inscreve em eventos com outro e-mail
> **Quero** cadastrar e comprovar meus e-mails alternativos
> **Para** que meu histórico de participação seja reconhecido corretamente

**Critérios de aceite**

- **Dado** que informo um e-mail alternativo, **quando** salvo, **então** ele fica com status "aguardando verificação" e um e-mail de confirmação é enviado ao endereço informado.
- **Dado** que clico no link de confirmação, **quando** o token é válido, **então** o e-mail é marcado como verificado e as participações históricas com aquele endereço são vinculadas retroativamente ao meu perfil.
- **Dado** que informo um e-mail já verificado por outra pessoa, **quando** salvo, **então** a operação é recusada com orientação de contato ao suporte.
- **Dado** que removo um e-mail alternativo, **quando** confirmo, **então** as participações associadas voltam ao estado não vinculado e minha pontuação é recalculada.

**Regras de negócio**

- RN-02.04.1 — **E-mail não verificado nunca gera vínculo de participação nem pontuação.** (Mitigação do risco A-2.)
- RN-02.04.2 — Máximo de 3 e-mails alternativos por pessoa.
- RN-02.04.3 — Um e-mail verificado é exclusivo de uma pessoa em toda a base.

**Notas técnicas**

- `POST /api/perfil/emails`, `GET /api/perfil/emails`, `DELETE /api/perfil/emails/{id}`, `POST /api/perfil/emails/{id}/reenviar-verificacao`.
- Ao verificar, disparar job `VincularParticipacoesOrfas(email)`.
- Ao remover, disparar `DesvincularParticipacoes(email)` + `RecalcularPontuacao(pessoa_id)`.

**CRUD completo:** Create · Read · Update (status) · Delete

**Dependências:** US-01.06, US-05.05 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-02.05 — Preferências de comunicação e gamificação

> **Como** membro
> **Quero** escolher se participo da gamificação e se recebo e-mail marketing
> **Para** controlar minha exposição e o volume de mensagens que recebo

**Critérios de aceite**

- **Dado** que estou em "Preferências", **quando** ativo/desativo cada opção, **então** a escolha é salva com data e hora e registrada como consentimento.
- **Dado** que desativo a gamificação, **quando** salvo, **então** meu nome deixa de aparecer no ranking público, embora meus badges já conquistados permaneçam no meu perfil privado.
- **Dado** que desativo e-mail marketing, **quando** salvo, **então** deixo de receber comunicações promocionais, mas continuo recebendo e-mails transacionais (segurança, filiação, resgates).

**Regras de negócio**

- RN-02.05.1 — Ambas as opções são _opt-in_ (padrão desativado).
- RN-02.05.2 — Distinção obrigatória entre e-mail transacional (legítimo interesse) e marketing (consentimento).
- RN-02.05.3 — Todo link de e-mail marketing deve conter descadastro em um clique.

**Notas técnicas**

- `PATCH /api/perfil/preferencias`; cada alteração insere nova linha em `consentimento` (histórico imutável, nunca _update_).

**Dependências:** US-02.01 · **Prioridade:** Must · **Estimativa:** 5 SP

---

#### US-02.06 — Admin: listar, buscar e filtrar pessoas

> **Como** administrador do capítulo
> **Quero** consultar toda a base de pessoas com filtros
> **Para** apoiar decisões de gestão e ações da diretoria

**Critérios de aceite**

- **Dado** que acesso "Pessoas" no console, **quando** a lista carrega, **então** vejo nome, e-mail, status de filiação, voluntário (sim/não), data de cadastro e último acesso, paginados.
- **Dado** que busco por nome, e-mail ou PMI ID, **quando** digito ao menos 3 caracteres, **então** os resultados filtram com _debounce_.
- **Dado** que aplico filtros combinados (status de filiação + voluntário + período de cadastro + aberto a oportunidades), **quando** aplico, **então** a lista reflete todos os critérios simultaneamente.
- **Dado** que exporto o resultado, **quando** confirmo, **então** recebo CSV e a ação fica registrada na auditoria com o total de registros exportados.

**Notas técnicas**

- `GET /api/admin/pessoas?q=&status_filiacao=&voluntario=&page=&size=&sort=`.
- Índices: `pessoa(email_principal)`, `pessoa(status_filiacao)`, GIN em `nome_completo` para busca por trigrama.
- Export limitado a 5.000 linhas por vez e gerado de forma assíncrona acima de 1.000 registros.
- **Exportação de base pessoal é ação sensível** — exige papel `SUPER_ADMIN` e registro obrigatório em auditoria.

**Dependências:** US-10.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-02.07 — Admin: conceder ou revogar filiação manualmente

> **Como** administrador
> **Quero** marcar manualmente uma pessoa como filiada
> **Para** atender casos em que a base do PMI está desatualizada ou o vínculo com o capítulo ainda não foi processado

**Critérios de aceite**

- **Dado** que abro o detalhe de uma pessoa, **quando** clico em "Conceder filiação manual", **então** devo informar justificativa e data de revisão (padrão: 90 dias) antes de confirmar.
- **Dado** que concedo, **quando** salvo, **então** o status vira `CONCEDIDO_MANUALMENTE`, o acesso exclusivo é liberado imediatamente e a ação fica registrada com meu usuário, data, hora e justificativa.
- **Dado** que a data de revisão vence, **quando** o job diário roda, **então** o admin recebe notificação para reavaliar o caso.
- **Dado** que revogo a concessão, **quando** confirmo, **então** a pessoa volta ao status determinado pela última reconciliação com o PMI.

**Regras de negócio**

- RN-02.07.1 — Concessão manual **não** é sobrescrita pelo job automático (RN-01.07.2), mas exige revisão periódica.
- RN-02.07.2 — Justificativa é campo obrigatório de no mínimo 20 caracteres.

**Notas técnicas**

- `POST /api/admin/pessoas/{id}/filiacao-manual` e `DELETE /api/admin/pessoas/{id}/filiacao-manual`.
- Relatório dedicado "Concessões manuais ativas" no console.

**Dependências:** US-02.06, US-01.07 · **Prioridade:** Must · **Estimativa:** 5 SP

---

### ÉP-03 — Voluntariado e Organograma do Capítulo

**Objetivo:** dar visibilidade a quem constrói o capítulo e organizar a estrutura de governança por gestão.

**Entidades:** `diretoria`, `cargo`, `vinculo_voluntario`.

#### US-03.01 — Admin: cadastrar diretorias e cargos

> **Como** administrador
> **Quero** manter a estrutura de diretorias e cargos do capítulo
> **Para** que o organograma reflita a organização real de cada gestão

**Critérios de aceite**

- **Dado** que acesso "Estrutura organizacional", **quando** crio uma diretoria com nome, sigla, descrição e ordem de exibição, **então** ela fica disponível para vinculação.
- **Dado** que tento excluir uma diretoria com voluntários ativos, **quando** confirmo, **então** a exclusão é bloqueada com a orientação de reatribuir ou encerrar os vínculos antes.
- **Dado** que inativo uma diretoria, **quando** salvo, **então** ela deixa de aparecer em novas vinculações mas permanece no histórico das gestões anteriores.

**Notas técnicas**

- CRUD completo em `/api/admin/diretorias` e `/api/admin/cargos`.
- Exclusão é **soft delete** (`ativa = false`); _hard delete_ apenas se nunca houve vínculo.
- `diretoria_pai_id` permite subdiretorias/coordenações (profundidade máxima recomendada: 3 níveis).

**CRUD completo:** Create · Read · Update · Delete (lógico)

**Dependências:** US-10.01 · **Prioridade:** Should · **Estimativa:** 5 SP

---

#### US-03.02 — Admin: designar pessoa como voluntária

> **Como** administrador
> **Quero** marcar uma pessoa como voluntária informando cargo e diretoria
> **Para** que ela seja reconhecida e apareça no organograma do capítulo

**Critérios de aceite**

- **Dado** que abro o detalhe de uma pessoa e clico em "Tornar voluntário(a)", **quando** o formulário abre, **então** cargo, diretoria e data de início são obrigatórios (gestão preenchida automaticamente com o ano vigente).
- **Dado** que confirmo a designação, **quando** salvo, **então** a pessoa passa a constar no organograma e recebe e-mail de boas-vindas ao time de voluntários.
- **Dado** que a pessoa já tem vínculo ativo em outra diretoria, **quando** designo novamente, **então** posso escolher entre encerrar o vínculo anterior ou manter ambos (voluntário atuando em duas frentes).
- **Dado** que encerro um vínculo, **quando** informo a data de fim, **então** a pessoa sai do organograma atual e o registro é preservado no histórico da gestão.

**Regras de negócio**

- RN-03.02.1 — Ser voluntário é independente de ser filiado (voluntários podem não ser filiados).
- RN-03.02.2 — `data_fim` nula significa vínculo ativo.
- RN-03.02.3 — A designação dispara a avaliação do badge de voluntariado (ÉP-08).

**Notas técnicas**

- `POST /api/admin/pessoas/{id}/vinculos`, `PATCH /api/admin/vinculos/{id}` (encerrar), `GET /api/admin/vinculos?gestao=`.
- Evento de domínio `VoluntarioDesignado` consumido pelo módulo de badges.

**Dependências:** US-03.01, US-02.06 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-03.03 — Visualizar o organograma do capítulo

> **Como** membro da comunidade
> **Quero** ver quem compõe a diretoria e os times de voluntários
> **Para** saber a quem recorrer e reconhecer quem contribui

**Critérios de aceite**

- **Dado** que acesso a página "Quem somos", **quando** ela carrega, **então** vejo os voluntários ativos agrupados por diretoria, com foto, nome e cargo, na ordem de exibição configurada.
- **Dado** que seleciono uma gestão anterior no filtro, **quando** aplico, **então** vejo a composição histórica daquele período.
- **Dado** que um voluntário não tem foto, **quando** a página renderiza, **então** é exibido o avatar padrão com iniciais.

**Notas técnicas**

- `GET /api/organograma?gestao=2026` — endpoint público, retornando **apenas** nome, foto, cargo e diretoria. Nunca e-mail, telefone ou dados demográficos.
- Cachear resposta por 15 minutos (conteúdo muda raramente).

**Dependências:** US-03.02 · **Prioridade:** Could · **Estimativa:** 5 SP

---

### ÉP-04 — Clube de Vantagens do Filiado

**Objetivo:** materializar o retorno financeiro e prático da filiação, com rastreabilidade de uso.

**Entidades:** `beneficio`, `cupom`, `resgate_beneficio`.

#### US-04.01 — Admin: cadastrar e manter benefícios

> **Como** administrador de benefícios
> **Quero** cadastrar os benefícios negociados com parceiros
> **Para** disponibilizá-los aos filiados

**Critérios de aceite**

- **Dado** que crio um benefício, **quando** informo título, legenda, imagem, parceiro, categoria e tipo de resgate, **então** ele é salvo como `RASCUNHO`.
- **Dado** que escolho o tipo "cupom compartilhado", **quando** salvo, **então** o campo de código único é obrigatório.
- **Dado** que escolho "instrução de texto", **quando** salvo, **então** o campo de instrução (editor com formatação básica) é obrigatório.
- **Dado** que escolho "cupom único por pessoa", **quando** salvo, **então** devo importar a lista de códigos antes de publicar.
- **Dado** que publico um benefício, **quando** confirmo, **então** ele passa a aparecer para os filiados imediatamente, respeitando a vigência configurada.
- **Dado** que edito um benefício publicado, **quando** salvo, **então** a versão anterior fica registrada na auditoria.
- **Dado** que arquivo um benefício, **quando** confirmo, **então** ele some da listagem mas o histórico de resgates permanece íntegro.

**Regras de negócio**

- RN-04.01.1 — Benefício fora da vigência não aparece na listagem ativa, mesmo publicado.
- RN-04.01.2 — Exclusão física é proibida; usar arquivamento (integridade referencial com resgates).
- RN-04.01.3 — Campos obrigatórios variam conforme `tipo_resgate` (validação condicional no backend).

**Notas técnicas**

- `POST|GET|PATCH /api/admin/beneficios`, `POST /api/admin/beneficios/{id}/publicar`, `POST /api/admin/beneficios/{id}/arquivar`.
- Importação de cupons via CSV: `POST /api/admin/beneficios/{id}/cupons/importar` (validação de duplicidade + relatório de linhas rejeitadas).
- Imagem por URL pré-assinada, com as mesmas validações de US-02.02.

**CRUD completo:** Create · Read · Update · Delete (arquivamento lógico)

**Dependências:** US-10.01 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-04.02 — Filiado: navegar pelo clube de vantagens

> **Como** filiado
> **Quero** ver todos os benefícios disponíveis
> **Para** aproveitar as vantagens da minha filiação

**Critérios de aceite**

- **Dado** que sou filiado ativo, **quando** acesso o clube, **então** vejo os benefícios vigentes em cards com imagem, título, legenda e categoria.
- **Dado** que filtro por categoria ou busco por texto, **quando** aplico, **então** a listagem é filtrada.
- **Dado** que abro um benefício, **quando** a página carrega, **então** vejo a descrição completa e o botão de resgate.
- **Dado** que um benefício está próximo do fim da vigência (≤7 dias), **quando** visualizo, **então** vejo destaque de urgência.

**Notas técnicas**

- `GET /api/beneficios` (retorna só metadados — **jamais** o código do cupom) e `GET /api/beneficios/{id}`.
- O código de resgate só é retornado pelo endpoint de resgate (US-04.03), nunca na listagem. Isso impede vazamento por inspeção de rede.

**Dependências:** US-04.01, US-01.03 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-04.03 — Filiado: resgatar benefício com registro de histórico

> **Como** filiado
> **Quero** resgatar um benefício e ver meu histórico de resgates
> **Para** usar a vantagem e acompanhar o que já aproveitei

**Critérios de aceite**

- **Dado** que clico em "Resgatar", **quando** sou filiado ativo com e-mail verificado, **então** o código/instrução é exibido, um registro é gravado em `resgate_beneficio` e recebo o mesmo conteúdo por e-mail.
- **Dado** que o benefício usa cupom único, **quando** resgato, **então** um código `DISPONIVEL` é reservado atomicamente para mim e marcado como `CONSUMIDO`.
- **Dado** que não há mais cupons disponíveis, **quando** tento resgatar, **então** vejo "Benefício esgotado" e nenhum registro de resgate é criado.
- **Dado** que já atingi o limite de resgates daquele benefício, **quando** tento novamente, **então** o botão exibe "Já resgatado" e mostra meu código anterior.
- **Dado** que acesso "Meus resgates", **quando** a página carrega, **então** vejo benefício, data e código de cada resgate, do mais recente ao mais antigo.

**Regras de negócio**

- RN-04.03.1 — Somente `status_filiacao IN (ATIVO, CONCEDIDO_MANUALMENTE)` pode resgatar. Verificação no **backend**, no momento da requisição.
- RN-04.03.2 — O resgate grava um _snapshot_ do status de filiação no momento da ação (prova de elegibilidade para auditoria com o parceiro).
- RN-04.03.3 — Se a pessoa perder a filiação, o histórico é preservado, mas ela deixa de ver os códigos.

**Notas técnicas**

- `POST /api/beneficios/{id}/resgatar` — **idempotente por (pessoa_id, beneficio_id)**: repetir a chamada devolve o mesmo resgate, não cria outro.
- Reserva de cupom com `SELECT ... FOR UPDATE SKIP LOCKED` dentro de transação, evitando condição de corrida em resgates simultâneos.
- _Rate limit_ de 10 resgates/pessoa/hora como proteção contra automação.
- `GET /api/perfil/resgates`.

**CRUD:** Create (resgate) · Read (histórico) · Sem update/delete pelo usuário — registro imutável

**Dependências:** US-04.02, US-01.06 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-04.04 — Admin: relatório de resgates

> **Como** administrador
> **Quero** acompanhar quantos resgates cada benefício teve
> **Para** negociar renovações com parceiros usando dados reais

**Critérios de aceite**

- **Dado** que acesso o relatório, **quando** filtro por período e benefício, **então** vejo total de resgates, filiados únicos e taxa de utilização sobre a base ativa.
- **Dado** que exporto, **quando** confirmo, **então** recebo CSV com benefício, parceiro, data e identificador anonimizado do filiado.

**Notas técnicas**

- `GET /api/admin/relatorios/resgates?de=&ate=&beneficio_id=`.
- **Exportação padrão é pseudonimizada.** Dados identificáveis exigem justificativa e papel `SUPER_ADMIN` (minimização LGPD).

**Dependências:** US-04.03 · **Prioridade:** Should · **Estimativa:** 5 SP

---

### ÉP-05 — Agenda de Eventos e Trilha de Participação

**Objetivo:** ser a fonte única da agenda do capítulo e transformar presença em histórico consultável.

**Entidades:** `evento`, `participacao_evento`, `sincronizacao_sympla`.

#### US-05.01 — Admin: cadastrar evento manualmente

> **Como** gestor de eventos
> **Quero** cadastrar eventos manualmente
> **Para** divulgar encontros que não estão no Sympla

**Critérios de aceite**

- **Dado** que crio um evento, **quando** informo título, legenda, descrição, data/hora de início e fim, formato e visibilidade, **então** ele é salvo como rascunho.
- **Dado** que o formato é `PRESENCIAL` ou `HIBRIDO`, **quando** salvo, **então** o campo de local é obrigatório.
- **Dado** que informo data de fim anterior à de início, **quando** salvo, **então** recebo erro de validação.
- **Dado** que publico, **quando** confirmo, **então** o evento aparece na agenda pública.

**Notas técnicas**

- CRUD completo em `/api/admin/eventos`; `origem = MANUAL`.
- Datas armazenadas em UTC (`timestamptz`) e exibidas em `America/Sao_Paulo`.
- Eventos com `origem = SYMPLA` têm campos sincronizados bloqueados para edição (evita sobrescrita no próximo sync); apenas `visibilidade` e `destaque` são editáveis localmente.

**CRUD completo:** Create · Read · Update · Delete (cancelamento lógico com motivo)

**Dependências:** US-10.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-05.02 — Sincronizar eventos do Sympla

> **Como** capítulo
> **Quero** que os eventos publicados no Sympla apareçam automaticamente na plataforma
> **Para** eliminar cadastro duplicado e divergência de informação

**Critérios de aceite**

- **Dado** que o job roda a cada 6 horas, **quando** encontra evento novo no Sympla, **então** cria o registro com `origem = SYMPLA` e `sympla_event_id`, publicado automaticamente.
- **Dado** que um evento já sincronizado teve título ou data alterados no Sympla, **quando** o job roda, **então** os campos sincronizados são atualizados sem perder configurações locais.
- **Dado** que um evento foi despublicado no Sympla, **quando** o job detecta, **então** ele é marcado como cancelado na plataforma, preservando o histórico de participação.
- **Dado** que a API do Sympla falha, **quando** o job roda, **então** o erro é registrado, o admin é notificado e nenhum evento existente é apagado.

**Notas técnicas**

- Ver seção 5.2 para o algoritmo e as regras de resiliência.
- `POST /api/admin/integracoes/sympla/sincronizar` para execução manual; tela de log das últimas execuções.

**Dependências:** US-05.01 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-05.03 — Consultar a agenda de eventos

> **Como** pessoa ou filiado
> **Quero** ver os próximos eventos do capítulo
> **Para** planejar minha participação

**Critérios de aceite**

- **Dado** que acesso a agenda, **quando** ela carrega, **então** vejo os próximos eventos ordenados por data, com título, legenda, data/hora, formato e imagem.
- **Dado** que alterno para a visão de calendário mensal, **quando** navego entre meses, **então** os eventos são exibidos nos respectivos dias.
- **Dado** que filtro por formato ou período, **quando** aplico, **então** a listagem responde ao filtro.
- **Dado** que abro um evento, **quando** a página carrega, **então** vejo a descrição completa e o botão de inscrição que me leva ao Sympla em nova aba.
- **Dado** que sou visitante não autenticado, **quando** acesso a agenda, **então** vejo os eventos públicos normalmente (a agenda é vitrine do capítulo).

**Notas técnicas**

- `GET /api/eventos?de=&ate=&formato=&page=`; endpoint público, cache de 5 min.
- Eventos com `visibilidade = EXCLUSIVO_FILIADO` aparecem para não filiados apenas como _teaser_ bloqueado (ÉP-09).
- Fornecer `.ics` para adicionar ao calendário pessoal: `GET /api/eventos/{id}/ics`.

**Dependências:** US-05.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-05.04 — Sincronizar participantes e vincular a pessoas

> **Como** plataforma
> **Quero** importar a lista de participantes de cada evento do Sympla
> **Para** construir o histórico de participação e alimentar o reconhecimento

**Critérios de aceite**

- **Dado** que um evento ocorreu, **quando** o job de participantes roda, **então** cada inscrito é gravado em `participacao_evento` com status de presença.
- **Dado** que o e-mail do participante corresponde ao e-mail principal ou a um e-mail alternativo **verificado**, **quando** o job processa, **então** a participação é vinculada àquela pessoa.
- **Dado** que o e-mail não corresponde a nenhuma pessoa, **quando** o job processa, **então** a participação é gravada como órfã, sem `pessoa_id`, e fica disponível para conciliação futura.
- **Dado** que o job é reexecutado sobre o mesmo evento, **quando** processa, **então** nenhum registro é duplicado.

**Regras de negócio**

- RN-05.04.1 — Só participação com `status_presenca = CHECKIN` gera pontuação de engajamento. Inscrever-se e não comparecer não pontua.
- RN-05.04.2 — Vínculo por e-mail não verificado é proibido (RN-02.04.1).

**Notas técnicas**

- Ver algoritmo na seção 5.2. Evento de domínio `ParticipacaoConfirmada` publicado em fila para o módulo de gamificação (acoplamento fraco).

**Dependências:** US-05.02, US-02.04 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-05.05 — Consultar minha trilha de participação

> **Como** membro
> **Quero** ver todos os eventos do capítulo dos quais participei
> **Para** acompanhar minha jornada e comprovar meu engajamento

**Critérios de aceite**

- **Dado** que acesso "Minha trilha", **quando** a página carrega, **então** vejo os eventos que participei em ordem cronológica decrescente, com título, data e formato.
- **Dado** que nunca participei de nenhum evento, **quando** acesso, **então** vejo um estado vazio com convite para a agenda e orientação sobre cadastrar e-mails alternativos.
- **Dado** que verifico um e-mail alternativo, **quando** volto à trilha, **então** as participações antigas com aquele e-mail já aparecem.
- **Dado** que sou filiado com gamificação ativa, **quando** acesso, **então** também vejo minha pontuação e posição no ranking.

**Notas técnicas**

- `GET /api/perfil/participacoes?page=` — retorna apenas as participações da pessoa autenticada. **Nunca** aceitar `pessoa_id` por parâmetro em endpoint de usuário final (risco de IDOR).

**Dependências:** US-05.04 · **Prioridade:** Must · **Estimativa:** 5 SP

---

#### US-05.06 — Admin: conciliar participações órfãs

> **Como** administrador
> **Quero** vincular manualmente participações sem dono
> **Para** corrigir casos de e-mail divergente e não penalizar membros ativos

**Critérios de aceite**

- **Dado** que acesso "Participações não vinculadas", **quando** a lista carrega, **então** vejo evento, e-mail, nome informado no Sympla e sugestões de pessoas com nome similar.
- **Dado** que seleciono uma pessoa e confirmo o vínculo, **quando** salvo, **então** a participação é atribuída, a pontuação é recalculada e a ação fica registrada na auditoria.
- **Dado** que marco uma participação como "não é membro", **quando** confirmo, **então** ela sai da fila de conciliação.

**Notas técnicas**

- `GET /api/admin/participacoes/orfas`, `POST /api/admin/participacoes/{id}/vincular { pessoa_id }`.
- Sugestão por similaridade de nome (trigrama, `pg_trgm`) com limiar de 0,4.

**Dependências:** US-05.04 · **Prioridade:** Should · **Estimativa:** 8 SP

---

### ÉP-06 — Hub de Conteúdo Exclusivo e Conhecimento PMI

**Objetivo:** centralizar em um único lugar o conteúdo produzido pelo capítulo e o conhecimento do PMI Global.

**Entidades:** `conteudo`, `categoria_conteudo`, `visualizacao_conteudo`.

#### US-06.01 — Admin: cadastrar conteúdo

> **Como** editor de conteúdo
> **Quero** cadastrar vídeos e links com título e descrição
> **Para** disponibilizá-los na área de conteúdo

**Critérios de aceite**

- **Dado** que cadastro um conteúdo, **quando** informo título, descrição, tipo, URL, categoria e nível de acesso, **então** ele é salvo.
- **Dado** que o tipo é vídeo do YouTube, **quando** colo a URL, **então** o sistema extrai o `video_id` automaticamente e busca a thumbnail.
- **Dado** que informo uma URL inválida ou não suportada, **quando** salvo, **então** recebo erro de validação.
- **Dado** que marco um conteúdo como destaque, **quando** salvo, **então** ele aparece no topo do hub (máximo 3 destaques simultâneos).

**Notas técnicas**

- CRUD completo em `/api/admin/conteudos` + CRUD de categorias em `/api/admin/categorias-conteudo`.
- _Parser_ de URL do YouTube cobrindo `youtube.com/watch?v=`, `youtu.be/` e `youtube.com/embed/`.
- Validação de URL contra **allowlist de domínios** para links do PMI (evita uso do hub como redirecionador aberto).

**CRUD completo:** Create · Read · Update · Delete (despublicação lógica)

**Dependências:** US-10.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-06.02 — Filiado: assistir a conteúdo exclusivo

> **Como** filiado
> **Quero** assistir aos vídeos exclusivos do capítulo
> **Para** aproveitar o conteúdo que só a filiação oferece

**Critérios de aceite**

- **Dado** que sou filiado ativo, **quando** abro um vídeo exclusivo, **então** o player carrega dentro da plataforma e a visualização é registrada.
- **Dado** que **não** sou filiado, **quando** acesso o mesmo conteúdo, **então** vejo título, thumbnail e descrição resumida, com o player bloqueado e o CTA de filiação — e **a URL do vídeo não está presente em nenhuma resposta da API nem no HTML**.
- **Dado** que minha filiação expira enquanto navego, **quando** meu token é renovado, **então** perco o acesso ao player na próxima interação.

**Regras de negócio**

- RN-06.02.1 — A URL/`video_id` de conteúdo exclusivo é servida exclusivamente por endpoint autenticado e autorizado.
- RN-06.02.2 — Vídeos devem estar como "não listados" no YouTube e com domínio de incorporação restrito.

**Notas técnicas**

- `GET /api/conteudos/{id}/player` → 200 `{ embed_url }` para filiado, 403 para os demais.
- Registrar `visualizacao_conteudo` a cada concessão de acesso.
- **Limitação conhecida e aceita:** um filiado pode compartilhar a URL obtida. A mitigação é dissuasiva (marca d'água com o nome do filiado no vídeo é possível em fase futura), não técnica.

**Dependências:** US-06.01, US-09.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-06.03 — Navegar pelo catálogo de conteúdo

> **Como** membro
> **Quero** buscar e filtrar conteúdos por categoria
> **Para** encontrar rapidamente o que me interessa

**Critérios de aceite**

- **Dado** que acesso o hub, **quando** a página carrega, **então** vejo os destaques, seguidos das categorias com seus conteúdos.
- **Dado** que busco por texto, **quando** digito, **então** título e descrição são pesquisados.
- **Dado** que filtro por categoria e tipo, **quando** aplico, **então** a listagem responde.

**Notas técnicas**

- `GET /api/conteudos?categoria=&tipo=&q=&page=`. Para não filiados, itens exclusivos vêm com `bloqueado: true` e **sem** o campo `url`.

**Dependências:** US-06.01 · **Prioridade:** Should · **Estimativa:** 5 SP

---

#### US-06.04 — Central de conhecimento PMI Global

> **Como** membro
> **Quero** acessar de um só lugar os recursos do PMI Global (PMBOK, cursos, certificações)
> **Para** não me perder navegando entre vários sites do PMI

**Critérios de aceite**

- **Dado** que acesso "Conhecimento PMI", **quando** a página carrega, **então** vejo os recursos curados agrupados por tema, com título, descrição e a indicação de que abrem em site do PMI Global.
- **Dado** que clico em um recurso, **quando** confirmo, **então** sou levado à URL oficial em nova aba, com o clique registrado para métricas.
- **Dado** que o recurso exige filiação PMI, **quando** o visualizo sem ser filiado, **então** vejo essa informação de forma explícita antes de clicar.

**Notas técnicas**

- Reaproveita `conteudo` com `tipo = LINK_PMI_GLOBAL`; nenhuma tabela nova.
- Contabilizar cliques em `visualizacao_conteudo` para medir quais recursos importam.
- **Nada de fazer _scraping_ ou espelhar conteúdo do PMI Global** — apenas redirecionamento, respeitando a propriedade intelectual do PMI.

**Dependências:** US-06.01 · **Prioridade:** Should · **Estimativa:** 5 SP

---

### ÉP-07 — Vitrine de Oportunidades de Carreira

**Objetivo:** conectar a comunidade a vagas de gerenciamento de projetos com curadoria do capítulo.

**Entidades:** `vaga`.

> **Nota de escopo (A-1):** não há candidatura, funil, currículo ou triagem. É um mural curado, somente leitura para os membros. Um ATS de verdade seria um produto próprio, com esforço de outra ordem de grandeza.

#### US-07.01 — Admin: cadastrar e manter vagas

> **Como** administrador
> **Quero** cadastrar vagas enviadas por empresas e recrutadores
> **Para** divulgá-las à comunidade do capítulo

**Critérios de aceite**

- **Dado** que cadastro uma vaga, **quando** informo título, empresa, descrição e e-mail do recrutador, **então** ela é salva com um `slug` único gerado automaticamente.
- **Dado** que adiciono logo da empresa (opcional), **quando** salvo, **então** a imagem é validada e otimizada.
- **Dado** que informo data de expiração, **quando** a data chega, **então** a vaga passa automaticamente para `EXPIRADA` e sai da listagem ativa.
- **Dado** que não informo data de expiração, **quando** salvo, **então** o sistema aplica 60 dias como padrão.
- **Dado** que edito ou arquivo uma vaga, **quando** salvo, **então** a alteração é registrada na auditoria.

**Notas técnicas**

- CRUD completo em `/api/admin/vagas`.
- `slug` = _kebab-case_ de título + empresa + sufixo curto aleatório; imutável após publicação (não quebra links já compartilhados).
- Job diário de expiração automática.
- Descrição em editor rich text com sanitização por allowlist no backend.

**CRUD completo:** Create · Read · Update · Delete (arquivamento)

**Dependências:** US-10.01 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-07.02 — Consultar vagas disponíveis

> **Como** membro
> **Quero** ver as vagas publicadas com filtros
> **Para** encontrar oportunidades compatíveis com meu perfil

**Critérios de aceite**

- **Dado** que acesso a vitrine, **quando** a página carrega, **então** vejo as vagas ativas da mais recente para a mais antiga, com título, empresa, modalidade e localidade.
- **Dado** que filtro por modalidade, nível ou localidade, **quando** aplico, **então** a listagem responde.
- **Dado** que abro uma vaga, **quando** a página carrega, **então** vejo a descrição completa e como contatar o recrutador.

**Regras de negócio**

- RN-07.02.1 — Vagas são visíveis a Pessoas e Filiados igualmente (o capítulo tem interesse em amplificar o alcance).
- RN-07.02.2 — O e-mail do recrutador é exibido **apenas para usuários autenticados**, com proteção contra coleta automatizada.

**Notas técnicas**

- `GET /api/vagas?modalidade=&nivel=&q=&page=` — público.
- `GET /api/vagas/{slug}/contato` — autenticado, com _rate limit_ de 20/hora por pessoa. Evita que a vitrine vire base de e-mails para spam.

**Dependências:** US-07.01 · **Prioridade:** Should · **Estimativa:** 5 SP

---

#### US-07.03 — Página compartilhável da vaga

> **Como** membro ou recrutador
> **Quero** compartilhar o link de uma vaga em redes sociais
> **Para** ampliar o alcance da oportunidade

**Critérios de aceite**

- **Dado** que copio o link de uma vaga, **quando** o colo no LinkedIn ou WhatsApp, **então** o preview mostra título, empresa, logo e uma descrição resumida.
- **Dado** que alguém não cadastrado abre o link, **quando** a página carrega, **então** vê o conteúdo da vaga e um convite para se cadastrar na plataforma.
- **Dado** que a vaga expirou, **quando** o link é aberto, **então** vejo aviso de vaga encerrada e sugestão de outras oportunidades.

**Notas técnicas**

- URL canônica `/vagas/{slug}` com metatags Open Graph e Twitter Card renderizadas no servidor (SSR ou pré-render) — SPA pura não gera preview.
- Incluir JSON-LD `JobPosting` para indexação em buscadores.
- Botões de compartilhamento nativos (Web Share API com _fallback_).

**Dependências:** US-07.02 · **Prioridade:** Could · **Estimativa:** 5 SP

---

### ÉP-08 — Reconhecimento: Badges e Ranking de Engajamento

**Objetivo:** reconhecer publicamente a fidelidade e a participação, criando um motivo emocional para permanecer filiado.

**Entidades:** `badge`, `badge_concedido`, `submissao_badge`, `pontuacao_engajamento`.

#### US-08.01 — Admin: criar e manter badges

> **Como** administrador
> **Quero** cadastrar novos badges com arte, critério e forma de comprovação
> **Para** criar novos reconhecimentos conforme as campanhas do capítulo

**Critérios de aceite**

- **Dado** que crio um badge, **quando** informo nome, descrição, arte, tipo e modo de concessão, **então** ele é salvo.
- **Dado** que escolho o modo "submissão manual", **quando** salvo, **então** os campos "o que deve ser submetido" e "critério de aprovação" tornam-se obrigatórios.
- **Dado** que escolho o modo "automático", **quando** salvo, **então** devo selecionar uma regra pré-definida (marco de filiação, nº de eventos, voluntariado).
- **Dado** que defino vigência para um badge comemorativo, **quando** o período termina, **então** ele deixa de aceitar novas submissões, mas quem já o conquistou o mantém.

**Regras de negócio**

- RN-08.01.1 — **Badge concedido é permanente.** Nem exclusão nem inativação do badge removem a conquista de quem já a possui.
- RN-08.01.2 — Badges são exclusivos de filiados. Exceção configurável: badges de voluntariado.
- RN-08.01.3 — Arte deve ter fundo transparente e proporção 1:1 (padronização visual).

**Notas técnicas**

- CRUD completo em `/api/admin/badges`.
- `regra_json` armazena parâmetros da regra automática, por exemplo `{ "tipo": "MARCO_FILIACAO", "anos": 5 }` ou `{ "tipo": "PARTICIPACAO", "minimo_eventos": 10, "periodo": "2026" }`.
- Motor de regras com _strategy pattern_: cada tipo de regra é uma classe avaliadora registrada, permitindo novos tipos sem alterar o núcleo.

**CRUD completo:** Create · Read · Update · Delete (apenas de badges nunca concedidos)

**Dependências:** US-10.01 · **Prioridade:** Should · **Estimativa:** 13 SP

---

#### US-08.02 — Concessão automática de badges de marco de filiação

> **Como** filiado
> **Quero** receber automaticamente os badges de tempo de filiação
> **Para** ter reconhecida minha trajetória no PMI sem precisar solicitar nada

**Critérios de aceite**

- **Dado** que minha filiação é confirmada como ativa, **quando** a validação conclui, **então** recebo o badge "Filiado PMI-DF" imediatamente.
- **Dado** que completo 1, 5 ou 10 anos de filiação (contados de `filiacao_inicio`), **quando** o job diário roda, **então** recebo o badge correspondente e uma notificação por e-mail.
- **Dado** que já possuo um badge, **quando** o job roda novamente, **então** ele **não** é concedido em duplicidade.
- **Dado** que o PMI não fornece a data de início da filiação, **quando** o job avalia, **então** o caso entra em fila de concessão manual para o admin.

**Regras de negócio**

- RN-08.02.1 — Tempo de filiação é contínuo desde `filiacao_inicio` do PMI. Interrupções e relações de continuidade devem ser validadas com a diretoria (**questão em aberto Q-4**).
- RN-08.02.2 — Perder a filiação **não** remove badges já conquistados (RN-08.01.1).

**Notas técnicas**

- Job diário `AvaliarBadgesAutomaticos`, idempotente, com chave única em `badge_concedido (badge_id, pessoa_id)` — a própria constraint garante a não duplicidade mesmo em corrida.
- Reagir também ao evento `FiliacaoConfirmada` para concessão imediata (sem esperar o job).

**Dependências:** US-08.01, US-01.07 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-08.03 — Filiado: submeter evidência para conquistar um badge

> **Como** filiado
> **Quero** enviar a comprovação de uma missão
> **Para** conquistar um badge comemorativo

**Critérios de aceite**

- **Dado** que acesso um badge de submissão dentro da vigência, **quando** clico em "Enviar comprovação", **então** vejo claramente o que precisa ser submetido.
- **Dado** que envio texto e/ou arquivo e/ou URL conforme exigido, **quando** confirmo, **então** a submissão fica `PENDENTE` e recebo confirmação por e-mail.
- **Dado** que já tenho submissão pendente para aquele badge, **quando** tento enviar outra, **então** sou impedido, com opção de editar a existente.
- **Dado** que minha submissão foi reprovada, **quando** acesso o badge, **então** vejo a justificativa e posso submeter novamente.

**Notas técnicas**

- `POST /api/badges/{id}/submissoes`, `PATCH /api/badges/submissoes/{id}`, `GET /api/perfil/submissoes`.
- Anexos: PDF, PNG, JPG até 10 MB, via URL pré-assinada, com validação de _magic bytes_ e varredura antivírus antes de disponibilizar ao admin.
- Arquivos armazenados em bucket privado; admin acessa por URL pré-assinada de curta duração.

**CRUD:** Create · Read · Update (enquanto pendente) · Delete (cancelamento pelo autor enquanto pendente)

**Dependências:** US-08.01, US-01.06 · **Prioridade:** Could · **Estimativa:** 8 SP

---

#### US-08.04 — Admin: avaliar submissões de badges

> **Como** administrador
> **Quero** analisar e aprovar ou reprovar as submissões
> **Para** garantir que o reconhecimento seja legítimo

**Critérios de aceite**

- **Dado** que acesso a fila de submissões, **quando** a lista carrega, **então** vejo pendentes primeiro, com badge, pessoa, data e prévia da evidência.
- **Dado** que aprovo uma submissão, **quando** confirmo, **então** o badge é concedido, a pessoa é notificada e o registro guarda meu usuário e a data.
- **Dado** que reprovo, **quando** informo a justificativa obrigatória, **então** a pessoa é notificada com o motivo e pode submeter novamente.
- **Dado** que existem submissões pendentes há mais de 7 dias, **quando** acesso o console, **então** vejo um alerta de SLA.

**Notas técnicas**

- `GET /api/admin/submissoes?status=`, `POST /api/admin/submissoes/{id}/aprovar`, `POST /api/admin/submissoes/{id}/reprovar { justificativa }`.
- Transição de status protegida por máquina de estados: `PENDENTE → EM_ANALISE → APROVADA|REPROVADA`. Nenhuma transição fora do fluxo é aceita.
- Toda avaliação vai para `log_auditoria`.

**Dependências:** US-08.03 · **Prioridade:** Could · **Estimativa:** 8 SP

---

#### US-08.05 — Vitrine de badges no perfil

> **Como** filiado
> **Quero** ver os badges que conquistei e os que ainda posso conquistar
> **Para** me motivar a participar mais da comunidade

**Critérios de aceite**

- **Dado** que acesso "Minhas conquistas", **quando** a página carrega, **então** vejo meus badges com data de conquista e os disponíveis em escala de cinza com o critério para obtê-los.
- **Dado** que conquisto um badge novo, **quando** acesso a plataforma, **então** vejo uma celebração na primeira visualização.
- **Dado** que não sou filiado, **quando** acesso a seção, **então** vejo o catálogo de badges como prévia bloqueada com CTA de filiação.

**Notas técnicas**

- `GET /api/perfil/badges` retorna `conquistados[]` e `disponiveis[]` com o critério de cada um.

**Dependências:** US-08.02 · **Prioridade:** Should · **Estimativa:** 5 SP

---

#### US-08.06 — Ranking de engajamento em eventos

> **Como** membro participativo
> **Quero** ver o ranking dos membros mais engajados
> **Para** me comparar e me motivar a participar mais

**Critérios de aceite**

- **Dado** que acesso o ranking, **quando** a página carrega, **então** vejo o Top 20 do período vigente, com posição, nome, foto e pontuação — **apenas** de quem optou pela gamificação.
- **Dado** que participo da gamificação mas não estou no Top 20, **quando** acesso, **então** vejo minha posição individual destacada ao final da lista.
- **Dado** que desativo a gamificação no perfil, **quando** o ranking é recalculado, **então** meu nome não aparece mais em nenhuma posição pública.
- **Dado** que há empate em pontuação, **quando** o ranking é ordenado, **então** o desempate é pela data da participação mais antiga no período.

**Regras de negócio**

- RN-08.06.1 — Pontuação padrão sugerida (a validar com a diretoria — **Q-5**): presença confirmada em evento = 10 pontos; evento presencial = +5; voluntariado ativo no período = +20/mês.
- RN-08.06.2 — Ranking é anual, com histórico dos anos anteriores consultável.
- RN-08.06.3 — Apenas participações com `CHECKIN` pontuam (RN-05.04.1).
- RN-08.06.4 — `opt_in_gamificacao = false` remove a pessoa da exibição pública, mas a pontuação continua sendo calculada e visível apenas para ela (permite reativar sem perder histórico).

**Notas técnicas**

- `GET /api/ranking?periodo=2026`; materializar em `pontuacao_engajamento`, recalculado de forma assíncrona a cada `ParticipacaoConfirmada` (nunca calcular no request).
- Cache de 10 minutos na listagem pública.

**Dependências:** US-05.04, US-02.05 · **Prioridade:** Could · **Estimativa:** 13 SP

---

### ÉP-09 — Experiência Freemium e Conversão para Filiação

**Objetivo:** transformar o bloqueio de conteúdo em vitrine persuasiva e medir a conversão em filiação. É o épico que sustenta diretamente o OB-1.

#### US-09.01 — Componente de bloqueio de conteúdo exclusivo

> **Como** pessoa não filiada
> **Quero** entender exatamente o que estou perdendo
> **Para** decidir com informação se vale a pena me filiar

**Critérios de aceite**

- **Dado** que sou Pessoa e acesso um recurso exclusivo, **quando** a página carrega, **então** vejo a prévia (título, imagem, resumo) e um bloqueio visual sobre o valor final, com mensagem sobre a filiação.
- **Dado** que estou vendo um bloqueio, **quando** olho a tela, **então** vejo o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
- **Dado** que sou filiado ativo, **quando** acesso o mesmo recurso, **então** nenhum bloqueio aparece.
- **Dado** que inspeciono a resposta da API como não filiado, **quando** analiso o payload, **então** os dados sensíveis (código de cupom, URL de vídeo) **não estão presentes**.

**Regras de negócio**

- RN-09.01.1 — Bloqueio é **sempre** aplicado no backend, via omissão de campos. O componente visual é consequência, não a proteção.
- RN-09.01.2 — Mensagem consistente em todos os módulos (mesmo componente reutilizável).

**Notas técnicas**

- DTOs distintos por nível de acesso: `BeneficioPublicoDTO` vs `BeneficioFiliadoDTO`. Nada de serializar o objeto completo e "esconder" no frontend.
- Componente `<ConteudoBloqueado origem="beneficios|conteudos|badges" />` com rastreamento de origem para análise de conversão.

**Dependências:** US-01.03 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-09.02 — Jornada de conversão para filiação

> **Como** capítulo PMI-DF
> **Quero** acompanhar quantas pessoas se filiam após interagir com os bloqueios
> **Para** medir o retorno da plataforma sobre o objetivo de crescimento (OB-1)

**Critérios de aceite**

- **Dado** que clico no CTA de filiação, **quando** o clique acontece, **então** o evento é registrado com o módulo de origem e sou levado à página explicativa do capítulo.
- **Dado** que uma pessoa que clicou no CTA se torna filiada em até 30 dias, **quando** o relatório é gerado, **então** a conversão é atribuída ao módulo de origem.
- **Dado** que acesso o painel de conversão como admin, **quando** a página carrega, **então** vejo cliques, conversões e taxa por módulo.

**Notas técnicas**

- Tabela `evento_conversao`: `pessoa_id`, `modulo_origem`, `acao`, `criado_em`.
- Janela de atribuição de 30 dias, _last touch_.

**Dependências:** US-09.01, US-01.07 · **Prioridade:** Should · **Estimativa:** 8 SP

---

#### US-09.03 — Boas-vindas ao novo filiado

> **Como** pessoa que acabou de ter a filiação confirmada
> **Quero** ser recebida e orientada sobre o que agora tenho acesso
> **Para** aproveitar desde o primeiro dia o que paguei

**Critérios de aceite**

- **Dado** que minha filiação é confirmada, **quando** acesso a plataforma, **então** vejo uma tela de boas-vindas listando os recursos desbloqueados.
- **Dado** que estou na tela de boas-vindas, **quando** navego pelo tour, **então** sou apresentado a benefícios, conteúdo exclusivo e badges, com opção de pular a qualquer momento.
- **Dado** que já vi o tour, **quando** entro novamente, **então** ele não é exibido de novo.

**Dependências:** US-01.07 · **Prioridade:** Could · **Estimativa:** 5 SP

---

### ÉP-10 — Console Administrativo, Auditoria e LGPD

**Objetivo:** dar à diretoria autonomia operacional com rastreabilidade e manter a plataforma em conformidade com a LGPD.

#### US-10.01 — Gestão de administradores e papéis

> **Como** super administrador
> **Quero** conceder e revogar papéis administrativos
> **Para** que cada gestão do capítulo opere com autonomia e segurança

**Critérios de aceite**

- **Dado** que acesso "Administradores", **quando** a lista carrega, **então** vejo quem tem papel administrativo, qual papel e desde quando.
- **Dado** que concedo um papel a uma pessoa, **quando** confirmo, **então** ela ganha o acesso correspondente, é notificada e a ação é auditada.
- **Dado** que revogo um papel, **quando** confirmo, **então** o acesso é removido na renovação seguinte do token (máximo 15 min) e todas as sessões administrativas ativas são encerradas.
- **Dado** que sou o único super admin, **quando** tento revogar meu próprio papel, **então** a operação é bloqueada.

**Notas técnicas**

- `GET|POST|DELETE /api/admin/papeis`.
- Papéis do MVP: `SUPER_ADMIN` (tudo), `ADMIN_CONTEUDO`, `ADMIN_EVENTOS`, `ADMIN_BENEFICIOS`. Autorização por _policy_ no backend, não por verificação espalhada nos controllers.
- Ao encerrar uma gestão, prever revogação em lote com um clique.

**Dependências:** US-01.03 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-10.02 — Trilha de auditoria

> **Como** super administrador
> **Quero** consultar o histórico de ações administrativas
> **Para** ter rastreabilidade nas trocas de gestão e em eventuais incidentes

**Critérios de aceite**

- **Dado** que uma ação administrativa ocorre (criar/editar/excluir/conceder/aprovar), **quando** é executada, **então** um registro imutável é gravado com ator, ação, entidade, valores antes/depois, IP e data/hora.
- **Dado** que acesso a auditoria, **quando** filtro por ator, período, entidade ou tipo de ação, **então** vejo os registros correspondentes.
- **Dado** que tento editar ou apagar um registro de auditoria, **quando** a operação é submetida, **então** ela é rejeitada pelo sistema.

**Notas técnicas**

- Interceptor/middleware transversal aplicado a toda rota `/api/admin/*`.
- Tabela append-only, sem `UPDATE`/`DELETE` concedidos ao usuário de aplicação no banco.
- **Mascarar dados sensíveis** nos payloads gravados (senha, token, código de cupom).
- Retenção mínima de 5 anos; particionamento mensal.

**Dependências:** US-10.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-10.03 — Gestão de consentimentos e documentos legais

> **Como** encarregado de dados do capítulo
> **Quero** versionar Termos e Política de Privacidade e registrar os aceites
> **Para** comprovar conformidade com a LGPD

**Critérios de aceite**

- **Dado** que publico nova versão de um documento legal, **quando** confirmo, **então** os usuários são solicitados a aceitar no próximo acesso.
- **Dado** que uma pessoa aceita, **quando** confirma, **então** o registro guarda versão, data, IP e user-agent.
- **Dado** que uma pessoa recusa a nova versão, **quando** confirma a recusa, **então** ela mantém acesso somente leitura ao próprio perfil e à exclusão de conta.
- **Dado** que consulto o histórico de consentimentos de uma pessoa, **quando** acesso seu detalhe, **então** vejo a linha do tempo completa.

**Dependências:** US-01.01 · **Prioridade:** Must · **Estimativa:** 8 SP

---

#### US-10.04 — Portabilidade e exclusão de dados

> **Como** titular dos meus dados
> **Quero** exportar ou excluir minha conta
> **Para** exercer meus direitos previstos na LGPD

**Critérios de aceite**

- **Dado** que solicito a exportação, **quando** confirmo, **então** recebo em até 15 dias um arquivo JSON/CSV com meus dados pessoais, participações, resgates e badges.
- **Dado** que solicito a exclusão, **quando** confirmo com senha ou reautenticação, **então** minha conta é anonimizada em até 15 dias e recebo confirmação.
- **Dado** que minha conta foi anonimizada, **quando** os relatórios agregados são gerados, **então** as métricas históricas permanecem corretas sem me identificar.
- **Dado** que há obrigação legal de retenção (registros fiscais/contratuais de benefícios), **quando** a exclusão é processada, **então** esses registros são preservados de forma pseudonimizada, com justificativa informada ao titular.

**Regras de negócio**

- RN-10.04.1 — Exclusão é **anonimização**, não `DELETE` físico: dados pessoais substituídos por valores irreversíveis, mantendo integridade referencial.
- RN-10.04.2 — Prazo legal de atendimento: 15 dias.

**Notas técnicas**

- `POST /api/perfil/exportar-dados`, `POST /api/perfil/excluir-conta`.
- Anonimização: `nome_completo = 'Usuário removido'`, `email_principal = 'removido+{uuid}@invalido.local'`, demais campos pessoais nulos, `excluido_em` preenchido.
- Processamento assíncrono com confirmação por e-mail antes da execução (janela de arrependimento de 7 dias).

**Dependências:** US-02.01 · **Prioridade:** Must · **Estimativa:** 13 SP

---

#### US-10.05 — Painel de indicadores do capítulo

> **Como** diretoria do PMI-DF
> **Quero** ver os indicadores da plataforma em um painel
> **Para** tomar decisões sobre eventos, benefícios e conteúdo com base em dados

**Critérios de aceite**

- **Dado** que acesso o painel, **quando** a página carrega, **então** vejo: total de pessoas, filiados ativos, novos cadastros no período, taxa de conversão freemium→filiado, eventos e participações no período, benefícios mais resgatados e conteúdos mais vistos.
- **Dado** que altero o período, **quando** aplico, **então** todos os indicadores são recalculados.
- **Dado** que exporto o painel, **quando** confirmo, **então** recebo os dados agregados em CSV.

**Notas técnicas**

- Consultas sobre _views_ materializadas, atualizadas a cada 1h. **Nunca** rodar agregação pesada sobre tabelas transacionais no request.
- Painel exibe apenas dados agregados — nada individualizado.

**Dependências:** US-04.03, US-05.04, US-06.02 · **Prioridade:** Should · **Estimativa:** 8 SP

---

## 8. Matriz consolidada de CRUDs

Legenda: **C** Criar · **R** Ler · **U** Atualizar · **D** Excluir (`L` = lógico/soft delete, `F` = físico) · **A** Automático (sistema/integração)

| Entidade                |   Pessoa    |   Filiado   |    Admin     |     Sistema     | Observação                            |
| ----------------------- | :---------: | :---------: | :----------: | :-------------: | ------------------------------------- |
| `pessoa`                |    C R U    |    C R U    |     R U      |   A (status)    | Delete = anonimização (US-10.04)      |
| `identidade_externa`    |    C R D    |    C R D    |      R       |        —        | Vinculação/desvinculação Google       |
| `email_alternativo`     |   C R U D   |   C R U D   |      R       | A (verificação) | Máx. 3 por pessoa                     |
| `consentimento`         |     C R     |     C R     |      R       |        —        | Append-only, sem update               |
| `pessoa_papel`          |      —      |      —      |    C R D     |        —        | Só `SUPER_ADMIN`                      |
| `diretoria` / `cargo`   |      R      |      R      |  C R U D(L)  |        —        | Delete bloqueado com vínculo ativo    |
| `vinculo_voluntario`    |      R      |      R      |  C R U D(L)  |        —        | Encerramento = `data_fim`             |
| `beneficio`             |      —      | R (prévia)  |  C R U D(L)  |        —        | Nunca delete físico                   |
| `cupom`                 |      —      |      —      |    C R D     |   A (reserva)   | Importação em lote via CSV            |
| `resgate_beneficio`     |      —      |     C R     |      R       |        —        | Imutável após criação                 |
| `evento`                |      R      |      R      |  C R U D(L)  |   A (Sympla)    | Campos do Sympla read-only            |
| `participacao_evento`   | R (própria) | R (própria) |     R U      |   A (Sympla)    | Update admin = conciliação            |
| `conteudo`              | R (prévia)  |      R      |  C R U D(L)  |        —        | URL exclusiva omitida na prévia       |
| `categoria_conteudo`    |      R      |      R      |  C R U D(L)  |        —        |                                       |
| `visualizacao_conteudo` |      —      |      A      |      R       |        A        | Registro automático                   |
| `vaga`                  |      R      |      R      |  C R U D(L)  |  A (expiração)  | Slug imutável                         |
| `badge`                 |      —      |      R      | C R U D(F\*) |        —        | \*Delete físico só se nunca concedido |
| `badge_concedido`       |      —      |      R      |     C R      |   A (regras)    | **Nunca deletável**                   |
| `submissao_badge`       |      —      |   C R U D   |     R U      |        —        | Update do filiado só se `PENDENTE`    |
| `pontuacao_engajamento` | R (própria) |      R      |      R       |  A (recálculo)  | Materializada                         |
| `log_auditoria`         |      —      |      —      |      R       |        A        | Append-only, imutável                 |

---

## 9. Requisitos não funcionais

### 9.1 Segurança

| ID         | Requisito                                                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| RNF-SEG-01 | HTTPS obrigatório em todo o tráfego, com HSTS.                                                                                               |
| RNF-SEG-02 | Senhas com Argon2id; nunca em log, e-mail ou resposta de API.                                                                                |
| RNF-SEG-03 | Autorização verificada no backend em 100% dos endpoints, por _policy_ centralizada.                                                          |
| RNF-SEG-04 | Proteção contra as OWASP Top 10, com atenção especial a IDOR (todo recurso pessoal filtrado pelo `sub` do token, nunca por ID em parâmetro). |
| RNF-SEG-05 | _Rate limiting_ por IP e por usuário nos endpoints de autenticação, resgate e contato de recrutador.                                         |
| RNF-SEG-06 | Segredos de integração em cofre (AWS Secrets Manager, Vault ou equivalente); nunca no repositório.                                           |
| RNF-SEG-07 | Uploads validados por _magic bytes_, com remoção de EXIF e varredura antivírus.                                                              |
| RNF-SEG-08 | CSP restritiva, permitindo `youtube-nocookie.com` apenas nos frames de conteúdo.                                                             |

### 9.2 Privacidade e conformidade (LGPD)

| ID          | Requisito                                                                                                        |
| ----------- | ---------------------------------------------------------------------------------------------------------------- |
| RNF-PRIV-01 | Minimização: coletar apenas o necessário; campos demográficos opcionais.                                         |
| RNF-PRIV-02 | Base legal explícita e registrada por finalidade de tratamento.                                                  |
| RNF-PRIV-03 | _Privacy by default_: toda exposição pública começa desativada.                                                  |
| RNF-PRIV-04 | Direitos do titular atendidos em até 15 dias (US-10.04).                                                         |
| RNF-PRIV-05 | Dados em repouso criptografados; backups criptografados e testados trimestralmente.                              |
| RNF-PRIV-06 | Contrato de operador com Sympla e demais fornecedores; registro de transferência internacional quando aplicável. |

### 9.3 Desempenho e disponibilidade

| ID         | Requisito                                                                                         |
| ---------- | ------------------------------------------------------------------------------------------------- |
| RNF-DES-01 | p95 de resposta da API ≤ 500 ms em endpoints de leitura; ≤ 1,5 s em escrita.                      |
| RNF-DES-02 | LCP ≤ 2,5 s em 4G nas páginas principais.                                                         |
| RNF-DES-03 | Suportar 500 usuários simultâneos (pico em dia de evento) sem degradação.                         |
| RNF-DES-04 | Disponibilidade alvo de 99,5% mensal.                                                             |
| RNF-DES-05 | Falha em integração externa **nunca** derruba a aplicação: degradação graciosa com dado em cache. |

### 9.4 Usabilidade e acessibilidade

| ID         | Requisito                                                     |
| ---------- | ------------------------------------------------------------- |
| RNF-USA-01 | Design _mobile-first_: a maioria dos acessos virá de celular. |
| RNF-USA-02 | Conformidade WCAG 2.1 nível AA.                               |
| RNF-USA-03 | Interface e comunicações em português do Brasil.              |
| RNF-USA-04 | Mensagens de erro orientadas à ação, sem jargão técnico.      |

### 9.5 Observabilidade e operação

| ID         | Requisito                                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------------------------- |
| RNF-OPS-01 | Log estruturado (JSON) com `correlation_id` propagado ponta a ponta.                                              |
| RNF-OPS-02 | Alertas para: falha de job de filiação, falha de sync Sympla, taxa de erro 5xx > 1%, fila de submissões > 7 dias. |
| RNF-OPS-03 | Painel de saúde das integrações visível ao admin.                                                                 |
| RNF-OPS-04 | Backup diário com retenção de 30 dias e RPO ≤ 24h, RTO ≤ 4h.                                                      |

---

## 10. Roadmap sugerido (por incrementos)

### Incremento 1 — Fundação de identidade (o que destrava tudo)

**Meta:** uma pessoa consegue se cadastrar, e a plataforma sabe com confiança quem é filiado.

`US-01.01` · `US-01.03` · `US-01.05` · `US-01.06` · `US-10.01` · `US-10.03` · `US-01.02` · `US-01.07` · `US-02.01`

> **Critério de sucesso:** filiado real do capítulo se cadastra e tem o status validado automaticamente contra a base do PMI.

### Incremento 2 — Valor exclusivo percebido

**Meta:** o filiado enxerga, na prática, o que a filiação lhe dá.

`US-09.01` · `US-04.01` · `US-04.02` · `US-04.03` · `US-06.01` · `US-06.02` · `US-02.05` · `US-10.02`

> **Critério de sucesso:** primeiro benefício resgatado e primeiro vídeo exclusivo assistido por filiado real.

### Incremento 3 — Comunidade e agenda

**Meta:** a plataforma vira o lugar onde se acompanha a vida do capítulo.

`US-05.01` · `US-05.02` · `US-05.03` · `US-05.04` · `US-05.05` · `US-02.04` · `US-02.02` · `US-06.03`

> **Critério de sucesso:** histórico de participação de um evento real importado e visível aos membros.

### Incremento 4 — Carreira, governança e conformidade

`US-07.01` · `US-07.02` · `US-03.01` · `US-03.02` · `US-02.06` · `US-02.07` · `US-10.04` · `US-05.06`

### Incremento 5 — Reconhecimento e conversão

`US-08.01` · `US-08.02` · `US-08.05` · `US-09.02` · `US-10.05` · `US-06.04` · `US-01.04`

### Incremento 6 — Engajamento avançado

`US-08.03` · `US-08.04` · `US-08.06` · `US-03.03` · `US-07.03` · `US-09.03` · `US-01.08`

---

## 11. Riscos, premissas e questões em aberto

### 11.1 Riscos

| ID  | Risco                                                                                            | Prob. |   Impacto   | Mitigação                                                                                                                                               |
| --- | ------------------------------------------------------------------------------------------------ | :---: | :---------: | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| R-1 | A API do ThoughtSpot/PMI não expõe os dados necessários ou o acesso não é autorizado ao capítulo | Média | **Crítico** | Spike técnica na sprint 0. Plano B: importação periódica de CSV do relatório de filiados, com concessão manual assistida.                               |
| R-2 | Limite de taxa da API do Sympla impede sincronização no volume necessário                        | Média |    Alto     | Sincronização incremental, cache agressivo, janelas de execução espaçadas, _webhooks_ se disponíveis.                                                   |
| R-3 | Baixa adesão dos filiados à plataforma                                                           | Alta  |    Alto     | Lançar com benefício exclusivo de alto valor; comunicar nos eventos presenciais; e-mail de ativação segmentado.                                         |
| R-4 | Vazamento de conteúdo exclusivo do YouTube                                                       | Alta  |    Médio    | Aceitar como risco residual; mitigar com endpoint autorizado e restrição de domínio (ver A-5).                                                          |
| R-5 | Rotatividade de voluntários deixando a plataforma sem manutenção de conteúdo                     | Alta  |    Alto     | Console administrativo simples o bastante para ser operado sem apoio técnico; documentação de operação; revogação de acesso em lote na troca de gestão. |
| R-6 | Incidente com dados pessoais (LGPD)                                                              | Baixa | **Crítico** | Minimização, criptografia, auditoria, plano de resposta a incidentes e encarregado nomeado.                                                             |

### 11.2 Premissas

- P-1 — O PMI-DF possui contrato ou permissão de acesso aos dados de filiação do seu capítulo via ThoughtSpot.
- P-2 — O capítulo tem conta de organizador no Sympla com token de API disponível.
- P-3 — Os eventos do capítulo são majoritariamente publicados no Sympla.
- P-4 — Existe pelo menos um voluntário com disponibilidade recorrente para operar o console administrativo.
- P-5 — O capítulo dispõe de canal de e-mail transacional (SMTP dedicado ou serviço como SES/SendGrid) com domínio autenticado (SPF, DKIM, DMARC).

### 11.3 Questões em aberto (a resolver antes das sprints indicadas)

| ID  | Questão                                                                             | Bloqueia           | Responsável          |
| --- | ----------------------------------------------------------------------------------- | ------------------ | -------------------- |
| Q-1 | A API do PMI retorna `member_since` (data de início da filiação)?                   | US-08.02           | Diretoria + TI PMI   |
| Q-2 | Qual a periodicidade contratual permitida de consulta à base do PMI?                | US-01.07           | Diretoria            |
| Q-3 | Benefícios têm limite de resgates negociado com os parceiros?                       | US-04.01           | Dir. de Parcerias    |
| Q-4 | Filiação interrompida e retomada conta como tempo contínuo para os badges de marco? | US-08.02           | Diretoria            |
| Q-5 | Qual a régua oficial de pontuação do ranking de engajamento?                        | US-08.06           | Diretoria            |
| Q-6 | O organograma deve exibir contato dos voluntários ou apenas nome e cargo?           | US-03.03           | Diretoria + Jurídico |
| Q-7 | Vagas podem ser submetidas por empresas ou apenas cadastradas pelo admin?           | US-07.01           | Dir. de Carreira     |
| Q-8 | Quem será o encarregado de dados (DPO) do capítulo?                                 | US-10.03, US-10.04 | Presidência          |

---

## 12. Glossário

| Termo            | Definição                                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------------------- |
| **Capítulo**     | Unidade regional do PMI. No caso, PMI-DF (Distrito Federal).                                                    |
| **Filiado**      | Pessoa com filiação ativa ao PMI e vínculo com o capítulo PMI-DF.                                               |
| **Freemium**     | Modelo em que o cadastro é gratuito e parte do conteúdo é reservada a quem paga a filiação.                     |
| **PMI ID**       | Identificador único do filiado na base global do PMI.                                                           |
| **PMBOK**        | _Project Management Body of Knowledge_, guia de referência do PMI.                                              |
| **PDU**          | _Professional Development Unit_, unidade de educação continuada para manutenção de certificações PMI.           |
| **ThoughtSpot**  | Plataforma de analytics usada pelo PMI para disponibilizar dados de filiação aos capítulos.                     |
| **Sympla**       | Plataforma brasileira de gestão de eventos e inscrições usada pelo capítulo.                                    |
| **Badge**        | Selo digital de reconhecimento, permanente após conquistado.                                                    |
| **IDOR**         | _Insecure Direct Object Reference_: falha em que se acessa dado alheio trocando um identificador na requisição. |
| **Idempotência** | Propriedade de uma operação que, repetida, produz o mesmo resultado sem efeitos colaterais adicionais.          |
| **Soft delete**  | Exclusão lógica: o registro é marcado como removido, mas permanece no banco para integridade e auditoria.       |
| **DoR / DoD**    | _Definition of Ready_ / _Definition of Done_: critérios para uma história entrar e sair da sprint.              |

---

_Documento vivo. Toda alteração deve ser versionada e comunicada ao time de desenvolvimento e à diretoria do PMI-DF._
