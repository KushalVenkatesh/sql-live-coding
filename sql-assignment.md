## Problem Statement

Your task is to prepare a list of `cities` with the `date of last reservation` made in the city and a main photo (`photos[0]`) of the most popular (by number of bookings) hotel in this city.

Sort results in ascending order by `city`. If many hotels have the same amount of bookings, sort them by `ID` (ascending order). Remember that the query will also be run on different datasets.

### Database Schema

```
create table city
(
    id int primary key,
    name varchar(50) not null
);

create table hotel
(
    id int primary key,
    city_id int not null references city,
    name varchar(50) not null,
    day_price numeric(8, 2) not null,
    photos jsonb default '[]'
);

create table booking
(
    id int primary key,
    hotel_id int not null references hotel,
    booking_date date not null,
    start_date date not null,
    end_date date not null
);
```

### Example Output

| city      | last_booking_date | hotel_id | hotel_photo |
|-----------|-------------------|----------|-------------|
| Barcelona | 2019-04-06        | 3        | 3-1.jpg     |
| Roma      | 2019-04-06        | 6        | 6-1.jpg     |

### Sample Data

**table: city**

| id | name      |
|----|-----------|
| 2  | Barcelona |
| 3  | Roma      |


### table: hotel

| id | city_id | name           | day_price | photos                        |
|----|---------|----------------|-----------|-------------------------------|
| 3  | 2       | The New View   | 67.00     | ["3-1.jpg", "3-2.jpg"]        |
| 4  | 2       | The Upper House| 99.00     | ["4-1.jpg", "4-2.jpg"]        |
| 5  | 2       | Ace Hotel      | 90.00     | ["5-1.jpg", "5-2.jpg"]        |
| 6  | 3       | Hotel Diva     | 111.00    | ["6-1.jpg", "6-2.jpg"]        |
| 7  | 3       | Hotel Triton   | 105.00    | ["7-1.jpg", "7-2.jpg"]        |

### table: booking


| id | hotel_id | booking_date | start_date | end_date   |
|----|----------|--------------|------------|------------|
| 6  | 3        | 2019-04-02   | 2019-05-07 | 2019-05-12 |
| 7  | 3        | 2019-04-03   | 2019-06-03 | 2019-06-07 |
| 8  | 3        | 2019-04-03   | 2019-06-04 | 2019-06-11 |
| 9  | 3        | 2019-04-04   | 2019-06-12 | 2019-06-18 |
| 10 | 4        | 2019-04-04   | 2019-05-07 | 2019-05-12 |
| 11 | 5        | 2019-04-05   | 2019-05-01 | 2019-05-05 |
| 12 | 5        | 2019-04-06   | 2019-05-03 | 2019-05-11 |
| 13 | 6        | 2019-04-06   | 2019-05-14 | 2019-05-18 |
| 14 | 6        | 2019-04-06   | 2019-05-07 | 2019-05-12 |


