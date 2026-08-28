# Brainstorming

## 1. Introdução

O **brainstorming** é uma técnica de geração coletiva de ideias formalizada por Alex Osborn em *Applied Imagination* ([Osborn, 1953](#referencias)), sustentada por quatro regras: suspender o julgamento, buscar quantidade antes de qualidade, encorajar ideias ousadas e combinar/aperfeiçoar as contribuições alheias. Sua premissa central é separar, no tempo, o ato de **criar** do ato de **avaliar** — quando as duas coisas acontecem juntas, a crítica precoce inibe a produção de alternativas.

No contexto de Engenharia de Requisitos, o brainstorming é classificado como uma técnica de **elicitação de requisitos**: um instrumento para descobrir, junto às partes interessadas, quais necessidades o software deve atender antes que qualquer decisão de projeto seja tomada ([ISO/IEC/IEEE 29148, 2018](#referencias); [Sommerville, 2011](#referencias)).

Neste projeto, a sessão de brainstorming serviu a três propósitos:

1. **Levantar** o espaço completo de possibilidades para a Área de Membros do PMI-DF, sem filtro prévio;
2. **Convergir** as visões divergentes dos stakeholders do capítulo em um entendimento compartilhado do produto;
3. **Priorizar** colaborativamente o escopo, produzindo a base para o backlog de [Histórias de Usuário](historias-usuario.md).

> O resultado deste documento é insumo direto para o [PM Canvas](pm-canvas.md) e para as [Histórias de Usuário (Backlog)](historias-usuario.md).

---

## 2. Metodologia

### 2.1. Ficha técnica da sessão

| Item | Descrição |
|------|-----------|
| **Data** | 27/08/2026 |
| **Duração total** | ≈ 60 minutos |
| **Participantes** | Stakeholders do PMI-DF e equipe de desenvolvimento |
| **Técnicas aplicadas** | Brainwriting (fase silenciosa), discussão convergente, priorização colaborativa |
| **Artefatos gerados** | Lista bruta de 33 itens mencionados e compilado refinado por módulo |

### 2.2. Estrutura adotada

A sessão foi desenhada segundo o modelo **divergir → convergir** do *Double Diamond* ([Design Council, 2005](#referencias)), no qual uma fase de abertura, que amplia deliberadamente o número de alternativas, é seguida por uma fase de fechamento, que reduz o conjunto a decisões acionáveis.

Para mitigar dois problemas clássicos do brainstorming presencial — o **bloqueio de produção** (só uma pessoa fala por vez) e a **apreensão de avaliação** (medo do julgamento alheio) —, a primeira seção foi executada em formato de **brainwriting**: cada participante registra suas ideias por escrito, em silêncio e simultaneamente aos demais ([Heslin, 2009](#referencias); [Gray; Brown; Macanufo, 2010](#referencias)).

A sessão foi dividida em quatro seções:

#### Seção 1 — Divergência silenciosa (8 min 42 s)

Cinco perguntas disparadoras foram respondidas **individualmente, em silêncio e sem julgamento**, com tempo cronometrado por pergunta. A orientação foi registrar o que viesse à cabeça, sem autocensura:

| # | Pergunta disparadora | Tempo |
|---|----------------------|-------|
| 1 | Qual ou quais problemas a área de comunidade resolve? | 2 min |
| 2 | A quem (quais perfis) a área de comunidade deve atender? | 45 s |
| 3 | O que faria os perfis mapeados usarem a área de comunidade, a nível de funcionalidades? | 2 min |
| 4 | Assim que a área de comunidade estiver pronta, o que isso muda para os perfis mapeados? | 2 min |
| 5 | Para você: o que a área de comunidade **não** é? | 2 min |

A pergunta 5 cumpre papel específico de **delimitação de escopo negativo** — explicitar o que está fora evita expectativas divergentes tão eficazmente quanto declarar o que está dentro.

#### Seção 2 — Convergência entre stakeholders (18 min)

As respostas individuais foram lidas em voz alta e discutidas. Para cada uma das cinco perguntas, os stakeholders formularam **uma resposta convergente única**, consolidando as visões individuais em um entendimento compartilhado.

> **Nota de execução:** esta seção estava planejada para 10 minutos e consumiu 18 minutos na prática. O tempo adicional foi absorvido pela Seção 4, que não tinha teto fixo de duração.

#### Seção 3 — Apresentação do catálogo de ideias (5 min)

A equipe de desenvolvimento apresentou um catálogo de ideias e histórias previamente elaboradas para a área de comunidade. Cada stakeholder anotou individualmente as ideias que considerou mais aderentes ao capítulo — uma etapa de **estímulo cruzado**, em que o material externo amplia o repertório sem substituir a produção autoral da Seção 1.

#### Seção 4 — Priorização conjunta do escopo (+28 min)

Stakeholders e desenvolvedores discutiram em conjunto para fechar um **escopo priorizado**, descrevendo as funcionalidades consideradas mais relevantes e os perfis atendidos por cada uma. A classificação adotada foi binária e simplificada, inspirada na lógica *Must have / Won't have* do método **MoSCoW** ([Clegg; Barker, 1994](#referencias); [DSDM Consortium, 2014](#referencias)):

- **Essencial** — sem o item, não há produto viável;
- **Prioridade** — entra no escopo, mas não bloqueia a primeira entrega;
- **Não prioridade / não essencial** — reconhecido como válido, porém deliberadamente adiado.

### 2.3. Pós-sessão — refinamento

Após a reunião, a lista bruta de itens foi consolidada pela equipe em um **compilado refinado por módulo funcional**, no qual cada bloco de funcionalidade recebeu descrição detalhada e definição de quais perfis o acessam. Esse compilado é apresentado na seção [3.3](#33-compilado-refinado-por-modulo).

---

## 3. Resultados

### 3.1. Perfis de usuário identificados

A pergunta 2 da Seção 1 convergiu para três perfis-base, posteriormente desdobrados no refinamento:

| Perfil | Descrição | Origem |
|--------|-----------|--------|
| **Pessoa** | Usuário autenticado não filiado ao PMI. Acessa o portal em modelo *freemium*. | Sessão |
| **Filiado** | Usuário com vínculo ativo ao PMI, validado por PMI ID. Acessa conteúdo exclusivo e benefícios. | Sessão |
| **Admin** | Opera a base, aprova cadastros e edita todo o conteúdo da plataforma. | Sessão |
| **Estudante** | Desdobramento de *Pessoa*, com validação por comprovante de matrícula. | Item 8 / 16 |
| **Voluntário** | Desdobramento atribuído manualmente pelo Admin; alimenta o organograma. | Item 13 / 16 |

### 3.2. Itens mencionados e priorização

Foram registrados **33 itens** ao longo da sessão. A tabela abaixo reproduz a priorização acordada na Seção 4.

#### 3.2.1. Escopo priorizado

| # | Item | Classificação |
|---|------|---------------|
| 1 | Login, cadastro, entrar com o Google e "esqueci a senha" | **Essencial** |
| 2 | Admin para modularidade | **Essencial** |
| 3 | Clube de benefícios | **Essencial** |
| 4 | Agenda / calendário | **Essencial** |
| 5 | Área de admin para edição do conteúdo | **Essencial** |
| 6 | Redirect para o site oficial do PMI (PMBOK, projectmanagement.com) | Prioridade |
| 7 | Acesso a conteúdos exclusivos — vídeos de treinamento (YouTube não listado) | Prioridade |
| 8 | Diferenciação do que cada perfil vê — filiado, pessoa, estudante | Prioridade |
| 9 | Oportunidades profissionais — conexão com vagas e empresas | Prioridade |
| 10 | Gamificação para maior engajamento | Prioridade |
| 11 | Fortalecer o sentimento de pertencimento e exclusividade de ser membro e voluntário | Prioridade |
| 12 | Ranking e benefícios resgatáveis; badges de crescimento na carreira | Prioridade |
| 13 | Perfil de usuário com mini bio e permissões de divulgação; voluntário exposto no organograma | Prioridade |

#### 3.2.2. Fora do escopo prioritário

| # | Item | Observação |
|---|------|------------|
| 14 | Filiado enxerga mais conteúdo que a Pessoa, para instigar a filiação | — |
| 15 | Espaço para postar mensagens e compartilhar conteúdo | Não essencial |
| 16 | "Enriquecimento" de cadastro — formulários distintos para Filiado, Estudante e Voluntário | — |
| 17 | Aprovação de usuário na área de admin | — |
| 18 | Usuário visualiza eventos passados (base para a gamificação) | — |
| 19 | Centralização de conteúdo (usuários consomem, admin registra) | Não essencial |
| 20 | Gestão de projetos voluntários (planejamento e *status report*) | — |
| 21 | Cadastro das atividades de um voluntário | — |
| 22 | Rede social, fórum, notícias PMI-DF e avisos por e-mail | Não essencial |
| 23 | FAQ ou chatbot | — |
| 24 | Vitrine de currículos para filiados, estudantes e voluntários | Não essencial |
| 25 | Validador de certificado de voluntário | — |
| 26 | Portal da transparência — participantes em eventos, número de filiados e demais indicadores | — |
| 27 | Personalização de personagem | Não essencial |
| 28 | Pesquisa de NPS dos eventos e demais pesquisas de feedback | — |
| 29 | Badges cumulativos de carreira para as jornadas de voluntário, filiado e estudante | Não essencial |
| 30 | Gamificação de voluntários com registro de projetos, visando a eleição de "voluntário do ano" | Não essencial |
| 31 | Gamificação de estudantes e filiados por presença em eventos e na comunidade | Não essencial |
| 32 | Lojinha de resgate de prêmios | Requer definição de logística |
| 33 | Ver quem vai ao evento | Não essencial |

#### 3.2.3. Distribuição

| Classificação | Itens | Proporção |
|---------------|-------|-----------|
| Essencial | 5 | 15% |
| Prioridade | 8 | 24% |
| Não prioridade | 20 | 61% |
| **Total** | **33** | **100%** |

A concentração de 61% dos itens fora do escopo prioritário é o resultado esperado de uma sessão bem conduzida: a fase divergente cumpriu seu papel de gerar volume, e a fase convergente cumpriu o dela ao recusar a maior parte desse volume para a primeira entrega.

### 3.3. Compilado refinado por módulo

O refinamento pós-sessão organizou os itens priorizados em **sete módulos funcionais**.

#### 3.3.1. Autenticação

- Cadastro com e-mail, senha, telefone, local de residência, escolaridade, data de nascimento e gênero.
- A própria tela de cadastro pode especializar **Pessoa** em **Filiado**; nesse caso é obrigatório informar o vínculo com o PMI-DF.
- Login social com Google.
- Recuperação de senha por e-mail.
- Modelo *freemium*: a **Pessoa** visualiza o que o **Filiado** acessa, mas o consumo é bloqueado na própria página.
- Integração com a API do PMI (sistema **ThoughtSpot**) para validar a condição de filiado.

#### 3.3.2. Perfil de usuário

- **Pessoa** e **Filiado** cadastram informações pessoais, mini bio, foto, indicação de que procuram emprego, os e-mails usados no Sympla e as opções de adesão à gamificação e ao e-mail marketing.
- O **Admin** visualiza todas as Pessoas e marca manualmente quem é filiado ou voluntário.
- Ao marcar alguém como voluntário, o registro entra automaticamente na aba de **organograma**, exigindo o preenchimento de cargo e diretoria.

#### 3.3.3. Clube de benefícios

- Página de benefícios **exclusiva do Filiado** — a Pessoa não acessa.
- Benefícios cadastrados pelo Admin com foto, título, legenda e código de cupom, ou com texto informativo explicando como solicitar.
- Obrigatório registrar em banco o histórico de qual filiado resgatou qual benefício.

#### 3.3.4. Calendário / agenda de eventos

- Exibição de eventos com título, legenda, data e formato — visível a Filiado e Pessoa.
- Exibição do histórico de eventos já participados — visível a Filiado e Pessoa.

#### 3.3.5. Área de conteúdo exclusivo

- "YouTube privado do filiado": exibição de conteúdos não listados na plataforma de vídeo.
- Admin cadastra URLs de vídeos e de outros conteúdos, com título e descrição.
- Redirecionamento centralizado para conteúdos do PMI — PMBOK, cursos e afins.

#### 3.3.6. ATS de vagas

- Cadastro de vagas pelo Admin com e-mail do recrutador, descrição, nome da empresa, foto opcional e URL compartilhável.
- Filiado e Pessoa apenas visualizam; **não há interação do usuário** com a vaga nesta versão.

#### 3.3.7. Gamificação

- Badges **permanentes e exclusivos de filiados**, com marcos por tempo de filiação: ao se tornar filiado e aos 1, 5 e 10 anos.
- Badges comemorativos — por exemplo, o de 2026, condicionado a uma missão específica.
- Ranking das pessoas mais participativas em eventos.
- Admin cadastra novos badges com foto, descrição de como conquistá-lo e o que deve ser submetido como comprovação.
- O Filiado submete a comprovação e a **aprovação é manual**, feita pelo Admin.

### 3.4. Rastreabilidade — do brainstorming ao backlog

A tabela relaciona os módulos e itens priorizados às [Histórias de Usuário](historias-usuario.md) já redigidas, evidenciando o que foi convertido em requisito e o que permanece em aberto.

| Módulo / item | Histórias de usuário derivadas | Situação |
|---------------|-------------------------------|----------|
| Autenticação (itens 1, 16) | HU 01, HU 04 | Coberto |
| Perfil de usuário (itens 13, 16) | HU 02, HU 03, HU 09 | Coberto |
| Admin e aprovação (itens 2, 5, 17) | HU 13, HU 14 | Coberto |
| Calendário / eventos (itens 4, 18) | HU 07, HU 08, HU 15 | Coberto |
| ATS de vagas (item 9) | HU 05 | Coberto |
| Portal da transparência (item 26) | HU 06, HU 10 | Coberto |
| Avisos por e-mail (item 22) | HU 11 | Coberto |
| Vitrine de currículos (item 24) | HU 12 | Coberto |
| **Clube de benefícios (item 3)** | — | **Lacuna** |
| **Conteúdo exclusivo (itens 6, 7)** | — | **Lacuna** |
| **Gamificação (itens 10, 12)** | — | **Lacuna** |

---

## 4. Conclusão

A sessão cumpriu os três objetivos definidos na introdução. A separação temporal entre divergência e convergência mostrou-se produtiva: 33 itens foram levantados em pouco mais de 8 minutos de escrita silenciosa e reduzidos, ao final, a um escopo de 13 itens priorizados — dos quais 5 foram classificados como essenciais.

Três conclusões se destacam:

1. **A base do produto é consensual.** Os cinco itens essenciais — autenticação, administração modular, edição de conteúdo, agenda e clube de benefícios — não geraram divergência entre os participantes. Isso sinaliza um núcleo estável para a primeira entrega.

2. **A gamificação é um tema recorrente, porém difuso.** Aparece em seis itens distintos (10, 12, 29, 30, 31 e 32), sendo que quatro deles foram classificados como não essenciais. O tema é claramente desejado, mas ainda não está maduro o suficiente para virar requisito — o que é coerente com o registro de que a história correspondente segue pendente de definição.

3. **Há lacunas de cobertura a endereçar.** Conforme a seção [3.4](#34-rastreabilidade-do-brainstorming-ao-backlog), três módulos priorizados — incluindo o **clube de benefícios**, classificado como *essencial* — ainda não possuem história de usuário redigida. Essa é a ação mais imediata decorrente deste documento.

### Próximos passos

- [ ] Redigir as histórias de usuário do **Clube de benefícios** (essencial).
- [ ] Redigir as histórias de **Conteúdo exclusivo** e **Gamificação**.
- [ ] Validar o escopo priorizado com os stakeholders no [PM Canvas](pm-canvas.md).
- [ ] Confrontar os itens 6, 7 e 9 com o [Benchmarking](benchmarking.md) para verificar soluções de mercado equivalentes.

### Limitações

Os tempos aplicados foram curtos por desenho — a Seção 1 dispôs de 8 min 42 s no total. Isso favorece a fluência e a quantidade de ideias, mas limita o aprofundamento individual. A Seção 2 excedeu em 8 minutos o tempo planejado, indicando que a convergência exigiu mais debate do que o previsto. Além disso, a priorização foi binária (essencial / não essencial), sem escala de esforço ou de valor de negócio — uma técnica complementar, como a matriz **Valor × Esforço**, poderia refinar a ordenação dentro da faixa "Prioridade".

---

## 5. Referências

<a id="referencias"></a>

1. **OSBORN, Alex F.** *Applied Imagination: Principles and Procedures of Creative Problem-Solving.* New York: Charles Scribner's Sons, 1953.

2. **ISO/IEC/IEEE 29148:2018.** *Systems and software engineering — Life cycle processes — Requirements engineering.* Geneva: ISO, 2018.

3. **SOMMERVILLE, Ian.** *Engenharia de Software.* 9. ed. São Paulo: Pearson Prentice Hall, 2011.

4. **DESIGN COUNCIL.** *The Double Diamond: A universally accepted depiction of the design process.* London: Design Council, 2005. Disponível em: <https://www.designcouncil.org.uk/our-resources/the-double-diamond/>.

5. **HESLIN, Peter A.** Better than brainstorming? Potential contextual boundary conditions to brainwriting for idea generation in organizations. *Journal of Occupational and Organizational Psychology*, v. 82, n. 1, p. 129-145, 2009.

6. **GRAY, Dave; BROWN, Sunni; MACANUFO, James.** *Gamestorming: A Playbook for Innovators, Rulebreakers, and Changemakers.* Sebastopol: O'Reilly Media, 2010.

7. **CLEGG, Dai; BARKER, Richard.** *Case Method Fast-Track: A RAD Approach.* Boston: Addison-Wesley, 1994.

8. **DSDM CONSORTIUM.** *The DSDM Agile Project Framework Handbook.* Ashford: DSDM Consortium, 2014. Disponível em: <https://www.agilebusiness.org/dsdm-project-framework.html>.

9. **PROJECT MANAGEMENT INSTITUTE.** *Um Guia do Conhecimento em Gerenciamento de Projetos (Guia PMBOK).* 7. ed. Newtown Square: PMI, 2021.

### Documentos-fonte

| Documento | Conteúdo |
|-----------|----------|
| `Brainstorming - PMI-DF.docx` | Agenda da sessão, perguntas disparadoras e lista bruta de 33 itens priorizados |
| `Brainstorming - PMI-DF (1).docx` | Compilado refinado por módulo funcional e perfis atendidos |

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 27/08/2026 | Criação do documento a partir da sessão de brainstorming de 27/08/2026 | Vitor Leonardo | Nome do revisor |
