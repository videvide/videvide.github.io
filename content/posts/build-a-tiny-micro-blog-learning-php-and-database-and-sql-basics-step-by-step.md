---
date: '2025-11-15T17:51:14+02:00'
title: 'Build a Tiny Micro-Blog: Learning PHP, Database, and SQL Basics Step by Step 🐘'
tags: ["php", "xampp", "mamp", "lamp", "sql", "mysql", "database", "data models", "web development", "server-side", "internet", "security", "authentication", "tutorial"]
categories: ["full-stack"]
---

**Advice:** Use LLMs and search engines to expand on topics, do more research, or get help if you get stuck on a command.

## This is what we are learning in this project
- Web fundamentals and how the internet works
- Server-side architecture and application design
- PHP fundamentals and backend development concepts
- Full-stack PHP development using the Laravel framework
- Relational databases (MySQL), SQL, data modeling, and database normalization
- Web security fundamentals

### Links to sources for each topic (Read this)

- [How does the internet work?](https://developer.mozilla.org/en-US/docs/Learn_web_development/Howto/Web_mechanics/How_does_the_Internet_work)
- [Internet on Wikipedia](https://en.wikipedia.org/wiki/Internet)
- [Introduction to the server side](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/First_steps/Introduction)
- [Client-server model](https://en.wikipedia.org/wiki/Client%E2%80%93server_model)
- [What is PHP and what can it do?](https://www.php.net/manual/en/introduction.php)
- [PHP on Wikipedia](https://en.wikipedia.org/wiki/PHP)
- [XAMPP: Cross-platform PHP development environment](https://www.apachefriends.org/)
- [MAMP: macOS PHP development environment](https://www.mamp.info/en/mac/)
- [LAMP: Linux, Apache, MySQL, PHP](https://en.wikipedia.org/wiki/LAMP_(software_bundle))
- [Laravel - The PHP Framework For Web Artisans](https://laravel.com/)
- [Database on Wikipedia](https://en.wikipedia.org/wiki/Database)
- [SQL on Wikipedia](https://en.wikipedia.org/wiki/SQL)
- [Tutorials (SQLZoo): Learn SQL step by step](https://sqlzoo.net/wiki/SQL_Tutorial)
- [Tutorials (SQLBolt): Learn SQL with simple, interactive exercises](https://sqlbolt.com/)
- [MySQL on Wikipedia](https://en.wikipedia.org/wiki/MySQL)
- [MySQL: Understanding What It Is and How It’s Used](https://www.oracle.com/mysql/what-is-mysql/)
- [The Difference Between MariaDB vs. MySQL](https://mariadb.com/database-topics/mariadb-vs-mysql/)
- [Data model](https://en.wikipedia.org/wiki/Data_model)
- [Database normalization](https://www.freecodecamp.org/news/database-normalization-1nf-2nf-3nf-table-examples/)
- [Database Security - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/Database_Security_Cheat_Sheet.html)
- [SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [Security on the web](https://developer.mozilla.org/en-US/docs/Web/Security)
- [Authentication](https://developer.mozilla.org/en-US/docs/Glossary/Authentication)


### Web fundamentals and how the internet works

The Internet is a vast global network of interconnected computer networks that communicate using the Internet Protocol Suite (TCP/IP), enabling billions of devices from private, public, academic, business, and government networks to exchange data. It functions by linking local networks through devices like switches, routers, and modems, connecting them via physical and wireless infrastructures, often building on pre-existing telephone networks.

The Internet serves as the underlying infrastructure for various services, most notably the World Wide Web, which is a large collection of interconnected web pages accessed via browsers using domain names that map to numeric IP addresses. The Internet itself is not the Web but a network that supports multiple communication services including email, streaming, and file sharing. The system is decentralized and maintained by organizations like ICANN and the IETF for protocols and address management, with connectivity provided through Internet Service Providers (ISPs) that link networks globally .

### Server-side architecture and application design

Server-side architecture involves programs and operations that run on web servers, enabling websites to deliver dynamic and personalized content. It contrasts with client-side operations, which happen on the user's device, and relies heavily on the client-server model where clients request services, and servers respond by processing data and generating appropriate responses, often using web frameworks to streamline development .

This architecture allows websites to efficiently store data in databases, customize user experiences based on stored information, control access to content, and manage sessions and notifications. Server-side programming supports complex functionalities such as dynamic content generation, user authentication, data analysis, and personalized notifications, making it fundamental for modern, interactive web applications .

### PHP fundamentals and backend development concepts

PHP is a widely-used open-source scripting language primarily designed for server-side web development, capable of embedding into HTML to generate dynamic web page content on the server that is then sent to the client. It is cross-platform, works with many web servers like Apache and Nginx, and supports both procedural and object-oriented programming, with extensive database connectivity and protocol support. PHP runs on web servers via an interpreter, most commonly powered by the Zend Engine, and is enhanced by caching mechanisms like OPcache for performance. It is the backbone of many web applications and content management systems.​

For backend PHP development, developers often use local server environments like XAMPP (a package with Apache, MariaDB, PHP, and Perl maintained by Apache Friends) or MAMP (similar stack specifically for macOS). These provide preconfigured, easy-to-install platforms for local testing and development, simulating live server environments. PHP is commonly deployed as part of the LAMP stack (Linux, Apache, MySQL/MariaDB, PHP), which forms a robust, open-source environment for building dynamic websites and backend services by handling HTTP requests, executing PHP code, interacting with databases, and serving generated content to clients.​

In summary, PHP fundamentals combine server-side scripting capabilities with a flexible ecosystem of tools and stacks like XAMPP, MAMP, and LAMP to enable efficient backend web development, from local setup to running complex, database-driven web applications.​

### Relational databases, SQL, data modeling, and database normalization

Relational databases organize data into tables composed of rows and columns, following the relational model developed by E. F. Codd. SQL (Structured Query Language) is the standard language used to define, manipulate, and query data within these relational tables. Data modeling in relational databases involves designing the schema that represents entities, their attributes, and relationships, often aiming for clarity and efficiency. Database normalization is a systematic process applied during data modeling that structures tables to minimize redundancy and avoid anomalies by dividing data into related tables following normal forms such as 1NF, 2NF, and 3NF. This ensures data integrity and simplifies updates by storing each fact only once and maintaining logical relationships between tables through keys. Together, these concepts facilitate efficient, reliable, and scalable data management in relational database systems.​

### Web security fundamentals

Web security fundamentals encompass a set of practices and principles aimed at protecting web applications, databases, and users from common threats such as SQL injection, authentication breaches, and unauthorized data access. Key to database security is isolating the database server, enforcing encrypted connections using TLS, and applying the principle of least privilege by granting only minimal necessary permissions to database accounts. Preventing SQL injection, a common and critical web attack, involves using parameterized queries or safe stored procedures instead of dynamically concatenating user input into SQL queries. Authentication ensures that users or systems accessing web resources are verified, preferably through strong methods such as mutual TLS or secured credential management rather than insecure approaches like basic authentication without encryption. Overall, maintaining security involves proper configuration, regular patching, secure authentication mechanisms, and protecting sensitive credentials outside source code, in addition to defensive coding and input validation techniques to harden applications and databases against attack.