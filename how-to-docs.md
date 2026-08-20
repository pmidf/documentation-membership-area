# Como contribuir com a documentação

Este arquivo explica como configurar seu ambiente e contribuir com esta documentação.

---

## 1. Pré-requisitos

- Python 3.9 ou superior instalado
- Git configurado na sua máquina
- Acesso de escrita a este repositório

Para verificar se o Python está instalado:

```bash
python3 --version
```

---

## 2. Clonar o repositório

Se ainda não tiver o projeto localmente:

```bash
git clone https://github.com/pmidf/documentation-membership-area.git
cd documentation-membership-area
```

---

## 3. Criar o ambiente virtual

O ambiente virtual isola as dependências deste projeto do resto do seu sistema, evitando conflitos com outras instalações de Python.

```bash
python3 -m venv venv
```

---

## 4. Ativar o ambiente virtual

**Linux / macOS:**
```bash
source venv/bin/activate
```

**Windows (PowerShell):**
```powershell
venv\Scripts\Activate.ps1
```

**Windows (CMD):**
```cmd
venv\Scripts\activate.bat
```

> Para desativar o ambiente virtual a qualquer momento, use o comando `deactivate`.

---

## 5. Instalar as dependências

Com o ambiente virtual ativo, instale tudo que o projeto precisa a partir do `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## 6. Rodar o servidor local

Para visualizar a documentação enquanto edita:

```bash
mkdocs serve
```

Para parar o servidor, use `Ctrl + C` no terminal.

---

## 7. Editar a documentação

- Todo o conteúdo fica dentro da pasta `docs/`, em arquivos `.md` (Markdown).
- Para criar uma nova página, adicione um arquivo `.md` dentro de `docs/` e inclua-o na navegação, editando o `mkdocs.yml`:

```yaml
nav:
  - Início: index.md
  - Minha nova página: minha-pagina.md
```

---

## 8. Atualizar dependências (quando necessário)

Se você instalar um novo plugin ou atualizar uma dependência, regenere o `requirements.txt` para manter as versões travadas:

```bash
pip freeze > requirements.txt
```

Inclua esse arquivo atualizado no seu commit.