
# ClearQuote Android SDK integration

### Steps to integrate the SDK inside an Android App

**1. In the Android app source code in build.gradle (app level) set minSDK to 23**
```javascript
android {
    compileSdk 31

    defaultConfig {
        ...
        minSdk 23 // Set minSDK to 23 
        ...
    }
    buildTypes {
       ...
    }
    compileOptions {
       ...
    }
    kotlinOptions {
        ...
    }
```


**2. Add jitpack in settings.gradle (Project settings) file in**
```javascript
dependencyResolutionManagement object
pluginManagement {
    repositories {
        ...
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' } // Add Jitpack
    }
}
rootProject.name = "<your app project name>"
include ':app'
```


**2. If It is a older project(without settings.gradle) then, Add jitpack in build.gradle (Project level)**
```javascript
// Top-level build file where you can add configuration options common to all sub-projects/modules.
buildscript {
   repositories {
       mavenLocal()
       google()
       mavenCentral()
   }
   dependencies {
       classpath 'com.android.tools.build:gradle:7.1.3'
       classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.31"

       maven { url 'https://jitpack.io' } // Add jitpack

       // NOTE: Do not place your application dependencies here; they belong
       // in the individual module build.gradle files
   }
}

task clean(type: Delete) {
   delete rootProject.buildDir
}
```

 
**3. Add the ClearQuote Android SDK library in the build.gradle (app level)**
```javascript
dependencies {
    ...

    // CQ SDK
    implementation 'com.github.clearquotetech:cq-android-sdk:<Latest version>' 
    // Current latest version is 1.0.0-beta.4
}

```

**4. Sync the project**

![press the android studio button present in the top right cornor](https://github.com/clearquotetech/cq-android-sdk/blob/main/Resized%20assets/gradle-sync-project.png)


**5. Use this starter code in your activity’s onCreate method**
```javascript
class MainActivity : AppCompatActivity() {
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       setContentView(R.layout.activity_main)

       // Instantiate the CQSDKInitializer class
       val cqSDKInitializer = CQSDKInitializer(this)

       // Check if SDK is initialized or not if not
       if (cqSDKInitializer.isCQSDKInitialized()){
           // Start the inspection flow
           cqSDKInitializer.startInspection(
               activityContext = this,
               result = { started ->
                   // Handle the response
               }
           )
       }else{
           // Initialize the SDK
            cqSDKInitializer.initSDK(
                // TODO Add the SDK key given by CQ
                token = "<SDK Key>",
                // TODO Add the Region here
                region = UAT,
                result = { initialized, code, message ->
                    if (code == 200){
                        // Start the inspection
                        cqSDKInitializer.startInspection(
                            activityContext = this,
                            result = { started ->
                                // Handle the response
                            }
                        )
                    }
                }
            )
       }
   }
}
```
## CQSDKInitializer class public methods
1. **isCQSDKInitialized():**
It returns true if SDK is already initialized, and false otherwise

2. **initSDK(sdkKey: String, region: String, result: (initialized: Boolean, code: Int, message: String) -> Unit):**
It initializes the SDK, It should be called only when SDK is not already initialized
This method provides a callback function which can be used to listen the SDK initialization updates Parameters description is as follows
    - **sdkKey**: SDK key provided by the ClearQuote team at the start of SDK integration
    - **region**: The region in which ClearQuote SDK needs to be operated, value should be picked from io.clearquote.assessment.cq_sdk.support.Constants class only Relevant region will be provided by the ClearQuote team at the start of SDK integration
    - **result**: It is a callback function in return it gives following parameters
        - **initialized**: Returns true if SDK initialized successfully, false otherwise
        - **code**: Response code from the SDK initialization
            - 100 Internal SDK error
            - 400 Bad request
            - 500 Internal server error
            - 200 SDK initialized successfully
            - 422 Invalid SDK Key
        - **message**: Error description

3. **startInspection(activityContext : Context, result: (inspectionStarted: Boolean) -> Unit):**
This functions starts the inspection flow in the app
    - **activityContext**: Context of the activity from which the inspection flow is supposed to start
    - **result**: It is a callback function parameter description is as follows
        - **inspectionStarted**: True if inspection flow is started successfully false otherwise
    

## Sample screenshots available from SDK integration:
#### **1. Input page to start a vehicle inspection**

![Input page](https://github.com/clearquotetech/cq-android-sdk/blob/main/Resized%20assets/Input%20page.jpg)
#### **2. Guided flow for capture of images for a vehicle - number of overlays with the sequence order of overlays with each overlay outline is configurable. If real-time check of all the captured images are required, that is also configurable**
![Guided flow](https://github.com/clearquotetech/cq-android-sdk/blob/main/Resized%20assets/guided%20flow.jpg)

#### **3. Images of a vehicle uploaded**
![Image uploads section](https://github.com/clearquotetech/cq-android-sdk/blob/main/Resized%20assets/upload%20images.jpg)

#### **4. Inspection checklist (optional - configurable checklist with sections and questions to be answered during vehicle inspection)**
![Inspection checklist](https://github.com/clearquotetech/cq-android-sdk/blob/main/Resized%20assets/checklist.jpg)
## Github sample
###### [Github project](https://github.com/clearquotetech/cq-android-sdk-integration-sample) where SDK has been integrated
