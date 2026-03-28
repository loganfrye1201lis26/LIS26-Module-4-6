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
