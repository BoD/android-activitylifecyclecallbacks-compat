Android ActivityLifecycleCallbacks Compatibility Library
========================================================

A 'compatibility' version of the ActivityLifecycleCallbacks APIs (http://developer.android.com/reference/android/app/Application.ActivityLifecycleCallbacks.html)
that were introduced in Android 4 (API Level 14).

This library works on Android 1+.

Goal
----

Sometimes it is useful to execute a piece of code on certain life cycle methods of all your activities.

For instance the Google Analytics library requires you to call a specific method in `onStart` and `onStop` of all your activities.

One way to do this is to have a base activity that does the job in `onStart` and `onStop` and then have all your activities extend it.
But this is not ideal - if you use several libraries like this, your activity hierarchy could start to get deep (and 
hard to read / maintain).

This is why the ActivityLifecycleCallbacks APIs were introduced in Android 4.

Basically you call `registerActivityLifecycleCallbacks` on your `Application`, passing an `ActivityLifecycleCallbacks`
and the system will call the methods following the life cycle of all the activities.

This library allows you to use these APIs in versions of Android lower than 4.

What's the catch?
-----------------

Unfortunately there is no way to intercept the activity life cycle methods except by having your activities extend some base 
class from this library.

*"So what's the point?"*, you may ask.

1. This library provides base 'instrumented' versions of the most commonly used `Activity` flavors: `Activity`, `FragmentActivity`, `ListActivity`, etc.
2. The API is basically the same as the one introduced in Android 4, so when you decide to drop support for older versions of the platform, you won't have to rewrite code: you can just remove this library and replace a few lines of code.

How to use
----------

This is an Android library project, you have to add it as a dependency to your project (please
see http://developer.android.com/guide/developing/projects/projects-eclipse.html#ReferencingLibraryProject to
know how to do that.)

Once you have done that, you simply need to call `ApplicationHelper.registerActivityLifecycleCallbacks(Application, ActivityLifecycleCallbacksCompat)`
instead of calling `Application.registerActivityLifecycleCallbacks(ActivityLifecycleCallbacks)` (which exists only since Android 4).

Then you need to have all your activities extend one of the base activities in the `org.jraf.android.util.activitylifecyclecallbackscompat.app` package.
For instance instead of:
```java
    public class MainActivity extends Activity {
```
    
Use:
```java
    public class MainActivity extends LifecycleDispatchActivity {
```

An example application is available in the `example-project` folder.

Using ActionBarSherlock or a similar library
--------------------------------------------

If you use a library that already requires your activities to extend a base class (e.g. ActionBarSherlock),
simply create your own base activity using `LifecycleDispatchActivity.java` as a starting point.
Here is an example for the ActionBarSherlock library:
```java
    public class MyBaseActivity extends SherlockFragmentActivity {
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityCreated(this, savedInstanceState);
        }
    
        @Override
        protected void onStart() {
            super.onStart();
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityStarted(this);
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityResumed(this);
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityPaused(this);
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityStopped(this);
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivitySaveInstanceState(this, outState);
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            if (ApplicationHelper.PRE_ICS) MainLifecycleDispatcher.get().onActivityDestroyed(this);
        }
    }
```

Licence
-------

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
