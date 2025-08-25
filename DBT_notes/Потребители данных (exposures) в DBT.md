В DBT можно добавить отдельную виртуальную сущность **exposure**, которая будет указывать на то, кто будет потреблять [[Модели в DBT|модель]] (дэшборд/приложение/скоринг).
Она описывается в конфигурационном файле `properties.yml` в отдельном разделе exposures:
```yml
exposures:
	- name: "financial_dashboard" 
	  label: "Financial Dashboard" 
	  type: "dashboard" 
	  maturity: "high" 
	  description: "Main financial dashboard on company with code financial metrics" 
	 depends_on: 
		 - "ref('revenue_daily')" 
	 owner: 
		 name: "Finance department"
```

Для связи **exposure** с моделью используется опция `depends_on` и макрос `ref()`.

После добавления exposure в конфигурацию и новой генерации каталога данных, страница с exposure отобразится в [[Каталог данных в DBT|каталоге данных]].

При необходимости можно материализовать всю цепочку данных вплоть до конкретного потребителя: `dbt build -s +exposure:financial_dashboard`