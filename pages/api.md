---
# permalink: /404.html
layout: single
classes: wide
title: "API"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-11-14
---

# Setup the detailed view model

0. Check the preview of the detailed view

1. Create a detailed view model
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
1. Create a new composible function in ContactsUI.kt and add it before
   @Composable
   fun ContactDetails(contact: Contact, modifier: Modifier = Modifier)

in ContactsUI.kt
```kotlin
@Composable
fun ContactDetailView(contactDetailViewModel: ContactDetailViewModel = viewModel(factory = AppViewModelProvider.Factory)) {

    // To read the contact we selected
//    val contact = contactDetailViewModel.
}
```

but sth is still missing, we cannot continue
On the top of the same file, we need to rename

```kotlin
@Composable
fun ContactsApp( modifier: Modifier = Modifier,
                 contactsViewModel: ContactsViewModel = viewModel(factory = AppViewModelProvider.Factory))   
// to
@Composable
fun ContactsListView( modifier: Modifier = Modifier,
                 contactsViewModel: ContactsViewModel = viewModel(factory = AppViewModelProvider.Factory))   
```

add a new composible function called ContactsApp() and before that create a enum class called Routes{}

```kotlin
// newly add
enum class Routes{
    List, 
    Detail
}

// newly add
@Composable
fun ContactsApp(
    modifier: Modifier = Modifier,
    navController: NavHostController = rememberNavController()
) {
    // if anything red above, add dependency and import

    NavHost(
        navController = navController, modifier = modifier, startDestination = Routes.List.name
    ) {
        composable(Routes.List.name) {
            ContactsListView()
        }
        composable(Routes.Detail.name) {
            ContactDetailView()
        }
    }
}

@Composable
fun ContactsListView( modifier: Modifier = Modifier,
                 contactsViewModel: ContactsViewModel = viewModel(factory = AppViewModelProvider.Factory))   
```

To make sure that we get the unique name, we need to pass id into route, called navigation archive?
- add a parameter to class Routes()
- change Routes.List.name -> Routes.List.route
- add a function parameter to ContactListView()
- update the parameter passes to ContactListView() in ContactsApp()

```kotlin
enum class Routes(val route: String) {
    List("list"),
    Detail("detail/{contactId}")
}

@Composable
fun ContactsApp(
    modifier: Modifier = Modifier,
    navController: NavHostController = rememberNavController()
) {
    // if anything red above, add dependency and import

    // change name to route
    NavHost(
        navController = navController, modifier = modifier, startDestination = Routes.List.route
    ) {
        composable(Routes.List.route) {
//            ContactsListView()           
            ContactsListView() 
            ContactsListView(){
                contactId -> navController.navigate("detail/$contactId")
            }                       
        }

        // to get contactId, we need to set the second parameter
        composable(Routes.Detail.route, listOf(navArgument("contactId"){
            type = NavType.IntType
        })) {
            ContactDetailView()
        }
    }
}

@Composable
fun ContactsListView(
    modifier: Modifier = Modifier,
    contactsViewModel: ContactsViewModel = viewModel(factory = AppViewModelProvider.Factory),
    onContactClick: (Int) -> Unit
)
```

- Go to fun ContactsListView()

```kotlin
        LazyColumn {
            itemsIndexed(state) { index, contact ->
                ContactListItem(contact, onCardClick = {
                    onContactClick(contact.id)
//                        contactsViewModel.onCardClick(index)
                })
            }
        }
```

- check contactId are the same in ContactsUI.kt and ContactDetailViewModel.kt
2. In ContactDetailViewModel.kt, add ContactDetailUiState()
   
in ContactDetailViewModel.kt
```kotlin
data class ContactDetailUiState(
    val contact: Contact
)

class ContactDetailViewModel(
    private val savedStateHandle: SavedStateHandle,
    private val repository: ContactRepository) : ViewModel() {

    // can use to read contactId
    // add to the nav controller
    private val contactId: Int = savedStateHandle["contactId"] ?: 0
    
    // newly add below
    private val _contactDetailUiState = MutableStateFlow(ContactDetailUiState(
        Contact(0,"", "", 0)
    ))
    val contactDetailUiState = _contactDetailUiState.asStateFlow()

    init{
        viewModelScope.launch {
            val contact = repository.findContactById(contactId)
            _contactDetailUiState.update {
                it.copy(contact = contact)
            }
        }
    }
}
```

3. Go to ContactRepository.kt and at the end of the file add findContactById()

```kotlin
    suspend fun addRandomContact() {
        contactsDao.addContact(
            ContactEntity(0, names.random(), "+4357894", 45)
        )
    }

    // newly add
    suspend fun findContactById(contactId: Int): Contact {
        val contactEntity = contactsDao.findContentById(contactId)
        return Contact(
            contactEntity.id, contactEntity.name, contactEntity.telephoneNumber, contactEntity.age
        )
    }
```

4. Go to ContactsUI.kt and update ContactDetailView()

```kotlin
@Composable
fun ContactDetailView(contactDetailViewModel: ContactDetailViewModel = viewModel(factory = AppViewModelProvider.Factory)) {

    // To read the contact we selected
    val state by contactDetailViewModel.contactDetailUiState.collectAsStateWithLifecycle()

    ContactDetails(state.contact)
}
```

Until now, the detailed view should be working. To test it, by default, it is using the Gesture Navigation Mode. This means that when using the smulator, we need to 
- Mouse: press mouse left button and swip from the right side of the emulator 
- Touch pad: press the touch pad and swip from the right side of the emulator (with one finger). Only works with Pixel 9a (API 36) on my machine, but Pixel 6a (API35) and Medium Phone API 36 do not work. No idea why.

# Connecting to the Internet

## Permissions

1. Go to app -> manifests, and add

```xml
<!-- Declare permission -->
<uses-permission android:name="android.permission.INTERNET" />
<application></application>
```

- [Retrofit](https://www.geeksforgeeks.org/android/introduction-retofit-2-android-set-1/): Retrofit is a type-safe HTTP client for Android, developed by Square. This is industry standard and recommended by Google.

  - GET: Retrieve data from the server.
  - POST: Create a new resource or submit data.
  - PUT: Update or replace an existing resource.
  - DELETE: Remove a resource.
  - Typical JSON API
  - https://my-json-server.typicode.com/GithubGenericUsername/find-your-pet/contacts 
  - https://my-json-server.typicode.com/GithubGenericUsername/find-your-pet/contacts/4

2. Go to Gradle script and add dependency

in Geadle script (module)
```kotlin
// on the top
plugins {    
    // ...
    id("org.jetbrains.kotlin.plugin.serialization") version "2.2.21"
}    

// at the bottom
dependencies {

    // room setup
    // ...

    // retrofit
    val retrofit_version = "3.0.0"
    implementation("com.squareup.retrofit2:converter-kotlinx-serialization:$retrofit_version")
    implementation("com.squareup.retrofit2:retrofit:$retrofit_version")
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.9.0")
}    
```

3. Create a new package under the main pacakage called remote and under the package create a new kotlin class called ContactDto.kt
   
```kotlin
package at.uastw.contactsapp.remote

import kotlinx.serialization.Serializable

@Serializable
data class ContactDto (
    val id: Int,
    val name: String,
    val telephoneNumber: Int,
    val age: Int
)
```

4. Create an interface under remote called ContactRemoteService.kt

in ContactRemoteService.kt
```kotlin
package at.uastw.contactsapp.remote

import retrofit2.http.Body
import retrofit2.http.GET
import retrofit2.http.POST
import retrofit2.http.Path

interface ContactRemoteService {

    @GET("contacts")
    suspend fun getAllContacts(): List<ContactDto>

    @GET("contacts/{contactId}")
    suspend fun getAllContactById(@Path("contactId") id: Int): ContactDto

    @POST("contacts")
    suspend fun addContact(@Body contactDto: ContactDto)
}
```

in ContactRepository.kt
```kotlin
// update
// class ContactRepository(private val contactsDao: ContactsDao) {...}
class ContactRepository(
    private val contactsDao: ContactsDao,
    private val contactRemoteService: ContactRemoteService
) {

    // newly add on top
    suspend fun loadInitialContacts(){
        val remoteContacts = contactRemoteService.getAllContacts()
        
        // map the data from the Internet to our database
        remoteContacts.map{
            dto ->
            ContactEntity(0,dto.name, dto.telephoneNumber.toString(),dto.age,)
        }.forEach{
            entity -> contactsDao.addContact(entity)        
        }
    }

    // ...
}
```

5. Until here, if we run the app, it will crash because ContactRepository(contactsDao) in ContactsApplication.kt is not correct.

in ContactsApplication.kt
```kotlin
class ContactsApplication : Application() { // only one instance

    // by lazy: only execute this code once, once it is accessed somewhere
    val contactRepository by lazy {
        val contactsDao = ContactsDatabase.getDatabase(this).contactsDao()

        // setup a retrofit service
        val retrofit = Retrofit.Builder()
            .baseUrl("https://my-json-server.typicode.com/GithubGenericUsername/find-your-pet/")
            // without contacts because we added it in the ContactRemoteService
            .addConverterFactory(Json { ignoreUnknownKeys = true }.asConverterFactory("application/json".toMediaType()))
            // do the serialization
            .build()

        // Create an object for us that implements this interface
        val contactRemoteService = retrofit.create(ContactRemoteService::class.java)
        
        ContactRepository(contactsDao, contactRemoteService)
    }
}
```

6. Now we need to use this loadInitialContacts() in the ContactsViewModel

in ContactsViewModel.kt
```kotlin
    val contactsUiState = repository.contacts.stateIn(
        viewModelScope,
        SharingStarted.WhileSubscribed(5000),
        // when it should start the state, and how long it should keep it
        // it will remain 5 sec after the viewmodel scope is closed
        emptyList() // Define the initial value
    )

    // newly add
    // only call for the first time
    init {
        viewModelScope.launch {
            repository.loadInitialContacts()
        }
    }
```

Until now, the json file should be appended after the existing data.

Error may show up: apk-build-fails-with-error-failed-to-open-apk-inconsistent-information
Solution: File -> Invalidate caches & restart
This is because Android Studio may keep some information to speed up the build process.

# Terminology

- [API](): An application programming interface (API) is a connection between computers or between computer programs. It is a type of software interface.

- [Data Transfer Object (DTO)](https://www.baeldung.com/java-dto-pattern): DTOs are a crucial design pattern used to effectively transmit data from one layer to another while keeping the required data pieces in a lightweight structure.

# Reflection

- One can move database package and remote package under the data pacakage

# Open Questions