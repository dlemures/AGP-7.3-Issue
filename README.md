# AGP-7.3-Issue
Sample project showing new bug introduced on AGP 7.3

## Bug description
On AGP 7.3, when minify is enabled (`minifyEnabled true`), the application apk dexes contain type definitions from the Android Framework.

## How to reproduce / how this sample app was created
1. Create empty Android project in Android Studio using project creation wizard.
2. In `app/build.gradle`, set `minifyEnabled` to true. This is key, otherwise it does not reproduce.
3. In `gradle/wrapper/gradle-wrapper.properties`, change gradle version to `7.4.1`.
4. In the root `build.gradle`, change AGP version to `7.2.2`.
5. --- AT THIS STAGE IT DOES NOT REPRODUCE ---
6. In the root `build.gradle`, change AGP version to `7.3.1`.
7. --- AT THIS STAGE IT REPRODUCES ---

## How to check whether it reproduces?
1. `./gradlew clean app:assembleRelease`
2. `dexdump app/build/outputs/apk/the_apk.apk | grep "Class desc" | sort > out.txt`
3. If it any type definitions starting with `Landroid/XXX` shows up, where XXX is not `support/...`, then the issue reproduces.

## Other observations
1. These type definitions go into the apk even though there is no actual code in the apk, not a single activity. However, if I remove the dependencies of the project, defined in `app/build.gradle`, they go away from the APK. So, it looks like they are transitive dependencies of the runtime dependencies I add to my project. They are included even if not used.
2. Also observed during my investigation that if I enforce different dependency versions using `resolutionStrategy`, the classes included in the APK change. This aligns with the point above: internal dependencies that probably change by version are exposed in the application APK.

## Sample of the output of dexdump
|AGP 7.2.2|AGP 7.3.1|
|--|--|
| Only `android/support`, `androidx`, `com/google` & `app code` | Same as 7.2.2, but also `android framework classes` |
|<img width="913" alt="image" src="https://user-images.githubusercontent.com/5851365/236351665-59fab893-b540-4311-a12b-9f8addc0b269.png">| <img width="786" alt="image" src="https://user-images.githubusercontent.com/5851365/236352086-e1617df9-89f2-4e3a-99f3-8dcc0155f6d0.png"> |
