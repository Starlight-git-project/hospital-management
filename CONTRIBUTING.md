## Guia de Contribuição

Este projeto é feito por 3 colaboradoras trabalhando em conjunto. As regras abaixo existem pra garantir que todas pratiquem tanto o conteúdo técnico quanto o processo de trabalho colaborativo real (o que conta tanto quanto o SQL em si).

## Regra de ouro

Nenhum código entra na branch principal sem passar por Issue → Branch → Pull Request → Revisão de outra colega.
Ninguém aprova o próprio Pull Request.

## Fluxo de trabalho

**1. Toda tarefa começa em uma Issue**

Antes de escrever qualquer código, deve existir uma issue no repositório descrevendo o que será feito (ver `ISSUES.md` para a lista inicial). Se surgir uma tarefa nova durante o projeto, abram uma issue nova antes de começar.

Cada issue deve ter:


- Um título claro ([Entregável] Descrição curta)
- Descrição do que precisa ser feito e critério de aceite
- Label indicando o tipo (modelagem, ddl, etl, sql-negocio, indices, views-sp, trigger, docs)
- Uma responsável atribuída (assignee)

**2. Branch por issue**

Nomeie a branch referenciando a issue:
```
<tipo>/<numero-da-issue>-descricao-curta
```

Exemplos:
```
feature/12-ddl-tabelas-consulta
docs/05-normalizacao-pacientes
fix/20-correcao-fk-medico
```
Tipos possíveis: `feature`, `docs`, `fix`, `refactor`.

**3. Commits**

Mensagens de commit em português, no imperativo, curtas e específicas:
```
feat: cria tabela Medico_Especialidade com PK composta
docs: documenta normalização da entidade Paciente
fix: corrige tipo de dado do campo Data_Nascimento
```

Evitem commits genéricos tipo `ajustes`,`finalizando` ou `update`.

**4. Pull Request**

Ao abrir o PR:

- Referencie a issue (`Closes #12`)
- Descreva o que foi feito e por quê (decisões de modelagem merecem 2-3 linhas de justificativa)
- Marque as outras duas colaboradoras como revisoras

**5. Revisão (Code Review)**

Toda PR precisa de pelo menos 1 aprovação de outra colaboradora antes do merge. Ao revisar, verifiquem:

- A modelagem faz sentido (cardinalidade, chaves, normalização)?
- O SQL responde exatamente à pergunta de negócio, sem gambiarra?
- Existe justificativa documentada para escolhas não óbvias (ex.: por que esse índice, por que essa view)?
- Nomenclatura consistente com o resto do projeto?


Se a revisora pedir mudanças, a autora ajusta e a revisora aprova antes do merge, **ninguém força merge sem aprovação**.

**6. Merge**

- Merge apenas depois de aprovação
- Preferência por squash merge, mantendo o histórico da branch principal limpo
- Apague a branch depois do merge / opcional - **para esse projeto eu prefiro que não apague**

**Divisão de papéis**

Os papéis abaixo devem rotacionar ao longo do projeto, cada colaboradora deve passar por modelagem, SQL de negócio e ETL/infra em algum momento; ninguém trava só num tópico.

```
| Papel | Responsabilidade |
|--------|------------------|
| Modelagem & Documentação | DER, dicionário de dados, normalização |
| Banco & Performance | DDL, índices, views, procedures, triggers |
| Negócio & ETL | Carga de dados, queries de negócio |

```
**Convenções de nomenclatura SQL**


- Tabelas: PascalCase no singular (Paciente, Consulta, Medico_Especialidade)
- Colunas: PascalCase (Nome_Paciente, Data_Nascimento)
- Chaves primárias: ID_<Tabela> (ID_Paciente)
- Chaves estrangeiras: mesmo nome da PK referenciada
- Índices: idx_<tabela>_<coluna(s)>
- Views: vw_<descricao>
- Stored Procedures: sp_<descricao>
- Triggers: trg_<tabela>_<evento>

## Definition of Ready (antes de começar uma issue)

- [ ] A issue tem descrição e critério de aceite claros.
- [ ] Não depende de outra issue ainda não concluída (ex.: não dá para criar um índice antes do DDL existir).

## Definition of Done (antes de fechar uma issue)

- [ ] Código/documento versionado e mergeado na branch **develop**.
- [ ] O Pull Request (PR) teve pelo menos 1 revisão de outra colaboradora.
- [ ] Se for SQL: testado executando contra o banco real, sem erros.
- [ ] Documentação/README da pasta correspondente atualizado, se aplicável.