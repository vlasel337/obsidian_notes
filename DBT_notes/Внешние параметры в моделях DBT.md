В [[Модели в DBT|модели]] DBT можно передавать внешние параметры. Это может быть полезно, если, например, в [[Инкрементальные модели в DBT|инкрементальную модель]] необходимо передать конкретную дату для пересчета витрины на конкретный день.

Параметры передаются в модель из двух источников:
1. Задаются на уровне проекта в словаре с переменными в конфигурационном файле проекта `dbt_project.yml`.
   Пример:
```yml
vars:  
	dt: '2016-06-01'
```
2. Передаются напрямую из терминала путем использования флага `--vars` при материализации модели. 
   Пример: `dbt build -s events_clean --vars "{dt: 2023-06-01}"`

В модель переменная попадает посредством макроса `var`, который состоит из двух элементов: 1) названия переменной, 2) ее значение по умолчанию, если оно не задано
Пример: `var('dt',none)`

Для того, чтобы использовать **внешнюю переменную** в модели можно объявить **локальную переменную** на уровне модели. Для этого существует макрос `set` (рекомендуется выносить его в начало скрипта модели).
Пример: `{% set date = var('dt', none) %}`

Если мы захотим обратиться к локальной переменной date, то это можно сделать так:
`'{{ date }}'`

Пример модели с использованием макросов var и set:
```sql
{% set date = var("date", none) %}
select distinct
    user_id,
    "timestamp",
    type_id,
    {{ updated_at() }}
from
    {{ source("scooters_raw", "events") }}
where
{% if is_incremental() %}
    {% if date %}
        date("timestamp") = date '{{ date }}'
    {% else %}
        "timestamp" > (select max("timestamp") from {{ this }})
    {% endif %}
{% else %}
    "timestamp" < timestamp '2023-08-01'
{% endif %}
```



