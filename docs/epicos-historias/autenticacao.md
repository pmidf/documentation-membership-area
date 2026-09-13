# ÉP-01 — Autenticação e Validação de Filiação

## 1. Visão geral do épico

**Objetivo:** permitir que qualquer pessoa crie conta e entre na plataforma com baixa fricção e, de forma segura e sem intervenção humana, determinar **quem é filiado ativo ao PMI-DF**, usando o ThoughtSpot do PMI como fonte de verdade da filiação — sem depender dele para autenticar.

**Valor:** é a camada que sustenta todos os demais épicos. Sem ela a plataforma não sabe quem é a pessoa nem se ela é Filiada — e, portanto, não consegue liberar o clube de benefícios (ÉP-04), o conteúdo exclusivo (ÉP-06), as oportunidades (ÉP-07) e a gamificação (ÉP-08) apenas a quem tem filiação vigente. Classificado como **Essencial** no brainstorming (item 1 — login, cadastro, entrar com o Google e "esqueci a senha").

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md). Os dados obrigatórios do cadastro são os mesmos de "Meu perfil" ([ÉP-02, US-02.01](meu-perfil.md#us-0201-visualizar-e-editar-meus-dados-pessoais)).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**, a cada requisição, a partir do status de vinculação gravado. **Declarar-se filiado nunca concede acesso**: só o status `APROVADO` (ou a concessão manual do Admin, ÉP-02) libera o que é exclusivo. `PENDENTE`, `AGUARDANDO_CODIGO` e `EXPIRADO` têm exatamente o acesso de Não-Filiado.

### 1.1 Princípios do modelo

| Princípio | O que significa |
|-----------|-----------------|
| **Autenticação é da plataforma** | Login com e-mail e senha próprios (ou Google). O PMI não participa do login; se o ThoughtSpot estiver fora do ar, cadastro e login continuam funcionando com o último status conhecido. |
| **Filiação é do PMI** | A plataforma nunca "decide" que alguém é filiado: consulta a base do PMI e grava o resultado. O Admin pode conceder manualmente em casos excepcionais (ÉP-02, US-02.11). |
| **Um PMI ID, uma conta** | Cada PMI ID pode estar vinculado a **uma única** conta aprovada. |
| **Posse do e-mail do PMI** | Além de o PMI ID existir e estar ativo no PMI-DF, a pessoa prova que controla o e-mail cadastrado no PMI, por código de 6 dígitos. É isso que impede alguém de usar o PMI ID de outra pessoa. |
| **Uma confirmação só, sempre que possível** | Se a pessoa se cadastra com o mesmo e-mail que usa no PMI, o código que confirma a conta já comprova a posse do e-mail do PMI: a filiação é aprovada sem uma segunda etapa. |
| **Acabou a filiação, acabou o acesso** | O status é revalidado a cada acesso; quando o PMI deixa de reconhecer a filiação, o acesso de Filiado é suspenso **imediatamente**, sem apagar conta nem histórico. |
| **Mensagens não vazam informação** | Erros de login, cadastro, recuperação e vinculação nunca revelam se um e-mail ou PMI ID existe na base. |

### 1.2 Status de vinculação PMI

O status de vinculação é o atributo que define o **estado de filiação** da pessoa na plataforma (Não-Filiado × Filiado, conforme [Perfis de Usuário](../planejamento/perfil-usuario.md)).

| Status | Significado | Estado de filiação resultante |
|--------|-------------|-------------------------------|
| `SEM_VINCULO` | Conta criada; PMI ID ainda não informado ou última tentativa recusada | Não-Filiado |
| `PENDENTE` | PMI ID e e-mail do PMI informados, mas ainda não localizados na base do PMI (ou PMI indisponível na consulta) | Não-Filiado |
| `AGUARDANDO_CODIGO` | Filiação ativa ao PMI-DF confirmada na base; falta confirmar o código enviado ao e-mail do PMI | Não-Filiado |
| `APROVADO` | Filiação ativa confirmada e posse do e-mail comprovada | **Filiado** |
| `EXPIRADO` | A revalidação deixou de reconhecer a filiação (vencida, não renovada ou inativa) | Não-Filiado |
| `CONCEDIDO_MANUALMENTE` | Filiação concedida pelo Admin (ÉP-02, US-02.11) | **Filiado** |

Situações de erro na vinculação — **outro capítulo**, **filiação inativa**, **dados não conferem** — **não são status persistidos**: são respostas imediatas ao usuário, que corrige e tenta de novo (US-01.07).

```
SEM_VINCULO ──(PMI ID + e-mail do PMI)──► consulta ao PMI
                                             │
        ┌────────────────────────────────────┼──────────────────────────────┐
        ▼                                    ▼                              ▼
  não localizado /                  confere, e-mail do PMI          confere, e-mail do PMI
  PMI indisponível                  ≠ e-mail da conta               = e-mail da conta (já verificado)
        │                                    │                              │
     PENDENTE ──(login ou             AGUARDANDO_CODIGO ──(código)──►    APROVADO
   "Atualizar situação")──►                                                 ▲   │
   consulta de novo                                                         │   │ revalidação no login
                                                                            │   ▼
                                    ("Atualizar situação" com filiação renovada) ◄── EXPIRADO (acesso de Não-Filiado)
```

### 1.3 Como a filiação é confirmada no ThoughtSpot

O PMI disponibiliza aos capítulos os dados de filiação em uma instância do **ThoughtSpot** (plataforma de analytics). Ela **não é um serviço de identidade**: a integração é de **consulta a um relatório**, feita com uma credencial de serviço do capítulo, e a resposta chega em segundos.

| Etapa | Como funciona |
|-------|---------------|
| **1. Autenticação de serviço** | `POST {host}/api/rest/2.0/auth/token/full` com a credencial de serviço do PMI-DF (usuário + senha ou *secret key* de autenticação confiável) e `validity_time_in_sec`. O *token* é guardado em cache com validade menor que a informada e renovado automaticamente. A credencial fica em cofre de segredos; nunca no código, no repositório ou no frontend. |
| **2. Consulta pelo PMI ID** | `POST {host}/api/rest/2.0/searchdata` com `query_string` filtrando o PMI ID (ex.: `[Member ID] = '1234567'`) sobre o *worksheet/model* de filiados indicado pelo PMI (`logical_table_identifier`). Alternativa, se o PMI só liberar um *liveboard*: `POST /api/rest/2.0/metadata/liveboard/data` com *runtime filter* pelo PMI ID. Colunas esperadas: PMI ID, nome, e-mail, capítulo(s), situação da filiação, data de expiração e data da fotografia. |
| **3. Escolha do registro** | A base guarda **fotografias mensais**: podem existir vários registros para o mesmo PMI ID. Considera-se **o mais recente** (maior data de fotografia). Se a pessoa tem mais de um capítulo, o PMI-DF deve constar entre eles. |
| **4. Normalização e persistência** | Grava-se apenas o necessário (seção 1.4) e o resultado alimenta o status de vinculação. Toda chamada é registrada (data, duração, sucesso/erro), sem *payload* completo. |

**Regras de resiliência:**

- *Timeout* de **8 segundos** por consulta; falha não perde os dados informados pelo usuário (US-01.07, critério 9).
- Após falhas consecutivas, a plataforma para de chamar o PMI por alguns minutos (*circuit breaker*) e responde "indisponível", sem travar cadastro ou login.
- **Nunca** rebaixar o acesso de um Filiado por falha de integração: só com resposta positiva e explícita de expiração ou inatividade (US-01.11).
- O cliente do ThoughtSpot fica isolado atrás de uma interface (`ValidadorFiliacao`), permitindo simulação em testes e troca de fornecedor (ou plano B por importação de arquivo) sem alterar as regras de negócio.

> ⚠️ **Spike obrigatória antes da sprint de integração:** confirmar com o PMI o acesso do capítulo à API (credencial de serviço, *org*, *worksheet*/*liveboard* liberado), os nomes exatos das colunas, a cadência de atualização (mensal? semanal?), se o capítulo aparece como coluna multivalorada ou uma linha por capítulo, e se existe a data de início da filiação (relevante para o ÉP-08). Ver questões em aberto.

### 1.4 Dados guardados (LGPD)

| Origem | O que é guardado | O que **não** é guardado |
|--------|------------------|--------------------------|
| Consulta ao PMI | PMI ID, e-mail do PMI, nome completo, código do capítulo, situação (ativa/inativa), data de expiração, data/hora da última verificação | Telefone, endereço, data de nascimento ou qualquer outro campo do PMI |
| Tentativas de vinculação | Conta, PMI ID informado, IP, resultado, data/hora | — |
| Conta | Senha apenas como *hash* (Argon2id); códigos de verificação, tokens de recuperação e códigos do PMI apenas como *hash* | Senha em texto claro, em log, e-mail ou resposta de API |

O Termo de Uso informa que a filiação é conferida junto ao PMI e que o e-mail cadastrado no PMI receberá um código de confirmação.

### 1.5 Matriz de permissões do épico

Legenda: **L** = Ler · **E** = Escrever · **—** = Sem acesso

| Recurso | Visitante | Não-Filiado | Filiado | Admin |
|---------|:---------:|:-----------:|:-------:|:-----:|
| Criar conta (e-mail/senha ou Google), informando PMI ID se já for filiado | E | — | — | — |
| Entrar, recuperar senha, confirmar e-mail | E | E | E | E |
| Onboarding inicial | — | E | E | E |
| Vincular filiação (PMI ID + e-mail do PMI) | — | E | — | conforme filiação |
| Confirmar código do e-mail do PMI | — | E | — | conforme filiação |
| "Atualizar situação" (reconsultar o PMI) | — | E | E | conforme filiação |
| Enviar comprovante para análise manual | — | E | — | — |
| Próprio status de filiação, expiração e última verificação | — | L | L | L |
| Próprias sessões ativas (ver e encerrar) | — | E | E | E |
| Alterar o próprio vínculo depois de `APROVADO` | — | — | — | — |
| Vínculos e tentativas de vinculação de terceiros | — | — | — | L |
| Desfazer o vínculo de uma pessoa | — | — | — | E |
| Conceder / revogar o papel de Admin | — | — | — | E |

### 1.6 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-01.01](#us-0101-criar-conta-com-e-mail-e-senha) | Criar conta com e-mail e senha | Visitante | Must |
| [US-01.02](#us-0102-confirmar-meu-e-mail-e-concluir-o-cadastro) | Confirmar meu e-mail e concluir o cadastro | Visitante | Must |
| [US-01.03](#us-0103-criar-conta-ou-entrar-com-o-google) | Criar conta ou entrar com o Google | Visitante, Não-Filiado, Filiado | Should |
| [US-01.04](#us-0104-entrar-com-e-mail-e-senha) | Entrar com e-mail e senha | Não-Filiado, Filiado, Admin | Must |
| [US-01.05](#us-0105-recuperar-minha-senha) | Recuperar minha senha | Não-Filiado, Filiado, Admin | Must |
| [US-01.06](#us-0106-responder-o-onboarding-inicial) | Responder o onboarding inicial | Não-Filiado, Filiado | Should |
| [US-01.07](#us-0107-vincular-minha-filiacao-informando-pmi-id-e-e-mail-do-pmi) | Vincular minha filiação informando PMI ID e e-mail do PMI | Não-Filiado | Must |
| [US-01.08](#us-0108-confirmar-o-codigo-enviado-ao-e-mail-do-pmi) | Confirmar o código enviado ao e-mail do PMI | Não-Filiado | Must |
| [US-01.09](#us-0109-acompanhar-a-vinculacao-pendente-e-atualizar-a-situacao) | Acompanhar a vinculação pendente e atualizar a situação | Não-Filiado | Must |
| [US-01.10](#us-0110-enviar-comprovante-para-analise-manual) | Enviar comprovante para análise manual | Não-Filiado | Should |
| [US-01.11](#us-0111-manter-minha-filiacao-revalidada-automaticamente) | Manter minha filiação revalidada automaticamente | Filiado | Must |
| [US-01.12](#us-0112-encerrar-minhas-sessoes) | Encerrar minhas sessões | Não-Filiado, Filiado, Admin | Should |
| [US-01.13](#us-0113-consultar-vinculos-e-tentativas-e-desfazer-um-vinculo) | Consultar vínculos e tentativas e desfazer um vínculo | Admin | Must |
| [US-01.14](#us-0114-conceder-e-revogar-o-papel-de-admin) | Conceder e revogar o papel de Admin | Admin | Must |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-01.XX.N). "Visitante" designa a pessoa ainda não autenticada, que ao concluir o cadastro se torna Não-Filiado.

### 1.7 Jornada resumida

A jornada foi desenhada para que **quem já é filiado confirme um único código** no caso comum (cadastro com o mesmo e-mail do PMI) e só precise de um segundo código quando o e-mail da conta for diferente do e-mail do PMI — situação em que a segunda confirmação é inevitável, pois prova a posse de outra caixa de e-mail, e é aproveitada: o e-mail do PMI vira automaticamente um e-mail alternativo verificado (ÉP-02, US-02.03), útil para reconhecer participações no Sympla.

```
Cadastro (dados obrigatórios + "Já é filiado?" → PMI ID, dica: use o e-mail do MyPMI)
   ─► código de 6 dígitos no e-mail da conta
        ├─ sem PMI ID ─────────────────────────────────────► Onboarding ─► Home como Não-Filiado
        └─ com PMI ID ─► consulta automática ao PMI
                           ├─ e-mail do PMI = e-mail da conta ─► APROVADO ─► Onboarding ─► Home como Filiado
                           ├─ e-mail do PMI ≠ e-mail da conta ─► 2º código (e-mail do PMI) ─► APROVADO
                           └─ não localizado / erro ──────────► PENDENTE ou SEM_VINCULO, com orientação;
                                                                 "Atualizar situação" em Meu perfil a qualquer momento

Já tinha conta e se filiou depois ─► Meu perfil > Filiação PMI > "Atualizar situação" ─► mesmo fluxo de vinculação
```

---

## 2. Histórias do Visitante (criação de conta e acesso)

### US-01.01 — Criar conta com e-mail e senha

**Atores:** Visitante

> **Como** visitante interessado na comunidade do PMI-DF
> **Quero** criar uma conta informando meus dados e, se já for filiado, meu PMI ID
> **Para** acessar a área de membros com o acesso correspondente à minha situação

**Critérios de aceite**

1. **Dado** que estou na tela de cadastro, **quando** informo nome completo, e-mail, senha e os dados pessoais obrigatórios — telefone, endereço completo (CEP, logradouro, número, complemento, bairro, cidade e UF), escolaridade, data de nascimento, gênero, empresa onde trabalho e cargo — e aceito o Termo de Uso e a Política de Privacidade, **então** a conta é criada com status `SEM_VINCULO` e recebo o e-mail com o código de confirmação (US-01.02).
2. **Dado** que o formulário é longo, **quando** o preencho, **então** ele é dividido em etapas (acesso → dados pessoais → endereço → profissional) com progresso salvo entre etapas na mesma sessão, e o link do LinkedIn é o único campo opcional.
3. **Dado** que estou na etapa de acesso, **quando** respondo "Sim" a "Você já é filiado ao PMI?", **então** aparece o campo **PMI ID** (obrigatório nesse caso), a orientação de onde encontrá-lo no MyPMI e a dica "**Cadastre-se com o mesmo e-mail que você usa no PMI** — assim uma única confirmação valida sua conta e sua filiação".
4. **Dado** que respondo "Não" ou "Não sei", **quando** sigo, **então** nenhum campo de PMI ID aparece e posso vincular a filiação depois, em "Meu perfil".
5. **Dado** que informo o CEP, **quando** saio do campo, **então** logradouro, bairro, cidade e UF são preenchidos automaticamente, permanecendo editáveis.
6. **Dado** que informo um e-mail já cadastrado, **quando** submeto, **então** vejo a mesma mensagem neutra de qualquer cadastro ("Enviamos um código para o seu e-mail") e o endereço recebe um aviso de que já possui conta, com link para entrar ou recuperar a senha — a tela **não** revela que a conta existe.
7. **Dado** que informo senha com menos de 10 caracteres ou presente em lista de senhas vazadas, **quando** submeto, **então** o cadastro é recusado com orientação clara e um indicador de força da senha.
8. **Dado** que deixo um campo obrigatório em branco ou informo dado inválido (telefone fora do padrão, CEP inexistente, data de nascimento futura ou idade abaixo de 18 anos, URL que não é do LinkedIn, PMI ID fora do padrão numérico), **quando** submeto, **então** vejo a mensagem junto ao campo e nada é persistido.
9. **Dado** que sou robô ou faço muitas tentativas, **quando** excedo 5 cadastros por IP por hora, **então** as tentativas seguintes exigem CAPTCHA.

**Regras de negócio**

- RN-01.01.1 — Campos obrigatórios: os mesmos de "Meu perfil" (ÉP-02, RN-02.01.1). Gênero inclui "Prefiro não informar". Escolaridade e gênero usam listas fechadas.
- RN-01.01.2 — **Idade mínima de 18 anos**, calculada pela data de nascimento no momento do cadastro.
- RN-01.01.3 — O e-mail é normalizado (minúsculas, sem espaços) e é único na base; é a chave de login.
- RN-01.01.4 — A senha é armazenada apenas como *hash* (Argon2id); nunca em texto claro, log, e-mail ou resposta de API.
- RN-01.01.5 — O aceite do Termo de Uso e da Política de Privacidade é registrado como consentimento com versão do documento, data, hora, IP e *user-agent* (ÉP-02, RN-02.04.3).
- RN-01.01.6 — Conta não confirmada **não entra** na plataforma (US-01.02); contas não confirmadas em 30 dias são removidas fisicamente, pois não possuem histórico.
- RN-01.01.7 — Informar o PMI ID no cadastro **não altera o status**: o PMI só é consultado depois que o e-mail da conta é confirmado (US-01.02), para que nenhuma consulta seja feita em nome de um e-mail que não foi comprovado.

---

### US-01.02 — Confirmar meu e-mail e concluir o cadastro

**Atores:** Visitante

> **Como** pessoa que acabou de se cadastrar
> **Quero** confirmar meu e-mail com um único código
> **Para** ativar minha conta e, se já sou filiado, ter minha filiação validada na mesma etapa

**Critérios de aceite**

1. **Dado** que me cadastrei, **quando** recebo o e-mail, **então** ele contém um **código de 6 dígitos** e um link equivalente; posso digitar o código na tela em que já estou ou clicar no link.
2. **Dado** que confirmo o código (ou o link) em até 24 horas, **quando** a confirmação conclui, **então** a conta é marcada como verificada e entro automaticamente.
3. **Dado** que informei um PMI ID no cadastro, **quando** a conta é confirmada, **então** a plataforma consulta o PMI **automaticamente**, usando o e-mail da conta como e-mail do PMI, e aplica as regras de US-01.07:
   - **tudo confere e o e-mail do PMI é o da conta** → `APROVADO` na hora, sem segundo código; sigo para o onboarding já como Filiado, com a mensagem "Filiação confirmada";
   - **tudo confere, mas o e-mail do PMI é outro** → `AGUARDANDO_CODIGO`; vejo a explicação de que o PMI tem outro e-mail cadastrado para mim (parcialmente mascarado) e sigo para confirmar o segundo código (US-01.08);
   - **não localizado / PMI indisponível** → `PENDENTE`, com a orientação de US-01.09; sigo para o onboarding como Não-Filiado;
   - **outro capítulo, filiação inativa ou dados não conferem** → `SEM_VINCULO`, com a mensagem correspondente e o caminho "Atualizar situação" em "Meu perfil"; sigo para o onboarding como Não-Filiado.
4. **Dado** que o código expirou ou já foi usado, **quando** o informo, **então** vejo a explicação e o botão "Reenviar código".
5. **Dado** que tento entrar sem ter confirmado, **quando** informo credenciais corretas, **então** não entro: vejo a tela "Confirme seu e-mail" com o campo do código e a opção de reenviar.
6. **Dado** que peço reenvio, **quando** já pedi há menos de 2 minutos, **então** vejo a orientação de aguardar; o código anterior é invalidado a cada reenvio.
7. **Dado** que erro o código 5 vezes, **quando** tento de novo, **então** o código é invalidado e preciso pedir um novo.
8. **Dado** que altero meu e-mail principal depois (ÉP-02, US-02.01), **quando** salvo, **então** o novo endereço passa pelo mesmo fluxo e só se torna efetivo após confirmação.

**Regras de negócio**

- RN-01.02.1 — Código de 6 dígitos e link de uso único, expiram em 24 horas; apenas o *hash* é armazenado; 5 erros invalidam o código.
- RN-01.02.2 — O e-mail confirmado é pré-requisito para tudo: entrar, vincular filiação, resgatar benefícios, vincular e-mails do Sympla.
- RN-01.02.3 — Contas criadas via Google nascem confirmadas (US-01.03); se informarem PMI ID ao completar o cadastro, a consulta automática do critério 3 roda ao concluir.
- RN-01.02.4 — A consulta automática conta como uma tentativa de vinculação para os limites de RN-01.07.5 e é registrada como as demais (RN-01.07.7).

---

### US-01.03 — Criar conta ou entrar com o Google

**Atores:** Visitante, Não-Filiado, Filiado

> **Como** visitante ou usuário cadastrado
> **Quero** usar minha conta Google para criar conta e entrar
> **Para** não gerenciar mais uma senha

**Critérios de aceite**

1. **Dado** que clico em "Continuar com o Google" e autorizo, **quando** o e-mail do Google não existe na base, **então** a conta é criada já confirmada e sou levado a **completar o cadastro** com os dados obrigatórios e a pergunta "Já é filiado?" (US-01.01, critérios 1 e 3) antes de acessar qualquer outra tela.
2. **Dado** que o e-mail do Google já existe como conta com senha, **quando** autorizo, **então** **não** entro automaticamente: vejo a solicitação de confirmar a vinculação informando a senha atual (ou por código enviado ao e-mail), e só depois as duas identidades ficam ligadas.
3. **Dado** que já vinculei o Google, **quando** entro por ele, **então** o login é direto e a sessão é igual à do login com senha.
4. **Dado** que estou autenticado, **quando** acesso "Segurança" em "Meu perfil", **então** posso vincular ou desvincular o Google; a desvinculação só é permitida se a conta tiver senha definida.
5. **Dado** que minha conta foi criada só com Google e peço "esqueci a senha", **quando** submeto, **então** o e-mail orienta a entrar com o Google ou a definir uma primeira senha por link verificado.

**Regras de negócio**

- RN-01.03.1 — Vinculação automática sem confirmação é proibida (risco de sequestro de conta).
- RN-01.03.2 — Uma pessoa tem no máximo uma identidade Google; o identificador estável é o `sub` do Google, não o e-mail.
- RN-01.03.3 — Escopos solicitados: apenas `openid`, `email` e `profile`. Fluxo *Authorization Code* com PKCE, validação de `state` e `nonce`.
- RN-01.03.4 — O login com Google não altera a filiação: a vinculação ao PMI é sempre a de US-01.07.

---

### US-01.04 — Entrar com e-mail e senha

**Atores:** Não-Filiado, Filiado, Admin

> **Como** pessoa cadastrada
> **Quero** entrar com meu e-mail e senha
> **Para** acessar a área de membros com o acesso correspondente ao meu status de filiação

**Critérios de aceite**

1. **Dado** credenciais válidas e e-mail confirmado, **quando** entro, **então** sou levado à home e a interface reflete meu status: Não-Filiado (prévias desfocadas) ou Filiado (acesso completo); se sou Admin, vejo também o acesso ao console.
2. **Dado** credenciais inválidas, **quando** tento entrar, **então** vejo a mensagem genérica "E-mail ou senha inválidos", sem indicar qual campo falhou e sem revelar se a conta existe.
3. **Dado** 5 tentativas falhas consecutivas para a mesma conta, **quando** tento novamente, **então** a conta entra em bloqueio temporário progressivo (1, 5 e 15 minutos) e recebo e-mail de alerta com orientação de recuperar a senha.
4. **Dado** que meu status é `PENDENTE`, **quando** entro, **então** a plataforma reconsulta o PMI automaticamente em segundo plano (US-01.09) e, se agora for localizado, sou conduzido ao passo seguinte do fluxo de vinculação.
5. **Dado** que sou Filiado com última verificação há mais de 7 dias ou expiração vencida, **quando** entro, **então** a revalidação roda em segundo plano sem atrasar o login (US-01.11).
6. **Dado** que minha conta foi suspensa pelo Admin (ÉP-02, US-02.10), **quando** tento entrar, **então** vejo a mensagem de conta suspensa com o canal de contato do capítulo.
7. **Dado** que marco "Manter-me conectado", **quando** volto em até 30 dias, **então** continuo autenticado sem redigitar a senha.

**Regras de negócio**

- RN-01.04.1 — A sessão usa um *token* de acesso de curta duração (15 minutos) com o status de filiação e o papel, e um *refresh token* rotativo (7 dias; 30 dias com "manter-me conectado"), em cookie `httpOnly`. O status no *token* tem vida curta justamente para refletir rebaixamentos e concessões em até 15 minutos.
- RN-01.04.2 — Suspensão de conta, revogação de filiação manual, expiração da filiação, desfazer vínculo e troca de senha invalidam **imediatamente** todos os *refresh tokens* da pessoa.
- RN-01.04.3 — A comparação de senha ocorre em tempo constante mesmo quando a conta não existe (evita enumeração por tempo de resposta).
- RN-01.04.4 — Limite de 10 tentativas de login por minuto por IP, além do bloqueio por conta.
- RN-01.04.5 — Indisponibilidade do PMI **nunca** impede o login: vale o último status conhecido (seção 1.1).

---

### US-01.05 — Recuperar minha senha

**Atores:** Não-Filiado, Filiado, Admin

> **Como** pessoa que esqueceu a senha
> **Quero** redefini-la por e-mail
> **Para** recuperar o acesso sem depender de suporte

**Critérios de aceite**

1. **Dado** que clico em "Esqueci minha senha" e informo qualquer e-mail, **quando** submeto, **então** vejo sempre a mesma mensagem neutra ("Se este e-mail estiver cadastrado, você receberá as instruções").
2. **Dado** que recebo o link, **quando** o utilizo em até 60 minutos, **então** defino uma nova senha (mesmas regras de US-01.01), todas as minhas sessões são encerradas e recebo e-mail confirmando a troca.
3. **Dado** que o link expirou ou já foi usado, **quando** o acesso, **então** vejo erro claro e a opção de solicitar um novo.
4. **Dado** que peço mais de 3 recuperações para o mesmo e-mail em uma hora, **quando** submeto, **então** a mensagem neutra continua igual, mas nenhum novo e-mail é enviado.
5. **Dado** que estou autenticado, **quando** troco a senha em "Segurança", **então** preciso informar a senha atual e as demais sessões são encerradas.

**Regras de negócio**

- RN-01.05.1 — Token aleatório de uso único, expira em 60 minutos; apenas o *hash* é armazenado.
- RN-01.05.2 — A redefinição não altera status de filiação, vínculo nem papel.
- RN-01.05.3 — E-mails de recuperação e de alerta são transacionais e independem da preferência de e-mail marketing (ÉP-02, RN-02.04.2).

---

### US-01.06 — Responder o onboarding inicial

**Atores:** Não-Filiado, Filiado

> **Como** pessoa que acabou de confirmar a conta
> **Quero** informar meu interesse e momento de carreira
> **Para** ter uma primeira experiência coerente com o que busco

**Critérios de aceite**

1. **Dado** que confirmei o e-mail (ou completei o cadastro via Google) e a etapa de filiação foi resolvida ou pulada, **quando** entro pela primeira vez, **então** vejo o onboarding em dois passos: interesse principal e momento de carreira.
2. **Dado** que estou em qualquer passo, **quando** clico em "Pular por agora", **então** sigo para a home sem bloqueio.
3. **Dado** que sou Não-Filiado sem PMI ID informado, **quando** concluo ou pulo, **então** chego à home com o convite "Já é filiado? Atualize sua situação" visível no perfil e nos painéis de prévia desfocada.
4. **Dado** que sou Filiado recém-aprovado, **quando** concluo ou pulo, **então** vejo uma tela de boas-vindas listando o que foi desbloqueado (benefícios, conteúdo exclusivo, oportunidades, conquistas), exibida uma única vez.
5. **Dado** que concluí ou pulei, **quando** entro de novo, **então** o onboarding não reaparece; interesse e momento de carreira ficam editáveis em "Meu perfil" (ÉP-02, US-02.01).

**Regras de negócio**

- RN-01.06.1 — O onboarding nunca bloqueia o acesso: toda resposta é opcional.
- RN-01.06.2 — O onboarding não pergunta sobre filiação; isso é resolvido no cadastro (US-01.01) ou em "Atualizar situação" (US-01.09).

---

## 3. Histórias do Não-Filiado (vinculação ao PMI)

### US-01.07 — Vincular minha filiação informando PMI ID e e-mail do PMI

**Atores:** Não-Filiado

> **Como** Não-Filiado que já é filiado ao PMI-DF
> **Quero** informar meu PMI ID e o e-mail que uso no PMI
> **Para** ter meu acesso de Filiado liberado sem intervenção humana

**Critérios de aceite**

1. **Dado** que acesso a vinculação — automaticamente após confirmar o cadastro com PMI ID (US-01.02), por "Meu perfil > Filiação PMI > Atualizar situação" ou pelo painel "Já sou filiado" das prévias desfocadas —, **quando** a tela carrega, **então** vejo os campos **PMI ID** e **e-mail cadastrado no PMI** (pré-preenchido com o e-mail da conta, editável), a orientação de onde encontrá-los no MyPMI e o aviso de que o e-mail do PMI receberá um código.
2. **Dado** que submeto, **quando** a plataforma consulta o PMI em tempo real (poucos segundos, com indicador de progresso), **então** as regras são avaliadas **nesta ordem** e recebo a resposta correspondente:
   - **Não localizado** → status `PENDENTE` e a mensagem "Não localizamos sua filiação na base do PMI. Se você se filiou recentemente, a atualização pode levar alguns dias. Você pode atualizar sua situação a qualquer momento em Meu Perfil." (US-01.09).
   - **Localizado, mas o PMI-DF não está entre os capítulos** → "Sua filiação ao PMI foi localizada, mas você não é filiado ao capítulo PMI-DF. Saiba como se filiar: [link]". O status não muda.
   - **Localizado no PMI-DF, mas filiação inativa** → "Sua filiação ao PMI-DF está inativa. Renove no PMI e tente novamente; a atualização pode levar alguns dias." O status não muda.
   - **Localizado, PMI-DF, ativa, mas o e-mail informado não é o e-mail do PMI** → "Os dados informados não conferem com o cadastro do PMI. Verifique o PMI ID e o e-mail cadastrado no MyPMI e tente novamente." O status não muda.
   - **Tudo confere** → segue para a confirmação do código (US-01.08).
3. **Dado** que existem vários registros para o mesmo PMI ID (fotografias mensais), **quando** a plataforma avalia, **então** considera apenas o mais recente.
4. **Dado** que o PMI ID já está vinculado a **outra conta aprovada**, **quando** submeto, **então** vejo a mesma mensagem genérica de "dados não conferem" (sem revelar que o ID está em uso) e a tentativa é sinalizada ao Admin (US-01.13).
5. **Dado** que tudo confere e o e-mail do PMI é **igual** ao e-mail da minha conta (já confirmado), **quando** a consulta conclui, **então** a etapa do código é pulada e passo direto a `APROVADO` (US-01.08, critério 6).
6. **Dado** que estou em `SEM_VINCULO`, `PENDENTE` ou `EXPIRADO`, **quando** quero corrigir PMI ID ou e-mail, **então** posso alterar os dados e tentar de novo, respeitando o limite de tentativas.
7. **Dado** que excedi 5 tentativas na hora, **quando** submeto, **então** vejo a orientação de aguardar, sem consulta ao PMI.
8. **Dado** que não confirmei meu e-mail da conta, **quando** tento vincular, **então** sou bloqueado com CTA para reenviar o código.
9. **Dado** que o PMI não responde em 8 segundos ou retorna erro, **quando** a consulta falha, **então** PMI ID e e-mail **não se perdem**: o status vira `PENDENTE` com motivo "indisponível" e vejo "Não conseguimos consultar o PMI agora. Tente novamente em alguns minutos ou use 'Atualizar situação' no seu perfil."

**Regras de negócio**

- RN-01.07.1 — A filiação exigida é ao **PMI-DF**: o capítulo deve constar entre os capítulos do membro (uma pessoa pode ter mais de um).
- RN-01.07.2 — Só filiação **ativa** conforme o PMI é aceita; status inativo ou não renovado não vincula.
- RN-01.07.3 — O e-mail informado deve ser **exatamente** o e-mail principal cadastrado no PMI, comparado sem diferenciar maiúsculas e sem espaços.
- RN-01.07.4 — Um PMI ID vincula-se a no máximo uma conta aprovada.
- RN-01.07.5 — Limites: **5 vinculações por hora por conta** e **20 por hora por IP**.
- RN-01.07.6 — Mensagens não revelam qual campo falhou nem se o PMI ID existe; "dados não conferem" cobre e-mail divergente **e** PMI ID já em uso.
- RN-01.07.7 — Toda tentativa é registrada (conta, PMI ID, IP, resultado, data/hora) para auditoria e detecção de abuso.
- RN-01.07.8 — Nenhuma das situações de erro concede acesso: em nenhum caso a pessoa passa a Filiado por erro ou por declaração.

---

### US-01.08 — Confirmar o código enviado ao e-mail do PMI

**Atores:** Não-Filiado

> **Como** Não-Filiado cuja filiação foi localizada no PMI com um e-mail diferente do da minha conta
> **Quero** provar que controlo o e-mail cadastrado no PMI
> **Para** que ninguém use meu PMI ID e para ter meu acesso de Filiado liberado

**Critérios de aceite**

1. **Dado** que a consulta confirmou filiação ativa ao PMI-DF (US-01.07), **quando** o e-mail do PMI é diferente do e-mail da conta, **então** o status vira `AGUARDANDO_CODIGO`, um código de 6 dígitos é enviado **ao e-mail que consta no PMI** (nunca ao digitado, se diferente) e vejo a tela de confirmação com o endereço parcialmente mascarado (ex.: `jo***@empresa.com`) e a explicação "O PMI tem este e-mail cadastrado para você; confirme o código para comprovar que ele é seu".
2. **Dado** que digito o código correto dentro de 10 minutos, **quando** confirmo, **então** o status vira `APROVADO`, a plataforma registra nome, capítulo e data de expiração vindos do PMI, o acesso de Filiado é liberado **imediatamente** (sem novo login) e recebo o e-mail "Filiação confirmada".
3. **Dado** que confirmei o código, **quando** o vínculo é aprovado, **então** o e-mail do PMI é cadastrado automaticamente como **e-mail alternativo verificado** da minha conta (ÉP-02, US-02.03), e as participações em eventos feitas com ele passam a constar no meu histórico — a segunda confirmação não é desperdiçada.
4. **Dado** que digito um código errado, **quando** erro pela 5ª vez, **então** o código é invalidado e preciso pedir um novo.
5. **Dado** que o código expirou ou quero outro, **quando** clico em "Reenviar código", **então** um novo é enviado e o anterior invalidado, respeitando o intervalo mínimo de 1 minuto e o máximo de 5 envios por hora.
6. **Dado** que o e-mail do PMI é igual ao e-mail da conta, já confirmado, **quando** a consulta conclui, **então** esta etapa é pulada e o critério 2 se aplica diretamente.
7. **Dado** que fecho a tela sem confirmar, **quando** volto (por login ou "Meu perfil"), **então** retomo a confirmação do código sem repetir a consulta ao PMI, enquanto o vínculo não expirar.
8. **Dado** que não tenho mais acesso ao e-mail que consta no PMI, **quando** leio a tela, **então** vejo a orientação de atualizar o e-mail no MyPMI e tentar de novo após a atualização da base, ou de enviar comprovante para análise manual (US-01.10).

**Regras de negócio**

- RN-01.08.1 — Código numérico de 6 dígitos, validade de 10 minutos, uso único; armazenado apenas como *hash*.
- RN-01.08.2 — Ao aprovar, `nome` e `PMI ID` passam a ser dados sincronizados do PMI e ficam somente leitura na plataforma (ÉP-02, US-02.07); o nome informado no cadastro é substituído pelo nome do PMI.
- RN-01.08.3 — A aprovação emite o evento de domínio `FiliacaoConfirmada`, consumido pelo ÉP-02 (selo, campos somente leitura) e pelo ÉP-08 (badges por tempo de filiação, contado a partir desta data).
- RN-01.08.4 — Em `APROVADO`, a pessoa **não** pode alterar PMI ID ou e-mail do PMI; só o Admin desfaz o vínculo (US-01.13).
- RN-01.08.5 — Um vínculo em `AGUARDANDO_CODIGO` sem confirmação por 7 dias volta a `SEM_VINCULO`, liberando o PMI ID para nova tentativa.
- RN-01.08.6 — O e-mail do PMI cadastrado como alternativo conta para o limite de 5 e-mails alternativos (ÉP-02, RN-02.03.2); se o limite já estiver atingido, o mais antigo não verificado é removido ou a pessoa é orientada a liberar espaço.

---

### US-01.09 — Acompanhar a vinculação pendente e atualizar a situação

**Atores:** Não-Filiado

> **Como** Não-Filiado — que ainda não vinculou, que está com vinculação pendente ou cuja filiação expirou e foi renovada
> **Quero** saber em que pé está minha filiação e poder atualizar a situação a qualquer momento
> **Para** concluir sozinho assim que o PMI atualizar a base

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil > Filiação PMI", **quando** a página carrega, **então** vejo meu status atual e o botão **"Atualizar situação"**, cujo comportamento depende do status:
   - `SEM_VINCULO` → abre o formulário de PMI ID e e-mail do PMI (US-01.07);
   - `PENDENTE` → repete a consulta ao PMI com os dados já informados, com opção de corrigi-los;
   - `EXPIRADO` → repete a consulta ao PMI (US-01.11, critério 5), com opção de corrigir os dados;
   - `AGUARDANDO_CODIGO` → retoma a tela do código (US-01.08).
2. **Dado** que estou em `PENDENTE`, **quando** vejo a seção, **então** vejo o PMI ID informado, a data da solicitação, a data/hora da última verificação e o motivo (não localizado ou PMI indisponível).
3. **Dado** que clico em "Atualizar situação" em `PENDENTE` ou `EXPIRADO`, **quando** a consulta roda, **então** as regras de US-01.07 são reavaliadas e, se localizado e conferindo, sigo para o código (ou direto a `APROVADO`); caso contrário, vejo a resposta correspondente.
4. **Dado** que usei "Atualizar situação" há menos de 10 minutos, **quando** clico de novo, **então** vejo o tempo restante e nenhuma consulta é feita.
5. **Dado** que faço login em `PENDENTE`, **quando** entro, **então** a plataforma reconsulta automaticamente em segundo plano e, se localizado, me conduz ao próximo passo com um aviso na home.
6. **Dado** que continuo `PENDENTE` após 1 dia, **quando** acesso a seção, **então** vejo a orientação para contato com a diretoria e a opção de enviar comprovante para análise manual (US-01.10); a conta continua `PENDENTE`, sem reprovação automática.
7. **Dado** que estou em qualquer status que não seja `APROVADO` ou `CONCEDIDO_MANUALMENTE`, **quando** navego pela plataforma, **então** tenho **exatamente** o acesso de Não-Filiado: prévias desfocadas em benefícios, conteúdo, oportunidades e conquistas.

**Regras de negócio**

- RN-01.09.1 — `PENDENTE`, `AGUARDANDO_CODIGO` e `EXPIRADO` nunca concedem acesso de Filiado, em nenhuma tela ou endpoint.
- RN-01.09.2 — "Atualizar situação": no máximo 1 consulta ao PMI a cada 10 minutos por pessoa; a reconsulta no login conta para esse limite.
- RN-01.09.3 — Não há reprovação automática: a pessoa permanece `PENDENTE` até ser localizada, corrigir os dados ou o Admin decidir (ÉP-02, US-02.11).
- RN-01.09.4 — O texto para não localizado nunca afirma que a pessoa "não é filiada"; informa apenas que o vínculo ainda não foi confirmado (ÉP-02, RN-02.06.2).

---

### US-01.10 — Enviar comprovante para análise manual

**Atores:** Não-Filiado

> **Como** Não-Filiado que continua não localizado na base do PMI
> **Quero** enviar um comprovante da minha filiação para a diretoria analisar
> **Para** não ficar sem acesso enquanto a base do PMI não é atualizada

**Critérios de aceite**

1. **Dado** que estou `PENDENTE` há mais de 1 dia, **quando** acesso "Filiação PMI", **então** vejo a opção "Enviar comprovante para análise" com a orientação do que anexar (recibo de filiação ou tela do MyPMI mostrando PMI ID, capítulo e validade).
2. **Dado** que anexo um PDF, JPG ou PNG de até 5 MB, **quando** envio, **então** o comprovante fica associado ao meu vínculo com status "Em análise", recebo confirmação por e-mail e o Admin vê o documento no meu detalhe (ÉP-02, US-02.11, critério 3).
3. **Dado** que o Admin concede a filiação, **quando** entro, **então** meu status é `CONCEDIDO_MANUALMENTE`, tenho acesso de Filiado e recebo e-mail.
4. **Dado** que o Admin não concede, **quando** acesso o perfil, **então** vejo a decisão e o motivo, continuo `PENDENTE` e posso corrigir os dados ou enviar novo comprovante (máximo de 3 envios).
5. **Dado** que estou `CONCEDIDO_MANUALMENTE`, **quando** a reconsulta no login (US-01.04) finalmente localiza minha filiação ativa e confere o e-mail, **então** o vínculo é promovido a `APROVADO` sem nenhuma ação minha.

**Regras de negócio**

- RN-01.10.1 — O comprovante é dado pessoal sensível: acessível só ao Admin, removido 90 dias após a decisão.
- RN-01.10.2 — A concessão manual é decisão do Admin (ÉP-02, RN-02.11.1); o envio do comprovante não altera o status por si só.
- RN-01.10.3 — Arquivo validado por conteúdo (*magic bytes*), não apenas por extensão.

---

## 4. Histórias do Filiado

### US-01.11 — Manter minha filiação revalidada automaticamente

**Atores:** Filiado

> **Como** Filiado
> **Quero** que a plataforma confira periodicamente minha filiação junto ao PMI, sem me atrapalhar
> **Para** que o acesso exclusivo continue apenas enquanto minha filiação estiver vigente

**Critérios de aceite**

1. **Dado** que sou `APROVADO` e minha última verificação tem mais de 7 dias **ou** a data de expiração já passou, **quando** faço login, **então** a plataforma reconsulta o PMI em segundo plano; o login não espera a resposta.
2. **Dado** que a filiação continua ativa, **quando** a revalidação conclui, **então** a data de expiração e a data/hora da última verificação são atualizadas.
3. **Dado** que a filiação consta como expirada, não renovada ou inativa, **quando** a revalidação conclui, **então** o status vira `EXPIRADO`, o acesso de Filiado é suspenso **imediatamente** (as sessões são renovadas com o novo status), passo a ver as prévias desfocadas, vejo o aviso "Sua filiação venceu em dd/mm. Renove no PMI e depois use 'Atualizar situação' para recuperar o acesso" e recebo e-mail transacional com o link de renovação.
4. **Dado** que estou `EXPIRADO`, **quando** navego, **então** conta, perfil, histórico de eventos, resgates e conquistas são preservados (ficam ocultos ou sem os códigos, conforme cada épico) e a plataforma se comporta como para qualquer Não-Filiado.
5. **Dado** que renovei no PMI, **quando** clico em "Atualizar situação" (ou faço login e a reconsulta automática roda), **então** a filiação é localizada ativa e volto a `APROVADO` sem repetir o código, pois o e-mail do PMI já foi comprovado.
6. **Dado** que o PMI está indisponível na revalidação, **quando** a consulta falha, **então** **nada muda**: mantenho o último status conhecido e a próxima tentativa ocorre no próximo login.
7. **Dado** que estou a 30 dias ou menos da data de expiração, **quando** acesso a plataforma, **então** vejo um lembrete discreto "Sua filiação vence em dd/mm — renove no PMI para não perder o acesso", para que a renovação aconteça antes do bloqueio.
8. **Dado** que acesso "Meu perfil > Filiação PMI", **quando** a página carrega, **então** vejo o selo de Filiado, o PMI ID, a data de expiração, a data/hora da última verificação e o botão "Atualizar situação".
9. **Dado** que nunca faço login, **quando** minha filiação vence, **então** não sou revalidado até o próximo acesso — e nesse acesso a revalidação roda antes de qualquer conteúdo exclusivo ser entregue.

**Regras de negócio**

- RN-01.11.1 — Gatilhos de revalidação: login (última verificação > 7 dias ou expiração vencida) e "Atualizar situação" (limite de 1 a cada 10 minutos). Não há *job* em lote no MVP.
- RN-01.11.2 — Rebaixamento só com resposta **positiva e explícita** do PMI (expirada, não renovada, inativa ou sem o PMI-DF entre os capítulos). Ausência de dado ou falha ≠ expiração.
- RN-01.11.3 — **Não há carência**: a partir do momento em que o PMI deixa de reconhecer a filiação, a pessoa é Não-Filiado em todos os épicos. Conta, histórico e conquistas não são apagados.
- RN-01.11.4 — Se a data de expiração registrada já passou e a revalidação ainda não rodou (pessoa sem login desde então), o backend trata a pessoa como Não-Filiado até a revalidação confirmar a renovação — a data de expiração é verificada a cada requisição a recurso exclusivo.
- RN-01.11.5 — Se o e-mail do PMI mudou na base, a volta a `APROVADO` exige novo código no novo e-mail (US-01.08).
- RN-01.11.6 — `CONCEDIDO_MANUALMENTE` não é rebaixado pela revalidação; só o Admin revoga (ÉP-02, RN-02.11.1).
- RN-01.11.7 — Toda transição de status gera registro de auditoria com origem (login, "Atualizar situação", Admin).

---

### US-01.12 — Encerrar minhas sessões

**Atores:** Não-Filiado, Filiado, Admin

> **Como** pessoa autenticada
> **Quero** sair da plataforma e ver onde estou conectada
> **Para** proteger minha conta em dispositivos compartilhados

**Critérios de aceite**

1. **Dado** que clico em "Sair", **quando** confirmo, **então** a sessão atual é encerrada, o *refresh token* revogado e volto à página pública.
2. **Dado** que acesso "Meu perfil > Segurança", **quando** a página carrega, **então** vejo cada sessão ativa com dispositivo, navegador, localização aproximada e último acesso, com a opção de encerrar uma ou todas as outras.
3. **Dado** que troco a senha ou redefino por recuperação, **quando** concluo, **então** todas as outras sessões são encerradas.
4. **Dado** que um *refresh token* já usado é apresentado de novo (indício de roubo), **quando** a plataforma detecta, **então** toda a família de tokens é invalidada e recebo e-mail de alerta.

**Regras de negócio**

- RN-01.12.1 — *Refresh token* rotativo com detecção de reuso.
- RN-01.12.2 — Sessões administrativas expiram por inatividade em 30 minutos; sessões comuns, em 7 dias (30 com "manter-me conectado").

---

## 5. Histórias do Admin

### US-01.13 — Consultar vínculos e tentativas e desfazer um vínculo

**Atores:** Admin

> **Como** Admin
> **Quero** ver o vínculo PMI de cada pessoa, o histórico de tentativas e poder desfazer um vínculo
> **Para** resolver casos de cadastro, atender a diretoria e detectar uso indevido de PMI ID

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa no console (ÉP-02, US-02.10), **quando** a página carrega, **então** vejo a seção "Filiação PMI" com status, PMI ID, e-mail do PMI, capítulo, data de expiração, data/hora da última verificação, origem (automática ou manual) e o comprovante, se houver (US-01.10).
2. **Dado** que estou no detalhe, **quando** clico em "Consultar o PMI agora", **então** a plataforma reconsulta e atualiza o status conforme as regras de US-01.07 e US-01.11, e a ação fica em auditoria.
3. **Dado** que preciso desfazer um vínculo (PMI ID usado por pessoa errada, conta duplicada), **quando** informo o motivo e confirmo, **então** a pessoa volta a `SEM_VINCULO`, o PMI ID fica livre para nova vinculação, as sessões dela são encerradas, ela recebe e-mail transacional com o motivo e a ação fica em auditoria.
4. **Dado** que acesso "Vinculações" no console, **quando** a lista carrega, **então** vejo todas as pessoas com status de vinculação, PMI ID, expiração e última verificação, com filtros por status e busca por nome, e-mail ou PMI ID, e exportação em CSV registrada em auditoria.
5. **Dado** que acesso "Tentativas de vinculação", **quando** a lista carrega, **então** vejo conta, PMI ID informado, IP, resultado e data/hora, com filtros por resultado e período.
6. **Dado** que um mesmo PMI ID foi tentado por 3 ou mais contas diferentes em 24 horas, ou uma conta excedeu os limites de US-01.07, **quando** acesso o console, **então** vejo o alerta correspondente.
7. **Dado** que acesso "Integrações > PMI (ThoughtSpot)", **quando** a página carrega, **então** vejo o estado da integração (última consulta bem-sucedida, taxa de erro, *circuit breaker* aberto/fechado) e o log das últimas consultas com data, duração e resultado, sem dados pessoais.

**Regras de negócio**

- RN-01.13.1 — O Admin **não** edita PMI ID, e-mail do PMI ou data de expiração; só desfaz o vínculo (a pessoa refaz) ou concede/revoga manualmente (ÉP-02, US-02.11).
- RN-01.13.2 — Desfazer vínculo exige motivo e é sempre auditado; não apaga histórico de eventos, resgates ou conquistas.
- RN-01.13.3 — A exportação de vínculos identifica filiados: ação sensível, sempre em auditoria.

---

### US-01.14 — Conceder e revogar o papel de Admin

**Atores:** Admin

> **Como** Admin
> **Quero** conceder e revogar o papel de Admin a outras pessoas
> **Para** que cada gestão do capítulo opere a plataforma com autonomia e rastreabilidade

**Critérios de aceite**

1. **Dado** que acesso "Administradores" no console, **quando** a lista carrega, **então** vejo quem tem o papel, quem concedeu e desde quando.
2. **Dado** que concedo o papel a uma pessoa cadastrada e confirmada, **quando** confirmo, **então** ela passa a ver o console no próximo *token* (até 15 minutos) ou no próximo login, é notificada por e-mail e a ação fica em auditoria.
3. **Dado** que revogo o papel, **quando** confirmo, **então** as sessões administrativas da pessoa são encerradas imediatamente, ela é notificada e a ação fica em auditoria.
4. **Dado** que sou o único Admin, **quando** tento revogar meu próprio papel, **então** a operação é bloqueada.
5. **Dado** que uma gestão termina, **quando** seleciono vários administradores e clico em "Revogar selecionados", **então** a revogação ocorre em lote, com um único registro de auditoria por pessoa.

**Regras de negócio**

- RN-01.14.1 — O papel de Admin é independente da filiação: um Admin pode ser Filiado ou não, e mantém o próprio perfil como qualquer usuário.
- RN-01.14.2 — Único nível administrativo no MVP; o modelo de dados guarda o papel em tabela própria (pessoa, papel, concedido por, concedido em) para permitir papéis granulares no futuro sem migração.
- RN-01.14.3 — Toda ação administrativa em qualquer épico é autorizada no backend pelo papel gravado, nunca por indicação vinda do frontend.

---

## 6. Decisões e questões em aberto

### Decisões

- **Não-Filiado entra na plataforma.** O fluxo de vinculação original previa que a conta sem vínculo visse apenas a tela de vinculação. Alinhado com [Perfis de Usuário](../planejamento/perfil-usuario.md) e com os épicos 04, 06, 07 e 08: a conta confirmada entra como Não-Filiado com acesso *freemium* (prévias desfocadas); a vinculação é oferecida no cadastro e no perfil, mas pode ser pulada. Só `APROVADO` e `CONCEDIDO_MANUALMENTE` têm acesso de Filiado.
- **`PENDENTE`, `AGUARDANDO_CODIGO` e `EXPIRADO` nunca dão acesso de Filiado.** Declarar-se filiado não concede nada; a confirmação do PMI e a posse do e-mail do PMI são obrigatórias.
- **Acabou a filiação, acabou o acesso — sem carência.** Quando a revalidação deixa de reconhecer a filiação, a pessoa é Não-Filiado imediatamente. Um lembrete 30 dias antes do vencimento (US-01.11, critério 7) substitui a carência como mecanismo para evitar surpresa. Renovou no PMI → "Atualizar situação" restabelece o acesso sem novo código.
- **Uma confirmação só no caso comum.** O cadastro pergunta "Já é filiado?" e pede o PMI ID, com a dica de usar o e-mail do MyPMI. Confirmado o e-mail da conta, a plataforma consulta o PMI automaticamente; se o e-mail do PMI for o mesmo, a filiação é aprovada sem segunda etapa. Só quando os e-mails são diferentes há um segundo código — inevitável por segurança e aproveitado: o e-mail do PMI vira e-mail alternativo verificado para o Sympla.
- **"Atualizar situação"** é o único ponto de entrada para quem já tem conta: abre o formulário (sem vínculo), reconsulta o PMI (pendente ou expirado) ou retoma o código. Corresponde ao "Verificar agora" do fluxo original; o nome foi unificado. A história correspondente no perfil é a US-02.06 do ÉP-02.
- **Cadastro completo desde o início.** Todos os dados obrigatórios de "Meu perfil" (ÉP-02, RN-02.01.1) são exigidos no cadastro, em etapas; LinkedIn é o único opcional. Quem entra via Google completa o cadastro antes de acessar qualquer tela.
- **Idade mínima de 18 anos** no cadastro.
- **Verificação da conta por código de 6 dígitos (com link equivalente)**, e não apenas por link: a pessoa permanece na mesma tela e o mesmo componente serve para o código do PMI.
- **Consulta em tempo real, sem *batch* diário.** Diferente da v1 (job às 03:00), a revalidação acontece no login (última verificação > 7 dias ou expiração vencida) e sob demanda. A data de expiração gravada é verificada a cada requisição a recurso exclusivo, então quem não entra desde o vencimento também perde o acesso.
- **Data de expiração é armazenada e exibida** em "Meu perfil > Filiação PMI" (ÉP-02, US-02.07).
- **Concessão manual continua no ÉP-02** (US-02.11); este épico apenas fornece o comprovante (US-01.10) e o status `CONCEDIDO_MANUALMENTE`, promovido a `APROVADO` se a base do PMI passar a reconhecer a filiação.
- **Nome vem do PMI após a aprovação.** O nome digitado no cadastro é substituído pelo nome da base do PMI e fica somente leitura (ÉP-02, US-02.07).
- **Único nível de Admin**, concedido por outro Admin (US-01.14); papéis granulares (conteúdo, eventos, benefícios) ficam para depois, com o modelo de dados já preparado.
- **Fora do escopo:** login com LinkedIn ou outros provedores; autenticação em dois fatores (2FA) para usuários comuns; cadastro de Estudante com comprovante de matrícula (item 16 do brainstorming); aprovação manual de cadastro (item 17) — qualquer pessoa cria conta; sincronização em lote da base inteira do PMI.

### Questões em aberto

1. **Acesso ao ThoughtSpot do PMI (bloqueante — spike).** O capítulo tem credencial de serviço com permissão de API? O PMI libera um *worksheet/model* consultável por `searchdata` ou apenas um *liveboard*? Quais os nomes exatos das colunas e os valores possíveis de situação da filiação? Existe limite de consultas por dia? Se a API não for autorizada, o plano B é importação periódica de arquivo (CSV) do relatório de filiados, mantendo o mesmo fluxo de vinculação sobre a base local.
2. **Cadência das fotografias.** Mensal, semanal? Define o texto "pode levar alguns dias" e o prazo de 1 dia para oferecer o comprovante (US-01.09/01.10).
3. **Capítulos múltiplos.** A base traz o capítulo como coluna multivalorada, uma linha por capítulo, ou apenas o capítulo principal? Impacta a regra "PMI-DF entre os capítulos" (RN-01.07.1).
4. **Data de início da filiação.** A base traz *member since*? Se sim, o badge "Filiado há N anos" (ÉP-08) pode usar a data real em vez da data de reconhecimento na plataforma.
5. **Renovação e fotografia mensal.** Se a base do PMI só é atualizada mensalmente, quem renova no dia seguinte ao vencimento pode ficar semanas sem acesso mesmo estando em dia. Confirmar a cadência (questão 2) e, se for o caso, orientar o filiado a renovar antes do vencimento (lembrete de 30 dias) ou prever concessão manual temporária pelo Admin mediante comprovante de renovação.
6. **Prazo para oferecer análise manual**: 1 dia em `PENDENTE` é suficiente, ou deve aguardar a próxima fotografia do PMI antes de acionar a diretoria?
7. **Quem é o primeiro Admin?** A concessão inicial acontece por configuração na implantação; definir o responsável.
8. **Reconsulta no login em `PENDENTE`** (US-01.04, critério 4) pode gerar volume alto em dia de evento; confirmar o limite de consultas do PMI antes de manter esse gatilho.

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 13/09/2026 | Criação do documento — histórias do ÉP-01 (Autenticação e Validação de Filiação) para Visitante, Não-Filiado, Filiado e Admin, com o fluxo de vinculação ao PMI via ThoughtSpot, estados de vinculação, confirmação por código e revalidação com carência | Vitor Leonardo | Nome do revisor |
| 1.1 | 13/09/2026 | Remoção da carência (expirou → Não-Filiado imediatamente) com lembrete prévio de 30 dias; idade mínima de 18 anos; jornada com uma única confirmação para quem se cadastra com o e-mail do PMI (PMI ID opcional no cadastro, consulta automática após confirmar o e-mail, e-mail do PMI vira alternativo verificado); "Verificar agora" renomeado para "Atualizar situação" e detalhado por status; onboarding reduzido a dois passos | Vitor Leonardo | Nome do revisor |
