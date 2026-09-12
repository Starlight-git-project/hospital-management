# Normalização: Paciente e Endereço

Documentação do processo de normalização das informações de Paciente e Endereço extraídas do dataset bruto do projeto VitaCare Health, evoluindo da tabela não normalizada até a Terceira Forma Normal (3FN).

---

## 1. Tabela Suja (Dado Bruto)

Na estrutura original do dataset (`patients.csv`), todos os atributos do paciente estão centralizados em uma única tabela, apresentando dados compostos e dependências que geram redundância.

**Exemplo de dado bruto:**

| patient_id | first_name | last_name | gender | date_of_birth | contact_number | address | registration_date | insurance_provider | insurance_number | email |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 101 | Ana | Silva | F | 1985-04-12 | (11) 98765-4321 | Rua das Flores, 123, Apto 42, Centro, São Paulo - SP, 01001-000 | 2023-01-15 | SulAmérica | SUL-9988 | ana.silva@email.com |

---

## 2. Primeira Forma Normal (1FN)

**Regra:** Garantir a atomicidade dos campos (eliminar valores múltiplos/compostos) e definir uma chave primária.

**Justificativa:** 
O campo `address` continha múltiplos dados combinados (logradouro, número, complemento, bairro, cidade, estado e CEP) em um único texto. Decompomos esse campo em atributos atômicos para permitir buscas, ordenações e filtragens eficientes por cidade, estado ou CEP.

**Tabela Resultante (`paciente_1fn`):**
* **Atributos:** `patient_id` (PK), `first_name`, `last_name`, `gender`, `date_of_birth`, `contact_number`, `street`, `number`, `complement`, `neighborhood`, `city`, `state`, `zip_code`, `registration_date`, `insurance_provider`, `insurance_number`, `email`.

---

## 3. Segunda Forma Normal (2FN)

**Regra:** Garantir que todos os atributos não-chave dependam totalmente da chave primária (aplica-se a tabelas com chaves primárias compostas).

**Justificativa:** 
**Não se aplica.** A chave primária da tabela é simples (`patient_id`). Não existem chaves compostas nesta entidade, portanto, a tabela já atende aos requisitos da 2FN automaticamente ao estar na 1FN.

---

## 4. Terceira Forma Normal (3FN)

**Regra:** Eliminar dependências transitivas (nenhum atributo não-chave pode depender de outro atributo não-chave).

**Justificativa:** 
1. **Endereço (`zip_code`):** Os atributos `street`, `neighborhood`, `city` e `state` dependem do `zip_code` (CEP) e não diretamente do `patient_id`. Isolamos o endereço para evitar redundância caso múltiplos pacientes morem no mesmo local/CEP.
2. **Convênio (`insurance_provider`):** O nome e as regras do plano dependem da entidade de plano/convênio. Isolamos a tabela de convênios para evitar repetição do nome da operadora para cada paciente.

---

## 5. Lista Final de Tabelas Resultantes (3FN)

### Tabela `paciente`
Representa os dados cadastrais únicos do paciente.
* `id_paciente` (PK)
* `primeiro_nome`
* `sobrenome`
* `genero`
* `data_nascimento`
* `telefone`
* `email`
* `data_cadastro`
* `id_endereco` (FK -> `endereco.id_endereco`)
* `id_convenio` (FK -> `convenio.id_convenio`)
* `numero_carteira_convenio`

### Tabela `endereco`
Centraliza as informações geográficas e atômicas de localização.
* `id_endereco` (PK)
* `cep`
* `logradouro`
* `numero`
* `complemento`
* `bairro`
* `cidade`
* `estado`

### Tabela `convenio`
Armazena o cadastro das operadoras de saúde.
* `id_convenio` (PK)
* `nome_convenio`