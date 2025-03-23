---
title: "Cockroach schema design"
date: 2025-03-22 00:00:00 +0800
categories: [Cockroach]
tags: [cockroach, schema]
---

## Summary Tables

 A summary table typically contains aggregated information that is expensive to com
pute on the fly. For instance, in the MovR application, we might have a dashboard
 that shows revenue trends by city based on the following query:
 ```sql
 SELECT CAST(r.start_time AS date) AS ride_date,u.city,SUM(r.revenue)
 FROM rides r
 JOIN users u ON (u.id=r.rider_id)
 GROUP BY 1,2;
 ```
 Because revenue for previous days rarely changes, it’s wasteful to continually reissue
 this expensive query every time the dashboard requests it. Instead, we create a
 summary table from this data and reload the data at regular intervals (perhaps once
 an hour).
 We can create such a summary table manually, but materialized views exist for this
 very purpose. We’d create a materialized view as follows:
```sql
 CREATE MATERIALIZED VIEW ride_revenue_by_date_city AS
 SELECT cast(r.start_time AS date) AS ride_date,u.city,SUM(r.revenue)
 FROM rides r
 JOIN users u ON (u.id=r.rider_id)
 GROUP BY 1,2;
 ```
 The resulting table is far smaller than the source tables, and we can manually refresh
 it whenever we like. One of the advantages of a materialized view is that we can also
 update it whenever we like with the REFRESH command:
 ```sql
 REFRESH MATERIALIZED VIEW ride_revenue_by_date_city
 ```

##  Vertical Partitioning

Vertical Partitioning
 Vertical partitioning involves breaking up a table into multiple tables, each of which
 contains a different set of rows. This is typically done to reduce the amount of work
 that needs to be completed when updating a row and can also reduce the conflicts
 that occur when two columns are subject to high concurrent update activity.
 For instance, consider an Internet of Things (IoT) application in which a city’s
 current temperature and air pressure are updated multiple times a second by weather
 sensor devices across the city:
 ```sql
 CREATE TABLE cityWeather  (
 city_id uuid NOT NULL PRIMARY KEY DEFAULT gen_random_uuid(),
 city_name varchar NOT NULL,
 currentTemp float NOT NULL,
 currentAirPressure float NOT NULL);
 ```
 The temperature values and air pressure readings come from different systems, and
 we’re concerned that they will cause transaction conflicts when they attempt to
change the same row simultaneously. We could partition the table into two tables to
 avoid this conflict:
 ```sql
 CREATE TABLE cityTemp  (
 city_id uuid NOT NULL PRIMARY KEY DEFAULT gen_random_uuid(),
 city_name varchar NOT NULL,
 currentTemp float NOT NULL);
 CREATE TABLE cityPressure (
 city_id uuid NOT NULL PRIMARY KEY DEFAULT gen_random_uuid(),
 city_name varchar NOT NULL,
 currentTemp float NOT NULL,
 currentAirPressure float NOT NULL);
 ```
 However, CockroachDB column families provide a solution that does not require
 us to modify our data model. Column families allow
 groups of columns to be stored separately in the storage layer. We simply add each
 measurement to its own family:
 ```sql
 CREATE TABLE cityWeather  (
 city_id uuid NOT NULL PRIMARY KEY DEFAULT gen_random_uuid(),
 city_name varchar NOT NULL,
 currentTemp float NOT NULL,
 currentAirPressure float NOT NULL,
 FAMILY f1 (city_id,city_name),
 FAMILY f2 (currentTemp),
 FAMILY f3 (currentAirPressure)
 );
 ```

 ## JSON Document Models

 JSON objects are self-describing and can contain nested JSON objects and arrays. It’s
 commonplace in document databases to embed child data within parent objects to
 avoid the need to perform joins. For instance, a house rental database might include
 all of the attributes of a property within a nested array


 ```json
 {
  "_id": "10006546",
  "listing_url": "https://www.airbnb.com/rooms/10006546",
  "name": "Ribeira Charming Duplex",
  "summary": "Fantastic duplex apartment with three bedrooms, located in the historic area of Porto, Ribeira (Cube)...",
  "amenities": [
    "TV",
    "Cable TV",
    "Wifi",
    "Kitchen",
    "Paid parking off premises",
    "Waterfront"
  ],
  "images": {
    "thumbnail_url": "",
    "medium_url": "",
    "picture_url": "https://a0.muscache.com/im/p/9b.jpg?aki_policy=large",
    "xl_picture_url": ""
  },
  "host": {
    "host_id": "51399391",
    "host_url": "https://www.airbnb.com/users/show/51399391"
  }
}
 ```

 ##  Indexing JSON Attributes

 You can create inverted indexes on JSONB col
umns. These inverted indexes allow you to search for attribute value matches within
 the JSON object.

 However, inverted indexes index every attribute in the JSON object and so can have
 many more entries in the index than rows in the table—with possible impacts on
 storage and index maintenance overhead. An alternative is to create computed col
umns on the JSONB attributes and create an index on those attributes. In this case,
 you’ll need to know which attributes might need an index lookup, but your indexes
 will remain relatively compact.

 The
 basic customer details look like this:

```json
{ "Address" : "1913 Hanoi Way",
 "City" : "Sasebo",
 "Country" : "Japan",
 "District" : "Nagasaki",
 "FirstName" : "Mary",
 "LastName" : "Smith",
 "Phone" : "886780309",
 "dob" :  "1982-02-20T13:00:00Z",
 "likes": ["Dinosaurs","Dogs","People"] }
```

 We know that we want to search on LastName, FirstName, and we also know we want
 to have a foreign key out to an existing cities table. Our CREATE TABLE might look
 like this:

 ```sql
 CREATE TABLE people (
 personId UUID PRIMARY KEY NOT NULL default gen_random_uuid(),
 cityId UUID ,
 personData JSONB,
 FirstName STRING AS (personData->>'FirstName') VIRTUAL,
 LastName STRING AS (personData->>'LastName') VIRTUAL,
 FOREIGN KEY (cityId) REFERENCES cities(cityid),
 INDEX  (LastName,Firstname)
 );
 ```

 ## Composite Indexes
 A composite index is simply an index created from more than one column. The
 advantage of a composite key is that it is often more selective than a single key index.
 The combination of columns will point to a smaller number of rows than indexes
 composed of the individual columns.

 For instance, if we know that we frequently perform searches on firstname and
 lastname, then it makes sense to create an index on both of those columns:

 ```sql
 CREATE INDEX flname_idx ON person (lastname,firstname);
 ```

  If a composite index could be used only when all of its keys appeared in the WHERE
 clause, then you would have to create a lot of composite indexes—as many as you had distinct combinations of columns in the WHERE clause. Luckily, a composite index can
 be used effectively, provided any of the initial or “leading” columns are used. Leading
 columns are those that are specified earliest in the index definition

 So, for instance, the index on (lastname, firstname) that we just created can
 optimize this query:
 ```sql
 SELECT * FROM person WHERE lastname='Wood';
 ```
 But not this query:
 ```sql
 SELECT * FROM person WHERE firstname='John'; 
 ```

## Expression Indexes
 Expression indexes allow us to create an index on an expression rather than a column
 name. It’s similar in effect to creating a COMPUTED column and then creating an index
 on that column.
 For instance, we could create an index on a lowercased version of the user’s name:
 ```sql
 CREATE INDEX lower_users_ix ON users (LOWER(name))
 ```
 Such an index would be able to optimize queries that ignored case by using the LOWER
 function in the query:
 ```sql
 SELECT *
 FROM users
 WHERE name=LOWER('Guy');
 ```