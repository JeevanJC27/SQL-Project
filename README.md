# CASE STUDY SQL-Project
``` SQL 
use painting;

select * from artist; -- 421
select * from canvas_size; -- 200
select * from image_link; -- 14775
select * from museum; -- 57
select * from museum_hours; -- 351
select * from product_size; -- 110135
select * from subject; -- 6771
select * from work; -- 14776
```
#### 1) Fetch all the paintings which are not displayed on any museums?
``` SQL
select *
from work
where museum_id is null;
```
#### 2) Are there museuems without any paintings?
``` SQL
select 
	museum_id,
    m.name
from museum as m
where not exists(select museum_id
			from work as w
            where w.museum_id = m.museum_id);
```
#### 3) How many paintings have an asking price of more than their regular price? 
``` SQL
select *
from product_size
where sale_price > regular_price;
```
#### 4) Identify the paintings whose asking price is less than 50% of its regular price
``` SQL
select work_id,size_id,sale_price,regular_price
from (
	select 
		*,
		sale_price / regular_price * 100 as sales_percentage
	from product_size) as t
where sales_percentage < 50
order by regular_price desc;
```

#### 5) Which canva size costs the most?
``` SQL
select 
	c.*,sale_price,regular_price
from (
	select 
		*,
		rank() over(order by sale_price desc) as rnk
	from product_size) as t
join canvas_size as c 
	on c.size_id = t.size_id
where rnk = 1;
```

#### 6) Delete duplicate records from work, product_size, subject and image_link tables
##### work table
``` SQL 
drop procedure if exists diplicate_fetch;

delimiter $$
create procedure diplicate_fetch()
begin

	drop table if exists work_info;
    create temporary table Work_info
	select 
		*
	from (
		select 
			*,
			row_number() over(partition by work_id, artist_id) as row_num
		from work) as t
	where row_num = 1;
    
    truncate table work;
    
    insert into work
    select work_id, name, artist_id, style, museum_id from Work_info;
    
    select * from work;

end $$
delimiter ;

call diplicate_fetch();
```

##### product_size
``` SQL
drop procedure if exists diplicate_fetch;

delimiter $$
create procedure diplicate_fetch()
begin

	drop table if exists product_size_info;
    create temporary table product_size_info
	select 
		*
	from (
		select 
			*,
			row_number() over(partition by work_id, size_id) as row_num
		from product_size) as t
	where row_num = 1;
    
    truncate table product_size;
    
    insert into product_size
    select work_id, size_id, sale_price, regular_price from product_size_info;
    
    select * from product_size;

end $$
delimiter ;

call diplicate_fetch();
```

##### subject
``` SQL
drop procedure if exists diplicate_fetch;

delimiter $$
create procedure diplicate_fetch()
begin

	drop table if exists subject_info;
    create temporary table subject_info
	select 
		*
	from (
		select 
			*,
			row_number() over(partition by work_id) as row_num
		from subject) as t
	where row_num = 1;
    
    truncate table subject;
    
    insert into subject
    select work_id, subject from subject_info;
    
    select * from subject; -- 6771

end $$
delimiter ;

call diplicate_fetch();
```
##### image_link
``` SQL
drop procedure if exists diplicate_fetch;

delimiter $$
create procedure diplicate_fetch()
begin

	drop table if exists image_link_info;
    create temporary table image_link_info
	select 
		*
	from (
		select 
			*,
			row_number() over(partition by work_id) as row_num
		from image_link) as t
	where row_num = 1;
    
    truncate table image_link;
    
    insert into image_link
    select work_id, url, thumbnail_small_url, thumbnail_large_url from image_link_info;
    
    select * from image_link; -- 6771

end $$
delimiter ;

call diplicate_fetch();
```
#### 7) Identify the museums with invalid city information in the given dataset
``` SQL
select * 
from museum
where city regexp "[0-9]";
```
#### 8) Museum_Hours table has 1 invalid entry. Identify it and remove it.
``` SQL
update Museum_Hours
set day = 'Thursday'
where day = "Thusday";

select distinct day
from Museum_Hours;
```
#### 9) Fetch the top 10 most famous painting subject
``` SQL
select 
	subject,
    count(*) as no_of_painting,
    rank() over(order by count(*) desc) as rnk
from work
join subject
	using(work_id)
group by subject
limit 10;
```
#### 10) Identify the museums which are open on both Sunday and Monday. Display museum name, city.
``` SQL  
with 
	Sun as (
		select * 
		from museum_hours
		where day = 'Sunday'
	),
    Mon as (
		select * 
		from museum_hours
		where day = 'Monday'
	),
	cte as (	
        select 
			museum_id
		from sun
		join mon 
			using(museum_id)
	)
select 
	name as museum_name,
    city
from cte
join museum
	using(museum_id);
```  
#### 11) How many museums are open every single day?
``` SQL
select 
	name as museum_name,
    city
from (
	select museum_id, count(*) as no_of_day
	from museum_hours
	group by museum_id
	having count(*) = 7) as t
join museum as m
	using(museum_id);
 ```   
#### 12) Which are the top 5 most popular museum? (Popularity is defined based on most no of paintings in a museum)
``` SQL
with 
	museum_painting as (
		select 
			museum_id,
			count(*) as no_of_painting,
			rank() over(order by count(*) desc) as rnk 
		from work
		where museum_id is not null
		group by museum_id
        )
select 
	museum_id,
    name as museum_name,
    country,
    no_of_painting
from museum_painting
join museum
	using(museum_id)
order by no_of_painting desc
limit 5;
```
#### 13) Who are the top 5 most popular artist? (Popularity is defined based on most no of paintings done by an artist)
``` SQL
with 
	artist_Painted as(
		select 
			artist_id,
			count(*) as no_of_painting,
			rank() over(order by count(*) desc) as rnk
		from work
		group by artist_id
	)
select 
	artist_id,
    full_name,
    no_of_painting
from artist_Painted
join artist
	using(artist_id)
order by no_of_painting desc
limit 5;
```

#### 14) Display the 3 least popular canva sizes
``` SQL
select 
	size_id,
	label,
    count(*) as no_of_painting,
    dense_rank() over(order by count(*)) as rnk
from work 
join product_size
	using(work_id)
join canvas_size
	using(size_id)
group by size_id, label
limit 3;
```
#### 15) Which museum is open for the longest during a day. Dispay museum name, city and hours open and which day?
``` SQL
select 
	museum_id,
	name as museum_name,
    city,
    open,
    close,
    duration
from (
	select
		museum_id,
		day,
		open,
		close,
		date_format(cast(open as time),"%r") - date_format(cast(close as time),"%r") as duration,
		rank() over(order by (date_format(cast(open as time),"%r") - date_format(cast(close as time),"%r")) desc) as rnk
	from museum_hours
) as t
join museum
	using(museum_id)
where rnk = 1;
```
#### 16) Which museum has the most no of most popular painting style?
``` SQL
with cte as(
	select 
		style,
		count(*) as no_of_painting,
		rank() over(order by count(*) desc) as rnk
	from work
	group by style
	limit 1
    ),
    cte1 as (
	select 
		museum_id
	from work
	where style = (select style from cte) and museum_id is not null
	group by museum_id
    )
select 
	m.museum_id, name as museum_name, city, country
from museum as m
where exists (select museum_id from cte1 as c where c.museum_id = m.museum_id);
```
#### 17) Identify the artists whose paintings are displayed in multiple countries
``` SQL
select * from artist;
select * from work;
select * from museum;

SELECT
    a.artist_id,
    a.full_name as artist_name
FROM
    artist a
JOIN
    work w ON a.artist_id = w.artist_id
JOIN
    museum m ON w.museum_id = m.museum_id
GROUP BY
    a.artist_id, a.full_name
HAVING
    COUNT(DISTINCT m.country) > 1;
 ```   
#### 18) Display the country and the city with most no of museums. Output 2 seperate columns to mention the city and country. If there are multiple value, seperate them with comma.
``` SQL
with cte_country as(
	select *
	from (
		select 
			country,
			count(*) as no_of_museum,
			rank() over(order by count(*) desc) as rnk
		from museum
		group by country) as t
	where rnk = 1),
    cte_city as(
    select *
	from (
		select 
			city,
			count(*) as no_of_museum,
			rank() over(order by count(*) desc) as rnk
		from museum
		group by city) as t
	where rnk = 1)
select 
	group_concat(distinct country separator ' , ') as country,
    group_concat(city order by city separator ' , ') as city
from cte_country
cross join cte_city;
```
#### 19) Identify the artist and the museum where the most expensive and least expensive painting is placed. Display the artist name, sale_price, painting name, museum name, museum city and canvas label
``` SQL
select * from artist;
select * from product_size;
select * from museum;
select * from canvas_size;
select * from work;

with cte_most_exp as(
	select 
		*,
		rank() over(order by sale_price desc) as most_expensive
	from product_size
	limit 1
	),
    cte_least_exp as(
	select 
		*,
		rank() over(order by sale_price) as least_expensive
	from product_size
	limit 1
    ),
    cte as(
	select 
		work_id, size_id, sale_price
	from cte_most_exp
	union 
	select 
		work_id, size_id, sale_price
	from cte_least_exp
	)
select 
	w.name as painting_name,
    c.sale_price,
    a.full_name as artist_name,
    cs.label as canvas_label,
    m.name as museum_name,
    m.city
from cte as c
join work as w
	on w.work_id = c.work_id
join artist as a
	on a.artist_id = w.artist_id
join museum as m
	on m.museum_id = w.museum_id
join canvas_size as cs
	on cs.size_id = c.size_id;
```
#### 20) Which country has the 5th highest no of paintings?
``` SQL
select 
	country,
    counts as no_of_paintings
from (
	select 
		country,
		count(*) as counts,
		rank() over(order by count(*) desc) as rnk 
	from museum
	join work
		using(museum_id)
	group by country) as t 
where rnk = 5;
```
####  21) Which are the 3 most popular and 3 least popular painting styles?
``` SQL
with 
	cte_most as(
		select 
			style,
			count(*) as no_of_painting,
			rank() over(order by count(*) desc) as rnk
		from work
		group by style
		limit 3
		),
	cte_least as(
		select 
			style,
			count(*) as no_of_painting,
			rank() over(order by count(*)) as rnk
		from work
		where style is not null
		group by style
		limit 3
        )
select 
	m.style as most_popular_style,
    l.style as least_popular_style
from cte_most as m
join cte_least as l
	on m.rnk = l.rnk;
```  
#### 22) Which artist has the most no of Portraits paintings outside USA?. Display artist name, no of paintings and the artist nationality.
``` SQL
with cte as (
	select 
		full_name as artist_name,
		nationality as artist_nationality,
        count(*) as no_of_painting,
        rank() over(order by count(*)  desc) as rnk
	from artist as a
	join work as w
		on w.artist_id = a.artist_id
	join museum as m
		on m.museum_id = w.museum_id
	join subject as s
		on s.work_id = w.work_id
	where m.country <> "USA" and s.subject='Portraits'
	group by full_name, nationality
    order by no_of_painting desc
    )
select 
	artist_name,
    artist_nationality,
    no_of_painting
from cte
where rnk = 1;
```


