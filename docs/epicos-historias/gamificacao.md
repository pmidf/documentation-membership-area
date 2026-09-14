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

- `quebra_cabeca`: imagem (proporção **4:2**, deitada), nome, legenda curta, história curta, grid fixo **2×4** (8 peças), silhueta pública (prévia borrada), status (`RASCUNHO`, `PUBLICADO`, `ARQUIVADO`).
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
| [US-08.06](#us-0806-cadastrar-e-manter-quebra-cabecas-no-catalogo) | Cadastrar e manter quebra-cabeças no catálogo | Admin | Must |
| [US-08.07](#us-0807-acompanhar-o-engajamento-com-a-gamificacao) | Acompanhar o engajamento com a gamificação | Admin | Should |

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

### US-08.06 — Cadastrar e manter quebra-cabeças no catálogo

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar quebra-cabeças com imagem, nome, legenda e história curta
> **Para** alimentar a coleção com os marcos, diretorias, conquistas e eventos do capítulo

**Critérios de aceite**

1. **Dado** que crio um quebra-cabeça, **quando** informo nome, legenda curta, história curta e a imagem (proporção **4:2**, deitada, JPG/PNG/WebP até 10 MB), **então** ele é salvo como `RASCUNHO` e a plataforma gera automaticamente as **8 peças** (grid 2×4), a silhueta borrada e a imagem completa processada.
2. **Dado** que a imagem foi processada, **quando** clico em "Pré-visualizar", **então** vejo a silhueta como aparecerá no catálogo e o tabuleiro com as 8 peças.
3. **Dado** que quero ajustar um rascunho, **quando** substituo a imagem, nome, legenda ou história, **então** os recortes são gerados de novo a partir da nova imagem.
4. **Dado** que publico o quebra-cabeça, **quando** confirmo, **então** ele entra no sorteio de novos progressos (US-08.03) e aparece nas silhuetas do catálogo.
5. **Dado** que um quebra-cabeça publicado já tem progresso de alguém, **quando** tento trocar a imagem, **então** a alteração é bloqueada — só nome, legenda e história continuam editáveis.
6. **Dado** que arquivo um quebra-cabeça, **quando** confirmo, **então** ele sai do sorteio e do catálogo de silhuetas para quem não o tem; quem já o montou continua vendo, e quem está montando **termina** normalmente.
7. **Dado** que consulto a lista no console, **quando** ela carrega, **então** vejo cada quebra-cabeça com status, quantos filiados o concluíram e quantos estão montando.

**Regras de negócio**

- RN-08.06.1 — **Sempre 8 peças, grid fixo 2×4** — não configurável pelo Admin (a proporção 4:2 da imagem garante peças quadradas).
- RN-08.06.2 — **Exclusão física é proibida** para quebra-cabeças com progresso; usar arquivamento.
- RN-08.06.3 — Imagem é **imutável após o primeiro progresso** registrado, para não invalidar peças já concedidas.
- RN-08.06.4 — A plataforma alerta o Admin quando há filiados com "coleção completa" (créditos parados sem quebra-cabeça disponível), sinalizando que é hora de publicar um novo.
- RN-08.06.5 — Publicação e arquivamento são registrados em auditoria.

---

### US-08.07 — Acompanhar o engajamento com a gamificação

**Atores:** Admin

> **Como** Admin
> **Quero** ver quantos créditos e peças cada filiado tem, e os totais por quebra-cabeça
> **Para** avaliar o engajamento e planejar novas publicações

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa no console (ÉP-02, US-02.10), **quando** a página carrega, **então** vejo seu saldo de créditos, os quebra-cabeças concluídos e o em andamento (X de 8).
2. **Dado** que acesso "Gamificação" no console, **quando** a página carrega, **então** vejo os totais: filiados com gamificação ativa, créditos emitidos e gastos no período, e quebra-cabeças concluídos no período.
3. **Dado** que filtro por período, **quando** aplico, **então** os totais refletem o filtro.
4. **Dado** que exporto os totais, **quando** confirmo, **então** recebo um CSV com uma linha por quebra-cabeça (nome, status, total de conclusões no período); a ação fica registrada em auditoria.

**Regras de negócio**

- RN-08.07.1 — Todos os números são calculados a partir das concessões registradas; nenhum é digitado.
- RN-08.07.2 — O Admin **não** concede nem remove créditos ou peças manualmente; toda concessão nasce de um fato verificado (presença confirmada) ou de uma ação do próprio Filiado no dado.

---

### Possíveis Atualizações
1. Implementação de Ranking

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-08 (Gamificação: badges e quebra-cabeças) para Não-Filiado, Filiado e Admin, com validação técnica dos dois motores | Vitor Leonardo | Nome do revisor |
| 2.0 | 13/09/2026 | Reformulação completa do épico: badges removidos; gamificação passa a ter uma única mecânica (quebra-cabeças de 8 peças, sorteados por crédito ganho em eventos, revelados no clique de um dado) | Isabelle Costa | Nome do revisor |
