## Projeto 04 - Modelagem Relacional | VitaCare

### Sobre o projeto

A VitaCare (nome fictício) não tem nenhuma estrutura de banco de dados relacional de verdade ,os dados vivem espalhados em planilhas e cadastros soltos. Este projeto é o trabalho de modelar, do zero, o banco de dados transacional (OLTP) dessa nova operação, junto com as consultas SQL que respondem às principais perguntas de negócio das áreas de RH, Financeiro, Diretoria e Recepção/Operações

O contexto de negócio completo e a lista de perguntas de negócio estão em [docs/contexto-e-perguntas-negocio.md](https://github.com/Starlight-git-project/hospital-management/blob/main/docs/contexto-perguntas-negocio.md/contexto-perguntas-negocio.md).

### Objetivo de aprendizagem

Este não é um projeto de "copiar solução pronta". O objetivo é que cada colaboradora estude e pratique, na prática, os seguintes tópicos:


- Modelagem de dados relacional (entidades, atributos, chaves)
- Cardinalidade (1:1, 1:N, N:N) e quando criar tabela associativa
- Normalização (1FN, 2FN, 3FN),partindo de dado "sujo"/desnormalizado
- Diagrama Entidade-Relacionamento (DER/MER)
- DDL (CREATE TABLE, constraints, chaves estrangeiras, tipos de dado)
- Carga/ETL de dados (CSV → banco)
- Escrita de SQL de negócio (do simples ao avançado: JOIN, subquery, agregação, window functions)
Índices (o que indexar, por quê, e como medir o ganho)
- Views e Stored Procedures
- Triggers
- Trabalho colaborativo com Git (branches, PRs, issues, code review)

### Equipe

Projeto para 3 colaboradoras. A divisão de tarefas está nas issues do repositório (ver[ ISSUES.md](https://github.com/Starlight-git-project/hospital-management/blob/main/ISSUES.md)), cada entregável abaixo vira uma ou mais issues, distribuídas entre as três, com revisão cruzada obrigatória (ninguém aprova o próprio PR).

### Dataset

Base de partida: [Hospital Management Dataset (Kaggle)](https://www.kaggle.com/datasets/kanakbaghel/hospital-management-dataset), contém Pacientes, Médicos, Consultas e Faturamento.

Esse dataset não é normalizado nem completo de propósito, vocês vão precisar:


- Modelar entidades que faltam (Especialidade, Convênio, Clínica/Unidade, Prescrição, Exame, Prontuário)
- Criar a tabela associativa Medico_Especialidade (relação N:N)
- Complementar com dados sintéticos onde o dataset for raso, garantindo volume suficiente para as perguntas
de negócio funcionarem de forma realista

### Entregáveis (requisitos do projeto)
Todo entregável abaixo é obrigatório, exceto onde marcado como opcional. Cada um vira uma issue própria, não iniciem um entregável **sem ele estar como issue aberta e atribuída**.


- [ ] **1. Contexto de negócio** (`docs/`) - Documento do cenário VitaCare (já iniciado).
- [ ] **2. DER / MER** (`modelagem/`) - Diagrama completo com cardinalidades, feito em dbdiagram.io ou MySQL Workbench.
- [ ] **3. Dicionário de dados** (`docs/`) - Tabela → campo → tipo → PK/FK → obrigatoriedade → descrição.
- [ ] **4. Normalização documentada** (`docs/normalizacao/`) - "Antes" (dado cru) e "depois" em 1FN/2FN/3FN, com justificativa de cada decomposição.
- [ ] **5. Script DDL** (`sql/ddl/`) - `CREATE TABLE` completo, com constraints, FKs e tipos corretos.
- [ ] **6. Script de carga (ETL)** (`etl/`) - Python (pandas + SQLAlchemy) ou `LOAD DATA INFILE`, do CSV para o banco.
- [ ] **7. Perguntas de negócio** (`sql/queries/`) - Todas as queries SQL respondendo às perguntas em `docs/contexto-e-perguntas-negocio.md`.
- [ ] **8. Índices** (`sql/indices/`) - Criação de índices + justificativa técnica (`EXPLAIN` antes/depois).
- [ ] **9. Views e Stored Procedures** (`sql/views/`, `sql/procedures/`) - Mínimo 2 views e 1 stored procedures, cada uma com justificativa de negócio.
- [ ] **10. Trigger** (`sql/triggers/`) - Pelo menos 1 trigger (ex.: auditoria de cancelamento/remarcação de consulta).
|

### Requisitos técnicos


- SGBD: MySQL ou PostgreSQL (definir em conjunto e documentar a escolha no README de modelagem/)
- Versionamento: Git + GitHub, seguindo o fluxo descrito em `CONTRIBUTING.md`
- Toda decisão de modelagem relevante deve ser **documentada**, não apenas implementada, o "porquê" importa tanto quanto o "como"

Como fazer download do Postgres + PgAdmin: [Lab01-AWS-DMS página 13](https://drive.google.com/file/d/1z0fKpNP-TZn-ol9pDYxvCq-6uuN8O62a/view?usp=sharing)

### Definição de Pronto (Definition of Done) do projeto

- [ ] Todos os 10 entregáveis estão completos e mergeados na branch principal.
- [ ] Todas as issues estão fechadas.
- [ ] O script DDL roda do zero sem erro e recria o banco completo.
- [ ] Todas as perguntas de negócio têm query funcional e testada.
- [ ] Existe pelo menos 1 revisão cruzada registrada em cada Pull Request (PR) para cada colabboradora.
- [ ] O README de cada pasta explica o que está ali e como rodar/usar.

### Estrutura de pastas sugerida
```
.
├── README.md
├── CONTRIBUTING.md
├── ISSUES.md
├── docs/
│   ├── contexto-e-perguntas-negocio.md
│   └── normalizacao/
├── modelagem/
│   └── der.png / der.dbml
├── sql/
│   ├── ddl/
│   ├── queries/
│   ├── indices/
│   ├── views/
│   ├── procedures/
│   └── triggers/
└── etl/

```
## Curso de apoio

Antes ou durante o projeto, façam o curso abaixo, ele cobre a base teórica da maior parte dos entregáveis (modelagem, normalização, dicionário de dados, DDL/DML/DQL, JOINs, Views/Procedures/Triggers):

**[Bancos de Dados Relacionais - (Udemy)](https://www.udemy.com/course/curso-completo-de-banco-de-dados-relacionais/?couponCode=KEEPLEARNING)**

O que vocês vão aprender lá:
- Modelagem Conceitual, Lógica e Física de um projeto de Banco de Dados
- SQL (DQL, DML, DDL) e T-SQL
- Normalização (1FN, 2FN, 3FN)
- Dicionários de Dados
- Instalação de MySQL, PostgreSQL, SQL Server e Oracle
- `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, `FULL JOIN`
- Triggers, Procedures, Functions e Views
- Ambientes transacionais

## Material de apoio - ETL (Entregável #6)

Para a issue de carga de dados (#10), que usa Python + pandas + SQLAlchemy,
os módulos abaixo do curso [Análise de Dados com Python e Pandas](COLE_O_LINK_CERTO_AQUI)
são suficientes - não precisa fazer o curso inteiro para este projeto:

- Criando o Banco de Dados e a Tabela
- Inserindo e Lendo Dados
- Atualizando e Excluindo Dados
- Utilizando o SQLAlchemy

> O restante do curso (Numpy, Matplotlib/Seaborn/Plotly, dashboards, MongoDB)
> é ótimo material, mas é escopo do Projeto 04 (camada analítica), não deste projeto.
