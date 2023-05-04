# AGP-7.3-Issue
Sample project showing new bug introduced on AGP 7.3

## Bug description
On AGP 7.3, when minify is enabled (`minifyEnabled true`), the application apk dexes contain type definitions from the Android Framework.

## How to reproduce / how this sample app was created
1. Create empty Android project in Android Studio using project creating wizard.
2. In `app/build.gradle`, set `minifyEnabled` to true. This is key, otherwise it does not reproduce.
3. In `gradle/wrapper/gradle-wrapper.properties`, change gradle version to `7.4.1`.
4. In the root `build.gradle`, change AGP version to `7.2.2`.
5. --- AT THIS STAGE IT DOES NOT REPRODUCE ---
6. In the root `build.gradle`, change AGP version to `7.3.1`.
7. --- AT THIS STAGE IT REPRODUCES ---

## How to check whether it reproduces?
1. `./gradlew clean app:assembleRelease`
2. `dexdump app/build/outputs/apk/the_apk.apk | grep "Class desc" | sort > out.txt`
3. If it any type definitions starting with `Landroid/XXX` shows up, where XXX is not `support\...`, then the issue reproduces.
