## Getting your device certified through Google Play developers portal

With Bliss OS builds that require users to flsh their own gapps package, users will need to register their device for Google Play developer certification in order to use Google Play Store and GMS. 
Follow the steps below to complete that process:

 1) Grant Termux SU from KernelSU. then open it up and run this command:
 ```
su
ANDROID_RUNTIME_ROOT=/apex/com.android.runtime ANDROID_DATA=/data ANDROID_TZDATA_ROOT=/apex/com.android.tzdata ANDROID_I18N_ROOT=/apex/com.android.i18n sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
 ```
 
 2) The output should be a string of numbers. Copy that and paste it into https://www.google.com/android/uncertified 

 3) Wait a few minutes, then reboot the device
