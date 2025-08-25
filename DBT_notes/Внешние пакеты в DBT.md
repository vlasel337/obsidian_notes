Внешние пакеты (**packages**) для DBT содержат готовую бизнес-логику (тесты, макросы), которую можно переиспользовать в проекте. 

Пакеты DBT можно найти на специализированном ресурсе [dbt Hub](https://hub.getdbt.com/).

Важно помнить, что внешние пакеты поддерживают только ограниченный перечень БД, поэтому перед установкой пакета нужно проверять подходит ли он для используемой в проекте БД.
### Установка внешнего пакета в проект
Внешние пакеты добавляются в формате: `имя_автора/имя_пакета` через файл `dependencies.yml`, расположенный в корне проекта:
```yml
packages: 
	- package: dbt-labs/dbt_utils 
	  version: 1.3.0
```

Для подключения пакетов из файла `dependencies.yml` можно воспользоваться командой:
`dbt deps`

Для подключения конкретного пакета прямо из терминала можно использовать команду:
`dbt deps --add-package "calogica/dbt_expectations@0.10.4"`

Запуск этой команды:
1. Создает в корне проекта папку `dbt_packages`, в которой хранятся все подключенные внешние пакеты (ее нужно добавить в `.gitignore`, чтобы не перегружать репозиторий).
2. Создает в корне проекта файл `packages-lock.yml`, в котором перечислены все установленные пакеты с их версиями и контрольными суммами.

Перед деплоем проекта с внешней зависимостью на удаленный сервер необходимо сообщить ему в файле `deploy_docs.yml`, что в проекте используются внешние зависимости:
```yml
- name: Install requirements 
  run: | 
	  pip install -r requirements.txt 
	  dbt deps
```

При создании [[Каталог данных в DBT|каталога данных]] для проекта с внешним пакетом внешний пакет будет отображаться в левой панели каталога:
![[DBT External packages.png|200]]
### Удаление внешнего пакета из проекта
Чтобы удалить пакет из проекта необходимо действовать по следующей инструкции:
1. Удалить пакет из файла `dependencies.yml`.
2. Запустить команду `dbt deps`, чтобы обновить файл `package-lock.yml`.
3. Удалить папку пакета из директории `dbt_packages` (или удалить папку `dbt_packages` целиком, а потом запустить команду `dbt deps`)

# Пакет dbt_utils
Один из самых популярных внешних пакетов dbt, который содержит множество тестов и макросов: [dbt_utils](https://hub.getdbt.com/dbt-labs/dbt_utils/latest/).

Так, например, в пакете содержится [[Макросы в DBT|макрос]] `pivot`, который позволяет реализовывать логику разворачивания столбцов, как в сводных таблицах:
```sql
select
	age,
	{{ dbt_utils.pivot("sex", ["M","F"]) }}
from {{ ref("trips_users") }}
group by age
order by age
```

Также пакет содержит множество [[Тесты в DBT#Обобщенные тесты (дженерики)|дженерик тестов]]. В качестве примера можно рассмотреть тест `not_constant`, который проверяет, что в колонке содержатся разнородные значения (более одного уникального значения):
```yml
model:
	- name: "sex_age_pivot"
	  description: "Trips per age pivoted by sex"
	  data_tests:
		  - unique_key:
			    columns: [ "age" ]
	  columns:
		  - name: "age"
		    description: "Numerical age of user"
		    data_tests:
			    - "dbt_utils.not_constant"
```

# Пакеты dbt_expectations, dbt_date
Популярный пакет, в котором содержится более 60 различных тестов: [dbt_expectations](https://hub.getdbt.com/calogica/dbt_expectations/latest/).
Вместе с пакетом dbt_expectations также автоматически устанавливается пакет [dbt_date](https://hub.getdbt.com/calogica/dbt_date/latest/), который содержит макросы для работы с датами.

Для корректной работы макросов из пакета dbt_date необходимо в конфигурационном файле проекта `dbt_project.yml` установить часовую зону по умолчанию:
```yml
vars: 'dbt_date:time_zone': 'Europe/Moscow'
```

Пакет dbt_expectations содержит такие тесты, как, например, тест [expect_table_column_count_to_equal](https://github.com/calogica/dbt-expectations#expect_table_column_count_to_equal) для проверки количества колонок в таблице:
```sql
models:
	- name: "sex_age_pivot"
	  description: "Trips per age pivoted by sex"
	  data_tests:
		  - dbt_expectations.expect_table_column_count_to_equal:
			    value: 3
```

# Пакет dbt_product_analytics
Пакет с макросами, в котором содержатся готовые решения для продуктовой аналитики ([витрина стриминга ивентов](https://github.com/mjirv/dbt_product_analytics?tab=readme-ov-file#event_stream-source), воронки, [ретеншены](https://github.com/mjirv/dbt_product_analytics?tab=readme-ov-file#retention-source) и тд): [dbt_product_analytics](https://hub.getdbt.com/mjirv/dbt_product_analytics/latest/).

Пакет устанавливается командой:
`dbt deps --add-package "mjirv/dbt_product_analytics@0.3.1"`

В пакете доступен макрос для создания таблицы со стримингом ивентов:
```sql
{{  dbt_product_analytics.event_stream(
	from=ref("events_full"),
	event_type_col="type",
	user_id_col="user_id",
	date_col='date("timestamp")'
) }}
```

И создания витрины ретеншенов:
```sql
{{  dbt_product_analytics.retention(
	event_stream=ref('events_stream'),
	first_action='start_search',
	second_action='book_scooter',
)}}
```




