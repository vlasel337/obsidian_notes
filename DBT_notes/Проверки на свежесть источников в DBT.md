В DBT существует концепция свежести (**freshness**) данных в [[Источники в DBT|источниках]], добавленных в проект. Параметры и критерии свежести могут быть заданы как для всего источника, так и для его отдельных таблиц.

Статус свежести определяется на основании данных из специально определенной регулярно обновляемой timestamp-колонки источника. Она конфигурируется для источника в файле `properties.yml` в опции **loaded_at_field**. Границы свежести/несвежести настраиваются вручную в этой же конфигурации источника.

При формировании правил свежести источника обычно устанавливают 2 порога свежести источника:
1. Первая (**warn_after**) - та, после которой нужно вывести **предупреждение** (warn) о том, что что-то идет не так.
2. Вторая (**error_after**) - та, после которой нужно вывести сообщение об **ошибке** (error).
Если данные в источнике окажутся несвежими, то в консоль будет сперва направлено соответствующее сообщение с предупреждением, а затем, если проблема не будет устранена, то с ошибкой.

Пример конфигурации свежести (**freshness**) источника в `properties.yml`:
```yml
sources:
	- name: "scooters_raw"
	  description: "Raw data provided by scooters service (the name of the scheme)"
	  loader: "https://t.me/inzhenerka_dbt_bot"
	  tables:
		  - name: "trips"
		    description: "Scooter trips"
		    loaded_at_field: "finished_at"
		    freshness:
				warn_after:
					count: 1
					period: "day"
				error_after:
					count: 3650
					period: "day"
```

При вычислении нахождения относительно порогов свежести DBT берет текущее время базы и сравнивает его с последним значением по колонке, заданной по атрибуту **loaded_at_field**.

Проверка свежести всех источников запускается командой:
`dbt source freshness`.

Важный момент: команда `dbt build` не проверяет свежесть источников, поэтому команду для проверки свежести источников необходимо запускать отдельно перед ней.
