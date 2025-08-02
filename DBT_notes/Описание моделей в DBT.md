Для того, чтобы текстовое описание [[Модели в DBT|моделей]] и их колонок из `properties.yml` отображалось прямо в БД в конфигурационном файле DBT проекта `dbt_project.yml` можно включить опцию `persist_docs`.

SQL-запрос для просмотра описания всех таблиц в схемах:
```sql
select  
    ns.nspname as schema_name,  
    cls.relname as table_name,  
    des.description as table_description,  
    cls.relkind = 'r' as is_table  
from  
    pg_catalog.pg_class cls  
    join pg_catalog.pg_namespace ns  
      on ns.oid = cls.relnamespace  
    left join pg_catalog.pg_description des  
      on des.objoid = cls.oid  
      and des.objsubid = 0  
where  
    ns.nspname in ('dbt', 'dbt_finance')  -- change schemas name
    and cls.relkind in ('r', 'v') -- only tables and views  
order by  
    1,  
    2;
```

Пример (включаем отображение описаний и для таблиц, и для их колонок):
```yml
models:
	dbt_scooters_new:
	+materialized: table
	+on_schema_change: append_new_columns
	+persist_docs:
		relation: true
		columns: true
```

Для [[Seeds (csv-файлы) в DBT|сидов]] эта опция настраивается в конфигурационном файле проекта `dbt_project.yml` отдельно полностью аналогично:
```yml
seeds:
	dbt_scooters_new:
	+persist_docs:
		relation: true
		columns: true
```

Если описание модели/сида слишком объемное, его можно либо добавить в конфигурацию `properties.yml` в виде многострочного текста:
```yml
seeds:
	- name: "scooters"
	  description: >
		  Scooter models information and park statistics by model.
		  Includes information about the manufacturing company.
		  Data received from the service team and uploaded by Mark.
		  Data is current as of summer 2023.
	  config:
		  delimeter: ","
```

Либо подтянуть через отдельный файл с документацией в формате Markdown:
```yml
seeds:
	- name: "event_types"
	  description: "{{ doc('event_types') }}"
	  config:
		  delimeter: ","
```

Файл с документацией в формате Markdown должен храниться в отдельной папке `models/docs` или `seeds/docs` и должен быть обернут специальным макросом `{% docs model_name %}`/`{% enddocs %}`:
```sql
{% docs event_types %}
	-- Some text
{% enddocs %}
```
