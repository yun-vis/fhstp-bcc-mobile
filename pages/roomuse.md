---
# permalink: /404.html
layout: single
classes: wide
title: "Room Usage"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-11-04
---

# Concept

## [Coroutine](https://kotlinlang.org/docs/coroutines-overview.html#coroutine-context-and-behavior)
Applications often need to perform multiple tasks at the same time, such as responding to user input, loading data, or updating the screen. To support this, they rely on concurrency, which allows operations to run independently without blocking each other.

## [Kotlin coroutines on Android](https://developer.android.com/kotlin/coroutines)
A coroutine is a concurrency design pattern that you can use on Android to simplify code that executes asynchronously. Coroutines were added to Kotlin in version 1.3 and are based on established concepts from other languages.

# Room Setup - Continued

## Step 1: Create a package called Coroutines and a file called Coroutines

in Coroutines.kt
```kotlin
package at.uastw.contactsapp.data.coroutines

import android.util.Log
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.async
import kotlinx.coroutines.coroutineScope
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch
import kotlinx.coroutines.runBlocking
import kotlin.system.measureTimeMillis

// so we can call the file directly without starting the whole app
// just to experience coroutine
fun main() {

    // measure the performance
    val duration = measureTimeMillis {
//        repeat(5000) {
//            println("a")
//        }

        // coroutine, and can call suspend functions
        runBlocking {
            // suspend can be called in a suspend function
            // sync should be 3 seconds
            printWeatherReport()
        }
    }

    println("The program took $duration ms")
}

// this function can be paused
suspend fun printWeatherReport() {

    // Step 1
//    delay(1000)
//    val weather = "Sunny"
//
//    delay(2000)
//    // "Windows + period" to open the Emoji Panel on Windows
//    // Windows: Alt+0176
//    // Mac: Shift + Option + 8
//    val temperature = "12°C"
//
//    println("The weather is $weather and the temperature is $temperature")

    // Step 2
    println("1: Thread: " + Thread.currentThread().name)

    coroutineScope {

        println("2: Thread: " + Thread.currentThread().name)

        // step 2
//        val weather = getWeather()
//        val temperature = getTemperature()

        // step 3
        // 20:43
        // Deferred, the string is available later
        // therefore the $weather does not return the expected value
//        val weather = async { getWeather() }
//        val temperature = async { getTemperature() }

        // step 4
//        val weatherDeferred = async { getWeather() }
//        val temperatureDeferred = async { getTemperature() }
        // The result of the deferred is available when it is completed and can be retrieved
        // by await method, which throws an exception if the deferred had failed.
//        val weather = weatherDeferred.await()
//        val temperature = temperatureDeferred.await()

        // step 5, observation and discussion
//        val weatherDeferred = async { getWeather() }
//        // await waits until the above code finishes
//        val weather = weatherDeferred.await()
//        // become 3 sec
//        val temperatureDeferred = async { getTemperature() }
//        val temperature = temperatureDeferred.await()

        // also start a coroutine, what's the difference?
        // execute a coroutine without expecting a return value, while async does
        // check the order of the print out in terminal
//        launch {
//            delay(400)
//            println("Hiho")
//        }

        // step 6: change the context
        // change the thread
        val weatherDeferred = async(Dispatchers.IO) {
            println("3: Thread: " + Thread.currentThread().name)
            getWeather()
        }
        val temperatureDeferred = async { getTemperature() }
        val weather = weatherDeferred.await()
        val temperature = temperatureDeferred.await()


        println("The weather is $weather and the temperature is $temperature")
    }
//    println(System.getProperty("file.encoding"))
//    println("Temperature: 25°C")
//    Log.d("TEST", "25\u00B0C")
//    Log.d("TEST", "25°C")
}

// suspend function is something that should run in the background
suspend fun getWeather(): String {
    delay(1000)
    return "Sunny"
}

suspend fun getTemperature(): String {
    delay(2000)
    return "12°C"
}
```

## Step 2: Links to the database

- Change the the run configuration from CoroutinesKt back to app
- Add suspend keyword to ContactDao.kt (based on slides)

in Contact.kt
```kotlin
@Dao
interface ContactsDao {

    //    @Insert
    // There are strategies implemented
    // @Upsert: if exist then update, otherwise insert
    @Insert(onConflict = OnConflictStrategy.IGNORE)
    suspend fun addContact(contactEntity: ContactEntity)

    @Update
    suspend fun updateContact(contactEntity: ContactEntity)

    @Delete
    suspend fun deleteContact(contactEntity: ContactEntity)

    // newly added
    @Query("SELECT * FROM contacts WHERE _id = :id")
    suspend fun findContentById(id: String): ContactEntity

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
- Update Contact.kt with Id

in Contact.kt
```kotlin
data class Contact(
    val id: Int = 0,
    // ...
)
```

Errors show up
in ContactRepository.kt
```kotlin
package at.uastw.contactsapp.data

import at.uastw.contactsapp.data.db.ContactEntity
import at.uastw.contactsapp.data.db.ContactsDao
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.flow.update
import kotlin.collections.plus

// Fake Database
class ContactRepository(private val contactsDao: ContactsDao) {

    // delete the following
    // private val _contacts = MutableStateFlow(createContacts())
    // val contacts = _contacts.asStateFlow()
    val contacts = contactsDao.getAllContacts()

    // move it to after contacts. why?
    val names = listOf(
        "Max",
        "Tom",
        "Anna",
        "Matt"
    )

    // delete the following
//    fun createContacts(): List<Contact> {
//        val contacts = (1..20).map{
//            Contact(
//                "${names.random()} $it",
//                "+43 123455$it",
//                25 + it
//            )
//        }
//
//        return contacts
//    }

    // update the following
//    fun addRandomContact() {
//        _contacts.update { oldList ->
//            oldList + Contact(0, names.random(), "+4357894", 45)
//        }
//    }
    suspend fun addRandomContact() {
        contactsDao.addContact(
            ContactEntity(0, names.random(), 45, "+4357894")
        )
    }
}
```

- Update ContactsViewModel.kt

in ContactsViewModel.kt
```kotlin

// class ContactsViewModel(val repository: ContactRepository = ContactRepository()): ViewModel() {
class ContactsViewModel(private val repository: ContactRepository) : ViewModel() {

    // remove
//    private val _contactsUiState = MutableStateFlow(ContactsUiState(emptyList(), null))
//    val contactsUiState = _contactsUiState.asStateFlow()
    // change from a flow to a stateflow, which collect a state
    // contacts return a flow and stateIn return a stateflow
    val contactsUiState = repository.contacts.stateIn(
        viewModelScope,
        SharingStarted.WhileSubscribed(5000),
        // when it should start the state, and how long it should keep it
        // it will remain 5 sec after the viewmodel scope is closed
        emptyList() // Define the initial value
    )

    // remove
//    init {
//        viewModelScope.launch {
//            repository.contacts.collect { data ->
//                // the collect function is called whenever the
//                // contacts data changes
//                _contactsUiState.update {
//                    it.copy(contacts = data)
//                }
//            }
//        }
//    }


    // update
    fun onAddButtonClicked() {

        // in viewmodel, we can start a coroutine
        // launch or async, launch because no return value
        viewModelScope.launch {
            repository.addRandomContact()
        }
    }

    // remove
//    fun onCardClick(index: Int) {
//        _contactsUiState.update {
//            it.copy(selectedCardIndex = index)
//        }
//    }
}
```
- Go back to the ContactRepository

in ContactRepository.kt
```kotlin
    // update contacts
    // val contacts = contactsDao.getAllContacts()
    val contacts = contactsDao.getAllContacts().map{ contactList ->
        contactList.map{entity->
            Contact(entity._id, entity.name, entity.telephoneNumber, entity.age)
        }
    }
```
- Go to the ContactsUI

in ContactsUI.kt  
```kotlin

// Step 1
    // remove
    // Log.i("ContactsApp", "Selected: ${state.selectedCardIndex}")

// Step 2
        LazyColumn {
            // update, because state is now a contact
//            itemsIndexed(state.contacts) { index, contact ->
            itemsIndexed(state) { index, contact ->
                // Here we need to provide the composable that shows a single contact
                // using the if we can change the composable if it is the selected card or not
                // remove if statement
//                if (index == state.selectedCardIndex) {
//                    ContactDetails(contact)
//                } else {
                ContactListItem(contact, onCardClick = {
                    // contactsViewModel.onCardClick(index)
                })
//                }
            }
        }

// Step 3: add 0
@Preview
@Composable
private fun ContactDetailsPreview() {
    ContactDetails(Contact(0,"Andrea", "+4354897", 28))
}

@Preview
@Composable
private fun ContactListItemPreview() {
    ContactListItem(Contact(0,"Andrea", "+4354897", 28), {})
}
```

Until here, the code will run but crashes. check logcat
1. check ContactsViewModel, we need a repository
2. we create the ContactsViewModel by calling viewModel() in ContactUI.kt. Right-click on ContactsViewModel -> go to -> declaration or usage. To fix it, we need to create a dao somehow? go to ContactDatabase. We want to have single instance for it. For Android, we can use so-called application class.
3. Go to app -> manifests
We can have many activities, but one application
```kotlin
        android:theme="@style/Theme.ContactsApp"
        android:name=".ContactsApplication">
```
4. We take the name and create a kotlin class with exactly the same name. The class must extend the Application.

```kotlin
package at.uastw.contactsapp

import android.app.Application
import at.uastw.contactsapp.data.ContactRepository
import at.uastw.contactsapp.data.db.ContactsDatabase
import at.uastw.contactsapp.ui.ContactListItem

class ContactsApplication : Application() { // only one instance

    // by lazy: only execute this code once, once it is accessed somewhere
    val contactRepository by lazy {

        val contactsDao = ContactsDatabase.getDatabase(this).contactsDao()
        ContactRepository(contactsDao)
    }
}
```

5. Connect this application somehow with our viewmodel. In viewModel(), we need to provide a factory. factory is an object that creates another object.
Add New -> Kotline class -> Object and name it AppViewModelProvider
(put it under ui, because viewmodel belongs to the ui)

in AppViewModelProvider.kt
```kotlin
package at.uastw.contactsapp.ui

import androidx.lifecycle.ViewModelProvider.AndroidViewModelFactory.Companion.APPLICATION_KEY
import androidx.lifecycle.viewmodel.initializer
import androidx.lifecycle.viewmodel.viewModelFactory
import at.uastw.contactsapp.ContactsApplication

object AppViewModelProvider {

    val Factory = viewModelFactory {
        initializer {
            val contactsApplication = this[APPLICATION_KEY] as ContactsApplication
            ContactsViewModel(contactsApplication.contactRepository)
        }
    }
}
```

6. Go to ContactsUI.kt

```kotlin
@Composable
fun ContactsApp(
    modifier: Modifier = Modifier,
    contactsViewModel: ContactsViewModel = viewModel(factory = AppViewModelProvider.Factory)) {
      // ...
}
```

7. From UI explorer -> more tool windows -> add app inspection. Select the current app and check database inspector. Double click on contacts, the it should show the current data. Since we use a FLow in Dao, we can update the name in the inspector. Observe that the app UI is also updated.

8. Create a detailed view model
Add New -> Kotline class and name it ContactDetailViewModel

in ContactDetailViewModel.kt
```kotlin
package at.uastw.contactsapp.ui

import androidx.lifecycle.SavedStateHandle
import androidx.lifecycle.ViewModel
import at.uastw.contactsapp.data.ContactRepository

class ContactDetailViewModel(
    private val savedStateHandle: SavedStateHandle,
    private val repository: ContactRepository) : ViewModel()
{
    // can use to read contactId
    // add to the nav controller
    private val contactId: Int = savedStateHandle["contactId"] ?: 0
}
```

in AppViewModelProvider.kt
```kotlin
object AppViewModelProvider {

    val Factory = viewModelFactory {
        initializer {
            val contactsApplication = this[APPLICATION_KEY] as ContactsApplication
            ContactsViewModel(contactsApplication.contactRepository)
        }

        initializer {
            val contactsApplication = this[APPLICATION_KEY] as ContactsApplication
            ContactDetailViewModel(
                this.createSavedStateHandle(),
                contactsApplication.contactRepository)
        }
    }
}
```


```kotlin
```

# Keywords

- [launch]()
- [suspend]()
- [runBlocking]()
- [Deferred](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-deferred/): Deferred value is a non-blocking cancellable future — it is a Job with a result. It is created with the async coroutine builder or via the constructor of CompletableDeferred class. It is in active state while the value is being computed.
- [await](): The result of the deferred is available when it is completed and can be retrieved by await method, which throws an exception if the deferred had failed.
- [context](https://www.geeksforgeeks.org/android/what-is-context-in-android/): The literal meaning of Context is: The Circumstances that form the setting for an Event, Statement, or Idea, and in terms of which it can be fully understood. Similarly when we talk about Android Programming Context can be understood as something which gives us the Context of the current state of our application. We can break the Context and its use into three major points: 
  - It allows us to access resources.
  - It allows us to interact with other Android components by sending messages.
  - It gives you information about your app environment.
- [Dispatchers.IO](https://kotlinlang.org/api/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-dispatchers/-i-o.html): Dispatchers.IO is a special coroutine dispatcher provided by Kotlin Coroutines designed for I/O operations like reading from or writing to a file, making network requests, or interacting with a database.
- 
viewmodelscope


# Terminology

- [Concurrency design pattern](https://www.baeldung.com/concurrency-principles-patterns): Concurrency design patterns are solutions for managing multiple operations that appear to run simultaneously, often in multi-threaded programs.

# Open question

- meaning of main() in kotlin 
- 