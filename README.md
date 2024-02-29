# SQL CRUD

## Part 1: Restaurant finder
### Table structure
I create a database called restaurants.db.
```
sqlite3 restaurants.db
```
I created two tables, one for restaurants and one for reviews. 
The code for "restaurants.csv" is shown below:
```sql
CREATE TABLE restaurants (
    restaurant_id INTEGER PRIMARY KEY,
    name TEXT,
    category TEXT,
    price_tier TEXT,
    neighborhood TEXT,
    opening_time TEXT,
    closing_time TEXT,
    average_rating INTEGER,
    good_for_kids BOOLEAN
);
```
Another table is for reviews, the code is shown below:
```sql
CREATE TABLE reviews (
    review_id INTEGER PRIMARY KEY,
    restaurant_id INTEGER,
    review_text TEXT,
    rating INTEGER,
    FOREIGN KEY (restaurant_id) REFERENCES restaurants(restaurant_id)
);
```
### Practice data
Then I created a mock data for restaurants by using [mockaroo.com](https://mockaroo.com) and download it as a CSV file [restaurants.csv](). 
And I import my practice data restaurants.csv into it, here is the code.
```sql
.mode csv
.import /Users/jingyan/Desktop/Database/4-sql-crud-jm8920/data/restaurants.csv restaurants
```
### Queries
Write a single SQL query to perform each of the following tasks:
1. Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```sql
SELECT * FROM restaurants WHERE price_tier = 'cheap' AND neighborhood = 'Harlem';
```
2. Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.
```sql
SELECT * FROM restaurants WHERE category = 'Indian' AND average_rating >= 3 ORDER BY average_rating DESC;
```
3. Find all restaurants that are open now
```sql
SELECT * FROM restaurants WHERE opening_time <= strftime('%H:%M', 'now') AND closing_time >= strftime('%H:%M', 'now');
```
4. Leave a review for a restaurant (pick any restaurant as an example; note that leaving a review has no automatic effect on the average rating of the restaurant).
```sql
INSERT INTO reviews (restaurant_id, review_text, rating) VALUES ((SELECT restaurant_id FROM restaurants WHERE name = 'Burger Haven'), 'Delicious food!', 4);
```
5. Delete all restaurants that are not good for kids.
```sql
DELETE FROM restaurants WHERE good_for_kids = 0;
```
6. Find the number of restaurants in each NYC neighborhood.
```sql
SELECT neighborhood, COUNT(*) AS 'Number of Restaurants' FROM restaurants GROUP BY neighborhood;
```

## Part 2: Social media app