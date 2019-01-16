## So you've been working on SQL tutorial this morning this lecture is to enlighten you about more with SQL

> What does SQL stand for? Structured Query Language
> Bit of background: was made in the 1970s - prior to that if you were working with data it would have been obscure mathematical style - so SQL was designed to be more accessible
> Why do we use it?

- not to _store_ data - thats what the database is for
- query based: querying, inserting and updating our DB

### What kind of database does SQL work with? --- Relational!

- Normally I'd show you theory and then practical example but now we'll build things up with theory as we go

* SQL & relational is built in tables - to have _normalised_ data : basically storing data in an organised way so we can handle relationships (which we'll see what that means in a bit)

- SQL comes in all kinds of different flavours or dialects which one will we be using today? what did you install - POSTGRESQL

## SYNTAX for this psql

- we're going to use the psql command which will open up the `psql` terminal and see what we can do
- you will end up with something like this - the CLI (client) to interact
- `\` is going to be our lifesaver if you enter \? it will show you all the commands you can use
  > if we're in this terminal what would one of the first things we want to know
  > wheres our data / what database we have so we're going to use `\l` for that which will list the dbs by name
  > besides listing them what else might we want to ?
  > see whats in it? how would we do that? SELECT \* ?
  > that would be great to query the db, but before we run those queries we need to know what to query
  > so we need to use `\c dbname` to connect - which will show us we've connected as our psql user
  > what else might we want to do now? before we start querying what would be helpful to know? how do we know what to query from?
  > `\dt` will show us the tables we have in this db - tables are also called `relations` > `\q` will let us exit out of the psql CLI

draw diagram of cylinder db which is then comprised of tables with heading columns

- So first thing we're going to do before we start querying is have data to query so we're going to make a database and some of these tables from scratch
- lets go back to `psql` cli and how could we make a database from scratch? well we can look online... but the syntax for this is - funilly enough -`CREATE DATABASE anat_imdb;` so create followed by the name and... remember the semi colon
- how can i check that that has created it ? `\l`
- as well as creating we might want to delete - often we'll want to build it up from scratch
- syntax for that? `DROP DATABASE anat_imdb;`
- what will happen if I try to `\c` connect to that database now? error - db does not exist

- this is alot to be doing on the terminal so we're going to make a file which will have this inside it and tell psql to run that file
- how will we run that file, well `man psql` (manual) will give us a list of things we can do... `-f filename` will let us run that file using `psql`
- so now we'll make a .sql file

## .sql

lets do some psuedo code - first thing to know is that sql comments aren't // but --

-- <-- this is an sql comment
-- drop database if it exists (i'll want to build it from scratch so i dont keep infinitely adding if i run it again)
-- create the database

what was the command for the dropping

```sql
DROP DATABASE IF EXISTS anat_imdb;
CREATE DATABASE anat_imdb
```

lets run this and see

> what will the command be? psql -f filename

- Now we can run a file create a database without having to go into the terminal

> If we want to build up a database made of tables what is the first thing im going to have to do?
> `\c anat_imdb`

### Let's start to create a table to store some information

- part of what makes a relational database is organising data in these tables

- what columns do you think we should have? 'Title' , 'year of release' , 'rating'
- when we create a table in sql we need to stipulate and say what the columns will be
- what we've left out is a key or id - we need a way of determining a specific entry because think of imdb there might be 40 films with the same name - they need a unique identifier so lets put that in the list of headers
- when we create a table we _also_ need to stipulate what data types these columns are expecting
- so lets look at sql datatypes
- what type would we expect title? rating? year of release

```sql
-- boolean  -- BOOLEAN
-- string   -- VARCHAR (string of variable characters)
-- number   -- INT
```

So now lets go back to our file
how would we create a table to store our films

```sql
CREATE TABLE ();
```

I recommend always starting off writing a table like this, otherwise if you miss something off you'll get error at or around line x

- what was our first header

```sql
CREATE TABLE films (
  film_id PRIMARY KEY, -- this is because this is how we'll be uniquely identifying
  title VARCHAR,
  year_of_release INT,
  rating FLOAT
);
```

- lets see how that works - hmm error - we're missing something from this film id syntax what do you think is missing? incrementing
- syntax for that is SERIAL

```sql
CREATE TABLE films (
  film_id SERIAL PRIMARY KEY, -- this is because this is how we'll be uniquely identifying
  title VARCHAR,
  year_of_release INT,
  rating FLOAT
);
```

> How would I check this has worked?
> `psql`, `\l`, `\c anat_imdb`, whats the command to see the tables? `\dt`

- is there another way without having to do that in the terminal?
- at the bottom of the file `\dt`
- if we don't want that all showing up in the terminal what trick could we do? (pipe)
  `\dt > output.txt` - take the contents of this command and write it

## rows

- what would a single item of data look like in our database or these tables we have? rows

- tell me any films you want to add and help me fill in the year of release and give me ratings

- lets use our SQL syntax to insert these rows - anyone can tell me how id put a single row in

```sql

INSERT INTO films (

)
```

which column names are we going to populate in this table (everything except the id - that will be generated and auto-populated for us by psql ) so all we need to worry about is the title, YoR and rating

```sql

INSERT INTO films (
title, year_of_release, rating
)
```

now we need to provide it the actual input with the keyword VALUES

```sql

INSERT INTO films (
title, year_of_release, rating
)
VALUES
('Jurassic Park', 1993, 9.9),
-- how do you think i'd put in 2 rows at once?  just a comma and another row here
('Inception', 2010, 9.1),
('Catch me if you can', 2002, 7.5);
```

lets see whether this works and pipe it into another output.txt file
\dt well we only have the tables like before

- how can i check that its actually gone in -- SELECT!

## Lets look at the anatomy of a SELECT

SELECT \* (what does this mean? all columns) so really

SELECT <columnname1, columnname2> FROM <tablename>

- so for this one what would it be

SELECT \* FROM films - select all columns from this table

> how many rows should that bring back? 3 - why? we put in 3 and we havent specified any conditions that only some should come back

## Lets put some conditions

- what year was 'BIG' released ? 1988 with rating 7.3 so lets add that in
- suppose i wanted the films that were in the 20th century only
  _WHERE_ clause yes get someone to navigate

```sql
SELECT * FROM films WHERE year_of_release > 1889 AND
--dont use & in sql
year_of_release < 2000
```

- what if i want to only have the title -- change \* - title

> what should I see in output.txt

any qus about that thus far?

## What am i missing from this table if we're building imdb?

-  director
- you may be misled to extend the table out to have director_name and biography
- anybody forsee any problems in the way we've constructed - we're going to be duplicating our data
- the reasons we have tables and structure is to avoid duplicating 20 steven speilbergs- why would we want 20 speilberg biography paragraphs manaually entered
  (show second diagram)

> How do we solve this problem? NEW TABLE

- we can break this director information into a new table

- what columns would we expect for our director table? id, name , description
- what do i need to do with respect to my film table? add director id column
- this table will have a relation to the directors table

  - this will be referred to as a FOREIGN KEY

so lets do this in sql

```sql
CREATE TABLE directors (
  director_id SERIAL PRIMARY KEY,
  dirName VARCHAR,
  bio TEXT
)
```

what do we need to add to films

```sql
CREATE TABLE films (
id SERIAL PRIMARY KEY,
title VARCHAR,
year_of_release INT,
rating FLOAT,
director_id INT REFERENCES directors (director_id)
);
```

- director_id is a column in films which references the director_id column in the directors table
- referencing a table that hasn't yet been created lets see that error

- what about my INSERTION order which should go first? directors

```sql
INSERT INTO films (title, yOF, rating, director_id) VALUES (
  'Jurassic Park', 1998, 9.9, 1
);
```

and lets select - well now ive solved the duplication BUT

- what if i want to see the director and his bio too? anyone know what i'd need for that?

## Anatomy of a JOIN

- its going to take this table look for any matches from this foreign key and this primary key in this table
- so it will _look_ like that duplicated data table

```sql
-- JOIN defaults to inner join
SELECT * FROM films
JOIN directors -- the other table to bring together what else will we need? the condition of how to join
ON films.director_id = directors.director_id;
```

- so now its smashed on the other table - that foreign key and primary key like a hinge of where its joined
- thats a silly join we've brought back all the info what if i just wanted the title and directors name?

- what if i had two columns named the same in each table? column name is ambiguous
- films.name directors.name

- this look silly 2 columns in the output called name so a helpful operator to give it a temporary alias
  AS
  SELECT films.name, director.name AS director

- can do all of films columns and just the directors name column

  ```sql
  SELECT films.*, directors.name AS director
  ```
* what about those directors who i dont currently have films associated with?
* left join or right join?

- bring back all of the rows of the directors table and join on the extra rows too

- This is what will be referred to as a 1 - many relationship - because 1 director can be associated with many films, but visa versa each film will only have 1 director