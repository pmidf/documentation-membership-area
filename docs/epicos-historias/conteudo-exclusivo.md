# ÉP-06 — Conteúdo Exclusivo

## 1. Visão geral do épico

**Objetivo:** centralizar em um único lugar, dentro da plataforma, o conteúdo produzido pelo capítulo (vídeos de treinamentos, webinars gravados, materiais) e os links para o conhecimento do PMI Global (PMBOK, projectmanagement.com, cursos). A maior parte do acervo é exclusiva para Filiados; uma seleção marcada como Pública fica aberta a qualquer visitante, como amostra do que a filiação oferece.

**Valor:** junto com o clube de benefícios, é o que torna a filiação **tangível no dia a dia**. Origem no brainstorming: item 7 (_acesso a conteúdos exclusivos — vídeos de treinamento, YouTube não listado_) e item 6 (_redirect para o site oficial do PMI_), ambos priorizados; bloco _Área de conteúdo exclusivo_ do refinamento pós-reunião.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. A URL ou o identificador de um vídeo exclusivo **nunca** é entregue a quem não é Filiado. A prévia desfocada é consequência da omissão de campos na API, não de um efeito visual.

### 1.1 Como o vídeo é entregue e o que é possível proteger

A plataforma **não hospeda nem transmite vídeo**. Os vídeos ficam no canal do PMI-DF no YouTube, configurados como **não listados**, e são exibidos **dentro da plataforma** por meio do player incorporado do YouTube. Isso define o que é e o que não é possível em termos de proteção:

| Medida | Efeito | Limite |
| --- | --- | --- |
| Vídeo **não listado** no YouTube | Não aparece em buscas, no canal nem em recomendações | Qualquer pessoa **com o link** assiste |
| URL do vídeo entregue **só por endpoint autenticado** a Filiado ativo | Não-Filiados nunca recebem o identificador do vídeo; nada aparece no HTML público nem na listagem | O Filiado, ao assistir, recebe o identificador no navegador, é assim que o player funciona |
| Player **embutido** na plataforma, sem botões de compartilhar/copiar link nossos | A experiência acontece dentro do portal; não há incentivo para sair | O player do YouTube mantém o botão "Assistir no YouTube" — os Termos da API do YouTube **proíbem** sobrepor ou ocultar elementos do player |
| **Registro de visualização por pessoa** (quem, o quê, quando) | Rastreabilidade: se um vídeo vazar, o capítulo sabe quem teve acesso | Não impede o vazamento |
| **Aviso de uso pessoal** exibido junto ao player | Dissuasão e base para eventual sanção pela diretoria | Depende de conduta, não de tecnologia |

### 1.2 Tipos de conteúdo

| Tipo              | O que é                                                                      | Como o Filiado consome                               |
| ----------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------- |
| `VIDEO_YOUTUBE`   | Vídeo não listado no canal do PMI-DF                                         | Player embutido dentro da plataforma                 |
| `LINK_EXTERNO`    | Material hospedado fora (artigo, PDF em drive, gravação em outra plataforma) | Abre em nova aba                                     |
| `LINK_PMI_GLOBAL` | Recurso oficial do PMI (PMBOK, projectmanagement.com, cursos, certificações) | Abre em nova aba, com indicação de que é site do PMI |

### 1.3 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia desfocada · **E** = Escrever · **—** = Sem acesso

| Recurso                                                       | Não-Filiado | Filiado | Admin |
| ------------------------------------------------------------- | :---------: | :-----: | :---: |
| Catálogo de conteúdos públicos (títulos, capas, categorias)   |      L      |    L    |   E   |
| Catálogo de conteúdos exclusivos (títulos, capas, categorias) |      P      |    L    |   E   |
| Player de vídeo / identificador do vídeo público              |      L      |    L    |   E   |
| Player de vídeo / identificador do vídeo exclusivo                     |      —      |    L    |   E   |
| Links externos públicos                                       |      L      |    L    |   E   |
| Links externos exclusivos                                     |      —      |    L    |   E   |
| Links do PMI Global                                           |      L      |    L    |   E   |
| Próprio histórico de visualização                             |      —      |    L    |   —   |
| Gestão de conteúdos e categorias                              |      —      |    —    |   E   |
| Números de visualização e exportação                          |      —      |    —    | L / E |

### 1.4 Mapa das histórias

| ID                                                                               | História                                                    | Atores      | Prioridade |
| -------------------------------------------------------------------------------- | ----------------------------------------------------------- | ----------- | ---------- |
| [US-06.01](#us-0601-ver-a-previa-desfocada-da-area-de-conteudo-exclusivo)        | Ver a prévia desfocada da área de conteúdo exclusivo        | Não-Filiado | Must       |
| [US-06.02](#us-0602-navegar-pelo-catalogo-de-conteudo)                           | Navegar pelo catálogo de conteúdo                           | Filiado     | Must       |
| [US-06.03](#us-0603-assistir-a-um-video-dentro-da-plataforma)                    | Assistir a um vídeo dentro da plataforma                    | Filiado     | Must       |
| [US-06.04](#us-0604-acessar-os-recursos-do-pmi-global-em-um-so-lugar)            | Acessar os recursos do PMI Global em um só lugar            | Filiado     | Should     |
| [US-06.05](#us-0605-cadastrar-e-publicar-um-conteudo)                            | Cadastrar e publicar um conteúdo                            | Admin       | Must       |
| [US-06.06](#us-0606-editar-despublicar-e-arquivar-um-conteudo)                   | Editar, despublicar e arquivar um conteúdo                  | Admin       | Must       |
| [US-06.07](#us-0607-gerenciar-e-exportar-todos-os-conteudos)                     | Gerenciar e exportar todos os conteúdos                     | Admin       | Should     |
| [US-06.08](#us-0608-acessar-os-conteudos-publicos-da-area-de-conteudo-exclusivo) | Acessar os conteúdos públicos da área de conteúdo exclusivo | Não-Filiado | Should     |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-06.XX.N).

---

## 2. Histórias do Não-Filiado

### US-06.01 — Ver a prévia desfocada da área de conteúdo exclusivo

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que existe uma área de conteúdo exclusivo e ter uma noção do que os filiados têm à disposição
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Conteúdo exclusivo", **então** vejo a página com os _cards_ dos conteúdos publicados **desfocados** (efeito _blur_), de modo que dá para perceber capas, a quantidade de conteúdos e os títulos, mas não ler descrições.
2. **Dado** que a página está desfocada, **quando** olho a tela, **então** vejo sobre ela um painel fixo com a mensagem "Exclusivo para filiados"".
3. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas a quantidade de conteúdos, as imagens de capa e título. Descrição, URL, identificador de vídeo e links **não estão presentes**.

**Regras de negócio**

- RN-06.01.1 — O desfoque é aplicado no frontend, mas a **proteção é a omissão de campos no backend**: o Não-Filiado nunca recebe os dados que o _blur_ esconde.

---

### US-06.08 — Acessar os conteúdos públicos da área de conteúdo exclusivo

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** conseguir abrir e consumir por completo os conteúdos que o capítulo marcou como Públicos
> **Para** experimentar de verdade a qualidade do conteúdo do PMI-DF antes de decidir me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Conteúdo exclusivo", **então** os _cards_ marcados como **Público** aparecem nítidos (sem _blur_), com capa, título, tipo, duração/formato e descrição — exatamente como um Filiado veria (US-06.02).
2. **Dado** que abro um conteúdo `VIDEO_YOUTUBE` marcado como Público, **quando** a página carrega, **então** o player do YouTube carrega embutido normalmente, sem exigir filiação nem redirecionar ao fluxo de validação.
3. **Dado** que abro um `LINK_EXTERNO` ou `LINK_PMI_GLOBAL` marcado como Público, **quando** clico, **então** ele abre em nova aba, do mesmo jeito que abriria para um Filiado (US-06.04).
4. **Dado** que estou na área de conteúdo, **quando** olho o catálogo, **então** os _cards_ Exclusivos continuam desfocados (US-06.01) lado a lado com os Públicos nítidos.
5. **Dado** que inspeciono a resposta da API sem filiação ativa para um conteúdo Público, **quando** analiso o conteúdo, **então** recebo os mesmos campos que um Filiado receberia para aquele item. A omissão de campos (RN-06.01.1) só vale para conteúdos Exclusivos.

**Regras de negócio**

- RN-06.08.1 — Todo conteúdo tem um **Nível de acesso** definido pelo Admin no cadastro (US-06.05): **Público** ou **Exclusivo para filiados**. `LINK_PMI_GLOBAL` é sempre Público, por já exigir login próprio no PMI Global.
- RN-06.08.2 — Para conteúdo Público, a omissão de campos da RN-06.01.1 não se aplica: os mesmos dados vistos por um Filiado são entregues a Não-Filiados.
- RN-06.08.3 — O identificador de vídeos Públicos é servido por um endpoint **sem exigência de filiação**, distinto do endpoint autenticado usado para vídeos Exclusivos (RN-06.03.1).
- RN-06.08.4 — Visualizações de conteúdo Público por Não-Filiado são contadas nas métricas da conteúdo (US-06.07).

---

## 3. Histórias do Filiado

### US-06.02 — Navegar pelo catálogo de conteúdo

**Atores:** Filiado

> **Como** Filiado
> **Quero** encontrar rapidamente os vídeos e materiais do capítulo
> **Para** aproveitar o conteúdo que só a filiação oferece

**Critérios de aceite**

1. **Dado** que sou Filiado ativo, **quando** acesso "Conteúdo exclusivo", **então** vejo os destaques no topo e, abaixo, os conteúdos publicados agrupados por categoria, em _cards_ com capa, título, tipo (vídeo, material ou PMI), duração (para vídeos) e data de publicação.
2. **Dado** que busco por texto, **quando** digito, **então** título e descrição são pesquisados.
3. **Dado** que filtro por categoria ou tipo, **quando** aplico, **então** a listagem responde ao filtro.
4. **Dado** que marquei um conteúdo como consumido, **quando** o vejo no catálogo, **então** o _card_ exibe a marca "Consumido".
5. **Dado** que abro um conteúdo do tipo material externo, **quando** clico, **então** ele abre em nova aba e o acesso é registrado.

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
3. **Dado** que estou na página do vídeo, **quando** leio abaixo do player, **então** vejo o aviso "Conteúdo exclusivo para filiados do PMI-DF. Uso pessoal; a redistribuição do link é vedada.".
4. **Dado** que minha filiação deixou de ser reconhecida enquanto navego, **quando** abro o próximo vídeo, **então** o player não carrega e vejo a prévia desfocada de US-06.01.

**Regras de negócio**

- RN-06.03.1 — O identificador do vídeo é servido **exclusivamente** por endpoint autenticado que verifica, no momento da requisição, que a filiação está ativa. Nunca aparece no HTML da página, na listagem ou em qualquer resposta a não filiados.
- RN-06.03.2 — Todo vídeo cadastrado deve estar como **não listado** no YouTube, com incorporação permitida (responsabilidade do Admin, US-06.05).
- RN-06.03.3 — **Nenhum elemento da plataforma é sobreposto ao player**, em respeito aos Termos da API do YouTube. O botão "Assistir no YouTube" do player permanece visível — limitação conhecida e aceita.
- RN-06.03.4 — Cada concessão de acesso a um vídeo gera um registro de visualização (pessoa, conteúdo, data e hora), consultável pelo Admin (US-06.07). É a base de rastreabilidade caso um link seja redistribuído.
- RN-06.03.5 — A plataforma não faz download, cópia ou retransmissão do vídeo — apenas incorpora o player do YouTube.

---

### US-06.04 — Acessar os recursos do PMI Global em um só lugar

**Atores:** Filiado

> **Como** Filiado
> **Quero** encontrar, dentro da área de conteúdo, os links oficiais do PMI Global (PMBOK, projectmanagement.com, cursos e certificações)
> **Para** não me perder navegando entre vários sites do PMI

**Critérios de aceite**

1. **Dado** que acesso a seção "Conhecimento PMI" dentro de "Conteúdo exclusivo", **quando** a página carrega, **então** vejo os recursos curados agrupados por tema (PMBOK e padrões, projectmanagement.com, cursos, certificações), com título, descrição e a indicação "Abre no site do PMI".
2. **Dado** que clico em um recurso, **quando** o clique acontece, **então** a URL oficial abre em nova aba e o clique é registrado.
3. **Dado** que o recurso exige login na conta do PMI Global, **quando** o visualizo, **então** essa informação aparece de forma explícita antes de clicar ("alguns exigem filiação ativa no PMI Global.").

**Regras de negócio**

- RN-06.04.1 — A plataforma apenas **redireciona**; nunca copia, espelha ou faz _scraping_ de conteúdo do PMI Global, respeitando a propriedade intelectual do PMI.
- RN-06.04.2 — Só URLs de domínios oficiais do PMI (`pmi.org`, `projectmanagement.com` e subdomínios) são aceitas para este tipo de conteúdo.
- RN-06.04.3 — Os links do PMI Global são sempre marcados como Público (RN-06.08.1) e ficam visíveis e clicáveis para Não-Filiado, sem desfoque, seguindo US-06.08.

---

## 4. Histórias do Admin

### US-06.05 — Cadastrar e publicar um conteúdo

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar vídeos do YouTube, materiais externos e links do PMI, com título, descrição e categoria
> **Para** disponibilizá-los na área de conteúdo exclusivo

**Critérios de aceite**

1. **Dado** que crio um conteúdo, **quando** informo título, descrição, tipo, URL e nível de acesso (Público ou Exclusivo para filiados), **então** clico no botão e Publicar Conteúdo para que ele seja visível.
2. **Dado** que o tipo é `VIDEO_YOUTUBE`, **quando** colo a URL (`youtube.com/watch?v=`, `youtu.be/` ou `youtube.com/embed/`), **então** a plataforma extrai o identificador do vídeo, busca automaticamente capa, título sugerido e duração, e exibe o player de pré-visualização para confirmar que a incorporação funciona.

**Regras de negócio**

- RN-06.05.1 — URLs de qualquer tipo são validadas contra lista de domínios permitidos, para que a área de conteúdo não funcione como redirecionador aberto.
- RN-06.05.2 — A publicação é registrada em auditoria com usuário, data e hora.

---

### US-06.06 — Editar e arquivar um conteúdo

**Atores:** Admin

> **Como** Admin
> **Quero** manter o ciclo de vida de um conteúdo após a publicação
> **Para** corrigir informações, retirar materiais desatualizados e manter a área sempre atual

**Critérios de aceite**

1. **Dado** que edito um conteúdo publicado, **quando** salvo, **então** a alteração é imediata para os usuários.
2. **Dado** que troco a URL de um vídeo, **quando** salvo, **então** a capa, a duração e o player de pré-visualização são atualizados.
3. **Dado** que removo um conteúdo, **quando** confirmo, **então** ele some do catálogo, mas o histórico de visualizações permanece.

**Regras de negócio**

- RN-06.06.1 — **Exclusão de conteúdo não deleta ele do banco**; o encerramento é sempre tirando qualquer acesso a ele, para preservar o histórico de visualizações.

---

### US-06.07 — Visualizar e exportar acessos de um conteúdo específico

**Atores:** Admin

> **Como** Admin  
> **Quero** visualizar os detalhes de um conteúdo específico e exportar a lista de filiados que o acessaram  
> **Para** saber exatamente quem consumiu aquele material, comprovar engajamento individual e prestar contas à diretoria  

**Critérios de aceite**

1. **Dado** que acesso a página de estatísticas de um conteúdo específico, **quando** a tela carrega, **então** vejo o título, tipo, data de publicação, métricas gerais (visualizações totais, espectadores únicos, conclusão média, visualizações recentes), o gráfico de visualizações ao longo do tempo e a seção **"Quem acessou"**.
2. **Dado** que visualizo a seção "Quem acessou", **quando** a lista é renderizada, **então** vejo o total de filiados que visualizaram o conteúdo, os registros contendo nome, cargo/organização e data/hora do acesso (`DD/MM/AAAA · HH:mm`), além do link "Ver todos os [N] espectadores".
3. **Dado** que clico no botão **"Exportar CSV"** dentro da seção "Quem acessou", **quando** a solicitação é processada, **então** recebo um arquivo CSV contendo apenas os registros de acesso daquele conteúdo específico, com uma linha por visualização (nome, e-mail, cargo, data, hora e progresso/conclusão).
4. **Dado** que o download do CSV de acessos é concluído, **quando** a ação finaliza, **então** o evento é registrado na trilha de auditoria com data, hora, usuário administrador e conteúdo exportado.

**Regras de negócio**

- RN-06.07.1 — Visualizações e registros de acesso derivam exclusivamente dos logs de consumo da plataforma autenticada; nunca são inseridos ou modificados manualmente.
- RN-06.07.2 — A exportação via seção "Quem acessou" é restrita ao conteúdo em visualização, contendo todos os filiados que registraram acesso, inclusive caso o conteúdo esteja arquivado.

---

## 5. Decisões e questões em aberto

### Decisões

- **Sem hospedagem ou streaming próprio.** Vídeos ficam no YouTube, não listados, e são exibidos por player embutido na plataforma.
- **Não é possível impedir a cópia do link por um Filiado.** O YouTube não oferece restrição de domínio para incorporação e seus Termos proíbem sobrepor elementos ao player. A plataforma garante que **não filiados nunca recebem o link** e que **todo acesso é rastreável**; a proteção contra redistribuição por filiados é dissuasiva (aviso de uso pessoal + registro por pessoa).
- **Prévia desfocada para o Não-Filiado**, no mesmo padrão do clube de benefícios.
<!-- - **Links do PMI Global ficam dentro da área exclusiva**, seguindo o refinamento ("da mesma forma, mas centralizado"). A plataforma só redireciona; nunca copia conteúdo do PMI. -->
- **Três tipos de conteúdo**: vídeo do YouTube, link externo e link do PMI Global. Sem upload de arquivos na plataforma.
- **Fora do escopo:** comentários, avaliações ou curtidas em conteúdos (item 15 do brainstorming, não essencial); playlists ou trilhas de aprendizagem; certificados por conteúdo assistido; marca d'água com nome do filiado (impossível com YouTube).

### Questões em aberto

1. **A área de conteúdo precisa mesmo ser inteiramente exclusiva?** Os links do PMI Global (PMBOK, projectmanagement.com) já exigem conta no PMI; poderiam ficar visíveis a Não-Filiados como isca de conversão sem perda. Decidir com a diretoria.
2. **Vale verificar automaticamente se o vídeo está "não listado"?** A YouTube Data API permite consultar o status de privacidade de um vídeo; hoje o cadastro depende do Admin.
3. **O nível de proteção dissuasiva é suficiente?** Se a diretoria exigir bloqueio real de redistribuição, será preciso migrar os vídeos para uma plataforma paga com restrição de domínio (Vimeo, Bunny Stream, Mux) — o que contraria a premissa de não ter hospedagem própria.
4. **Quais categorias iniciais?** (ex.: Webinars, Treinamentos, Encontros gravados, Materiais de apoio, Conhecimento PMI). Impacta o catálogo e a exportação.
5. **Vídeos exigem vínculo com um evento da agenda** (gravação do webinar X)? Se sim, o cadastro deveria permitir associar o conteúdo a um evento (ÉP-05).
6. **Progresso e "continuar assistindo devem ser adicionados?"** Se devemos adicionar uma forma de guardar o progresso da visualização de um conteúdo por um usuário.

---

## Tabela de Contribuições

| Versão | Data       | Descrição                                                                                                                                                         | Autor          | Revisor          |
| ------ | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ---------------- |
| 1.0    | 11/09/2026 | Criação do documento — histórias do ÉP-06 (Conteúdo Exclusivo) para Não-Filiado, Filiado e Admin, com análise das possibilidades de proteção de vídeos do YouTube | Vitor Leonardo | Ana Clara Borges |
| 1.1    | 13/09/2026 | Adição da US-06.08 e adaptações nas demais histórias para coerência. Retirada de possibilidade de usuários visitantes | Ana Clara Borges |  |
