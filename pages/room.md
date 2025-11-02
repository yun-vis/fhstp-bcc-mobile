---
# permalink: /404.html
layout: single
classes: wide
title: "Data"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-08-25
---

# Import the ContactApp project from the previous lecture

## Import an existing project
- Click File > New > Import Project.
- In the window that appears, navigate to the root directory of the project you want to import and click OK.


# Data

The codelab from the Android Developer documentation is too old and not usefull anymore, but here I am sending you my slides I used for the masterclass.
Since the slides are just for reference, here is the main documentation for it:
https://developer.android.com/training/data-storage/room
https://developer.android.com/topic/architecture
https://developer.android.com/kotlin/coroutines

Also, since I do not have videos of my lecture on the topic, I think this YouTube video is helpful if you would like to reproduce an example for practicing:
https://www.youtube.com/watch?v=bOd3wO0uFr8

# [Guide to app architecture](https://developer.android.com/topic/architecture/intro)

# [SQL](https://www.w3schools.com/sql/sql_intro.asp) 

SQL (Structured Query Language) is the standard language used to store, manipulate, and retrieve data in relational databases (like MySQL, PostgreSQL, Oracle, SQLite, etc.).

| Category | Full Form                        | Purpose                                                                               | Common Commands                                 |
| -------- | -------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------- |
| **DDL**  | **Data Definition Language**     | Defines and manages the **structure** of database objects (tables, schemas, indexes). | `CREATE`, `ALTER`, `DROP`, `RENAME`, `TRUNCATE` |
| **DML**  | **Data Manipulation Language**   | Handles the **modification** of data stored in tables.                                | `INSERT`, `UPDATE`, `DELETE`, `MERGE`           |
| **DQL**  | **Data Query Language**          | Used to **retrieve** data from databases.                                             | `SELECT`                                        |
| **DCL**  | **Data Control Language**        | Controls **access** and **permissions** to database objects.                          | `GRANT`, `REVOKE`                               |
| **TCL**  | **Transaction Control Language** | Manages **transactions** and ensures data integrity.                                  | `COMMIT`, `ROLLBACK`, `SAVEPOINT`               |

## [SQLite Database](https://developer.android.com/training/data-storage/sqlite) 
Saving data to a database is ideal for repeating or structured data, such as contact information. Although SQLite APIs are powerful, they are fairly low-level and require a great deal of time and effort to use.

## [Room Persistence Library](https://developer.android.com/training/data-storage/room) 
- Abstration over SQLite
- Compile-time verification: There is no compile-time verification of raw SQL queries. As your data graph changes, you need to update the affected SQL queries manually. This process can be time consuming and error prone. 
- Main components
  - Data Entity
  - Data Access Object (DAO)
  - Database

### Data Entity
  - @PrimaryKey: Defines the unique identifier for each record in a table.
    - Ensures every row is unique.
    - Used internally by Room for updates, deletions, and relationships.
  - @ColumnInfo: Defines metadata about how a class property maps to a column in the database.
    - Customize the column name, default value, or type affinity.
  - @ForeignKey: Defines a relationship between two entities — a parent table and a child table. It enforces referential integrity (so child rows must match existing parent rows).
    - Connect tables through a parent–child relationship.
    - Enforce data consistency between related entities.

## Database Migration Problem
A database migration is the process of updating the database schema (tables, columns, indexes) without losing existing user data when the app is updated.
In Android, this is particularly important when using Room, because Room enforces compile-time schema validation.

## Room Setup

```kotlin
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    alias(libs.plugins.android.application) apply false
    alias(libs.plugins.kotlin.android) apply false
    alias(libs.plugins.kotlin.compose) apply false
}
```

# Terminology

- [Coroutine]():

- [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences): A SharedPreferences object points to a file containing key-value pairs and provides simple methods to read and write them. DataStore is a modern data storage solution that you should use instead of SharedPreferences. It builds on Kotlin coroutines and Flow, and overcomes many of the drawbacks of SharedPreferences.

- [DataStore](https://developer.android.com/topic/libraries/architecture/datastore)

- [Persistent models](): A persistent data model is a way to store data in a non-volatile storage system (e.g., hard drives, SSDs) so it remains available after power cycles and application closures, unlike ephemeral data stored in volatile memory like RAM. 

- [Single Source of Truth (SSOT)](): The SSOT is the owner of that data, and only the SSOT can modify or mutate it. To achieve this, the SSOT exposes the data using an immutable type, and to modify the data, the SSOT exposes functions or receive events that other types can call.

- [Unidirectional Data Flow](): The single source of truth principle is often used in our guides with the Unidirectional Data Flow (UDF) pattern. In UDF, state flows in only one direction. The events that modify the data flow in the opposite direction.
  - Event flow: A user interacts with an element (e.g., clicks a button) or another part of the app triggers an event. This event is passed up the component hierarchy to a handler, often in a ViewModel or state management layer.
  - State update: The handler processes the event and updates the application's state accordingly. For example, a "favorite" button click would update the isFavorite state for that item.
  - State flow: The updated state is then passed down from the state holder (like the ViewModel) to the UI components.
  - UI re-render: The UI components, which are observing the state, automatically re-render to reflect the new state. 

- [Database](): A database is an electronically stored, systematic collection of data. It can contain any type of data, including words, numbers, images, videos, and files. You can use software called a database management system (DBMS) to store, retrieve, and edit data.

- [Preference database](): A preference database stores user preferences for personalization and can be a simple key-value store for small data, like app settings, or a relational database for complex, large-scale preference data. 

  - Shared Preferences: A simple key-value store, often used in mobile apps, for storing small amounts of data like user settings (e.g., a selected theme color).

  - Preferences refers to lightweight, structured configuration data. In a database context, preferences refer to structured data that represents configurable choices, options, or settings made by a user, group, or system — typically small, descriptive, and key–value based.
  
  - Unstructured data such as: Video files (MP4, MOV), Images (JPEG, PNG), Audio (MP3, WAV), and Documents (PDF, DOCX) are large binary blobs and: Don’t fit well in preference or key–value stores (which are optimized for text/JSON). Cause performance, scaling, and backup issues. Are difficult to query, cache, or index effectively. Store them in dedicated object storage or content management systems, and keep only metadata or references in your structured data store.

  - Tables: Collections of related data organized in rows and columns (like a spreadsheet). Each table represents one type of entity (e.g., Customers, Orders, Products).
  - Columns: Define the attributes or fields of the entity (e.g., customer_id, name, email). Each column has a specific data type (e.g., INT, VARCHAR, DATE).
  - Rows: Represent individual records or instances of data (e.g., one customer, one order). Each row follows the same schema defined by the table’s columns.
  
- Data Graph: In Room, a data graph refers to the network of related data objects (entities) that are linked together through relationships

- [Singleton]: The singleton pattern is a software design pattern that ensures a class has only one instance and provides a global point of access to it. It is used when a program needs a single object for tasks like managing a cache, handling logging, or controlling access to a database connection. 