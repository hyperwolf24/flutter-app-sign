# flutter-app-sign

#Keys for signing

Mac / Linux

```bash
keytool -genkey -v -keystore ~/upload-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```

Windows

```bash
keytool -genkey -v -keystore %userprofile%\upload-keystore.jks -storetype JKS -keyalg RSA -keysize 2048 -validity 10000 -alias upload
```

Note: The keytool command might not be in your path—it’s part of Java, which is installed as part of Android Studio.
For the concrete path, run flutter doctor -v and locate the path printed after ‘Java binary at:’.
Then use that fully qualified path replacing java (at the end) with keytool.
If your path includes space-separated names, such as Program Files, use platform-appropriate notation for the names.
For example, on Mac/Linux use Program\ Files, and on Windows use "Program Files".

# key store

Create a file named [project]/android/key.properties that contains a reference to your keystore.

```bash
storePassword=<password-from-previous-step>
keyPassword=<password-from-previous-step>
keyAlias=upload
storeFile=<keystore-file-location>
```

Don’t include the angle brackets (< >).

# Gradle configuration

Configure gradle to use your upload key when building your app in release mode by editing the [project]/android/app/build.gradle file.

```gradle
def keystoreProperties = new Properties()
   def keystorePropertiesFile = rootProject.file('key.properties')
   if (keystorePropertiesFile.exists()) {
       keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
   }
```

Add buildtypes for release (remove the debug from signingConfigs.debug)

```gradle
signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
   }
   buildTypes {
       release {
           signingConfig signingConfigs.release
       }
   }
```

# Finally run the following

To generate a clean build

```bash
flutter clean
```

Once done based on apk or app bundle follow these steps

APK

```bash
flutter build apk --split-per-abi
```

App bundle

```bash
flutter build appbundle --release
```

(The ```bash flutter build``` command defaults to --release.)
