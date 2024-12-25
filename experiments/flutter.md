
# Flutter

Based on https://docs.flutter.dev/get-started/install/linux/android

Required packages:
```bash
sudo apt-get update -y && sudo apt-get upgrade -y;
sudo apt-get install -y curl git unzip xz-utils zip libglu1-mesa
```

IDE Extensions: [VSCode](https://marketplace.visualstudio.com/items?itemName=Dart-Code.flutter), [emacs](https://jwill.dev/blog/2022/01/24/EmacsAsIDE-Flutter.html)

## Android Studio

64-bit Linux requires more packages:
```bash
sudo apt-get install libc6:amd64 libstdc++6:amd64 lib32z1 libbz2-1.0:amd64
sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386 lib32z1 libbz2-1.0:i386
```

Afterwards [Download](https://developer.android.com/studio) and extract to `/usr/local/`. Execute `/usr/local/android-studio/bin/studio.sh`. Once launched, open this repo. You can then use `Tools > Create Desktop Entry` to create a .desktop shortcut so you don't need to remember the command.

## Flutter SDK

Depending on your IDE:
<details><summary>VSCode</summary>

- Run `Flutter: New Project`
- Click 'Download SDK' in the prompt
- Select a directory, where the SDK will be cloned
- Wait
- When asked for an application type, cancel (Esc)
</details>
<details><summary>Other</summary>

[Download](https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.24.5-stable.tar.xz) and extract it somewhere.
</details>

Add to PATH (replace `path/to` with actual path)
```bash
echo 'export PATH="path/to/flutter/bin:$PATH"' >> ~/.bash_profile
```
Restart terminal or run `source ~/.bash_profile`

## Android Setup

In Android Studio go to `File > Settings > Languages & Frameworks > Android SDK`. We want Android 14 (API Level 34) and then under SDK Tools make sure these are all installed:
- Android SDK Build-Tools
- Android SDK Command-line Tools
- Android Emulator
- Android SDK Platform-Tools
<!-- - Android SDK Platform, API 35.0.1 -->

Accept the Android SDK licenses
```bash
flutter doctor --android-licenses
```

## Configure Android Device

Physical or virtual device are possible. See [here](https://docs.flutter.dev/get-started/install/linux/android#configure-your-target-android-device) for virtual devices, but take a look at [Hardware Virtualization](#hardware-virtualization) below.

### Physical Device

On your phone, search for your build number in the settings. Tap it 7 times to enable developer mode. Go to the developer settings and enable USB debugging. Then connect your phone to your computer.

### Hardware Virtualization

A virtual device benefits from hardware virtualization. WIthout it the emulator will be much slower. Instructions are based on https://help.ubuntu.com/community/KVM/Installation

Check if CPU supports hardware virtualization. 0 means no, 1 or more means yes. Still need to check if it is enabled in BIOS.
```bash
egrep -c '(vmx|svm)' /proc/cpuinfo
```

Install packages (Ubuntu 18.10 or later)
```bash
sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
```

Add user to kvm and libvirt groups
```bash
sudo adduser $USER kvm
sudo adduser $USER libvirt
```

**Reboot** and check if this doesn't print any errors
```bash
virsh list --all
```

## Verify Installation

```bash
flutter doctor
```
Ignore Chrome (and potentially VSCode).

## First Flutter Project

Based on https://codelabs.developers.google.com/codelabs/flutter-codelab-first#2

```bash
flutter create <output directory>
cd <output directory>
```
or in <details><summary>VSCode</summary>
- Run `Flutter: New Project`
- Select a directory
This automatically opens the project in VSCode

Disable telemetrics for the VSCode extension:
```bash
sudo snap install flutter --classic
dart --disable-analytics
```
</details>

Replace the contents of the following files:
<details><summary>pubspec.yaml</summary>

```yaml
name: namer_app # Replace with your project name
description: A new Flutter project.

publish_to: 'none' # Remove this line if you wish to publish to pub.dev

version: 0.0.1+1

environment:
  sdk: ^3.1.1

dependencies:
  flutter:
    sdk: flutter

  english_words: ^4.0.0
  provider: ^6.0.0

dev_dependencies:
  flutter_test:
    sdk: flutter

  flutter_lints: ^2.0.0

flutter:
  uses-material-design: true
```
</details>
<details><summary>analysis_options.yaml</summary>

```yaml
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    avoid_print: false
    prefer_const_constructors_in_immutables: false
    prefer_const_constructors: false
    prefer_const_literals_to_create_immutables: false
    prefer_final_fields: false
    unnecessary_breaks: true
    use_key_in_widget_constructors: false
```
</details>
<details><summary>lib/main.dart</summary>

```dart
import 'package:english_words/english_words.dart';
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider(
      create: (context) => MyAppState(),
      child: MaterialApp(
        title: 'Namer App',
        theme: ThemeData(
          useMaterial3: true,
          colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepOrange),
        ),
        home: MyHomePage(),
      ),
    );
  }
}

class MyAppState extends ChangeNotifier {
  var current = WordPair.random();
}

class MyHomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    var appState = context.watch<MyAppState>();

    return Scaffold(
      body: Column(
        children: [
          Text('A random idea:'),
          Text(appState.current.asLowerCase),
        ],
      ),
    );
  }
}
```
</details>

Then run the app:
```bash
flutter run
```
Or in VSCode, press F5.
