# Android's MVVM Architecture in Kotlin ft. Retrofit

This is an example to demonstrate **MVVM Architecture in Kotlin with Retrofit** in Android. I

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

