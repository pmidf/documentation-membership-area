# ÉP-04 — Clube de Benefícios

## 1. Visão geral do épico

**Objetivo:** materializar o retorno prático e financeiro da filiação ao PMI-DF, entregando ao Filiado os benefícios negociados com parceiros do capítulo e registrando cada resgate para prestação de contas.

**Valor:** é o módulo que torna a filiação **tangível**. Junto com o conteúdo exclusivo, é o principal argumento de conversão do Não-Filiado e foi classificado como **Essencial** no brainstorming.

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. O código do cupom e a instrução de resgate **nunca** trafegam para quem não é Filiado — a prévia desfocada é consequência da omissão de campos na API, não de um efeito visual.

### 1.1 Matriz de permissões do épico

Legenda: **L** = Ler · **P** = Prévia desfocada · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Lista de benefícios | P | L | E |
| Detalhe do benefício | P | L | E |
| Código do cupom / instrução de resgate | — | L (após resgatar) | E |
| Resgatar benefício | — | E | — |
| Próprio histórico de resgates (em "Meu perfil") | — | L | — |
| Histórico de resgates de terceiros | — | — | L |
| Gestão e exportação de benefícios | — | — | E |
| Relatório de resgates | — | — | L / E |

### 1.2 Tipos de resgate

| Tipo | Descrição | O que o Filiado recebe | Exemplo |
|------|-----------|------------------------|---------|
| `CUPOM` | Um código, igual para todos os filiados | O código, com botão "Copiar" e link do parceiro | `PMIDF20` em uma livraria parceira |
| `INSTRUCAO` | Não há código; o parceiro atende por outro canal | A instrução (e-mail, formulário, senha verbal) | "Apresente a carteirinha PMI na recepção" |

### 1.3 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-04.01](#us-0401-ver-a-previa-desfocada-do-clube-de-beneficios) | Ver a prévia desfocada do clube de benefícios | Não-Filiado | Must |
| [US-04.02](#us-0402-navegar-pelo-clube-e-consultar-um-beneficio) | Navegar pelo clube e consultar um benefício | Filiado | Must |
| [US-04.03](#us-0403-resgatar-um-beneficio) | Resgatar um benefício | Filiado | Must |
| [US-04.04](#us-0404-consultar-meu-historico-de-resgates-em-meu-perfil) | Consultar meu histórico de resgates em "Meu perfil" | Filiado | Must |
| [US-04.05](#us-0405-cadastrar-e-publicar-um-beneficio) | Cadastrar e publicar um benefício | Admin | Must |
| [US-04.06](#us-0406-editar-despublicar-e-arquivar-um-beneficio) | Editar, despublicar e arquivar um benefício | Admin | Must |
| [US-04.07](#us-0407-gerenciar-e-exportar-todos-os-beneficios) | Gerenciar e exportar todos os benefícios | Admin | Must |
| [US-04.08](#us-0408-acompanhar-o-relatorio-de-resgates) | Acompanhar o relatório de resgates | Admin | Should |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-04.XX.N).

---

## 2. Histórias do Não-Filiado

### US-04.01 — Ver a prévia desfocada do clube de benefícios

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver que existe um clube de benefícios e ter uma noção do que os filiados têm à disposição
> **Para** entender o que ganho ao me filiar

**Critérios de aceite**

1. **Dado** que sou Não-Filiado, **quando** acesso "Clube de benefícios", **então** vejo a página com os *cards* dos benefícios vigentes **desfocados** (efeito *blur*), de modo que dá para perceber imagens e a quantidade de benefícios, mas não ler título, parceiro ou detalhes.
2. **Dado** que a página está desfocada, **quando** olho a tela, **então** vejo sobre ela um painel fixo com a mensagem "Exclusivo para filiados", o CTA "Quero ser filiado" e o link "Já sou filiado — validar meu PMI ID".
3. **Dado** que tento clicar em um *card* desfocado, **quando** o clique acontece, **então** nada abre — o único caminho é pelo painel de filiação.
4. **Dado** que clico em "Já sou filiado", **quando** sou levado ao fluxo de validação (ÉP-01), **então** ao retornar com filiação ativa o desfoque desaparece e a página passa a se comportar como em US-04.02.
5. **Dado** que inspeciono a resposta da API como Não-Filiado, **quando** analiso o conteúdo, **então** recebo apenas a quantidade de benefícios e as imagens de capa — título, legenda, parceiro, descrição, código e instrução **não estão presentes**.
6. **Dado** que clico em "Quero ser filiado", **quando** o clique acontece, **então** o evento é registrado com origem `beneficios` para medir conversão.

**Regras de negócio**

- RN-04.01.1 — O desfoque é aplicado no frontend, mas a **proteção é a omissão de campos no backend**: o Não-Filiado nunca recebe os dados que o *blur* esconde.
- RN-04.01.2 — Visitantes não autenticados veem a mesma prévia; a diferença é o CTA, que primeiro pede cadastro.
- RN-04.01.3 — O mesmo componente de prévia desfocada é reutilizado no conteúdo exclusivo, para manter a experiência consistente.

---

## 3. Histórias do Filiado

### US-04.02 — Navegar pelo clube e consultar um benefício

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver todos os benefícios disponíveis e os detalhes de cada um
> **Para** escolher qual vantagem da minha filiação aproveitar

**Critérios de aceite**

1. **Dado** que sou Filiado ativo, **quando** acesso "Clube de benefícios", **então** vejo os benefícios publicados e dentro da vigência em *cards* com imagem, título, legenda, parceiro e categoria.
2. **Dado** que filtro por categoria ou busco por texto (título, parceiro), **quando** aplico, **então** a listagem é filtrada.
3. **Dado** que abro um benefício, **quando** a página carrega, **então** vejo descrição completa, parceiro, categoria, período de vigência, tipo de resgate e o botão "Resgatar".
4. **Dado** que um benefício está a 7 dias ou menos do fim da vigência, **quando** o visualizo, **então** vejo destaque de urgência com a data limite.
5. **Dado** que já resgatei um benefício, **quando** o vejo na listagem, **então** o *card* exibe a marca "Resgatado".
6. **Dado** que não há benefícios vigentes, **quando** acesso o clube, **então** vejo uma mensagem explicativa em vez de uma página vazia.

**Regras de negócio**

- RN-04.02.1 — A listagem e o detalhe retornam apenas metadados; o código do cupom e a instrução **só** são entregues pelo ato de resgate (US-04.03).
- RN-04.02.2 — Benefício fora da vigência não aparece, mesmo com status `PUBLICADO`.
- RN-04.02.3 — Ordenação padrão: mais recentes primeiro.

---

### US-04.03 — Resgatar um benefício

**Atores:** Filiado

> **Como** Filiado
> **Quero** resgatar um benefício e receber o código ou a instrução de uso
> **Para** usar a vantagem junto ao parceiro

**Critérios de aceite**

1. **Dado** que sou Filiado ativo com e-mail verificado, **quando** clico em "Resgatar", **então** o código ou a instrução é exibido na tela, um registro de resgate é gravado e recebo o mesmo conteúdo por e-mail transacional.
2. **Dado** que o benefício é do tipo `CUPOM`, **quando** resgato, **então** vejo o código com botão "Copiar" e, se houver, o link do parceiro.
3. **Dado** que o benefício é do tipo `INSTRUCAO`, **quando** resgato, **então** vejo o texto com a orientação de como solicitar o benefício ao parceiro.
4. **Dado** que já resgatei aquele benefício, **quando** o abro novamente, **então** o botão exibe "Já resgatado" e mostra o código ou a instrução que recebi, sem criar novo registro.
5. **Dado** que não verifiquei meu e-mail, **quando** tento resgatar, **então** sou bloqueado com CTA para reenviar a verificação.
6. **Dado** que minha filiação deixou de ser reconhecida entre abrir a página e clicar em "Resgatar", **quando** a requisição chega ao backend, **então** o resgate é recusado com a mensagem de filiação não vigente.

**Regras de negócio**

- RN-04.03.1 — Somente filiação ativa (automática ou concedida manualmente) pode resgatar. Verificação no **backend**, no momento da requisição.
- RN-04.03.2 — O resgate grava um *snapshot* do status de filiação no momento da ação, como prova de elegibilidade para auditoria com o parceiro.
- RN-04.03.3 — O resgate é **idempotente** por (pessoa, benefício): um filiado resgata cada benefício uma única vez; repetir a ação devolve o mesmo resgate.
- RN-04.03.4 — Limite de 10 resgates por pessoa por hora, como proteção contra automação.
- RN-04.03.5 — E-mail verificado é pré-requisito: o código é enviado por e-mail e o parceiro pode contatar o filiado por ele.

---

### US-04.04 — Consultar meu histórico de resgates em "Meu perfil"

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver em "Meu perfil" tudo o que já resgatei
> **Para** recuperar um código que recebi e acompanhar o que aproveitei da minha filiação

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil", **quando** a página carrega, **então** vejo a aba "Meus resgates" ao lado das demais abas do perfil.
2. **Dado** que abro a aba "Meus resgates", **quando** ela carrega, **então** vejo benefício, parceiro, data do resgate e o código ou a instrução, do mais recente ao mais antigo.
3. **Dado** que o benefício de um resgate antigo foi arquivado pelo Admin, **quando** vejo meu histórico, **então** o registro continua lá, sinalizado como "benefício encerrado".
4. **Dado** que perdi a filiação, **quando** acesso a aba, **então** vejo o histórico (benefício, parceiro, data) **sem** os códigos e instruções, com aviso de que a renovação da filiação restabelece o acesso.
5. **Dado** que recupero a filiação, **quando** acesso a aba, **então** os códigos e instruções voltam a ser exibidos.
6. **Dado** que ainda não resgatei nada, **quando** abro a aba, **então** vejo uma mensagem com link para o clube de benefícios.

**Regras de negócio**

- RN-04.04.1 — O histórico de resgates fica **unicamente** na aba "Meus resgates" de "Meu perfil"; o clube de benefícios não exibe histórico, apenas a marca "Resgatado" nos *cards* (US-04.02).
- RN-04.04.2 — O registro de resgate é **imutável**: o Filiado não edita nem exclui; a exclusão da conta (ÉP-02, US-02.05) pseudonimiza o registro, mas não o apaga, por obrigação de prestação de contas com o parceiro.
- RN-04.04.3 — Se a pessoa perder a filiação, o histórico é preservado, mas ela deixa de ver os códigos e instruções.

---

## 4. Histórias do Admin

### US-04.05 — Cadastrar e publicar um benefício

**Atores:** Admin

> **Como** Admin
> **Quero** cadastrar os benefícios negociados com parceiros e publicá-los
> **Para** disponibilizá-los aos filiados

**Critérios de aceite**

1. **Dado** que crio um benefício, **quando** informo título, legenda, imagem, descrição completa, parceiro, categoria, tipo de resgate e vigência (início e fim), **então** ele é salvo como `RASCUNHO` e ainda não aparece para ninguém.
2. **Dado** que escolho o tipo `CUPOM`, **quando** salvo, **então** o campo "código do cupom" é obrigatório e o campo "link do parceiro" é opcional.
3. **Dado** que escolho o tipo `INSTRUCAO`, **quando** salvo, **então** o campo "instrução de resgate" (editor com formatação básica) é obrigatório.
4. **Dado** que publico um benefício, **quando** confirmo, **então** ele passa a aparecer imediatamente para os filiados (e desfocado para não-filiados), respeitando a vigência configurada.
5. **Dado** que a imagem enviada não é JPG/PNG/WebP ou excede 5 MB, **quando** submeto, **então** o upload é recusado com mensagem clara.
6. **Dado** que visualizo o rascunho, **quando** clico em "Pré-visualizar", **então** vejo o benefício exatamente como o Filiado o verá.

**Regras de negócio**

- RN-04.05.1 — Campos obrigatórios variam conforme o tipo de resgate; a validação condicional é feita no backend.
- RN-04.05.2 — Vigência de início não pode ser posterior à de fim; vigência de fim vazia significa "sem prazo".
- RN-04.05.3 — A publicação é registrada em auditoria com usuário, data e hora.

---

### US-04.06 — Editar, despublicar e arquivar um benefício

**Atores:** Admin

> **Como** Admin
> **Quero** manter o ciclo de vida de um benefício após a publicação
> **Para** corrigir informações, encerrar parcerias e manter o clube sempre atual

**Critérios de aceite**

1. **Dado** que edito um benefício publicado, **quando** salvo, **então** a alteração é imediata para os filiados e a versão anterior fica registrada na auditoria.
2. **Dado** que altero o tipo de resgate de um benefício que já tem resgates, **quando** tento salvar, **então** a alteração é bloqueada — o tipo é imutável após o primeiro resgate.
3. **Dado** que despublico um benefício (volta a `RASCUNHO`), **quando** confirmo, **então** ele some da listagem, mas os resgates já feitos continuam visíveis no histórico dos filiados.
4. **Dado** que arquivo um benefício, **quando** confirmo, **então** ele some da listagem, não pode mais ser publicado e o histórico de resgates permanece íntegro.
5. **Dado** que a vigência de um benefício termina, **quando** a rotina diária roda, **então** ele sai da listagem automaticamente, sem intervenção do Admin.

**Regras de negócio**

- RN-04.06.1 — **Exclusão física é proibida**; o encerramento é sempre por arquivamento, para preservar a integridade referencial com os resgates.
- RN-04.06.2 — Um benefício arquivado pode ser duplicado como novo rascunho (ex.: renovação de parceria com novo código).
- RN-04.06.3 — Alterar o código de um `CUPOM` já resgatado não altera o que consta no histórico dos filiados que já resgataram; eles continuam vendo o código que receberam.

---

### US-04.07 — Gerenciar e exportar todos os benefícios

**Atores:** Admin

> **Como** Admin
> **Quero** ver todos os benefícios cadastrados em um só lugar, com a quantidade de resgates de cada um, e exportá-los
> **Para** ter visão geral do clube e prestar contas aos parceiros e à diretoria

**Critérios de aceite**

1. **Dado** que acesso "Benefícios" no console, **quando** a lista carrega, **então** vejo todos os benefícios — de qualquer tipo (`CUPOM` ou `INSTRUCAO`) e status (`RASCUNHO`, `PUBLICADO`, `ARQUIVADO`) — com título, parceiro, categoria, tipo de resgate, vigência, status e **quantidade de resgates**, com paginação.
2. **Dado** que filtro por status, tipo de resgate, categoria, parceiro ou vigência, ou busco por texto, **quando** aplico, **então** a lista reflete todos os critérios simultaneamente.
3. **Dado** que ordeno por quantidade de resgates, **quando** aplico, **então** vejo os benefícios mais e menos resgatados no topo, conforme a direção escolhida.
4. **Dado** que exporto a lista, **quando** confirmo, **então** recebo um CSV respeitando os filtros aplicados, com uma linha por benefício contendo: título, legenda, parceiro, categoria, tipo de resgate, código ou instrução, link do parceiro, vigência (início e fim), status, data de publicação, **quantidade de resgates** e **quantidade de filiados distintos que resgataram**.
5. **Dado** que a exportação é gerada, **quando** ela conclui, **então** a ação fica registrada na auditoria com meu usuário, data, hora e filtros aplicados.
6. **Dado** que clico em um benefício na lista, **quando** a página abre, **então** acesso as ações de edição, despublicação e arquivamento (US-04.06).

**Regras de negócio**

- RN-04.07.1 — A quantidade de resgates é calculada a partir dos registros de resgate; nunca é digitada.
- RN-04.07.2 — A exportação de benefícios traz apenas dados do benefício e totais; **não** traz dados de quem resgatou — isso pertence ao relatório de resgates (US-04.08).
- RN-04.07.3 — Benefícios arquivados constam na exportação, para que a prestação de contas cubra parcerias já encerradas.

---

### US-04.08 — Acompanhar o relatório de resgates

**Atores:** Admin

> **Como** Admin
> **Quero** acompanhar os resgates por período e por benefício, e saber quem resgatou
> **Para** negociar renovações com parceiros usando dados reais e atender pedidos de conferência

**Critérios de aceite**

1. **Dado** que acesso "Relatório de resgates", **quando** filtro por período, benefício, parceiro ou categoria, **então** vejo total de resgates, filiados únicos e taxa de utilização sobre a base de filiados ativos.
2. **Dado** que abro um benefício no relatório, **quando** a página carrega, **então** vejo a evolução de resgates ao longo do tempo.
3. **Dado** que exporto o relatório, **quando** confirmo, **então** recebo um CSV com uma linha por resgate: benefício, parceiro, data, nome e e-mail do filiado e status de filiação no momento do resgate; a exportação é registrada em auditoria.
4. **Dado** que abro o detalhe de uma pessoa no console (ÉP-02, US-02.10), **quando** a página carrega, **então** vejo a lista dos resgates dela com benefício, data e status de filiação no momento do resgate.

**Regras de negócio**

- RN-04.08.1 — Exportação de dados de resgate identifica o filiado; é ação sensível, sempre registrada em auditoria.
- RN-04.08.2 — A taxa de utilização usa como denominador o número de filiados ativos no último dia do período filtrado.

---

## 5. Decisões e questões em aberto

### Decisões

- **Prévia desfocada para o Não-Filiado.** O refinamento diz que o clube é "apenas para o filiado"; adotamos a prévia com *blur* como mecanismo de conversão, com os dados omitidos no backend. Se a diretoria preferir ocultar o clube por completo, basta remover US-04.01 — nenhuma outra história depende dela.
- **Dois tipos de resgate**: cupom (código igual para todos) e instrução em texto. Cupons nominais/únicos por filiado, com estoque de códigos, ficam fora por enquanto.
- **Um resgate por filiado por benefício.** Não há limite configurável nem resgates repetidos.
- **Histórico de resgates fica unicamente em "Meu perfil"**, em aba própria; o clube mostra só a marca "Resgatado".
- **Gestão de benefícios é uma lista única** de todos os tipos e status, com quantidade de resgates e exportação; o relatório de resgates (US-04.08) é onde se vê quem resgatou.
- **Fora do escopo:** lojinha de resgate de prêmios, benefício como recompensa de gamificação, avaliação/comentário/compartilhamento de benefício, integração com o sistema do parceiro para confirmar o uso do cupom.

### Questões em aberto

1. Algum parceiro exigirá **cupons nominais** (um código diferente por filiado)? Se sim, será preciso reintroduzir o estoque de códigos com importação e controle de esgotamento.
2. Confirmar com a diretoria a decisão de **prévia desfocada × ocultação total** para o Não-Filiado (US-04.01).
3. Quais **categorias** iniciais de benefício? (ex.: Educação, Livros, Saúde, Tecnologia, Eventos). Impacta o filtro de US-04.02 e de US-04.07.
4. Um filiado que perdeu a filiação e a **recuperou** deve poder resgatar novamente um benefício que já resgatou antes? Hoje a RN-04.03.3 diz que não.
5. Há necessidade de o parceiro **confirmar o uso** do cupom (fechamento do ciclo), ou o registro de resgate é suficiente para a prestação de contas?

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-04 (Clube de Benefícios) para Não-Filiado, Filiado e Admin | Vitor Leonardo | Anne de Capdeville |
| 1.1 | 11/09/2026 | Atores movidos para perfil-usuario.md; referências ao ÉP-02 atualizadas após renumeração | Vitor Leonardo | Anne de Capdeville |
| 1.2 | 11/09/2026 | Prévia desfocada (blur) para Não-Filiado; histórico de resgates unicamente em "Meu perfil"; US-04.07 passa a ser gestão e exportação de todos os benefícios com quantidade de resgates; tipos de resgate reduzidos a cupom e instrução; remoção de origem/dependências e da tabela de rastreabilidade | Vitor Leonardo | Anne de Capdeville |
