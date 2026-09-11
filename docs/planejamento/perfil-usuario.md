# Perfis de Usuário

Este documento define os **três atores** que aparecem em todas as histórias de usuário da plataforma. Cada história indica quais deles atende; a descrição completa de cada um fica centralizada aqui e não é repetida nos épicos.

## 1. Modelo de atores

A plataforma trabalha com **um único tipo de conta** — a Pessoa cadastrada — e diferencia os atores por dois atributos independentes:

| Atributo | Valores | Quem define |
|----------|---------|-------------|
| **Filiação** | Não-Filiado · Filiado | Validação automática contra a base do PMI (ThoughtSpot) ou concessão manual pelo Admin |
| **Papel de acesso** | Usuário comum · Admin | Concessão manual por outro Admin |

Isso significa que:

- Todo usuário é, antes de tudo, uma Pessoa cadastrada.
- **Filiado** é um *estado* dessa pessoa — pode ser adquirido e perdido ao longo do tempo.
- **Admin** é um *papel* — um Admin pode ser filiado ou não, e continua tendo seu próprio perfil como qualquer usuário.
- **Voluntário** não é um ator: é uma **marcação** feita pelo Admin no perfil de uma pessoa (filiada ou não). Serve para identificar quem atua no capítulo e não altera o que a pessoa pode acessar.

Visitantes não autenticados não são tratados como ator nas histórias: enxergam apenas as páginas públicas (institucional, login, cadastro) e as prévias que a plataforma decidir expor.

---

## 2. Não-Filiado

### 2.1 Quem é

Pessoa que criou conta na plataforma e **não possui filiação ativa** ao PMI reconhecida pelo sistema. Inclui quem nunca foi filiado, quem já foi e a filiação expirou, e quem informou um PMI ID que ainda não foi confirmado.

### 2.2 Como se torna

- Criando conta com e-mail e senha ou com login Google (ÉP-01).
- Automaticamente, quando um Filiado tem a filiação expirada ou revogada.

### 2.3 O que acessa

| Área | Acesso |
|------|--------|
| Meu perfil | Completo: edita dados pessoais, foto, mini bio, e-mails alternativos, preferências; pode excluir a própria conta |
| Status de filiação | Vê que não é filiado e tem o caminho para informar o PMI ID ou para se filiar |
| Clube de benefícios | Apenas **prévia**: vê os benefícios, mas não o código ou a instrução de resgate |
| Agenda de eventos | Vê a agenda e o próprio histórico de participação |
| Conteúdo exclusivo | Apenas prévia (título, imagem, resumo) |
| Vitrine — Profissionais da comunidade | Vê os profissionais filiados que se apresentaram; não pode aparecer |
| Vitrine — Oportunidades | Apenas **prévia**: vê que há vagas, mas não o conteúdo |
| Gamificação | Não acumula badges nem peças de quebra-cabeça; vê o catálogo como prévia |
| Console administrativo | Sem acesso |

### 2.4 O que não pode

- Resgatar benefícios.
- Consumir conteúdo exclusivo (vídeos, links curados).
- Ver as oportunidades (vagas) ou aparecer em "Profissionais da comunidade".
- Conquistar badges ou peças de quebra-cabeça.
- Ver ou editar dados de outras pessoas.

### 2.5 Objetivo na plataforma

Conhecer o capítulo, acompanhar eventos e **perceber o valor da filiação** — a prévia bloqueada existe para converter o Não-Filiado em Filiado.

---

## 3. Filiado

### 3.1 Quem é

Pessoa cadastrada cuja **filiação ao PMI está ativa e reconhecida** pela plataforma. É o público principal do produto: quem paga a anuidade e para quem os benefícios, o conteúdo exclusivo e o reconhecimento são desenhados.

### 3.2 Como se torna

- **Automaticamente**, quando o PMI ID informado é confirmado na base do PMI sincronizada com a plataforma (ThoughtSpot).
- **Manualmente**, por concessão do Admin, para casos em que a base do PMI está desatualizada ou o vínculo com o capítulo ainda não foi processado.

### 3.3 Como deixa de ser

- A sincronização com a base do PMI deixa de reconhecer a filiação (expiração).
- O Admin revoga uma concessão manual.

Em ambos os casos a pessoa volta ao estado Não-Filiado, **sem perder a conta nem o histórico** — apenas o acesso exclusivo é suspenso.

### 3.4 O que acessa

Tudo o que o Não-Filiado acessa, **mais**:

| Área | Acesso |
|------|--------|
| Meu perfil | Além do que o Não-Filiado edita, vê os dados sincronizados do PMI (nome e PMI ID) em modo leitura, com o selo de filiado |
| Clube de benefícios | Completo: navega, resgata e consulta o próprio histórico de resgates |
| Conteúdo exclusivo | Completo: assiste a vídeos e acessa os links curados do PMI |
| Vitrine — Profissionais da comunidade | Pode **aparecer**, ativando a opção no perfil (só dados profissionais) |
| Vitrine — Oportunidades | Completo: vê as vagas e o link ou e-mail para se candidatar |
| Gamificação | Conquista badges e peças de quebra-cabeça (se optar pela gamificação) |

### 3.5 O que não pode

- Editar os dados que vêm da base do PMI (nome e PMI ID) — a correção é feita no PMI Global.
- Ver ou editar dados de outras pessoas.
- Acessar o console administrativo.

### 3.6 Objetivo na plataforma

**Aproveitar o que já paga**: resgatar benefícios, consumir conteúdo, acompanhar a própria participação e ser reconhecido pela comunidade.

---

## 4. Admin

### 4.1 Quem é

Pessoa cadastrada com **papel administrativo**. Opera a plataforma em nome do capítulo: cadastra e mantém o conteúdo, gerencia a base de pessoas e faz a curadoria do que os demais veem. Normalmente é um diretor ou voluntário de confiança do PMI-DF.

### 4.2 Como se torna

Por concessão manual de outro Admin. O papel é independente da filiação: um Admin pode ser filiado ou não.

### 4.3 O que acessa

Tudo o que o próprio estado de filiação permite (como qualquer usuário), **mais** o console administrativo:

| Área | Acesso |
|------|--------|
| Pessoas | Lista, busca, filtra e exporta toda a base; abre e edita o perfil de qualquer pessoa; suspende e reativa contas |
| Filiação | Concede ou revoga filiação manualmente |
| Voluntariado | Marca se uma pessoa é voluntária ou não |
| Clube de benefícios | Cadastra, publica, edita e arquiva benefícios; gerencia cupons; acompanha o relatório de resgates |
| Agenda de eventos | Cadastra eventos e concilia participações |
| Conteúdo exclusivo | Cadastra e mantém vídeos e links |
| Vitrine | Cadastra e mantém oportunidades; pode ocultar um profissional da vitrine |
| Gamificação | Cadastra badges e quebra-cabeças; acompanha as conquistas |
| Auditoria | Consulta o registro das ações administrativas |

### 4.4 O que não pode

- Editar consentimentos ou preferências de outra pessoa (apenas consultá-los).
- Alterar dados sincronizados da base do PMI.
- Excluir fisicamente registros que tenham histórico (benefícios com resgates, contas com participações) — o encerramento é sempre por arquivamento, suspensão ou anonimização.

### 4.5 Objetivo na plataforma

**Operar o capítulo com rastreabilidade**: manter o conteúdo atual, resolver casos de cadastro e prestar contas com base em dados reais.

---

## 5. Resumo comparativo

| | Não-Filiado | Filiado | Admin |
|---|:---:|:---:|:---:|
| Tem conta e perfil próprio | ✔ | ✔ | ✔ |
| Filiação ativa reconhecida | — | ✔ | depende |
| Prévia de benefícios e conteúdo | ✔ | — (vê completo) | conforme filiação |
| Resgata benefícios e consome conteúdo exclusivo | — | ✔ | conforme filiação |
| Vê oportunidades (vagas) | — | ✔ | conforme filiação |
| Aparece em Profissionais da comunidade (opt-in) | — | ✔ | conforme filiação |
| Conquista badges | — | ✔ | conforme filiação |
| Vê e edita dados de terceiros | — | — | ✔ |
| Concede/revoga filiação, marca voluntário | — | — | ✔ |
| Cadastra e mantém conteúdo da plataforma | — | — | ✔ |

> **Regra transversal:** toda verificação de permissão ocorre no **backend**. Ocultar elementos na interface é experiência do usuário, nunca controle de acesso.

---

## Tabela de Contribuições

| Versão | Data | Descrição | Autor | Revisor |
|--------|------|-----------|-------|---------|
| 1.0 | 11/09/2026 | Criação do documento — definição dos atores Não-Filiado, Filiado e Admin | Vitor Leonardo | Nome do revisor |
| 1.1 | 11/09/2026 | Acessos à Vitrine detalhados por visão: Profissionais da comunidade (todos veem, só Filiados aparecem) e Oportunidades (só Filiados) | Vitor Leonardo | Nome do revisor |
| 1.2 | 11/09/2026 | Gamificação alinhada ao ÉP-08: badges e quebra-cabeças, sem ranking nem submissões | Vitor Leonardo | Nome do revisor |
