# ÉP-06 — Conteúdo Exclusivo

## 1. Visão geral do épico

**Objetivo:** centralizar em um único lugar, dentro da plataforma, o conteúdo produzido pelo capítulo (vídeos de treinamentos, webinars gravados, materiais) e os links para o conhecimento do PMI Global (PMBOK, projectmanagement.com, cursos), disponível apenas para Filiados.

**Valor:** junto com o clube de benefícios, é o que torna a filiação **tangível no dia a dia**. Origem no brainstorming: item 7 (*acesso a conteúdos exclusivos — vídeos de treinamento, YouTube não listado*) e item 6 (*redirect para o site oficial do PMI*), ambos priorizados; bloco *Área de conteúdo exclusivo* do refinamento pós-reunião.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. A URL ou o identificador de um vídeo exclusivo **nunca** é entregue a quem não é Filiado — a prévia desfocada é consequência da omissão de campos na API, não de um efeito visual.

### 1.1 Como o vídeo é entregue e o que é possível proteger

A plataforma **não hospeda nem transmite vídeo**. Os vídeos ficam no canal do PMI-DF no YouTube, configurados como **não listados**, e são exibidos **dentro da plataforma** por meio do player incorporado do YouTube. Isso define o que é e o que não é possível em termos de proteção:

| Medida | Efeito | Limite |
|--------|--------|--------|
| Vídeo **não listado** no YouTube | Não aparece em buscas, no canal nem em recomendações | Qualquer pessoa **com o link** assiste |
| URL do vídeo entregue **só por endpoint autenticado** a Filiado ativo | Não-Filiado, visitante e robôs nunca recebem o identificador do vídeo; nada aparece no HTML público nem na listagem | O Filiado, ao assistir, recebe o identificador no navegador — é assim que o player funciona |
| Player **embutido** na plataforma, sem botões de compartilhar/copiar link nossos | A experiência acontece dentro do portal; não há incentivo para sair | O player do YouTube mantém o botão "Assistir no YouTube" — os Termos da API do YouTube **proíbem** sobrepor ou ocultar elementos do player |
| Parâmetros `modestbranding`, `rel=0` e domínio `youtube-nocookie.com` | Reduz logotipo, vídeos relacionados e rastreamento | Não remove o link para o YouTube |
| **Registro de visualização por pessoa** (quem, o quê, quando) | Rastreabilidade: se um vídeo vazar, o capítulo sabe quem teve acesso | Não impede o vazamento |
| **Aviso de uso pessoal** exibido junto ao player | Dissuasão e base para eventual sanção pela diretoria | Depende de conduta, não de tecnologia |

**Conclusão:** **não é possível impedir tecnicamente que um Filiado copie o link** de um vídeo do YouTube. O YouTube não oferece restrição de domínio para incorporação (esse recurso existe em plataformas pagas como Vimeo, Bunny Stream ou Mux). O que a plataforma garante é que **quem não é Filiado nunca recebe o link** e que **todo acesso é rastreável**. A proteção contra redistribuição por um Filiado é dissuasiva.

### 1.2 Tipos de conteúdo

| Tipo | O que é | Como o Filiado consome |
|------|---------|------------------------|
| `VIDEO_YOUTUBE` | Vídeo não listado no canal do PMI-DF | Player embutido dentro da plataforma |
| `LINK_EXTERNO` | Material hospedado fora (artigo, PDF em drive, gravação em outra plataforma) | Abre em nova aba |
| `LINK_PMI_GLOBAL` | Recurso oficial do PMI (PMBOK, projectmanagement.com, cursos, certificações) | Abre em nova aba, com indicação de que é site do PMI |

### 1.3 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia desfocada · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Catálogo de conteúdos (títulos, capas, categorias) | P | L | E |
| Player de vídeo / identificador do vídeo | — | L | E |
| Links externos e links do PMI Global | — | L | E |
| Próprio histórico de visualização | — | L | — |
| Gestão de conteúdos e categorias | — | — | E |
| Números de visualização e exportação | — | — | L / E |

### 1.4 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-06.01](#us-0601-ver-a-previa-desfocada-da-area-de-conteudo-exclusivo) | Ver a prévia desfocada da área de conteúdo exclusivo | Não-Filiado | Must |
| [US-06.02](#us-0602-navegar-pelo-catalogo-de-conteudo) | Navegar pelo catálogo de conteúdo | Filiado | Must |
| [US-06.03](#us-0603-assistir-a-um-video-dentro-da-plataforma) | Assistir a um vídeo dentro da plataforma | Filiado | Must |
| [US-06.04](#us-0604-acessar-os-recursos-do-pmi-global-em-um-so-lugar) | Acessar os recursos do PMI Global em um só lugar | Filiado | Should |
| [US-06.05](#us-0605-cadastrar-e-publicar-um-conteudo) | Cadastrar e publicar um conteúdo | Admin | Must |
| [US-06.06](#us-0606-editar-despublicar-e-arquivar-um-conteudo) | Editar, despublicar e arquivar um conteúdo | Admin | Must |
| [US-06.07](#us-0607-gerenciar-e-exportar-todos-os-conteudos) | Gerenciar e exportar todos os conteúdos | Admin | Should |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-06.XX.N).

---

## 2. Histórias do Não-Filiado

### US-06.01 — Ver a prévia desfocada da área de conteúdo exclusivo

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que existe uma área de conteúdo exclusivo e ter uma noção do que os filiados têm à disposição
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Conteúdo exclusivo", **então** vejo a página com os *cards* dos conteúdos publicados **desfocados** (efeito *blur*), de modo que dá para perceber capas e a quantidade de conteúdos, mas não ler títulos ou descrições.
2. **Dado** que a página está desfocada, **quando** olho a tela, **então** vejo sobre ela um painel fixo com a mensagem "Exclusivo para filiados", o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
3. **Dado** que tento clicar em um *card* desfocado, **quando** o clique acontece, **então** nada abre — o único caminho é pelo painel de filiação.
4. **Dado** que clico em "Já sou filiado", **quando** sou levado ao fluxo de validação (ÉP-01), **então** ao retornar com filiação ativa o desfoque desaparece e a página passa a se comportar como em US-06.02.
5. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas a quantidade de conteúdos e as imagens de capa — título, descrição, URL, identificador de vídeo e links **não estão presentes**.
6. **Dado** que clico em "Quero ser filiado", **quando** o clique acontece, **então** o evento é registrado com origem `conteudo` para medir conversão.

**Regras de negócio**

- RN-06.01.1 — O desfoque é aplicado no frontend, mas a **proteção é a omissão de campos no backend**: o Não-Filiado nunca recebe os dados que o *blur* esconde.
- RN-06.01.2 — Visitantes não autenticados veem a mesma prévia; a diferença é o CTA, que primeiro pede cadastro.
- RN-06.01.3 — O mesmo componente de prévia desfocada do clube de benefícios (ÉP-04, US-04.01) é reutilizado aqui.

---

## 3. Histórias do Filiado

### US-06.02 — Navegar pelo catálogo de conteúdo

**Atores:** Filiado

> **Como** Filiado
> **Quero** encontrar rapidamente os vídeos e materiais do capítulo
> **Para** aproveitar o conteúdo que só a filiação oferece

**Critérios de aceite**

1. **Dado** que sou Filiado ativo, **quando** acesso "Conteúdo exclusivo", **então** vejo os destaques no topo e, abaixo, os conteúdos publicados agrupados por categoria, em *cards* com capa, título, tipo (vídeo, material ou PMI), duração (para vídeos) e data de publicação.
2. **Dado** que busco por texto, **quando** digito, **então** título e descrição são pesquisados.
3. **Dado** que filtro por categoria ou tipo, **quando** aplico, **então** a listagem responde ao filtro.
4. **Dado** que já assisti a um vídeo, **quando** o vejo no catálogo, **então** o *card* exibe a marca "Assistido" ou a barra de progresso, se parei no meio.
5. **Dado** que quero retomar, **quando** acesso a seção "Continuar assistindo", **então** vejo os vídeos iniciados e não concluídos, do mais recente ao mais antigo.
6. **Dado** que abro um conteúdo do tipo material externo, **quando** clico, **então** ele abre em nova aba e o acesso é registrado.

**Regras de negócio**

- RN-06.02.1 — A listagem retorna apenas metadados; o identificador do vídeo e a URL de materiais externos **só** são entregues ao abrir o conteúdo (US-06.03), nunca na listagem.
- RN-06.02.2 — Só conteúdos com status `PUBLICADO` aparecem no catálogo.
- RN-06.02.3 — Ordenação padrão dentro de cada categoria: mais recentes primeiro; destaques respeitam a ordem definida pelo Admin.

---

### US-06.03 — Assistir a um vídeo dentro da plataforma

**Atores:** Filiado

> **Como** Filiado
> **Quero** assistir aos vídeos exclusivos sem sair da plataforma
> **Para** consumir o conteúdo do capítulo em um ambiente reservado aos filiados

**Critérios de aceite**

1. **Dado** que sou Filiado ativo, **quando** abro um vídeo, **então** o player do YouTube carrega **embutido** na página da plataforma, com título, descrição, categoria e duração ao lado, e a visualização é registrada com meu usuário, data e hora.
2. **Dado** que o player está carregado, **quando** olho a página, **então** não existe nenhum botão da plataforma para copiar link, compartilhar ou abrir o vídeo em outra página; o único elemento que leva ao YouTube é o do próprio player, que não pode ser removido.
3. **Dado** que assisti a parte do vídeo, **quando** volto a ele depois, **então** o player retoma de onde parei.
4. **Dado** que assisti a mais de 90% do vídeo, **quando** encerro, **então** ele é marcado como "Assistido" no meu catálogo.
5. **Dado** que estou na página do vídeo, **quando** leio abaixo do player, **então** vejo o aviso "Conteúdo exclusivo para filiados do PMI-DF. Uso pessoal; a redistribuição do link é vedada."
6. **Dado** que minha filiação deixou de ser reconhecida enquanto navego, **quando** abro o próximo vídeo, **então** o player não carrega e vejo a prévia desfocada de US-06.01.
7. **Dado** que o vídeo foi removido ou tornado privado no YouTube, **quando** abro a página, **então** vejo a mensagem "Conteúdo temporariamente indisponível" em vez de um player quebrado, e o Admin é notificado.
8. **Dado** que inspeciono a resposta da API sem filiação ativa, **quando** analiso o conteúdo, **então** o identificador do vídeo **não está presente**; a resposta é uma recusa de acesso.

**Regras de negócio**

- RN-06.03.1 — O identificador do vídeo é servido **exclusivamente** por endpoint autenticado que verifica, no momento da requisição, que a filiação está ativa. Nunca aparece no HTML da página, na listagem ou em qualquer resposta a não filiados.
- RN-06.03.2 — Todo vídeo cadastrado deve estar como **não listado** no YouTube, com incorporação permitida (responsabilidade do Admin, US-06.05).
- RN-06.03.3 — O player usa o domínio `youtube-nocookie.com` com parâmetros que reduzem a marca do YouTube e desativam vídeos relacionados de outros canais.
- RN-06.03.4 — **Nenhum elemento da plataforma é sobreposto ao player**, em respeito aos Termos da API do YouTube. O botão "Assistir no YouTube" do player permanece visível — limitação conhecida e aceita.
- RN-06.03.5 — Cada concessão de acesso a um vídeo gera um registro de visualização (pessoa, conteúdo, data, hora e progresso), consultável pelo Admin (US-06.07). É a base de rastreabilidade caso um link seja redistribuído.
- RN-06.03.6 — A plataforma não faz download, cópia ou retransmissão do vídeo — apenas incorpora o player do YouTube.

---

### US-06.04 — Acessar os recursos do PMI Global em um só lugar

**Atores:** Filiado

> **Como** Filiado
> **Quero** encontrar, dentro da área de conteúdo, os links oficiais do PMI Global (PMBOK, projectmanagement.com, cursos e certificações)
> **Para** não me perder navegando entre vários sites do PMI

**Critérios de aceite**

1. **Dado** que acesso a seção "Conhecimento PMI" dentro de "Conteúdo exclusivo", **quando** a página carrega, **então** vejo os recursos curados agrupados por tema (PMBOK e padrões, projectmanagement.com, cursos, certificações), com título, descrição e a indicação "Abre no site do PMI".
2. **Dado** que clico em um recurso, **quando** o clique acontece, **então** a URL oficial abre em nova aba e o clique é registrado.
3. **Dado** que o recurso exige login na conta do PMI Global, **quando** o visualizo, **então** essa informação aparece de forma explícita antes de clicar ("Use seu login do PMI Global").

**Regras de negócio**

- RN-06.04.1 — A plataforma apenas **redireciona**; nunca copia, espelha ou faz *scraping* de conteúdo do PMI Global, respeitando a propriedade intelectual do PMI.
- RN-06.04.2 — Só URLs de domínios oficiais do PMI (`pmi.org`, `projectmanagement.com` e subdomínios) são aceitas para este tipo de conteúdo.
- RN-06.04.3 — Os links do PMI ficam dentro da área exclusiva e seguem a mesma regra de acesso: Não-Filiado vê a prévia desfocada (US-06.01).

---

## 4. Histórias do Admin

### US-06.05 — Cadastrar e publicar um conteúdo

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar vídeos do YouTube, materiais externos e links do PMI, com título, descrição e categoria
> **Para** disponibilizá-los na área de conteúdo exclusivo

**Critérios de aceite**

1. **Dado** que crio um conteúdo, **quando** informo título, descrição, tipo, URL, categoria e, opcionalmente, capa, **então** ele é salvo como `RASCUNHO` e ainda não aparece para ninguém.
2. **Dado** que o tipo é `VIDEO_YOUTUBE`, **quando** colo a URL (`youtube.com/watch?v=`, `youtu.be/` ou `youtube.com/embed/`), **então** a plataforma extrai o identificador do vídeo, busca automaticamente capa, título sugerido e duração, e exibe o player de pré-visualização para confirmar que a incorporação funciona.
3. **Dado** que o tipo é `VIDEO_YOUTUBE`, **quando** salvo, **então** devo confirmar a declaração "Este vídeo está configurado como *não listado* e com incorporação permitida no YouTube".
4. **Dado** que o tipo é `LINK_PMI_GLOBAL`, **quando** informo uma URL fora dos domínios oficiais do PMI, **então** o cadastro é recusado com a orientação de usar o tipo `LINK_EXTERNO`.
5. **Dado** que informo uma URL inválida ou inacessível, **quando** salvo, **então** recebo erro de validação.
6. **Dado** que marco o conteúdo como destaque, **quando** salvo, **então** ele aparece no topo do catálogo, respeitando o máximo de 3 destaques simultâneos e a ordem que eu definir.
7. **Dado** que publico o conteúdo, **quando** confirmo, **então** ele passa a aparecer imediatamente no catálogo dos filiados (e desfocado para não-filiados).
8. **Dado** que preciso de uma categoria nova, **quando** a crio informando nome e ordem de exibição, **então** ela fica disponível para vinculação.

**Regras de negócio**

- RN-06.05.1 — Campos obrigatórios variam conforme o tipo; a validação é feita no backend.
- RN-06.05.2 — URLs de qualquer tipo são validadas contra lista de domínios permitidos, para que a área de conteúdo não funcione como redirecionador aberto.
- RN-06.05.3 — A publicação é registrada em auditoria com usuário, data e hora.
- RN-06.05.4 — Uma categoria só pode ser removida se não tiver conteúdos vinculados.

---

### US-06.06 — Editar, despublicar e arquivar um conteúdo

**Atores:** Admin

> **Como** Admin
> **Quero** manter o ciclo de vida de um conteúdo após a publicação
> **Para** corrigir informações, retirar materiais desatualizados e manter a área sempre atual

**Critérios de aceite**

1. **Dado** que edito um conteúdo publicado, **quando** salvo, **então** a alteração é imediata para os filiados e a versão anterior fica registrada na auditoria.
2. **Dado** que troco a URL de um vídeo, **quando** salvo, **então** a capa, a duração e o player de pré-visualização são atualizados, e o progresso de quem já assistia é reiniciado.
3. **Dado** que despublico um conteúdo (volta a `RASCUNHO`), **quando** confirmo, **então** ele some do catálogo, mas o histórico de visualizações permanece.
4. **Dado** que arquivo um conteúdo, **quando** confirmo, **então** ele some do catálogo, não pode mais ser publicado e o histórico de visualizações permanece íntegro.
5. **Dado** que a plataforma detecta que um vídeo ficou indisponível no YouTube (US-06.03, critério 7), **quando** acesso o console, **então** vejo o alerta com o conteúdo afetado e a opção de trocar a URL ou despublicar.

**Regras de negócio**

- RN-06.06.1 — **Exclusão física é proibida**; o encerramento é sempre por arquivamento, para preservar o histórico de visualizações.
- RN-06.06.2 — Ao trocar a URL de um vídeo, o identificador antigo deixa de ser servido imediatamente.

---

### US-06.07 — Gerenciar e exportar todos os conteúdos

**Atores:** Admin

> **Como** Admin
> **Quero** ver todos os conteúdos cadastrados em um só lugar, com as visualizações de cada um, e exportá-los
> **Para** saber o que os filiados consomem e prestar contas à diretoria

**Critérios de aceite**

1. **Dado** que acesso "Conteúdos" no console, **quando** a lista carrega, **então** vejo todos os conteúdos — de qualquer tipo e status — com título, tipo, categoria, status, data de publicação, **visualizações** e **filiados distintos** que acessaram, com paginação.
2. **Dado** que filtro por status, tipo, categoria ou período de publicação, ou busco por texto, **quando** aplico, **então** a lista reflete todos os critérios.
3. **Dado** que ordeno por visualizações, **quando** aplico, **então** vejo os conteúdos mais e menos acessados no topo, conforme a direção escolhida.
4. **Dado** que abro um conteúdo na lista, **quando** a página carrega, **então** vejo a evolução de visualizações ao longo do tempo, a taxa de conclusão (para vídeos) e a lista de quem acessou, com nome, e-mail, data e progresso.
5. **Dado** que exporto a lista de conteúdos, **quando** confirmo, **então** recebo um CSV com uma linha por conteúdo contendo título, tipo, categoria, URL, status, data de publicação, visualizações e filiados distintos; a ação fica registrada em auditoria.
6. **Dado** que exporto as visualizações de um conteúdo, **quando** confirmo, **então** recebo um CSV com uma linha por acesso (nome, e-mail, data, hora, progresso); a ação fica registrada em auditoria.

**Regras de negócio**

- RN-06.07.1 — Visualizações são contadas a partir dos registros de acesso; nunca são digitadas.
- RN-06.07.2 — A lista de quem acessou identifica o filiado; é ação sensível, sempre registrada em auditoria. É também o instrumento de rastreabilidade previsto na RN-06.03.5.
- RN-06.07.3 — Conteúdos arquivados constam na exportação, para que a prestação de contas cubra todo o histórico.

---

## 5. Decisões e questões em aberto

### Decisões

- **Sem hospedagem ou streaming próprio.** Vídeos ficam no YouTube, não listados, e são exibidos por player embutido na plataforma.
- **Não é possível impedir a cópia do link por um Filiado.** O YouTube não oferece restrição de domínio para incorporação e seus Termos proíbem sobrepor elementos ao player. A plataforma garante que **não filiados nunca recebem o link** e que **todo acesso é rastreável**; a proteção contra redistribuição por filiados é dissuasiva (aviso de uso pessoal + registro por pessoa).
- **Prévia desfocada para o Não-Filiado**, no mesmo padrão do clube de benefícios.
- **Links do PMI Global ficam dentro da área exclusiva**, seguindo o refinamento ("da mesma forma, mas centralizado"). A plataforma só redireciona; nunca copia conteúdo do PMI.
- **Três tipos de conteúdo**: vídeo do YouTube, link externo e link do PMI Global. Sem upload de arquivos na plataforma.
- **Progresso e "continuar assistindo"** entram no escopo porque o player do YouTube expõe a posição de reprodução sem custo adicional.
- **Fora do escopo:** comentários, avaliações ou curtidas em conteúdos (item 15 do brainstorming, não essencial); playlists ou trilhas de aprendizagem; certificados por conteúdo assistido; marca d'água com nome do filiado (impossível com YouTube).

### Questões em aberto

1. **A área de conteúdo precisa mesmo ser inteiramente exclusiva?** Os links do PMI Global (PMBOK, projectmanagement.com) já exigem conta no PMI; poderiam ficar visíveis a Não-Filiados como isca de conversão sem perda. Decidir com a diretoria.
2. **Vale verificar automaticamente se o vídeo está "não listado"?** A YouTube Data API permite consultar o status de privacidade de um vídeo; hoje o cadastro depende da declaração do Admin (US-06.05, critério 3).
3. **O nível de proteção dissuasiva é suficiente?** Se a diretoria exigir bloqueio real de redistribuição, será preciso migrar os vídeos para uma plataforma paga com restrição de domínio (Vimeo, Bunny Stream, Mux) — o que contraria a premissa de não ter hospedagem própria.
4. **Quais categorias iniciais?** (ex.: Webinars, Treinamentos, Encontros gravados, Materiais de apoio, Conhecimento PMI). Impacta o catálogo e a exportação.
5. **Vídeos exigem vínculo com um evento da agenda** (gravação do webinar X)? Se sim, o cadastro deveria permitir associar o conteúdo a um evento (ÉP-05).

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-06 (Conteúdo Exclusivo) para Não-Filiado, Filiado e Admin, com análise das possibilidades de proteção de vídeos do YouTube | Vitor Leonardo | Nome do revisor |
