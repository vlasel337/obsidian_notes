**Модели** также, как и [[Источники в DBT|источники]] описываются в файле `properties.yml`.

Пример описания модели:
```yml
models:
	- name: "trips_stat_daily"
	  description: "Daily trips statistics"
	  config:
		  materialized: view
		  indexes:
			  - columns: ["date"]
```

Для **ссылки** из модели **на другую существующую модель** используется [[Jinja макросы в DBT|макрос]]:
  `{{ ref("ref_table_name") }}

Пайплайн материализации обычной модели (для адаптера postgres):
![[Пайплайн материализации обычной модели.png]]