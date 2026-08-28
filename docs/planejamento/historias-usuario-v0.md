# Histórias de Usuário (Backlog)

# HISTÓRIAS DE USUÁRIO

## Pessoa

### 01 · Criar conta e verificar e-mail

> Como **Pessoa**, quero criar conta com e-mail ou login social, para acessar o portal sem ser filiada.

1. Cadastro pede apenas nome, e-mail e senha; nenhum campo de PMI ID, matrícula ou voluntariado aparece aqui.

2. Login com Google ou LinkedIn dispensa a verificação de e-mail e permite vincular a uma conta local existente após confirmação por senha.

3. Link de verificação expira em 24 horas, é de uso único e pode ser reenviado a cada 2 minutos.

4. Conta não verificada não acessa o portal.

5. E-mail já cadastrado exibe caminho de recuperação sem confirmar por outra via que a conta existe.

---

### 02 · Responder o onboarding

> Como **Pessoa**, quero informar interesse e momento de carreira, para receber um painel inicial coerente com o que busco.

1. Três passos: interesse, momento de carreira e "já é filiado ao PMI?".

2. Todo passo tem "Pular por agora" e o abandono não bloqueia o acesso.

3. Responder "Sim, tenho PMI ID" encaminha à história 07.

4. As respostas alimentam o painel da história 05 e ficam editáveis no perfil.

5. Concluído ou pulado, o onboarding não reaparece, mas segue acessível pelo perfil.

---

### 03 · Controlar meus consentimentos e meus dados

> Como **Pessoa**, quero escolher item a item o que autorizo e poder sair, para exercer meus direitos de titular.

1. Quatro consentimentos independentes: transacional (obrigatório), newsletter, diretório de membros, WhatsApp.

2. Cada aceite e cada revogação grava finalidade, versão do texto, data, hora e IP.

3. Comunicação transacional (verificação, decisão sobre papel, expiração) é enviada por e-mail independentemente das preferências; divulgação respeita os consentimentos.

4. Envio por WhatsApp exige número verificado.

5. Exportação dos dados em JSON e pedido de exclusão com reautenticação, informando o que é anonimizado em vez de apagado.

---

### 04 · Validar minha filiação por PMI ID

> Como **Pessoa** filiada ao PMI, quero informar meu PMI ID, para receber o papel Filiado sem enviar documento.

1. A consulta ocorre contra a base local importada, não contra o PMI em tempo real.

2. **PMI ID e e-mail coincidentes:** papel concedido na hora, com validade igual à expiração registrada na base.

3. **PMI ID existe, e-mail diverge:** código de 6 dígitos enviado ao e-mail registrado no PMI Global — nunca ao digitado no portal — válido por 15 minutos e 3 tentativas.

4. **PMI ID não encontrado:** a mensagem informa que a sincronização leva até 5 dias úteis e não afirma que a pessoa não é filiada. Duas saídas: aguardar (reserva o PMI ID e concede automaticamente quando aparecer na importação) ou enviar comprovante para análise manual.

5. Um mesmo PMI ID não pode estar ativo em duas contas.

6. Tentativas limitadas a 5 por hora por conta.

---

### 05 · Consultar vagas e candidatar-se

> Como **Pessoa**, quero ver as vagas com carga horária e contrapartida e me candidatar, para ser avaliada pela diretoria.

1. Cada vaga exibe título, diretoria, descrição, competências, carga horária semanal, duração e o que o voluntário leva ao final.

2. O catálogo é visível a qualquer conta autenticada, independente de papel.

3. Candidatura pede disponibilidade real em horas por semana, competências, LinkedIn e motivação.

4. O envio notifica o diretor da área; não é possível ter duas candidaturas simultâneas para a mesma vaga.

5. Vagas encerradas saem do catálogo na data de encerramento.

---

### 06 · Consultar os números do capítulo `F2`

> Como **Pessoa**, mesmo sem login, quero ver o que o PMI-DF entregou no ano, para conhecer a atuação do capítulo.

**Critérios de aceite**

1. Página pública com filiados ativos, eventos realizados, participações, voluntários ativos, horas de voluntariado validadas, projetos entregues e PDUs distribuídos.

2. Todos os números são calculados a partir das bases do sistema; nenhum é digitado.

3. Cada indicador informa a data da última atualização.

4. A lista de eventos do ano é exibida com inscritos e participantes.

---

## Usuário

### 07 · Visualizar todos os eventos disponíveis

> Como **Usuário**, quero visualizar uma vitrine centralizada de todos os eventos e webinars do capítulo, para encontrar oportunidades de aprendizado e networking de forma rápida.

### Critérios de aceitação

1. A plataforma deve exibir uma aba ou calendário de eventos atualizados automaticamente a partir da integração com a API de eventos do Sympla.

2. Cada evento listado deve destacar título, data, palestrantes envolvidos e se ele concede isenção de taxas de emissão de certificado para membros.

3. O painel deve permitir que o estudante use filtros de busca específicos, separando:

   - Webinars

   - Eventos Regionais

   - Atividades do Student Club

---

### 08 · Visualizar histórico de eventos participados

> Como **Usuário**, quero visualizar a lista de eventos passados em que estive presente, para acompanhar meu progresso e acessar meus certificados sem custos.

### Critérios de aceitação

1. O estudante deve ter acesso a uma aba chamada **"Eventos Passados"** que exibe todo o histórico de eventos em que o check-in foi devidamente realizado.

2. O status de participação (presença) do estudante deve mudar para confirmado assim que a lista de presença for processada, via planilha importada ou sincronização via API.

3. Para cada evento com presença validada, o sistema deve exibir um botão para baixar o certificado gratuitamente, com isenção da taxa de emissão que é cobrada de não filiados.

---

## Estudante

### 09 · Ativar perfil de estudante e informar previsão de formatura

> Como **Estudante**, quero enviar meu comprovante de matrícula e minha data estimada de formatura, para que meu acesso estudantil seja validado e liberado na plataforma.

### Critérios de aceitação

1. O formulário exige que o estudante faça o **upload obrigatório do comprovante de matrícula** ativo de sua universidade e digite a **data de previsão de sua formatura**.

2. O sistema armazena a data de formatura informada e agenda uma rotina para **cortar o acesso de estudante automaticamente** assim que o prazo de formação for atingido.

3. Enquanto o perfil estudantil não é aprovado ou caso a data informada expire, as **funcionalidades e abas exclusivas da área do estudante na barra lateral permanecem bloqueadas** para o usuário.

4. O cadastro de estudante passa por uma etapa de **aprovação manual pela área de administração** no painel de controle antes de liberar os acessos.

---

## Voluntário

### 10 · Acessar o Portal da Transparência do capítulo

> Como **Voluntário**, quero visualizar os dados analíticos de participação no Portal da Transparência, para entender o impacto dos eventos que realizamos.

### Critérios de aceitação

1. O link de acesso ao painel do Portal da Transparência fica visível **única e exclusivamente** para usuários com perfil de voluntário ativo ou administrador homologados no banco de dados.

2. O portal exibe gráficos e dados consolidados com métricas de participações totais de membros no ano.

3. O sistema permite detalhar as participações de forma individualizada por cada evento específico realizado.

4. Para garantir a segurança, nenhuma informação confidencial ou dados de contato sensíveis dos participantes comuns é exibida no portal, apenas métricas e contagens quantitativas.

---

### 11 · Publicar avisos integrados com disparo de e-mail

> Como **Voluntário de comunicação**, quero publicar comunicados oficiais na plataforma, para que o aviso fique fixado no portal e seja disparado por e-mail para a comunidade.

### Critérios de aceitação

1. O voluntário de comunicação preenche um formulário de aviso contendo:

   - Título

   - Mensagem

   - Seleção do público-alvo: Estudantes, Filiados, Voluntários ou Geral

2. Ao publicar, o comunicado é exibido imediatamente na seção de avisos da área logada dos perfis selecionados.

3. De forma automática e em segundo plano, o sistema realiza o disparo integrado de uma cópia exata do aviso para o e-mail de todos os usuários que pertencem ao público-alvo.

4. O sistema impede novos disparos repetidos para o mesmo grupo em um intervalo inferior a **5 minutos**, para evitar spam.

---

### 12 · Cadastrar perfil profissional na Vitrine de Currículos

> Como **Voluntário**, quero preencher minhas competências na Vitrine de Currículos, para aumentar minha visibilidade de carreira perante os filiados seniores que têm poder de contratação.

### Critérios de aceitação

1. O voluntário tem acesso a uma tela para estruturar seu perfil técnico, podendo:

   - Fazer o upload de uma foto;

   - Escrever uma mini bio;

   - Inserir o link direto de seu LinkedIn.

2. Ele pode listar suas principais habilidades técnicas, certificações e marcar se está ativamente **"Disponível para Oportunidades"**.

3. Ao salvar, as informações são catalogadas na **"Vitrine de Currículos"** unificada do PMI DF.

4. A busca e a visualização detalhada desses currículos ficam restritas aos filiados profissionais e parceiros comerciais autorizados, protegendo a privacidade dos dados de acordo com a governança do capítulo.

---

## Administrador

### 13 · Aprovação de novos usuários

> Como **Administrador**, quero visualizar e gerenciar as solicitações de novos cadastros.

### Critérios de aceitação

1. O fluxo de aprovação é acionado assim que o usuário realiza a escolha de perfil e preenche o formulário específico de seu grupo (**"1 form pra cada"**).

2. O painel administrativo exibe uma fila de solicitações pendentes de validação contendo os dados inseridos pelo usuário.

3. O administrador pode aprovar ou rejeitar a solicitação de acesso de forma individual e manual.

4. O acesso às funcionalidades restritas e a liberação do perfil no banco de dados do capítulo ocorrem apenas após a **homologação manual** realizada pelo administrador.

---

### 14 · Consultar e editar a base de membros `MVP`

> Como **Administrador**, quero buscar, filtrar e exportar os registros, para operar a base e prestar contas.

**Critérios de aceite**

1. Tabela com nome, e-mail, papéis, situação e data de entrada, com paginação.

2. Busca por nome ou e-mail e filtros por papel e situação.

3. Exportação em CSV respeitando os filtros aplicados.

4. Campos originados da base do PMI Global são somente leitura e sinalizados como tal.

5. Suspender uma conta bloqueia o acesso e preserva o histórico.

---

### 15 · Consultar participantes de um evento `F2`

> Como **Administrador**, quero ver quem participou de cada evento, para conferir a base e apurar presença.

**Critérios de aceite**

1. Lista de participantes com nome, e-mail e vínculo com conta do portal, quando houver.

2. E-mail sem conta vinculada é sinalizado como não reconciliado.

3. Exportação em CSV.

4. Sincronização manual disponível além da rotina automática.

---

# Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 26/08/2026 | Criação do documento | Isabelle | Nome do revisor |
| 2.0 | 26/08/2026 | Agegação de novas historias | Isabelle | Nome do revisor |
| 2.0 | 26/08/2026 | Agegação de novas historias | Isabelle | Nome do revisor |
