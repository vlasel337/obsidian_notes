В dbt есть команда `show` для запуска select запросов, но она не подходит для запуска DDL запросов или запросов, связанных с управлением правами доступа.

Для запуска **произвольных SQL-запросов** в БД из DBT существует команда:
`dbt run-operation operation_name`
Также в этой команде прямо из терминала можно передавать в SQL-скрипт аргументы посредством флага `--args`:
`dbt  operation_name --args "key:value"`

Эта команда умеет запускать не модели, а особые [[Макросы в DBT|макросы]] со специальной конструкцией:
`{% do run_query(…) %}`
Внутри этого макроса как раз и описывается скрипт SQL-запроса.

Пример макроса для создание ролей в БД:
```sql
{% macro create_role(name) %} 
	{% set query %} 
		create role {{ name }} nologin;
	{% endset %} 
	{% do log("Creating role: " ~ name, info=True) %} 
	{% do run_query(query) %} 
{% endmacro %}
```
Пояснение:
- через конструкцию `set`/`endset` в переменную `query` записывается тело запроса;
- в запрос передается внешний аргумент `{{ name }}`, который прилетает из флага `--args`;
- расширение `do` вызывает функцию `log()` для выведения информационного сообщения в консоль;
- также расширение `do` запускает запрос, передавая в функцию `run_query()` переменную `query` инициализированную ранее, после чего запрос отрабатывает в БД.

Макрос `create_role()`, как и любой другой необходимо зарегистрировать в файле `properties.yml`:
```yml
macros:
	- name: "create_role" 
	  description: "Create role for users as dbt operation" 
	  arguments: 
		  - name: "name" 
		    type: "string" 
		    description: "Role name"
```

Макрос можно вызвать и передать в него аргумент посредством команды:
`dbt run-operation create_role --args "name: finance"`