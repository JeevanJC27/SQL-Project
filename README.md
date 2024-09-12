# CASE STUDY SQL-Project

## Data cleaning in Python
#### Import essential libraries
``` SQL
import pandas as pd 
from sqlalchemy import create_engine
from urllib.parse import quote
```
#### Creating Database connection
``` SQL
password = '******'
encode_password = quote(password,safe='')
connection = f"mysql+mysqlconnector://root:{encode_password}@localhost:3306/painting"
db = create_engine(connection)
conn = db.connect()
artist = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/artist.csv")
canvas_size = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/canvas_size.csv")
image_link = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/image_link.csv")
museum = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/museum.csv")
museum_hours = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/museum_hours.csv")
product_size = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/product_size.csv")
subject = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/subject.csv")
work = pd.read_csv(f"D:/Project/SQL Project/Painting/DataSet/work.csv")
```
## Data prepration
### Artist
``` SQL
artist.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 421 entries, 0 to 420
Data columns (total 9 columns):
 #   Column        Non-Null Count  Dtype 
---  ------        --------------  ----- 
 0   artist_id     421 non-null    int64 
 1   full_name     421 non-null    object
 2   first_name    421 non-null    object
 3   middle_names  148 non-null    object
 4   last_name     421 non-null    object
 5   nationality   421 non-null    object
 6   style         421 non-null    object
 7   birth         421 non-null    int64 
 8   death         421 non-null    int64 
dtypes: int64(3), object(6)
memory usage: 29.7+ KB
```
``` SQL
artist.isnull().sum()
artist_id         0
full_name         0
first_name        0
middle_names    273
last_name         0
nationality       0
style             0
birth             0
death             0
dtype: int64
```
``` SQL
Checking for duplicate
artist.duplicated().sum()
0
```
``` SQL
I am droping this column because I have this middle name in my full_name column
artist.drop(['middle_names'],axis=1,inplace=True)
artist.columns
Index(['artist_id', 'full_name', 'first_name', 'last_name', 'nationality',
       'style', 'birth', 'death'],
      dtype='object')
```
``` SQL
artist.shape
(421, 8)
```
### canvas_size
``` SQL
canvas_size.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 200 entries, 0 to 199
Data columns (total 4 columns):
 #   Column   Non-Null Count  Dtype  
---  ------   --------------  -----  
 0   size_id  200 non-null    int64  
 1   width    200 non-null    int64  
 2   height   193 non-null    float64
 3   label    200 non-null    object 
dtypes: float64(1), int64(2), object(1)
memory usage: 6.4+ KB
```
``` SQL
canvas_size.isnull().sum()
size_id    0
width      0
height     7
label      0
dtype: int64
```
### image_link
``` SQL
image_link.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 14775 entries, 0 to 14774
Data columns (total 4 columns):
 #   Column               Non-Null Count  Dtype 
---  ------               --------------  ----- 
 0   work_id              14775 non-null  int64 
 1   url                  14775 non-null  object
 2   thumbnail_small_url  14773 non-null  object
 3   thumbnail_large_url  14773 non-null  object
dtypes: int64(1), object(3)
memory usage: 461.8+ KB
```
``` SQL
image_link.shape
(14775, 4)

image_link.isnull().sum()
work_id                0
url                    0
thumbnail_small_url    2
thumbnail_large_url    2
dtype: int64

image_link.dropna(inplace=True)

image_link.isnull().sum()
work_id                0
url                    0
thumbnail_small_url    0
thumbnail_large_url    0
dtype: int64
```
### museum
``` SQL
museum.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 57 entries, 0 to 56
Data columns (total 9 columns):
 #   Column     Non-Null Count  Dtype 
---  ------     --------------  ----- 
 0   museum_id  57 non-null     int64 
 1   name       57 non-null     object
 2   address    57 non-null     object
 3   city       57 non-null     object
 4   state      38 non-null     object
 5   postal     50 non-null     object
 6   country    57 non-null     object
 7   phone      57 non-null     object
 8   url        57 non-null     object
dtypes: int64(1), object(8)
memory usage: 4.1+ KB
```
``` SQL
museum.isnull().sum()
museum_id     0
name          0
address       0
city          0
state        19
postal        7
country       0
phone         0
url           0
dtype: int64
```
``` SQL
museum.shape
(57, 9)
```
### museum_hours
``` SQL
museum_hours.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 351 entries, 0 to 350
Data columns (total 4 columns):
 #   Column     Non-Null Count  Dtype 
---  ------     --------------  ----- 
 0   museum_id  351 non-null    int64 
 1   day        351 non-null    object
 2   open       351 non-null    object
 3   close      351 non-null    object
dtypes: int64(1), object(3)
memory usage: 11.1+ KB
```
``` SQL
museum_hours.isnull().sum()
museum_id    0
day          0
open         0
close        0
dtype: int64
```
### product_size
``` SQL
product_size.info()
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 110347 entries, 0 to 110346
Data columns (total 4 columns):
 #   Column         Non-Null Count   Dtype 
---  ------         --------------   ----- 
 0   work_id        110347 non-null  int64 
 1   size_id        110347 non-null  object
 2   sale_price     110347 non-null  int64 
 3   regular_price  110347 non-null  int64 
dtypes: int64(3), object(1)
memory usage: 3.4+ MB
```
``` SQL
product_size.isnull().sum()
work_id          0
size_id          0
sale_price       0
regular_price    0
dtype: int64
```
``` SQL
product_size['size_id'].unique()
array(['24', '30', '3024', '3226', '3629', '4030', '4836', '6048', '6854',
       '3624', '4630', '5436', '6040', '7248', '20', '2430', '2632',
       '2936', '3040', '3648', '4860', '5468', '36', '2436', '3046',
       '3654', '4060', '4872', '1620', '2024', '1624', '2030', '40', '48',
       '4020', '4824', '6030', '7236', '8040', '9648', '2424', '3030',
       '3636', '4040', '4848', '6060', '2016', '2420', '2020', '2416',
       '3020', '#VALUE!', '1632', '2040', '2448', '3060', '3672', '4080',
       '16.223', '28.836', '39.532', '3216', '56', '3628.7', '32.226',
       '3225.5', '4896', '3218', '32.426', '3239.8', '2936.5', '3225.6',
       '3223.7', '36.329', '36.432', '36.532', '27.326', '31.944',
       '28.525', '3232', '57.745', '4434', '3225.9', '2417.8', '7171',
       '5136.5', '5439.4', '22.43', '26.232', '2429', '50.635', '39.632',
       '3225.8', '3730', '3627', '35.652', '4235.5', '5062.6', '2823.2',
       '2419.7', '25.632', '37.428', '2921.7', '2921.5', '2639.8',
       '32.251', '31.525', '5936.2', '2923.6', '2923.7', '25.732',
       '2632.1', '57.542', '3536.5', '38.531', '5691.2', '3225.7',
       '32.326', '39.526', '23.529', '4023.7', '36.429', '36.326',
       '36.829', '32.524', '36.529', '39.435', '79.163', '36.626',
       '3239.4', '39.429', '5844.9', '3239.2', '57.545', '25.832',
       '32.332', '28.824', '3628.6', '4635.4', '4635', '2632.3', '50.465',
       '4835.8', '19.524', '28.936', '3246', '3628.5', '44.534', '3628.8',
       '45.835', '28.537', '32.126', '28.724', '3729.3', '37.53',
       '29.137', '3527.6', '2227.2', '4532', '3123.6', '3729.9', '26.332',
       '2936.6', '35.628'], dtype=object)
```
``` SQL
product_size['size_id'] = pd.to_numeric(product_size['size_id'],errors='coerce')

product_size['size_id'].unique()
array([  24.   ,   30.   , 3024.   , 3226.   , 3629.   , 4030.   ,
       4836.   , 6048.   , 6854.   , 3624.   , 4630.   , 5436.   ,
       6040.   , 7248.   ,   20.   , 2430.   , 2632.   , 2936.   ,
       3040.   , 3648.   , 4860.   , 5468.   ,   36.   , 2436.   ,
       3046.   , 3654.   , 4060.   , 4872.   , 1620.   , 2024.   ,
       1624.   , 2030.   ,   40.   ,   48.   , 4020.   , 4824.   ,
       6030.   , 7236.   , 8040.   , 9648.   , 2424.   , 3030.   ,
       3636.   , 4040.   , 4848.   , 6060.   , 2016.   , 2420.   ,
       2020.   , 2416.   , 3020.   ,      nan, 1632.   , 2040.   ,
       2448.   , 3060.   , 3672.   , 4080.   ,   16.223,   28.836,
         39.532, 3216.   ,   56.   , 3628.7  ,   32.226, 3225.5  ,
       4896.   , 3218.   ,   32.426, 3239.8  , 2936.5  , 3225.6  ,
       3223.7  ,   36.329,   36.432,   36.532,   27.326,   31.944,
         28.525, 3232.   ,   57.745, 4434.   , 3225.9  , 2417.8  ,
       7171.   , 5136.5  , 5439.4  ,   22.43 ,   26.232, 2429.   ,
         50.635,   39.632, 3225.8  , 3730.   , 3627.   ,   35.652,
       4235.5  , 5062.6  , 2823.2  , 2419.7  ,   25.632,   37.428,
       2921.7  , 2921.5  , 2639.8  ,   32.251,   31.525, 5936.2  ,
       2923.6  , 2923.7  ,   25.732, 2632.1  ,   57.542, 3536.5  ,
         38.531, 5691.2  , 3225.7  ,   32.326,   39.526,   23.529,
       4023.7  ,   36.429,   36.326,   36.829,   32.524,   36.529,
         39.435,   79.163,   36.626, 3239.4  ,   39.429, 5844.9  ,
       3239.2  ,   57.545,   25.832,   32.332,   28.824, 3628.6  ,
       4635.4  , 4635.   , 2632.3  ,   50.465, 4835.8  ,   19.524,
         28.936, 3246.   , 3628.5  ,   44.534, 3628.8  ,   45.835,
         28.537,   32.126,   28.724, 3729.3  ,   37.53 ,   29.137,
       3527.6  , 2227.2  , 4532.   , 3123.6  , 3729.9  ,   26.332,
       2936.6  ,   35.628])
```
``` SQL
product_size.shape
(110347, 4)

product_size.isnull().sum()
work_id            0
size_id          212
sale_price         0
regular_price      0
dtype: int64

product_size.dropna(subset=['size_id'],inplace=True)

product_size.isnull().sum()
work_id          0
size_id          0
sale_price       0
regular_price    0
dtype: int64
```
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


