# FacebookNewsFeed
A simplified database to store and query FB newsfeed

## Desgin considerations
FB is a very large scale and distributed system. To handle that level of scale we would have to rely on NoSQL databases for storing and managing user data. But for the purposes of learning, I will be using a MySQL database to create a simplified version designed for smaller scale. The concept can be used to build larger noSQL systems.

- Very High read - NewsFeed generation everytime someone opens their app
- High write - People post pretty often too, but a lot less compared to reads.
- The system should be highly available and durable
- And have minimum latency

## Database design

To store the user information, I will be using following tables
1. __User__ - id, name, email, gender, location, created_at, is_active, phone
2. __Friends__ - id1, id2
3. __NewsFeedItem__ - item_id, description, user_id, media_url, created_at, location, likes
Since we will have a lot of newsfeed items over time, a SQL based DB is not scalable, Ideally we will use a noSQL store. A key value pair store where key is the userId that maps to all their posts, and another store that stores all the items. This will help us store and retrieve items 

## Questions to be solved

## Find names of friends for a given User
SELECT name from User WHERE User_id IN (SELECT User_Id_2 FROM Friends WHERE User_id_1 = 1)

## Count my male and female Friends
SELECT Gender, count(Gender) FROM User WHERE User_id IN (SELECT User_Id_2 FROM Friends WHERE User_id_1 = 1) GROUP By Gender

## Find my friend who created the account the longest time ago
SELECT name from User WHERE User_id IN (SELECT User_Id_2 FROM Friends WHERE User_id_1 = 1) ORDER BY User.Created_At DESC  LIMIT 1


## Find friends who are in the same city as me
SELECT name FROM User WHERE User_id IN (SELECT User_Id_2 FROM Friends WHERE User_id_1 = 1) AND Location = "San Francisco"

## Create my newsfeed 
SELECT  User.name, NewsFeedItem.* FROM User 
INNER JOIN NewsFeedItem on User.User_id = NewsFeedItem.User_id 
WHERE NewsFeedItem.User_id IN (SELECT User_Id_2 FROM Friends WHERE User_id_1 = 1)
ORDER BY NewsFeedItem.Created_At DESC

