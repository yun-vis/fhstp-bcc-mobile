---
# permalink: /404.html
layout: single
classes: wide
title: "Room Setup"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-11-09
---

# Import the ContactsApp project 

## Import an existing project

- Import the ContactsApp project from the previous lecture
- Click File > New > Import Project.
- In the window that appears, navigate to the root directory of the project you want to import and click OK.
- It takes sometime until the Andriod app explorer is built.

# [Guide to app architecture](https://developer.android.com/topic/architecture/intro)

- [UI Layer](https://developer.android.com/topic/architecture/ui-layer): The role of the UI is to display the application data on the screen and also to serve as the primary point of user interaction. Whenever the data changes, either due to user interaction (like pressing a button) or external input (like a network response), the UI should update to reflect those changes. Effectively, the UI is a visual representation of the application state as retrieved from the data layer.
  - [UI elements](): In Android's Jetpack Compose, UI elements are created using composable functions that describe the UI's appearance and behavior. These functions are the building blocks and can be nested to create complex interfaces, ranging from basic text and buttons to more complex components like app bars, navigation drawers, and date pickers, often built using the Material Design system. 
  
  - [State holders](https://developer.android.com/topic/architecture/ui-layer/stateholders): The UI layer guide discusses unidirectional data flow (UDF) as a means of producing and managing the UI State for the UI layer. It also highlights the benefits of delegating UDF management to a special class called a state holder. You can implement a state holder either through a ViewModel or a plain class. This document takes a closer look at state holders and the role they play in the UI layer.

- [Domain Layer](https://developer.android.com/topic/architecture/domain-layer): The domain layer is an optional layer that sits between the UI layer and the data layer. The domain layer is responsible for encapsulating complex business logic, or simple business logic that is reused by multiple ViewModels. This layer is optional because not all apps will have these requirements. You should only use it when needed-for example, to handle complexity or favor reusability.

- [Data Layer](https://developer.android.com/topic/architecture/data-layer): The data layer contains application data and business logic. The business logic is what gives value to your app—it's made of real-world business rules that determine how application data must be created, stored, and changed.
  - [Repositories](https://developer.android.com/topic/architecture/data-layer): The data layer is made of repositories that each can contain zero to many data sources. You should create a repository class for each different type of data you handle in your app. For example, you might create a MoviesRepository class for data related to movies, or a PaymentsRepository class for data related to payments.
  - [Data Sources](https://developer.android.com/topic/architecture/data-layer): Data sources are the origins or providers of data in your app’s data layer.
They define how and from where data is retrieved, saved, or updated.

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

### Step 1: Adapt the Gradle script. 

  - build.gradle.kts -> Project 
  in plugin, add 
  id("com.google.devtools.ksp") version "2.0.21-1.0.27" apply false

  ```kotlin
  // Top-level build file where you can add configuration options common to all sub-projects/modules.
  plugins {
      alias(libs.plugins.android.application) apply false
      alias(libs.plugins.kotlin.android) apply false
      alias(libs.plugins.kotlin.compose) apply false
      // add the following 
      id("com.google.devtools.ksp") version "2.0.21-1.0.27" apply false
  }
  ```

  - Explanation:
    - id("com.google.devtools.ksp"): This refers to the Gradle plugin ID. com.google.devtools.ksp is the Kotlin Symbol Processing (KSP) plugin, which is similar to annotation processors in Java (kapt) but optimized for Kotlin. KSP allows libraries to generate code at compile time based on annotations or other symbols.

    - version "1.9.0-1.0.13": This specifies the version of the KSP plugin you want to use.It ensures Gradle knows which version of the plugin to download and apply.

    - apply false: This is a subtle but important part.apply false does not apply the plugin to the project immediately. Instead, it just makes the plugin available so that it can be applied in subprojects or modules individually.This is common in a multi-module project, where you might want KSP only in certain modules (like app or data) rather than in the root project.

  - build.gradle.kts -> Module 
  in plugin (on the top), add 
  id("com.google.devtools.ksp")

  ```kotlin
  // At the top
  plugins {
      alias(libs.plugins.android.application)
      alias(libs.plugins.kotlin.android)
      alias(libs.plugins.kotlin.compose)
      // add the following
      id("com.google.devtools.ksp")
  }

  // At the bottom
  dependencies {

      // add material 3, but already exists
      implementation(libs.androidx.compose.material3)

      // room setup
      val room_version = "2.8.3"
      implementation("androidx.room:room-ktx:$room_version")
      implementation("androidx.room:room-runtime:$room_version")
      // In case of error, press gradle sync
      // It will download the room dependency and install it
      ksp("androidx.room:room-compiler:$room_version")

      // ...
      // viewModel() function
      implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.9.4")

  }
  ```
### Step 2: Create a package called db and a data class called ContactEntity

in ContactEntity.kt
```kotlin
package at.uastw.contactsapp.data.db

import androidx.room.ColumnInfo
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "contacts")
// @Entity is enough as the table name go with the class name by default. One can also define foreign key here.
data class ContactEntity(
    @PrimaryKey(autoGenerate = true) // go with the next line
    val _id: Int = 0, // database specific. set default to 0. shouldn't it be a default value null?
    val name: String,
    val age: Int,
    
    // The @ColumnInfo annotation in Room is used to customize the column details of a field in your Entity (table), such as: The column name in the database.
    // Change the name of column for room, add
    @ColumnInfo("telephone_number")
    val telephoneNumber: String
)
```

### Step 3: Under db package, create a DAO using Kotlin Interface and call it ContactsDao

Room requires your DAO (Data Access Object) to be an interface (or an abstract class) so that it can automatically generate the implementation for you at compile time.

in ContactsDao.kt
```kotlin
package at.uastw.contactsapp.data.db

import androidx.room.Dao
import androidx.room.Delete
import androidx.room.Insert
import androidx.room.OnConflictStrategy
import androidx.room.Query
import androidx.room.Update
import kotlinx.coroutines.flow.Flow

@Dao
interface ContactsDao {

    //    @Insert
    // There are strategies implemented
    // @Upsert: if exist then update, otherwise insert
    @Insert(onConflict = OnConflictStrategy.IGNORE)
    fun addContact(contactEntity: ContactEntity)

    @Update
    fun updateContact(contactEntity: ContactEntity)

    @Delete
    fun deleteContact(contactEntity: ContactEntity)

    // SQL keywords are not case sensitive, keep it upper case to distinct from user-defined content
    @Query("SELECT * FROM contacts")
    fun getAllContacts(): Flow<List<ContactEntity>>

    @Query("SELECT * FROM contacts WHERE name = :contactName")
    // flow is a ds that will notify us about there is a change in the table. in practice, the flow will emit a new list of contact
    fun findContactsWithName(contactName: String): Flow<List<ContactEntity>>
//    fun main(){
//        dao.findContactsWithName("Ali")
//    }
}
```

### Step 4: Under db package, create a db using Kotlin Class and call it ContactsDatabase

in ContactsDatabase.kt
```kotlin
package at.uastw.contactsapp.data.db

import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

// Typically one database for the whole app
// One entity per table, at least one DAO where entities are used
// Entities and Dao are all linked to the db
// @Database(entities = [ContactEntity::class, add "," when more databases], version = 1)
// Why version of the database? db are installed with app on individual devices.
// We need to version our db in app development.
@Database(
    entities = [ContactEntity::class], // The database will include one entity/table: ContactEntity.
    version = 1
)
abstract class ContactsDatabase : RoomDatabase() { 
// You must extend RoomDatabase to create a Room database class. It’s abstract because Room generates the actual implementation for you at compile time.
    abstract fun contactsDao(): ContactsDao

    // This is an abstract function that returns your DAO (ContactsDao). Room will automatically generate the code to return the DAO implementation when the database is built. You’ll use this to perform database operations like insert, update, query, delete, etc.

    // in general, the same structure for every database
    // need to double check it

    // companion object: store data at class level, as static in java context
    // A companion object in Kotlin acts like a static object in Java — meaning it holds a single copy of variables and methods for the entire class.
    // Here it’s used to: Hold a singleton instance of the database. Provide a thread-safe way to create or get that instance.

    companion object {

        // concept from memory access
        // read the main memory RAM, instead of Cache
        // make sure to create one db instance per app (singleton pattern)
        // @Volatile ensures that changes made to Instance by one thread are visible to all threads immediately. Instance holds a single reference to your database so multiple parts of your app don’t create multiple copies (which could corrupt the data).
        @Volatile
        private var Instance: ContactsDatabase? = null

        // Provides a global access point to your database. If the database hasn’t been created yet, it builds one.
        fun getDatabase(context: Context): ContactsDatabase {
            // if the Instance is not null, return it, otherwise create a new database instance.
            // synchronized: only one thread can enter this block
            // Checks if Instance is null. If yes, uses synchronized(this) to ensure only one thread at a time can create the database — prevents multiple instances in multithreaded environments.
            return Instance ?: synchronized(this) {
                // build database
                // context: Application or Activity context. For the resources.
                // ContactsDatabase::class.java: The database class to instantiate.
                // "contact_database": The physical database filename.
                // .fallbackToDestructiveMigration(false): Prevents automatic deletion of old data if a version conflict occurs (if true, it would recreate the DB when a version mismatch happens).
                val instance = Room.databaseBuilder(context, ContactsDatabase::class.java, "contact_database")
                    /**
                     * Setting this option in your app's database builder means that Room
                     * permanently deletes all data from the tables in your database when it
                     * attempts to perform a migration with no defined migration path.
                     */
                    // Do not use in production app as it deleted all inputs from the users
                    // .fallbackToDestructiveMigration() -> deprecated
                    .fallbackToDestructiveMigration(false)
                    .build()

                // Stores and returns the newly created database instance.
                Instance = instance
                return instance
            }
        }
    }
}
```

### Step 5: Compile and see the implementation under the folder /java

# Terminology

- [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences): A SharedPreferences object points to a file containing key-value pairs and provides simple methods to read and write them. DataStore is a modern data storage solution that you should use instead of SharedPreferences. It builds on Kotlin coroutines and Flow, and overcomes many of the drawbacks of SharedPreferences.

- [DataStore](https://developer.android.com/topic/libraries/architecture/datastore)

- [Persistent models](): A persistent data model is a way to store data in a non-volatile storage system (e.g., hard drives, SSDs) so it remains available after power cycles and application closures, unlike ephemeral data stored in volatile memory like RAM. Persistence in data management means storing data permanently so that it continues to exist independently of the process or program that created it, ensuring durability, consistency, and long-term accessibility.

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

- unique identifier (UID): A unique identifier (UID) is an identifier that is guaranteed to be unique among all identifiers used for those objects and for a specific purpose. For example, in database management, unique identifiers distinguish one record from another, which allows data to be retrievable quickly and efficiently without ambiguity or confusion. They can also link different records from different tables easily.

## References from Victor

- [Room](https://developer.android.com/training/data-storage/room) 
- [Architecture](https://developer.android.com/topic/architecture)
- [Coroutines](https://developer.android.com/kotlin/coroutines)
- [ContactsApp Tutorial](https://www.youtube.com/watch?v=bOd3wO0uFr8)

# Open Questions

- Add New -> Kolin file and class in Android Studio
- Add material 3 to the current project
- Data injection attack: A data injection attack is a technique in which an unauthorized person sends copied or fabricated data to a target system, compromising its integrity and reliability. Room already gives you strong protection if you use it correctly. The key idea: never build SQL by concatenating untrusted strings. Use Room’s parameter binding (or safe builders) and whitelist any dynamic SQL identifiers (table/column/order direction). Below are concrete rules and code examples in Kotlin.