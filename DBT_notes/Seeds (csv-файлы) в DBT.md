Сиды позволяют самым простым образом решать задачу [[Инджестинг данных|инджестинга]] файлов - путем добавления csv-файлов в папку `seeds`. Сиды обычно используются для добавления в проект небольших файлов, которые не часто обновляются (например, словарика с типами продуктов или таблички с возрастными группами).

DBT позволяет для каждого сида сконфигурировать такие параметры, как:
- **delimeter** — тип разделителя, по умолчанию запятая;
- **column_types** — вручную задать типы данных колонок;
- **quote_columns** — названия колонок могут быть взяты в кавычки в DDL-запросе, что позволяет минимизировать проблемы и конфликты при создании таблицы.

Конфигурация сидов задается в файле `properties.yml`.
Пример регистрации сида в конфигурационном файле:
```yml
seeds:
	- name: "scooters"
	  description: "File with scooters models"
	  config:
		  delimeter: ","
```


Сиды материализуются командой (можно добавлять селектор -s):
`dbt seed -s scooters`

 Чтобы материализовать сид и **всю цепочку зависимостей после него** можно использовать плюсик:
`dbt build -s scooters+`

Обращение к сиду происходит, как и к другим моделям, через макрос:
`{{ ref("scooters") }}`



