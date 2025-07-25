В DBT существует 4 вида стратегий [[Инкрементальные модели в DBT|инкрементального обновления таблиц]]. Выбор каждой из них зависит от бизнес требований к обновлению таблицы, а также от того поддерживает ли БД тот или иной тип инкрементального обновления.

| Стратегия        | Описание                                                                                                                                                                                                                     | Указание ключа |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| Append           | Новые строки просто вставляются в таблицу посредством операции **insert**, дополняя старые. *Работает в DBT по умолчанию.*                                                                                                   | Не требуется   |
| Merge            | Строки из базовой таблицы, по которым находятся совпадения в инкрементальном срезе, обновляются операцией **update**.<br>Строки среза, по которым в базовой таблице не нашлось совпадения, добавляются операцией **insert**. | Требуется      |
| Delete+Insert    | Сперва из базовой таблицы происходит удаление записей, совпавших по ключу с записями из инкремента. Затем происходит вставка всех записей из инкрементального среза.                                                         | Требуется      |
| Insert_overwrite | Работает по аналогии со стратегией Delete+Insert, но удаление происходит не по ключу, а по целым блоками (партициями/файлам) в БД.                                                                                           | Не требуется   |

Пример внесения стратегии в файл конфигурации моделей `properties.yml`:
```yml
models:
	- name: "events_clean"
	  description: "Dedublicated events table"
	  config:
		  materialized: "incremental"
		  strategie: "merge"
		  unique_key: ["user_id","timestamp","type_id"]
```


## Поведение стратегии при изменении схемы модели
По умолчанию инкрементальные стратегии **игнорируют изменение атрибутного состава** моделей. Для того, чтобы это исправить необходимо изменить параметр `on_schema_change: append_new_columns` в конфигурационном файле `properties.yml`. 

Параметр может принимать следующие значения:
- **ignore** — не делать ничего с новыми колонками (вариант по умолчанию)
- **fail** — выдать ошибку, что схема таблицы изменилась
- **append_new_columns** — добавить новые колонки модели в таблицу
- **sync_all_columns** — добавить новые колонки и удалить столбцы, которых уже нет в модели (опасно потерей данных).

Пример адаптации стратегии к изменению схемы таблицы: 
```yml
models:
	- name: "revenue_daily"
	  description: "Daily revenue aggregate"
	  config:
		  materialized: "incremental"
		  strategy: "merge"
		  unique_key: [date]
```
