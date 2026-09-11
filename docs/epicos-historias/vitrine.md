# ÉP-07 — Vitrine

## 1. Visão geral do épico

**Objetivo:** conectar a comunidade do PMI-DF ao mercado em duas direções, dentro de uma única área da plataforma:

- **Profissionais da comunidade** — uma vitrine dos filiados que optaram por se apresentar, com seus dados profissionais (nome, foto, cargo, empresa, mini bio, LinkedIn). Visível a todos os usuários da plataforma; só Filiados aparecem.
- **Oportunidades** — um mural de vagas enviadas por empresas e recrutadores, cadastradas pelo Admin da forma mais simples possível (poucas informações e um link ou um e-mail para se candidatar). Exclusivo para Filiados.

**Valor:** responde ao item 9 do brainstorming (*oportunidades profissionais — conexão com vagas e empresas*), priorizado, e ao item 24 (*vitrine de currículos*), reduzido aqui a uma apresentação profissional *opt-in*. Não há candidatura, funil, currículo anexado ou triagem: a plataforma **divulga e conecta**, o processo seletivo acontece fora dela.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. As vagas e o meio de candidatura só saem para Filiados; dos profissionais, só saem dados profissionais — nunca e-mail, telefone, endereço ou dados demográficos.

### 1.1 As duas visões

| Visão | Quem aparece | Quem vê | Quem cadastra |
|-------|--------------|---------|---------------|
| **Profissionais da comunidade** | Filiados com filiação ativa que ativaram a opção em "Meu perfil" (ÉP-02, US-02.08) | Filiados e Não-Filiados | O próprio Filiado, pelo perfil |
| **Oportunidades** | Vagas cadastradas pelo Admin | Filiados; Não-Filiado vê prévia desfocada | Admin |

### 1.2 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia desfocada · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Profissionais da comunidade (lista e perfil profissional) | L | L | L |
| Aparecer em Profissionais da comunidade | — | E (via ÉP-02) | — |
| Ocultar um profissional da vitrine | — | — | E |
| Oportunidades (lista, detalhe e meio de candidatura) | P | L | L |
| Cadastro, edição e encerramento de oportunidades | — | — | E |

### 1.3 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-07.01](#us-0701-consultar-os-profissionais-da-comunidade) | Consultar os profissionais da comunidade | Filiado, Não-Filiado | Should |
| [US-07.02](#us-0702-ver-a-previa-desfocada-das-oportunidades) | Ver a prévia desfocada das oportunidades | Não-Filiado | Must |
| [US-07.03](#us-0703-consultar-as-oportunidades) | Consultar as oportunidades | Filiado | Must |
| [US-07.04](#us-0704-cadastrar-e-publicar-uma-oportunidade) | Cadastrar e publicar uma oportunidade | Admin | Must |
| [US-07.05](#us-0705-manter-as-oportunidades) | Manter as oportunidades | Admin | Must |
| [US-07.06](#us-0706-ocultar-um-profissional-da-vitrine) | Ocultar um profissional da vitrine | Admin | Could |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-07.XX.N).

---

## 2. Histórias do Filiado e do Não-Filiado

### US-07.01 — Consultar os profissionais da comunidade

**Atores:** Filiado, Não-Filiado

> **Como** usuário da plataforma
> **Quero** conhecer os profissionais filiados ao capítulo que se apresentaram na vitrine
> **Para** fazer networking, encontrar talentos e me conectar pelo LinkedIn

**Critérios de aceite**

1. **Dado** que acesso "Vitrine > Profissionais da comunidade", **quando** a página carrega, **então** vejo os profissionais em *cards* com foto, nome, cargo, empresa e cidade/UF, em ordem alfabética.
2. **Dado** que busco por nome, cargo ou empresa, **quando** digito, **então** a listagem é filtrada.
3. **Dado** que filtro por cidade/UF, **quando** aplico, **então** a listagem responde.
4. **Dado** que abro um profissional, **quando** o perfil carrega, **então** vejo foto, nome, cargo, empresa, cidade/UF, mini bio, o selo de filiado e o botão "Ver no LinkedIn", que abre o perfil em nova aba.
5. **Dado** que o profissional não informou LinkedIn, **quando** abro o perfil, **então** o botão não é exibido.
6. **Dado** que sou Filiado e ainda não me apresentei na vitrine, **quando** acesso a aba, **então** vejo um convite discreto "Apareça aqui também" com link para "Meu perfil > Preferências" (ÉP-02, US-02.08).
7. **Dado** que sou Não-Filiado, **quando** acesso a aba, **então** vejo os profissionais normalmente e uma nota discreta "Apenas filiados podem se apresentar aqui", com o CTA "Quero ser filiado".
8. **Dado** que inspeciono a resposta da API, **quando** analiso o conteúdo, **então** e-mail, telefone, endereço e dados demográficos dos profissionais **não estão presentes**.

**Regras de negócio**

- RN-07.01.1 — Só **aparecem** Filiados com filiação **ativa** que ativaram a exibição em "Meu perfil" (ÉP-02, RN-02.08.1 e RN-02.08.3). A visualização é aberta a Filiados e Não-Filiados.
- RN-07.01.2 — Só dados **profissionais** são exibidos: nome, foto, cargo, empresa, cidade/UF, mini bio e LinkedIn (ÉP-02, RN-02.08.2).
- RN-07.01.3 — Não há mensagem interna, contato ou "convite" dentro da plataforma; a conexão acontece pelo LinkedIn.
- RN-07.01.4 — A vitrine exige autenticação e não é indexável por buscadores; visitantes não autenticados não a acessam.

---

## 3. Histórias do Não-Filiado

### US-07.02 — Ver a prévia desfocada das oportunidades

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que o capítulo divulga vagas aos filiados
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Vitrine > Oportunidades", **então** vejo os *cards* das vagas ativas **desfocados** (efeito *blur*), de modo que dá para perceber logos e a quantidade de vagas, mas não ler título, empresa ou detalhes.
2. **Dado** que a página está desfocada, **quando** olho a tela, **então** vejo sobre ela um painel fixo com a mensagem "Exclusivo para filiados", o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
3. **Dado** que tento clicar em um *card* desfocado, **quando** o clique acontece, **então** nada abre.
4. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas a quantidade de vagas e os logos — título, empresa, descrição, link e e-mail **não estão presentes**.
5. **Dado** que clico em "Quero ser filiado", **quando** o clique acontece, **então** o evento é registrado com origem `vitrine` para medir conversão.

**Regras de negócio**

- RN-07.02.1 — O desfoque é aplicado no frontend, mas a **proteção é a omissão de campos no backend**.
- RN-07.02.2 — Visitantes não autenticados veem a mesma prévia; a diferença é o CTA, que primeiro pede cadastro.
- RN-07.02.3 — O mesmo componente de prévia desfocada do clube de benefícios (ÉP-04, US-04.01) é reutilizado aqui.

---

## 4. Histórias do Filiado

### US-07.03 — Consultar as oportunidades

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver as vagas divulgadas pelo capítulo e saber como me candidatar
> **Para** aproveitar as oportunidades de carreira que a filiação me dá acesso

**Critérios de aceite**

1. **Dado** que sou Filiado ativo, **quando** acesso "Vitrine > Oportunidades", **então** vejo as vagas ativas da mais recente para a mais antiga, em *cards* com título, empresa, modalidade (presencial, remoto ou híbrido), localidade e data de publicação.
2. **Dado** que busco por texto ou filtro por modalidade, **quando** aplico, **então** a listagem responde.
3. **Dado** que abro uma vaga, **quando** a página carrega, **então** vejo a descrição completa e um único botão de ação: "Candidatar-se" (quando a vaga tem link) ou "Enviar e-mail" (quando a vaga tem e-mail do recrutador).
4. **Dado** que clico em "Candidatar-se", **quando** o clique acontece, **então** o link informado pelo Admin abre em nova aba.
5. **Dado** que clico em "Enviar e-mail", **quando** o clique acontece, **então** meu aplicativo de e-mail abre com destinatário e assunto "Candidatura — [título da vaga] — via PMI-DF".
6. **Dado** que a vaga está a 7 dias ou menos do encerramento, **quando** a visualizo, **então** vejo o aviso com a data limite.
7. **Dado** que abro o link de uma vaga já encerrada, **quando** a página carrega, **então** vejo "Oportunidade encerrada" e a lista das vagas ativas.
8. **Dado** que não há vagas ativas, **quando** acesso a aba, **então** vejo uma mensagem explicativa em vez de uma página vazia.
9. **Dado** que minha filiação deixou de ser reconhecida, **quando** acesso a aba, **então** vejo a prévia desfocada de US-07.02.

**Regras de negócio**

- RN-07.03.1 — Oportunidades são **exclusivas para Filiados** com filiação ativa; a verificação é feita no backend a cada requisição.
- RN-07.03.2 — Não há candidatura, envio de currículo ou qualquer interação **dentro** da plataforma; o processo seletivo acontece no link ou por e-mail, fora dela.
- RN-07.03.3 — O e-mail do recrutador só é entregue **após o clique** em "Enviar e-mail", nunca na listagem — protege o recrutador contra coleta automatizada.

---

## 5. Histórias do Admin

### US-07.04 — Cadastrar e publicar uma oportunidade

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar uma vaga em poucos passos, informando apenas o essencial e como se candidatar
> **Para** divulgar rapidamente aos filiados as oportunidades que empresas e recrutadores enviam ao capítulo

**Critérios de aceite**

1. **Dado** que crio uma oportunidade, **quando** informo **título**, **empresa**, **descrição** e **como se candidatar** (um link **ou** um e-mail), **então** ela é salva como `RASCUNHO` — nada mais é obrigatório.
2. **Dado** que quero enriquecer a vaga, **quando** preencho os campos opcionais — modalidade (presencial, remoto ou híbrido), localidade, logo da empresa e data de encerramento —, **então** eles aparecem no detalhe da vaga.
3. **Dado** que escolho "link", **quando** salvo, **então** a URL é validada; **dado** que escolho "e-mail", **quando** salvo, **então** o endereço é validado. Não é possível salvar sem um dos dois.
4. **Dado** que não informo data de encerramento, **quando** salvo, **então** a plataforma aplica 60 dias a partir da publicação.
5. **Dado** que publico a vaga, **quando** confirmo, **então** ela aparece imediatamente em "Oportunidades" para os filiados (e desfocada para não-filiados).
6. **Dado** que quero conferir antes, **quando** clico em "Pré-visualizar", **então** vejo a vaga exatamente como o filiado a verá.

**Regras de negócio**

- RN-07.04.1 — Cadastro mínimo: título, empresa, descrição e um meio de candidatura (link ou e-mail). Tudo o mais é opcional.
- RN-07.04.2 — A vaga tem **um único** meio de candidatura; se a empresa oferece os dois, o Admin escolhe o principal.
- RN-07.04.3 — A descrição aceita formatação básica e é sanitizada no backend.
- RN-07.04.4 — A publicação é registrada em auditoria com usuário, data e hora.

---

### US-07.05 — Manter as oportunidades

**Atores:** Admin

> **Como** Admin
> **Quero** editar, encerrar e republicar as vagas
> **Para** manter a vitrine sempre atual para os filiados

**Critérios de aceite**

1. **Dado** que acesso "Oportunidades" no console, **quando** a lista carrega, **então** vejo todas as vagas — de qualquer status (`RASCUNHO`, `PUBLICADA`, `ENCERRADA`) — com título, empresa, modalidade, data de publicação, data de encerramento e status.
2. **Dado** que filtro por status ou busco por texto, **quando** aplico, **então** a lista reflete os critérios.
3. **Dado** que edito uma vaga publicada, **quando** salvo, **então** a alteração é imediata e a versão anterior fica na auditoria.
4. **Dado** que encerro uma vaga antes do prazo (preenchida ou cancelada pela empresa), **quando** confirmo, **então** ela sai da vitrine na hora e o link direto passa a exibir "Oportunidade encerrada".
5. **Dado** que a data de encerramento chega, **quando** a rotina diária roda, **então** a vaga é encerrada automaticamente.
6. **Dado** que quero republicar uma vaga encerrada, **quando** clico em "Duplicar", **então** um novo rascunho é criado com os mesmos dados e nova data de encerramento.

**Regras de negócio**

- RN-07.05.1 — **Exclusão física é proibida**; vagas são encerradas, nunca apagadas, para preservar o histórico de divulgação.
- RN-07.05.2 — A plataforma **não** conta visualizações nem cliques em vagas; o objetivo é divulgar, não medir interesse.

---

### US-07.06 — Ocultar um profissional da vitrine

**Atores:** Admin

> **Como** Admin
> **Quero** ocultar um perfil de "Profissionais da comunidade" quando ele violar as regras do capítulo
> **Para** proteger a vitrine de conteúdo impróprio ou de perfis falsos

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa no console (ÉP-02, US-02.10), **quando** ela está exibida na vitrine, **então** vejo a opção "Ocultar da vitrine".
2. **Dado** que oculto um perfil, **quando** informo o motivo e confirmo, **então** ele sai da vitrine imediatamente, a pessoa recebe e-mail transacional com o motivo e a ação fica em auditoria.
3. **Dado** que um perfil está oculto pelo Admin, **quando** a pessoa acessa "Preferências", **então** vê a opção de exibição desabilitada com a nota "Ocultado pelo capítulo" e o motivo.
4. **Dado** que reverto a ocultação, **quando** confirmo, **então** o perfil volta à vitrine se a pessoa ainda tiver a opção ativada.

**Regras de negócio**

- RN-07.06.1 — Ocultação exige motivo obrigatório e é reversível.
- RN-07.06.2 — A ocultação pelo Admin prevalece sobre a escolha da pessoa até ser revertida.

---

## 6. Decisões e questões em aberto

### Decisões

- **Duas visões em uma área, com acessos opostos**: "Profissionais da comunidade" é visível a todos os usuários (Filiados e Não-Filiados), mas só Filiados aparecem; "Oportunidades" é exclusiva para Filiados, com prévia desfocada para Não-Filiados.
- **Aparecer na vitrine é *opt-in*** pela marcação em "Meu perfil" (ÉP-02, US-02.08). Só dados profissionais são exibidos; e-mail, telefone, endereço e dados demográficos nunca.
- **Sem "aberto a oportunidades", sem currículo, sem mensagens internas.** A vitrine apresenta; a conexão acontece no LinkedIn.
- **Oportunidades são um mural, não um ATS.** Cadastro mínimo de quatro campos (título, empresa, descrição, link ou e-mail); sem candidatura dentro da plataforma, sem funil, sem triagem. Confirma o ponto A-1 da v1 e o refinamento ("não tem interação do usuário, somente para ver").
- **Um único meio de candidatura por vaga** (link ou e-mail), para manter o cadastro simples e o botão de ação inequívoco.
- **Sem métricas de vagas.** A plataforma não conta visualizações nem cliques; o objetivo é divulgar aos filiados.
- **Fora do escopo:** página pública compartilhável com preview em redes sociais (v1 US-07.03), cadastro de vaga pela própria empresa, filtros por nível ou faixa salarial, alertas de novas vagas por e-mail, exportação de vagas.

### Questões em aberto

1. **Visitantes não autenticados devem ver "Profissionais da comunidade"?** Hoje a vitrine exige login (RN-07.01.4), pois expõe nomes, fotos e empresas de filiados; abrir à internet torna esses dados indexáveis por buscadores.
2. **Quem envia as vagas ao capítulo e por qual canal?** O fluxo de recebimento (e-mail da diretoria, formulário externo) está fora da plataforma; definir para que o Admin tenha o que cadastrar.
3. **Ordenação da vitrine de profissionais**: alfabética (atual) ou aleatória a cada acesso, para não privilegiar sempre os mesmos nomes?

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-07 (Vitrine: Profissionais da comunidade e Oportunidades) para Não-Filiado, Filiado e Admin | Vitor Leonardo | Nome do revisor |
| 1.1 | 11/09/2026 | Inversão do modelo de acesso: Profissionais da comunidade visível a todos (só Filiados aparecem); Oportunidades exclusivas para Filiados com prévia desfocada; remoção de métricas de visualizações/cliques e da exportação | Vitor Leonardo | Nome do revisor |
