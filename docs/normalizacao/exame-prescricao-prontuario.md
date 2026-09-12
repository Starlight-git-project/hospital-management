# Normalização: Exame, Prescrição e Prontuário

Documentação da normalização das entidades derivadas de Consulta, evoluindo
da tabela não normalizada até a Terceira Forma Normal (3FN).

---

## 1. Escopo

Esta documentação trata das entidades:
- `Exame` / `Tratamento` (já existe como `treatments.csv` no dataset original)
- `Prescrição` (não existe no dataset - criada do zero)
- `Prontuário` (não existe no dataset - criado do zero)

`Consulta`, `Convênio` e `Clínica` já foram normalizadas na issue #10 e são
apenas referenciadas aqui por FK.

---

## 2. Tabela suja (dado bruto)

O dataset original só cobre Tratamento (`treatments.csv`). Prescrição e
Prontuário não existem - o exemplo abaixo é hipotético, construído a partir
do fluxo de negócio real de uma consulta médica.

| id_consulta | tipo_tratamento | descricao | custo | data_tratamento | medicamento_prescrito | dosagem | observacoes_medicas | diagnostico |
|---|---|---|---|---|---|---|---|---|
| A001 | Quimioterapia | Triagem básica | 3941.97 | 2023-08-09 | Ondansetrona, Dexametasona | 8mg, 4mg | Paciente tolerou bem o procedimento | Neoplasia em remissão |

### Problemas identificados
1. `medicamento_prescrito` e `dosagem` guardam **mais de um valor na mesma célula** (um paciente pode receber mais de um medicamento na mesma consulta)
2. `observacoes_medicas` e `diagnostico` são informações de naturezas diferentes de `tipo_tratamento`/`custo` - misturam registro clínico com registro de procedimento/cobrança

---

## 3. Primeira Forma Normal (1FN)

**Justificativa:**
`medicamento_prescrito` e `dosagem` violam atomicidade - uma consulta pode
gerar mais de uma prescrição (mais de um medicamento). Isso não é erro, é
relacionamento **1:N** entre Consulta e Prescrição (uma consulta pode gerar
várias prescrições; cada prescrição pertence a uma única consulta).

**Tabelas resultantes (1FN):**

`tratamento_1fn`
- `id_tratamento` (PK), `id_consulta` (FK), `tipo_tratamento`, `descricao`, `custo`, `data_tratamento`

`prescricao_1fn`
- `id_prescricao` (PK), `id_consulta` (FK), `medicamento`, `dosagem`

`prontuario_1fn`
- `id_prontuario` (PK), `id_consulta` (FK), `observacoes_medicas`, `diagnostico`

---

## 4. Segunda Forma Normal (2FN)

**Justificativa:**
Todas as tabelas deste bloco têm chave primária simples (`id_tratamento`,
`id_prescricao`, `id_prontuario`). Não há chave composta neste bloco -
**2FN satisfeita automaticamente**, sem necessidade de decomposição.

---

## 5. Terceira Forma Normal (3FN)

**Justificativa:**
Não há dependência transitiva remanescente: todos os atributos não-chave
(`tipo_tratamento`, `medicamento`, `diagnostico`, etc.) dependem diretamente
da própria chave primária de cada tabela, não de outro atributo não-chave.
**3FN satisfeita.**

---

## 6. Decisão de cardinalidade: Consulta ↔ Prontuário

**Pergunta-chave:** um paciente pode ter mais de um registro de prontuário
para a mesma consulta?

**Decisão: 1:1**

**Justificativa:**
Um prontuário representa o registro clínico daquele atendimento específico
- diagnóstico, observações do médico sobre aquela consulta. Não faz sentido
de negócio uma mesma consulta gerar dois prontuários diferentes (isso
duplicaria/confundiria o histórico clínico do paciente). Se o médico
precisar complementar informação depois, o correto é **atualizar** o
prontuário existente, não criar um segundo registro.

Essa decisão também é coerente com o que observamos nos dados reais: no
dataset original, cada `appointment_id` aparece no máximo 1 vez em
`treatments.csv` - não há exemplo de uma mesma consulta gerando múltiplos
registros de tratamento, o que reforça a leitura de que o padrão de negócio
aqui é "1 consulta = 1 registro clínico consolidado".

**Consulta ↔ Prescrição, por outro lado, é 1:N** - uma consulta pode gerar
várias prescrições (vários medicamentos), o que é diferente do prontuário
(que é o registro único da consulta).

**Consulta ↔ Tratamento também é 1:N** - uma consulta pode envolver mais de
um tratamento/exame, mesmo que o dataset de exemplo atual só mostre 1:1
(ponto documentado como observação para o ETL, igual foi feito em #9 para
Médico/Especialidade).

---

## 7. Lista final de tabelas resultantes

### Tabela `tratamento`
- `id_tratamento` (PK)
- `id_consulta` (FK → `consulta.id_consulta`)
- `tipo_tratamento`
- `descricao`
- `custo`
- `data_tratamento`

### Tabela `prescricao`
- `id_prescricao` (PK)
- `id_consulta` (FK → `consulta.id_consulta`)
- `medicamento`
- `dosagem`

### Tabela `prontuario`
- `id_prontuario` (PK)
- `id_consulta` (FK → `consulta.id_consulta`, **UNIQUE** - garante a
  cardinalidade 1:1)
- `observacoes_medicas`
- `diagnostico`

---

## 8. Observação sobre os dados atuais

O dataset processado (`tratamentos.csv`) só cobre `Tratamento`. `Prescrição`
e `Prontuário` não existem nos dados de origem - precisarão ser gerados
como dado sintético na issue de ETL (#15), mantendo a proporção 1:N de
prescrições por consulta e 1:1 de prontuário por consulta.