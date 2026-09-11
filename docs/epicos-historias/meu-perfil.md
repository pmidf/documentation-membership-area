# ÉP-02 — Meu Perfil

## 1. Visão geral do épico

**Objetivo:** transformar o cadastro básico (ÉP-01) em uma identidade dentro da comunidade PMI-DF, capturando as informações e preferências que alimentam os demais módulos — agenda e trilha de participação, vitrine de oportunidades, gamificação e comunicação.

**Valor:** sem um perfil confiável, a plataforma não consegue reconhecer participações em eventos (e-mails do Sympla), não consegue diferenciar o que cada pessoa vê (Filiado × Não-Filiado) e não tem base legal para se comunicar com o membro (consentimentos).

Os atores estão definidos em [Perfis de Usuário](../planejamento/perfil-usuario.md).

### 1.1 Matriz de permissões do épico

Legenda: **L** = Ler · **E** = Escrever · **—** = Sem acesso

| Recurso | Não-Filiado | Filiado | Admin |
|---------|:-----------:|:-------:|:-----:|
| Próprios dados pessoais | E | E | E |
| Próprios campos sincronizados do PMI (nome, PMI ID) | — | L | L |
| Própria foto e mini bio | E | E | E |
| Próprios e-mails alternativos (Sympla) | E | E | E |
| Próprias preferências (gamificação, e-mail marketing) | E | E | E |
| Excluir a própria conta | E | E | E |
| Exibir-me em "Profissionais da comunidade" | — | E | — |
| Lista completa de pessoas, busca, filtros e exportação | — | — | E |
| Perfil de terceiros | — | — | E |
| Conceder / revogar filiação manual | — | — | E |
| Marcar pessoa como voluntária | — | — | E |
| Suspender / reativar conta | — | — | E |

### 1.2 Mapa das histórias

| ID | História | Atores | Prioridade |
|----|----------|--------|------------|
| [US-02.01](#us-0201-visualizar-e-editar-meus-dados-pessoais) | Visualizar e editar meus dados pessoais | Filiado, Não-Filiado | Must |
| [US-02.02](#us-0202-adicionar-foto-de-perfil-e-mini-bio) | Adicionar foto de perfil e mini bio | Filiado, Não-Filiado | Should |
| [US-02.03](#us-0203-cadastrar-e-verificar-meus-e-mails-alternativos-do-sympla) | Cadastrar e verificar meus e-mails alternativos do Sympla | Filiado, Não-Filiado | Must |
| [US-02.04](#us-0204-definir-minhas-preferencias-de-gamificacao-e-e-mail-marketing) | Definir minhas preferências de gamificação e e-mail marketing | Filiado, Não-Filiado | Must |
| [US-02.05](#us-0205-excluir-minha-conta) | Excluir minha conta | Filiado, Não-Filiado | Must |
| [US-02.06](#us-0206-consultar-meu-status-de-filiacao-e-iniciar-a-validacao) | Consultar meu status de filiação e iniciar a validação | Não-Filiado | Must |
| [US-02.07](#us-0207-visualizar-meus-dados-de-filiacao-sincronizados-do-pmi) | Visualizar meus dados de filiação sincronizados do PMI | Filiado | Must |
| [US-02.08](#us-0208-exibir-meu-perfil-em-profissionais-da-comunidade) | Exibir meu perfil em "Profissionais da comunidade" | Filiado | Should |
| [US-02.09](#us-0209-listar-buscar-filtrar-e-exportar-pessoas) | Listar, buscar, filtrar e exportar pessoas | Admin | Must |
| [US-02.10](#us-0210-consultar-e-editar-o-perfil-de-uma-pessoa) | Consultar e editar o perfil de uma pessoa | Admin | Should |
| [US-02.11](#us-0211-conceder-ou-revogar-filiacao-manualmente) | Conceder ou revogar filiação manualmente | Admin | Must |
| [US-02.12](#us-0212-marcar-uma-pessoa-como-voluntaria) | Marcar uma pessoa como voluntária | Admin | Should |

**Convenções:** prioridade em MoSCoW (`Must` · `Should` · `Could` · `Won't`); critérios de aceite em **Dado / Quando / Então**; regras de negócio numeradas por história (RN-02.XX.N).

---

## 2. Histórias do Filiado e do Não-Filiado

### US-02.01 — Visualizar e editar meus dados pessoais

**Atores:** Filiado, Não-Filiado

> **Como** usuário cadastrado
> **Quero** visualizar e editar meus dados pessoais em "Meu perfil"
> **Para** manter meu cadastro correto e atualizado

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil", **quando** a página carrega, **então** vejo meu status de filiação e os dados: nome, e-mail principal, telefone, endereço completo (CEP, logradouro, número, complemento, bairro, cidade e UF), escolaridade, data de nascimento, gênero, empresa onde trabalho, cargo e link do LinkedIn.
2. **Dado** que altero telefone, endereço, escolaridade, data de nascimento, gênero, empresa, cargo ou LinkedIn e salvo, **quando** a operação conclui, **então** os dados são persistidos e recebo confirmação visual.
3. **Dado** que altero o e-mail principal, **quando** salvo, **então** o novo endereço entra como *pendente* e só se torna efetivo após verificação; o e-mail antigo recebe aviso da tentativa de troca.
4. **Dado** que deixo qualquer campo obrigatório em branco, **quando** salvo, **então** vejo a indicação do campo e nada é persistido.
5. **Dado** que informo dado inválido (telefone fora do padrão, CEP inexistente, data futura, URL que não é do LinkedIn), **quando** salvo, **então** vejo a mensagem de erro junto ao campo e nada é persistido.
6. **Dado** que informo o CEP, **quando** saio do campo, **então** logradouro, bairro, cidade e UF são preenchidos automaticamente, permanecendo editáveis.
7. **Dado** que respondi (ou pulei) o onboarding, **quando** acesso "Meu perfil", **então** consigo rever e editar interesse e momento de carreira.

**Regras de negócio**

- RN-02.01.1 — Todos os dados pessoais listados no critério 1 são **obrigatórios**, exceto o link do LinkedIn, que é opcional e deve apontar para `linkedin.com/in/`.
- RN-02.01.2 — Toda alteração de dados pessoais gera registro de auditoria.
- RN-02.01.3 — Para o Filiado, `nome` e `PMI ID` são somente leitura (ver US-02.07).
- RN-02.01.4 — O e-mail principal é a chave de login e pode ser alterado; a troca só se efetiva após verificação do novo endereço.

---

### US-02.02 — Adicionar foto de perfil e mini bio

**Atores:** Filiado, Não-Filiado

> **Como** usuário cadastrado
> **Quero** adicionar uma foto e escrever uma mini bio
> **Para** ser reconhecido pelos demais membros do capítulo

**Critérios de aceite**

1. **Dado** que seleciono uma imagem JPG, PNG ou WebP de até 5 MB, **quando** confirmo o recorte quadrado, **então** a foto é salva e passa a ser exibida no meu perfil.
2. **Dado** que envio arquivo com tipo não permitido ou acima do limite, **quando** submeto, **então** o upload é recusado com mensagem clara sobre o motivo.
3. **Dado** que escrevo uma mini bio de até 600 caracteres, **quando** salvo, **então** o texto é armazenado sanitizado (sem HTML executável) e exibido com contador de caracteres durante a edição.
4. **Dado** que removo minha foto, **quando** confirmo, **então** volta o avatar padrão com minhas iniciais.
5. **Dado** que não tenho foto, **quando** meu perfil aparece em qualquer lugar da plataforma, **então** o avatar padrão com iniciais é exibido.

**Regras de negócio**

- RN-02.02.1 — Metadados EXIF são removidos da imagem (podem conter geolocalização).
- RN-02.02.2 — A validação do tipo de arquivo ocorre pelo conteúdo (*magic bytes*), não apenas pela extensão.

---

### US-02.03 — Cadastrar e verificar meus e-mails alternativos do Sympla

**Atores:** Filiado, Não-Filiado

> **Como** usuário que se inscreve em eventos com outro e-mail
> **Quero** cadastrar e comprovar meus e-mails alternativos
> **Para** que minha participação em eventos seja reconhecida no meu histórico

**Critérios de aceite**

1. **Dado** que acesso "Meus e-mails", **quando** a tela carrega, **então** vejo meu e-mail principal (editável em US-02.01) e a lista de e-mails alternativos com o status de cada um.
2. **Dado** que informo um e-mail alternativo, **quando** salvo, **então** ele fica com status "aguardando verificação" e um e-mail de confirmação é enviado ao endereço informado.
3. **Dado** que clico no link de confirmação com token válido, **quando** a verificação conclui, **então** o e-mail é marcado como verificado e as participações históricas com aquele endereço são vinculadas retroativamente ao meu perfil.
4. **Dado** que informo um e-mail já verificado por outra pessoa, **quando** salvo, **então** a operação é recusada com orientação para contatar o suporte.
5. **Dado** que tenho um e-mail pendente, **quando** clico em "Reenviar verificação", **então** um novo link é enviado e o anterior é invalidado.
6. **Dado** que já tenho 5 e-mails alternativos, **quando** tento adicionar outro, **então** a ação é bloqueada com a informação do limite.
7. **Dado** que removo um e-mail alternativo, **quando** confirmo, **então** as participações associadas voltam ao estado não vinculado e minha pontuação de engajamento é recalculada.

**Regras de negócio**

- RN-02.03.1 — **E-mail não verificado nunca gera vínculo de participação nem pontuação.**
- RN-02.03.2 — Máximo de **5 e-mails alternativos** por pessoa, além do e-mail principal.
- RN-02.03.3 — Um e-mail verificado é exclusivo de uma pessoa em toda a base.
- RN-02.03.4 — O e-mail principal também é considerado na vinculação de participações.

---

### US-02.04 — Definir minhas preferências de gamificação e e-mail marketing

**Atores:** Filiado, Não-Filiado

> **Como** usuário cadastrado
> **Quero** escolher se participo da gamificação e se recebo e-mail marketing
> **Para** controlar minha exposição e o volume de mensagens que recebo

**Critérios de aceite**

1. **Dado** que estou em "Preferências", **quando** ativo ou desativo cada opção, **então** a escolha é salva com data e hora e registrada como consentimento.
2. **Dado** que desativo a gamificação, **quando** salvo, **então** deixo de conquistar badges e peças de quebra-cabeça, embora as conquistas já obtidas permaneçam no meu perfil (ÉP-08).
3. **Dado** que desativo e-mail marketing, **quando** salvo, **então** deixo de receber comunicações promocionais, mas continuo recebendo e-mails transacionais (segurança, filiação, resgates).
4. **Dado** que recebo um e-mail marketing, **quando** clico em "descadastrar" no rodapé, **então** a preferência é desativada em um clique, sem exigir login.

**Regras de negócio**

- RN-02.04.1 — Ambas as opções são *opt-in* (padrão desativado).
- RN-02.04.2 — Distinção obrigatória entre e-mail transacional (legítimo interesse) e marketing (consentimento).
- RN-02.04.3 — Cada alteração insere um novo registro de consentimento; o histórico é imutável.

---

### US-02.05 — Excluir minha conta

**Atores:** Filiado, Não-Filiado

> **Como** usuário cadastrado, titular dos meus dados
> **Quero** excluir minha conta da plataforma
> **Para** exercer meu direito previsto na LGPD

**Critérios de aceite**

1. **Dado** que acesso "Meu perfil", **quando** rolo até o final da página, **então** encontro a opção "Excluir minha conta" de forma discreta, fora das ações principais.
2. **Dado** que clico em "Excluir minha conta", **quando** a confirmação abre, **então** vejo o que será anonimizado, o que será retido por obrigação legal e o prazo de arrependimento.
3. **Dado** que confirmo com senha ou reautenticação, **quando** a solicitação é registrada, **então** recebo um e-mail de confirmação e a exclusão só é executada após esse segundo aceite.
4. **Dado** que confirmei por e-mail, **quando** o prazo de arrependimento de 7 dias termina, **então** minha conta é anonimizada e recebo a confirmação final.
5. **Dado** que me arrependo dentro do prazo, **quando** faço login e cancelo a solicitação, **então** a conta permanece ativa sem qualquer alteração.

**Regras de negócio**

- RN-02.05.1 — Exclusão é **anonimização**, não remoção física: dados pessoais substituídos por valores irreversíveis, preservando integridade referencial.
- RN-02.05.2 — Registros com obrigação legal de retenção (resgates de benefícios) são preservados de forma pseudonimizada.
- RN-02.05.3 — Prazo legal de atendimento: 15 dias.

---

## 3. Histórias do Não-Filiado

### US-02.06 — Consultar meu status de filiação e iniciar a validação

**Atores:** Não-Filiado

> **Como** Não-Filiado
> **Quero** ver claramente no meu perfil que ainda não sou filiado e o que preciso fazer para ser reconhecido como tal
> **Para** desbloquear o conteúdo exclusivo se eu já for, ou entender o valor de me filiar se ainda não for

**Critérios de aceite**

1. **Dado** que não tenho filiação validada, **quando** acesso "Meu perfil", **então** vejo o status "Não filiado" com dois caminhos: "Já sou filiado — informar meu PMI ID" e "Quero me filiar".
2. **Dado** que escolho "Já sou filiado", **quando** informo meu PMI ID, **então** sou conduzido ao fluxo de validação de filiação do ÉP-01 e, ao retornar, o status no perfil reflete o resultado (`ATIVO`, `PENDENTE_VALIDACAO` ou `NAO_FILIADO`).
3. **Dado** que meu status é `PENDENTE_VALIDACAO`, **quando** acesso o perfil, **então** vejo a data da solicitação e a informação de que a sincronização com a base do PMI pode levar alguns dias.
4. **Dado** que minha filiação deixou de ser reconhecida, **quando** acesso o perfil, **então** vejo o aviso de que o acesso exclusivo foi suspenso e o link para renovar no site do PMI.
5. **Dado** que escolho "Quero me filiar", **quando** clico, **então** sou direcionado à jornada de conversão sem sair da plataforma.

**Regras de negócio**

- RN-02.06.1 — O perfil apenas **exibe** o status e encaminha; a validação em si (consulta à base do PMI, código de confirmação, análise manual) pertence ao ÉP-01.
- RN-02.06.2 — A mensagem para PMI ID não encontrado nunca afirma que a pessoa "não é filiada"; informa apenas que o vínculo ainda não foi confirmado.

---

## 4. Histórias do Filiado

### US-02.07 — Visualizar meus dados de filiação sincronizados do PMI

**Atores:** Filiado

> **Como** Filiado
> **Quero** ver no meu perfil que minha filiação está reconhecida
> **Para** confirmar que tenho acesso ao que a filiação me dá direito

**Critérios de aceite**

1. **Dado** que minha filiação está ativa, **quando** acesso "Meu perfil", **então** vejo o selo "Filiado PMI-DF", meu PMI ID e a data da última sincronização com a base do PMI.
2. **Dado** que sou Filiado, **quando** tento editar `nome` ou `PMI ID`, **então** os campos aparecem em modo leitura com a nota "Dado sincronizado da base do PMI — para corrigir, atualize seu cadastro no PMI Global".
3. **Dado** que minha filiação foi concedida manualmente pelo Admin, **quando** acesso o perfil, **então** o selo é exibido da mesma forma que para qualquer outro filiado.
4. **Dado** que a sincronização deixa de reconhecer minha filiação, **quando** acesso a plataforma, **então** meu perfil volta a exibir o estado descrito em US-02.06 (critério 4) e recebo e-mail transacional informando a mudança.

**Regras de negócio**

- RN-02.07.1 — Campos originados da base do PMI são somente leitura na aplicação, para evitar divergência com a fonte de verdade.
- RN-02.07.2 — Por enquanto a plataforma informa apenas **se** a pessoa é filiada; não exibe datas de início ou expiração da filiação.
- RN-02.07.3 — A renovação acontece no PMI Global; a plataforma apenas reflete o resultado na próxima sincronização.

---

### US-02.08 — Exibir meu perfil em "Profissionais da comunidade"

**Atores:** Filiado

> **Como** Filiado
> **Quero** escolher se meu perfil profissional aparece na vitrine "Profissionais da comunidade"
> **Para** ganhar visibilidade na comunidade do capítulo sem expor meus dados pessoais

**Critérios de aceite**

1. **Dado** que sou Filiado, **quando** acesso "Preferências" em "Meu perfil", **então** vejo a opção "Exibir meu perfil em Profissionais da comunidade", desativada por padrão.
2. **Dado** que ativo a opção, **quando** salvo, **então** vejo exatamente quais dados serão exibidos — nome, foto, cargo, empresa, cidade/UF, mini bio e link do LinkedIn — e a escolha é registrada como consentimento com data e hora.
3. **Dado** que ativei a opção, **quando** qualquer usuário da plataforma (Filiado ou Não-Filiado) acessa a vitrine (ÉP-07), **então** meu perfil profissional aparece lá.
4. **Dado** que desativo a opção, **quando** salvo, **então** meu perfil sai da vitrine imediatamente.
5. **Dado** que minha filiação deixou de ser reconhecida, **quando** a sincronização processa, **então** meu perfil sai da vitrine automaticamente e a opção fica desabilitada até a filiação voltar, preservando minha escolha.
6. **Dado** que sou Não-Filiado, **quando** acesso "Preferências", **então** a opção aparece desabilitada com a nota "Disponível para filiados".

**Regras de negócio**

- RN-02.08.1 — *Opt-in*: padrão desativado; ninguém aparece na vitrine sem escolher.
- RN-02.08.2 — Apenas dados **profissionais** são exibidos: nome, foto, cargo, empresa, cidade/UF, mini bio e LinkedIn. E-mail, telefone, endereço, data de nascimento, gênero e escolaridade **nunca** aparecem.
- RN-02.08.3 — Somente Filiados com filiação ativa **aparecem** na vitrine; a perda da filiação remove o perfil sem apagar a preferência. A vitrine é **visível** a todos os usuários da plataforma.
- RN-02.08.4 — Cada ativação e desativação insere um novo registro de consentimento; o histórico é imutável.

---

## 5. Histórias do Admin

### US-02.09 — Listar, buscar, filtrar e exportar pessoas

**Atores:** Admin

> **Como** Admin
> **Quero** consultar toda a base de pessoas com busca e filtros
> **Para** operar o cadastro do capítulo e apoiar decisões da diretoria

**Critérios de aceite**

1. **Dado** que acesso "Pessoas" no console, **quando** a lista carrega, **então** vejo nome, e-mail principal, status de filiação, voluntário (sim/não), situação da conta, data de cadastro e último acesso, com paginação.
2. **Dado** que busco por nome, e-mail ou PMI ID, **quando** digito ao menos 3 caracteres, **então** os resultados são filtrados sem recarregar a página.
3. **Dado** que combino filtros (status de filiação, voluntário, período de cadastro, situação da conta), **quando** aplico, **então** a lista reflete todos os critérios simultaneamente.
4. **Dado** que exporto o resultado, **quando** confirmo, **então** recebo um CSV respeitando os filtros aplicados, contendo **todas as informações cadastradas** por cada pessoa: nome, e-mail principal, e-mails alternativos, telefone, endereço completo, escolaridade, data de nascimento, gênero, empresa, cargo, LinkedIn, PMI ID, status de filiação, marcação de voluntário, exibição em "Profissionais da comunidade" e situação da conta.
5. **Dado** que a exportação é gerada, **quando** ela conclui, **então** a ação fica registrada na auditoria com meu usuário, data, hora, filtros aplicados e total de registros.

**Regras de negócio**

- RN-02.09.1 — Exportação de base pessoal é ação sensível: sempre registrada em auditoria.

---

### US-02.10 — Consultar e editar o perfil de uma pessoa

**Atores:** Admin

> **Como** Admin
> **Quero** abrir o detalhe de qualquer pessoa, corrigir dados e suspender ou reativar a conta
> **Para** resolver problemas de cadastro e proteger a base quando necessário

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa, **quando** a página carrega, **então** vejo todos os dados do perfil, o histórico de consentimentos, os e-mails alternativos e seus status, a marcação de voluntário, o status de filiação e se o perfil está exibido em "Profissionais da comunidade".
2. **Dado** que corrijo um dado pessoal e salvo, **quando** a operação conclui, **então** a alteração é persistida e registrada na auditoria com meu usuário, data e hora.
3. **Dado** que a pessoa é Filiada, **quando** visualizo `nome` e `PMI ID`, **então** os campos aparecem como somente leitura, sinalizados como originados da base do PMI.
4. **Dado** que suspendo uma conta, **quando** informo o motivo e confirmo, **então** a pessoa perde o acesso imediatamente, o histórico é preservado e ela recebe e-mail transacional com o motivo.
5. **Dado** que reativo uma conta suspensa, **quando** confirmo, **então** o acesso é restabelecido com o mesmo status de filiação anterior.

**Regras de negócio**

- RN-02.10.1 — O Admin **não** edita consentimentos nem preferências de outra pessoa; pode apenas consultá-los.
- RN-02.10.2 — Suspensão exige motivo obrigatório e é reversível; não se confunde com exclusão (US-02.05).
- RN-02.10.3 — Toda ação sobre o perfil de terceiros gera registro de auditoria.

---

### US-02.11 — Conceder ou revogar filiação manualmente

**Atores:** Admin

> **Como** Admin
> **Quero** marcar ou desmarcar manualmente uma pessoa como filiada
> **Para** atender casos em que a base do PMI está desatualizada ou o vínculo com o capítulo ainda não foi processado

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa não filiada, **quando** clico em "Conceder filiação" e confirmo, **então** o status vira `CONCEDIDO_MANUALMENTE`, o acesso exclusivo é liberado imediatamente e a ação fica registrada com meu usuário, data e hora.
2. **Dado** que abro o detalhe de uma pessoa com filiação concedida manualmente, **quando** clico em "Revogar filiação" e confirmo, **então** a pessoa volta ao status determinado pela última sincronização com a base do PMI.
3. **Dado** que a pessoa enviou comprovante de filiação (caminho "análise manual" do ÉP-01), **quando** abro o detalhe, **então** vejo o documento anexado junto ao botão de concessão.
4. **Dado** que a pessoa tem filiação reconhecida automaticamente pela base do PMI, **quando** abro o detalhe, **então** o botão "Conceder filiação" não é exibido — a filiação já está ativa.

**Regras de negócio**

- RN-02.11.1 — Concessão manual **não** é sobrescrita pela sincronização automática; só o Admin a remove.
- RN-02.11.2 — Concessão e revogação são sempre registradas em auditoria.

---

### US-02.12 — Marcar uma pessoa como voluntária

**Atores:** Admin

> **Como** Admin
> **Quero** marcar se uma pessoa é ou não voluntária do capítulo
> **Para** identificar quem atua no PMI-DF

**Critérios de aceite**

1. **Dado** que abro o detalhe de uma pessoa, **quando** ativo a marcação "Voluntário(a)" e confirmo, **então** a pessoa passa a constar como voluntária e a ação fica registrada na auditoria.
2. **Dado** que a pessoa está marcada como voluntária, **quando** desativo a marcação e confirmo, **então** ela deixa de constar como voluntária.
3. **Dado** que a marcação foi alterada, **quando** consulto a lista de pessoas (US-02.09), **então** a coluna e o filtro "voluntário" refletem o novo valor.

**Regras de negócio**

- RN-02.12.1 — Ser voluntário é **independente** de ser filiado.
- RN-02.12.2 — A marcação não altera o que a pessoa pode acessar na plataforma.

---

## 6. Decisões e questões em aberto

### Decisões

- **Todos os dados pessoais são obrigatórios** (nome, e-mail, telefone, endereço completo, escolaridade, data de nascimento, gênero, empresa e cargo).
- **Aprovação manual de cadastro** não existe neste épico: qualquer pessoa cria conta e entra como Não-Filiado; o Admin intervém apenas para conceder filiação ou marcar voluntário.
- **Filiação é binária por enquanto**: a plataforma informa se a pessoa é ou não filiada, sem datas de início ou expiração.
- **Voluntário é uma marcação simples** (sim/não), sem cargo, diretoria ou histórico de vínculos.
- **Sem exportação de dados pelo próprio usuário**: o perfil oferece apenas a exclusão da conta.
- **Sem sinal "aberto a oportunidades".** A única exposição do perfil a terceiros é a vitrine "Profissionais da comunidade" (ÉP-07): *opt-in*, visível a todos os usuários, onde só Filiados aparecem, limitada a dados profissionais (US-02.08).
- **Vitrine de badges no perfil** permanece no épico de gamificação.

### Questões em aberto

1. Os dados obrigatórios (endereço, empresa, cargo etc.) são exigidos já no cadastro (ÉP-01) ou a conta pode ser criada só com nome/e-mail/senha e o preenchimento completado depois em "Meu perfil"? Se for a segunda opção, o que fica bloqueado enquanto o perfil está incompleto?
2. A troca de e-mail principal deve ser bloqueada para Filiados enquanto o e-mail for a chave de reconciliação com a base do PMI?
3. A sincronização com o ThoughtSpot traz data de expiração da filiação? Se trouxer, a RN-02.07.2 pode ser revista para exibir vigência e alertas.
4. O Admin pode alterar o e-mail principal de uma pessoa (US-02.10)? Se sim, exige verificação do novo endereço como em US-02.01?

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — histórias do ÉP-02 (Meu Perfil) para Não-Filiado, Filiado e Admin | Vitor Leonardo | Nome do revisor |
| 1.1 | 11/09/2026 | Atores movidos para perfil-usuario.md; remoção de origem/dependências; dados pessoais obrigatórios com endereço, empresa e cargo; limite de 5 e-mails; exclusão de conta sem exportação; filiação binária; voluntário como marcação simples; remoção de US-02.03 e US-02.06 e renumeração | Vitor Leonardo | Nome do revisor |
| 1.2 | 11/09/2026 | Link do LinkedIn (opcional) em US-02.01; nova US-02.08 (exibir perfil em "Profissionais da comunidade", opt-in de Filiado) e renumeração das histórias de Admin para US-02.09 a US-02.12 | Vitor Leonardo | Nome do revisor |
| 1.3 | 11/09/2026 | US-02.08: vitrine de profissionais passa a ser visível a todos os usuários (só Filiados aparecem) | Vitor Leonardo | Nome do revisor |
| 1.4 | 11/09/2026 | US-02.04: critério de desativação da gamificação alinhado ao ÉP-08 (sem ranking) | Vitor Leonardo | Nome do revisor |
