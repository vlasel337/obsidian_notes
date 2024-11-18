## Микросервисы
*Микросервис* - нелбольшое атомарное приложение, отвечающее за конкретную бизнес-задачу: аутентификация, платеж, функция поиска и фильтрации. Приложение-микросервис независимо от других микросервисов и не имеют доступа к базам друг друга.

Преимущества микросервисов:
- увеличение отказоустойчивости
- простота доработки и поддержки

Микросервисы общаются между собой по сети через REST, http, gRPC или Kafka.
Запросы от сервиса к сервису могут быть:
- синхронными
- асинхронными (не дожидаться ответа)
## Устройство Kafka

Kafka позволяет:
- настроить отправку сообщений сразу же на множество получателей
- не заниматься дополнительной настройкой доставки сообщений в каждое новое приложение-адресат (не нужно затрагивать существующий код)
- реализовать асинхронную доставку сообщений консьюмерам
- не терять сообщения, отправленные в сервисы, которые на момент отправки отвалились по таймауту

*Event* - сообщение с данными. EVENTы в Kafka - неизменяемые и неудаляемые.

*Broker* - это сервер, который принимает сообщения (EVENTы) от PUBLISHERов, хранит их и передает CONSUMERам. Могут существовать в виде одного сервера или кластера из нескольких инстансов, что позволяет увеличить отказоустойчивость.

*Topic* - логическая категория, которая находится внутри BROKERа и позволяет организовывать хранение данных в Kafka. По умолчанию TOPIC настроен на хранение EVENTов в течение 7 дней.

*Partition* - логические порции данных, которые находятся внутри TOPICов и разделяют их на "строки". CONSUMERы считывают партиции в параллельном порядке. В рамках конкретной партиции сообщения считываются по порядку. Новые сообщения всегда добавляются в конец партиций.

*Offset* - индекс сообщения в партиции.

Message в Kafka состоит из:
- Key (если для разных сообщений указать одитн и тот же Key, они запишутся в одну и ту же партицию)
- Event (JSON, string)
- Timestamp
- Headers (key-value пары с данными, например, для прохождения аутентификации)

## Протокол REST
## Event-driven архитектура

Event-driven архитектура - это архитектура, которая соответствует ряду требований:
- асинхронное взаимодействие 
- слабые связи
- гибкость изменений
- легкое масштабирование
- при восстановлении consumer может обработать полученные сообщения
- producer не ждет обработки сообщений и не знает, кто их обрабатывает