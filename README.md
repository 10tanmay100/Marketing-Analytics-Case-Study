# Marketing-Analytics-Case-Study
This is the solution of Danny Ma's Marketing Analytics Case Study. Here I have applied my best SQL Skills to solve the Business Problem. I have done whole analysis on MYSQL Workbench.
![Screenshot (216)](https://user-images.githubusercontent.com/72690313/132275250-4d54f455-54f2-4c9f-b822-cf8fd40aad30.png)
########Entity Relationship Diagram
![Screenshot (218)](https://user-images.githubusercontent.com/72690313/132275381-dcbc986f-979b-482b-9b32-c4d0072ad2da.png)
########Case Study Overview
Personalized customer emails based off marketing analytics is a winning formula for 
many digital companies, and this is exactly the initiative that the leadership team at DVD 
Rental Co has decided to tackle!
We have been asked to support the customer analytics team at DVD Rental Co who 
have been tasked with generating the necessary data points required to populate 
specific parts of this first-ever customer email campaign.
Throughout this marketing case study we will cover many SQL data manipulation and 
analysis techniques. The aim is to further extend your SQL knowledge base and also 
expose you to some scenarios where you can apply some neat tricks that I’ve picked up 
over the years!
######## key Business Requirements
The marketing team have shared with us a draft of the email they wish to send to their 
customers.
###################### Requirement #1
**Top 2 Categories**
For each customer, we need to identify the top 2 categories for each customer based off 
their past rental history. These top categories will drive marketing creative images as 
seen in the travel and sci-fi examples in the draft email
###################### Requirement #2
**Category Film Recommendations**
The marketing team has also requested for the 3 most popular films for each customer’s 
top 2 categories.
There is a catch though - we cannot recommend a film which the customer has already 
viewed.
If there are less than 3 films available - marketing is happy to show at least 1 film.
Any customer which do not have any film recommendations for either category must be 
flagged out so the marketing team can exclude from the email campaign - this is of high 
importance
####################### Requirement #3 & #4
**Individual Customer Insights**
The number of films watched by each customer in their top 2 categories is required as 
well as some specific insights.
For the 1st category, the marketing requires the following insights (requirement 3):
1. How many total films have they watched in their top category?
2. How many more films has the customer watched compared to the average DVD 
Rental Co customer?
3. How does the customer rank in terms of the top X% compared to all other 
customers in this film category?
**For the second ranking category (requirement 4):**
1. How many total films has the customer watched in this category?
2. What proportion of each customer’s total films watched does this count make?
Note the specific rounding of the percentages with 0 decimal places
####################### Requirement #5
**Favorite Actor Recommendations**
Along with the top 2 categories, marketing has also requested top actor film 
recommendations where up to 3 more films are included in the recommendations list as 
well as the count of films by the top actor.
We have been given guidance by marketing to choose the actors in alphabetical order 
should there be any ties - i.e. if the customer has seen 5 Brad Pitt films vs 5 George 
Clooney films - Brad Pitt will be chosen instead of George Clooney.
The same logical business rules apply - in addition any films that have already been 
recommended in the top 2 categories must not be included as actor recommendations.
If the customer doesn’t have at least 1 film recommendation - they also need to be 
flagged with a separate actor exclusion flag.
######## Understanding the data
####################### **Table #1 - rental**
This dataset consists of rental data points at a customer level. There is a unique 
sequential rental_id for each record in the table which corresponds to an 
individual customer_id purchasing or renting a specific item with a inventory_id. There 
is also information about the rental_date and return_date as well as which staff 
member served the customers.
The last_update field is an internal database timestamp for when the datapoints were 
inserted into the table.
In the ERD - we can see that there is a linkage between this rental table with 
the inventory table via the inventory_id field.
Click here to inspect raw data
####################### **Table #2 - inventory**
This inventory dataset consists of the relationship between specific items available for 
rental at each store - note that there may be multiple inventory items for a specific film at 
a unique store.
In other words - say a specific film like “Harry Potter & The Chamber of Secrets” might 
have 4 copies at store #1 and an additional 4 copies in store #2 - each record in this 
dataset will have a separate inventory_id whilst the film_id will all be the same and 
the store_id changes according to the store number.
This inventory table is linked to the previous rental table via the inventory_id which 
is an integer datatype.
The next table we will investigate is how we will link these inventory items to a specific 
film via the film table and the film_id column.
####################### **Table #3 - film**
The third table in our ERD is the film table which helps us identify the title of films 
rented by DVD Rental Co customers. The film title, description as well as special 
features and other fields are available for further analysis.
One specific column which might be of interest is the rental_rate column which 
represents the cost of each rental - something which could be useful if we wanted to 
look at the sales performance of DVD Rental Co.
Note that there are a few complex data types in the last few columns of this table - you 
can practically ignore them for the time being but these more involved data structures do 
occur in real life too so it’s just something you need to be aware of.
We will use the film_id column to help us join onto table #4 film_actor to help us 
identify which actors appeared in each film.
####################### **Table #4 - film_category**
The 4th table in the ERD is film_category which shows the relationship 
between film_id and category_id.
Multiple films will appear in each relevant category_id which will map one-to-one with 
our next table in our ERD, the category table.
####################### **Table #5 - category**
The 5th table in the ERD is the category table which simply contains a one to one 
mapping between category_id and the name of each category.
####################### **Table #6 - film_actor**
The 6th table in our ERD is film_actor which shows actors who appeared in each film 
based off related actor_id and film_id values.
An actor can appear in multiple films and a film can feature multiple actors. This 
relationship between values is what we usually call a “many-to-many” relationship - this 
is really important to note especially when we are dealing with joins.
####################### **Table #7 - actor**
The actor table simply shows the first and last name for each actor based off their 
unique actor_id - we can trace which films a specific actor appears in by joining this 
table onto the previously discussed film_actor table on the actor_id column.

###################### Business Questions
1. Identify top 2 categories for each customer based off their past rental history
2. For each customer recommend up to 3 popular unwatched films for each 
category
3. Generate 1st category insights that includes:
o How many total films have they watched in their top category?
o How many more films has the customer watched compared to the average 
DVD Rental Co customer?
o How does the customer rank in terms of the top X% compared to all other 
customers in this film category?
4. Generate 2nd insights that includes:
o How many total films has the customer watched in this category?
o What proportion of each customer’s total films watched does this count 
make?
5. Identify each customer’s favorite actor and film count, then recommend up to 3 
more unwatched films starring the same actor.
6. What is the second ranking category by total rental count?
7. What was the top category watched by total rental count?

####################### Solutions
1. Identify top 2 categories for each customer based off their past rental history
![Screenshot (220)](https://user-images.githubusercontent.com/72690313/132277217-8b7f1b36-4877-40ee-a34d-58086aaaa2e0.png)
..so on

Creating view for further use the query
![Screenshot (222)](https://user-images.githubusercontent.com/72690313/132277438-efe5334f-ecc7-488c-957e-1e03e3a1460d.png)
2.  For each customer recommend up to 3 popular unwatched films for each 
category
**The Query was too big so I have pasted here the query**
with my_data7 as(
with my_data6 as(with my_data5 as(with my_data4 as (with my_data3 as(with my_data2 as(with my_data1 as(
with my_data as(select dense_rank() over 
(partition by customer_id order by rental_count desc) as 
drank,customer_id,category_id,rental_count from
(select customer_id,category_id,
count(rental_id) as rental_count from
(select customer_id,rental_id,rental.inventory_id,
inventory.film_id,category_id from rental inner join inventory 
on(inventory.inventory_id=rental.inventory_id)
inner join film_category on(film_category.film_id=inventory.film_id)
order by 1) as temptable
group by 1,2
order by 1,3 desc) as temptable)
select * from my_data
where drank in(1,2))
select customer_id,name as category_name,rental_count from my_data1, category where my_data1.category_id=category.category_id)
select customer_id,category_id,rental_count from my_data2 inner join category on(category.name=my_data2.category_name)
order by 1)
select my_data3.customer_id as customer_id,my_data3.category_id as category_id,film_id from my_data3 
inner join ((select customer_id,film_id,category_id from(select customer_id,rental_id,rental.inventory_id,inventory.film_id as 
film_id,film_category.category_id  as category_id from 
rental inner join inventory on(inventory.inventory_id=rental.inventory_id) inner join film_category
on(film_category.film_id=inventory.film_id)
order by 1,5) as temptable))as temporary where(temporary.category_id=my_data3.category_id) and(temporary.customer_id=my_data3.customer_id)
order by customer_id,category_id)
select distinct customer_id,my_data4.category_id,my_data4.film_id from my_data4 inner join (with my_d2 as(with my_d as(select customer_id,rental.inventory_id as inventory_id,film_id from 
rental inner join inventory on(rental.inventory_id=inventory.inventory_id)
order by 3)
select film_id,count(film_id) as films_counts from my_d
group by 1
order by 2 desc)
select my_d2.film_id,films_counts,category_id from my_d2 inner join film_category on(film_category.film_id=my_d2.film_id)) as temporary where (temporary.film_id<>my_data4.film_id)
 and (temporary.category_id=my_data4.category_id)
order by 1,2)
select customer_id,category_id as category_id,my_data5.film_id as film_id,films_counts from my_data5 inner join ((with my_d as(select customer_id,rental.inventory_id as inventory_id,film_id from
 rental inner join inventory on(rental.inventory_id=inventory.inventory_id)
order by 3)
select film_id,count(film_id) as films_counts from my_d
group by 1
order by 2 desc)) as temporary1 on(temporary1.film_id=my_data5.film_id)
order by customer_id,category_id,films_counts desc)
select dense_rank() over (partition by customer_id,category_id  order by films_counts desc) as drank ,customer_id,category_id,film_id,films_counts from my_data6)
select customer_id,category_id,film_id from my_data7
where drank in(1,2,3);
![Screenshot (225)](https://user-images.githubusercontent.com/72690313/132277638-dddebbdc-d2ae-41a5-bd19-eab18ad5ca4c.png)
o How many total films have they watched in their top category?
![Screenshot (227)](https://user-images.githubusercontent.com/72690313/132277769-7b35d50f-0937-427c-822d-9888ae7524b1.png)
.. so on
creating view for further use of the query
![Screenshot (229)](https://user-images.githubusercontent.com/72690313/132277903-e6ed254c-68aa-41bd-a9a0-2195184723d9.png)
o How many more films has the customer watched compared to the average 
DVD Rental Co customer?
![Screenshot (231)](https://user-images.githubusercontent.com/72690313/132278045-813146ac-ddf2-4991-ac95-424e58229835.png)
.. so on
o How does the customer rank in terms of the top X% compared to all other 
customers in this film category?
![Screenshot (233)](https://user-images.githubusercontent.com/72690313/132278154-46d18d6f-ea62-42b8-b17e-4b7056691452.png)
o How many total films has the customer watched in this category?
![Screenshot (235)](https://user-images.githubusercontent.com/72690313/132278330-070f402c-0589-4d22-96e0-9d63ec97712a.png)
.. so on

creating view for further use the query
![Screenshot (237)](https://user-images.githubusercontent.com/72690313/132278460-25c17594-43a0-481f-9c5d-9a12d58d8202.png)
o What proportion of each customer’s total films watched does this count 
make?
![Screenshot (239)](https://user-images.githubusercontent.com/72690313/132278590-e6c7c515-b20d-45f0-91e2-c86f379e1372.png)
.. so on
5. Identify each customer’s favorite actor and film count, then recommend up to 3 
more unwatched films starring the same actor.
**Favourite Actors of Each Customer and count of films of each actors**
![Screenshot (242)](https://user-images.githubusercontent.com/72690313/132278776-e6bae2e7-9055-41da-8433-e16738021c02.png)

creating view for further use
![Screenshot (244)](https://user-images.githubusercontent.com/72690313/132278956-cefdb7f1-0c46-4a6d-ae7b-81b95707b341.png)

**recommend up to 3 
more unwatched films starring the same actor**(Part of Ques-5)
**Query is too big so I have pasted the query here**
with my_d3 as(with my_d2 as(with my_d1 as(with my_d as(select cust_fav_actor.customer_id as customer_id,film_id,cust_fav_actor.actor_id as actor_id from cust_fav_actor 
inner join (select customer_id,film_id,actor_id from(select customer_id,rental.inventory_id as inventory_id,inventory.film_id as film_id,actor_id from rental
inner join inventory on(inventory.inventory_id=rental.inventory_id) inner join film_actor on(film_actor.film_id=inventory.film_id)
order by customer_id)as temptable
order by 1,3)as temp where (temp.customer_id=cust_fav_actor.customer_id) and (temp.actor_id=cust_fav_actor.actor_id)
order by 1)
select distinct my_d.customer_id as customer_id,my_d.actor_id as actor_id,film_actor.film_id as film_id from my_d inner join film_actor where (film_actor.actor_id=my_d.actor_id) and (film_actor.film_id<>my_d.film_id)
order by 1,3)
select customer_id,my_d1.actor_id as actor_id,my_d1.film_id film_id,rental_counts from my_d1 inner join (select film_id,count(rental_id) as rental_counts from(select rental_id,rental.inventory_id as inventory_id,inventory.film_id 
as 
film_id from rental inner join inventory on(inventory.inventory_id=rental.inventory_id)) as temo
group by 1
order by 1)as temp where temp.film_id=my_d1.film_id
order by 1,3 desc)
select customer_id,dense_rank() over (partition by customer_id,actor_id order by rental_counts desc) as drank,actor_id,film_id,rental_counts from my_d2
order by 1,5 desc)
select customer_id,actor_id,film_id from my_d3
where drank in(1,2,3)
order by 1;
6.What was the top category watched by total rental count?
![Screenshot (248)](https://user-images.githubusercontent.com/72690313/132279896-6f7c83f0-c999-4281-8c0a-2d5d628c8ceb.png)
7. What is the second ranking category by total rental count?
![Screenshot (250)](https://user-images.githubusercontent.com/72690313/132279986-4dce88ed-b4f6-4b2a-b05e-56c83b426b47.png)
Thank You!
