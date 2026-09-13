# ÉP-05 — Agenda do PMI-DF

## 1. Visão geral do épico

**Objetivo:** ser a fonte única da agenda de eventos do capítulo, sincronizada automaticamente com o Sympla, e transformar a presença nos eventos em histórico consultável por cada pessoa e em números confiáveis para o Admin.

**Valor:** o capítulo continua operando os eventos onde já opera (Sympla) e a plataforma vira a camada de divulgação, identidade e histórico. Classificado como **Essencial** no brainstorming (item 4 — Agenda/calendário), com o histórico por usuário (item 18) servindo de base para a gamificação.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. Dados de participantes vindos do Sympla (e-mail, nome, ingresso, check-in) são tratados como dados pessoais: **nunca** são expostos a Filiado ou Não-Filiado, exceto a própria participação de cada um.

### 1.1 O que a API do Sympla permite

A integração usa a **API Pública do Sympla v1.6.0** (`https://api.sympla.com.br/public`), autenticada pelo header `s_token` gerado em *Minha Conta > Integrações* da conta organizadora do PMI-DF. Todas as rotas são **do lado do organizador**: retornam apenas eventos e participantes do próprio produtor autenticado.

| Rota | O que retorna | Uso na plataforma |
|------|---------------|-------------------|
| `GET /v1.6.0/events` | Eventos do produtor: `id`, `name`, `detail`, `start_date`, `end_date`, `image`, `url`, `address`, `host`, `category_prim`, `category_sec`, `published`, `cancelled`, `private_event`. Filtros `published`, `from`; paginação por cursor (até 200 por página) | Sincronizar a agenda |
| `GET /v1.6.0/events/{id}` | Detalhe de um evento | Atualizar um evento específico |
| `GET /v1.6.0/events/{id}/participants` | Participantes: `first_name`, `last_name`, `email`, `ticket_name`, `ticket_status`, `order_status`, `checkin.check_in`, `checkin.check_in_date`, `custom_form`, preços. Filtros `participant_email`, `cancelled_filter`, `from`/`to` sobre `ticket_updated_at`; até 500 por página | Histórico de participação e números dos eventos |
| `POST /v1.6.0/events/{id}/participants/{participantId}/check-in` e `POST .../qrcode/check-in` | Registram check-in | **Não usado** — presença é registrada no aplicativo do Sympla |
| `GET /v1.6.0/events/{id}/orders` | Pedidos com dados do comprador e valores | **Não usado** — dados financeiros fora do escopo |
| `GET /v1.6.0/events/{id}/presentations` | Sessões/apresentações de um evento com múltiplas datas | **Não usado** no MVP |

**Consequências para o produto:**

| Limitação da API | Impacto |
|------------------|---------|
| Não existe rota para **criar inscrição**, nem SSO para participantes, nem parâmetros de pré-preenchimento na URL de checkout | A inscrição acontece sempre no site do Sympla, por link externo comum; não é possível um transbordo autenticado |
| O evento **não tem campo de formato** (online / presencial / híbrido) | O formato é inferido: com `address` → presencial; sem `address` → online |
| O evento **não traz contagem de inscritos** | Inscritos e presentes são calculados pela plataforma a partir da lista de participantes sincronizada |
| A lista de participantes traz **dados pessoais** (e-mail, nome, formulário, preço pago) | Só o Admin acessa; Filiado e Não-Filiado veem apenas a própria participação, vinculada por e-mail |
| O vínculo entre participante e conta é feito **por e-mail** | A pessoa precisa se inscrever no Sympla com o e-mail principal ou um e-mail alternativo verificado (ÉP-02, US-02.03) |

### 1.2 Matriz de permissões do épico

Legenda: **L** = Ler · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Agenda de eventos (lista e calendário) | L | L | L |
| Detalhe de evento e link de inscrição no Sympla | L | L | L |
| Próprio histórico de eventos (em "Meu perfil") | L | L | L |
| Números dos eventos (por evento e agregados) | — | — | L  |
| Lista de participantes de um evento | — | — | L  |
| Sincronização com o Sympla (automática e manual) | — | — | L |
| Conciliação de participações não vinculadas | — | — | L |

### 1.3 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-05.01](#us-0501-consultar-a-agenda-de-eventos) | Consultar a agenda de eventos | Filiado, Não-Filiado | Must |
| [US-05.02](#us-0502-consultar-meu-historico-de-eventos-em-meu-perfil) | Consultar meu histórico de eventos em "Meu perfil" | Filiado, Não-Filiado | Must |
| [US-05.03](#us-0503-sincronizar-eventos-do-sympla) | Sincronizar eventos do Sympla | Admin | Must |
| [US-05.04](#us-0504-sincronizar-participantes-e-vincular-a-pessoas) | Sincronizar participantes e vincular a pessoas | Admin | Must |
| [US-05.05](#us-0505-consultar-numeros-e-participantes-dos-eventos) | Consultar números e participantes dos eventos | Admin | Must |
| [US-05.06](#us-0506-conciliar-participacoes-nao-vinculadas) | Conciliar participações não vinculadas | Admin | Should |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-05.XX.N).

---

## 2. Histórias do Filiado e do Não-Filiado

### US-05.01 — Consultar a agenda de eventos

**Atores:** Filiado, Não-Filiado

> **Como** usuário da plataforma
> **Quero** ver os próximos eventos do PMI-DF em lista e em calendário e ir para a inscrição
> **Para** planejar e garantir minha participação

**Critérios de aceite**

1. **Dado** que acesso "Agenda", **quando** a página carrega, **então** vejo os próximos eventos ordenados por data, cada um com imagem, título, data e hora de início e fim, formato (online ou presencial) e local (quando presencial).
2. **Dado** que alterno para a visão de calendário mensal, **quando** navego entre os meses, **então** os eventos aparecem nos respectivos dias e clicar em um deles abre o detalhe.
3. **Dado** que filtro por período, formato ou categoria, **quando** aplico, **então** a listagem e o calendário respondem ao filtro.
4. **Dado** que abro um evento, **quando** a página carrega, **então** vejo a descrição completa, endereço com mapa (quando presencial), categoria e o botão "Inscrever-se no Sympla".
5. **Dado** que clico em "Inscrever-se no Sympla", **quando** o clique acontece, **então** a página do evento no Sympla abre em nova aba; se estou autenticado, antes de sair vejo o aviso "Inscreva-se com um destes e-mails para que sua participação seja reconhecida", listando meu e-mail principal e os alternativos verificados.
6. **Dado** que quero guardar o evento, **quando** clico em "Adicionar ao meu calendário", **então** recebo um arquivo `.ics` com título, datas, local e link do evento.
7. **Dado** que um evento foi cancelado no Sympla, **quando** acesso a agenda, **então** ele aparece com a marca "Cancelado" pelos 7 dias seguintes à data original e depois sai da agenda.
8. **Dado** que o evento já ocorreu ou foi cancelado, **quando** abro o detalhe, **então** o botão "Inscrever-se no Sympla" não é exibido.
9. **Dado** que acesso a agenda sem estar autenticado, **quando** a página carrega, **então** vejo os eventos normalmente — a agenda é vitrine pública do capítulo.
10. **Dado** que quero ver o que já aconteceu, **quando** seleciono "Eventos passados", **então** vejo os eventos encerrados em ordem decrescente de data.

**Regras de negócio**

- RN-05.01.1 — A agenda exibe **exclusivamente eventos publicados no Sympla** do PMI-DF, trazidos pela sincronização (US-05.03). Não há cadastro de eventos na plataforma.
- RN-05.01.2 — A inscrição acontece **somente no Sympla**; a plataforma não coleta dados de inscrição nem cria pedidos. O reconhecimento da participação depende de o e-mail usado no Sympla coincidir com o e-mail principal ou um alternativo **verificado** da pessoa.
- RN-05.01.3 — A agenda **não exibe** quantidade de inscritos nem quem vai ao evento.
- RN-05.01.4 — Datas são exibidas no fuso America/Sao_Paulo. (ver questão em aberto sobre confirmação do fuso retornado pela API)
- RN-05.01.5 — Filiado e Não-Filiado veem exatamente a mesma agenda.
  

---

### US-05.02 — Consultar meu histórico de eventos em "Meu perfil"

**Atores:** Filiado, Não-Filiado

> **Como** usuário da plataforma
> **Quero** ver em "Meu perfil" todos os eventos do PMI-DF dos quais participei
> **Para** acompanhar minha jornada no capítulo

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil", **quando** a página carrega, **então** vejo a aba "Meus eventos" ao lado das demais abas do perfil.
2. **Dado** que abro "Meus eventos", **quando** ela carrega, **então** vejo os eventos em ordem decrescente de data, com título, data, formato e meu status: **Inscrito** (evento futuro), **Presente** (check-in registrado) ou **Ausente** (inscrito sem check-in em evento encerrado).
3. **Dado** que nunca participei de nenhum evento, **quando** abro a aba, **então** vejo uma mensagem com link para a agenda e a orientação de cadastrar os e-mails que uso no Sympla.
4. **Dado** que verifico um e-mail alternativo (ÉP-02, US-02.03), **quando** volto à aba, **então** as participações antigas feitas com aquele e-mail já aparecem.
5. **Dado** que participei de um evento e ele ainda não aparece, **quando** leio a aba, **então** vejo a data e hora da última sincronização com o Sympla e a informação de que a presença pode levar até 24 horas para ser refletida.
6. **Dado** que sou Filiado com gamificação ativa, **quando** abro a aba, **então** vejo também a contagem de presenças que conta para meu engajamento.

**Regras de negócio**

- RN-05.02.1 — A aba mostra **apenas** as participações da pessoa autenticada; não existe consulta de histórico de terceiros para Filiado ou Não-Filiado.
- RN-05.02.2 — Só participação com check-in registrado no Sympla vale como **Presente**. Inscrever-se e não comparecer é **Ausente** e não pontua.
- RN-05.02.3 — Participação vinculada por e-mail não verificado é proibida (ÉP-02, RN-02.03.1).
- RN-05.02.4 — O histórico é preservado se a pessoa perder a filiação; ele pertence à conta, não ao estado de filiado.

---

## 3. Histórias do Admin

### US-05.03 — Sincronizar eventos do Sympla

**Atores:** Admin

> **Como** Admin
> **Quero** que os eventos publicados no Sympla do PMI-DF apareçam automaticamente na agenda
> **Para** que a agenda seja sempre um espelho fiel do Sympla, sem cadastro manual

**Critérios de aceite**

1. **Dado** que a sincronização automática roda a cada 6 horas, **quando** encontra um evento publicado novo no Sympla, **então** ele é criado na plataforma e exibido na agenda com título, descrição, datas, imagem, endereço, categoria e link de inscrição.
2. **Dado** que um evento já sincronizado teve título, datas, descrição, imagem ou endereço alterados no Sympla, **quando** a sincronização roda, **então** os dados são atualizados na plataforma.
3. **Dado** que um evento foi cancelado ou despublicado no Sympla, **quando** a sincronização detecta, **então** ele é marcado como cancelado na plataforma, preservando o histórico de participação.
4. **Dado** que o evento do Sympla não tem endereço, **quando** é criado ou atualizado, **então** o formato é definido como "online"; com endereço, como "presencial".
5. **Dado** que preciso atualizar agora, **quando** clico em "Sincronizar agora" no console, **então** a sincronização é executada imediatamente e vejo o resultado (eventos lidos, criados, atualizados, cancelados).
6. **Dado** que a API do Sympla falha ou o token é inválido, **quando** a sincronização roda, **então** o erro é registrado, o Admin é notificado e nenhum evento existente é apagado ou alterado.
7. **Dado** que acesso "Integrações > Sympla", **quando** a página carrega, **então** vejo o log das últimas execuções com data, duração, status e contagens.

**Regras de negócio**


- RN-05.03.1 — **Todos os eventos vêm do Sympla.** A plataforma não permite criar nem editar eventos; qualquer correção é feita no Sympla e refletida na próxima sincronização.
- RN-05.03.2 — A sincronização lê apenas eventos com published = true (padrão da API) e usa o filtro from, presumindo que ele filtra por data do evento, para buscar eventos a partir de 12 meses atrás, garantindo o histórico. (ver questão em aberto nº 5 — a semântica exata de from neste endpoint precisa ser confirmada)
- RN-05.03.3 — O token `s_token` fica em cofre de segredos; nunca no código, no repositório ou no frontend.
- RN-05.03.4 — Eventos nunca são excluídos pela sincronização — no máximo, marcados como cancelados.
- RN-05.03.5 — A paginação por cursor é percorrida até o fim em cada execução; respostas `429` são respeitadas com espera antes de nova tentativa.

---

### US-05.04 — Sincronizar participantes e vincular a pessoas

**Atores:** Admin

> **Como** Admin
> **Quero** que a lista de inscritos e o check-in de cada evento sejam importados do Sympla e vinculados às contas da plataforma
> **Para** construir o histórico de participação e os números do capítulo sem trabalho manual

**Critérios de aceite**

1. **Dado** que um evento do Sympla existe na plataforma, **quando** a sincronização de participantes roda, **então** cada inscrito é gravado com nome, e-mail, tipo de ingresso, status do ingresso e status de check-in (com data e hora, quando houver).
2. **Dado** que o e-mail do participante corresponde ao e-mail principal ou a um e-mail alternativo **verificado** de uma pessoa, **quando** a sincronização processa, **então** a participação é vinculada àquela pessoa e aparece em "Meus eventos".
3. **Dado** que o e-mail não corresponde a nenhuma pessoa, **quando** a sincronização processa, **então** a participação é gravada como **não vinculada** e entra na fila de conciliação (US-05.06).
4. **Dado** que uma pessoa se cadastra ou verifica um e-mail que já consta em participações não vinculadas, **quando** a verificação conclui, **então** essas participações são vinculadas retroativamente.
5. **Dado** que a sincronização é reexecutada sobre o mesmo evento, **quando** processa, **então** nenhum participante é duplicado e apenas status alterados (ex.: check-in feito, ingresso cancelado) são atualizados.
6. **Dado** que a sincronização usa o filtro `from` sobre `ticket_updated_at`, **quando** roda, **então** lê apenas participantes alterados desde a última execução bem-sucedida.
7. **Dado** que um ingresso foi cancelado no Sympla, **quando** a sincronização detecta, **então** a participação é marcada como cancelada e sai do histórico da pessoa.

**Regras de negócio**
- RN-05.04.1 — Frequência: 1 vez por dia para eventos futuros fora da janela abaixo; a cada hora nas 48 horas em torno da data do evento; depois 1 vez por dia durante 7 dias após o evento; depois encerra. Pode ser disparada manualmente pelo Admin a qualquer momento.
- RN-05.04.2 — Vínculo por e-mail **não verificado** é proibido.
- RN-05.04.3 — Participações não vinculadas são preservadas indefinidamente: são o gancho para reconhecer o histórico de quem se cadastrar depois.
- RN-05.04.4 — Da lista de participantes, a plataforma armazena apenas nome, e-mail, tipo de ingresso, status do ingresso e check-in. Valores pagos e respostas de formulário customizado **não** são importados.
- RN-05.04.5 — Só participação com `checkin.check_in = true` gera pontuação de engajamento (ÉP-08).

---

### US-05.05 — Consultar números e participantes dos eventos

**Atores:** Admin

> **Como** Admin
> **Quero** uma visão analítica de todos os eventos do capítulo, com filtros e totais por evento ou agregados, e a possibilidade de descer até os participantes
> **Para** acompanhar o desempenho dos eventos, apurar presença e prestar contas à diretoria

**Critérios de aceite**

1. **Dado** que acesso "Eventos" no console, **quando** a página carrega, **então** vejo o painel com **todos os eventos** sincronizados, um por linha, com título, data, formato, situação (ativo ou inativo) e os números: inscritos, presentes, ausentes, cancelados e taxa de presença.
2. **Dado** que o painel está carregado, **quando** olho o topo, **então** vejo os **totais agregados** do conjunto exibido: quantidade de eventos, inscritos, presentes, ausentes, cancelados e taxa média de presença.
3. **Dado** que filtro por situação (ativos, inativos ou todos), período, formato, categoria ou busco por título, **quando** aplico, **então** a lista e os totais agregados refletem os critérios.
4. **Dado** que marco eventos específicos na lista, **quando** a seleção muda, **então** os totais agregados passam a considerar **apenas os eventos selecionados**, com indicação "N eventos selecionados".
5. **Dado** que quero comparar, **quando** ordeno por qualquer coluna numérica (inscritos, presentes, taxa de presença), **então** a lista é reordenada.
6. **Dado** que exporto os números, **quando** confirmo, **então** recebo um CSV com uma linha por evento (respeitando filtros e seleção) contendo título, data, formato, situação, inscritos, presentes, ausentes, cancelados e taxa de presença, mais uma linha final com os totais agregados; a ação fica registrada em auditoria.
7. **Dado** que os números são exibidos, **quando** leio a tela, **então** vejo a data e hora da última sincronização de participantes e o botão "Sincronizar agora".
8. **Dado** que abro um evento a partir do painel, **quando** a página carrega, **então** vejo os números daquele evento por tipo de ingresso e a lista de participantes com nome, e-mail, tipo de ingresso, status de check-in com data e hora, e o vínculo com conta da plataforma (nome da pessoa vinculada, "não vinculado" ou "não é membro").
9. **Dado** que estou na lista de participantes, **quando** busco por nome ou e-mail, ou filtro por status de check-in, tipo de ingresso ou vínculo, **então** a lista reflete os critérios.
10. **Dado** que exporto a lista de participantes, **quando** confirmo, **então** recebo um CSV com os mesmos campos da lista, respeitando os filtros, e a ação fica registrada em auditoria.
11. **Dado** que abro o detalhe de uma pessoa (ÉP-02, US-02.10), **quando** a página carrega, **então** vejo os eventos em que ela esteve inscrita e presente.

**Regras de negócio**

- RN-05.05.1 — Todos os números são **calculados** a partir das participações sincronizadas; nenhum é digitado.
- RN-05.05.2 — "Inscrito" = ingresso não cancelado; "Presente" = check-in registrado; "Ausente" = inscrito sem check-in em evento encerrado; "Cancelado" = ingresso cancelado no Sympla.
- RN-05.05.3 — Situação do evento: **ativo** = evento futuro ou em andamento; **inativo** = evento encerrado ou cancelado.
- RN-05.05.4 — Taxa de presença = presentes ÷ inscritos; para o agregado, calculada sobre as somas, não pela média das taxas.
- RN-05.05.5 — Exportações de números e de participantes são ações sensíveis: sempre registradas em auditoria.
- RN-05.05.6 — Os totais agregados alimentam os números públicos do capítulo (portal da transparência), sem identificar participantes.

---

### US-05.06 — Conciliar participações não vinculadas

**Atores:** Admin

> **Como** Admin
> **Quero** vincular manualmente participações cujo e-mail não corresponde a nenhuma conta
> **Para** corrigir casos de e-mail divergente e não deixar membros ativos sem histórico

**Critérios de aceite**

1. **Dado** que acesso "Participações não vinculadas", **quando** a lista carrega, **então** vejo evento, nome e e-mail informados no Sympla, status de check-in e sugestões de pessoas com nome semelhante.
2. **Dado** que seleciono uma pessoa e confirmo o vínculo, **quando** salvo, **então** a participação passa a constar em "Meus eventos" daquela pessoa, a pontuação é recalculada e a ação fica em auditoria.
3. **Dado** que marco uma participação como "não é membro", **quando** confirmo, **então** ela sai da fila mas continua contando nos números do evento (US-05.05).
4. **Dado** que vinculei por engano, **quando** desfaço o vínculo, **então** a participação volta à fila e a pontuação da pessoa é recalculada.

**Regras de negócio**

- RN-05.06.1 — O vínculo manual **não** cadastra o e-mail do Sympla como e-mail alternativo da pessoa; se ela quiser reconhecimento automático nos próximos eventos, precisa cadastrá-lo e verificá-lo (ÉP-02, US-02.03).
- RN-05.06.2 — Uma participação só pode estar vinculada a uma pessoa.

---

## 4. Decisões e questões em aberto

### Decisões

- **Todos os eventos vêm do Sympla.** Não existe cadastro nem edição de eventos na plataforma; a agenda é um espelho do Sympla e qualquer correção é feita lá.
- **Não há eventos exclusivos para filiados.** Filiado e Não-Filiado veem a mesma agenda.
- **Inscrição por link externo comum.** A API do Sympla não permite criar inscrições, não oferece SSO para participantes nem pré-preenchimento na URL de checkout — um transbordo autenticado é inviável. A plataforma apenas abre o Sympla e orienta sobre qual e-mail usar.
- **Formato do evento é inferido pelo endereço** (com endereço → presencial; sem → online) e não é editável.
- **Check-in é feito exclusivamente no aplicativo do Sympla.** As rotas de check-in da API não são usadas.
- **Números e participantes são exclusivos do Admin**, em visão analítica com filtros, seleção, agregação e exportação. A agenda não mostra quantidade de inscritos nem "quem vai" (item 33 do brainstorming, não essencial). Totais agregados vão para o portal da transparência, sem identificação.
- **Histórico de eventos fica em "Meu perfil"**, na aba "Meus eventos", seguindo o padrão do histórico de resgates (ÉP-04).
- **Dados financeiros e formulários customizados não são importados** — só nome, e-mail, tipo de ingresso, status e check-in.
- **Fora do escopo:** emissão ou download de certificados, pesquisa de NPS pós-evento (item 28), sessões múltiplas de um mesmo evento (rota `presentations`), eventos híbridos como formato distinto, eventos fora da conta Sympla do PMI-DF.

### Questões em aberto

1. **Qual conta do Sympla gera o `s_token`?** A API só retorna eventos do produtor autenticado. Se o capítulo usa mais de uma conta organizadora, será preciso um token por conta e a sincronização precisa consolidá-las.
2. **Eventos privados no Sympla (`private_event`)** devem aparecer na agenda? Hoje a sincronização traz todos os publicados, privados ou não.
3. **Valores de `ticket_status`** não estão enumerados na especificação da API. Precisamos confirmar com o Sympla quais valores existem para mapear "cancelado" com segurança.
4. **Limites de requisição** (rate limit) da API não estão documentados. Confirmar antes de definir a frequência final de sincronização.
5. **Eventos passados antes da plataforma existir:** até quando voltar na sincronização inicial? Hoje a RN-05.03.2 assume 12 meses.
6. **Correções de check-in feitas no Sympla:** após o encerramento da janela de sync (7 dias pós-evento) não são mais capturadas automaticamente — só via "Sincronizar agora" manual. Isso é aceitável ou precisamos de uma janela residual (ex.: sync mensal por mais X meses)?
7. Confirmar o fuso horário retornado pela API do Sympla para start_date/end_date — a RN-05.01.4 assume America/Sao_Paulo, mas isso não está documentado na especificação consultada.

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|

| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-05 (Agenda do PMI-DF) para Não-Filiado, Filiado e Admin, com análise da API Pública do Sympla v1.6.0 | Vitor Leonardo | Nome do revisor |
| 1.1 | 11/09/2026 | Todos os eventos passam a vir do Sympla (sem cadastro manual, sem eventos exclusivos, sem check-in pelo portal); remoção de US-05.02, US-05.04, US-05.06 e US-05.10 e renumeração; US de números reescrita com enfoque analítico (filtros, seleção, agregados, CSV) | Vitor Leonardo | Nome do revisor |
| 1.2 | 13/09/2026 | remoção de histórias antigas (US-02, US-04, US-06 e US-10 da versão anterior, não as US-05.02/04/06 atuais); US de números reescrita  | João Filipe | Revisão das US, regras de negócio e matriz de permissões |

