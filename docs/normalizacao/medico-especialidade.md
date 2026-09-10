# Normalização: Médico e Especialidade

Documentação do processo de normalização das informações de Médico e Especialidade,
evoluindo da tabela não normalizada até a Terceira Forma Normal (3FN).

---

## 1. Tabela Suja (Dado Bruto)

Na estrutura original do dataset (`doctors.csv`), os atributos do médico estão
centralizados em uma única tabela. A especialidade aparece como um único valor
textual por médico.

**Exemplo de dado bruto:**

| id_medico | cpf | crm | nome | sobrenome | especialidade | telefone | anos_experiencia | unidade_hospitalar | email |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| D001 | 785.634.921-28 | CRM/SP 93810 | David | Taylor | Dermatologia | 8322010158 | 17 | Clínica Zona Oeste | dr.david.taylor@hospital.com |

**Nota sobre o dataset de origem:** os dados brutos do Kaggle trazem apenas
**1 especialidade por médico**. Na prática de negócio, no entanto, um médico
pode atuar em mais de uma especialidade - por isso, para efeito de modelagem,
consideramos um cenário hipotético de dado sujo em que isso aconteça:

| id_medico | ... | especialidade |
| :--- | :--- | :--- |
| D001 | ... | Dermatologia, Pediatria |

Esse é o cenário que justifica a decisão de 1FN abaixo, mesmo que os dados de
exemplo atuais não demonstrem esse caso.

---

## 2. Primeira Forma Normal (1FN)

**Regra:** Garantir a atomicidade dos campos (eliminar valores múltiplos/compostos)
e definir uma chave primária.

**Justificativa:**
O campo `especialidade` pode conter mais de um valor para o mesmo médico (ex.:
"Dermatologia, Pediatria"), o que viola atomicidade. Não é um erro do dado -
é um relacionamento **N:N** real entre Médico e Especialidade: um médico pode
ter várias especialidades, e uma especialidade é exercida por vários médicos.

A solução não é só "separar em colunas" (como fizemos com endereço) - é criar
uma **entidade própria** para Especialidade e uma **tabela associativa** que
conecta Médico e Especialidade.

**Tabelas resultantes (1FN):**

`medico_1fn`
- `id_medico` (PK), `cpf`, `crm`, `nome`, `sobrenome`, `telefone`,
  `anos_experiencia`, `unidade_hospitalar`, `email`

`especialidade_1fn`
- `id_especialidade` (PK), `nome_especialidade`

`medico_especialidade_1fn` (tabela associativa, resolve o N:N)
- `id_medico` (FK), `id_especialidade` (FK)

---

## 3. Segunda Forma Normal (2FN)

**Regra:** Garantir que todos os atributos não-chave dependam totalmente da
chave primária (aplica-se a tabelas com chave composta).

**Justificativa:**
A tabela `medico_especialidade` é a única com chave composta neste bloco
(`id_medico` + `id_especialidade`). Ela não possui nenhum atributo adicional
além das duas FKs - logo, não há nada que dependa só de parte da chave. **2FN
satisfeita.**

As demais tabelas (`medico`, `especialidade`) têm chave simples, então a 2FN
já está automaticamente satisfeita nelas, sem necessidade de decomposição.

---

## 4. Terceira Forma Normal (3FN)

**Regra:** Eliminar dependências transitivas (nenhum atributo não-chave pode
depender de outro atributo não-chave).

**Justificativa:**

1. **Especialidade:** `nome_especialidade` depende da entidade Especialidade,
   não do médico em si. Se ficasse dentro de `medico`, o mesmo nome de
   especialidade se repetiria a cada médico que a exercesse - mantida como
   tabela própria desde a 1FN, aqui confirmamos que não há dependência
   transitiva remanescente.

2. **Unidade hospitalar (`unidade_hospitalar`):** o nome da unidade/clínica
   não é uma característica do médico - é uma entidade própria (Clínica), com
   endereço e outros atributos que não dependem do médico. **Este campo não
   foi decomposto aqui de propósito**, porque a normalização de Clínica é
   escopo da issue #10 (Consulta/Convênio/Clínica). Fica registrado como
   candidato a virar `id_unidade` (FK) assim que a #10 definir a estrutura de
   `Clinica`, para evitar conflito de modelagem entre as duas issues.

---

## 5. Lista Final de Tabelas Resultantes (3FN)

### Tabela `medico`
Representa os dados cadastrais únicos do médico.
- `id_medico` (PK)
- `cpf`
- `crm`
- `nome`
- `sobrenome`
- `telefone`
- `anos_experiencia`
- `email`
- `id_unidade` (FK → `clinica.id_unidade` - estrutura final a ser definida na issue #10)

### Tabela `especialidade`
Armazena as especialidades médicas existentes.
- `id_especialidade` (PK)
- `nome_especialidade`

### Tabela `medico_especialidade`
Tabela associativa que resolve o relacionamento N:N entre Médico e
Especialidade.
- `id_medico` (FK → `medico.id_medico`)
- `id_especialidade` (FK → `especialidade.id_especialidade`)
- Chave primária composta: (`id_medico`, `id_especialidade`)

---

## Observação sobre os dados atuais

O dataset processado (`medicos.csv`) traz apenas 1 especialidade por médico.
A tabela `medico_especialidade` continua sendo a decisão correta de
modelagem (suporta o caso real de negócio), mas o volume de dados atual não
testa o cenário N:N de fato. Isso deve ser considerado na issue de ETL (#15):
se quisermos validar a tabela associativa com dado de verdade, será
necessário gerar alguns médicos sintéticos com mais de uma especialidade.