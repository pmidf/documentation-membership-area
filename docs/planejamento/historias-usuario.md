# Histórias de Usuário (Backlog)

# HISTÓRIAS DE USUÁRIO

## 01 · Aprovação de novos usuários

**Como Administrador**, quero visualizar e gerenciar as solicitações de novos cadastros.

### Critérios de aceitação

1. O fluxo de aprovação é acionado assim que o usuário realiza a escolha de perfil e preenche o formulário específico de seu grupo (**"1 form pra cada"**).
2. O painel administrativo exibe uma fila de solicitações pendentes de validação contendo os dados inseridos pelo usuário.
3. O administrador pode aprovar ou rejeitar a solicitação de acesso de forma individual e manual.
4. O acesso às funcionalidades restritas e a liberação do perfil no banco de dados do capítulo ocorrem apenas após a **homologação manual** realizada pelo administrador.

---

## 02 · Ativar perfil de estudante e informar previsão de formatura

**Como Estudante**, quero enviar meu comprovante de matrícula e minha data estimada de formatura, para que meu acesso estudantil seja validado e liberado na plataforma.

### Critérios de aceitação

1. O formulário exige que o estudante faça o **upload obrigatório do comprovante de matrícula** ativo de sua universidade e digite a **data de previsão de sua formatura**.
2. O sistema armazena a data de formatura informada e agenda uma rotina para **cortar o acesso de estudante automaticamente** assim que o prazo de formação for atingido.
3. Enquanto o perfil estudantil não é aprovado ou caso a data informada expire, as **funcionalidades e abas exclusivas da área do estudante na barra lateral permanecem bloqueadas** para o usuário.
4. O cadastro de estudante passa por uma etapa de **aprovação manual pela área de administração** no painel de controle antes de liberar os acessos.

---

## 03 · Visualizar todos os eventos disponíveis

**Como Usuário**, quero visualizar uma vitrine centralizada de todos os eventos e webinars do capítulo, para encontrar oportunidades de aprendizado e networking de forma rápida.

### Critérios de aceitação

1. A plataforma deve exibir uma aba ou calendário de eventos atualizados automaticamente a partir da integração com a API de eventos do Sympla.
2. Cada evento listado deve destacar título, data, palestrantes envolvidos e se ele concede isenção de taxas de emissão de certificado para membros.
3. O painel deve permitir que o estudante use filtros de busca específicos, separando:
   - Webinars
   - Eventos Regionais
   - Atividades do Student Club

---

## 04 · Me inscrever em eventos do capítulo

**Como Usuário**, quero me inscrever nos eventos e webinars disponíveis diretamente pela plataforma, para garantir minha participação sem complicação de dados.

### Critérios de aceitação

1. Ao clicar em se inscrever, o sistema deve utilizar de forma automática as informações básicas de cadastro do estudante (nome e e-mail principal).
2. O e-mail utilizado na inscrição do evento deve coincidir obrigatoriamente com o e-mail de login da Área de Membros para que a futura validação de presença funcione.
3. O estudante deve receber uma notificação de confirmação da inscrição na plataforma e um aviso complementar por e-mail.

---

## 05 · Visualizar histórico de eventos participados

**Como Usuário**, quero visualizar a lista de eventos passados em que estive presente, para acompanhar meu progresso e acessar meus certificados sem custos.

### Critérios de aceitação

1. O estudante deve ter acesso a uma aba chamada **"Eventos Passados"** que exibe todo o histórico de eventos em que o check-in foi devidamente realizado.
2. O status de participação (presença) do estudante deve mudar para confirmado assim que a lista de presença for processada, via planilha importada ou sincronização via API.
3. Para cada evento com presença validada, o sistema deve exibir um botão para baixar o certificado gratuitamente, com isenção da taxa de emissão que é cobrada de não filiados.

---

## 06 · Gamificação

**Falta definir a história de usuário e os critérios de aceitação relacionados à gamificação.**

---

## 08 · Gerenciar e editar eventos do capítulo

**Como Voluntário organizador**, quero cadastrar, visualizar, editar e manejar as informações dos eventos sob minha responsabilidade, para manter a vitrine de eventos atualizada e sem erros para a comunidade.

### Critérios de aceitação

1. O voluntário visualiza uma lista restrita contendo os eventos em que ele foi definido como organizador ou criador.
2. O sistema fornece um formulário para cadastrar novos eventos informando:
   - Título
   - Data
   - Descrição
   - Link de inscrição do Sympla
   - Se haverá ou não emissão de certificado gratuito
3. O voluntário pode editar ou alterar dados de um evento (como alterar data, link ou palestrante) a qualquer momento antes de sua realização.
4. O voluntário pode cancelar ou desativar um evento, o que o remove imediatamente da vitrine pública dos estudantes/filiados e dispara um e-mail automático de aviso aos inscritos.

---

## 10 · Acessar o Portal da Transparência do capítulo

**Como Voluntário**, quero visualizar os dados analíticos de participação no Portal da Transparência, para entender o impacto dos eventos que realizamos.

### Critérios de aceitação

1. O link de acesso ao painel do Portal da Transparência fica visível **única e exclusivamente** para usuários com perfil de voluntário ativo ou administrador homologados no banco de dados.
2. O portal exibe gráficos e dados consolidados com métricas de participações totais de membros no ano.
3. O sistema permite detalhar as participações de forma individualizada por cada evento específico realizado.
4. Para garantir a segurança, nenhuma informação confidencial ou dados de contato sensíveis dos participantes comuns é exibida no portal, apenas métricas e contagens quantitativas.

---

## 12 · Publicar avisos integrados com disparo de e-mail

**Como Voluntário de comunicação**, quero publicar comunicados oficiais na plataforma, para que o aviso fique fixado no portal e seja disparado por e-mail para a comunidade.

### Critérios de aceitação

1. O voluntário de comunicação preenche um formulário de aviso contendo:
   - Título
   - Mensagem
   - Seleção do público-alvo: Estudantes, Filiados, Voluntários ou Geral
2. Ao publicar, o comunicado é exibido imediatamente na seção de avisos da área logada dos perfis selecionados.
3. De forma automática e em segundo plano, o sistema realiza o disparo integrado de uma cópia exata do aviso para o e-mail de todos os usuários que pertencem ao público-alvo.
4. O sistema impede novos disparos repetidos para o mesmo grupo em um intervalo inferior a **5 minutos**, para evitar spam.

---

## 13 · Cadastrar perfil profissional na Vitrine de Currículos

**Como Voluntário**, quero preencher minhas competências na Vitrine de Currículos, para aumentar minha visibilidade de carreira perante os filiados seniores que têm poder de contratação.

### Critérios de aceitação

1. O voluntário tem acesso a uma tela para estruturar seu perfil técnico, podendo:
   - Fazer o upload de uma foto;
   - Escrever uma mini bio;
   - Inserir o link direto de seu LinkedIn.
2. Ele pode listar suas principais habilidades técnicas, certificações e marcar se está ativamente **"Disponível para Oportunidades"**.
3. Ao salvar, as informações são catalogadas na **"Vitrine de Currículos"** unificada do PMI DF.
4. A busca e a visualização detalhada desses currículos ficam restritas aos filiados profissionais e parceiros comerciais autorizados, protegendo a privacidade dos dados de acordo com a governança do capítulo.

---


## Tabela de Contribuições

| Versão | Data       | Descrição              | Autor            | Revisor          |
|--------|------------|-------------------------|-------------------|-------------------|
| 1.0    | 26/08/2026 | Criação do documento    | Isabelle          | Nome do revisor   |
