## Android Instrumentation Test Flow (`./gradlew connectedAndroidTest`)

```mermaid
flowchart TD

  %% Start points
  startLocal([Start - Local Run\n./gradlew connectedAndroidTest])
  startCI([Start - CI Run\nPush / PR / scheduled job])

  %% CI pipeline details
  subgraph CI_Pipeline [CI Run]
    direction TB
    ci1[CI Step 1 – Checkout project\nGet the repo onto the build machine]
    ci2[CI Step 2 – Set up JDK + Android SDK\nJava + Android tooling ready]
    ci3[CI Step 3 – Make a device available\nEmulator / device / device farm]
  end

  %% General 5 steps of instrumentation test
  subgraph Steps [Instrumentation Testing in Android Studio]
    direction TB

    s1["1. Run Gradle and set up the build\n- Instrumentation test is initiated\n- Gradle wrapper ensures correct version\n- Builds task graph"]

    s2["2. Build app and androidTest APKs\n- Debug app APK (App Under Test)\n- androidTest APK (tests + runner)"]

    s3["3. Discover device/emulator & install APKs\n- adb finds connected device/emulator\n- Installs/updates App APK\n- Installs/updates Test APK"]

    s4["4. Launch instrumentation with test runner\n- AndroidJUnitRunner or subclass\n- Runs in instrumentation process\n- Discovers & executes tests\n- Sends test results back"]

    s5["5. Collect results & finish build\n- adb returns instrumentation output\n- Gradle writes XML/HTML reports\n- Build marked SUCCESS or FAILED"]
  end

  %% Detailed breakdown of Step 4
  subgraph Step4Details [Step 4 – Runner lifecycle]
    direction TB
    d41[4.1 Runner startup]
    d42[4.2 Test discovery]
    d43[4.3 Per-test lifecycle]
    d44[4.4 Handle pass/fail/error]
    d45[4.5 Finish run]
  end

  %% Entry paths
  startLocal --> s1
  startCI --> ci1 --> ci2 --> ci3 --> s1

  %% Main flow
  s1 --> s2 --> s3 --> s4 --> s5

  %% Step 4 expansion
  s4 --> d41
  d41 --> d42 --> d43 --> d44 --> d45

