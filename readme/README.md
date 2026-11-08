# Mercado Morcelle — Sistema Web

Jornal digital de ofertas do Mercado Morcelle: site público com produtos e promoções, e painel administrativo protegido por login para gerenciar produtos, categorias, promoções e imagens.

**Tecnologias:** Python 3.11 + Flask + Jinja2 + HTML5 + CSS3 + JavaScript puro. Banco: SQLite (padrão) ou PostgreSQL.

---

## Estrutura do projeto

```
ProjetoMorcelli/
├── html/                       → FRONTEND (o que o usuário vê)
│   ├── templates/              → páginas HTML (Jinja2)
│   │   ├── base.html, index.html, login.html, produto_detalhes.html, 404.html
│   │   ├── _product_card.html
│   │   └── admin/              → páginas do painel administrativo
│   └── static/
│       ├── css/style.css
│       ├── js/script.js
│       ├── images/logo.png
│       ├── uploads/produtos/   → imagens enviadas pelo admin (se não usar Cloudinary)
│       ├── manifest.json       → PWA
│       └── service-worker.js   → PWA
│
├── back/                       → BACKEND (código Python)
│   ├── app.py                  → rotas, banco de dados, login, upload
│   ├── Procfile                → comando de produção (gunicorn)
│   ├── runtime.txt / .python-version → versão do Python (3.11.9)
│   ├── .env.example            → lista das variáveis de ambiente
│   └── database.db             → criado automaticamente (SQLite)
│
├── requerimento/
│   └── requirements.txt        → dependências do Python
│
├── readme/
│   └── README.md               → este arquivo
└── .gitignore
```

O `app.py` já está configurado para buscar `templates` e `static` na pasta `html/`. **Não mude as pastas de lugar** sem ajustar `TEMPLATES_DIR` e `STATIC_DIR` no começo do `app.py`.

---

## Como instalar e rodar (passo a passo)

Todos os comandos abaixo são executados dentro da pasta **`back/`**.

### 1. Instalar o Python
Python 3.10 ou superior (testado com 3.11.9): https://www.python.org/downloads/
No Windows, marque **"Add Python to PATH"** na instalação. Confira com `python --version`.

### 2. Criar e ativar o ambiente virtual
```
cd back
python -m venv venv
```
Windows: `venv\Scripts\activate`
Linux/macOS: `source venv/bin/activate`

### 3. Instalar as dependências
```
pip install -r ../requerimento/requirements.txt
```

### 4. Executar

**Para testes (computador local):**
```
python app.py
```
Abra http://127.0.0.1:5000

**Para o servidor (produção):**
```
gunicorn app:app --bind 0.0.0.0:8000
```
(gunicorn funciona em Linux/macOS. Em servidor Windows, use `pip install waitress` e `waitress-serve --port=8000 app:app`.)

Na primeira execução o sistema cria sozinho o banco, as tabelas, as categorias padrão e o usuário administrador.

### 5. Endereços
- Site público: `/`
- Painel administrativo: `/login`

---

## Usuário administrador inicial

```
E-mail: admin@mercadomorcelle.com
Senha:  MudeEssaSenha123!
```

**Troque a senha no primeiro acesso:** menu lateral → **Configurações** → informe a senha atual e a nova (mínimo 8 caracteres).

---

## Configuração (variáveis de ambiente)

Veja `back/.env.example`. Todas são opcionais para rodar localmente.

| Variável | Para que serve |
|---|---|
| `SECRET_KEY` | Chave das sessões. **Defina uma chave própria em produção.** |
| `DATABASE_URL` | Se definida, usa PostgreSQL (ex.: Supabase). Se vazia, usa SQLite local. |
| `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET` | Se definidas, as imagens vão para o Cloudinary. Se vazias, ficam em `html/static/uploads/produtos/`. |

Exemplo (Linux/macOS):
```
export SECRET_KEY="uma-chave-longa-e-aleatoria"
gunicorn app:app --bind 0.0.0.0:8000
```
Exemplo (Windows, PowerShell):
```
$env:SECRET_KEY="uma-chave-longa-e-aleatoria"
python app.py
```

> **Atenção:** as senhas reais (banco, Cloudinary) **não** vão neste pacote. Serão passadas separadamente, se necessário.

---

## Como usar

**Cadastrar produto:** login → **Adicionar produto** → nome, descrição, categoria, preço → **ESCOLHER IMAGENS** → **CADASTRAR PRODUTO**. A primeira imagem vira a principal.

**Editar imagens:** **Produtos** → **Editar** → marcar outra como principal (★), excluir (✕) ou adicionar novas → **SALVAR ALTERAÇÕES**.

**Criar promoção:** **Promoções** → **Nova promoção** → produto, preço promocional (menor que o normal) e período. A oferta só aparece no site dentro das datas informadas.

**Trocar a logo:** substitua `html/static/images/logo.png` por outro arquivo com o mesmo nome.

---

## Segurança

- Senhas guardadas com hash (Werkzeug), nunca em texto puro.
- Todas as rotas `/admin/*` exigem login, verificado no backend.
- Uploads: `secure_filename`, nome único (UUID), extensões jpg/jpeg/png/webp e limite de 5 MB.

## Fora do escopo

Sistema apenas de divulgação: não possui carrinho, pagamento, venda online, nota fiscal ou fidelidade.
