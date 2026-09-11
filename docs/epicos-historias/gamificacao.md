# ÉP-08 — Gamificação

## 1. Visão geral do épico

**Objetivo:** reconhecer e recompensar a participação dos filiados no capítulo por meio de duas mecânicas — **badges** (conquistas por consumo de conteúdo, presença em eventos e tempo de vínculo) e **quebra-cabeças colecionáveis** (uma peça a cada presença confirmada em evento, revelando aos poucos imagens que contam a história do PMI-DF).

**Valor:** responde aos itens 10, 11 e 12 do brainstorming (*gamificação para engajamento*, *pertencimento e exclusividade*, *badges de crescimento na carreira*). Ambas as mecânicas são ancoradas em **fatos verificáveis já registrados pela plataforma** — check-in sincronizado do Sympla (ÉP-05), visualização de vídeo (ÉP-06), datas de conta e filiação (ÉP-02) — o que as torna sustentáveis e resistentes a fraude sem nenhuma verificação adicional.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda concessão é calculada no **backend** a partir de eventos de domínio já auditados; nenhuma conquista é solicitada, submetida ou digitada pelo usuário. A imagem final de um quebra-cabeça **nunca** é entregue ao navegador antes da conclusão.

### 1.1 As duas mecânicas

| | Badges | Quebra-cabeças |
|---|---|---|
| **O que é** | Conquista permanente por atingir um critério | Coleção de imagens montadas peça a peça |
| **Gatilho** | Vídeo assistido, playlist concluída, check-in em evento, NPS respondido, tempo de conta, tempo de filiação | Exclusivamente check-in em evento |
| **Quem define** | Admin cadastra badges com regra parametrizada | Admin cadastra quebra-cabeças (imagem, nº de peças, história) |
| **Surpresa** | Não — o catálogo mostra o critério de cada badge | Sim — a pessoa não sabe qual quebra-cabeça está montando |
| **Onde aparece** | Aba "Conquistas" em "Meu perfil" | Aba "Conquistas" em "Meu perfil" |

### 1.2 Validação técnica

#### Motor de badges — regras declarativas, avaliação por evento

Um badge **não é código**: é um registro com `tipo_regra` + `parametros` (JSON). O motor tem um avaliador por tipo de regra e é acionado por **eventos de domínio** que os outros épicos já emitem. Cadastrar um badge novo nunca exige alteração de código, desde que o tipo de regra exista.

| Tipo de regra | Parâmetros | Gatilho (evento de domínio) | Como avalia |
|---------------|------------|-----------------------------|-------------|
| `CONTAGEM` | `fato` (VIDEO, EVENTO, PLAYLIST, NPS), `minimo` | O fato correspondente | `count(fatos da pessoa) >= minimo` |
| `TOTALIDADE` | `fato` (VIDEO, EVENTO), `escopo` (TODOS, ANO=AAAA) | O fato correspondente | `count(fatos da pessoa no escopo) == count(itens publicados no escopo)` |
| `FREQUENCIA` | `fato`, `janela` (SEMANA), `repeticoes` (4) | O fato correspondente | Cada uma das últimas N janelas consecutivas tem ≥ 1 fato |
| `ITEM_ESPECIFICO` | `fato` (EVENTO, VIDEO), `item_id` | O fato correspondente | Existe fato da pessoa para aquele item |
| `TEMPO_CONTA` | `anos` | Rotina diária | `hoje − criado_em >= anos` |
| `TEMPO_FILIACAO` | `anos` | Rotina diária | `hoje − filiacao_reconhecida_desde >= anos` |

**Propriedades que garantem escala e correção:**

- **Avaliação incremental**: cada evento de domínio dispara a avaliação **somente** dos badges cujo `tipo_regra` escuta aquele fato, **somente** para a pessoa do evento. Custo por evento: O(badges ativos daquele fato), tipicamente < 10 consultas simples.
- **Idempotência**: `badge_conquistado` é único por (pessoa, badge). Reprocessar o mesmo evento (ex.: nova sincronização do Sympla) não duplica nem falha.
- **Assíncrono**: a avaliação roda em fila, fora da requisição que gerou o fato; um atraso de segundos é aceitável e nunca bloqueia a sincronização ou o player.
- **Retroatividade sob controle**: ao ativar um badge, o Admin escolhe se ele deve ser **avaliado retroativamente**; nesse caso um job em lote percorre as pessoas em páginas, sem travar o sistema. Sem isso, só fatos futuros contam.
- **Permanência**: badge conquistado nunca é removido — nem por inativação do badge, nem por mudança de regra, nem por perda da filiação.
- **Contadores materializados**: para `CONTAGEM` e `TOTALIDADE`, a plataforma mantém contadores por pessoa (vídeos assistidos, eventos com presença) atualizados a cada fato, evitando `COUNT(*)` sobre tabelas grandes a cada avaliação.

#### Motor de quebra-cabeças — uma peça por check-in, sem repetição, com segredo

**Modelo:**

- `quebra_cabeca`: imagem final, nome, história, nº de peças (5 a 10), *grid* (ex.: 2×3, 2×5), silhueta pública, status.
- `progresso`: uma linha por (pessoa, quebra-cabeça) com `pecas_obtidas`, `status` (EM_ANDAMENTO, CONCLUIDO), datas. **No máximo uma linha EM_ANDAMENTO por pessoa** (restrição no banco).
- `peca_conquistada`: (pessoa, quebra-cabeça, índice, `participacao_evento_id`). **Único por `participacao_evento_id`** — é isso que garante "uma peça por check-in", mesmo que o Sympla seja sincronizado dez vezes.
- `creditos_pendentes` por pessoa: contador usado quando não há quebra-cabeça disponível (ver abaixo).

**Fluxo a cada `ParticipacaoConfirmada` (check-in vinculado a uma pessoa):**

1. Se já existe `peca_conquistada` para aquela participação → encerra (idempotente).
2. Se a pessoa não tem quebra-cabeça EM_ANDAMENTO → **sorteia** um entre os ativos que ela ainda não concluiu e cria o progresso com 0 peças. Se não há nenhum disponível → incrementa `creditos_pendentes` e encerra.
3. Concede a peça de índice `pecas_obtidas + 1`, grava `peca_conquistada` e incrementa o contador — tudo em **uma transação com bloqueio da linha de progresso**, para que dois check-ins processados em paralelo não gerem a mesma peça.
4. Se `pecas_obtidas == total` → marca CONCLUIDO, registra a data e emite `QuebraCabecaConcluido` (usado pela celebração e por badges, se houver).
5. Quando o Admin publica um quebra-cabeça novo, uma rotina consome os `creditos_pendentes` de cada pessoa, aplicando os passos 2–4 para cada crédito.

**Segredo da imagem — como garantir tecnicamente:**

- Ao publicar, a plataforma **pré-gera** os arquivos derivados: as N peças (recortes conforme o *grid*), a silhueta/prévia borrada e a imagem completa, armazenados com nomes não adivinháveis.
- A silhueta é pública no catálogo; **cada peça só é servida por endpoint autenticado** que verifica se a pessoa a conquistou; a **imagem completa só é servida após CONCLUIDO**.
- O `id` do quebra-cabeça em andamento **não é enviado ao navegador**: a tela de progresso recebe apenas "peças obtidas / total" e os recortes conquistados, sem nada que permita cruzar com o catálogo.
- As peças obtidas revelam, naturalmente, partes da imagem — isso é parte da diversão (adivinhar) e não compromete o segredo do conjunto, cujo nome e história só aparecem ao concluir.

**Escala:** o custo por check-in é constante (uma leitura e uma escrita); o catálogo e as silhuetas são cacheáveis; o processamento de imagem acontece uma única vez, na publicação. Com 10 peças por quebra-cabeça e ~20 eventos por ano, um filiado assíduo conclui cerca de 2 quebra-cabeças por ano — o Admin precisa publicar poucos por ano para a coleção nunca "acabar".

### 1.3 Catálogo inicial de badges

Os badges abaixo são o ponto de partida; todos são cadastrados pelo Admin (US-08.06) e a coluna "Situação" indica o que já é possível com os épicos atuais.

| Categoria | Badge | Critério | Tipo de regra | Situação |
|-----------|-------|----------|---------------|----------|
| Conteúdo | **Primeiro passo** | Assistiu ao primeiro vídeo | `CONTAGEM` VIDEO ≥ 1 | Pronto (ÉP-06) |
| Conteúdo | **Primeira trilha** | Concluiu a primeira playlist | `CONTAGEM` PLAYLIST ≥ 1 | **Depende de playlists no ÉP-06** (hoje fora do escopo) |
| Conteúdo | **Maratonista** | Assistiu a todos os vídeos publicados | `TOTALIDADE` VIDEO, TODOS | Pronto (ÉP-06) |
| Conteúdo | **Sempre atualizado** | 1 vídeo por semana durante 4 semanas seguidas | `FREQUENCIA` VIDEO, SEMANA, 4 | Pronto (ÉP-06) |
| Eventos | **Estreante** | Presença no primeiro evento | `CONTAGEM` EVENTO ≥ 1 | Pronto (ÉP-05) |
| Eventos | **Presença de ferro** | Presença em N eventos (N definido pelo Admin) | `CONTAGEM` EVENTO ≥ N | Pronto (ÉP-05) |
| Eventos | **Voz ativa** | Respondeu à pesquisa de NPS pós-evento | `CONTAGEM` NPS ≥ 1 | **Depende de NPS pós-evento** (hoje fora do escopo do ÉP-05) |
| Eventos | **Ano completo** | Presença em todos os eventos de um ano | `TOTALIDADE` EVENTO, ANO=AAAA | Pronto (ÉP-05) |
| Eventos | **Participação em evento X** | Presença em um evento específico (comemorativo) | `ITEM_ESPECIFICO` EVENTO | Pronto (ÉP-05) |
| Tempo | **Membro há N anos** | N anos desde a criação da conta | `TEMPO_CONTA` N | Pronto (ÉP-02) |
| Tempo | **Filiado há N anos** | N anos como filiado | `TEMPO_FILIACAO` N | **Parcial**: só é possível contar desde que a filiação foi **reconhecida na plataforma**, pois a base do PMI não traz a data de início (ÉP-02, RN-02.07.2) |

### 1.4 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia desfocada · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Aba "Conquistas" (próprios badges e quebra-cabeças) | P | L | L |
| Catálogo de badges (critérios) e de quebra-cabeças (silhuetas) | P | L | L |
| Conquistar badges e peças | — | E (automático) | — |
| Cadastro e manutenção de badges | — | — | E |
| Cadastro e manutenção de quebra-cabeças | — | — | E |
| Conquistas de terceiros e totais | — | — | L |

### 1.5 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-08.01](#us-0801-ver-a-previa-desfocada-das-conquistas) | Ver a prévia desfocada das conquistas | Não-Filiado | Should |
| [US-08.02](#us-0802-conquistar-badges-automaticamente) | Conquistar badges automaticamente | Filiado | Must |
| [US-08.03](#us-0803-consultar-meus-badges-e-o-catalogo) | Consultar meus badges e o catálogo | Filiado | Must |
| [US-08.04](#us-0804-receber-uma-peca-de-quebra-cabeca-a-cada-presenca-confirmada) | Receber uma peça de quebra-cabeça a cada presença confirmada | Filiado | Must |
| [US-08.05](#us-0805-consultar-meus-quebra-cabecas-e-o-catalogo) | Consultar meus quebra-cabeças e o catálogo | Filiado | Must |
| [US-08.06](#us-0806-cadastrar-e-manter-badges) | Cadastrar e manter badges | Admin | Must |
| [US-08.07](#us-0807-cadastrar-e-manter-quebra-cabecas) | Cadastrar e manter quebra-cabeças | Admin | Must |
| [US-08.08](#us-0808-acompanhar-as-conquistas-dos-filiados) | Acompanhar as conquistas dos filiados | Admin | Should |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-08.XX.N).

---

## 2. Histórias do Não-Filiado

### US-08.01 — Ver a prévia desfocada das conquistas

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que existem badges e quebra-cabeças para os filiados
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Meu perfil > Conquistas", **então** vejo o catálogo de badges e as silhuetas dos quebra-cabeças **desfocados**, com o painel fixo "Exclusivo para filiados", o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
2. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas as quantidades (badges existentes, quebra-cabeças existentes) — nomes, critérios e imagens **não estão presentes**.
3. **Dado** que clico em "Quero ser filiado", **quando** o clique acontece, **então** o evento é registrado com origem `gamificacao` para medir conversão.

**Regras de negócio**

- RN-08.01.1 — Não-Filiado **não acumula** badges nem peças, mesmo com presença confirmada em eventos. Ao se tornar Filiado e ativar a gamificação, fatos anteriores contam conforme a retroatividade de cada mecânica (RN-08.02.3 e RN-08.04.4).
- RN-08.01.2 — O mesmo componente de prévia desfocada do clube de benefícios (ÉP-04, US-04.01) é reutilizado aqui.

---

## 3. Histórias do Filiado

### US-08.02 — Conquistar badges automaticamente

**Atores:** Filiado

> **Como** Filiado com gamificação ativa
> **Quero** receber badges automaticamente quando atinjo um critério
> **Para** ser reconhecido pela minha participação sem precisar pedir nada

**Critérios de aceite**

1. **Dado** que assisti a um vídeo até o fim (ÉP-06), tive presença confirmada em um evento (ÉP-05) ou completei um marco de tempo, **quando** o fato é registrado, **então** os badges cujo critério passei a atender são concedidos em até 1 minuto, sem nenhuma ação minha.
2. **Dado** que conquistei um badge, **quando** acesso a plataforma pela primeira vez depois disso, **então** vejo uma celebração com nome, arte e critério do badge, exibida uma única vez.
3. **Dado** que conquistei um badge, **quando** olho minha caixa de e-mail, **então** recebi um e-mail transacional com a conquista (respeitando a preferência de e-mail marketing: badge é comunicação transacional).
4. **Dado** que o mesmo fato é reprocessado (ex.: nova sincronização do Sympla), **quando** o motor avalia, **então** nenhum badge é concedido em duplicidade.
5. **Dado** que minha filiação deixou de ser reconhecida, **quando** acesso "Conquistas", **então** vejo a prévia desfocada de US-08.01, mas meus badges continuam guardados e reaparecem quando a filiação voltar.
6. **Dado** que desativei a gamificação nas preferências (ÉP-02, US-02.04), **quando** um fato é registrado, **então** nenhum badge é concedido; os já conquistados permanecem.

**Regras de negócio**

- RN-08.02.1 — Somente Filiados com filiação ativa **e** gamificação ativada conquistam badges.
- RN-08.02.2 — **Badge conquistado é permanente**: não é removido por inativação do badge, mudança de regra, desativação da gamificação ou perda da filiação.
- RN-08.02.3 — Badges são **retroativos** quando o Admin assim define ao ativá-los (US-08.06); do contrário, contam apenas fatos posteriores à ativação.
- RN-08.02.4 — Só contam fatos verificados: presença = check-in sincronizado do Sympla; vídeo = visualização ≥ 90% registrada pelo player.
- RN-08.02.5 — A avaliação é assíncrona e idempotente (seção 1.2).

---

### US-08.03 — Consultar meus badges e o catálogo

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver os badges que conquistei e os que ainda posso conquistar, com meu progresso em cada um
> **Para** me motivar a participar mais da comunidade

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil > Conquistas", **quando** a aba carrega, **então** vejo a seção "Badges" com os conquistados (arte colorida, nome e data) e os disponíveis (arte em escala de cinza, nome e critério), agrupados por categoria: Conteúdo, Eventos e Tempo.
2. **Dado** que um badge disponível é de contagem ou totalidade, **quando** o vejo, **então** vejo meu progresso ("7 de 10 eventos", "12 de 15 vídeos").
3. **Dado** que um badge é comemorativo de um evento específico já ocorrido e eu não participei, **quando** o vejo, **então** ele aparece como "Encerrado" — não é mais possível conquistá-lo.
4. **Dado** que abro um badge conquistado, **quando** o detalhe carrega, **então** vejo o critério e a data exata da conquista.
5. **Dado** que ainda não conquistei nenhum badge, **quando** abro a aba, **então** vejo uma mensagem com os caminhos mais próximos (o próximo vídeo, o próximo evento).

**Regras de negócio**

- RN-08.03.1 — A aba mostra apenas as conquistas da pessoa autenticada; não há visualização de badges de terceiros.
- RN-08.03.2 — O catálogo exibe todos os badges ativos, inclusive os que a pessoa não pode mais conquistar (comemorativos encerrados), para preservar o sentido de coleção.
- RN-08.03.3 — Badges inativados pelo Admin somem do catálogo, mas continuam visíveis para quem os conquistou.

---

### US-08.04 — Receber uma peça de quebra-cabeça a cada presença confirmada

**Atores:** Filiado

> **Como** Filiado com gamificação ativa
> **Quero** ganhar uma peça de quebra-cabeça a cada evento em que minha presença for confirmada
> **Para** montar, aos poucos e com surpresa, as imagens da história do capítulo

**Critérios de aceite**

1. **Dado** que minha presença em um evento é confirmada (check-in sincronizado do Sympla, ÉP-05), **quando** o fato é registrado, **então** recebo a **próxima peça** do quebra-cabeça em andamento — sempre a de menor índice que ainda me falta.
2. **Dado** que não tenho quebra-cabeça em andamento, **quando** recebo minha primeira peça (ou a primeira após concluir um), **então** a plataforma sorteia um quebra-cabeça entre os que ainda não montei, sem me informar qual é.
3. **Dado** que recebo uma peça, **quando** acesso a plataforma, **então** vejo uma celebração mostrando a peça encaixada no tabuleiro, com "peça X de N".
4. **Dado** que recebo a última peça, **quando** o quebra-cabeça se completa, **então** vejo a imagem inteira revelada, o nome e a história daquele quebra-cabeça, e ele passa para a minha coleção.
5. **Dado** que já montei todos os quebra-cabeças publicados, **quando** tenho presença confirmada, **então** vejo a mensagem "Coleção completa — sua peça ficará guardada para o próximo quebra-cabeça" e a peça é creditada assim que um novo for publicado.
6. **Dado** que a mesma presença é reprocessada, **quando** o motor avalia, **então** nenhuma peça é concedida em duplicidade.
7. **Dado** que uma participação minha foi vinculada depois pela conciliação do Admin (ÉP-05, US-05.06), **quando** o vínculo é criado, **então** a peça correspondente é concedida da mesma forma.

**Regras de negócio**

- RN-08.04.1 — **Uma peça por presença confirmada**, garantida pela unicidade (participação, peça) no banco. Inscrever-se sem comparecer não gera peça.
- RN-08.04.2 — **Um único quebra-cabeça em andamento por pessoa**; o próximo só começa quando o atual é concluído.
- RN-08.04.3 — **Peças em ordem, nunca repetidas**: a peça concedida é sempre a de índice `obtidas + 1`.
- RN-08.04.4 — **Não retroativo**: só presenças confirmadas a partir da data de lançamento da mecânica geram peças. Isso evita que um filiado antigo complete vários quebra-cabeças de uma vez sem viver a surpresa.
- RN-08.04.5 — O sorteio é uniforme entre os quebra-cabeças ativos ainda não concluídos pela pessoa.
- RN-08.04.6 — Peças e quebra-cabeças concluídos são permanentes e sobrevivem à perda da filiação (ficam ocultos até ela voltar).
- RN-08.04.7 — Somente Filiados com filiação ativa e gamificação ativada recebem peças (mesma regra dos badges).

---

### US-08.05 — Consultar meus quebra-cabeças e o catálogo

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver os quebra-cabeças que já montei, o que estou montando agora e os que ainda não descobri
> **Para** acompanhar minha coleção e me motivar a ir ao próximo evento

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil > Conquistas", **quando** a aba carrega, **então** vejo a seção "Quebra-cabeças" com três blocos: **Montados**, **Em andamento** e **Ainda não descobertos**.
2. **Dado** que tenho um quebra-cabeça em andamento, **quando** o vejo, **então** vejo o tabuleiro com as peças que já conquistei encaixadas, as posições vazias em branco, o contador "X de N peças" e a frase "Falta(m) N−X presença(s) para completar" — **sem** nome, história ou qualquer identificação do quebra-cabeça.
3. **Dado** que abro um quebra-cabeça montado, **quando** o detalhe carrega, **então** vejo a imagem completa, o nome, a história que ela conta, a data de conclusão e a lista dos eventos que renderam cada peça.
4. **Dado** que vejo "Ainda não descobertos", **quando** olho o bloco, **então** vejo as silhuetas ou prévias borradas de todos os quebra-cabeças ativos que ainda não montei, com a quantidade de peças de cada um, sem revelar qual está em andamento.
5. **Dado** que ainda não tenho nenhuma peça, **quando** abro a seção, **então** vejo o catálogo de silhuetas e o convite "Sua primeira peça vem no próximo evento", com link para a agenda.
6. **Dado** que inspeciono a resposta da API, **quando** analiso o conteúdo, **então** o identificador do quebra-cabeça em andamento e a URL da imagem completa **não estão presentes** — só os recortes das peças conquistadas e os totais.

**Regras de negócio**

- RN-08.05.1 — A imagem completa, o nome e a história só são exibidos após a conclusão.
- RN-08.05.2 — As silhuetas do catálogo são iguais para todos e não permitem inferir o quebra-cabeça em andamento; o bloco "Ainda não descobertos" inclui, propositalmente, o que está em andamento.
- RN-08.05.3 — A aba mostra apenas a coleção da pessoa autenticada.

---

## 4. Histórias do Admin

### US-08.06 — Cadastrar e manter badges

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar badges com arte, critério e regra parametrizada, sem depender de desenvolvimento
> **Para** criar reconhecimentos conforme as campanhas e os marcos do capítulo

**Critérios de aceite**

1. **Dado** que crio um badge, **quando** informo nome, descrição, arte (1:1, fundo transparente), categoria (Conteúdo, Eventos ou Tempo) e tipo de regra, **então** o formulário exibe apenas os parâmetros daquele tipo (ex.: `CONTAGEM` pede o fato e o mínimo; `ITEM_ESPECIFICO` pede o evento ou vídeo; `TEMPO_*` pede os anos).
2. **Dado** que preencho a regra, **quando** salvo, **então** o badge fica como `RASCUNHO` e vejo uma **simulação**: quantos filiados já atenderiam ao critério hoje.
3. **Dado** que ativo o badge, **quando** confirmo, **então** escolho entre "avaliar retroativamente" (concede a quem já atende, em lote) ou "somente daqui em diante", e o badge passa a constar no catálogo.
4. **Dado** que edito a regra de um badge ativo, **quando** salvo, **então** a nova regra vale para concessões futuras e ninguém perde a conquista já feita.
5. **Dado** que inativo um badge, **quando** confirmo, **então** ele some do catálogo, não é mais concedido e quem já o tem continua vendo.
6. **Dado** que o tipo de regra depende de um fato ainda não disponível na plataforma (PLAYLIST, NPS), **quando** o seleciono, **então** vejo o aviso "Este critério ainda não gera conquistas" e o badge não pode ser ativado.
7. **Dado** que consulto a lista de badges no console, **quando** filtro por categoria ou status, **então** vejo cada badge com o total de filiados que o conquistaram.

**Regras de negócio**

- RN-08.06.1 — Badge é dado, não código: os tipos de regra da seção 1.2 são os únicos disponíveis; criar um tipo novo é desenvolvimento.
- RN-08.06.2 — **Exclusão física é proibida** para badges já concedidos a alguém; usar inativação.
- RN-08.06.3 — Badges do tipo `ITEM_ESPECIFICO` vinculados a um evento só podem ser ativados antes ou até 30 dias após o evento, para funcionarem como comemorativos.
- RN-08.06.4 — Ativação, edição e inativação são registradas em auditoria.

---

### US-08.07 — Cadastrar e manter quebra-cabeças

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar quebra-cabeças com imagem, número de peças e a história que a imagem conta
> **Para** alimentar a coleção com os marcos, diretorias, conquistas e eventos do capítulo

**Critérios de aceite**

1. **Dado** que crio um quebra-cabeça, **quando** informo nome, história (texto curto), imagem final (JPG/PNG/WebP até 10 MB, proporção livre) e número de peças (de 5 a 10), **então** ele é salvo como `RASCUNHO` e a plataforma gera automaticamente a silhueta, a prévia borrada e os recortes das peças.
2. **Dado** que a imagem foi processada, **quando** clico em "Pré-visualizar", **então** vejo a silhueta como aparecerá no catálogo e o tabuleiro com as peças numeradas na ordem em que serão concedidas.
3. **Dado** que quero ajustar, **quando** altero o número de peças ou a imagem de um rascunho, **então** os recortes são gerados de novo.
4. **Dado** que publico o quebra-cabeça, **quando** confirmo, **então** ele entra no sorteio para novos progressos, aparece nas silhuetas do catálogo e os créditos pendentes dos filiados com coleção completa são consumidos.
5. **Dado** que um quebra-cabeça publicado já tem progresso de alguém, **quando** tento alterar imagem ou número de peças, **então** a alteração é bloqueada — só nome e história continuam editáveis.
6. **Dado** que arquivo um quebra-cabeça, **quando** confirmo, **então** ele sai do sorteio e do catálogo de silhuetas para quem não o tem; quem já o montou continua vendo, e quem está montando **termina** normalmente.
7. **Dado** que consulto a lista no console, **quando** ela carrega, **então** vejo cada quebra-cabeça com status, nº de peças, quantos filiados o concluíram e quantos estão montando.

**Regras de negócio**

- RN-08.07.1 — Número de peças entre 5 e 10; o *grid* é escolhido automaticamente pela proporção da imagem (ex.: 5 → 1×5, 6 → 2×3, 8 → 2×4, 9 → 3×3, 10 → 2×5).
- RN-08.07.2 — Imagem e número de peças são **imutáveis após o primeiro progresso**, para não invalidar peças já concedidas.
- RN-08.07.3 — **Exclusão física é proibida** para quebra-cabeças com progresso; usar arquivamento.
- RN-08.07.4 — A plataforma alerta o Admin quando há filiados com coleção completa (créditos pendentes), sinalizando que é hora de publicar um novo quebra-cabeça.
- RN-08.07.5 — Publicação e arquivamento são registrados em auditoria.

---

### US-08.08 — Acompanhar as conquistas dos filiados

**Atores:** Admin

> **Como** Admin
> **Quero** ver quais conquistas cada filiado tem e os totais por badge e quebra-cabeça
> **Para** avaliar o engajamento e planejar novos reconhecimentos

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa no console (ÉP-02, US-02.10), **quando** a página carrega, **então** vejo os badges conquistados com data, os quebra-cabeças montados, o em andamento (X de N) e os créditos pendentes.
2. **Dado** que acesso "Gamificação" no console, **quando** a página carrega, **então** vejo os totais: filiados com gamificação ativa, badges concedidos no período, quebra-cabeças concluídos no período e os badges mais e menos conquistados.
3. **Dado** que filtro por período, **quando** aplico, **então** os totais refletem o filtro.
4. **Dado** que exporto os totais, **quando** confirmo, **então** recebo um CSV com uma linha por badge e uma por quebra-cabeça (nome, status, total de conquistas no período); a ação fica registrada em auditoria.

**Regras de negócio**

- RN-08.08.1 — Todos os números são calculados a partir das concessões registradas; nenhum é digitado.
- RN-08.08.2 — O Admin **não** concede nem remove badges ou peças manualmente; toda conquista nasce de um fato verificado.

---

## 5. Decisões e questões em aberto

### Decisões

- **Duas mecânicas, ambas automáticas.** Não há submissão de evidência, aprovação manual nem concessão pelo Admin (diferente do refinamento, que previa "o filiado submete e o admin aprova"). O Admin cadastra as regras; o sistema concede.
- **Só Filiados com gamificação ativada participam**, nas duas mecânicas. Não-Filiado vê a prévia desfocada.
- **Badges são retroativos sob decisão do Admin; quebra-cabeças não são retroativos.** Preserva a surpresa e evita que um filiado antigo complete vários de uma vez.
- **Um quebra-cabeça em andamento por vez, peças em ordem, sorteio uniforme, imagem em segredo até a conclusão** — conforme especificado. Créditos pendentes garantem que nenhuma presença é "perdida" quando a coleção está completa.
- **Regras de badge são dados, não código**: seis tipos de regra parametrizáveis (seção 1.2) cobrem todo o catálogo inicial e permitem badges novos sem desenvolvimento.
- **Sem ranking.** As duas gamificações definidas são badges e quebra-cabeças; o ranking de engajamento (v1 US-08.06) fica fora. A menção a ranking na US-02.04 do ÉP-02 foi ajustada.
- **Sem loja de resgate de prêmios, sem personalização de personagem, sem badges para voluntários** (itens 27, 30 e 32 do brainstorming, não essenciais).

### Questões em aberto

1. **Primeira trilha** exige playlists de vídeos, hoje fora do escopo do ÉP-06. Reabrir o escopo do conteúdo exclusivo ou retirar o badge?
2. **Voz ativa** exige pesquisa de NPS pós-evento, hoje fora do escopo do ÉP-05. Reabrir ou retirar o badge?
3. **Filiado há N anos** só pode contar a partir do reconhecimento da filiação na plataforma, não desde a filiação real ao PMI. Aceitar essa semântica ("filiado reconhecido há N anos") ou aguardar que a base do PMI traga a data de início (questão já aberta no ÉP-02)?
4. **Valores iniciais**: N de "Presença de ferro" (sugestão: 10) e anos de "Membro há N anos" e "Filiado há N anos" (sugestão: 1, 3, 5).
5. **Filiado que desativou a gamificação** e a reativa depois: presenças confirmadas durante o período desativado geram peças? Hoje não (RN-08.04.7); alternativa é acumulá-las como créditos pendentes.
6. **Data de lançamento** da mecânica de quebra-cabeças (RN-08.04.4): a partir do lançamento da plataforma ou de uma campanha específica?
7. **Quantos quebra-cabeças no lançamento?** Sugestão: 4 a 6, para que o sorteio faça sentido e a coleção dure mais de um ano.

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-08 (Gamificação: badges e quebra-cabeças) para Não-Filiado, Filiado e Admin, com validação técnica dos dois motores | Vitor Leonardo | Nome do revisor |
