Тесты запускаются при помощи специальной команды:
`dbt test` (возможно добавление селектора `-s`).

В DBT существует два основных вида тестов: отдельные и обобщенные.
## Отдельные тесты (сингулярные)
Отдельные тесты (**сингулярные**) - это отдельные файлы с SQL-скриптами, которые хранятся в папке `tests`. В SQL-скрипте мы нарочно пытаемся сделать выборку кривых данных (например, nulls). Если результат запроса выдаст непустой результат, то тест сработает и прервет пайплайн. Сингулярные тесты по своей сути напоминают модели, только они в отличие от моделей не материализуются.
   
Пример простого сингулярного теста:
```sql
select * 
from {{ source("scooters_raw", "users") }} 
where sex is null
```

Пример сингулярного теста на совпадение количества строк в двух таблицах:
```sql
with source_cte as (
	select count(*) as row_count
	from {{ ref("events_clean") }}
),
target_cte as (
	select count(*) as row_count
	from {{ ref("events_full") }}
)
select
	'Row count mismatch' as error_message,
	source_cte.row_count as source_count,
	target_cte.row_count as target_count
from source_cte, target_cte
where source_cte.row_count != target_cte.row_count
```

## Обобщенные тесты (дженерики)
Обобщенные тесты (**дженерики**) - тесты представляющие из себя шаблонизированный SQL скрипт, параметры в который можно передать из конфигурации модели. Обобщенные тесты должны храниться в папке `tests/generic`.
   
Примеры простого дженерик теста:
```sql
{% macro column_not_null(model, column_name) %}
	select * from {{ model }} 
	where {{ column_name }} is null 
{% endmacro %}
```

```sql
{% test unique_not_null(model, column_name) %} 
	-- Put SQL here 
{% endtest %}
```

Тесты регистрируются в блоке конфигурационного файла `data_tests` и могут быть заданы:
- как на уровне всей модели, 
- так и на уровне отдельной колонки модели.

Применяется обобщенный тест **для конкретной колонки** в конфигурации модели/источника следующим образом (если макрос принимает модель и колонку):
```yml
sources: 
	- name: "scooters_raw"
	  tables: 
		  - name: "users" 
		    columns: 
			    - name: "sex" 
			      data_tests: 
				      - "column_not_null"

```

Если тест применяется целиком **к конкретной таблице**, то это выглядит так (если макрос принимает только модель):
```yml
models: 
	- name: "trips_prep"
	  data_tests: 
		  - "all_columns_not_null"
```

Наряду с проверуой **not_null** у дженерик тестов в DBT существует 4 вида готовых проверок, доступных из коробки:
1. **unique** - проверка колонки на уникальность (отсутствие дублей);
2. **not_null** - проверка на отсутствие пропусков (nulls);
3. **accepted_values** - проверка на попадание значений в определенный заранее указанный в конфигурации список разрешенных значений (**игнорирует nulls**);
4. **relationships** - проверка, что при объединении таблиц не произошло потери данных.

## Важность тестов (severity)
Тесты в DBT различаются по уровню важности (**severity**). 
По умолчанию каждый тест имеет уровень важности severity: error.

От уровня важности зависит поведение пайплайна:
- Если уровень важности severity = error, то в случае срабатывания **пайплайн прервется** с ошибкой
- Если уровень важности severity = warn, то в случае срабатывания появится предупреждение, но **пайплайн не будет прерван**.

Уровень важности проверки можно изменить в конфиге модели в файле `properties.yml`:
```yml
models:
	- name: "users"
	  description: "Users of scooter service"
	  columns:
		  - name: "sex"
		    description: "User gender"
		    data_tests:
			    - accepted_values:
				      values: ["M","F"]
				- not_null:
					  config:
						  severity: "warn"
```

## Тест на уникальность (UNIQUE)
Пример проверки на уникальность значений **одной колонки** модели:
```yml
models:
	- name: "companies_trips"
	  description: "Number of trips by companies"
	  columns:
		  - name: "company"
		    data_tests:
			    - unique
```

Если требуется проверять на уникальность ключ, состоящий из **нескольких колонок**, то конфиг будет выглядеть так:
```yml
models:
	- name: "trips_age_daily"
	  description: "Daily trips statistics by user age"
	  data_tests:
		  - unique:
		        column_name: "date::text || age::text"
```

## Тест на согласованность двух таблиц (Relationships)
Пример теста на согласованность данных двух таблиц посредством проверки того, что данные из колонки user_id источника "**trips**" полностью содержатся в колонке id источника "**users**":
```yml
sources:
	- name: "scooters_raw"
	  description: "Raw data provided by scooters service (the name of the scheme)"
	  loader: "https://t.me/inzhenerka_dbt_bot"
	  tables:
		  - name: "trips"
		    description: "Scooter trips"
			columns:
				- name: "user_id"
				  data_tests:
					  - not_null
					  - relationships:
							name: "every_trip_has_user"
							to: "source('scooters_raw', 'users')"
							field: "id"
```

Запустить тест для [[Источники в DBT|источника]] можно командой:
`dbt test -s source:scooters_raw.trips`