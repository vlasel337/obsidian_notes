В DBT за [[Семантический слой|семантический слой]] отвечает внешний инструмент (python-библиотека) **MetricFlow**, интегрировавшийся с DBT в 2023 году (ранее был другой инструмент).

Библиотека совместима с версиями **Python 12** и позже.
Для установки библиотеки необходимо прописать в файле `requirements.txt` следующие зависимости:
```bash
dbt-core==1.8.9 
dbt-postgres==1.8.2 
dbt-metricflow==0.7.1
```

В DBT пользоваться семантическими моделями не получится, пока в проекте не будет создана служебная модель-календарь `models/metricflow_time_spine.sql`, материализованная в таблицу:
```sql
with days as (
	{{ dbt.date_spine(
	'day',
	"date '2023-06-01'",
	"date '2023-08-31'"
	) }}
),
final as (
	select cast(date_day as date) as date_day
	from days
)
select *
from final
```
``
Эту таблицу необходимо описать в файле `models/metricflow_time_spine.yml`:
```yml
time_spines:
	- name: metricflow_time_spine
	  description: "Time spine for MetricFlow"
	  model: ref('metricflow_time_spine')

entities:
	- name: date
	  type: primary
	  expr: date_day # укажи колонку, которая уникально идентифицирует запись

dimensions:
	- name: date_day
	  type: time
	  expr: date_day
	  type_params:
	  time_granularity: day
```


В DBT семантические модели описываются в отдельной папке `metrics` внутри папки `models`. Семантические модели строятся на основе обычных моделей.

Пример описания конкретной семантической модели:
```yml
semantic_models:
	- name: "trips_users_metrics"
	  description: "Trips with users metadata fact table"
	  model: "ref('trips_users')"
	  entities:
		  - name: "trip"
		    type: "primary"
		    expr: "id"
		  - name: "user"
			type: "foreign"
			expr: "user_id"
		  - name: "scooter"
			type: "foreign"
			expr: "scooter_hw_id"
	  dimensions:
		  - name: "trip__sex"
		    type: "categorical"
		  - name: "trips__age"
			type: "categorical"
		  - name: "trip__is_free"
		    type: "categorical"
		  - name: "started_at"
		    type: "time"
		    expr: "date(started_at)"
		    type_params:
			    time_granularity: "day"
		  - name: "finished_date"
		    type: "time"
		    expr: "date(finished_at)"
		    type_params:
			    time_granularity: "day"
	  defaults:
		  agg_time_dimension: "started_at"
	  measures:
		  - name: "revenue_sum"
		    description: "The total amount of revenue (in rubles)"
		    agg: "sum"
		    expr: "price_rub"
		    create_metric: True
		  - name: "trips_count"
			description: "Total number of performed trips"
			expr: "1"
			agg: "sum"
			create_metric: True
		  - name: "users_count"
			description: "Distinct count of users making trips"
			agg: "count_distinct"
			expr: "user_id"
			create_metric: True
		  - name: "revenue_avg"
			description: "Average revenue (in rubles)"
			agg: "average"
			expr: "price_rub"
		  - name: "free_trips_count"
			description: "Total number of free (unpaid) trips"
			agg: "sum_boolean"
			expr: "is_free"
			create_metric: True
		  - name: "duration_m_median"
			description: "Median duration of trip in minutes"
			agg: "median"
			expr: "duration_s / 60.0"
			create_metric: True
		  - name: "revenue_cumsum" 
			label: "Cumulative revenue" 
			description: "Cumulative revenue (in rubles)" 
			type: "cumulative" 
			type_params: 
				measure: 
					name: "revenue_sum" 
			filter: "{{ Dimension('trip__is_free') }} = false"
		  - name: "users_count_growth_mom" 
			label: "User total growth % M/M" 
			description: "Percentage growth of unique users to 1 month ago" 
			type: "derived" 
			type_params: 
				expr: "(users_count - users_count_prev_month) * 100.0 / users_count_prev_month" 
				metrics: 
					- name: "users_count" 
					- name: "users_count" 
					  offset_window: "1 month" 
					  alias: "users_count_prev_month"
```
``
Описание:
- В секции `entities` содержатся три сущности: trip, user, scootrer.
- В секции `dimensions` описаны **категорийные** (пол, возраст) и **временные** (started_at, finished_date) размерности.
- Также в модели в секции `defaults` указана дефолтная размерность времени (в нее передаем имя размерности, а не имя колонки), по которой будет производиться аналитика по умолчанию.
- В наименованиях размерностей через dunders нужно указывать сущности, к которым они имеют отношение.
- В секции `measures` описано множество метрик, для части которых созданы простые метрики (`create_metric: True`).
- В модели описана кумулятивная (типа **cumulative**) метрика `revenue_cumsum`, которая считает выручку накопительно.
- Также в модели описана метрика `users_count_growth_mom` типа **derived**, которая переиспользует другие метрики для вычисления прироста пользователей период к периоду.




