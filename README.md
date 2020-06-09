# Android's MVVM Architecture in Kotlin ft. Retrofit

This is an example to demonstrate **MVVM Architecture in Kotlin with Retrofit** in Android.

This example will demonstrate the working of MVVM using Live data and Retrofit in Kotlin. Just follow the steps and you will be able to try out the same in your Android Studio as well.

So Let's Get Started:

1. What is MVVM, LiveData, ViewModel, Model, Repository?
2. Implementation Step-by-Step
3. Conclusion

## 1. What is MVVM, LiveData, ViewModel, Model, Repository?

**Answer:** Let's see what are the important concepts in MVVM.

**MVVM:** Model-View-ViewModel (i.e MVVM) is a template of a client application architecture, proposed by John Gossman as an alternative to MVC and MVP patterns when using Data Binding technology. Its concept is to separate data presentation logic 
from business logic by moving it into particular class for a clear distinction.

**LiveData:** LiveData is an observable data holder class. Unlike a regular observable, LiveData is lifecycle-aware, meaning it respects the lifecycle of other app components, such as activities, fragments, or services. This awareness ensures LiveData only updates app component observers that are in an active lifecycle state.

## Advantages of Using LiveData:
**Ensures your UI matches your data state:** LiveData follows the observer pattern. LiveData notifies Observer objects when the lifecycle state changes. You can consolidate your code to update the UI in these Observer objects. Instead of updating the UI every time the app data changes, your observer can update the UI every time there's a change. 

**No memory leaks:**  Observers are bound to Lifecycle objects and clean up after themselves when their associated lifecycle is destroyed. 

**No crashes due to stopped activities:** If the observer's lifecycle is inactive, such as in the case of an activity in the back stack, then it doesn't receive any LiveData events. 

**No more manual lifecycle handling:** UI components just observe relevant data and don't stop or resume observation. LiveData automatically manages all of this since it's aware of the relevant lifecycle status changes while observing. 

**Always up to date data:** If a lifecycle becomes inactive, it receives the latest data upon becoming active again. For example, an activity that was in the background receives the latest data right after it returns to the foreground. 

**Proper configuration changes:** If an activity or fragment is recreated due to a configuration change, like device rotation, it immediately receives the latest available data. 

**Sharing resources:** You can extend a LiveData object using the singleton pattern to wrap system services so that they can be shared in your app. The LiveData object connects to the system service once, and then any observer that needs the resource can just watch the LiveData object. For more information, see Extend LiveData.

**ViewModel:** The ViewModel class is designed to store and manage UI-related data in a lifecycle conscious way. The ViewModel class allows data to survive configuration changes such as screen rotations.

**Model:** Model can be applied to a class which represents your application's data model, and will cause instances of the class to become observable, such that a read of a property of an instance of this class during the invocation of a composable function will cause that component to be "subscribed" to mutations of that instance. Composable functions which directly or indirectly read properties of the model class, the composables will be recomposed whenever any properties of the the model are written to.

**Repository:** Repository modules handle data operations. They provide a clean API so that the rest of the app can retrieve this data easily. They know where to get the data from and what API calls to make when data is updated. You can consider repositories to be mediators between different data sources, such as persistent models, web services, and caches.

## 2. Implementation Step-by-Step?
As said before, this example uses MVVM with Retrofit using Kotlin. Let's dive into the steps of doing it.

### **Step1:** Add dependencies to your project:

```xml
dependencies {
...
...
    def lifecycle_version = "2.2.0"
    // - - ViewModel
    implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycle_version"
    // - - LiveData
    implementation "androidx.lifecycle:lifecycle-livedata-ktx:$lifecycle_version"

    // - - Retrofit2
    def retrofit_version = "2.9.0"
    def logging_version = "4.3.1"
    implementation "com.squareup.retrofit2:retrofit:$retrofit_version"
    implementation "com.squareup.retrofit2:converter-gson:$retrofit_version"
    implementation "com.squareup.okhttp3:okhttp:$logging_version"
    implementation "com.squareup.okhttp3:logging-interceptor:$logging_version"

    // - - WP7 Progress Bar
    implementation 'com.github.shadowalker77:wp7progressbar:1.0.5'
...
...
}
```


### **Step2:** Create different folders that relate to MVVM:

<img src="https://i.ibb.co/Tm3zPDs/Screenshot-2020-06-04-at-11-16-43-PM.png" />

 
### **Step3:** Design your MainActivity which should look like this:
 
 ```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".view.MainActivity">

    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/btnClick"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="Click to Start"
        android:textColor="#000000"
        android:textSize="15sp"
        android:layout_margin="15dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ir.alirezabdn.wp7progress.WP7ProgressBar
        android:id="@+id/wp7progressBar"
        android:layout_width="0dp"
        android:layout_height="10dp"
        android:layout_centerVertical="true"
        app:animationDuration="2300"
        app:indicatorColor="@color/colorPrimary"
        app:indicatorHeight="7"
        app:indicatorRadius="5"
        app:interval="100"
        android:layout_marginStart="10dp"
        android:layout_marginEnd="10dp"
        app:layout_constraintBottom_toTopOf="@+id/lblYourResponseHere"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/btnClick" />

    <androidx.appcompat.widget.AppCompatTextView
        android:id="@+id/lblYourResponseHere"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Your Response Here"
        android:layout_marginBottom="30dp"
        android:textSize="20sp"
        android:textColor="#000000"
        app:layout_constraintBottom_toTopOf="@+id/lblResponse"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

    <androidx.appcompat.widget.AppCompatTextView
        android:id="@+id/lblResponse"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="- - -"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

### **Step4:** Now let's create few singleton classes:

In Kotlin, Singletons are very easy to create they just use a keyword called **object** before the class name. Check the code below

**a. Retrofit Singleton**

```kotlin
package com.example.mvvmkotlinexample.retrofit

import com.example.mvvmkotlinexample.BuildConfig
import okhttp3.OkHttpClient
import okhttp3.logging.HttpLoggingInterceptor
import okhttp3.logging.HttpLoggingInterceptor.Level
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

object RetrofitClient {

    const val MainServer = "http://api.drfriday.in/api/user/"

    val retrofitClient: Retrofit.Builder by lazy {

        val levelType: Level
        if (BuildConfig.BUILD_TYPE.contentEquals("debug"))
            levelType = Level.BODY else levelType = Level.NONE

        val logging = HttpLoggingInterceptor()
        logging.setLevel(levelType)

        val okhttpClient = OkHttpClient.Builder()
        okhttpClient.addInterceptor(logging)

        Retrofit.Builder()
            .baseUrl(MainServer)
            .client(okhttpClient.build())
            .addConverterFactory(GsonConverterFactory.create())
    }

    val apiInterface: ApiInterface by lazy {
        retrofitClient
            .build()
            .create(ApiInterface::class.java)
    }
}
```

**b. Repository Singleton**

```kotlin
package com.example.mvvmkotlinexample.repository

import android.util.Log
import androidx.lifecycle.MutableLiveData
import com.example.mvvmkotlinexample.model.ServicesSetterGetter
import com.example.mvvmkotlinexample.retrofit.RetrofitClient
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response

object MainActivityRepository {

    val serviceSetterGetter = MutableLiveData<ServicesSetterGetter>()

    fun getServicesApiCall(): MutableLiveData<ServicesSetterGetter> {

        val call = RetrofitClient.apiInterface.getServices()

        call.enqueue(object: Callback<ServicesSetterGetter> {
            override fun onFailure(call: Call<ServicesSetterGetter>, t: Throwable) {
                // TODO("Not yet implemented")
                Log.v("DEBUG : ", t.message.toString())
            }

            override fun onResponse(
                call: Call<ServicesSetterGetter>,
                response: Response<ServicesSetterGetter>
            ) {
                // TODO("Not yet implemented")
                Log.v("DEBUG : ", response.body().toString())

                val data = response.body()

                val msg = data!!.message

                serviceSetterGetter.value = ServicesSetterGetter(msg)
            }
        })

        return serviceSetterGetter
    }
}
```

### **Step5:** Next step is to create the Model class:

```kotlin
package com.example.mvvmkotlinexample.model

data class ServicesSetterGetter (
    val message: String? = null
)
```

### **Step6:** Next we create ApiInterface for the APIs:

```kotlin
package com.example.mvvmkotlinexample.retrofit

import com.example.mvvmkotlinexample.model.ServicesSetterGetter
import retrofit2.Call
import retrofit2.http.GET

interface ApiInterface {

    @GET("services")
    fun getServices() : Call<ServicesSetterGetter>

}
```

### **Step7:** Next and very important step is to have a ViewModel in the project:

```kotlin
package com.example.mvvmkotlinexample.viewmodel

import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel
import com.example.mvvmkotlinexample.model.ServicesSetterGetter
import com.example.mvvmkotlinexample.repository.MainActivityRepository

class MainActivityViewModel : ViewModel() {

    var servicesLiveData: MutableLiveData<ServicesSetterGetter>? = null

    fun getUser() : LiveData<ServicesSetterGetter>? {
        servicesLiveData = MainActivityRepository.getServicesApiCall()
        return servicesLiveData
    }

}
```

### **Step8:** Finally, we code the MainActivity kotlin file:

```kotlin
package com.example.mvvmkotlinexample.view

import android.content.Context
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import androidx.lifecycle.Observer
import androidx.lifecycle.ViewModelProvider
import com.example.mvvmkotlinexample.R
import com.example.mvvmkotlinexample.viewmodel.MainActivityViewModel
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    lateinit var context: Context

    lateinit var mainActivityViewModel: MainActivityViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        context = this@MainActivity

        mainActivityViewModel = ViewModelProvider(this).get(MainActivityViewModel::class.java)

        btnClick.setOnClickListener {

            wp7progressBar.showProgressBar()

            mainActivityViewModel.getUser()!!.observe(this, Observer { serviceSetterGetter ->

                wp7progressBar.hideProgressBar()

                val msg = serviceSetterGetter.message

                lblResponse.text = msg

            })

        }

    }
}
```

For any clarifications please refer to the repository.


## **Conclusion**

The goal of the MVVM using Kotlin and Retrofit is to acheive the best possible solution and save development time by using the best architectural pattern suggested by Google.

I hope it will help you too.
