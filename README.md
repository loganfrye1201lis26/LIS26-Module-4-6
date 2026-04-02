# LIS26-Module-4-6
## MySQL & PHP OPAC Setup Notes
- This project demonstrates how to install and configure a MySQL database on a Linux server and connect it to a PHP web application running on an Apache web server. The goal was to build a simple Online Public Access Catalog (OPAC) page that retrieves records from a database and displays them on a webpage.
The server environment was hosted on a Google Cloud Virtual Machine using the Linux command line.

## System Prep
### Update the System
- Before installing new software, the package list was updated and existing packages were upgraded.

sudo apt update
sudo apt upgrade -y

Updating ensures that the server is running the latest versions of system packages and reduces the chance of installation conflicts.

## Securing MySQL
- The MySQL security script was used to remove insecure default settings.

sudo mysql_secure_installation

Key steps included:
- Removing anonymous users
- Disabling remote root login
- Removing the test database
- Reloading privilege tables
This improves the security of the database environment.
### Access MySQL
1. sudo mysql
2. CREATE DATABASE opacdb ;
3. CREATE USER 'opacuser'@'localhost' IDENTIFIED BY 'password123' ;
4. GRANT ALL PRIVILEGES ON opacdb.* TO 'opacuser'@'localhost'; FLUSH PRIVILEGES;
### Creating the Books Table
- After selecting the database:
1. USE opacdb ;
2. CREATE TABLE books (
        id INT AUTO_INCREMENT PRIMARY KEY,
       title VARCHAR (255) ,
       author VARCHAR (255) ,
       year INT
   );
3. INSERT INTO books (title, author, year)
VALUES
('The Hobbit', 'J.R.R. Tolkien', 1937),
('1984', 'George Orwell', 1949),
('Pride and Prejudice', 'Jane Austen', 1813);

- These records were used to test database queries.

## Installing PHP-MySQL Support
- To allow PHP to communicate with the MySQL database:
1. sudo apt install php-mysql -y

- Apache was restarted afterward:
2. sudo systemctl restart apache2

## Outcome
- The final result was a working web application where:

1. Apache receives the web request.
2. PHP executes the script.
3. PHP connects to the MySQL database.
4. A query retrieves book records.
5. The results are displayed in the browser.

- This exercise demonstrated how web servers, scripting languages, and databases work together to create dynamic web applications.

## Reflection
This project provided a clear, hands-on understanding of how different components of a web application stack interact. Setting up MySQL, securing it, and connecting it to a PHP application through Apache reinforced the relationship among server configuration, database management, and backend scripting. Working in a Linux environment on a Google Cloud VM also highlighted the importance of command-line proficiency and system maintenance. One of the most valuable aspects was seeing how raw database data can be transformed into a user-facing interface using PHP. Creating the books table and successfully retrieving and displaying records demonstrated the practical flow of data from storage to presentation. Additionally, the security steps taken with MySQL emphasized the need for safe configurations even in small-scale projects.

Overall, this process strengthened both technical skills and conceptual understanding of full-stack web development, particularly how databases and web servers collaborate to deliver dynamic content.

# Module 5
## Overview
This week, I worked through the lectures on relational databases, building a basic OPAC, and creating a very simple cataloging module. The main goal was to practice structuring MySQL databases and then connecting them to HTML and PHP pages so that records could be searched and added through a web browser.

## Part 1: Practicing Basic MySQL Commands
I began by working through the relational database lecture and practicing the basic MySQL commands. I logged into MySQL, selected the correct database, and created tables to better understand how relational databases organize information.
- Issue #1:
-         No database selected
- Fix:  SHOW DATABASES;
        USE DinnerDB;
        SELECT DATABASE();
I then created the Meals and Ingredients tables.
create table Meals (
    meal_id int auto_increment primary key,
    meal_name varchar(100) not null,
    cuisine varchar(50),
    cooking_time int not null default 1 check (cooking_time > 0),
    vegetarian boolean
);

create table Ingredients (
    ingredient_id int auto_increment primary key,
    meal_id int not null,
    ingredient_name varchar(100) not null,
    quantity varchar(50),
    foreign key (meal_id) references Meals(meal_id) on delete cascade
);
This helped me understand the relationship between a primary key and a foreign key. The Meals table acted as the parent table, and the Ingredients table connected to it through meal_id, creating a one-to-many relationship.

## Part 2: Building the Basic OPAC
After working with MySQL, I moved on to the OPAC portion of the assignment. The purpose of this section was to create a very simple public catalog that could search records from the books table.
At first, I accidentally pasted HTML and PHP code directly into the bash terminal, which caused many syntax errors. I realized that HTML and PHP code had to be placed into files rather than run as shell commands.
To fix this, I opened the correct files using nano and saved the code there instead.
### Public OPAC page
cd /var/www/html
sudo nano mylibrary.html
sudo nano opac.php
php -l /var/www/html/opac.php
- The php -l command was especially helpful because it let me check for syntax errors in my PHP file before testing it in the browser.

## Part 3: Creating the Cataloging Module
Next, I created a separate cataloging area that allows records to be entered into the same books table through a web form.
sudo mkdir -p /var/www/html/cataloging
cd /var/www/html/cataloging
sudo nano index.html
sudo nano insert.php
php -l /var/www/html/cataloging/insert.php

## Part 4: Apache Authentication
The final part was protecting the cataloging module with Apache authentication so that only authorized users could access the record-entry page.
sudo htpasswd -c /etc/apache2/.htpasswd libcat
sudo nano /etc/apache2/apache2.conf
sudo nano /var/www/html/cataloging/.htaccess
sudo apachectl configtest
sudo systemctl restart apache2

# Reflection
This was one of the more challenging modules so far because it required several parts to work together simultaneously. A mistake in the database, PHP file, HTML form, or Apache configuration could stop the whole process from working. The biggest thing I learned was that troubleshooting has to be done carefully and step-by-step. I also have a much better understanding now of how an OPAC works behind the scenes. Even though this project was very basic, it helped clarify the connection among databases, web forms, and server-side processing.

# Module 5 Documentation
## Step-by-Step Setup & Functioning
1. **Datbase Connection**
The first step is establishing a connection to the database server so PHP can send SQL commands.
_Common Pattern:_
- Use [mysqli] with host, username, password, and database name defined as variables or constants.
- Call the appropriate constructor or connection function, and handle errors if the connection fails (for example, by displaying a friendly error message or logging it).
- Host is often [localhost] on your Ubuntu server.
- Credentials must match the MySQL user you created.
- The selected database name must match the one where your catalog tables live.

2. **Cataloging Module Structure (Adding Records)**
The cataloging module is usually an internal-facing page where staff can add or edit bibliographic records. In a simple PHP implementation, it typically consists of:
- **HTML form for input**
          - Fields: title, author, publication year, etc.
          - Uses method [POST] and submits back to the same PHP page.
- **Form handling logic**
          - On form submission, PHP reads [$_POST] variables (for example, $_POST['title']).
          - Basic validation is performed (checking for required fields such as title and author, numeric year fields, etc.).
          - The code builds a parameterized [INSERT] SQL statement to add a row to the database table.
          - The script executes the SQL and reports success or failure to the user.

3. **Search and Retrieval in the Bare-Bones OPAC**
The bare-bones OPAC usually consists of a search form and a result display page:
        1. **Search Form**
        - Single text box for keyword, or separate fields for title, author, and subject.
        - Uses method [GET] or [POST] and submits to a PHP script.
        2. **Query Construction**
           - The script reads the search input and builds an SQL [SELECT] statement with [WHERE] clauses.
           - Simple implementations use [LIKE] for partial matches, for example:
                   - [WHERE] title LIKE '%keyword%' OR author LIKE '%keyword%']
           - The query is executed against the bibliographic table.
        3. **Result Set Processing**
        - The script loops over the [mysqli_result] and prints each record as a row in an HTML table or as separate blocks.
        - Each record shows core fields such as title, author, publication year, and call number.
        - In real OPACs, each result would typically link to a detailed record page, but a bare-bones version may show all details in the list.
        4. **Handling No Results**
        - If the result set is empty, a “no records found” message is displayed rather than a blank page.
This minimal pattern reflects how OPACs let patrons search by title, author, subject, or keyword across the library’s holdings.

4. **Making the Modules More “Real World”**
- _Richer bibliographic data_
  Support for MARC 21 fields or a MARC-like structure instead of a single “books” table.
  Separate tables for authors, subjects, and publishers with foreign keys (normalization).
- _Holdings and item records_
  Distinction between the bibliographic “work” and copies (items) with barcodes, statuses, and locations.
- _Patron accounts and circulation_
  Authentication so patrons can log in, place holds, renew items, and see due dates.

5. **Necessary Configuration Steps**
- Install required softwar: Apache, PHP, & MYSQL
- Configure PHP: Ensure needed extensions are enabled (mysqli).
- Create the database and tables: [opacdb] ; Create tables matching the schema expected by the code.
- Place code in the web root: Copy the PHP files from the repo into [/var/www/html/opac] and [/var/www/html/catalog] ; Ensure file permissions allow the web server user to read them.
- Adjust configuration within PHP files: Set correct database host, username, password, and databse name.
- Test!!!: Open the OPAC and cataloging URLs in a browser ; Add a sample record and verify that it appears in OPAC search results.

## Key Small but Crucial Details
- Data types:  Storing years as [INT], while titles and authors use [VARCHAR] with enough length for longer strings.
- Indexes: Adding indexes on frequently searched fields (title, author, subject) improves performance, even on small datasets.
- Consistent field names: Matching database column names with form field names reduces confusion and errors when binding variables.
- Error handling: Checking the result of database queries and echoing or logging error messages helps with troubleshooting.
