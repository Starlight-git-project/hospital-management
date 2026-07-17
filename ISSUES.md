# Issues Iniciais do Projeto

Lista de issues que estão no GitHub (Issues). Cada uma já vem com label, assignee sugerido, critério de aceite e dependência. A coluna "Depende de" indica que a issue só deve começar depois que a issue referenciada estiver fechada.

**Equipe:** Colaboradora A · Colaboradora B · Colaboradora C
**Duração do projeto:** 6 semanas

---

## Cronograma sugerido

| Semana | Foco | Issues | Observação |
|---|---|---|---|
| 1 | Kickoff, contexto e levantamento de entidades | #1, #2 | Trabalho conjunto - as 3 precisam sair alinhadas sobre o que vão modelar |
| 2 | Normalização (paralelo) | #3 (A), #4 (B), #5 (C) | Cada uma normaliza seu bloco de entidades e apresenta pras outras revisarem |
| 3 | Fechar normalização + DER + Dicionário | #6 (A), #7 (todas), #8 (B) | #7 é o ponto de convergência: só fecha depois que #3–#6 estiverem prontas |
| 4 | DDL + ETL | #9 (B), #10 (C) | Sequencial e crítico - bloqueia todo mundo depois. A pode revisar/testar o schema enquanto isso |
| 5 | Perguntas de negócio (paralelo) + início dos índices | #11 (A), #12 (B), #13 (C), #14 (A, início) | Cada uma fecha um bloco de perguntas; índices começam assim que houver query rodando |
| 6 | Views, SP, Trigger + revisão final | #15 (B), #16 (C), #17 (A), #18 (todas) | Última semana é justa - não deixem views/SP pra última hora, comecem assim que #12/#13 fecharem |

**Ponto de atenção:** a Semana 4 (#9 → #10) é o gargalo do cronograma — nada da Semana 5 começa antes disso estar pronto. Se atrasar, o atraso empurra o projeto inteiro. Vale considerar antecipar o início do DDL ainda no fim da Semana 3, assim que o DER estiver estável, mesmo antes do dicionário estar 100% finalizado.

---

### #1 - [Docs] Refinar contexto de negócio e validar dataset
**Label:** `docs` · **Assignee:** Todas (facilitação: Colab A) · **Semana:** 1
**Depende de:** —
**Descrição:** Revisar o documento de contexto (`docs/contexto-e-perguntas-negocio.md`), baixar o dataset do Kaggle, explorar os campos disponíveis e mapear quais entidades do contexto o dataset já cobre e quais precisarão ser criadas manualmente.
**Critério de aceite:** Documento atualizado com a lista de entidades cobertas pelo dataset vs. entidades a criar do zero.

### #2 - [Modelagem] Levantamento de entidades e atributos
**Label:** `modelagem` · **Assignee:** Todas (facilitação: Colab B) · **Semana:** 1
**Depende de:** #1
**Descrição:** A partir do dataset e do contexto, listar todas as entidades (Paciente, Médico, Especialidade, Consulta, Convênio, Clínica, Exame, Prescrição, Prontuário) com seus atributos brutos, antes de qualquer normalização.
**Critério de aceite:** Planilha ou markdown com entidade → lista de atributos brutos.

### #3 - [Modelagem] Normalização - Paciente e Endereço
**Label:** `modelagem` · **Assignee:** Colaboradora A · **Semana:** 2
**Depende de:** #2
**Descrição:** Demonstrar a normalização de Paciente/Endereço da 1FN até a 3FN, com exemplos de "antes" (dado cru) e "depois", justificando cada decomposição.
**Critério de aceite:** Documento em `docs/normalizacao/paciente.md` com exemplos e justificativa.

### #4 - [Modelagem] Normalização - Médico, Especialidade e relação N:N
**Label:** `modelagem` · **Assignee:** Colaboradora B · **Semana:** 2
**Depende de:** #2
**Descrição:** Normalizar Médico e Especialidade, justificando a criação da tabela associativa `Medico_Especialidade`.
**Critério de aceite:** Documento em `docs/normalizacao/medico-especialidade.md`.

### #5 - [Modelagem] Normalização - Consulta, Convênio, Clínica
**Label:** `modelagem` · **Assignee:** Colaboradora C · **Semana:** 2
**Depende de:** #2
**Descrição:** Normalizar as entidades transacionais (Consulta) e suas dependências (Convênio, Clínica), definindo cardinalidades.
**Critério de aceite:** Documento em `docs/normalizacao/consulta-convenio-clinica.md`.

### #6 - [Modelagem] Normalização - Exame, Prescrição, Prontuário
**Label:** `modelagem` · **Assignee:** Colaboradora A · **Semana:** 3
**Depende de:** #5
**Descrição:** Normalizar as entidades derivadas de Consulta, decidindo e justificando se Prontuário é 1:1 ou 1:N com Consulta.
**Critério de aceite:** Documento em `docs/normalizacao/exame-prescricao-prontuario.md`.

### #7 - [Modelagem] DER / MER completo
**Label:** `modelagem` · **Assignee:** Todas (facilitação: Colab C) · **Semana:** 3
**Depende de:** #3, #4, #5, #6
**Descrição:** Montar o Diagrama Entidade-Relacionamento completo em dbdiagram.io ou MySQL Workbench, com todas as cardinalidades e chaves.
**Critério de aceite:** Arquivo do diagrama + export em imagem salvos em `modelagem/`.

### #8 — [Docs] Dicionário de dados
**Label:** `docs` · **Assignee:** Colaboradora B · **Semana:** 3
**Depende de:** #7
**Descrição:** Documentar todas as tabelas finais: nome do campo, tipo de dado, PK/FK, obrigatoriedade e descrição de negócio.
**Critério de aceite:** Tabela completa em `docs/dicionario-de-dados.md`, uma seção por tabela.

### #9 - [DDL] Script de criação das tabelas
**Label:** `ddl` · **Assignee:** Colaboradora B · **Semana:** 4
**Depende de:** #7
**Descrição:** Escrever o script `CREATE TABLE` completo com constraints, PKs, FKs e tipos corretos, seguindo o DER aprovado.
**Critério de aceite:** Script em `sql/ddl/create_tables.sql` roda do zero sem erro e recria o schema inteiro.

### #10 - [ETL] Script de carga de dados
**Label:** `etl` · **Assignee:** Colaboradora C · **Semana:** 4
**Depende de:** #9
**Descrição:** Escrever o script de ETL (Python + pandas + SQLAlchemy, ou `LOAD DATA INFILE`) que popula todas as tabelas a partir do CSV original + dados sintéticos complementares.
**Critério de aceite:** Script em `etl/` roda do zero e popula o banco de forma consistente (sem violar FK).

### #11 - [SQL Negócio] Perguntas de negócio — Bloco 1 (Q1–Q19)
**Label:** `sql-negocio` · **Assignee:** Colaboradora A · **Semana:** 5
**Depende de:** #10
**Descrição:** Responder em SQL às perguntas 1 a 19 do documento de contexto (consultas simples, WHERE, ORDER BY, JOIN básico).
**Critério de aceite:** Uma query por pergunta, testada, em `sql/queries/bloco1.sql`, com comentário indicando a pergunta respondida.

### #12 - [SQL Negócio] Perguntas de negócio — Bloco 2 (Q20–Q39)
**Label:** `sql-negocio` · **Assignee:** Colaboradora B · **Semana:** 5
**Depende de:** #10
**Descrição:** Responder às perguntas 20 a 39 (agregação, subquery, UPDATE/ALTER, relatórios de negócio).
**Critério de aceite:** `sql/queries/bloco2.sql`, mesmas regras do #11.

### #13 - [SQL Negócio] Perguntas de negócio — Bloco 3 (Q40–Q58)
**Label:** `sql-negocio` · **Assignee:** Colaboradora C · **Semana:** 5
**Depende de:** #10
**Descrição:** Responder às perguntas 40 a 58 (consultas avançadas, ranking, window functions onde couber).
**Critério de aceite:** `sql/queries/bloco3.sql`, mesmas regras do #11.

### #14 - [Índices] Análise e criação de índices
**Label:** `indices` · **Assignee:** Colaboradora A · **Semana:** 5–6
**Depende de:** #11, #12, #13
**Descrição:** A partir das queries de negócio já escritas, identificar colunas candidatas a índice (FKs, filtros frequentes, colunas de ORDER BY), rodar `EXPLAIN` antes e depois de criar cada índice, e documentar o ganho.
**Critério de aceite:** Script em `sql/indices/create_indices.sql` + documento comparando `EXPLAIN` antes/depois.

### #15 - [Views/SP] Views de negócio
**Label:** `views-sp` · **Assignee:** Colaboradora B · **Semana:** 6
**Depende de:** #12
**Descrição:** Criar no mínimo 2 views que resolvam necessidades recorrentes de negócio (ex.: consultas + valor por convênio), com justificativa de por que virou view.
**Critério de aceite:** Script em `sql/views/`, com comentário de justificativa por view.

### #16 - [Views/SP] Stored Procedures
**Label:** `views-sp` · **Assignee:** Colaboradora C · **Semana:** 6
**Depende de:** #13
**Descrição:** Criar no mínimo 2 stored procedures parametrizadas (ex.: reajuste de valores de consulta por convênio, fechamento mensal de inadimplência).
**Critério de aceite:** Script em `sql/procedures/`, testado com pelo menos um exemplo de chamada.

### #17 - [Trigger] Auditoria de cancelamento/remarcação de consulta
**Label:** `trigger` · **Assignee:** Colaboradora A · **Semana:** 6
**Depende de:** #9
**Descrição:** Criar um trigger que registre em uma tabela de auditoria toda vez que uma consulta for cancelada ou remarcada.
**Critério de aceite:** Trigger em `sql/triggers/`, testado com um UPDATE/DELETE de exemplo mostrando o registro de auditoria gerado.

### #18 - [Docs] Revisão final e README das pastas
**Label:** `docs` · **Assignee:** Todas · **Semana:** 6
**Depende de:** todas as anteriores
**Descrição:** Revisar o repositório inteiro, garantir que cada pasta principal (`sql/`, `etl/`, `modelagem/`, `docs/`) tem um README curto explicando o que está ali, e conferir a checklist de Definition of Done do projeto.
**Critério de aceite:** Checklist do `README.md` principal 100% marcada.

---

## Resumo de carga por colaboradora

| | Modelagem | Banco/Performance | SQL de Negócio | Total de issues individuais |
|---|---|---|---|---|
| **Colaboradora A** | #3, #6 | #14, #17 | #11 | 5 |
| **Colaboradora B** | #4, #8 | #9, #15 | #12 | 5 |
| **Colaboradora C** | #5 | #10, #16 | #13 | 4 |

(+ 4 issues conjuntas: #1, #2, #7, #18 - com facilitadora rotativa)

## Labels sugeridas para criar no repositório

`docs` · `modelagem` · `ddl` · `etl` · `sql-negocio` · `indices` · `views-sp` · `trigger`