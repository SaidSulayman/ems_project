# Google Maps API Key Configuration Guide

This guide will help you configure Google Maps API key for both Android and iOS platforms.

## Step 1: Get a Google Maps API Key

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select an existing one
3. Enable the following APIs:
   - **Maps SDK for Android**
   - **Maps SDK for iOS**
   - **Geocoding API** (optional, for address conversion)
   - **Directions API** (optional, for route planning)

4. Go to **Credentials** → **Create Credentials** → **API Key**
5. Copy your API key
6. (Recommended) Restrict your API key:
   - Click on the API key to edit it
   - Under **Application restrictions**, select:
     - **Android apps** for Android key
     - **iOS apps** for iOS key
   - Add your app's package name and SHA-1 certificate fingerprint (Android)
   - Add your app's bundle identifier (iOS)

## Step 2: Configure Android

### Option A: Using AndroidManifest.xml (Recommended)

1. Open `android/app/src/main/AndroidManifest.xml`
2. Add the API key inside the `<application>` tag:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.ems_app">
    
    <application>
        <!-- Add this meta-data tag inside <application> -->
        <meta-data
            android:name="com.google.android.geo.API_KEY"
            android:value="YOUR_ANDROID_API_KEY_HERE"/>
            
        <!-- Other application configurations -->
    </application>
</manifest>
```

### Option B: Using build.gradle (Alternative)

1. Open `android/app/build.gradle`
2. Add to `android` → `defaultConfig`:

```gradle
android {
    defaultConfig {
        // ... other config
        manifestPlaceholders = [
            GOOGLE_MAPS_API_KEY: "YOUR_ANDROID_API_KEY_HERE"
        ]
    }
}
```

3. Then in `AndroidManifest.xml`:

```xml
<meta-data
    android:name="com.google.android.geo.API_KEY"
    android:value="${GOOGLE_MAPS_API_KEY}"/>
```

### Required Permissions

Make sure these permissions are in `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

## Step 3: Configure iOS

### Option A: Using AppDelegate.swift

1. Open `ios/Runner/AppDelegate.swift`
2. Add the import and API key configuration:

```swift
import UIKit
import Flutter
import GoogleMaps

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    // Add this line with your iOS API key
    GMSServices.provideAPIKey("YOUR_IOS_API_KEY_HERE")
    
    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
}
```

### Option B: Using Info.plist

1. Open `ios/Runner/Info.plist`
2. Add the API key:

```xml
<key>GMSApiKey</key>
<string>YOUR_IOS_API_KEY_HERE</string>
```

### Required Permissions

Add location permissions to `ios/Runner/Info.plist`:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs location access to find nearby ambulances</string>
<key>NSLocationAlwaysUsageDescription</key>
<string>This app needs location access to track ambulance location</string>
```

## Step 4: Verify Configuration

1. Run the app:
```bash
flutter clean
flutter pub get
flutter run
```

2. Navigate to the tracking screen
3. You should see the Google Map with markers for ambulance and patient locations

## Troubleshooting

### Android Issues

**Error: "API key not found"**
- Verify the API key is correctly added in `AndroidManifest.xml`
- Make sure it's inside the `<application>` tag
- Check for typos in the API key

**Error: "This API project is not authorized to use this API"**
- Enable "Maps SDK for Android" in Google Cloud Console
- Wait a few minutes for changes to propagate

**Map shows blank/gray**
- Check internet connection
- Verify API key restrictions allow your app
- Check SHA-1 certificate fingerprint matches

### iOS Issues

**Error: "API key not found"**
- Verify the API key is set in `AppDelegate.swift` or `Info.plist`
- Make sure `GoogleMaps` is imported in `AppDelegate.swift`

**Error: "This API project is not authorized"**
- Enable "Maps SDK for iOS" in Google Cloud Console
- Verify bundle identifier matches restrictions

**Map doesn't load**
- Check location permissions are granted
- Verify API key restrictions allow your bundle ID

### Getting SHA-1 Certificate Fingerprint (Android)

For debug builds:
```bash
cd android
./gradlew signingReport
```

Look for `SHA1` under `Variant: debug`

For release builds, use your keystore:
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

## Security Best Practices

1. **Restrict API Keys**: Always restrict your API keys to specific apps
2. **Use Different Keys**: Use separate API keys for Android and iOS
3. **Set Usage Limits**: Set daily quotas to prevent abuse
4. **Monitor Usage**: Regularly check API usage in Google Cloud Console
5. **Don't Commit Keys**: Never commit API keys to version control
   - Add to `.gitignore`:
   ```
   android/app/src/main/AndroidManifest.xml
   ios/Runner/AppDelegate.swift
   ```
   - Use environment variables or secure storage instead

## Environment Variables (Advanced)

For better security, use environment variables:

1. Create `.env` file (add to `.gitignore`):
```
GOOGLE_MAPS_ANDROID_API_KEY=your_android_key_here
GOOGLE_MAPS_IOS_API_KEY=your_ios_key_here
```

2. Use `flutter_dotenv` package:
```yaml
dependencies:
  flutter_dotenv: ^5.1.0
```

3. Load in code:
```dart
import 'package:flutter_dotenv/flutter_dotenv.dart';

await dotenv.load(fileName: ".env");
String apiKey = dotenv.env['GOOGLE_MAPS_ANDROID_API_KEY']!;
```

## Additional Resources

- [Google Maps Flutter Plugin](https://pub.dev/packages/google_maps_flutter)
- [Google Maps Platform Documentation](https://developers.google.com/maps/documentation)
- [API Key Best Practices](https://developers.google.com/maps/api-security-best-practices)

