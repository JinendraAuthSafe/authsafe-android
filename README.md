authsafe-android
================

**Description :-**

AuthSafe sdk helps us to detect suspicious activities in real time and provides device fingerprinting, behavioral analysis, and client-side event monitoring. 

**Library size and method count :**
| Total AAR Size in authsafe-release.aar  | 0.031 mb   | 
| :---:   | :---: | 
| Total methods in authsafe-release.aar  | 248   |
| Total classes in authsafe-release.aar | 61    |

**Configuretion Requirements:-**

* minSdk version = 21 
* Property Id and Property Secret([Authsafe Dashboard](https://authsafe.ai/)) 
* Permission
  * Internet
  * Location (ACCESS_FINE_LOCATION,ACCESS_COARSE_LOCATION) //optional

**Getting Start:-**

**Step 1:**  Add the JitPack repository to your build file

```gradle

allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
  ```
**Step 2:** Add the dependency

```gradle
dependencies {
	        implementation 'com.authsafe.authsafe:authsafe-android:1.0.2'
	}
  ```
**Step 3:** Define secret key in your string file
  ```
           <string name="auth_secret" translatable="false">Property Id:Property Secret</string>
      E.g. <string name="auth_secret" translatable="false">9153******208592:OKOw7dV****O874a</string>

  ```
  
**Step 4:** Add secret key in your manifest file
  ```
  <meta-data 
    android:name="auth_safe_secret_key" 
    android:value="@string/auth_secret" /> 
  ```
  
Implementation
--------------
  // Place the below in your application class onCreate method 
```
Java

AuthSafe.configure(this); 

Kotlin

AuthSafe.configure(this) 

//this is application class
E.g.   override fun onCreate() {
        super.onCreate()

        AuthSafe.configure(this)
        OR
        AuthSafe.configure(this,"9153477437238592:OKOw7dVPVWdO874a"); // If you are using this one, don't need to define secret kes in Manifest file
   }
```
The AuthSafe SDK collects _Device fingerprints_ from the device and sends this data directly to the Authsafe Dashboard.  

Event Monitoring
----------------

**1. Screen Change Event: -** In the screen change event, AuthSafe tracks every screen transaction between the screen open to close. 

For Activity: - Once you initialize the AuthSafe’s onCreate method in your application, the activity tracking starts automatically and tracks the whole lifecycle of the activity.  

For Fragment:-For fragment tracking you need to call a method in your root fragment activity. 

```
Java

  AuthSafeFragmentLifecycleCallbacks.trackFragmentChange(this); 

  //It's required higher Sdk version 8 (Orio) 

  if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { 
      AuthSafeFragmentLifecycleCallbacks.trackFragmentChange(this); 
  } 

Kotlin

  AuthSafeFragmentLifecycleCallbacks.trackFragmentChange(this)

  //It's required higher Sdk version 8 (Orio) 

  if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { 
      AuthSafeFragmentLifecycleCallbacks.trackFragmentChange(this)
  } 

```
**2. Click Events: -** AuthSafe SDK tracks each click event.

For Activity:- You have to override the below method in your every activity or BaseActivity and if you don't want to track click event for a particular activity then just skip this. 

```
Java

  @Override 
  public boolean onTouchEvent(MotionEvent event) { 
      AuthSafe.trackClickEvent(event); 
      return true; 
  } 

Kotlin

    override fun onTouchEvent(event: MotionEvent?): Boolean {
        AuthSafe.trackClickEvent(event)
        return super.onTouchEvent(event)
    }

```

For Fragment:- You have to call the below method in your every fragment or BaseFragment and if you don't want to track click event for a particular fragment then just skip this.

```
Java

  AuthSafe.trackClickEvent(rootview); 
  
Kotlin

  AuthSafe.trackClickEvent(rootview)

```

For View (Button, Image, etc..):- On view click you need call below method. 

```
Java

  AuthSafe.trackClickEvent(); 
  
 Kotlin 
  
    AuthSafe.trackClickEvent()
    
 E.g. button?.setOnClickListener(View.OnClickListener {
            AuthSafe.trackClickEvent()
        })
```

For Recyclerview:- 

```
Java

  AuthSafe.trackClickEvent(holder.itemView); 
  
Kotlin

  AuthSafe.trackClickEvent(holder.itemView)

```

**2. Location Event: -** This event is useful for fast geo-track location. In this event you need to get permission from the user, then you need to call the below method.  
Permission required for this event - ACCESS_FINE_LOCATION,ACCESS_COARSE_LOCATION 

```
Java 
  //Location object
  AuthSafe.trackLocation(location);

Kotlin 
  //Location object
  AuthSafe.trackLocation(location)
  
E.g. private fun locationTracking() {
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED
            && ContextCompat.checkSelfPermission(this,Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
            // Permission is not granted
            ActivityCompat.requestPermissions(this,arrayOf(Manifest.permission.ACCESS_FINE_LOCATION,Manifest.permission.ACCESS_COARSE_LOCATION),REQUEST_CODE)
        } else {
            val providers: List<String> = locationManager.getAllProviders()
            var bestLocation: Location? = null
            for (provider in providers) {
                val location: Location = locationManager.getLastKnownLocation(provider)
                if (location == null) {
                    continue
                }
                if (bestLocation == null || location.accuracy < bestLocation.accuracy) {
                    bestLocation = location
                }
            }
            if (bestLocation == null) {
                Toast.makeText(this, "Unable to find location.", Toast.LENGTH_SHORT).show()
            }
            else
            AuthSafe.trackLocation(bestLocation)
        }
    }

```

**4. Login Event: -** For tracking login event you need to send the authafe token in your login request. 

```
Java
  //For form body
  key = "request_token" 
  value = AuthSafe.getRequestToken()); 

  //For raw data
  jsonObject.addProperty("request_token", AuthSafe.getRequestToken()); 

Kotlin
  //For form body 
  key = "request_token"  
  value = AuthSafe.getRequestToken()) 

  //For raw data 
  jsonObject.addProperty("request_token", AuthSafe.getRequestToken()) 
```

Error may occur by authsafe:- 
----------------------------

* E/AuthSafe: Please check your Network: - When the internet is not available.  
* E/AuthSafe: Provide a valid AuthSafe Secret and Property id while initializing the SDK :- If secret and property id not delayered in manifest file. 
* E/AuthSafe: Failed to load meta-data, please check your secret and property id, NullPointer:- If secret and property is null or manifest not able load meta data. 
* E/AuthSafe: Failed to load meta-data, NameNotFound:- If key is wrong in manifest file use this key for declaring secret id and property -  “auth_safe_secret_key”. 
* E/AuthSafe: Provide a valid AuthSafe Secret and property id while initializing the SDK: - If secret and property is wrong in that case, please contact us or generate a new key using authsafe dashboard. 
* E/AuthSafe: AuthSafe SDK must be configured before calling this method: - If authsafe not initialize in application class (Authsafe.configure(this)). 
* E/AuthSafe: Device Information Logged Failed: - Device fingerprints not logged to server there might be connection timeout or server error. Please try again if you get the error back-to-back contact us.  
* E/AuthSafe: Error Message >> Token expired: - If device token in expired (current limit is 12 hours). 
* E/AuthSafe: Impossible to connect to Geocoder: -  Authsafe getting Location object null or not able to get the city, state, region or pin from location. 
* E/AuthSafe: Failed to create queue: Try to open again if you are still getting error, please contact us.  


Thank you!!
Copyright@Authsafe.ai



