# Projeto 03 - Modelagem Relacional

## Contexto: VitaCare Health

---

## CONTEXTO

A VitaCare (nome fictício) não tem nenhuma estrutura de banco de dados relacional de verdade ,os dados vivem espalhados em planilhas e cadastros soltos. Este projeto é o trabalho de modelar, do zero, o banco de dados transacional (OLTP) dessa nova operação, junto com as consultas SQL que respondem às principais perguntas de negócio das áreas de RH, Financeiro, Diretoria e Recepção/Operações

A nossa equipe de ciência de dados, formada por analistas, engenheiros e cientistas de dados, foi chamada para uma série de reuniões de due diligence técnica. Nelas tivemos a oportunidade de conhecer a operação e como estão montadas as soluções de tecnologia da VitaCare. Durante essas reuniões percebemos que a empresa adquirida opera de forma totalmente descentralizada: cada clínica mantém suas próprias planilhas de agendamento, prontuários em papel digitalizados soltos em pastas, e um sistema de cadastro de pacientes feito sob medida há mais de dez anos, sem nenhuma camada de Analytics ou Ciência de Dados.

Para que a VitaCare possa operar com eficiência, reduzir glosas de convênio, otimizar a agenda dos médicos e gerar insights relevantes para os acionistas (ocupação de agenda, especialidades mais demandadas, inadimplência, sazonalidade de consultas, entre outros), será necessário estruturar do zero um modelo relacional sólido, capaz de sustentar tanto a operação transacional (OLTP) quanto, futuramente, a camada analítica.

O sistema legado da VitaCare (que chamaremos de **"Sistema Cadastral VitaCare"**) organiza a informação, de forma não normalizada, em torno dos seguintes conceitos:

1. Pacientes
2. Médicos
3. Especialidades Médicas
4. Consultas / Agendamentos
5. Prescrições (Receitas)
6. Convênios (Planos de Saúde)
7. Clínicas / Unidades
8. Exames
9. Prontuários (registro clínico por consulta)

Com base nestas informações, o nosso trabalho será aplicar tudo o que aprendemos **"operacionalizar"** a solução de Analytics e Data Science da VitaCare, começando pela modelagem relacional do banco transacional.

---

## DATASET SUGERIDO

- **Hospital Management Dataset** (Kaggle) - [faça o download aqui ](https://www.kaggle.com/datasets/kanakbaghel/hospital-management-dataset)- contém registros de Pacientes, Médicos, Consultas (Appointments) e Faturamento (Billing). Serve como ponto de partida "cru" para normalização.
- Complementar com dados sintéticos próprios para as entidades que o dataset não cobrir bem (ex.: Especialidades, Convênios, Prescrições, Exames), garantindo que existam relacionamentos N:N reais para o exercício de modelagem (ex.: médico atende múltiplas especialidades / convênio atende múltiplas clínicas).

---

## ENTREGÁVEIS TÉCNICOS DO PROJETO 03

1. Contexto de negócio (este documento)
2. DER / MER (dbdiagram.io ou MySQL Workbench)
3. Dicionário de dados (tabela, campo, tipo, PK/FK, descrição, obrigatoriedade)
4. Normalização documentada (1FN → 2FN → 3FN, mostrando o "antes" do CSV cru)
5. Script DDL (CREATE TABLE, constraints, FKs, tipos)
6. Script de carga (ETL simples: CSV → banco)
7. Respostas às perguntas de negócio abaixo, em SQL
8. Índices (criação + justificativa técnica, com EXPLAIN antes/depois)
9. Views e Stored Procedures (mínimo 1 de cada)
10. Pelo menos 1 Trigger (ex.: auditoria de cancelamento de consulta)

---

## PERGUNTAS DE NEGÓCIO

### Bloco 1 - Consultas simples, ORDER BY, WHERE, LIKE (Questões 1 a 12)

1. A Diretora Marina Kessler solicitou uma pesquisa que informe todos os pacientes cadastrados ordenados por data de nascimento.
2. A rede vai fechar parceria com um novo convênio, mas só assina o contrato se a clínica tiver menos de 25 médicos ativos. Verifique a quantidade atual de médicos ativos.
3. A Gerência quer saber em quais datas ocorreram consultas e quantas consultas aconteceram em cada data. A consulta deve retornar apenas um registro por data.
4. O Assistente de RH solicitou uma pesquisa que informe todas as consultas atendidas pelo médico Dr. Ricardo Falcão entre as 08h e as 09h.
5. A Diretoria solicitou uma pesquisa sobre consultas canceladas entre 01/03/2022 e 15/06/2022.
6. A Gerência quer saber quais agendamentos foram feitos com data maior ou igual a 10/01/2023 que ainda estão com status "Agendado".
7. O RH solicitou uma pesquisa para saber quais prontuários foram fechados antes de 01/03/2022.
8. A Gerência quer saber quais especialidades médicas existem na clínica, excluindo "Clínica Geral" e "Pediatria".
9. O RH solicitou uma pesquisa para saber quantos médicos existem na especialidade "Cardiologia".
10. A Diretoria quer identificar qual convênio possui a maior quantidade de pacientes vinculados, incluindo o nome da clínica de referência de cada paciente.
11. O Financeiro precisa saber qual especialidade tem a menor quantidade de médicos disponíveis e quantas consultas essa especialidade recebeu, para avaliar contratação de reforço.
12. O RH precisa identificar a quantidade total de consultas atendidas por cada médico ativo.

### Bloco 2 - JOINs, agregações, subqueries (Questões 13 a 25)

13. A Diretoria quer identificar as especialidades com o maior número de consultas realizadas, agrupadas por unidade/clínica.
14. A recepcionista Bianca Ortiz solicitou a alteração da especialidade do Dr. Eduardo Nogueira para "Ortopedia".
15. A recepção solicitou a alteração do bairro do paciente "Marcos Vinicius" que morava no bairro "Tatuapé" e agora mora em "Vila Mariana".
16. A clínica identificou uma falha no sistema de estoque de vacinas: as vacinas "Influenza", "Hepatite B" e "Febre Amarela" tiveram 5 doses cada descontadas indevidamente do estoque — será necessário reverter (somar de volta) essa quantidade.
17. A recepcionista Camila Duarte não conseguiu terminar o cadastro de cinco pacientes que passaram pela clínica. Insira esses pacientes (endereço primeiro, pois paciente referencia endereço).
18. A Gerência solicitou uma consulta para verificar se existe duplicidade de pacientes cadastrados (mesmo CPF).
19. A recepção identificou que existem dois cadastros duplicados de pacientes; será necessária a exclusão de um dos registros.
20. O Financeiro solicitou a criação de um campo `Valor_Consulta` na tabela `Consulta`. Defina o tipo de dado adequado.
21. A Consultoria verificou que o campo `Tipo_Convenio` está com tamanho VARCHAR(2) e solicitou alteração para VARCHAR(3).
22. Foi identificado que o campo `Retorno` está preenchido de forma errada: alterar registros "0" para "Não" e "1" para "Sim".
23. A Diretoria solicitou a exclusão do campo `Valor_Consulta` da tabela `Consulta`, pois o pedido do Financeiro estava incorreto.
24. A Diretoria solicitou a lista de todas as consultas que foram remarcadas com atraso em relação à data original agendada, junto com o nome do médico responsável.
25. A Gerência solicitou a lista de todos os exames cujos laboratórios não são da rede própria, já entregues, e o valor total de cada exame.

### Bloco 3 - Relatórios de negócio (Questões 26 a 39)

26. O RH solicitou a lista de todos os pacientes que tiveram consulta com data de atendimento igual a 21/08/2022.
27. O Financeiro solicitou a lista de todas as consultas com data de agendamento anterior a 04/03/2023, sua respectiva especialidade e o valor cobrado.
28. O RH solicitou a lista de todos os médicos separados por ativos ou inativos, com respectivas especialidades e salários.
29. A Gerência solicitou uma lista de todas as consultas com os nomes dos médicos, especialidades e o valor total faturado por médico.
30. A Diretoria solicitou a lista de todos os médicos com seus respectivos departamentos/unidades que têm idade entre 30 e 50 anos.
31. O Financeiro solicitou a criação de uma view que retorne o nome do médico, sua especialidade e o valor médio cobrado por consulta.
32. A recepção solicitou uma lista com o código e nome do exame cujo valor seja maior que R$ 200,00.
33. O RH solicitou a atualização do salário de todos os médicos da especialidade "Pediatria" em 10% sobre o salário atual.
34. O Financeiro solicitou a atualização da data de desligamento da médica Dra. Alice Meire para o último dia do mês atual.
35. A Gerência solicitou uma lista de todos os pacientes cujo nome contenha a letra "C", ordenados por bairro, com data de cadastro entre 2021 e 2023.
36. A recepção solicitou uma lista de todos os médicos com código, nome e especialidade, ordenada pelo nome, que não atenderam nenhuma consulta.
37. A clínica solicitou uma lista que exiba a quantidade de pacientes agrupados por CEP.
38. A Diretoria solicitou uma lista que exiba a quantidade de endereços agrupados por paciente.
39. Foi solicitada a busca de todas as consultas agendadas no dia 18/08/2022 às 15:00 e o nome do médico responsável.

### Bloco 4 - Consultas avançadas (Questões 40 a 58)

40. O Financeiro solicitou o levantamento de quando as pacientes Emily Souza e Whitney Lins tiveram consultas, quais especialidades e o respectivo valor.
41. Hoje é aniversário da rede de clínicas; o gerente Carlos Mendes pediu a relação do primeiro paciente a agendar, ser atendido e receber alta, com seus dados de contato.
42. A Diretoria quer saber quantos médicos cada especialidade tem na clínica.
43. Fechamento referente a janeiro (hoje é 03/02/2023): o Financeiro pediu um levantamento de todas as consultas com pagamento em atraso, mostrando quantos dias de atraso, multa diária de R$ 5,00, nome do paciente, especialidade e total a receber.
44. Foi solicitada a informação de todos os pacientes que moram em uma avenida, mostrando nome, CPF e logradouro, ordenado por CPF decrescente.
45. Mensagem urgente da diretoria: relatório de todos os médicos que atenderam mais de 50 consultas entre 2021 e 2023, agrupados por especialidade.
46. O Financeiro pediu o valor médio das consultas e quais especialidades estão com valor abaixo dessa média.
47. O RH pediu a média salarial dos médicos e quem ganha acima da média em cada especialidade.
48. Pesquisa de todos os pacientes cadastrados que nunca tiveram consulta, mostrando o nome em maiúsculas.
49. Verificar quais pacientes já tiveram mais de 3 consultas, mostrando nomes e especialidades, ordenado por CEP crescente.
50. Análise de agenda: lista de todos os médicos que já tiveram horários reservados e atendidos, mostrando quantos horários ainda estão disponíveis, classificados por especialidade.
51. Pesquisa de qual o horário de maior e menor movimento da clínica, avaliando todos os horários de consulta, exame e agendamento.
52. Levantamento dos 3 médicos com mais consultas realizadas em 2022 e 2023, e os 2 com menos.
53. Criar uma tabela/relatório que exiba a lista de consultas por médico, total atendido e total de reagendamentos.
54. Verificar quais pacientes possuem o mesmo endereço de algum convênio parceiro (indício de conflito de interesse), mostrando nome, telefone e convênio.
55. Criação de uma view que traga todas as consultas e o valor cobrado pelo convênio "SaúdeMais".
56. O convênio "VidaPlus" reajustou os valores de consulta em 12%; atualize os preços das consultas cobertas por esse convênio.
57. Apresentar as 5 especialidades com menos consultas e as 5 com mais, mostrando médico, unidade, especialidade e quantidade, em ordem decrescente.
58. A diretoria pediu a relação de todos os pacientes cujo CPF começa com "193", que receberão um brinde de aniversário da rede (193 meses de fundação).

---

## Observações para modelagem

- Entidades associativas que valem a pena forçar no modelo: `Medico_Especialidade` (N:N, um médico pode ter mais de uma especialidade), `Convenio_Clinica` (N:N, um convênio pode atender várias unidades).
- `Consulta` deve carregar FKs para `Paciente`, `Medico`, `Clinica` e opcionalmente `Convenio`.
- `Prescricao` e `Exame` dependem de `Consulta` (1:N a partir de Consulta).
- `Prontuario` pode ser 1:1 com `Consulta` (um prontuário fechado por atendimento), bom exemplo pra discutir cardinalidade 1:1 vs 1:N no DER.