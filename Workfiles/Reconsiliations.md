Тред про R2: https://indriver.slack.com/archives/C093GLT42CE/p1754403585171839
Тред с Emprex: https://indriver.slack.com/archives/C095F4T31NK/p1752585371889319
Гайд по реконсиляциям от Даши:
https://docs.google.com/spreadsheets/d/1GxxelgQ_M1VKJlZedQFXARcq7IVpmZE6/edit?gid=501068398#gid=501068398



Пэйауты по Emprex (по понедельникам запрашиваем данные и отправляем деньги в пн):
Силван истерит по Дедлайнам, это по Emprex
1. Есть файлик cash loan repayment schedule (https://drive.google.com/file/d/1GxxelgQ_M1VKJlZedQFXARcq7IVpmZE6/view?ts=6893300e) 
2. Написать Себу [в треде](https://indriver.slack.com/archives/C078JA44L0P/p1754294385923789) и спросить про сумму за период (столбик D)
3. Прогоняем скрипт, выставляем период
4. Вставляем сумму, вылезает чек
5. Пишем Андрею или отправляем через админку
6. Записываем транзакшен id
Хотим отключить autowithdrawal, чтобы не было ситуации, когда мы хотим выплатить, а денег на балансе нет

Пэйауты по Fingular (оплата по понедельникам):
1. Нужно каждую пятницу в финансовом канале от Машки получать список транзакций и сумму
2. Идем в jupyter скриптец
3. Открываем Fingular, в конфиге меняем дату и сумму
4. Далее в ячейку вставляем сумму фингуляра (не смотрим на расхождение)

Пэйауты по R2:
1. Инструкция от Даши: https://docs.google.com/spreadsheets/d/1GxxelgQ_M1VKJlZedQFXARcq7IVpmZE6/edit?gid=501068398#gid=501068398


От Даши нам нужны:  
- Требования по подготовке **реконсиляционной gBQ витрины** (которая будет строиться на наших данных и данных из API партнера)
- Требования по формату данных, которые мы хотим получать от Emprex по **rev share**
- Требования по формату данных, которые мы хотим получать от Emprex по **коллекшенам**
- Уточнить требования по админке для платежей
- Нужны google docs с инструкциями по пэйаутам


Алгоритм реконсиляции на нашей стороне
- С какой регулярностью? на 1 число каждого месяца ща весь закрытый месяц
- Какой диапазон данных берем? с 1 по 31 июля
- По какому ключу джойним? по financings

Revenue Share (hurdle rate):
- Костя формирует витрину на данных коллекшенов из gBQ (референс на последней странице договора)
  Завел [задачку](https://indriver.atlassian.net/browse/NEOA-291)
- Мы запрашиваем методологию расчета витрины у Себа в [трэде](https://indriver.slack.com/archives/C095F4T31NK/p1754484488409569)
- Мы запрашиваем данные по hurdle rate (пока на холде)

Коллекшены сверяются ежемесячно:
- Тип ride
- Формируем требования на основании данных из MetaBase
- Запросил API у Себа в [трэде](https://indriver.slack.com/archives/C095F4T31NK/p1754486510441709)

Запрос для выбора коллекшенов в gBQ:
```sql
SELECT * FROM `indrive-neobank.core.neobank_loan_repayments`  
WHERE 1=1  
  AND project_name = 'EMPREX'  
  AND repayment_type = 'COLLECTION'  
  AND country_name = 'Brazil'  
  AND repayment_created_dt_part BETWEEN '2025-05-05' AND '2025-05-11'

```

Cash-in прикрутим к пэйаутам в DWH


R2:
- В табличку вс трансферами добавить repayment_comission прикрепить rev_share