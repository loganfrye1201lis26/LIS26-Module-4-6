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
