# Setup do Ambiente - PostgreSQL

Guia de instalação do banco de dados PostgreSQL. Sigam o passo a passo do seu sistema operacional. 
Se travar em algo, comentem na `issue #1` marcando as outras ou no `discord`. não fiquem travadas sozinhas.

> Usaremos a versão **16** ou **17** do PostgreSQL (estáveis). **Não instalem a versão 19**, ainda está em beta.

---

## Windows

1. Acesse: [https://www.postgresql.org/download/windows/](https://www.postgresql.org/download/windows/)
2. Baixe o instalador (EDB) da versão 
3. Rode o instalador e **mantenha marcados** os componentes padrão:
   - PostgreSQL Server
   - pgAdmin 4
   - Command Line Tools
4. Durante a instalação, ele vai pedir uma **senha para o usuário `postgres`** - anotem em lugar seguro (bloco de notas, gerenciador de senha).
É a senha de administrador do banco local, vamos usar bastante.
5. Mantenham a porta padrão: `5432`
6. Ao final, o instalador oferece abrir o "Stack Builder", pode fechar, não precisamos disso para este projeto.

---

## macOS

Duas opções - escolham uma:

### Opção A - Postgres.app (mais simples)
1. Baixe em: https://postgresapp.com/downloads.html
2. Arraste para a pasta Applications
3. Abra o app e clique em **"Initialize"** — o servidor já sobe rodando, sem configuração extra

### Opção B - Homebrew (se já usam)
```bash
brew install postgresql@16
brew services start postgresql@16
```

---

## Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

---

## Cliente gráfico (pgAdmin)

- **Windows:** já vem junto no instalador, nada a fazer
- **macOS/Linux:** se não veio junto, baixem separado em https://www.pgadmin.org/download/

O pgAdmin é a interface visual onde vocês vão ver as tabelas, rodar queries e conferir dados — vamos usar ele nas issues de DDL, ETL e SQL de negócio.

---

## Testando se funcionou

Abram o terminal (Prompt de Comando/PowerShell no Windows, Terminal no Mac/Linux) e rodem:

```bash
psql -U postgres
```

Ele vai pedir a senha que vocês definiram na instalação. Se abrir um prompt assim:

```
postgres=#
```

Deu certo! Pode sair digitando `\q` e apertando Enter.

Se der erro de "comando não encontrado" (`psql` não reconhecido), o PostgreSQL instalou mas não ficou no PATH do sistema,comentem na issue #1 que ajudamos a resolver.

---

## Configuração do `.env` local

Depois de instalar, copiem o arquivo `.env.example` da raiz do projeto para `.env` e preencham com os dados da instalação de vocês:

```bash
cp .env.example .env
```

```
DB_HOST=localhost
DB_PORT=5432
DB_NAME=vitacare_saude
DB_USER=postgres
DB_PASSWORD=<a senha que vocês definiram na instalação>
```

⚠️ O `.env` **nunca** deve ser commitado (já está no `.gitignore`), é só pra uso **local de cada uma**.

---

## Checklist antes de seguir pras próximas issues

- [ ] PostgreSQL instalado (versão 17 ou 18)
- [ ] pgAdmin funcionando, conectado no servidor local
- [ ] `psql -U postgres` abre sem erro
- [ ] `.env` local criado e preenchido (não commitado)🚨
