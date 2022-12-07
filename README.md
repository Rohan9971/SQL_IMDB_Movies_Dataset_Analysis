
# SQL IMDB Movies Dataset Analysis For RSVP Movies 



SQL queries performed on IMDb database to provide recommendations to RSVP Movies based on insights.


## Table Content

 - Problem Statement

- Approach

- Techmologies Used

- Screenshots

- Queries Performed On The Dataset

- Summary
## Problem statement
RSVP Movies is an Indian film production company which has produced many super-hit movies. They have usually released movies for the Indian audience but for their next project, they are planning to release a movie for the global audience in upcoming future.
## Approach 

- Download the IMDb dataset.

- The first tab contains the ERD and the table details. Study that carefully and understand the relationships between the table.

- Inspect each table given in the subsequent tabs and understand the features associated with each of them.

- Open your MySQL Workbench and start writing the DDL and DML commands to create the database.

If you don't wish to perform the data loading part, you can directly download the SQL script file containing all the commands and data required for the database creation and start directly with the querying.
## Technologies Used

**MySQL Workbench** 

**Microsoft Excel** 


## Screenshots

**Entity Relationship Diagram (ERD)**

![ERdiagram](https://user-images.githubusercontent.com/112953571/206124002-580b22fa-fd77-4b97-b406-ab9b8f146bd5.png)


**Table (Schema)**


![table](https://user-images.githubusercontent.com/112953571/206124083-e88e8b12-6f2c-4795-bfa9-06e7a1927a60.png)


**Movies Table**


![movies](https://user-images.githubusercontent.com/112953571/206124486-35373841-2f28-4f8a-be80-6d4fd6a169dd.png)



**genre**

![genre](https://user-images.githubusercontent.com/112953571/206143741-0e343e70-e111-4c8f-be32-09e525984557.png)


**director_mapping**


![director_mapping](https://user-images.githubusercontent.com/112953571/206144148-5e115f40-601e-40db-9347-1f6198b8c5b3.png



**role_mapping**

![role_maping](https://user-images.githubusercontent.com/112953571/206144320-0f42261b-7d54-47ea-90bd-f0c6ed9951d1.png)



**names**

![names](https://user-images.githubusercontent.com/112953571/206144422-32d2f7d1-d13c-4cbd-9540-ac0ba1f716f7.png)


**ratings**


![ratings](https://user-images.githubusercontent.com/112953571/206144536-b0784ff4-173c-472c-b0fa-081e55aa1b9f.png)




## Queries Performed On The Dataset

 Q1. FIND THE TOTAL NUMBER OF ROWS IN EACH TABLE OF THE SCHEMA?


**QUERY**

```
SELECT
TABLE_NAME, SUM(TABLE_ROWS)
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = 'IMDB'
GROUP BY TABLE_NAME;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans1.png?raw=true)


Q2. WHICH COLUMNS IN THE MOVIE TABLE HAVE NULL VALUES?

**QUERY**

```
SELECT
(SELECT count(*) FROM movie WHERE id is NULL) as id,
(SELECT count(*) FROM movie WHERE title is NULL) as title,
(SELECT count(*) FROM movie WHERE year  is NULL) as year,
(SELECT count(*) FROM movie WHERE date_published  is NULL) as date_published,
(SELECT count(*) FROM movie WHERE duration  is NULL) as duration,
(SELECT count(*) FROM movie WHERE country  is NULL) as year, 
(SELECT count(*) FROM movie WHERE worlwide_gross_income  is NULL) as worlwide_gross_income,
(SELECT count(*) FROM movie WHERE languages  is NULL) as languages,
(SELECT count(*) FROM movie WHERE production_company  is NULL) as production_company
;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans2.png?raw=true)


Q3. FIND THE TOTAL NUMBER OF MOVIES RELEASED EACH YEAR? HOW DOES THE TREND LOOK MONTH WISE?


**QUERY**

```
SELECT year,
       Count(title) AS NUMBER_OF_MOVIES
FROM   movie
GROUP  BY year;

```
```
SELECT Month(date_published) AS MONTH_NUM,
       Count(*)              AS NUMBER_OF_MOVIES
FROM   movie
GROUP  BY month_num
ORDER  BY month_num;

```

**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans3.1.png?raw=true)

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans3.2.png?raw=true)

Q4. HOW MANY MOVIES WERE PRODUCED IN THE USA OR INDIA IN THE YEAR 2019?

**QUERY**

```
SELECT Count(DISTINCT id) AS number_of_movies, year
FROM   movie
WHERE  ( upper(country) LIKE '%INDIA%'
          OR upper(country) LIKE '%USA%' )
       AND year = 2019; 

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans4.png?raw=true)


Q5. FIND THE UNIQUE LIST OF THE GENRES PRESENT IN THE DATA SET?

**QUERY**

```
SELECT DISTINCT genre FROM   genre; 

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans5.png?raw=true)


Q6.WHICH GENRE HAD THE HIGHEST NUMBER OF MOVIES PRODUCED OVERALL?

**QUERY**

```
SELECT     genre,
           Count(mov.id) AS number_of_movies
FROM       movie       AS mov
INNER JOIN genre       AS gen
where      gen.movie_id = mov.id
GROUP BY   genre
ORDER BY   number_of_movies DESC limit 1 ;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans6.png?raw=true)

Q7. HOW MANY MOVIES BELONG TO ONLY ONE GENRE?


**QUERY**

```
SELECT genre_count, 
       Count(movie_id) movie_count
FROM (SELECT movie_id, Count(genre) genre_count
      FROM genre
      GROUP BY movie_id
      ORDER BY genre_count DESC) genre_counts
WHERE genre_count = 1
GROUP BY genre_count;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans7.png?raw=true)


Q8.WHAT IS THE AVERAGE DURATION OF MOVIES IN EACH GENRE? 

**QUERY**

```
SELECT     genre,
           Round(Avg(duration),2) AS avg_duration
FROM       movie as mov
INNER JOIN genre as gen
ON      gen.movie_id = mov.id
GROUP BY   genre
ORDER BY avg_duration DESC;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans8.png?raw=true)



Q9.WHAT IS THE RANK OF THE ‘THRILLER’ GENRE OF MOVIES AMONG ALL THE GENRES IN TERMS OF NUMBER OF MOVIES PRODUCED? 


**QUERY**

```
WITH genre_summary AS
(
   SELECT  
      genre,
	  Count(movie_id) AS movie_count ,
	  Rank() OVER(ORDER BY Count(movie_id) DESC) AS genre_rank
   FROM       genre                                 
   GROUP BY   genre 
   )
SELECT *
FROM   genre_summary
WHERE  genre = "THRILLER" ;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans9.png?raw=true)



Q10.  FIND THE MINIMUM AND MAXIMUM VALUES IN  EACH COLUMN OF THE RATINGS TABLE EXCEPT THE movies_id COLUMN?

**QUERY**

```
SELECT 
   Min(avg_rating) AS min_avg_rating,
   Max(avg_rating) AS max_avg_rating,
   Min(total_votes) AS min_total_votes,
   Max(total_votes) AS max_total_votes,
   Min(median_rating) AS min_median_rating,
   Max(median_rating) AS min_median_rating
FROM   ratings;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans10.png?raw=true)


Q11. WHICH ARE THE TOP 10 MOVIES BASED ON AVERAGE RATING?


**QUERY**

```
SELECT     
   title,
   avg_rating,
   Rank() OVER(ORDER BY avg_rating DESC) AS movie_rank
FROM ratings AS rat
INNER JOIN movie AS mov
ON mov.id = rat.movie_id limit 10;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans11.png?raw=true)


Q12. SUMMARISE THE RATINGS TABLE BASED ON THE MOVIE COUNTS BY MEDIAN RATINGS.

**QUERY**

```
SELECT median_rating,
       Count(movie_id) AS movie_count
FROM ratings
GROUP BY median_rating
ORDER BY movie_count DESC;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans12.png?raw=true)


Q13. WHICH PRODUCTION HOUSE HAS PRODUCED THE MOST NUMBER OF HIT MOVIES (AVERAGE RATING > 8)?

**QUERY**

```
SELECT production_company,
      Count(movie_id) AS movie_count, 
      Rank() OVER( ORDER BY Count(movie_id) DESC ) AS prod_company_rank
FROM ratings AS rat
     INNER JOIN movie AS mov
     ON mov.id = rat.movie_id
WHERE avg_rating > 8
     AND production_company IS NOT NULL
GROUP BY production_company;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans13.png?raw=true)


Q14. HOW MANY MOVIES RELEASED IN EACH GENRE DURING MARCH 2017 IN THE USA HAD MORE THAN 1,000 VOTES?

**QUERY**

```
SELECT genre,
       Count(mov.id) AS movie_count
FROM movie AS mov
     INNER JOIN genre AS gen
           ON gen.movie_id = mov.id
     INNER JOIN ratings AS rat
           ON rat.movie_id = mov.id
WHERE year = 2017
	  AND Month(date_published) = 3
	  AND country LIKE '%USA%'
	  AND total_votes > 1000
GROUP BY genre
ORDER BY movie_count DESC;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans14.png?raw=true)

Q15. FIND MOVIES OF EACH GENRE THAT START WITH THE WORD ‘THE’ AND WHICH HAVE AN AVERAGE RATING > 8?

**QUERY**

```
SELECT title, avg_rating, genre
FROM movie AS mov
     INNER JOIN genre AS gen
           ON gen.movie_id = mov.id
     INNER JOIN ratings AS rat
               ON rat.movie_id = mov.id
WHERE avg_rating > 8
	  AND title LIKE 'THE%'
ORDER BY avg_rating DESC;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans15.png?raw=true)


Q16. OF THE MOVIES RELEASED BETWEEN 1 APRIL 2018 AND 1 APRIL 2019, HOW MANY WERE GIVEN A MEDIAN RATING OF 8?


**QUERY**

```
SELECT 
   median_rating,
Count(*) AS movie_count
FROM movie AS mov INNER JOIN 
     ratings AS rat ON rat.movie_id = mov.id
WHERE median_rating = 8
	  AND date_published BETWEEN '2018-04-01' AND '2019-04-01'
GROUP BY median_rating;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans16.png?raw=true)


Q17. DO GERMAN MOVIES GET MORE VOTES THAN ITALIAN MOVIES?

**QUERY**

```
SELECT 
   country, 
   sum(total_votes) as total_votes
FROM movie AS mov
	INNER JOIN ratings as rat
          ON mov.id=rat.movie_id
WHERE lower(country) = 'germany' or lower(country) = 'italy'
GROUP BY country;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans17.png?raw=true)

Q18. WHICH COLUMNS IN THE NAMES TABLE HAVE NULL VALUES?

**QUERY**

```
SELECT Sum(CASE
             WHEN name IS NULL THEN 1
             ELSE 0
           END) AS name_null,
       Sum(CASE
             WHEN height IS NULL THEN 1
             ELSE 0
           END) AS height_null,
       Sum(CASE
             WHEN date_of_birth IS NULL THEN 1
             ELSE 0
           END) AS date_of_birth_null,
       Sum(CASE
             WHEN known_for_movies IS NULL THEN 1
             ELSE 0
           END) AS known_for_movies_null
FROM names;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans18.png?raw=true)



Q19. WHO ARE THE TOP THREE DIRECTORS IN THE TOP THREE GENRES WHOSE MOVIES HAVE AN AVERAGE RATING > 8?

**QUERY**

```
WITH top_3_genres
AS (
    SELECT genre,
	   Count(mov.id) AS movie_count ,
	   Rank() OVER(ORDER BY Count(mov.id) DESC) AS genre_rank
    FROM movie AS mov
	   INNER JOIN genre AS gen
			 ON gen.movie_id = mov.id
	   INNER JOIN ratings AS rat
			 ON rat.movie_id = mov.id  
    WHERE avg_rating > 8
    GROUP BY genre limit 3 
    )
SELECT 
    nam.NAME AS director_name ,
	Count(dm.movie_id) AS movie_count
FROM director_mapping AS dm
       INNER JOIN genre gen using (movie_id)
       INNER JOIN names AS nam
       ON nam.id = dm.name_id
       INNER JOIN top_3_genres using (genre)
       INNER JOIN ratings using (movie_id)
WHERE avg_rating > 8
GROUP BY name
ORDER BY movie_count DESC limit 3 ;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans19.png?raw=true)


Q20. WHO ARE THE TOP TWO ACTORS WHOSE MOVIES HAVE A MEDIAN RATING >= 8?

**QUERY**

```
SELECT 
   nam.name AS actor_name,
       Count(movie_id) AS movie_count
FROM role_mapping AS rm
       INNER JOIN movie AS mov
             ON mov.id = rm.movie_id
       INNER JOIN ratings AS rat USING(movie_id)
       INNER JOIN names AS nam
             ON nam.id = rm.name_id
WHERE rat.median_rating >= 8
	  AND category = 'actor'
GROUP BY actor_name
ORDER BY movie_count DESC LIMIT 2;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans20.png?raw=true)



Q21. WHICH ARE THE TOP THREE PRODUCTION HOUSES BASED ON THE NUMBER OF VOTES RECEIVED BY THEIR MOVIES?

**QUERY**

```
SELECT 
   production_company,
   Sum(total_votes) AS vote_count,
   Rank() OVER(ORDER BY Sum(total_votes) DESC) AS prod_comp_rank
FROM movie AS mov
INNER JOIN ratings AS rat
	  ON rat.movie_id = mov.id
GROUP BY production_company LIMIT 3;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans21.png?raw=true)


Q22. RANK ACTORS WITH MOVIES RELEASED IN INDIA BASED ON THEIR AVERAGE RATINGS. WHICH ACTOR IS AT THE TOP OF THE LIST?


**QUERY**

```
WITH actor_summary
     AS (SELECT n.name AS actor_name, total_votes,
                Count(R.movie_id) AS movie_count,
                Round(Sum(avg_rating * total_votes) / Sum(total_votes), 2) AS actor_avg_rating
         FROM movie AS m
                INNER JOIN ratings AS r
                      ON m.id = r.movie_id
                INNER JOIN role_mapping AS rm
					  ON m.id = rm.movie_id
                INNER JOIN names AS n
                        ON rm.name_id = n.id
         WHERE category = 'actor'
                AND country = "india"
         GROUP BY name
         HAVING movie_count >= 5)
SELECT *,
       Rank() OVER(ORDER BY actor_avg_rating DESC) AS actor_rank
FROM actor_summary;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans22.png?raw=true)



Q23.Find out the top five actresses in Hindi movies released in India based on their average ratings? 

**QUERY**

```
WITH actress_detail
	 AS(
       SELECT 
          n.name AS actress_name, total_votes,
		  Count(r.movie_id) AS movie_count,
		  Round(Sum(avg_rating*total_votes)/Sum(total_votes),2) AS actress_avg_rating
        FROM movie AS m
             INNER JOIN ratings AS r
                   ON m.id=r.movie_id
			 INNER JOIN role_mapping AS rm
                   ON m.id = rm.movie_id
			 INNER JOIN names AS n
                   ON rm.name_id = n.id
	    WHERE Upper(category) = 'ACTRESS'
              AND Upper(country) = "INDIA"
              AND Upper(languages) LIKE '%HINDI%'
	   GROUP BY name
	   HAVING movie_count>=3 
       )
SELECT *,
         Rank() OVER(ORDER BY actress_avg_rating DESC) AS actress_rank
FROM actress_detail LIMIT 5;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans23.png?raw=true)


Q24. Select thriller movies as per avg rating and classify them in the following category: 

Rating > 8: Superhit movies

Rating between 7 and 8: Hit movies

Rating between 5 and 7: One-time-watch movies

Rating < 5: Flop movies

 
**QUERY**

```
with thriller_movies as (
    select  
       distinct title, 
       avg_rating
    from movie as mov inner join ratings as rat
         on mov.id = rat.movie_id 
         inner join genre as gen on gen.movie_id = mov.id
	where genre like 'THRILLER')
select *, 
       case 
         when avg_rating > 8 then 'superhit movies'
         when avg_rating between 7 and 8  then 'Hit movies'
         when avg_rating between 5 and 7 then 'one-time-watch movies'
         else 'Flop movies'
		end as avg_rating_category
from thriller_movies ;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans24.png?raw=true)


Q25. What is the genre-wise running total and moving average of the average movie duration? 

**QUERY**

```
SELECT genre,
       ROUND(AVG(duration),2) AS avg_duration,
       SUM(ROUND(AVG(duration),2)) OVER(ORDER BY genre ROWS UNBOUNDED PRECEDING) AS running_total_duration,
       ROUND(AVG(AVG(duration)) OVER(ORDER BY genre ROWS 10 PRECEDING),2) AS moving_avg_duration
FROM movie AS m 
INNER JOIN genre AS g 
ON m.id= g.movie_id
GROUP BY genre
ORDER BY genre;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans25.png?raw=true)


Q26. WHICH ARE THE FIVE HIGHEST-GROSSING MOVIES OF EACH YEAR THAT BELONG TO THE TOP THREE GENRES?

**QUERY**

```
WITH top_3_genres 
     AS( SELECT genre,
                Count(m.id) AS movie_count ,
                Rank() OVER(ORDER BY Count(m.id) DESC) AS genre_rank
         FROM movie AS m
              INNER JOIN genre AS g
                     ON g.movie_id = m.id
              INNER JOIN ratings AS r
                     ON r.movie_id = m.id
         GROUP BY genre limit 3 ), movie_summary 
     AS( SELECT genre, year,
                title AS movie_name,
                CAST(replace(replace(ifnull(worlwide_gross_income,0),'INR',''),'$','') AS decimal(10)) AS worlwide_gross_income ,
                DENSE_RANK() OVER(partition BY year ORDER BY CAST(replace(replace(ifnull(worlwide_gross_income,0),'INR',''),'$','') AS decimal(10))  DESC ) AS movie_rank
         FROM movie AS m
              INNER JOIN genre AS g
                    ON m.id = g.movie_id
         WHERE genre IN
         ( SELECT genre FROM top_3_genres)
         GROUP BY   movie_name
          )
SELECT * FROM   movie_summary
WHERE  movie_rank<=5
ORDER BY YEAR;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans26.png?raw=true)

 Q27.  WHICH ARE THE TOP TWO PRODUCTION HOUSES THAT HAVE PRODUCED THE HIGHEST NUMBER OF HITS (MEDIAN RATING >= 8) AMONG MULTILINGUAL MOVIES?

**QUERY**

```
WITH production_company_detail
     AS (SELECT production_company,
                Count(*) AS movie_count
         FROM movie AS mov
                INNER JOIN ratings AS rat
		      ON rat.movie_id = mov.id
         WHERE median_rating >= 8
	       AND production_company IS NOT NULL
               AND Position(',' IN languages) > 0
         GROUP BY production_company
         ORDER BY movie_count DESC)
SELECT *,
       Rank() over( ORDER BY movie_count DESC) AS prod_comp_rank
FROM production_company_detail LIMIT 2;

```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans27.png?raw=true)


Q28. WHO ARE THE TOP 3 ACTRESSES BASED ON NUMBER OF SUPER HIT MOVIES (AVERAGE RATING >8) IN DRAMA GENRE?

**QUERY**

```
WITH actress_summary 
     AS( SELECT n.name AS actress_name,
                SUM(total_votes) AS total_votes,
		Count(r.movie_id) AS movie_count,
                Round(Sum(avg_rating*total_votes)/Sum(total_votes),2) AS actress_avg_rating
	FROM movie AS m
             INNER JOIN ratings AS r
                   ON m.id=r.movie_id
             INNER JOIN role_mapping AS rm
                   ON m.id = rm.movie_id
             INNER JOIN names AS n
		   ON rm.name_id = n.id
             INNER JOIN GENRE AS g
                  ON g.movie_id = m.id
	WHERE lower(category) = 'actress'
              AND avg_rating>8
              AND lower(genre) = "drama"
	GROUP BY name )
SELECT *,
	   Rank() OVER(ORDER BY movie_count DESC) AS actress_rank
FROM actress_summary LIMIT 3;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans28.png?raw=true)


Q29. GET THE FOLLOWING DETAILS FOR TOP 9 DIRECTORS (BASED ON NUMBER OF MOVIES)

DIRECTOR ID
NAME

NUMBER OF MOVIES

AVERAGE INTER MOVIE DURATION IN DAYS

AVERAGE MOVIE RATINGS

TOTAL VOTES

MIN RATING

MAX RATING

TOTAL MOVIE DURATIONS


**QUERY**

```
WITH next_date_published_detail
     AS( SELECT d.name_id, name, d.movie_id, duration, r.avg_rating, total_votes, m.date_published,
                Lead(date_published,1) OVER(partition BY d.name_id ORDER BY date_published,movie_id ) AS next_date_published
          FROM director_mapping  AS d
               INNER JOIN names  AS n
                     ON n.id = d.name_id
               INNER JOIN movie AS m
                     ON m.id = d.movie_id
               INNER JOIN ratings AS r
                     ON r.movie_id = m.id ), top_director_summary AS
( SELECT *,
         Datediff(next_date_published, date_published) AS date_difference
  FROM   next_date_published_detail )
SELECT   name_id AS director_id,
         name AS director_name,
         Count(movie_id) AS number_of_movies,
         Round(Avg(date_difference),2) AS avg_inter_movie_days,
         Round(Avg(avg_rating),2) AS avg_rating,
         Sum(total_votes) AS total_votes,
         Min(avg_rating) AS min_rating,
         Max(avg_rating) AS max_rating,
         Sum(duration) AS total_duration
FROM top_director_summary
GROUP BY director_id
ORDER BY Count(movie_id) DESC limit 9;


```
**OUTPUT**

![App Screenshot](https://github.com/Rohan9971/SQL_IMDB_Movies_Dataset_Analysis/blob/main/Screenshots/ans29.png?raw=true)





## Summary

**RSVP EXECUTIVE SUMMARY AND RECOMMENDATIONS**

There are 13 distinct genres on which RSVP Movies can make a movie. The following 
conditions will ensure that next project is succesful

-  Most movies produced in Drama genre followed by comedy and thriller, hence next 
   project has to be in one of these category

- Drama genre with less than 100 min movie time is the the best choice, also, its the one 
  mostly voted from March 2017 in USA

- While producing Action genre duration has to be high (112.88 min) followed by 
  romance and crime genres.  

- The number of movies released since 2017 (3052 movies) has fallen. In march, highest 
  number of movies were released in March (824) and lowest in December (438)  

- Kirket, Love in Kilnerry are the highest rated movies.

- Dream Warrior Pictures and National Theater Live production houses can be considered 
  as they produced most hit movies (3), with an average rating greater than 8

- German movies (4695) will be profitable, having highest votes (almost 3 times) 
  compared to Italian movies (1684)

- Highest votes received by Marvel movies, followed by Twentieth Century Fox and then 
  Warner Bros. These can be considered as world-wide release partner.

  
