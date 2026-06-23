
### 13. - Android Development Guide
```markdown
# - Android Development Standards

## Android Architecture

### Recommended Architecture

app/
├── src/
│ ├── main/
│ │ ├── java/com/example/app/
│ │ │ ├── data/
│ │ │ │ ├── models/
│ │ │ │ ├── repositories/
│ │ │ │ └── datasources/
│ │ │ ├── domain/
│ │ │ │ ├── usecases/
│ │ │ │ └── entities/
│ │ │ ├── presentation/
│ │ │ │ ├── ui/
│ │ │ │ │ ├── activities/
│ │ │ │ │ ├── fragments/
│ │ │ │ │ └── adapters/
│ │ │ │ ├── viewmodels/
│ │ │ │ └── di/
│ │ │ └── utils/
│ │ └── res/
│ │ ├── drawable/
│ │ ├── layout/
│ │ ├── values/
│ │ └── menu/
│ └── androidTest/
│ └── java/com/example/app/
│ └── instrumentation/
└── build.gradle


### Architecture Patterns

**MVVM (Model-View-ViewModel)**
```kotlin
// ✅ Good: MVVM Architecture
// ViewModel
class UserViewModel(
    private val getUserUseCase: GetUserUseCase,
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    private val _user = MutableStateFlow<User?>(null)
    val user: StateFlow<User?> = _user.asStateFlow()

    private val _isLoading = MutableStateFlow(false)
    val isLoading: StateFlow<Boolean> = _isLoading.asStateFlow()

    fun loadUser(userId: String) {
        viewModelScope.launch {
            _isLoading.value = true
            try {
                val result = getUserUseCase(userId)
                _user.value = result.getOrNull()
            } catch (e: Exception) {
                // Handle error
            } finally {
                _isLoading.value = false
            }
        }
    }
}

// ✅ Good: Activity/Fragment
class UserActivity : AppCompatActivity() {
    private val viewModel: UserViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_user)

        viewModel.user.observe(this) { user ->
            user?.let { displayUser(it) }
        }

        viewModel.isLoading.observe(this) { isLoading ->
            showLoading(isLoading)
        }

        viewModel.loadUser(userId)
    }
}

Android Development Best Practices

Kotlin Standards

// ✅ Good: Kotlin best practices
// Use data classes for models
data class User(
    val id: String,
    val name: String,
    val email: String
)

// Use sealed classes for states
sealed class Resource<out T> {
    data class Success<T>(val data: T) : Resource<T>()
    data class Error(val message: String) : Resource<Nothing>()
    object Loading : Resource<Nothing>()
}

// Use extension functions
fun View.show() { visibility = View.VISIBLE }
fun View.hide() { visibility = View.GONE }

// Use coroutines for async
suspend fun fetchUser(id: String): User {
    return withContext(Dispatchers.IO) {
        apiService.getUser(id)
    }
}

// ✅ Good: Null safety
val user: User? = getUser()
user?.let { displayUser(it) } // Safe call

Dependency Injection

// ✅ Good: Dagger/Hilt
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    @Singleton
    fun provideRetrofit(): Retrofit {
        return Retrofit.Builder()
            .baseUrl(BuildConfig.API_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
}

// ✅ Good: Hilt injection in Activity
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    @Inject
    lateinit var userRepository: UserRepository

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Use injected dependencies
    }
}

Unit Testing
kotlin
// ✅ Good: Unit test with MockK
@Test
fun `test user loading`() = runTest {
    val mockUser = User("1", "John", "john@example.com")
    val useCase = mockk<GetUserUseCase>()
    coEvery { useCase("1") } returns Result.success(mockUser)

    val viewModel = UserViewModel(useCase)
    viewModel.loadUser("1")

    assertEquals(mockUser, viewModel.user.value)
}
UI Development
Material Design
xml
<!-- ✅ Good: Material Design components -->
<com.google.android.material.button.MaterialButton
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="Submit"
    android:backgroundTint="?attr/colorPrimary"
    app:cornerRadius="8dp" />

<com.google.android.material.textfield.TextInputLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="Email"
    app:startIconDrawable="@drawable/ic_email">
    <com.google.android.material.textfield.TextInputEditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:inputType="textEmailAddress" />
</com.google.android.material.textfield.TextInputLayout>

<com.google.android.material.card.MaterialCardView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:cardElevation="4dp"
    app:cardCornerRadius="8dp">
    <!-- Card content -->
</com.google.android.material.card.MaterialCardView>
Navigation
kotlin
// ✅ Good: Navigation Component
<fragment
    android:id="@+id/nav_host"
    android:name="androidx.navigation.fragment.NavHostFragment"
    app:defaultNavHost="true"
    app:navGraph="@navigation/nav_graph" />

// Navigate with safe args
findNavController().navigate(
    UserFragmentDirections.actionUserToDetails(userId)
)
Performance Optimization
List Performance
kotlin
// ✅ Good: RecyclerView optimization
class UserAdapter : RecyclerView.Adapter<UserViewHolder>() {
    private var items: List<User> = emptyList()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): UserViewHolder {
        return UserViewHolder(
            LayoutInflater.from(parent.context)
                .inflate(R.layout.item_user, parent, false)
        )
    }

    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        holder.bind(items[position])
    }

    override fun getItemCount() = items.size

    fun submitList(newItems: List<User>) {
        items = newItems
        notifyDataSetChanged()
    }
}
Memory Management
kotlin
// ✅ Good: Avoid memory leaks
// Use WeakReferences
private val listeners = mutableListOf<WeakReference<EventListener>>()

// ✅ Good: ViewModel for lifecycle-aware data
// ✅ Good: Use Application context not Activity context
// ✅ Good: Avoid static references to Activities
// ✅ Good: Use LeakCanary for detection
Security
Secure Data Storage
kotlin
// ✅ Good: Encrypted SharedPreferences
import androidx.security.crypto.EncryptedSharedPreferences
import androidx.security.crypto.MasterKeys

val masterKeyAlias = MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC)
val sharedPreferences = EncryptedSharedPreferences.create(
    "secure_prefs",
    masterKeyAlias,
    context,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
)

// ✅ Good: Store sensitive data in EncryptedSharedPreferences
sharedPreferences.edit()
    .putString("user_token", token)
    .apply()
Network Security
xml
<!-- ✅ Good: Network security config -->
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="false">
        <trust-anchors>
            <certificates src="system" />
        </trust-anchors>
    </base-config>
</network-security-config>
API Integration
Retrofit Setup
kotlin
// ✅ Good: Retrofit with error handling
interface ApiService {
    @GET("users/{id}")
    suspend fun getUser(@Path("id") id: String): Response<User>

    @POST("users")
    suspend fun createUser(@Body user: User): Response<User>
}

// ✅ Good: Error handling
suspend fun <T> safeApiCall(
    apiCall: suspend () -> Response<T>
): Resource<T> {
    return try {
        val response = apiCall()
        if (response.isSuccessful) {
            response.body()?.let {
                Resource.Success(it)
            } ?: Resource.Error("Empty response")
        } else {
            Resource.Error("Error: ${response.code()}")
        }
    } catch (e: Exception) {
        Resource.Error(e.message ?: "Network error")
    }
}
App Testing
UI Testing (Espresso)
kotlin
// ✅ Good: Espresso UI test
@RunWith(AndroidJUnit4::class)
@LargeTest
class LoginActivityTest {
    @get:Rule
    val activityRule = ActivityTestRule(LoginActivity::class.java)

    @Test
    fun testSuccessfulLogin() {
        onView(withId(R.id.emailInput))
            .perform(typeText("test@example.com"), closeSoftKeyboard())
        onView(withId(R.id.passwordInput))
            .perform(typeText("password123"), closeSoftKeyboard())
        onView(withId(R.id.loginButton)).perform(click())

        onView(withId(R.id.welcomeMessage))
            .check(matches(withText("Welcome, test@example.com!")))
    }
}
Instrumentation Testing
kotlin
// ✅ Good: Instrumentation test
@RunWith(AndroidJUnit4::class)
class DatabaseTest {
    private lateinit var database: AppDatabase
    private lateinit var userDao: UserDao

    @Before
    fun setup() {
        val context = ApplicationProvider.getApplicationContext<Context>()
        database = Room.inMemoryDatabaseBuilder(context, AppDatabase::class.java).build()
        userDao = database.userDao()
    }

    @Test
    fun testInsertUser() = runTest {
        val user = User("1", "John", "john@example.com")
        userDao.insert(user)
        val users = userDao.getAll()
        assertEquals(1, users.size)
        assertEquals(user, users[0])
    }
}
Build Configuration
Gradle Optimization
gradle
// ✅ Good: Build configuration
android {
    compileSdk 34

    defaultConfig {
        applicationId "com.example.app"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug {
            applicationIdSuffix ".debug"
            debuggable true
        }
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_17
        targetCompatibility JavaVersion.VERSION_17
    }
}

// ✅ Good: Dependency optimization
dependencies {
    implementation platform('androidx.compose:compose-bom:2024.04.01')
    implementation 'androidx.core:core-ktx:1.12.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.11.0'

    // Use strict versions
    implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3'
}
Android Checklist
App follows Material Design guidelines

All strings externalized (strings.xml)

Dark mode supported

Edge-to-edge display handling

Responsive layouts for different screen sizes

Permission handling (runtime permissions)

Offline support (caching)

Crash reporting (Firebase Crashlytics)

Analytics (Firebase Analytics)

ProGuard/R8 rules configured

Unit tests coverage ≥ 80%

UI tests for critical flows

Security best practices followed

API error handling

Loading states displayed

Accessibility (TalkBack, content descriptions)

App icon and splash screen

Deep linking configured

Notification support

Play Store listing prepared