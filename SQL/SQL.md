

**Where**
```
SELECT * 
FROM table 
WHERE condition1 
	AND/OR condition2 
	AND/OR condition3;
```
- think of it like excel filter

**Conditional**
- And
- Or
- Not

**Between**
- Where + Between
	- Selects values within a given range
	- has to be pared with 'AND'
		- where column_name between value1 and value2;
- Inclusive
	- both start and end

**IN**
- allows us to specify multiple values in a single line's WHERE clause instead of having to use multiple OR conditions to filter multiple values
- where column in (....);
	- checks if particular column value matches anything in the value you specify

**LIKE**
- filter rows based on whether a string matches a certain pattern
- where column like ....
- and/or column not like ...

	**%**
	- wild-card
	- `where drug like "%Relief%"`
	- zero or more characters

	**`_`**
	- wildcard to represent a **single** character
	- 4 letter words taht begin with 'f' and have 'c' as 3rd letter
		- `where word like "f_c_"`

**RLIKE / regexp**
- regex
- `'^[a-z0-9._%+-]+@[a-z0-9.-]+\\.com$';`

**regexp_like()**
- boolean test

**regexp_replace()**
- replace
- can use this to count the frequency of the pattern

**regexp_substr()**
- return first substring matching regex

**regexp_instr()**
- return position of match

**basic logic operation**
- =
- !=, <>
- <, >
- <=, >=
- Between ... and ...

**ORDER BY**
- asc by default
- order by column asc/desc;
- order by column1 asc, column2 desc; # multiple col
```
select policy_holder_id, call_category, call_received
from callers
order by 1,3 desc;
```
- 1 : policy_holder_id
- 3 : call_received

**LIMIT**
- `order by call_received desc`
- `limit 5;`
- top 5 most rescent phone calls 한정

**OFFSET**
- control results returned
- `offset 10`
	- skip first 10 rows


**Aggregate Functions**
1. SUM
2. MIN
3. MAX
4. AVG
5. COUNT

*Example*
- calculating total sales
	- select sum(*)
- Average rating for product reviews
	- select avg()
- identify most active users
	- select count(posts), group_by user_id
- lowest and highest prices
	- select max(prices), min(prices)

**GROUP BY w agg**
- select target to aggregate
- multiple categories
	- group by ticker, year
- again, we can identify the column name via number
	- group by 1, 2

**GROUP BY w HAVING**
- *agg function is not allowed in WHERE*
	- `where avg(open) > 200` is not allowed
- allows you to filter data based on values from aggregated functions
	- kinda like lambda

**WHERE vs HAVING**
- Where
	- filters on values in individual rows
- Having
	- filters values aggregated from groups of rows

**Having without Group by**
- it is technically possible to have HAVING without GROUP BY
- it works the same as where

**Distinct**
- helpful for data exploration
- may include two or more columns (return unique pairs)
- may use distinct with **agg functions**
	- count(distinct user_id) # most often used
	- distinct goes inside the agg function

**Arithmetic**
- math expressions to transform column values
- - 
- +
- *
- /
- %
- ^
- parentheses -> exponents -> multiplications & division -> add/subtract

**Math Functions**
- ABS
	- Absolute number
- Round
	- Round(number, 2)
	- 2 : number of decimal present
- **CEIL**
	- round up
- **FLOOR**
	- round down
- **Power**
	- `power(number, 2)`
	- `^2`
- **MOD**
	- %5 == 0
	- mod(number, 5)

**division**
- '/' treats integers (whole numbers) differently than in excel
- integer division discards the remainder from the output

*including decimal points*
1. use cast
	- cast(10 as decimal)
2. multiply by 1.0
	- `10 * 1.0 / 6`
3. explicit type specification using **`::`**
	- 10::DECIMAL/4    # 2.500000000
	- 10::FLOAT/4         # 2.5
	- 10/4::DECIMAL    # 2.50000000
	- 10/4::FLOAT         # 2.5
 
**CAST**
- converts one or both operands into decimal or floating-point data types
- cast(number AS DECIMAL)
- cast(number AS FLOAT)

**percentage**
- `(a / b) * 100`

**nulls**
- cannot use `=` comparison with null
- null is considered the smallest value hence when you sort it, it ascends to top of the list
- **IS NULL**
	- used to identify null and non-null values
	- is not null
- **COALESCE()**
	- takes multiple inputs and return first non-null value
	- coalesce(col_name, 'expression') or coalesce(col1, col2, col3, 'expression')
		- if expression is specified, it returns the expression
		- else it returns the value of column_name
		- check from the front col1, col2, col3, until its not null, else return expression
- **IFNULL()**
	- ifnull(column_name, value_if_null)
- **coalesce vs ifnull**
	- coalesce : versatile for multiple arguments
	- ifnull : handles two arguments

**Case + Select**
- used to create new columns, categorize data, perform calculations
```sql
SELECT 
	column_1, 
	column_2, 
	CASE 
		WHEN condition_1 THEN result_1 
		WHEN condition_2 THEN result_2 
		WHEN ... THEN ... 
		ELSE result_3
	END AS column_3_name
```

**Case + Where**
- filter rows based on specified conditions
- same column, different conditions for different values

```sql
SELECT 
column_1, 
column_2 
FROM table_1 
WHERE CASE 
WHEN platform = "instagram" THEN followers >= 500000 
WHEN condition_2 THEN result_2 
WHEN ... THEN ... 
ELSE result_3
end;
```

**count + case**
```sql
select
	platform
	count(case
		when followers >= 500000 then 1
		else null
		end) as popular_actor_count,
	count(case
		when followers < 500000 then 1
		else null) as less_popular_actor_count
from marvel_avengers
group by platform;
```

**sum + case**
```sql
SELECT 
	platform, 
	SUM(CASE 
		WHEN engagement_rate >= 8.0 
		THEN followers ELSE 0 END) 
	AS high_engagement_followers_sum,
```

**joins**
1. **INNER JOIN**
	- returns only the rows with matching values from both tables
2. **LEFT JOIN**
	- all rows from left table is fetched, and matching rows from right tables
3. **RIGHT JOIN**
	- all rows from right table is fetched, and matching rows from left table
4. **FULL OUTER JOIN**
	- returns all rows

**Conditional Joins**
```
select g.book_title, ,o.quantity
from goodreads as g
inner join orders as o
on g.book_id = o.book_id
	and o.quantity > 2;
```
- applies condition as part of join
	1. if the condition `o.quantity > 2` affects the joining key
		- filter the output
	2. if the condition does not affect the joining key
		- all values of that column that does not meet the condition, you can think of it as null values

**self joins**
- find similar books within the same genre

```
select 
	b1.genre,
	b1.book_title as current_book
	b2.book_title as suggested_book
from 
	goodreads as b1 inner join goodreads as b2
on 
	b1.genre = b2.genre
where
	b1.genre = b2.genre
order by
	b1.book_title
```
- creates a cartesian join within genre

**UNION**
- combine data vertically
- **rules**
	- number of columns in each select must match
	- data types of columns must match
	- columns selected must be in the same order
- **gives list of unique values without duplicates**

**UNION ALL**
- combines results from two or more select statements
- **it includes all values including duplicates**

```
select ingredient
from recipe1 union all
select ingredient
from recepie2;
```

**INTERSECT**
- finding intersect
```
select first_name, last_name
from employees
intersect
select first_name, last_name
from customers;
```
- remove duplicates

**INTERSECT ALL**
- finding intersect
```
select first_name, last_name
from employees
intersect all
select first_name, last_name
from customers;
```
- preserves duplicates









**`CURRENT_DATE`**
- returns today's date

**`CURRENT_TIME`**
- returns the current time without the date

**`CURRENT_TIMESTAMP or NOW()`**
- returns the current date and time

*can use >, =, < operands to timestamp/date*

**Extract**
- extracts a specific component from a date or timestamp

**Date_part()**
- extracts a specific component from a date or timestamp

```sql
EXTRACT(YEAR FROM sent_date) AS extracted_year, DATE_PART('year', sent_date) AS part_year, EXTRACT(MONTH FROM sent_date) AS extracted_month, DATE_PART('month', sent_date) AS part_month, EXTRACT(DAY FROM sent_date) AS extracted_day, DATE_PART('day', sent_date) AS part_day, EXTRACT(HOUR FROM sent_date) AS extracted_hour, DATE_PART('hour', sent_date) AS part_hour, EXTRACT(MINUTE FROM sent_date) AS extracted_minute, DATE_PART('minute', sent_date) AS part_minute
```

**date_trunc**
- `date_trunc('month', sent_date)`

08/03/2022 16:43:00
- **trunc to month**
	- `08/01/2022 00:00:00`
- **trunc to day**
	- `08/03/2022 00:00:00`
- **trunc to hour**
	- `08/03/2022 16:00:00`
- behaves like **floor** not round

**Interval**
- used for adding/subtracting date and time gaps
- `+ interval '2 days'`
- `- interval 2 hours`

**to_char**
- convert date or timestamp to a string (specified format)
- `to_char(sent_date, 'YYYY-MM-DD HH:MI:SS')`

**to_date**
- convert string to date or timestamp
- `to_date('2023-08-27, 'YYYY-MM-DD')`


*Date 관련해서 mysql vs postgresql*
https://datalemur.com/blog/mysql-postgresql-date-time-functions

---
https://datalemur.com/sql-tutorial/sql-cte-subquery

**CTE (Common Table Expression)**
- query within query
- creating temporary table using `WITH`

**Subqueries**
- embed one query within another
- nesting them via parentheses

**When to use CTE or Subquery**
- **CTE**
	- break down complex queries
```
with genre_revenue_cte as (
	select genre, sum(concert_revenue) as total_revenue
	from concerts
	groupby genre
)

select g.genre, g.total_revenue,
```
- when you need to reuse the subquery results, cte is useful
- when you need to perform recursive queries, cte are ideal choice

- **Subquery**
	- single value comparison in where clauses
```sql
# artists whose concert has greater than average revenue
select artist_name
from concerts
where concert_revenue > (select avg(concert_revenue) from concerts)
```

**column creation and aggregation**
- use subqueries to create new columns for real-time computations
```sql
select
	artist_name,
	genre,
	concert_revenue,
	(select avg(concert_revenue) from concerts) as avg_concert
	(select max(concert_revenue) from concerts) as avg_concert
from concerts;
```

**RANK**
- `RANK() over DATA`
- `RANK() over (partition by col_name order by col_name)`

**Aggregate Window**
- window are functions that operate by creating virtual windows within the dataset
- each window operate independently so we can do aggregate functions like sum, avg
- **Partition By**
	- ㅁㅁㅁ over ()
		- 해당 aggretation 수행
	- partition by
		- 해당 group 에 대해
	- **ORDER BY**
		- sets up for incremental accumulation 
		- if not present, it fills in the same value for all same partition by column

- partition by a, b
	- 2개도 가능

![[Pasted image 20250721173703.png]]
```sql
SELECT 
	user_id, 
	category, 
	product, 
	spend, 
	SUM(spend) OVER ( 
		PARTITION BY user_id ORDER BY transaction_date) AS cumulative_spend 
	FROM product_spend;
```
- **when "transaction_date" is identical to the next one, they both get 519.79**

**avg**
- as the window gets larger, it fetches the average of 2, 3, 4 rows

*EXCEPTION*
- LAST_VALUE() returns the last value of the entire partition, not expanding window


**RANK**
- assign ranks to rows based on partitioning and order expressions
- **rank**
	- 공동 순위 존재, the next rank jumps from 4 -> 6
- **dense_rank**
	- 공동 순위 존재, the rank does not jump
- **row_number**
	- assigns sequential ranks to each artist based on their concert revenue (unique)

```
select 
	col_1,
	col_2,
	row_number() over (order by concert_revenue) as row_num
	row_number() over (partition by country order by yof) as row_num
```

**time-series window functions**
- **lead()**
	- access data from rows that come after
- **lag()**
	- access data from rows before the current row
```
lead(col_name, offset) over (
	partition by partition_col
	order by order_col
)

or lag
```

**unbounded/bounded (value functions)**
- by default most of the functions have
	- `rows between unbounded preceding and current row`
	- hence, the last_value() will always be the value of the current row
- `rows between unbounded preceding and unbounded following`
	- applies on full partition
- `rows between current row and unbounded following`
	- reverse cumulative totals
- `rows between 1 and preceding and 1 following`
	- not selected
	- **selected**
	- **current_row**
	- **selected**
	- not selected

**unbounded/bounded (aggregate functions)**
- sum, avg, count, max, min
- `range between unbounded preceding and current row`


https://datalemur.com/sql-tutorial/sql-time-series-window-function-lead-lag










**with rollup**
- 소계를 만들어 줌
- group by region, product with rollup;
	- east, A, 100
	- east, B, 150
	- east, Null, 250 < subtotal
	- west, A, 200
	- west, B, 250
	- west, null, 450 < subtotal
	- null, null, 700 < grand total

**grouping(column_name)**ㅁ
- returns 1 if the column is "rolled up"
- column_name 기준으로 진행

**offset**
`limit 2 offset 3;`
- limit 개수 offset 건너뛰는 개수
	- 1, 2 건너뛰고 3, 4, 5 return
- pagenation 에 많이 이용 됨

**Exists**
- subquery 안에 값이 존재하는지, 안하는지에 따라 밖에 있는 쿼리의 작동 유무를 판단




---

**DML (data manipulation language)**
- 데이터 조작 언어
- 이미 존재하는 table 에 데이터 저장, 수정, 삭제, 검색
- commit 과 rollback 이 적용되는 명령어
- insert/update/delete


*mysql은 int 자료형의 컬럼은 default 값이 0*


`insert into emp01 (ename, deptno) values ('신짱구', 99), ('신짱아', 99)`

`delete from emp01 where empno=0`

`update table_name set deptno=50 where empno=0;`

```
select @@autocommit;
set @@autocommit = 0; 
insert into ...
commit;

= 0 (false)
-- 트랜잭션 단위로 commit을 작성할 때까지 실제 db에는 반영되지 않는 상태

-- select 절을 쓰면 반영이 된걸 볼 수 있는데, temp memory 에만 반영 되고, 다시 시작하면 날라감
```

```
start transaction;
insert into emp01 values (1, '신짱구', 10);
SAVEPOINT sp1;
update emp01 set empno=3 where empno=1;
savepoint sp2;
delete from emp01 where empno=3;
savepoint sp3;

ROLLBACK to savepoint sp2;
```
- if rollbacked to sp1 and try sp2 again, its gone
- if you did sp3 first and sp1 then its okay
- unspecified rollback : start transaction 바로 다음으로

*한꺼번에 모든 쿼리를 실행하는 단위 : transaction*

**truncate table emp01**
- 정보만 다 없애고 구조만 남김

**drop table emp01**
- table 자체가 사라짐

**create table emp01 select empno, ename, hiredate, sal from emp;**


`alter table emp01 add column col_name INT;`

`update emp01 e01 join emp e on e.empno=e01.empno set e01.deptno2=e.deptno;`

`update emp01 set deptno = 30, job = 'MANAGER' where ename='SMITH';`

**UPSERT**
```
INSERT INTO people (name, age) VALUES ('김연재', 25)

ON DUPLICATE KEY UPDATE  -- ON DUPLICATE KEY가 의미하는 것은 실제로 UNIQUE가 걸려있는 COLUMN에서의 DUPLICATE를 의미합니다

  name = '중복값',

  age = age + 1;

SELECT * FROM people;
```

---
**INDEXING**
- DB의 빠른 검색을 위한 색인 기능
- 클러스터형 인덱스
	- primary key, unique not null
	- 데이터베이스 테이블에서 기본 키를 기준으로 생성되는 인덱스
	- 하나의 테이블에는 하나의 클러스터형 인덱스만 존재할 수 있음
```
-- PK를 지정하면 저절로 클러스터형 인덱스가 지정됨 ALTER TABLE students ADD PRIMARY KEY (id); -- 위 코드와 같은 의미 CREATE CLUSTERED INDEX idx_students_id ON students (id); -- 제거 DROP 인덱스명 ON 테이블명; -- 확인 SHOW INDEX FROM students; SHOW TABLE STATUS LIKE 'students';
```

**Secondary Index**
- 테이블에서 기본 키 이외의 다른 열을 기준으로 생성되는 인덱스
- 중복을 허용하는 / 중복을 허용하지 않는 인덱스 2가지 방법이 존재
- 한 테이블당 여러 보조 인덱스 생성 가능
```
CREATE INDEX idx_students_english ON students (english); 
CREATE UNIQUE INDEX idx_students_english ON students (english); 
ALTER TABLE students ADD INDEX idx_students_english (english); 
SELECT english FROM students; -- 결과의 Execution Plan 창에서 확인해보기
```

*네이밍 : 기능명_테이블명_컬럼명*

**When to use index**
- column that gets included in the where clause a lot
- column that gets used often
- remove unused index

**When to not use index**
- when index column is massive
	- requires more storage
- when there are multiple duplicate values (gender : male, female)
- index slows down insert, update, delete

```
create index index_name on table_name (col_1, col_2, col_3);
```

---
*self-paced*
**create database**
- `create new database test`

**create table**
- `create table emp01 as select(
	- a INT primary key,
	- b INT,
	- c INT`

**create index**
- `create idx_emp01_empno on emp01(empno)`

**multi-column index**
- `create index idx_usertbl_name_birthyear on usertbl(name, birthyear)`
- even when only one of them is called, index works 

**create unique index**
- enforces uniqueness even on columns that was not defined "unique" upon initial creation of 

**show index**
- `show index from tbl1`

**alter table**
- modify existing table structure
- `alter table my_table`
- `add constraint pk_name primary key (name)`
	- add primary key constraint
- `alter table mixed_table`
- `add constraint uk_mixed_table_name unique(name)`
	- add unique constraint
- `alter table usertable drop primary key`
- `alter table usertable drop index idx_usertbl_name_birthyear`
- `alter table table_name drop foreign key buytbl_ibfk_1`

*primary key = clustered index*
- it is automatically created when primary key is defined either during create table or alter table

**full text index**
- used for natural language text searching such as "%text%"
- `create fulltext index idx_content on articles(content)`

**show table status**
- provide table level metadata (row count, data length, index length, etc)
- `show table status like 'usertbl'`

**analyze table**
- analyze table usertbl;

**insert into**
- `insert into usertbl (col1, col2) values ('LSG', '이승기', 1897)`

----
**integrity (무결성)**

**Create Table ... Constraints**
```
create table tbl_name (
	col1 datatype [constraints],
	...
	constraint constraint_name constraint_type(column)
)
```

**constraint types**
1. **primary key**
```
create table dept(
	deptno int not null
	constraint pk_dept primary key (deptno)
)
```
- auto-indexed & define clustered index

2. **unique**
- `empno int not null unique,`
- prevents duplicate values but allow null

3. **check**
- `age int check (age between 1 and 100)`
- `alter table emp05 add check (age > 0)`

4. **default**
- sets a default value if none is provided
- `age int default 1`

5. **foreign key**
- ensure referential integrity between parent and child tables
- child table's FK must match PK of parent table
```
alter table emp
add constraint fk_emp_dept
foreign key (deptno) references dept(deptno)
on delete no action on update no action;
```
- **on delete / on update**
	- **no action** : prevent deletion/update if references exist
	- **cascade** : automatically update/delete children
	- **set null** : sets fk field to null on parent deletion/update

**drop constraints**
- alter table emp drop primary key;
- alter table emp drop foreign key fk_emp_dept;
- alter table emp drop index uq_ename;


**information schema**
- stores metadata (not modifiable)
	- table constraints
	- columns
	- referential constraints
	- key column usage
```
SELECT * FROM information_schema.TABLE_CONSTRAINTS
WHERE table_name = 'emp';
```

---

일반 집계 함수:   SUM, MAX, MIN, AVG, COUNT  
그룹 내 행 순서 함수: FIRST_VALUE, LAST_VALUE, LAG, LEAD
그룹 내 비율 함수: RATIO_TO_REPROT, PERCENT_RANK, CUME_DIST, NTILE 등


----

**Making Variables**
- set @var_name = var_value

```
SET @변수이름 = 변수의 값; -- 변수 선언 및 값 대입 
SELECT @변수이름; -- 변수의 값 출력 
SET @count = 5; 

SELECT code, name, continent, region, population 
FROM country 
WHERE population > 100000000 
ORDER BY @count ASC;
```
- 위와 같이 그냥 쓰면 variable 이 제대로 작동 하지 않음

```
SET @count = 5;
PREPARE mySQL FROM 'SELECT code, name, continent, region, population
  FROM world.country
 WHERE population > 100000000
 ORDER BY 1 ASC
 LIMIT ?';
EXECUTE mySQL USING @count;
```
- prepare - execute 문을 같이 사용 해야 함

**Stored Procedure**
- 자주 사용하는 여러개의 sql 문들을 한꺼번에 묶어서 일괄적으로 처리

```mysql
-- 82년보다 먼저 입사한 사람의 수를 리턴하는 procedure
delimiter // -- 시작
create procedure employees_hireyear(in target_year, out employee_count int) -- return variable & datatype
begin
	쿼리1;
	쿼리2;
	쿼리3;
	쿼리4;
end //
delimiter ; -- 끝

call fisa.employees_hireyear(2, @hire_year);
```
- procedure/functions belong to a specific database.

```mysql
set @status_message = ''

create procedure employees_hireyear2(in target_year, out employee_count out status_message)
begin
	select count(*) into employee_count
	from fisa.emp
	where year(hiredate) < yarget_year;

	if employee_count > 10 then
		set status_message = '명예퇴직 기잔';
	else
		set status_message = 'OKAY';
	end if;
end //
```

**FUNCTION**
```mysql
create function employees_hireyear()
returns int -- 리턴받은 자료형
deterministic -- 입력에 따라 같은 로직을 사용한 결과 출력
begin
	declare employee_count int;
	select count(*) into employee_count
	from fisa.emp where year(hiredate) < 1985;
	return employee_count;
```


*self-paced*

**stored procedure**
- saved script of sql commands that you can call and run with a single line
- `BEGIN,... , END`
- 입력값 (IN) & 출력값 (OUT)
- delimiter 을 표시해야 begin~end 블록을 인식 (//, 등)

```mysql
DELIMITER //
CREATE PROCEDURE procedure_name(IN param1 INT, OUT result INT)
BEGIN
    -- logic
END //
DELIMITER ;

CALL procedure_name(1985, @result);
SELECT @result;
```
- 자주 사용하는 로직 재사용 가능
- 네트워크 통신 횟수 감소 -> 성능 향상
- **DB 상태 변경 가능 (Insert, update, delete)**

**function**
- 단일 값을 반환하는 sql 코드 블록
- return 값이 반드시 존재 해야 함
- select, where, join 등에서 사용 가능
- **DB 상태 변경 불가능**
- 항상 같은 입력 -> 같은 출력이면 deterministic 사용

```mysql
DELIMITER //
CREATE FUNCTION function_name(param INT)
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE result INT;
    SELECT COUNT(*) INTO result FROM emp WHERE year(hiredate) < param;
    RETURN result;
END //
DELIMITER ;

SELECT function_name(1985);
```
- must have exactly only one **return** value

**trigger**
- 테이블에서 변화가 발생하면 자동 실행되는 코드
- before / after + insert / update / delete 조합
- 뭔가 바뀌는 순간 (**delete, insert, update**) 자동 실행
- new, old 키워드를 사용해서 변경 전/후 데이터 접근

```mysql
delimiter //
create trigger trg_backup_order
after delete on orders
for each row -- ensure that the trigger runs once per affected row
begin
	insert into backup_order(order_id, customer_id, product_id, order_date, quantity)
	values (old.older_id, old.customer_id, old.product_id, old.order_date, old.quantity);

//
delimiter ;
```
- ***"FOR EACH ROW" 는 CREATE TRIGGER 에 필수 항목**
- does not work on **TRUNCATE**
- okay triggered by changes in the data (cannot just run it like a function)
- cannot use it on view

**변수 선언 & 사용**
- declare variable_name int;
- set variable_name = 5;

**if inside procedure**
- if some_condition then
- -- something
- else
- -- something else
- end it

```
-- view triggers on a specific table
show triggers;
show triggers like 'table_name';

-- view trigger source code
select trigger_name, action_statement
from information_schema.triggers
where trigger_schema = 'your_database_name'

-- view stored procedures
show procedure status; --view all procedures
show procedure status where db = 'your_database_name'; -- filter by current database
show create procedure_name; -- view procedure definition

```






https://datalemur.com/sql-tutorial/sql-union-intercept-except
https://datalemur.com/questions/prime-warehouse-storage
----

**partition**
- MySQL에서 테이블 파티션은 큰 테이블을 더 작은, 더 관리하기 쉬운 부분으로 나누는 방법입니다.
- 이를 통해 쿼리 성능을 향상시키고 데이터 관리를 용이하게 할 수 있습니다. 파티션은 물리적으로는 하나의 테이블이지만, 논리적으로는 여러 개의 작은 테이블처럼 동작합니다.

- **partition 유형**
	- **range**
		- 특정 범위의 값을 기준으로 데이터 분할
		- 날짜 범위, 숫자 범위 등
	- **list**
		- 특정 값 목록을 기준으로 데이터 분할
		- 특정 지역 코드, 상태 코드 등
	- **hash**
		- 해시 함수를 사용하여 데이터를 균등하게 분할
		- 특정 열의 해시 값 기준으로 분할
	- **key**
		- mysql 내부 해시 함수를 사용하여 분할
		- 해시 함수를 자동으로 선택

- ***When using "partition by", the partition key must be part of every unique key****
	- unique key
	- primary key


**정규화**
- 중복 데이터를 줄이고, 데이터 무결성을 높이기 위한 데이터베이스 설계 원칙

| 정규형 | 목적                                                                                           | 조치 내용                | 장점                          |
| --- | -------------------------------------------------------------------------------------------- | -------------------- | --------------------------- |
| 1NF | 원자값 유지(칸마다 하나만!)                                                                             | 반복값 분리 (제작사 등)       | 처리 용이, 검색 정확도 향상            |
| 2NF | 부분 종속 제거(중복되는 걸 다른 표로!)                                                                      | 기관 정보(수입사, 배급사 등) 분리 | 중복 제거(저장공간 확보), 무결성 유지,     |
| 3NF | 이행적 종속 제거(정보는 한 곳에만!)                                                                        | 의미 단위 분리             | 테이블 의미 명확화                  |
| 4NF | 다치 종속 제거(여러 개의 독립적인 정보는 따로!)                                                                 | N:M 관계 테이블 분리        | 관계 명확, 중복 최소화, 관련이 없는 조합 분리 |
| 5NF | 조인 종속 제거(**복합키 사용,** 때로는 중복을 감수하더라도 서로 다른 필드의 데이터가 정확히 같이 있어야 의미가 있는 경우 **정확한 관계**를 다시 잘 연결) | 조인 복원 보장             | 데이터 정확성, 구조 확장 용이           |

1nf
- ensure that data contains only atomic values
- atomic values
	- each entry in a column is indivisible

2nf
- eliminate partial dependencies
	- assure that non-key attributes depend only on the primary key
- **primary key 가 한개 이상의 열로 이루어진 테이블이 존재하는지**
	- 2개 이상의 column으로 uniqueness 를 보장해야 할 시
- **is any non-prime attribute functionally dependent on the whole key**
	- functionally dependent : A열의 값이 B열의 값을 1:1로 유추할 수 있을 시
- redundancy patterns
	- 한 column 에 중복되는 값이 존재
	- non-prime attribute 들이 compose key 의 일부 (한 column) 에 대해서만 dependent 할 시

3nf
- remove transitive dependency
	- ensure non-key attribute
1. **define non-prime attributes**
2. **for each non-prime attribute**
	1. is it fully dependent on the entire key?
	2. is it indirectly dependent on the key (is it dependent on another column, and that column is dependent on the pk) 
		1. if yes -> transitive dependency
	3. does non-prime attribute determine another non-prime attribute


bcnf
- every determinant is a candidate key
	- determinant : attribute that determines another attribute


4nf
- there is non-trivial multivalued dependencies other than a candidate key


5nf
- every join dependency in the relation is implied by the candidate keys
1. are you storing 3 or more independent relationships in one table?
2. can the table be decomposed into 3+ tables and joined back losslessly
3. is there a lossy join when decomposing into 3+ relations?
	1. lossy join : when you join the decomposed relations, 
		1. you get extra rows
		2. you lose information (some rows disappear)
4. are there join dependencies not implied by candidate keys?
	1. if so did you decompose into minimal projections to fix it?

***Determine logical groups and their relationship first, then perform normalization within each group***

***always model the data objectively based on the real-world entities and relationships, then layer business-centric views or query paths on top of that***


**pivoting**
- rotating table by converting unique values from a single column into multiple columns

```
SELECT
  superhero_alias,
  MAX(CASE WHEN platform = 'Instagram' THEN engagement_rate END) AS instagram_engagement_rate,
  MAX(CASE WHEN platform = 'Twitter' THEN engagement_rate END) AS twitter_engagement_rate,
  MAX(CASE WHEN platform = 'TikTok' THEN engagement_rate END) AS tiktok_engagement_rate,
  MAX(CASE WHEN platform = 'YouTube' THEN engagement_rate END) AS youtube_engagement_rate
FROM marvel_avengers
WHERE superhero_alias IN ('Iron Man', 'Captain America', 'Black Widow', 'Thor')
GROUP BY superhero_alias
ORDER BY superhero_alias;
```

**unpivoting**
- transform columns into row values
- equivalant to `melt`

```
SELECT
  superhero_alias,
  platform,
  CASE platform
    WHEN 'Instagram' THEN engagement_rate
    WHEN 'Twitter' THEN engagement_rate
    WHEN 'YouTube' THEN engagement_rate
    WHEN 'TikTok' THEN engagement_rate
  END AS engagement_rate
FROM marvel_avengers
WHERE superhero_alias IN ('Iron Man', 'Captain America', 'Black Widow', 'Thor')
ORDER BY superhero_alias;
```

---
**sql regex**
- \d - digit
- \w - any letter
- \s - " "
- \t - tab

**pattern matching (only available in postgresql)**
- `~` - case insensitive
- `!~` - not & case insensitive
	- used like `Like`
- `SELECT * FROM users WHERE name !~* 'admin';`

---
**Nulls behavior**
- COUNT(col)
	- if column is (1, NULL), then it returns 1 (ignores NULL when count)
- COUNT (`*`)
	- counts rows, return 2
- NOT IN()
	- fails if the subquery returns any NULL
- NOT EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id)
	- NULL-safe
	- inside exists/not exists, the actual columns don't matter
	- the engine only checks if a row exists
		- `SELECT 1`, `SELECT *`, `SELECT 42`—all equivalent for existence checks.

**postgresql equivalance of last_value that is not null (ignore null)**
```sql
COALESCE(
  val,
  MAX(CASE WHEN val IS NOT NULL THEN val END)
    OVER (PARTITION BY acct ORDER BY ts
          ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING)
)
```

**anti-join**
- a query that returns rows in A that have no match in B

**boolean expression on ON**
- it filters before/during the join
- where filters after the join


**ALL()**
- `WHERE val >= ALL(SELECT val FROM t)`
- determine bool based on OP


60 min
24 mcq
4 sql
1 diagram (drag and drop)





# 📝 SQL Window & Pivot Cheatsheet (Data Engineer Preset)

## 1. Ranking & Row Number

`-- Row number within partition 
```sql
SELECT      customer_id,     order_date,     ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS rn FROM orders;  
```

```sql
-- Ranking with ties 
SELECT      product_id,     RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rnk FROM sales;  

-- Dense ranking (no gaps) 
SELECT      product_id,     DENSE_RANK() OVER (ORDER BY revenue DESC) AS drnk FROM sales;
```
---

## 2. First / Last Value

`-- First and last purchase date per customer SELECT      customer_id,     FIRST_VALUE(order_date) OVER (PARTITION BY customer_id ORDER BY order_date ASC) AS first_order,     LAST_VALUE(order_date) OVER (PARTITION BY customer_id ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_order FROM orders;`

⚠️ Remember: `LAST_VALUE` needs a proper frame (`ROWS BETWEEN ...`) or it defaults incorrectly.

---

## 3. Moving / Sliding Windows

`-- 7-day rolling sum of revenue SELECT     order_date,     SUM(revenue) OVER (         ORDER BY order_date         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW     ) AS rolling_7d_sum FROM sales;  -- Previous and next row comparison SELECT     order_id,     revenue,     LAG(revenue, 1) OVER (ORDER BY order_date) AS prev_revenue,     LEAD(revenue, 1) OVER (ORDER BY order_date) AS next_revenue FROM sales;`

---

## 4. Running Totals & Cumulative Dist

`-- Running total of sales per customer SELECT      customer_id,     order_date,     SUM(amount) OVER (PARTITION BY customer_id ORDER BY order_date ROWS UNBOUNDED PRECEDING) AS running_total FROM orders;  -- Cumulative percentage SELECT      product_id,     revenue,     SUM(revenue) OVER (ORDER BY revenue DESC) * 1.0 /     SUM(revenue) OVER () AS cumulative_pct FROM sales;`

---

## 5. Percentiles & N-Tile

`-- Quartiles SELECT      customer_id,     NTILE(4) OVER (ORDER BY total_spent DESC) AS quartile FROM customer_spending;  -- Median revenue (approx using percentile) SELECT DISTINCT     PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY revenue)      OVER () AS median_revenue FROM sales;`

---

## 6. Pivot / Unpivot

`-- Pivot: sales by region into columns SELECT * FROM (     SELECT region, product, revenue     FROM sales ) src PIVOT (     SUM(revenue)     FOR region IN ([APAC], [EMEA], [AMER]) ) p;  -- Unpivot: columns back into rows SELECT product, region, revenue FROM sales_pivoted UNPIVOT (     revenue FOR region IN ([APAC], [EMEA], [AMER]) ) u;`

💡 Works in SQL Server/Oracle. For Postgres/ANSI: use `FILTER` or `CASE WHEN` aggregations.

---

## 7. Conditional Aggregates (Poor Man’s Pivot)

`-- Pivot-like aggregation (portable SQL) SELECT     product,     SUM(CASE WHEN region = 'APAC' THEN revenue END) AS apac_rev,     SUM(CASE WHEN region = 'EMEA' THEN revenue END) AS emea_rev,     SUM(CASE WHEN region = 'AMER' THEN revenue END) AS amer_rev FROM sales GROUP BY product;`

---

## 8. Gap & Island Detection (Sessions)

`-- Identify continuous sessions (gaps > 1 day = new session) SELECT      user_id,     login_date,     SUM(CASE WHEN DATEDIFF(day, LAG(login_date) OVER (PARTITION BY user_id ORDER BY login_date), login_date) > 1 THEN 1 ELSE 0 END)         OVER (PARTITION BY user_id ORDER BY login_date) AS session_id FROM logins;`

---

## 9. Top-N Per Group

`-- Get top 3 products by revenue in each category SELECT * FROM (     SELECT          category,         product,         revenue,         ROW_NUMBER() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn     FROM sales ) t WHERE rn <= 3;`

---

## 10. Window Function + Joins (Common Data Eng Use)

`-- Detect anomalies: revenue deviation from group avg SELECT     s.*,     revenue - AVG(revenue) OVER (PARTITION BY category) AS deviation FROM sales s;`













----

#### Null Cheat Sheet

# 1) What NULL _is_ (and is not)
- **Meaning:** “unknown / missing / not applicable.” It is **not** a value.
- **Not the same as:** `0`, empty string, `FALSE`, or a blank. (One major vendor historically treats empty string as NULL—don’t rely on that across systems.)
    

---

# 2) Three-valued logic (3VL)

- SQL uses **TRUE / FALSE / UNKNOWN**    
- Any comparison with NULL (e.g., `=`, `<>`, `<`, `>=`) yields **UNKNOWN**.
- **WHERE keeps only TRUE.** Rows that evaluate to FALSE **or UNKNOWN** are filtered out.
    
- Boolean rules you must know:
    - `TRUE AND UNKNOWN` → UNKNOWN
    - `FALSE AND UNKNOWN` → FALSE
    - `TRUE OR UNKNOWN` → TRUE
    - `FALSE OR UNKNOWN` → UNKNOWN
    - `NOT UNKNOWN` → UNKNOWN

> Exam trap: a predicate that becomes UNKNOWN **won’t pass the WHERE filter**.

---

# 3) Testing for NULL

- Use **“is nullness” predicates**: **IS NULL** / **IS NOT NULL**.
- Equality/inequality operators (`=`, `<>`) **do not** work with NULL.
- Some DBs support **null-safe equality** (treats `NULL` = `NULL` as TRUE) or **“is distinct from”** style operations. These are _not_ universal—know they exist conceptually.

---

# 4) Comparisons, arithmetic, concatenation
- **Comparisons:** any `value OP NULL` → **UNKNOWN**.
- **Arithmetic:** `NULL` in an expression → **NULL** result (e.g., `price * NULL` → NULL).
- **String concat:** if any side is NULL → result is usually **NULL** (don’t assume empty string).
---

# 5) Aggregates & GROUP BY
- **COUNT(`*`)** counts **rows** (includes NULL rows).
- **COUNT(col)** counts **non-NULL values** only.
- **SUM/AVG/MIN/MAX** **ignore NULL inputs** (they aggregate only existing values).
- **AVG** equals `SUM(non-NULL)/COUNT(non-NULL)`.
- **GROUP BY**: all rows with NULL in a grouping column form **one group** (treat NULLs as one bucket).
- **DISTINCT**: multiple NULLs collapse to a **single distinct** NULL.

> Exam traps:  
> • “Why do my counts differ?” → `COUNT(col)` vs `COUNT(*)`.  
> • “AVG with NULLs” → denominator is non-NULL count.

---

# 6) HAVING vs WHERE with NULL

- **WHERE** filters **before** aggregation; **HAVING** filters **after** aggregation.
- A `WHERE col = something` drops NULL rows (predicate is UNKNOWN).
- To filter on aggregate results (which already skipped NULLs), use **HAVING**.

---

# 7) Joins & NULL
- Join conditions that compare to NULL **never match** (comparison becomes UNKNOWN).
- **INNER JOIN:** rows with NULLs in the join keys will not match.
- **LEFT JOIN:** keeps all left rows; unmatched right side appears as NULLs.
- **Filtering after LEFT JOIN**:
    - Put right-table conditions in **WHERE** → might **turn it into an inner-like** (unmatched rows dropped).
    - Put right-table conditions in the **ON** clause → **preserve** unmatched left rows; unmatched rights stay NULL.

> Diagram/MCQ trap: “Why did my LEFT JOIN drop rows?” → because a right-side filter was placed in WHERE instead of ON.

---

# 8) Anti-joins (A without matching B)

- Safe patterns conceptually:
    - **LEFT-join then keep rows where right is NULL.**
    - **NOT EXISTS** subquery.

- **NOT IN** subquery is dangerous if the subquery can return **NULLs**; the result becomes UNKNOWN and often matches **nothing**.

```sql
SELECT c.id, c.name
FROM customers c
WHERE c.id NOT IN (SELECT cust_id FROM orders);
Subquery result: {1, 2, NULL}

Now check each customer:

Alice (1): 1 NOT IN {1,2,NULL} → UNKNOWN (because NULL in set) → excluded.

Bob (2): same → UNKNOWN → excluded.

Carol (3): 3 NOT IN {1,2,NULL} → UNKNOWN → excluded.

Dave (4): 4 NOT IN {1,2,NULL} → UNKNOWN → excluded.
```

> MCQ classic: “Which anti-join is NULL-safe?” → NOT EXISTS or LEFT…IS NULL; avoid NOT IN if the subquery column may contain NULL.

---

# 9) Subqueries & NULL

- **IN** with a subquery that returns NULLs still works for positives, but:
    
- **NOT IN** with **any** NULL in subquery → overall predicate becomes UNKNOWN → typically yields **no rows**.
    
- **EXISTS/NOT EXISTS** aren’t affected by subquery column values (only row existence).
    

---

# 10) ORDER BY with NULLs

- Default placement (**NULLS FIRST/LAST**) **varies by vendor** and by sort direction.
    
- Best practice: **explicitly choose** first/last if you care. (Conceptual note—syntax varies.)
    

---

# 11) Window functions & NULL

- **Windowed aggregates** follow the same rule as normal aggregates: **they ignore NULL inputs**.
    
- **Window frames**:
    
    - **ROWS**: row-count windows (e.g., last 3 rows).
        
    - **RANGE**: value-based windows (e.g., last 7 days by timestamp/order key).
        
- “Last non-NULL” carry-forward generally requires a special technique; some engines offer “ignore NULLs” options, others don’t. Concept: compute a running “last seen non-NULL” and coalesce.

- **Positional window functions (first_value, last_value, ...)**
	- do not ignore NULLs by default

> MCQ trap: “7-day sliding window with multiple rows/day” → use **value-based** (RANGE by time), not row-based (ROWS).

---

# 12) CASE expressions with NULL

- Comparisons like `CASE WHEN salary > 0` with `salary` NULL → condition is UNKNOWN → falls to **ELSE**.
    
- To treat NULL as 0 (or text), **wrap with a NULL-handling function** (see §14).

---

# 13) Constraints & NULL

- **PRIMARY KEY** implies **NOT NULL** (no NULLs allowed).
    
- **UNIQUE** constraints typically allow **multiple NULLs** because NULL ≠ NULL (not equal, unknown).
    
- **FOREIGN KEY** columns may be **NULL** → means “no reference” (not a violation).
    
- **CHECK** constraints can forbid NULLs explicitly.
    

> Exam trap: “Can unique columns have multiple NULLs?” → Generally **yes**.

---

# 14) NULL-handling functions (portable concepts)

- **COALESCE(a, b, c, …)**: returns the first **non-NULL** argument.
    
    - Use to **default** NULLs (e.g., to 0 or ‘N/A’) or to align datatypes.
        
- **NULLIF(a, b)**: returns **NULL** if `a` equals `b`, else returns `a`.
    
    - Use to **avoid divide-by-zero** (e.g., divide by `NULLIF(den, 0)` → becomes NULL instead of error).
        
- Vendor-specific aliases (concept only): most platforms have a short form (e.g., “if-null”) but names differ—**COALESCE/NULLIF** are the portable ideas.
    
- **IS [NOT] NULL**: only reliable way to test nullness across engines.
    

---

# 15) DISTINCT, UNION, INTERSECT, EXCEPT with NULL

- **DISTINCT** treats all NULLs as the **same** for deduplication.
    
- **UNION** (and UNION ALL vs UNION): with **UNION** duplicates are removed—**NULL rows dedupe** as well.
    
- **INTERSECT/EXCEPT**: comparisons use SQL semantics; NULLs compare in a way consistent with set logic (e.g., a NULL row matches another identical NULL row structurally).
    

---

# 16) Defaults vs NULL on insert

- **Omitting a column** can trigger its **DEFAULT**.
    
- **Inserting NULL explicitly** typically **bypasses** the DEFAULT and stores NULL (unless constrained).
    
- If column is **NOT NULL**, inserting NULL fails.
    

---

# 17) Indexes & NULL (high-level)

- Whether NULLs are indexed or how they behave in indexes can **vary**.  
    Treat **IS NULL** and **IS NOT NULL** predicates as potentially indexable, but **do not rely** on uniform behavior across engines.
    

---

# 18) Data-quality patterns with NULL

- Decide per column: does NULL mean “unknown,” or should you store a **sentinel** (e.g., 0 or empty string)? Mixing both leads to pain.
    
- For facts:
    
    - Use **foreign-key checks** to prevent orphaned references (or quarantine rows).
        
    - Use **COALESCE** for numeric rollups when you truly want missing = 0.
        
- For dimensions:
    
    - Be consistent: NULL vs “Unknown” member (a special key) for referential completeness.
        

---

# 19) Classic NULL pitfalls (you’ll see these in tests)

1. **`NOT IN` with NULLs in the subquery** → returns **no rows**. Use **NOT EXISTS** or clean the NULLs.
    
2. **LEFT JOIN + WHERE filter on right** → drops unmatched rows (behaves like inner). Put the filter in **ON** to preserve left rows.
    
3. **`COUNT(col)` vs `COUNT(*)`** → they differ when col has NULLs.
    
4. **AVG with NULLs** → denominator is count of **non-NULL** rows.
    
5. **Comparisons to NULL** → always UNKNOWN; use **IS NULL**.
    
6. **Window: ROWS vs RANGE** → only RANGE can express value-based windows like “last 7 days.”
    
7. **UNIQUE with NULLs** → generally allows multiple NULLs.
    

---

# 20) Quick mental checklist for any SQL with NULLs

- Am I **testing** for NULL properly (IS NULL) instead of `= NULL`?
    
- Will this **join** lose rows because join keys can be NULL?
    
- If I’m doing **A minus B**, is my anti-join **NULL-safe** (NOT EXISTS / LEFT…IS NULL)?
    
- Do I want **row-based** or **value-based** window frames?
    
- Do my **aggregates** do what I expect with NULLs (COUNT(col) vs COUNT(*))?
    
- Should I **default** NULLs (COALESCE) for the business requirement?
    
- Do I need a **BI-safe** “Unknown member” instead of NULL for a foreign key?


---

