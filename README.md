## Document Deeplink WAODATE

### Description:
- Tài liệu report về cách cài đặt môi trường, cách viết code và cách test

### Lib:

```dart
uni_links: ^0.5.1
```

### Installation:

#### For Android

- **You need to declare at least one of the two intent filters in ***android/app/src/main/AndroidManifest.xml***:

- replace "[YOUR_SCHEME]" by http or https
- replace "[YOUR_HOST]" by waodate.com

```xml
<manifest ...>
  <!-- ... other tags -->
  <application ...>
    <activity ...>
      <!-- ... other tags -->

      <!-- Deep Links -->
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with YOUR_SCHEME://YOUR_HOST -->
        <data
          android:scheme="[YOUR_SCHEME]"
          android:host="[YOUR_HOST]" />
      </intent-filter>

      <!-- App Links -->
      <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- Accepts URIs that begin with https://YOUR_HOST -->
        <data
          android:scheme="https"
          android:host="[YOUR_HOST]" />
      </intent-filter>
    </activity>
  </application>
</manifest>
```

#### For iOS

**Creating the entitlements file in Xcode:**

- Open up Xcode by double-clicking on your ios/Runner.xcworkspace file.
- Go to the Project navigator (Cmd+1) and select the Runner root item at the very top.
- Select the Runner target and then the Signing & Capabilities tab.
- Click the + Capability (plus) button to add a new capability.
- Type 'associated domains` and select the item.
- Double-click the first item in the Domains list and change it from webcredentials:example.com to: applinks: + your host (ex: my-fancy-domain.com).
- A file called Runner.entitlements will be created and added to the project.
- :rocket: :rocket: :rocket: Done.

**For Custom URL schemes you need to declare the scheme in ios/Runner/Info.plist (or through Xcode's Target Info editor, under URL Types):**

- replace "[ANY_URL_NAME]" by http or https
- replace "[YOUR_HOST]" by waodate.com

```plist
<?xml ...>
<!-- ... other tags -->
<plist>
<dict>
  <!-- ... other tags -->
  <key>CFBundleURLTypes</key>
  <array>
    <dict>
      <key>CFBundleTypeRole</key>
      <string>Editor</string>
      <key>CFBundleURLName</key>
      <string>[ANY_URL_NAME]</string>
      <key>CFBundleURLSchemes</key>
      <array>
        <string>[YOUR_SCHEME]</string>
      </array>
    </dict>
  </array>
  <!-- ... other tags -->
</dict>
</plist>
```

### Usage

- Handle event open app from deeplink, I handle all events in ***app.dart***:

**Import Library**

```dart
import 'package:uni_links/uni_links.dart';
```

**Write function handler**

```dart
Future<void> initUniLinks() async {
    _sub = uriLinkStream.listen((Uri uri) {
      List<String> paths = uri.path.split('/');
      if (paths.contains('home')) {
        navGlogbalKey.currentState.pushNamedAndRemoveUntil(
          AppRoutes.HOME,
          (route) => false,
          arguments: {
            'index': 0,
          },
        );
      } else if (paths.contains('profile')) {
        navGlogbalKey.currentState.pushNamed(
          AppRoutes.PROFILE,
          arguments: {'userId': paths.last},
        );
      } else if (paths.contains('vvip')) {
        navGlogbalKey.currentState.pushNamedAndRemoveUntil(
          AppRoutes.HOME,
          (route) => false,
          arguments: {
            'index': 0,
          },
        );
        navGlogbalKey.currentState.pushNamed(
          AppRoutes.VVIP,
        );
      } else if (paths.contains('concept')) {
        navGlogbalKey.currentState.pushNamedAndRemoveUntil(
          AppRoutes.HOME,
          (route) => false,
          arguments: {
            'index': 1,
          },
        );
        navGlogbalKey.currentState.pushNamed(
          AppRoutes.DETAILS_CONCEPT,
          arguments: {
            'conceptId': paths.last,
            'appointmentModel': AppointmentModel(
              appointmentId: paths.last,
            ),
          },
        );
      }
      // Use the uri and warn the user, if it is not correct
    }, onError: (err) {
      // Handle exception by warning the user their action did not succeed
    });
}
```

**Call function run when open app and keep alive until user killed application:**

```dart
  @override
  void initState() {
    initUniLinks();
  }
```

### Test cases:

### Features:

### Author: lambiengcode from waodate.com