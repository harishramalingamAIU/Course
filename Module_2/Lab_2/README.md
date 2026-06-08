# Lab 2 - Cloud SQL for MySQL: Qwik Start

## Lab Summary

This lab focused on creating and managing a Cloud SQL for MySQL instance using Google Cloud Platform. The lab demonstrated how to create a Cloud SQL instance, connect to it using the MySQL client in Cloud Shell, create a database, create tables, insert records, and retrieve data using SQL queries.

## Lab Objective

The objective of this lab was to learn how to provision a Cloud SQL for MySQL instance, connect to it securely, and perform basic database administration and SQL operations within Google Cloud.

## Tools & Services Used

- Google Cloud Platform (GCP)
- Cloud SQL for MySQL
- Cloud Shell
- MySQL Client
- gcloud CLI
- SQL Commands
- Relational Databases

## Key Steps Performed

### 1. Created a Cloud SQL Instance

- Opened the Cloud SQL service from the Google Cloud Console.
- Selected **MySQL** as the database engine.
- Chose **Enterprise Edition**.
- Selected the **Development** preset.
- Configured MySQL version 8.
- Created a Cloud SQL instance named `myinstance`.

- Instance configuration:
  - Database Engine: MySQL
  - Edition: Enterprise
  - Preset: Development
  - Version: MySQL 8
  - Instance ID: `myinstance`

- Verified that the instance was successfully created and available.

### 2. Connected to the Cloud SQL Instance

- Opened Cloud Shell.
- Connected to the Cloud SQL instance using the gcloud command.

```bash
gcloud sql connect myinstance --user=root
```

- Authenticated using the generated root password.
- Successfully accessed the MySQL command-line interface.

### 3. Created a Database

- Created a new database named `guestbook`.

```sql
CREATE DATABASE guestbook;
```

- Verified that the database was successfully created.

### 4. Selected the Database

- Switched to the newly created database.

```sql
USE guestbook;
```

### 5. Created a Table

- Created a table named `entries` to store guestbook messages.

```sql
CREATE TABLE entries (
    guestName VARCHAR(255),
    content VARCHAR(255),
    entryID INT NOT NULL AUTO_INCREMENT,
    PRIMARY KEY(entryID)
);
```

- Verified that the table structure was created successfully.

### 6. Inserted Sample Data

- Added sample guestbook entries into the table.

```sql
INSERT INTO entries (guestName, content)
VALUES ("first guest", "I got here!");

INSERT INTO entries (guestName, content)
VALUES ("second guest", "Me too!");
```

- Confirmed that the records were inserted without errors.

### 7. Retrieved Data from the Database

- Queried the table to view all stored records.

```sql
SELECT * FROM entries;
```

- Output returned:

```text
+--------------+-------------------+---------+
| guestName    | content           | entryID |
+--------------+-------------------+---------+
| first guest  | I got here!       |       1 |
| second guest | Me too!           |       2 |
+--------------+-------------------+---------+
```

- Verified that the data was successfully stored and retrieved.

## Evidence/Screenshots

Screenshots/Evidence of work are provided in the screenshot folder.

## Results

- Successfully created a Cloud SQL for MySQL instance.
- Connected to the instance using Cloud Shell.
- Created a custom MySQL database.
- Created a relational database table.
- Inserted records into the database.
- Retrieved and verified stored data using SQL queries.
- Gained hands-on experience with Cloud SQL administration and MySQL operations in Google Cloud.

## Reflection

This lab provided practical experience with managed relational databases in Google Cloud. I learned how to provision a Cloud SQL for MySQL instance, connect using the MySQL client, create databases and tables, and perform basic CRUD-related database operations. The lab improved my understanding of cloud-hosted database management, SQL query execution, and how Google Cloud simplifies database administration through managed services.