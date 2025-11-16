# SQL фишки

```sql
-- 1) Создаем таблицу
drop table if exists match_scores
create table match_scores
(
	id int,
	team varchar(max),
	score int
)

insert into match_scores values
(1, 'Спартак', 35),
(2, 'Зенит', 37),
(3, 'ЦСКА', 42),
(4, 'Краснодар', 28),
(5, 'Динамо', 39),
(6, 'Зенит', 11),
(7, 'Зенит', 16)

-- 2) Выводим кумулятивную сумму через оконку
select team, score, sum(score) over(partition by team order by id) as sum_cumulative
from match_scores

-- 3) Проставляем ранги без использования оконок
ALTER TABLE match_scores
ADD place INT;

UPDATE match_scores
SET place = (
    SELECT COUNT(*)
    FROM match_scores AS ms
    WHERE ms.score > match_scores.score
) + 1;

select * from match_scores

```

- Фильтрация в WHERE происходит раньше, чем отрабатывает оконная функция