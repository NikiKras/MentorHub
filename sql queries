/*1. Какое количество менторов и менти взаимодействуют каждый месяц на нашей платформе? Как меняется этот показатель из месяца в месяц?
Учитывайте все сессии в расчетах. Даже если сессия не состоялась, это показывает активность.*/ 

select to_char(session_date_time,'YYYY/MM') as month, count(distinct(mentor_id)) as mentor_cnt, count(distinct(mentee_id)) as mentee_cnt
from sessions s 
group by month
order by month

/*2. Сколько на платформе менторов и менти, которые еще не приняли участие ни в одной встрече? 
Учитывайте тех пользователей, кто ни разу не назначал себе встречи. 
Почему они не принимают участие во встречах? Какие гипотезы можно проверить?*/

select "role", count(user_id) as noactive_users 
from sessions s 
right join  users u 
on u.user_id = s.mentee_id
where session_id is null
group by "role" 

/*Задача 3: Сколько у каждого ментора в среднем успешных сессий в неделю по месяцам? 
Как меняется частота встреч в неделю от месяца к месяцу?
Определите ТОП-5 менторов с самым большим числом сессий за последний полный месяц.*/

with s as(
    select to_char(session_date_time,'YYYY/MM') as month, 
    cast(to_char(session_date_time , 'W') as int) as week_of_month, 
    max(cast(to_char(session_date_time , 'W') as int)) over (partition by to_char(session_date_time,'YYYY/MM')) as max_weeks_in_month,
    mentor_id, session_id, session_status
  from sessions s 
), s2 as (
  select s.month, s.mentor_id, count(s.session_status) as ses_quantity, s.max_weeks_in_month
  from s 
  where s.session_status = 'finished'
  group by s.month, s.mentor_id, s.max_weeks_in_month
)
select *, round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2) as avg_ses_per_week, 
lag (round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2)) over (partition by s2.mentor_id order by month), 
case
    when round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2) > lag (round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2)) over (partition by s2.mentor_id order by month) then 'Увеличение'
    when round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2) < lag (round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2)) over (partition by s2.mentor_id order by month) then 'Уменьшение'
    when lag (round((s2.ses_quantity*1.0 / s2.max_weeks_in_month),2)) over (partition by s2.mentor_id order by month) is null then 'Нет данных'
    else 'Без изменений'
end as trend
from s2
order by s2.mentor_id, s2.month

select  mentor_id, count(*) as session_count
from sessions
where  session_date_time >= DATE_TRUNC('month', (select MAX(session_date_time) from sessions s)) - interval '1 month'
  and session_date_time < DATE_TRUNC('month', (select MAX(session_date_time) from sessions s))
  and session_status = 'finished'
group by mentor_id
order by session_count desc
limit 5;

/*Задача: 4. Сколько времени в среднем проходит между менторскими встречами у одного ментора?*/

with s as(
  select *, lag (session_date_time) over (partition by mentor_id order by session_id) as lg
  from sessions s 
  where session_status = 'finished'
  )
select avg(session_date_time - lg) as avg_time_between
from s

/*Сколько времени в среднем проходит между менторскими встречами у одного менти?*/

with s as(
  select *, lag (session_date_time) over (partition by mentee_id order by session_id) as lg
  from sessions s 
  where session_status = 'finished'
  )
select avg(session_date_time - lg) as avg_time_between
from s

/* 5. Сколько сессий по каждому направлению менторства в месяц обычно отменяется? 
 Как меняется доля отмененных сессий помесячно?*/

select to_char(DATE_TRUNC('month', session_date_time),'YYYY/MM') as month,
       d.name as name,
       COUNT(*) as total_sessions,
       COUNT(case when session_status = 'canceled' then 1 end) AS canceled_sessions,
       ROUND(COUNT(case when session_status = 'canceled' then 1 end) * 100.0 / COUNT(*), 2) AS canceled_session_percentage
from sessions s
join domain d
on s.mentor_domain_id = d.id
group by month,d.name
order by month

/*6. Определите, в какой день недели последнего полного месяца прошло больше всего встреч.
Определите самый загруженный день недели для каждого направления менторства. 
В результатах выведите тип направления, день недели и количество встреч. */

select
    to_char(session_date_time,'Day') AS week_day,
    count(*) AS total_sessions
from sessions s2 
where  session_date_time >= DATE_TRUNC('month', (select MAX(session_date_time) from sessions s)) - interval '1 month'
  and session_date_time < DATE_TRUNC('month', (select MAX(session_date_time) from sessions s))
  and session_status = 'finished'
group by week_day
order by total_sessions DESC
limit 1

with most_dif_day as (
	select
    	d.name as name,
    	to_char(s.session_date_time,'Day') AS week_day,
    	count(*) AS total_sessions
	from sessions s 
	join domain d
	on s.mentor_domain_id = d.id
	where  session_date_time >= DATE_TRUNC('month', (select MAX(session_date_time) from sessions s)) - INTERVAL '1 month'
  	and session_date_time < DATE_TRUNC('month', (select MAX(session_date_time) from sessions s))
  	and session_status = 'finished'
	group by name, week_day
	order by total_sessions desc
	)
select m.name, m.week_day, m.total_sessions
from most_dif_day m  
join (select name, max(total_sessions) as max_total_sessions from most_dif_day group by name) as m1
on m.name= m1.name and m.total_sessions = m1.max_total_sessions

/*4*. Спрогнозируйте, сколько новых менторов нужно найти, если в следующем месяце количество активных менти увеличится
на 500 человек. Учитывайте, что занятость новых менторов будет такой же, как у текущих. Ответ: 356 менторов*/

with s as(select to_char(session_date_time,'YYYY/MM') as month, count(distinct(mentor_id)) as mentor_cnt, count(distinct(mentee_id)) as mentee_cnt
from sessions s 
group by month
order by month
) select  round(avg(mentor_cnt) / avg(mentee_cnt) * 500) as quan_mentor
from s
