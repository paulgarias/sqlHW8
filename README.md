## Homework Assignment

* 1a. Display the first and last names of all actors from the table `actor`. 

use sakila;
select first_name, last_name from actor limit 0, 1000;


* 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`. 

use sakila;
select concat(first_name,' ', last_name) as 'Actor Name' from actor;

* 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?

use sakila;
select * from actor where first_name like 'JOE';
  	
* 2b. Find all actors whose last name contain the letters `GEN`:
  	
use sakila;
select * from actor where last_name like '%GEN%';

* 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order:

use sakila;
select first_name, last_name from actor 
    where last_name like '%LI%'
    order by last_name, first_name;

* 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:

use sakila;
select country_id, country
	from country
    where country in ('Afghanistan','Bangladesh', 'China');

* 3a. Add a `middle_name` column to the table `actor`. Position it between `first_name` and `last_name`. Hint: you will need to specify the data type.
  
use sakila;
CREATE TABLE actor_test LIKE actor; 
INSERT actor_test SELECT * FROM actor;
ALTER TABLE actor_test
ADD COLUMN middle_name VARCHAR(45) AFTER first_name;
	
* 3b. You realize that some of these actors have tremendously long last names. Change the data type of the `middle_name` column to `blobs`.

use sakila;
alter table actor_test modify column middle_name blob;

* 3c. Now delete the `middle_name` column.

use sakila;
alter table actor_test drop column middle_name;

* 4a. List the last names of actors, as well as how many actors have that last name.
  	
use sakila;
select last_name,count(last_name) as num_last_name from actor group by last_name;

* 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

use sakila;
select last_name, count(last_name)  
	from actor 
	group by last_name
    having count(last_name)>=2;

  	
* 4c. Oh, no! The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

use sakila;
update actor set first_name='HARPO' where first_name='GROUCHO' and last_name='WILLIAMS';
  	
* 4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`. Otherwise, change the first name to `MUCHO GROUCHO`, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO `MUCHO GROUCHO`, HOWEVER! (Hint: update the record using a unique identifier.)

use sakila;
update actor set first_name='GROUCHO' where first_name='HARPO' and last_name='WILLIAMS';

* 5a. You cannot locate the schema of the `address` table. Which query would you use to re-create it? 

  * Hint: [https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html](https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html)

use sakila;
SHOW CREATE TABLE address;

* 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:

use sakila;
SELECT * FROM staff LEFT JOIN (address)
                 ON (staff.address_id = address.address_id)

* 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`. 

use sakila;
SELECT username,sum(amount) FROM staff LEFT JOIN (payment)
                 ON (staff.staff_id = payment.staff_id) 
                 group by username;
  	
* 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.

use sakila;
select title,count(film_actor.film_id) from film inner join ( film_actor)
	on (film.film_id = film_actor.film_id)
    group by title;
  	
* 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?

use sakila;
select title from film where title ='Hunchback Impossible'

Return 1

* 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:

  ```
  	![Total amount paid](Images/total_payment.png)
  ```
use sakila;
## Tables are payment and customer
## Need to join on customer_id
## Need to display first_name, last_name and sum of all payments (amount)
select customer.first_name, customer.last_name, sum(amount) 
from customer inner join (payment) 
on (customer.customer_id = payment.customer_id)
group by customer.last_name asc, customer.first_name asc



* 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English. 

use sakila;
select title from film 
where title in (
select title where title like 'K%'  or title like 'Q%');

* 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.

use sakila;
#film_id is 17
#need film_actor and actor
select actor.first_name, actor.last_name from actor left join (film_actor)
on (actor.actor_id = film_actor.actor_id)
where film_actor.film_id=17;
   
* 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

use sakila;
# Need names and email from Canadian customers
# Customer table has (customer_id, store_id, 
# first_name, last_name, email, address_id, active, create_date,
# last_update)
# Customer list has (ID which corresponds to customer_id)

select customer.first_name, customer.last_name, customer.email
from customer left join (customer_list) 
on (customer.customer_id = customer_list.ID)
where customer_list.country = 'canada'

* 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.

use sakila;

create view A  as
select fc.film_id, fc.category_id, f.title 
from film_category fc inner join (film f) 
on (f.film_id = fc.film_id)  ;

select A.film_id, A.category_id, A.title, ct.name as name from A inner join(category ct) 
on (ct.category_id = A.category_id )
and name = "Family"


* 7e. Display the most frequently rented movies in descending order.

use sakila;

show tables;
select * from film; --                                film_id, title
select * from rental; --       inventory_id,             rental_id, rental_date, customer_id, return_date, staff_id
select * from inventory; --  inventory_id, film_id, store_id

select inventory_id, count(customer_id) from rental group by inventory_id;

create view A as 
select inv.film_id, inv.inventory_id, r.rental_id from inventory inv inner join(rental r)
on (inv.inventory_id = r.inventory_id) ;

create view B as 
select film_id, count(inventory_id) as Number_of_rentals from A group by film_id order by Number_of_rentals desc;

select f.title, B.Number_of_rentals   from B inner join (film f) 
on (B.film_id = f.film_id) order by B.Number_of_rentals desc ;

  	
* 7f. Write a query to display how much business, in dollars, each store brought in.

use sakila;
select * from sales_by_store; 

* 7g. Write a query to display for each store its store ID, city, and country.

use sakila;
select SID, city, country from staff_list; 

* 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)
* 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.
* 8b. How would you display the view that you created in 8a?
* 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.

use sakila;

show tables;
select * from category; --                                 category_id, name
select * from  film_category; --            film_id, category_id
select * from  inventory;--                    film_id,                               inventory_id, store_id
select * from  rental;--                                                                    inventory_id,                customer_id,               rental_id
select * from  payment;-- payment_id,                                                                               customer_id, staff_id, rental_id, amount

-- top five genres in gross revenue in descending order
-- need sum(amount) : payment
-- need category: 


create view A as
select cat.name, fc.film_id from category cat inner join(film_category fc)
on (fc.category_id = cat.category_id); -- this has name and film_id

create view B as 
select A.name, inv.inventory_id, inv.store_id from A inner join(inventory inv)
on (inv.film_id= A.film_id);

create view C as 
select B.name, B.inventory_id, B.store_id, r.customer_id, r.rental_id from B inner join(rental r)
on (r.inventory_id = B.inventory_id);

create view D as
select C.name, sum(p.amount) as sum_genre from C inner join(payment p)
on (p.rental_id = C.rental_id)
group by C.name;


select * from D order by sum_genre desc limit 5; 

drop view A;
drop view B;
drop view C;
drop view D;


  	

### Appendix: List of Tables in the Sakila DB

* A schema is also available as `sakila_schema.svg`. Open it with a browser to view.

```sql
	'actor'
	'actor_info'
	'address'
	'category'
	'city'
	'country'
	'customer'
	'customer_list'
	'film'
	'film_actor'
	'film_category'
	'film_list'
	'film_text'
	'inventory'
	'language'
	'nicer_but_slower_film_list'
	'payment'
	'rental'
	'sales_by_film_category'
	'sales_by_store'
	'staff'
	'staff_list'
	'store'
```
