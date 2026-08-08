# Exploração do Dataset - Renata Rocha

## 1. Campos que encontrei no dataset (por tabela do Kaggle)
- **`appointments.csv`**: appointment_id, patient_id, doctor_id, appointment_date, appointment_time, reason_for_visit, status
- **`billing.csv`**: bill_id, patient_id, treatment_id, bill_date, amount, payment_method, payment_status
- **`doctors.csv`**: doctor_id, first_name, last_name, specialization, phone_number, years_experience, hospital_branch, email
- **`patients.csv`**: patient_id, first_name, last_name, gender, date_of_birth, contact_number, address, registration_date, insurance_provider, insurance_number, email
- **`treatments.csv`**: treatment_id, appointment_id, treatment_type, description, cost, treatment_date

## 2. Entidades do contexto que o dataset JÁ cobre
- **Paciente**: Cadastro básico e dados pessoais.
- **Médico**: Dados profissionais e contato.
- **Consulta / Agendamento**: Datas, horários e motivos.
- **Tratamento**: Procedimentos e custos.
- **Faturamento / Cobrança**: Registros de pagamentos e pendências.

## 3. Entidades do contexto que a gente vai ter que criar do zero
- **Especialidade Médica**: Isolar a especialidade para permitir regras financeiras e N:N com médicos.
- **Clínica / Unidade**: Estruturar filiais e unidades de atendimento.
- **Convênio**: Isolar empresas parceiras e planos de saúde.
- **Exame**: Tipos de exames, laboratórios parceiros e tabela de preços.
- **Prescrição (Receita)**: Medicamentos e dosagens vinculados aos atendimentos.
- **Prontuário**: Histórico clínico individual fechado por consulta.
- **Endereço**: Desmembrar CEP, logradouro e bairro para permitir análises geográficas.

## 4. Dúvidas / pontos estranhos que percebi
- Inconsistência de dados cadastrais: Registros em `patients.csv` com gêneros divergentes do nome (ex: David como 'F', Laura como 'M').
- Ausência de Chave Estrangeira para Convênios: O nome do plano de saúde em `patients.csv` está em formato de texto livre (`insurance_provider`), sem ID padronizado.
- Baixo nível de relacionamento em faturamento: A tabela `billing.csv` vincula-se apenas ao tratamento (`treatment_id`), mas não possui chave direta para a consulta (`appointment_id`).

## 5. Minhas entidades da issue #2 (atributos brutos, sem normalizar)
### Entidade: Convenio
- id_convenio
- nome_convenio
- cnpj
- tipo_plano
- taxa_reajuste_anual
- status_ativo

### Entidade: Exame
- id_exame
- nome_exame
- tipo_exame
- laboratorio_parceiro
- e_rede_propria
- valor_exame

### Entidade: Prescricao
- id_prescricao
- id_consulta
- medicamento
- dosagem
- instrucoes_uso
- quantidade