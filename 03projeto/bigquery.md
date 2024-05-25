# Processamento e analises:

# Processamento e preparação da base de dados

**Consulta para identificar valores nulos**

```jsx

##CONSULTA PARA IDENTIFICAR NULL##

SELECT  *
 FROM `projeto-risco-relativo-422616.origem.default` 
 WHERE user_id IS NULL OR default_flag IS NULL
 ##SEM NULL##

 SELECT *
 FROM `projeto-risco-relativo-422616.origem.loans_default`
 WHERE user_id IS NULL OR more_90_days_overdue IS NULL OR using_lines_not_secured_personal_assets IS NULL OR number_times_delayed_payment_loan_30_59_days IS NULL OR debt_ratio IS NULL OR number_times_delayed_payment_loan_60_89_days IS NULL 
##SEM NULL##

 SELECT *
 FROM `projeto-risco-relativo-422616.origem.loans_outstanding`
 WHERE loan_id IS NULL OR user_id IS NULL OR loan_type IS NULL
##SEM NULL##

 SELECT*
 FROM `projeto-risco-relativo-422616.origem.user_info`
WHERE user_id IS NULL OR age IS NULL OR sex IS NULL OR last_month_salary IS NULL OR  number_dependents IS NULL 
##943 NULL NA COLUNA NUMBER_DEPENDENTS##
## 7199 NUMM NA COLUNA LAST_MONTH_SALARY##
```

<aside>
❗ Foram encontrados 943 nulos na coluna number_dependents e 7199 nulos na coluna last_month_salary

</aside>

**Left join para unir as tabelas default e user_info**

```jsx
SELECT defaultt.user_id, default_flag, age, sex, last_month_salary, number_dependents

 FROM `projeto-risco-relativo-422616.origem.default` AS defaultt  LEFT JOIN 
 
`projeto-risco-relativo-422616.origem.user_info` AS info ON info.user_id = defaultt.user_id

```

**Consulta para alterar os nulos da last month para mediana e number dependets**

```jsx
WITH salario AS (
  SELECT last_month_salary, number_dependents
  FROM `projeto-risco-relativo-422616.join.default_user_info`
)
SELECT 
  MIN(last_month_salary) AS salario_minimo, ##0.0##
  MAX(last_month_salary) AS salario_maximo, ## 1560100.0##
  AVG(last_month_salary) AS salario_media, ##6675.05##
  APPROX_QUANTILES(last_month_salary, 2)[OFFSET(1)] AS salario_mediana, ##5396.0##

  MIN(number_dependents) AS min_dependentes, ##0##
  MAX(number_dependents) AS max_dependentes, ##13##
  AVG(number_dependents) AS media_dependentes, ##0.75##
   APPROX_QUANTILES(number_dependents, 2)[OFFSET(1)] AS mediana_dependents ##0##
FROM 
  salario;

SELECT 
  *
FROM 
  (SELECT 
     *,
     COALESCE(last_month_salary, 5396.0) AS last_month_salary_,
     COALESCE(number_dependents, 0) AS number_dependents_
   FROM 
     `projeto-risco-relativo-422616.join.default_user_info`
  ) AS subquery

```

**Consulta para identificar vamores duplicados**

```jsx

SELECT COUNT(*)
FROM `projeto-risco-relativo-422616.tratados.default_user_info` 
GROUP BY user_id, default_flag,age,sex,last_month_salary_,number_dependents
HAVING COUNT (*) >1
##SEM DUPLICADOS##

 SELECT *
 FROM `projeto-risco-relativo-422616.origem.loans_default`
GROUP BY user_id, more_90_days_overdue,using_lines_not_secured_personal_assets,number_times_delayed_payment_loan_30_59_days, debt_ratio,number_times_delayed_payment_loan_60_89_days
HAVING COUNT (*) >1
##SEM DUPLICADOS##

SELECT user_id, 
FROM `projeto-risco-relativo-422616.origem.loans_outstanding`
GROUP BY user_id
HAVING COUNT(*) > 1;
##34510 DUPLICADOS#

```

<aside>
❗  foi encontrado valores duplicados somente no user_id da tabela loans_outstanding os ids estavam duplicados devido a tabela ser de transações de cada cliente, 34510 duplicados.

</aside>

**Consulta para calcular min, max, avg e mediana de todos os dados das tabelas.**

```jsx
SELECT 
  MIN(emprestimo_imoveis) AS minimo_imoveis, ##0##
  MAX(emprestimo_imoveis) AS maximo_imoveis, ##25##
  AVG(emprestimo_imoveis) AS media_imoveis, ##1.02##
  APPROX_QUANTILES(emprestimo_imoveis, 100)[SAFE_ORDINAL(50)] AS mediana_imoveis, ##1##
  STDDEV(emprestimo_imoveis) AS desvio_padrao_imoveis, ##1.11##

  MIN(emprestimo_outros) AS minimo_outros, ##0##
    MAX(emprestimo_outros) AS maximo_outros, ##56##
  AVG(emprestimo_outros) AS media_outros, ##7,55##
  APPROX_QUANTILES(emprestimo_outros, 100)[SAFE_ORDINAL(50)] AS mediana_outros, ##7##
  STDDEV(emprestimo_outros) AS desvio_padrao_outros, ##4,75##
  
  MIN(total_emprestimos) AS total_minimo, ##1##
  MAX (total_emprestimos) AS total_max, ##57##
  AVG(total_emprestimos) AS total_media, ##8,58##
  APPROX_QUANTILES(total_emprestimos, 100)[SAFE_ORDINAL(50)] AS total_outros, ##8##
  STDDEV(total_emprestimos) AS total_desvio_padrao, ##5,12##

FROM 
  `projeto-risco-relativo-422616.tratados.loans_outstanding`;

SELECT 
MIN (default_flag) AS min_default, ##0##
MAX (default_flag) AS max_default, ##1##
AVG(default_flag) AS media_default, ##0.01##
APPROX_QUANTILES(default_flag, 100)[SAFE_ORDINAL(50)] AS mediana_default, ##0##
STDDEV(default_flag) AS desvio_padrao_default,##0.13##

MIN(age) AS min_idade, ##21##
MAX(age) AS max_idade, ##109##
AVG(age) AS media_idade, ##52.41##
APPROX_QUANTILES(age, 100)[SAFE_ORDINAL(50)] AS mediana_idade, ##52##
STDDEV(age) AS desvio_padrao_idade, ##14.79##

MIN(last_month_salary_) AS min_salario, ##0##
MAX(last_month_salary_) AS max_salario, ##1560100.0##
AVG(last_month_salary_) AS media_salario, ##6419.2##
APPROX_QUANTILES(last_month_salary_, 100)[SAFE_ORDINAL(50)] AS mediana_salario, ##5396.0##
STDDEV(last_month_salary_) AS desvio_padrao_salario, ##11604.8##

MIN(number_dependents) AS min_dependentes, ##0##
MAX(number_dependents) AS max_dependentes, ##13##
AVG(number_dependents) AS media_dependentes, ##0,75##
APPROX_QUANTILES(number_dependents, 100)[SAFE_ORDINAL(50)] AS mediana_dependentes, ##0##
STDDEV(number_dependents) AS desvio_padrao_dependentes, ##1.11#

FROM `projeto-risco-relativo-422616.tratados.default_user_info`

SELECT
MIN(using_lines_not_secured_personal_assets) AS min_linhas_credito, ##0.0##
MAX(using_lines_not_secured_personal_assets) AS max_linhas_credito, ##22000.0##
AVG(using_lines_not_secured_personal_assets) AS media_linhas_credito, ##5.80##
APPROX_QUANTILES(using_lines_not_secured_personal_assets, 100)[SAFE_ORDINAL(50)] AS mediana_linhas_credito, ##0.14##
STDDEV(using_lines_not_secured_personal_assets) AS desvio_linhas_credito, ##223,40##

MIN(number_times_delayed_payment_loan_30_59_days) AS min_atraso, ##0##
MAX(number_times_delayed_payment_loan_30_59_days) AS max_atraso, ##98##
AVG(number_times_delayed_payment_loan_30_59_days) AS media_atraso, ##0.41##
APPROX_QUANTILES(number_times_delayed_payment_loan_30_59_days, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(number_times_delayed_payment_loan_30_59_days) AS desvio_atraso, ##4.14##

MIN(number_times_delayed_payment_loan_60_89_days) AS Min_atraso, ##0##
MAX(number_times_delayed_payment_loan_60_89_days) AS max_atraso, ##98##
AVG(number_times_delayed_payment_loan_60_89_days) AS media_atraso, ##0.23##
APPROX_QUANTILES(number_times_delayed_payment_loan_60_89_days, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(number_times_delayed_payment_loan_60_89_days) AS desvio_atraso, ##4.10##

MIN(more_90_days_overdue) AS Min_atraso, ##0##
MAX(more_90_days_overdue) AS max_atraso, ##98##
AVG(more_90_days_overdue) AS media_atraso, ##0.23##
APPROX_QUANTILES(more_90_days_overdue, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(more_90_days_overdue) AS desvio_atraso, ##4.12##

MIN(debt_ratio) AS min_endividamento, ##0## 
MAX(debt_ratio) AS max_endividamento, ##307001.0##
AVG(debt_ratio) AS media_endividamento,##351.580##
APPROX_QUANTILES(debt_ratio, 100)[SAFE_ORDINAL(50)] AS mediana_endividamento, ##0.35##
STDDEV(debt_ratio) AS desvio_endividamento, ##2011.3##

FROM `projeto-risco-relativo-422616.origem.loans_default`

##OS VALORES QUE ESTÃO ENTRE ##_## SÃO OS RESULTADOS PARA CADA LINHA##
```

**Consulta para identificar correlação entre as varíaveis** 

```jsx
SELECT 
CORR(more_90_days_overdue, number_times_delayed_payment_loan_60_89_days) AS corr_60_89,
##CORR 0,99##

CORR(more_90_days_overdue, number_times_delayed_payment_loan_30_59_days) AS corr_30_59,
##CORR 0,98##

CORR(number_times_delayed_payment_loan_30_59_days,number_times_delayed_payment_loan_60_89_days) AS correlacao,
##CORR 0,98##

CORR (using_lines_not_secured_personal_assets,debt_ratio) AS correlacao
##CORR 0,01##
FROM `projeto-risco-relativo-422616.origem.loans_default`

SELECT STDDEV_SAMP(number_times_delayed_payment_loan_30_59_days) AS loan_30_59 ,
       STDDEV_SAMP(more_90_days_overdue) AS more_90,
       STDDEV_SAMP(number_times_delayed_payment_loan_60_89_days) AS dias_89
FROM `projeto-risco-relativo-422616.origem.loans_default`
##DESVIO PADRAO LOAN_30_59 4.14 ## MORE_90 4.12## ##dias_89 4.10##

##OPTANDO POR UTILIZAR SOMENTE A VARIAVEL DIAS_89 POR TER UM DESVIO PADRÃO MENOR##

SELECT 
CORR(default_flag, last_month_salary_) AS correlacao,
##CORR -0,01##

CORR(default_flag, number_dependents) AS correlacao
##CORR 0,03##
FROM `projeto-risco-relativo-422616.tratados.default_user_info` 

SELECT CORR(emprestimo_imoveis, emprestimo_outros) AS correlacao, ##CORR 0.23##
CORR (taxa_endividamento, clientes_inadimplentes) as teste ##-0.007##
FROM  `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
```

**Consulta para arredondar os numeros das colunas abaixo.**

```jsx
SELECT *,
       ROUND(using_lines_not_secured_personal_assets, 2) AS linhas_nao_protegidas_por_bens_pessoais,
       ROUND(debt_ratio, 1) AS taxa_endividamento
FROM `projeto-risco-relativo-422616.origem.loans_default`;

##CONSULTA PARA ARREDONDAR OS NUMEROS NAS RESPECRIVAS COLUNAS##
```

**Consulta para tratar dados inconsistentes**

```

SELECT DISTINCT loan_type
FROM `risco-relativo-421812.origem.loans_outstanding`
##CONSULTA PARA IDENTIFICAR DADOS INCONSISTENTES##

WITH emprestimos AS (
  SELECT DISTINCT 
    user_id,
    CASE
      WHEN LOWER(loan_type) = 'real estate' THEN 'Real estate'
      WHEN LOWER(loan_type) = 'other' THEN 'Other'
      WHEN LOWER(loan_type) =  'others' THEN 'Other'
      WHEN LOWER(loan_type) = 'REAL ESTATE' THEN 'Real estate'
      WHEN LOWER(loan_type) = 'Real Estate' THEN 'Real estate'
      WHEN LOWER(loan_type) = 'Other' THEN 'Other'
      WHEN LOWER(loan_type) = 'OTHER' THEN 'Other'
      ELSE loan_type 
    END AS loan_type_clean
  FROM
    `risco-relativo-421812.origem.loans_outstanding`
)
##CONSULTA PARA TRATAR DADOS INCONSISTENTES##

SELECT 
  user_id,
  SUM(CASE WHEN loan_type_clean = 'Real estate' THEN 1 ELSE 0 END) AS emprestimo_imoveis,
  SUM(CASE WHEN loan_type_clean = 'Other' THEN 1 ELSE 0 END) AS emprestimo_outros,
  COUNT(*) AS total_emprestimos
FROM 
  emprestimos
GROUP BY 
  user_id;

##CONSULTA PARA CRIAR NOVAS VARIAVEIS##
```

**Consulta para verificar se existem outliers**

```jsx
SELECT 
  MIN(emprestimo_imoveis) AS minimo_imoveis, ##0##
  MAX(emprestimo_imoveis) AS maximo_imoveis, ##25##
  AVG(emprestimo_imoveis) AS media_imoveis, ##1.02##
  APPROX_QUANTILES(emprestimo_imoveis, 100)[SAFE_ORDINAL(50)] AS mediana_imoveis, ##1##
  STDDEV(emprestimo_imoveis) AS desvio_padrao_imoveis, ##1.11##

  MIN(emprestimo_outros) AS minimo_outros, ##0##
    MAX(emprestimo_outros) AS maximo_outros, ##56##
  AVG(emprestimo_outros) AS media_outros, ##7,55##
  APPROX_QUANTILES(emprestimo_outros, 100)[SAFE_ORDINAL(50)] AS mediana_outros, ##7##
  STDDEV(emprestimo_outros) AS desvio_padrao_outros, ##4,75##
  
  MIN(total_emprestimos) AS total_minimo, ##1##
  MAX (total_emprestimos) AS total_max, ##57##
  AVG(total_emprestimos) AS total_media, ##8,58##
  APPROX_QUANTILES(total_emprestimos, 100)[SAFE_ORDINAL(50)] AS total_outros, ##8##
  STDDEV(total_emprestimos) AS total_desvio_padrao, ##5,12##

FROM 
  `projeto-risco-relativo-422616.tratados.loans_outstanding`;

SELECT 
MIN (default_flag) AS min_default, ##0##
MAX (default_flag) AS max_default, ##1##
AVG(default_flag) AS media_default, ##0.01##
APPROX_QUANTILES(default_flag, 100)[SAFE_ORDINAL(50)] AS mediana_default, ##0##
STDDEV(default_flag) AS desvio_padrao_default,##0.13##

MIN(age) AS min_idade, ##21##
MAX(age) AS max_idade, ##109##
AVG(age) AS media_idade, ##52.41##
APPROX_QUANTILES(age, 100)[SAFE_ORDINAL(50)] AS mediana_idade, ##52##
STDDEV(age) AS desvio_padrao_idade, ##14.79##

MIN(last_month_salary_) AS min_salario, ##0##
MAX(last_month_salary_) AS max_salario, ##1560100.0##
AVG(last_month_salary_) AS media_salario, ##6419.2##
APPROX_QUANTILES(last_month_salary_, 100)[SAFE_ORDINAL(50)] AS mediana_salario, ##5396.0##
STDDEV(last_month_salary_) AS desvio_padrao_salario, ##11604.8##

MIN(number_dependents) AS min_dependentes, ##0##
MAX(number_dependents) AS max_dependentes, ##13##
AVG(number_dependents) AS media_dependentes, ##0,75##
APPROX_QUANTILES(number_dependents, 100)[SAFE_ORDINAL(50)] AS mediana_dependentes, ##0##
STDDEV(number_dependents) AS desvio_padrao_dependentes, ##1.11#

FROM `projeto-risco-relativo-422616.tratados.default_user_info`

SELECT
MIN(using_lines_not_secured_personal_assets) AS min_linhas_credito, ##0.0##
MAX(using_lines_not_secured_personal_assets) AS max_linhas_credito, ##22000.0##
AVG(using_lines_not_secured_personal_assets) AS media_linhas_credito, ##5.80##
APPROX_QUANTILES(using_lines_not_secured_personal_assets, 100)[SAFE_ORDINAL(50)] AS mediana_linhas_credito, ##0.14##
STDDEV(using_lines_not_secured_personal_assets) AS desvio_linhas_credito, ##223,40##

MIN(number_times_delayed_payment_loan_30_59_days) AS min_atraso, ##0##
MAX(number_times_delayed_payment_loan_30_59_days) AS max_atraso, ##98##
AVG(number_times_delayed_payment_loan_30_59_days) AS media_atraso, ##0.41##
APPROX_QUANTILES(number_times_delayed_payment_loan_30_59_days, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(number_times_delayed_payment_loan_30_59_days) AS desvio_atraso, ##4.14##

MIN(number_times_delayed_payment_loan_60_89_days) AS Min_atraso, ##0##
MAX(number_times_delayed_payment_loan_60_89_days) AS max_atraso, ##98##
AVG(number_times_delayed_payment_loan_60_89_days) AS media_atraso, ##0.23##
APPROX_QUANTILES(number_times_delayed_payment_loan_60_89_days, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(number_times_delayed_payment_loan_60_89_days) AS desvio_atraso, ##4.10##

MIN(more_90_days_overdue) AS Min_atraso, ##0##
MAX(more_90_days_overdue) AS max_atraso, ##98##
AVG(more_90_days_overdue) AS media_atraso, ##0.23##
APPROX_QUANTILES(more_90_days_overdue, 100)[SAFE_ORDINAL(50)] AS mediana_atraso, ##0##
STDDEV(more_90_days_overdue) AS desvio_atraso, ##4.12##

MIN(debt_ratio) AS min_endividamento, ##0## 
MAX(debt_ratio) AS max_endividamento, ##307001.0##
AVG(debt_ratio) AS media_endividamento,##351.580##
APPROX_QUANTILES(debt_ratio, 100)[SAFE_ORDINAL(50)] AS mediana_endividamento, ##0.35##
STDDEV(debt_ratio) AS desvio_endividamento, ##2011.3##

FROM `projeto-risco-relativo-422616.origem.loans_default`

##OS VALORES QUE ESTÃO ENTRE ##_## SÃO OS RESULTADOS PARA CADA LINHA##
```

<aside>
❗ Identificamos na coluna last_month_salary_, porém entendi que esse salario realmente poderia exister, optei por não mexer no dado, já nas colunas  number_times_delayed_payment_loan_30_59_days e number_times_delayed_payment_loan_60_89_days identifiquei que clientes ficaram devendo um maximo de 98 dias, porém nessas colunas eram de atrasos de no maximo 59 dias e na seguinte coluna 89 dias, provavelmente a quantidade de dias que o cliente ficou devendo foi digitada errada, porém esse cliente deve realmente ter atraso, resolvi considerar essas colunas.

</aside>

**Consulta para renomear colunas e fazer a junção.**

```jsx
SELECT 
info.user_id AS cliente_id, linhas_nao_protegidas_por_bens_pessoais, number_times_delayed_payment_loan_30_59_days AS atraso_pagamento_entre_30_59_dias, number_times_delayed_payment_loan_60_89_days AS atraso_pagamento_entre_60_89_dias, taxa_endividamento, default_flag AS clientes_inadimplentes ,age AS idade, last_month_salary_ AS ultimo_salario , number_dependents AS n_dependentes,emprestimo_imoveis, emprestimo_outros,total_emprestimos

 FROM `projeto-risco-relativo-422616.tratados.default` AS loans LEFT JOIN `projeto-risco-relativo-422616.tratados.default_user_info` AS info ON loans.user_id = info.user_id LEFT  JOIN `projeto-risco-relativo-422616.tratados.loans_outstanding`AS outstanding ON info.user_id = outstanding.user_id;

 ##RENOMIEI ALGUMAS COLUNAS E FIZ O LEFT JOIN PARA UNIFICAR AS PLANILHAS##
```

**Consulta para verificar se existem valores nulos após o join**.

```jsx

WITH tabela_temporaria AS (SELECT 
emprestimo_imoveis, emprestimo_outros, total_emprestimos
FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`)

SELECT *
FROM tabela_temporaria
WHERE emprestimo_imoveis IS NULL

##APOS A UNIFICAÇÃO DAS TABELAS APARECERAM NULL##

##OPTEI POR NÃO UTILIZAR OS VALORES NULOS NA ANÁLISE, POR SER SOMENTE 425 LINHAS O QUE SIGNIFICA UMA PORCENTAGEM DE 1,18% DA PORCENTAGEM TOTAL DOS DADOS.
```

**Consulta para calcular a correlação entre as variaveis após o join.**

```jsx
SELECT CORR(emprestimo_imoveis, emprestimo_outros) AS correlacao, ##CORR 0.23##
CORR (taxa_endividamento, clientes_inadimplentes) as teste ##-0.007##
FROM  `projeto-risco-relativo-422616.tratados.tabelas_unificadas
```

**Consulta para criar faixa etaria.**

```jsx
WITH faixas_etarias AS (
  SELECT cliente_id,
         idade,
         CASE
           WHEN idade BETWEEN 21 AND 29 THEN '21-29'
           WHEN idade BETWEEN 30 AND 39 THEN '30-39'
           WHEN idade BETWEEN 40 AND 49 THEN '40-49'
           WHEN idade BETWEEN 50 AND 59 THEN '50-59'
           WHEN idade BETWEEN 60 AND 69 THEN '60-69'
           WHEN idade BETWEEN 70 AND 79 THEN '70-79'
           WHEN idade BETWEEN 80 AND 89 THEN '80-89'
           ELSE '90+'
         END AS faixa_etaria
  FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
)
SELECT * FROM faixas_etarias;

```

**Consulta para calcular o quartil para todas as variaveis relevantes para o risco relativo e em seguida gerar a tabela.**

```jsx
WITH 
  quartil_idade AS (
    SELECT 
      NTILE(4) OVER (ORDER BY idade) AS quartil,
      idade,
      clientes_inadimplentes,
      ultimo_salario
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  ),
  quartil_n_dependentes AS (
    SELECT 
      NTILE(4) OVER (ORDER BY n_dependentes) AS quartil,
      n_dependentes,
      clientes_inadimplentes,
      ultimo_salario
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  ),
  quartil_total_emprestimos AS (
    SELECT 
      NTILE(4) OVER (ORDER BY total_emprestimos) AS quartil,
      total_emprestimos,
      clientes_inadimplentes,
      ultimo_salario
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  ),
   quartil_mais_90_dias_atraso AS (
    SELECT 
      NTILE(4) OVER (ORDER BY mais_90_dias_atraso) AS quartil,
      mais_90_dias_atraso,
      clientes_inadimplentes,
      ultimo_salario
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  ),
  quartil_ultimo_salario AS (
    SELECT 
      NTILE(4) OVER (ORDER BY ultimo_salario) AS quartil,
      ultimo_salario,
      clientes_inadimplentes
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  ),
  quartil_linhas AS (
    SELECT 
      NTILE(4) OVER (ORDER BY linhas_nao_protegidas_por_bens_pessoais) AS quartil,
      linhas_nao_protegidas_por_bens_pessoais,
      clientes_inadimplentes,
      ultimo_salario
    FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
  )

SELECT 
  'idade' as categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(idade) AS minimo,
  MAX(idade) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) as risco_relativo,
  CASE WHEN quartil = 1 THEN 'mau pagador'
       WHEN quartil = 2 THEN 'mau pagador'
       WHEN quartil = 3 THEN 'bom pagador'
       WHEN quartil = 4 THEN 'bom pagador'
  END AS classificacao
FROM quartil_idade 
GROUP BY quartil

UNION ALL

SELECT 
  'n_dependentes' as categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(n_dependentes) AS minimo,
  MAX(n_dependentes) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) as risco_relativo,
  CASE WHEN quartil = 1 THEN 'mau pagador'
       WHEN quartil = 4 THEN 'mau pagador'
       WHEN quartil = 3 THEN 'bom pagador'
       WHEN quartil = 2 THEN 'bom pagador'
  END AS classificacao
FROM quartil_n_dependentes 
GROUP BY quartil

UNION ALL

SELECT 
  'total_emprestimos' as categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(total_emprestimos) AS minimo,
  MAX(total_emprestimos) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) as risco_relativo,
  CASE WHEN quartil = 1 THEN 'mau pagador'
       WHEN quartil = 2 THEN 'mau pagador'
       WHEN quartil = 3 THEN 'bom pagador'
       WHEN quartil = 4 THEN 'bom pagador'
  END AS classificacao
FROM quartil_total_emprestimos 
GROUP BY quartil

UNION ALL

SELECT 
  'mais_90_dias_atraso' AS categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(mais_90_dias_atraso) AS minimo,
  MAX(mais_90_dias_atraso) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) AS risco_relativo,
  CASE 
    WHEN quartil = 4 THEN 'mau pagador'
    WHEN quartil IN (1, 2, 3) THEN 'bom pagador'
  END AS classificacao
FROM quartil_mais_90_dias_atraso
GROUP BY quartil

UNION ALL

SELECT 
  'ultimo_salario' as categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(ultimo_salario) AS minimo,
  MAX(ultimo_salario) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) as risco_relativo,
  CASE WHEN quartil = 1 THEN 'mau pagador'
       WHEN quartil = 2 THEN 'mau pagador'
       WHEN quartil = 3 THEN 'bom pagador'
       WHEN quartil = 4 THEN 'bom pagador'
  END AS classificacao
FROM quartil_ultimo_salario 
GROUP BY quartil

UNION ALL

SELECT 
  'linhas_nao_protegidas_por_bens_pessoais' as categoria,
  quartil,
  COUNT(*) AS total_pessoas,
  SUM(CASE WHEN clientes_inadimplentes = 1 THEN 1 ELSE 0 END) AS maus_pagadores,
  MIN(linhas_nao_protegidas_por_bens_pessoais) AS minimo,
  MAX(linhas_nao_protegidas_por_bens_pessoais) AS maximo,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) AS incidencia,
  AVG(CAST(clientes_inadimplentes AS FLOAT64)) / (SELECT AVG(CAST(clientes_inadimplentes AS FLOAT64)) FROM `projeto-risco-relativo-422616.tratados.tabelas_unificadas`) as risco_relativo,
  CASE WHEN quartil = 1 THEN 'bom pagador'
       WHEN quartil = 2 THEN 'mau pagador'
       WHEN quartil = 3 THEN 'bom pagador'
       WHEN quartil = 4 THEN 'mau pagador'
  END AS classificacao
FROM quartil_linhas 
GROUP BY quartil;

```

 

**Consulta para passar as variavies para dummy**

```jsx
WITH quartis AS (
  SELECT 
    *,
    NTILE(4) OVER (ORDER BY idade) AS quartil_idade,
    NTILE(4) OVER (ORDER BY linhas_nao_protegidas_por_bens_pessoais) AS quartil_linhas,
    NTILE(4) OVER (ORDER BY ultimo_salario) AS quartil_salario,
    NTILE(4) OVER (ORDER BY total_emprestimos) AS quartil_emprestimos,
    NTILE(4) OVER (ORDER BY n_dependentes) AS quartil_dependentes,
    NTILE(4) OVER (ORDER BY taxa_endividamento) AS quartil_taxa_endividamento,
    NTILE(4) OVER (ORDER BY mais_90_dias_atraso) AS quartil_90_dias
  FROM 
    `projeto-risco-relativo-422616.tratados.tabelas_unificadas`
),
dummy_totals AS (
  SELECT
    *,(CASE WHEN quartil_idade IN (1, 2) THEN 1 ELSE 0 END) AS idade_dummy,
    (CASE WHEN quartil_linhas IN (1, 2) THEN 1 ELSE 0 END) AS linhas_dummy,
    (CASE WHEN quartil_salario IN (1, 2) THEN 1 ELSE 0 END) AS salario_dummy,
    (CASE WHEN quartil_emprestimos IN (1, 2) THEN 1 ELSE 0 END) AS emprestimos_dummy,
    (CASE WHEN quartil_dependentes IN (1, 4) THEN 1 ELSE 0 END) AS dependentes_dummy,
    (CASE WHEN quartil_taxa_endividamento = 4 THEN 1 ELSE 0 END) AS taxa_dummy,
    (CASE WHEN quartil_90_dias = 4 THEN 1 ELSE 0 END) AS mais90_dias_dummy
  FROM 
    quartis
),
nova_tabela AS (
  SELECT
    *,
     clientes_inadimplentes + idade_dummy + linhas_dummy + salario_dummy + emprestimos_dummy + dependentes_dummy + taxa_dummy + mais90_dias_dummy AS score_dummy
  FROM
    dummy_totals
),
classificacao AS (
  SELECT 
    *,
    CASE WHEN score_dummy >= 6 THEN 'mau pagador' ELSE 'bom pagador' END AS classificacao_clientes
  FROM 
    nova_tabela
)
SELECT
  cliente_id,
  faixa_etaria,
  idade_dummy,
  clientes_inadimplentes,
  linhas_dummy,
  salario_dummy,
  emprestimos_dummy,
  dependentes_dummy,
  taxa_dummy,
  mais90_dias_dummy,
  score_dummy,
  classificacao_clientes
FROM
  classificacao;
```

<aside>
❗ posteriormente efetuar o score e a classificação. Optei por também transformar a variavel idade em dummy mesmo sendo continua, por entender que idade é uma variavel importante para  futuramente calcular o score.

</aside>

**Consulta para calcular a matriz de confusão**

```jsx
WITH matriz AS (
  SELECT 
    SUM(CASE WHEN clientes_inadimplentes = 1 AND classificacao_clientes = 'mau pagador' THEN 1 ELSE 0 END) AS verdadeiros_positivos,
    SUM(CASE WHEN clientes_inadimplentes = 0 AND classificacao_clientes = 'bom pagador' THEN 1 ELSE 0 END) AS verdadeiros_negativos,
    SUM(CASE WHEN clientes_inadimplentes = 0 AND classificacao_clientes = 'mau pagador' THEN 1 ELSE 0 END) AS falsos_positivos,
    SUM(CASE WHEN clientes_inadimplentes = 1 AND classificacao_clientes = 'bom pagador' THEN 1 ELSE 0 END) AS falsos_negativos
  FROM 
    `projeto-risco-relativo-422616.tratados.dm`
),

tabela AS (
  SELECT
    SUM(verdadeiros_positivos) AS verdadeiro_positivo,
    SUM(verdadeiros_negativos) AS verdadeiro_negativo,
    SUM(falsos_positivos) AS falso_positivo,
    SUM(falsos_negativos) AS falso_negativo,
    SUM(verdadeiros_positivos + verdadeiros_negativos) / SUM(verdadeiros_positivos + falsos_positivos + verdadeiros_negativos + falsos_negativos) AS acuracia,
    SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_positivos) AS precision,
    SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_negativos) AS recall,
    2 * (SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_positivos)) * (SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_negativos)) / ((SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_positivos)) + (SUM(verdadeiros_positivos) / SUM(verdadeiros_positivos + falsos_negativos))) AS f1_score
  FROM 
    matriz
)

SELECT * FROM tabela;

```

**Proximo passo exportar as tabelas relevantes para o google colab.**