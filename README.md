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
.import /Users/jingyan/Desktop/4-sql-crud-jm8920/data/restaurants.csv restaurants --skip 1
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
### Creating 2 Tables for users and posts
I create a database called restaurants.db.
```
sqlite3 socialmedia.db
```
```sql
CREATE TABLE users(
    id INTEGER PRIMARY KEY,
    email TEXT,
    username TEXT,
    password TEXT,
);
```
```sql
CREATE TABLE posts (
    post_id INTEGER PRIMARY KEY,
    post_type TEXT NOT NULL,
    sender TEXT NOT NULL,
    recipient TEXT NOT NULL,
    content TEXT NOT NULL,
    post_time TEXT NOT NULL,
    is_visible BOOLEAN NOT NULL,
    FOREIGN KEY (sender) REFERENCES users(id),
    FOREIGN KEY (recipient) REFERENCES users(id)
);
```

### Importing csv data
Then I created mock data for users and posts by using [mockaroo.com](https://mockaroo.com) and download them CSV files. 
And I import my practice data into it, here is the code:
```sql
.mode csv
.import /Users/jingyan/Desktop/4-sql-crud-jm8920/data/users.csv users --skip 1
.import /Users/jingyan/Desktop/4-sql-crud-jm8920/data/posts.csv posts --skip 1
```
### Queries
Write a single SQL query to perform each of the followin tasks:
1. Register a new User.
```sql
insert into users (email, username, password)
    values ('newuser@email.com', 'newnewnew', '12345678');
```
2. Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```sql
insert into posts (post_type, sender, recipient, content, post_time, is_visible )
    values ('message', 'mjarvisf', 'lwittletonf', 'Nice to meet you!!!','2023/3/19 06:48', TRUE);
```

3. Create a new Story by a particular User (pick any User for example).
```sql
insert into posts (posy_type, sender, content, post_time, is_visible)
    values ('story', (select id from users where email='rsmead8@wsj.com','2023/3/19 06:48', TRUE), 'Enjoying a lovely afternoon!')
```

4. Show the 10 most recent visible Messages and Stories, in order of recency.
```sql
SELECT * FROM posts
WHERE is_visible = TRUE
ORDER BY datetime(post_time) DESC
LIMIT 10;
```

5. Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```sql
SELECT * FROM posts
WHERE sender = 'iprahm9' AND recipient = 'jboeter9'
      AND is_visible = TRUE AND post_type = 'message'
ORDER BY datetime(post_time) DESC
LIMIT 10;
```

6. Make all Stories that are more than 24 hours old invisible.
```sql
UPDATE posts
SET is_visible = 0
WHERE post_type = 'Story' AND
ROUND((JULIANDAY('now') - JULIANDAY(post_time)) * 24) > 24;
```

7. Show all invisible Messages and Stories, in order of recency.
```sql
SELECT post_id, post_type, sender, recipient, content, post_time, is_visible, 
ROUND((JULIANDAY('now') - JULIANDAY(post_time)) * 24) AS hours_ago from posts
WHERE is_visible = 0
ORDER BY post_time DESC;
```
8. Show the number of posts by each User.
```sql
SELECT sender, COUNT(*) as "num_posts"
FROM posts
GROUP BY sender;
```

9. Show the post text and email address of all posts and the User who made them within the last 24 hours.
```sql
SELECT p.content AS post_text, u.email AS user_email, u.username AS user_name
FROM posts AS p
JOIN users AS u ON p.sender = u.username
WHERE p.is_visible = 1 AND p.post_type = 'Story' AND
ROUND((JULIANDAY('now') - JULIANDAY(p.post_time)) * 24) >= 24;
```

10. Show the email addresses of all Users who have not posted anything yet.
```sql
SELECT u.email
FROM users AS u
LEFT JOIN posts AS p ON u.id = p.sender
WHERE p.sender IS NULL;
```