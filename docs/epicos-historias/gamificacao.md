# ÉP-08 — Gamificação

## 1. Visão geral do épico

**Objetivo:** reconhecer e recompensar a participação dos filiados no capítulo por meio de **quebra-cabeças colecionáveis**: a cada presença confirmada em evento, o filiado ganha um **crédito**; cada crédito pode ser trocado, no clique de um dado, por uma peça de um quebra-cabeça — revelando aos poucos imagens que contam a história do PMI-DF.

**Valor:** responde aos itens 10 e 11 do brainstorming (*gamificação para engajamento*, *pertencimento e exclusividade*). A mecânica é ancorada em um único fato verificável já registrado pela plataforma — check-in sincronizado do Sympla (ÉP-05) — o que a torna simples de auditar e resistente a fraude sem nenhuma verificação adicional.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** todo crédito e toda peça são calculados no **backend** a partir de eventos de domínio já auditados; a única ação manual do usuário é o **clique no dado**, que ou sorteia qual quebra-cabeça ele vai montar (grátis) ou troca um crédito já existente por uma peça. Nenhuma peça é solicitada, submetida ou digitada. A imagem final de um quebra-cabeça **nunca** é entregue ao navegador antes da conclusão.

> **Nota de versão:** esta é uma reformulação completa do épico. A versão anterior (v1.0) previa duas mecânicas — badges e quebra-cabeças. **Badges foram removidos do escopo.** A gamificação passa a ter uma única mecânica, com uma interação explícita (o dado) no lugar da concessão silenciosa. Ver seção 5 para o comparativo.

### 1.1 O ciclo do jogo

1. Filiado tem presença confirmada em um evento (check-in Sympla, ÉP-05) → ganha **1 crédito**.
2. Sem quebra-cabeça em andamento → aparece um **dado grande** na aba "Gamificação" → clica → a plataforma **sorteia** um quebra-cabeça entre os publicados que ele ainda não montou (ação **gratuita**, não gasta crédito) → o tabuleiro aparece com **8 peças cinzas**, sem nenhuma pista do que é a imagem.
3. Com quebra-cabeça em andamento e **pelo menos 1 crédito** → o dado aparece disponível para trocar um crédito por uma peça → clica → a plataforma **sorteia aleatoriamente** uma das posições que ele ainda não tem, revela aquela peça e desconta 1 crédito.
4. Ao reunir as 8 peças → quebra-cabeça **concluído**: imagem completa, nome, legenda e história são revelados; ele vai para "Concluídos".
5. Sem quebra-cabeça em andamento de novo → volta ao passo 2. **Só é possível sortear um novo quebra-cabeça depois de terminar o atual.**

### 1.2 Validação técnica

#### Modelo

- `quebra_cabeca`: imagem (proporção **4:2**, deitada), nome, legenda curta, história curta, grid fixo **2×4** (8 peças), silhueta pública (prévia borrada), status (`RASCUNHO`, `PUBLICADO`, `ARQUIVADO`). Na interface, `ARQUIVADO` é rotulado **"Fora do ar"** e é **reversível** pelo Admin (US-08.09) — não é mais uma via de mão única.
- `progresso`: uma linha por (pessoa, quebra-cabeça) com `pecas_obtidas` (0 a 8), `status` (`EM_ANDAMENTO`, `CONCLUIDO`), datas. **No máximo uma linha `EM_ANDAMENTO` por pessoa** (restrição no banco) — é isso que impede sortear um quebra-cabeça novo antes de terminar o atual.
- `peca_conquistada`: (pessoa, quebra-cabeça, índice de 0 a 7, `participacao_evento_id` quando aplicável). **Único por (pessoa, quebra-cabeça, índice)** — garante que a mesma posição nunca é sorteada duas vezes para a mesma pessoa.
- `creditos`: contador por pessoa. Incrementado a cada `ParticipacaoConfirmada` (check-in Sympla, ÉP-05); decrementado quando o clique no dado troca 1 crédito por 1 peça.

#### Ação A — Sortear quebra-cabeça (gratuita)

1. Só é oferecida quando a pessoa **não** tem progresso `EM_ANDAMENTO`.
2. Ao clicar no dado, o sistema sorteia, **de forma uniforme**, um quebra-cabeça entre os `PUBLICADO` que a pessoa ainda não concluiu.
3. Cria a linha de progresso com `pecas_obtidas = 0`, status `EM_ANDAMENTO`. **Não consome crédito.**
4. Se não há nenhum quebra-cabeça disponível (a pessoa já concluiu todos os publicados) → nenhum progresso é criado; os créditos acumulados continuam guardados como **créditos pendentes** até o Admin publicar um novo.
5. A pessoa não sabe qual foi sorteado: nome, imagem e id do quebra-cabeça não são enviados ao navegador antes da conclusão — a tela recebe só "0 de 8" e os 8 espaços em cinza.

#### Ação B — Sortear peça (troca 1 crédito)

1. Só é oferecida quando a pessoa tem progresso `EM_ANDAMENTO` **e** `creditos > 0`.
2. Ao clicar no dado, em **uma transação com bloqueio da linha de progresso** (evita corrida em duplo clique ou abas simultâneas): confirma que ainda há crédito, sorteia **aleatoriamente** um índice entre os que faltam (0 a 7 menos os já conquistados), grava `peca_conquistada`, incrementa `pecas_obtidas` e decrementa 1 crédito.
3. Se `pecas_obtidas == 8` → marca `CONCLUIDO`, registra a data e emite `QuebraCabecaConcluido`.
4. **Idempotência**: um mesmo clique nunca é processado duas vezes (proteção contra duplo clique/retry de rede) — no máximo 1 peça e 1 crédito descontado por clique confirmado.

#### Créditos

- **1 crédito por presença confirmada** — mesmo fato de domínio do check-in Sympla (ÉP-05); inscrição sem comparecimento não gera crédito.
- **Idempotente**: `creditos` é incrementado a partir de um contador de participações já processadas por pessoa; reprocessar/sincronizar de novo a mesma presença não gera crédito duplicado.
- **Não expira**: fica disponível até a pessoa decidir gastá-lo no dado, inclusive atravessando a conclusão de um quebra-cabeça (o crédito sobra para o próximo).
- **Não retroativo**: só presenças confirmadas a partir da ativação da gamificação (ÉP-02, US-02.04) geram crédito.

#### Segredo da imagem — como garantir tecnicamente

- Ao publicar, a plataforma **pré-gera** os arquivos derivados: as 8 peças (recortes do grid 2×4), a silhueta/prévia borrada e a imagem completa, armazenados com nomes não adivinháveis.
- A silhueta é pública no catálogo; **cada peça só é servida por endpoint autenticado** que verifica se a pessoa a conquistou; a **imagem completa só é servida após `CONCLUIDO`**.
- O `id` do quebra-cabeça em andamento **não é enviado ao navegador**: a tela de progresso recebe apenas "peças obtidas / 8" e os recortes conquistados, sem nada que permita cruzar com o catálogo.
- As peças obtidas revelam, naturalmente, partes da imagem — isso é parte da diversão (adivinhar) e não compromete o segredo do conjunto, cujo nome e história só aparecem ao concluir.

**Escala:** custo por evento é constante (1 escrita no contador de créditos); custo por clique no dado é constante (1 leitura + 1 escrita, com bloqueio de linha); o processamento de imagem acontece uma única vez, na publicação.

### 1.3 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia bloqueada · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Aba "Gamificação" (meus quebra-cabeças e créditos) | P | L | L |
| Catálogo de quebra-cabeças (silhuetas) | P | L | L |
| Sortear quebra-cabeça e peças (dado) | — | E (próprio) | — |
| Cadastro e manutenção de quebra-cabeças | — | — | E |
| Quebra-cabeças e créditos de terceiros | — | — | L |

### 1.4 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-08.01](#us-0801-ver-a-aba-de-gamificacao-bloqueada) | Ver a aba de gamificação bloqueada | Não-Filiado | Should |
| [US-08.02](#us-0802-ganhar-credito-com-presenca-confirmada-em-evento) | Ganhar crédito com presença confirmada em evento | Filiado | Must |
| [US-08.03](#us-0803-sortear-um-novo-quebra-cabeca) | Sortear um novo quebra-cabeça | Filiado | Must |
| [US-08.04](#us-0804-usar-um-credito-para-sortear-uma-peca) | Usar um crédito para sortear uma peça | Filiado | Must |
| [US-08.05](#us-0805-consultar-meus-quebra-cabecas-e-o-catalogo) | Consultar meus quebra-cabeças e o catálogo | Filiado | Must |
| [US-08.06](#us-0806-cadastrar-um-quebra-cabeca-no-catalogo) | Cadastrar um quebra-cabeça no catálogo | Admin | Must |
| [US-08.07](#us-0807-ver-o-ranking-dos-10-filiados-mais-engajados) | Ver o ranking dos 10 filiados mais engajados | Admin | Should |
| [US-08.08](#us-0808-editar-um-quebra-cabeca-existente) | Editar um quebra-cabeça existente | Admin | Must |
| [US-08.09](#us-0809-tirar-um-quebra-cabeca-do-ar) | Tirar um quebra-cabeça do ar | Admin | Must |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-08.XX.N).

---

## 2. Histórias do Não-Filiado

### US-08.01 — Ver a aba de gamificação bloqueada

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que existe uma coleção de quebra-cabeças para os filiados
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Meu perfil > Gamificação", **então** vejo o catálogo de quebra-cabeças com as silhuetas **desfocadas**, o painel fixo "Exclusivo para filiados", o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
2. **Dado** que estou na aba, **quando** procuro o dado de sorteio, **então** ele aparece desabilitado, com a dica "Disponível para filiados".
3. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas a quantidade de quebra-cabeças no catálogo — nomes, legendas, histórias e imagens **não estão presentes**.
4. **Dado** que clico em "Quero ser filiado", **quando** o clique acontece, **então** o evento é registrado com origem `gamificacao` para medir conversão.

**Regras de negócio**

- RN-08.01.1 — Não-Filiado **não acumula** créditos nem peças, mesmo com presença confirmada em eventos. Ao se tornar Filiado e ativar a gamificação, só fatos posteriores à ativação contam (RN-08.02.4).
- RN-08.01.2 — O mesmo componente de prévia bloqueada do clube de benefícios (ÉP-04, US-04.01) é reutilizado aqui.

---

## 3. Histórias do Filiado

### US-08.02 — Ganhar crédito com presença confirmada em evento

**Atores:** Filiado

> **Como** Filiado com gamificação ativa
> **Quero** ganhar um crédito sempre que minha presença em um evento for confirmada
> **Para** poder trocá-lo por peças de quebra-cabeça

**Critérios de aceite**

1. **Dado** que minha presença em um evento é confirmada (check-in sincronizado do Sympla, ÉP-05), **quando** o fato é registrado, **então** ganho 1 crédito em até 1 minuto, visível no meu saldo na aba "Gamificação".
2. **Dado** que ganho um crédito, **quando** acesso a plataforma pela primeira vez depois disso, **então** vejo um aviso simples ("+1 crédito!") sem revelar nada sobre peças ou quebra-cabeças.
3. **Dado** que o mesmo check-in é reprocessado (ex.: nova sincronização do Sympla), **quando** o sistema avalia, **então** nenhum crédito é concedido em duplicidade.
4. **Dado** que minha filiação deixou de ser reconhecida, **quando** acesso "Gamificação", **então** vejo a tela bloqueada de US-08.01, mas meus créditos e meu progresso continuam guardados e reaparecem quando a filiação voltar.
5. **Dado** que desativei a gamificação nas preferências (ÉP-02, US-02.04), **quando** um check-in é confirmado, **então** nenhum crédito é concedido; os créditos já acumulados permanecem guardados.

**Regras de negócio**

- RN-08.02.1 — Somente Filiados com filiação ativa **e** gamificação ativada ganham créditos.
- RN-08.02.2 — **1 crédito por presença confirmada.** Inscrever-se sem comparecer não gera crédito.
- RN-08.02.3 — Crédito é permanente até ser gasto: não expira, não é removido por inativação de quebra-cabeça, desativação da gamificação ou perda da filiação.
- RN-08.02.4 — **Não retroativo**: só presenças confirmadas a partir da ativação da gamificação geram crédito.
- RN-08.02.5 — A concessão de crédito é assíncrona e idempotente (seção 1.2).

---

### US-08.03 — Sortear um novo quebra-cabeça

**Atores:** Filiado

> **Como** Filiado com gamificação ativa e sem quebra-cabeça em andamento
> **Quero** clicar em um dado para sortear qual quebra-cabeça vou montar
> **Para** começar minha próxima coleção sem saber de antemão o que é

**Critérios de aceite**

1. **Dado** que não tenho quebra-cabeça em andamento, **quando** acesso "Meu perfil > Gamificação", **então** vejo um dado grande com a chamada "Sortear quebra-cabeça".
2. **Dado** que clico no dado, **quando** o sorteio acontece, **então** a plataforma escolhe aleatoriamente um quebra-cabeça entre os publicados que eu ainda não completei, meu progresso passa a existir com 0 de 8 peças, e o tabuleiro aparece com **8 espaços cinzas** — sem nome, imagem ou história.
3. **Dado** que já tenho um quebra-cabeça em andamento, **quando** acesso a aba, **então** o dado de sorteio de novo quebra-cabeça **não aparece** — só o dado de trocar crédito por peça (US-08.04).
4. **Dado** que já completei todos os quebra-cabeças publicados, **quando** acesso a aba, **então** vejo "Coleção completa! Assim que soltarmos um quebra-cabeça novo, você já começa nele", meus créditos continuam guardados, e nenhum novo progresso é criado.
5. **Dado** que ainda não existe nenhum quebra-cabeça publicado no catálogo, **quando** acesso a aba, **então** vejo a mensagem "Em breve" no lugar do dado.

**Regras de negócio**

- RN-08.03.1 — Sortear qual quebra-cabeça montar é **gratuito**: não consome crédito.
- RN-08.03.2 — **No máximo um quebra-cabeça `EM_ANDAMENTO` por pessoa** (restrição no banco); só é possível sortear um novo depois de concluir o atual.
- RN-08.03.3 — Sorteio uniforme entre os quebra-cabeças `PUBLICADO` ainda não concluídos pela pessoa.
- RN-08.03.4 — Quebra-cabeças arquivados pelo Admin não entram no sorteio.

---

### US-08.04 — Usar um crédito para sortear uma peça

**Atores:** Filiado

> **Como** Filiado com um quebra-cabeça em andamento e pelo menos 1 crédito
> **Quero** clicar no dado para trocar um crédito por uma peça
> **Para** ir completando meu quebra-cabeça a cada evento que participo

**Critérios de aceite**

1. **Dado** que tenho um quebra-cabeça em andamento e pelo menos 1 crédito, **quando** acesso a aba, **então** vejo o dado habilitado com "Usar 1 crédito (tenho N)".
2. **Dado** que clico no dado, **quando** o sorteio acontece, **então** o sistema escolhe aleatoriamente uma das posições que ainda não tenho, revela aquela peça no tabuleiro e meu saldo de créditos diminui em 1.
3. **Dado** que não tenho créditos, **quando** acesso a aba, **então** o dado aparece desabilitado com "Vá a um evento para ganhar um crédito", com link para a agenda (ÉP-05).
4. **Dado** que clico duas vezes seguidas rapidamente (duplo clique ou conexão lenta), **quando** o sistema processa, **então** apenas 1 peça é concedida e apenas 1 crédito é descontado.
5. **Dado** que recebo uma peça e o tabuleiro ainda não está completo, **quando** o sorteio termina, **então** vejo uma celebração mostrando a peça encaixando no tabuleiro, com "peça X de 8".
6. **Dado** que recebo a peça que completa o tabuleiro (8 de 8), **quando** o sorteio termina, **então** vejo a imagem inteira revelada, o nome, a legenda e a história daquele quebra-cabeça, e ele passa para "Concluídos".

**Regras de negócio**

- RN-08.04.1 — Cada clique no dado gasta exatamente 1 crédito e concede exatamente 1 peça, em uma única transação com bloqueio da linha de progresso.
- RN-08.04.2 — **Peça sorteada aleatoriamente** entre as posições (0 a 7) ainda não obtidas naquele quebra-cabeça — sem ordem fixa.
- RN-08.04.3 — Peças e quebra-cabeças concluídos são **permanentes** e sobrevivem à perda da filiação ou desativação da gamificação (ficam ocultos até a situação se regularizar).
- RN-08.04.4 — O dado de "trocar crédito por peça" só aparece quando há progresso `EM_ANDAMENTO`; sem isso, o dado exibido é o de sortear quebra-cabeça (US-08.03).

---

### US-08.05 — Consultar meus quebra-cabeças e o catálogo

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver os quebra-cabeças que já montei, o que estou montando agora, meu saldo de créditos e os que ainda não descobri
> **Para** acompanhar minha coleção e me motivar a ir ao próximo evento

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil > Gamificação", **quando** a aba carrega, **então** vejo meu saldo de créditos e três blocos: **Concluídos**, **Em andamento** e **Ainda não descobertos**.
2. **Dado** que tenho um quebra-cabeça em andamento, **quando** o vejo, **então** vejo o tabuleiro 2×4 com as peças já conquistadas encaixadas, as posições vazias em cinza, e o contador "X de 8 peças" — **sem** nome, história ou qualquer identificação do quebra-cabeça.
3. **Dado** que abro um quebra-cabeça concluído, **quando** o detalhe carrega, **então** vejo a imagem completa, o nome, a legenda, a história, a data de conclusão e a lista dos eventos que renderam cada peça.
4. **Dado** que vejo "Ainda não descobertos", **quando** olho o bloco, **então** vejo as silhuetas de todos os quebra-cabeças publicados que ainda não montei, sem revelar qual está em andamento.
5. **Dado** que ainda não tenho nenhuma peça, **quando** abro a aba, **então** vejo o catálogo de silhuetas e o convite "Sua primeira peça vem no próximo evento", com link para a agenda.
6. **Dado** que inspeciono a resposta da API, **quando** analiso o conteúdo, **então** o identificador do quebra-cabeça em andamento e a URL da imagem completa **não estão presentes** — só os recortes das peças conquistadas e os totais.

**Regras de negócio**

- RN-08.05.1 — A aba mostra apenas a coleção, o progresso e os créditos da pessoa autenticada.
- RN-08.05.2 — Imagem completa, nome, legenda e história só são exibidos após a conclusão.
- RN-08.05.3 — As silhuetas do catálogo são iguais para todos e não permitem inferir o quebra-cabeça em andamento; o bloco "Ainda não descobertos" inclui, propositalmente, o que está em andamento.

---

## 4. Histórias do Admin

### US-08.06 — Cadastrar um quebra-cabeça no catálogo

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar quebra-cabeças com imagem, nome, legenda e história curta
> **Para** alimentar a coleção com os marcos, diretorias, conquistas e eventos do capítulo

**Critérios de aceite**

1. **Dado** que crio um quebra-cabeça, **quando** informo nome, legenda curta, história curta e a imagem (proporção **4:2**, deitada, JPG/PNG/WebP até 10 MB), **então** ele é salvo como `RASCUNHO` e a plataforma gera automaticamente as **8 peças** (grid 2×4), a silhueta borrada e a imagem completa processada.
2. **Dado** que a imagem foi processada, **quando** clico em "Pré-visualizar", **então** vejo a silhueta como aparecerá no catálogo e o tabuleiro numerado com as 8 peças na ordem em que serão concedidas.
3. **Dado** que publico o quebra-cabeça, **quando** confirmo, **então** vejo a tela "Quebra-cabeça criado com sucesso!" e, a partir daí, ele entra no sorteio de novos progressos (US-08.03) e aparece nas silhuetas do catálogo.
4. **Dado** que consulto a lista no console, **quando** ela carrega, **então** vejo cada quebra-cabeça com nome, status, nº de peças, quantos filiados o concluíram e quantos estão montando, além dos botões **Editar** (US-08.08) e **Tirar do ar** (US-08.09) por linha.

**Regras de negócio**

- RN-08.06.1 — **Sempre 8 peças, grid fixo 2×4** — não configurável pelo Admin (a proporção 4:2 da imagem garante peças quadradas).
- RN-08.06.2 — **Exclusão física é proibida** para quebra-cabeças com progresso; usar "Tirar do ar" (US-08.09).
- RN-08.06.3 — A plataforma alerta o Admin quando há filiados com "coleção completa" (créditos parados sem quebra-cabeça disponível), sinalizando que é hora de publicar um novo.
- RN-08.06.4 — Publicação é registrada em auditoria.

---

### US-08.07 — Ver o ranking dos 10 filiados mais engajados

**Atores:** Admin

> **Como** Admin
> **Quero** ver quem são os filiados que mais completam quebra-cabeças
> **Para** ter uma noção rápida do engajamento com a gamificação

**Critérios de aceite**

1. **Dado** que estou na lista de quebra-cabeças no console, **quando** clico em "📊 Ver estatísticas", **então** vejo uma tela de ranking.
2. **Dado** que a tela de ranking carrega, **quando** a lista aparece, **então** vejo os **10 filiados** com mais quebra-cabeças concluídos, em ordem decrescente, cada um com: **nome**, **quantos quebra-cabeças completou / total de quebra-cabeças publicados**, e **quantas peças já tem no quebra-cabeça atual / 8**.
3. **Dado** que clico em "‹ Voltar para quebra-cabeças", **quando** o clique acontece, **então** volto para a lista do console.

**Regras de negócio**

- RN-08.07.1 — O ranking é calculado a partir das concessões registradas (`progresso.status = CONCLUIDO`); nenhum número é digitado pelo Admin.
- RN-08.07.2 — Em caso de empate no total de concluídos, o desempate segue a ordem de conclusão mais antiga primeiro.
- RN-08.07.3 — Filiado sem nenhum quebra-cabeça em andamento aparece com "0 / 8" na coluna de peças do atual.

---

### US-08.08 — Editar um quebra-cabeça existente

**Atores:** Admin

> **Como** Admin
> **Quero** editar nome, legenda, história e — quando ainda não há progresso — a imagem de um quebra-cabeça
> **Para** corrigir ou refinar o conteúdo sem precisar recriar do zero

**Critérios de aceite**

1. **Dado** que estou na lista de quebra-cabeças, **quando** clico em "✎ Editar" numa linha, **então** vejo o mesmo formulário de cadastro, já preenchido com os dados atuais.
2. **Dado** que o quebra-cabeça ainda não tem nenhum progresso registrado, **quando** edito nome, legenda, história ou imagem, **então** tudo é editável normalmente e trocar a imagem gera novos recortes.
3. **Dado** que o quebra-cabeça já tem progresso de pelo menos um filiado, **quando** abro a edição, **então** o campo de imagem aparece bloqueado com o aviso "🔒 Imagem bloqueada — já há peças conquistadas por filiados", e só nome, legenda e história continuam editáveis.
4. **Dado** que confirmo as alterações, **quando** clico em "Salvar alterações", **então** vejo a tela "Alterações salvas com sucesso!" antes de voltar para a lista.

**Regras de negócio**

- RN-08.08.1 — Editar não afeta peças já concedidas nem o progresso de ninguém.
- RN-08.08.2 — Imagem é **imutável após o primeiro progresso** registrado, para não invalidar peças já concedidas.
- RN-08.08.3 — Toda edição é registrada em auditoria.

---

### US-08.09 — Tirar um quebra-cabeça do ar

**Atores:** Admin

> **Como** Admin
> **Quero** tirar um quebra-cabeça do ar temporariamente
> **Para** pausá-lo sem perder o cadastro, podendo trazê-lo de volta depois

**Critérios de aceite**

1. **Dado** que estou na lista de quebra-cabeças, **quando** clico em "⨯ Tirar do ar" numa linha, **então** vejo um banner de confirmação: *"Tirar '\[nome]' do ar? Ele fica marcado como Fora do ar e não entra mais no sorteio de novos quebra-cabeças. Quem já está montando ou já concluiu continua vendo normalmente."*, com as opções **Cancelar** e **Tirar do ar**.
2. **Dado** que confirmo, **quando** a ação é processada, **então** o quebra-cabeça sai do sorteio imediatamente e a linha na lista aparece esmaecida, com status **"Fora do ar"** e um único botão **"↑ Subir novamente"** no lugar de Editar/Tirar do ar.
3. **Dado** que clico em "↑ Subir novamente", **quando** confirmo, **então** o status volta para `PUBLICADO` e ele volta a entrar no sorteio (US-08.03).
4. **Dado** que um filiado já estava montando esse quebra-cabeça quando ele foi tirado do ar, **quando** essa pessoa acessa "Gamificação", **então** ela continua vendo e montando normalmente — ele só deixa de ser sorteado para quem ainda não tem nenhum em andamento.

**Regras de negócio**

- RN-08.09.1 — "Fora do ar" é o mesmo status `ARQUIVADO` do modelo (seção 1.2), agora **reversível** pelo Admin a qualquer momento — substitui o antigo arquivamento sem volta.
- RN-08.09.2 — Tirar do ar e subir novamente são registrados em auditoria.
- RN-08.09.3 — Exclusão física continua proibida (RN-08.06.2); "Tirar do ar" é a única forma de remover um quebra-cabeça do sorteio.

---

### Possíveis Atualizações

_(nenhuma pendente no momento — o ranking do Admin, cogitado aqui antes, já foi implementado como US-08.07)_

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-08 (Gamificação: badges e quebra-cabeças) para Não-Filiado, Filiado e Admin, com validação técnica dos dois motores | Vitor Leonardo | Nome do revisor |
| 2.0 | 13/09/2026 | Reformulação completa do épico: badges removidos; gamificação passa a ter uma única mecânica (quebra-cabeças de 8 peças, sorteados por crédito ganho em eventos, revelados no clique de um dado) | Isabelle Costa | Nome do revisor |
| 3.0 | 14/09/2026 | US-08.07 reescrita (era dashboard+pessoa+CSV, virou ranking dos 10 filiados mais engajados); US-08.08 (Editar quebra-cabeça) e US-08.09 (Tirar do ar, reversível) adicionadas, separadas do antigo US-08.06; `ARQUIVADO` agora é rotulado "Fora do ar" e reversível | Isabelle Costa | Nome do revisor |
