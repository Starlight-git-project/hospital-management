# Normalização — Consulta, Convênio e Clínica

## 1. Escopo

Esta documentação trata exclusivamente da normalização das informações relacionadas a:

- `Consulta`;
- `Convênio`;
- `Clínica`;
- relacionamento N:N entre `Convênio` e `Clínica`.

As entidades `Paciente`, `Médico`, `Especialidade` e `Endereço` não são normalizadas novamente aqui. Elas são referenciadas por suas chaves estrangeiras, pois possuem documentação própria no projeto, issues #8 e #9.

A nomenclatura utilizada segue o documento `docs/normalizacao/de-para-campos.md`.

### Observação sobre o dataset original

O dataset original **não possui** uma tabela independente de `Convênio` ou `Clínica`. Elas estão escondidas como texto solto em outras tabelas:
 
- `consultas.csv` fornece `id_consulta`, `id_paciente`, `id_medico`, data, hora, motivo e status.
- `pacientes.csv` fornece `convenio` e `numero_convenio`.
- `medicos.csv` fornece `unidade_hospitalar`.

Ou seja: hoje não existe nenhum campo, nem em `consultas.csv`, que diga em qual unidade uma consulta específica ocorreu, ou qual convênio cobriu aquela consulta em particular. Por isso, vamos extrair `Convênio` e `Clínica` como entidades e definir como `Consulta` se relaciona com elas.

---

## 2. De-para utilizado

| Planilha / coluna atual | Campo no modelo normalizado |
|---|---|
| `consultas.id_consulta` | `consulta.id_consulta` |
| `consultas.id_paciente` | `consulta.id_paciente` (FK) |
| `consultas.id_medico` | `consulta.id_medico` (FK) |
| `consultas.data_consulta` | `consulta.data_consulta` |
| `consultas.hora_consulta` | `consulta.hora_consulta` |
| `consultas.motivo_consulta` | `consulta.motivo_consulta` |
| `consultas.status_consulta` | `consulta.status_consulta` |
| `pacientes.convenio` | `convenio.nome_convenio` |
| `pacientes.numero_convenio` | dado do vínculo paciente-convênio (`paciente_convenio`, issue #8) |
| `medicos.unidade_hospitalar` | `clinica.nome_clinica` |
 
Os valores de `unidade_hospitalar` já vêm traduzidos pelo de-para do projeto: "Clínica Zona Oeste", "Clínica Zona Leste" e "Hospital Central".

---

# 3. Tabela suja

Para demonstrar a normalização, os dados relevantes podem ser vistos como se tivessem vindo de um único `join` manual entre `consultas.csv`, `pacientes.csv` e `medicos.csv`, como uma planilha só.

### Estrutura
| id_consulta | id_paciente | nome_paciente | convenio_paciente | numero_convenio | id_medico | nome_medico | especialidade | unidade_hospitalar | convenios_aceitos_na_unidade | data_consulta | hora_consulta | motivo_consulta | status_consulta |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| A001 | P034 | Sarah Miller | WellnessCorp | INS118820 | D009 | John Brown | Oncologia | Hospital Central | WellnessCorp, PulseSecure, HealthIndia | 2023-08-09 | 15:15:00 | Terapia | Agendada |
| A002 | P032 | Robert Davis | PulseSecure | INS552031 | D004 | David Jones | Pediatria | Clínica Zona Leste | PulseSecure, HealthIndia | 2023-06-09 | 14:30:00 | Terapia | Não compareceu |
| A003 | P048 | Laura Jones | HealthIndia | INS903312 | D004 | David Jones | Pediatria | Clínica Zona Leste | PulseSecure, HealthIndia | 2023-06-28 | 8:00:00 | Consulta de rotina | Cancelada |

### Problemas identificados

1. `convenios_aceitos_na_unidade` guarda **mais de um valor na mesma célula**. É essa coluna que revela a existência do relacionamento N:N entre convênio e clínica, onde cada unidade aceita vários convênios, e cada convênio é aceito em várias unidades.
2. `nome_paciente`, `convenio_paciente`, `numero_convenio`, `nome_medico`, `especialidade` e `unidade_hospitalar` se repetem a cada linha em que a mesma pessoa ou unidade aparece.
3. `unidade_hospitalar` está atrelada ao **médico**, não a consulta. Hoje não existe forma de registrar em qual unidade uma consulta específica conteceu, só em qual unidade o médico trabalha.

---

# 4. Primeira Forma Normal — 1FN

## Critério

Uma relação está na 1FN quando cada atributo contém valores atômicos, não existem grupos repetitivos e cada registro é identificado por uma chave.

### Violação e correção
 
O único valor multivalorado da tabela suja é `convenios_aceitos_na_unidade`. Resolver isso significa não guardar uma lista dentro da célula, e sim uma linha por combinação (unidade, convênio aceito):
 
| unidade_hospitalar | convenio_aceito |
|---|---|
| Hospital Central | WellnessCorp |
| Hospital Central | PulseSecure |
| Hospital Central | HealthIndia |
| Clínica Zona Leste | PulseSecure |
| Clínica Zona Leste | HealthIndia |

Por isso, é necessário avançar para a 2FN e, principalmente, para a 3FN.

### Resultado em 1FN

```text
CONSULTA_1FN
------------
id_consulta PK
id_paciente
id_medico
data_consulta
hora_consulta
motivo_consulta
status_consulta
nome_convenio
numero_convenio
nome_clinica
```

Cada linha representa uma consulta e cada coluna possui um único valor.

---

# 5. Segunda Forma Normal — 2FN

## Critério

A 2FN elimina dependências parciais de atributos não-chave em relação a uma parte de uma chave primária composta.

### Aplicação

Na tabela `CONSULTA_1FN`, a chave é `id_consulta`, uma chave simples, então a 2FN **não introduz decomposição adicional** nela:

```text
id_consulta → id_paciente, id_medico, data_consulta,
              hora_consulta, motivo_consulta, status_consulta,
              nome_convenio, numero_convenio, nome_clinica
```

Entretanto, a análise de normalização identifica que alguns atributos pertencem conceitualmente a outras entidades.

Isso será resolvido na 3FN.

---

# 6. Terceira Forma Normal — 3FN

## 6.1 Dependências funcionais

Cada atributo de `Consulta` precisa depender **diretamente** de `id_consulta`, e não de outro campo não-chave. As principais dependências utilizadas são:

```text
id_consulta → id_paciente
id_consulta → id_medico
id_consulta → data_consulta
id_consulta → hora_consulta
id_consulta → motivo_consulta
id_consulta → status_consulta
```

Para convênio:

```text
id_convenio → nome_convenio
```

Para clínica:

```text
id_clinica → nome_clinica
```

Assim, `nome_convenio` não é uma propriedade da consulta, é uma propriedade do convênio.

Da mesma forma, `nome_clinica` não é uma propriedade da consulta, é uma propriedade da clínica.

---

## 6.2 Tabela `Consulta`

A tabela de consulta passa a armazenar apenas os atributos próprios do evento de consulta e as referências às entidades relacionadas.

```text
CONSULTA
--------
id_consulta PK
id_paciente FK -> Paciente
id_medico FK
id_clinica FK
id_convenio FK NULL
FK (id_convenio, id_clinica) → CONVENIO_CLINICA
data_consulta
hora_consulta
motivo_consulta
status_consulta

FK (id_convenio, id_clinica)
   → CONVENIO_CLINICA (id_convenio, id_clinica)
```

### Justificativa

- `id_paciente` referencia o paciente atendido;
- `id_medico` referencia o médico responsável;
- `id_clinica` referencia a unidade onde a consulta ocorre;
- `id_convenio` referencia o convênio utilizado, quando houver;
- `id_convenio` pode ser `NULL`, pois a consulta pode ser particular;
- os atributos de data, hora, motivo e status dependem diretamente de `id_consulta`.

Os dados de médico e paciente não são duplicados nessa tabela. Apenas suas chaves são mantidas.

---

## 6.3 Tabela `Convenio`

O dataset possui os seguintes provedores distintos:

| ID sugerido | Nome do convênio |
|---|---|
| `COV001` | HealthIndia |
| `COV002` | MedCare Plus |
| `COV003` | PulseSecure |
| `COV004` | WellnessCorp |

A tabela resultante é:

```text
CONVENIO
--------
id_convenio PK
nome_convenio
```

### Justificativa

`nome_convenio` representa o nome da operadora/convênio. Como o mesmo convênio aparece em diversos pacientes, armazenar seu nome diretamente em `Consulta` causaria repetição.

A criação da entidade `Convenio` permite que cada convênio seja cadastrado uma única vez.

#### Sobre `numero_convenio`

`numero_convenio` não deve ser tratado como atributo de `Convenio`, pois os valores são diferentes para cada paciente. Esse dado pertence ao vínculo entre
paciente e convênio, modelado em `Paciente_Convenio` (issue #8), não à tabela `Convenio` em si.

---

## 6.4 Tabela `Clínica`

Os valores de `unidade_hospitalar` representam as unidades onde os médicos atuam.

Após aplicar o de-para do projeto:

| ID sugerido | Valor original | Nome no modelo |
|---|---|---|
| `CLI001` | Central Hospital | Hospital Central |
| `CLI002` | Eastside Clinic | Clínica Zona Leste |
| `CLI003` | Westside Clinic | Clínica Zona Oeste |

A tabela resultante é:

```text
CLINICA
-------
id_clinica PK
nome_clinica
```

### Justificativa

Uma unidade hospitalar pode estar associada a vários médicos e, consequentemente, a várias consultas.

Manter `nome_clinica` repetido em cada consulta cria redundância e aumenta o risco de inconsistência.

A entidade `Clinica` centraliza essa informação e passa a ser referenciada por FK tanto em `Consulta` quanto em `Convenio_Clinica`.

---

## 6.5 Relacionamento N:N — `Convenio_Clinica`

Um requisito do modelo é que um convênio pode atender várias unidades e também é possível que uma clínica trabalhe com vários convênios. Portanto, a cardinalidade é:

```text
CONVENIO N : N CLINICA
```

A relação N:N é resolvida por uma tabela associativa:

```text
CONVENIO_CLINICA
----------------
id_convenio PK/FK
id_clinica PK/FK

PK (id_convenio, id_clinica)
```

### Justificativa

A tabela associativa transforma o N:N em duas relações 1:N, o que nos permite cadastrar uma nova combinação de convênio e clínica sem alterar a estrutura das tabelas principais.

Além disso, quando uma consulta possuir convênio, não basta verificar se o `id_convenio` existe na tabela `CONVENIO`. É necessário garantir que o convênio esteja habilitado para a clínica onde a consulta ocorrerá.

---

# 7. Cardinalidades

| Relacionamento | Cardinalidade | Leitura |
|---|---|---|
| Paciente — Consulta | 1:N | um paciente tem várias consultas; cada consulta é de um paciente |
| Medico — Consulta | 1:N | um médico realiza várias consultas; cada consulta tem um médico |
| Clinica — Consulta | 1:N | uma clínica recebe várias consultas; cada consulta ocorre em uma clínica |
| Convenio — Consulta | 1:0..N | um convênio pode cobrir várias consultas; a consulta usa **no máximo um** convênio (ou nenhum) |
| Convenio — Clinica | **N:N** | resolvido por `Convenio_Clinica` |
| Convenio — Convenio_Clinica | 1:N | |
| Clinica — Convenio_Clinica | 1:N | |
 
```text
CONSULTA.id_paciente → PACIENTE.id_paciente
CONSULTA.id_medico   → MEDICO.id_medico
CONSULTA.id_clinica  → CLINICA.id_clinica
CONSULTA.id_convenio → CONVENIO.id_convenio     (NULL permitido)
```
 
A normalização de `Paciente` e `Medico` não faz parte deste documento (ver issues #8 e #9).

---

# 8. Lista final das tabelas resultantes

Considerando exclusivamente o escopo desta normalização:

### `CONSULTA`

```text
id_consulta PK
id_paciente FK
id_medico FK
id_clinica FK
id_convenio FK NULL
data_consulta
hora_consulta
motivo_consulta
status_consulta
```

### `CONVENIO`

```text
id_convenio PK
nome_convenio
```

### `CLINICA`

```text
id_clinica PK
nome_clinica
```

### `CONVENIO_CLINICA`

```text
id_convenio PK/FK
id_clinica PK/FK
```

### Entidades relacionadas, mas normalizadas em outras partes do projeto

```text
PACIENTE           → #8
ENDERECO           → #8
PACIENTE_CONVENIO  → #8
MEDICO             → #9
ESPECIALIDADE      → #9
MEDICO_ESPECIALIDADE → #9
```

---

# 9. Conclusão

A normalização separa os atributos de consulta dos atributos próprios de convênio e clínica. A principal decomposição necessária é:

```text
CONSULTA
CONVENIO
CLINICA
CONVENIO_CLINICA
```

A tabela `CONVENIO_CLINICA` resolve o relacionamento N:N entre convênios e unidades. A tabela `CONSULTA` mantém as FKs para `Paciente`, `Médico`, `Clínica` e `Convênio` (reforçada por uma FK composta contra `Convenio_Clinica` para impedir que uma consulta registre um
convênio que a clínica não aceita). 

O `numero_convenio` não é armazenado em `CONVENIO`, pois identifica o vínculo do paciente com seu convênio e não a operadora em si. Esse atributo deve ser tratado na modelagem do relacionamento `Paciente_Convenio`.
