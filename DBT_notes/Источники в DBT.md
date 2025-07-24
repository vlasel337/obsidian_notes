**Источники** - это все таблицы с исходными данными, которые dbt не контролирует.

Источники - это сырые таблицы, пришедшие в DBT извне, с них начинается Data Lineage.
Источники в DBT как правило вводятся в проект в папке `models` в файле `properties.yml`. 

Пример описания источника в `properties.yml`:
```yml
sources:
	- name: "scooters_raw"
	  description: "Raw data provided by scooters service (scheme name)"
	  loader: "https://t.me/inzhenerka_dbt_bot"
	  tables:
		  - name: "trips"
		    description: "Scooter trips"
```

Для обращения к зарегистрированным источникам в моделях можно через [[Полезные Jinja макросы в DBT|макрос]]: 
`{{ source("<name>", "<table_name>") }}

