# Capture and process health data with Kotlin FHIR Data Capture

This guide takes you through the codelab on the Heropa lab, where you write the code in an editor in your browser and the app runs as a web page. You install nothing.

It is built on the [published codelab](https://ohs-foundation.github.io/ohs-docs/tutorials-and-codelabs/data-capture/), which was written for Android Studio on your own computer. This guide covers the lab only. If you would rather work in Android Studio, follow the [published codelab](https://ohs-foundation.github.io/ohs-docs/tutorials-and-codelabs/data-capture/) instead of this guide.

The codelab text comes from the [Kotlin FHIR Data Capture](https://github.com/ohs-foundation/kotlin-fhir-data-capture) project under `codelabs` directory and is used under the Apache License 2.0.

## 1. Before you begin

### What you'll build

In this codelab, you're going to build a Kotlin Multiplatform app with the Kotlin FHIR Data
Capture library. Your app will use the library to render and process FHIR Questionnaires and
responses. The same code runs on Android, iOS, Desktop (JVM) and Web (Wasm and JS).

### What you'll learn

*   How to integrate Kotlin FHIR Data Capture into your Kotlin Multiplatform application
*   How to display a Questionnaire
*   How to get answers as a QuestionnaireResponse
*   How to extract FHIR resources from a QuestionnaireResponse

### What you'll need

On the lab you need only a recent version of Chrome, Edge, Firefox or Safari. The app uses WebAssembly features that older browsers lack.

You do not need to know Kotlin. Every change is given in full, and this guide says exactly which file to open and where to put each line.

This codelab is focused on
[Kotlin FHIR Data Capture](https://github.com/ohs-foundation/kotlin-fhir-data-capture).
Non-relevant concepts and code blocks are glossed over and are provided for you to simply copy and
paste. If you haven't built a Kotlin Multiplatform app before, you can start by
[creating your first Compose Multiplatform app](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-multiplatform-create-first-app.html).

## 2. Get set up

### Start your lab

1. Open the terminal of your lab VM in Heropa.
2. Start the lab.

   ```bash
   cd ~/sdc-codelab
   ./lab-init.sh
   ```

3. Wait about a minute. The command ends with a summary like the example below. The addresses and the password in the example are placeholders and do not work. Use the ones your own terminal prints.

   ```text
   ------------------------------------------------------------------------
   The lab is ready. Open these in the browser on your own computer.

     Editor            https://203-0-113-10.sslip.io/ide/
     Editor password   4827
     App               https://203-0-113-10.sslip.io/preview/

     Scan a code with a phone or tablet camera to open that address there.

     Editor                               App
     [QR code]                            [QR code]

   First steps
     1. Open the Editor address and enter the password.
     2. Click Open app at the very bottom of the Editor. The app opens in a
        second tab.

   Lost these details? Run ./lab-init.sh --info to print them again.
   The tabs stopped loading? Run ./lab-init.sh again. It is safe at any time.
   ------------------------------------------------------------------------
   ```

**The addresses and password in this manual are examples only.** Wherever you see `203-0-113-10.sslip.io`, `203.0.113.10` or the password `4827`, use the values from your own summary instead. Your addresses and password are your own. You can get them back at any time, and the section **Find your addresses again** below explains how.

Where the example shows `[QR code]`, your terminal draws two real QR codes, one for the Editor and one for the App. Scan a code with the camera of a phone or tablet to open that address there without typing it. The Editor still asks for the password. The start page of your lab, which is the address with nothing after the name, shows the App code as well. Do the codelab itself on a computer, because the Editor needs a keyboard and a wide screen. The App on a phone is for a look only. What it prints does not reach the Output tab on your computer. If the codes look like broken characters in your terminal, ignore them and type the addresses.

### Open the editor and the app

You work in two browser tabs, one for the Editor and one for the App.

1. Open the **Editor** address in a new tab. Type the editor password, which is a code of four digits, and press **Submit**. If the summary shows `none needed` instead of a password, the Editor opens without asking.
2. In the bar at the very bottom of the Editor, click **Open app**. The app opens in a second tab. If your browser blocks the new tab, allow it, or open the **App** address yourself.

If you have room, put the two tabs side by side in separate windows.

The Editor tab has six areas. The last three share the space under the code, and you switch between them by clicking their tabs.

| Area | Where | What it is for |
|---|---|---|
| Explorer | Far left | The project files |
| Code | Top centre | The file you are changing |
| Build log | Bottom centre | Shows each build and whether it worked |
| Output | Bottom centre, the tab next to Build log | Shows what the app prints when you click **Submit**. You read the JSON here in steps 5 and 6. |
| Terminal | Bottom centre, the tab next to Output | For typing commands |
| Guide | Right | This guide |

You can drag the borders between the areas to resize them. The bar at the very bottom has six buttons.

| Button | What it does |
|---|---|
| **Open app** | Opens the App tab |
| **Build now** | Saves every open file and builds the app straight away. You rarely need it, because saving a file starts a build automatically. |
| **Guide** | Brings the Guide back if you closed it |
| **Build log** | Brings the Build log back if you closed it |
| **Output** | Brings the Output tab back if you closed it |
| **Fix imports** | Adds the import lines your code needs. A later part explains imports. |

The App tab has a bar along the top and a strip along the bottom.

| Control | What it does |
|---|---|
| Status dot | Green when the app matches your code, amber while a build runs, red when the last build failed |
| **Auto refresh** | When ticked, the app reloads automatically after every successful build |
| **Refresh** | Reloads the app now |
| **Output** | The strip along the bottom, which shows whatever the app prints. The Output tab in the Editor shows the same thing. |

### Find your addresses again

Come here when you return to the lab after a break, when the Editor or App tab stops loading, or when you have lost the addresses or the password.

The lab VM stops when you close the course, when you stop it yourself, and when it sits idle for a while. Your code stays on it. The VM is set up to keep the same IP address, so the same addresses normally work again once it is running.

1. Open the course in Heropa and start the VM if it is stopped. Give it a minute.
2. Reload the Editor and App tabs. If they load, you are done.
3. If they do not load, or you no longer have the addresses, open the terminal of the VM and run the same two commands as before.

   ```bash
   cd ~/sdc-codelab
   ./lab-init.sh
   ```

4. Read the summary it prints. The lines under **Since the last run** tell you what to do.

When the address is the same, the summary says so. Reload your tabs, or open the addresses again.

```text
Since the last run
  Address    The same as before. Tabs you still have open work again after
             a reload.
  Password   The same as before.
  Your code  Kept as you left it.
```

When the VM came back with a different IP address, the summary says `CHANGED` and shows the old address. The address in this example is a placeholder. Close the old Editor and App tabs, because they no longer work, and so do bookmarks to them. Open the new **Editor** address from the top of the summary, enter the password, and click **Open app**.

```text
Since the last run
  Address    CHANGED. It was https://203-0-113-10.sslip.io
             Close the old Editor and App tabs. They no longer work. Open
             the two addresses above.
  Password   The same as before.
  Your code  Kept as you left it.
```

| What | After the VM restarts |
|---|---|
| Editor and App addresses | Normally the same. The summary says `CHANGED` when they are not. |
| Editor password | Always the same |
| Your code | Always kept as you left it |
| Build log and Output | Start empty, and fill again at the next build and the next **Submit** |

The command is safe to run at any time and as often as you like. It changes nothing when the address is the same. When the address changed it takes about a minute, because it gets a certificate for the new address.

Always copy the addresses from the summary. They are made from the IP address of the VM, normally with dashes instead of dots and `.sslip.io` at the end, so an example IP of `203.0.113.10` would give `https://203-0-113-10.sslip.io/ide/`. Sometimes the summary shows the plain IP, such as `https://203.0.113.10/ide/`, with a note that says why. Those addresses work in exactly the same way.

### If the lab starts in reduced mode

The Editor needs a certificate that your browser trusts. The lab asks for one each time the address changes. Occasionally it cannot get one, usually because the service that issues them is over its limit. The lab then makes its own certificate and starts in **reduced mode**, and the summary from `./lab-init.sh` opens with the words `REDUCED MODE`. It also gives the reason, the pages to use, and when to try again.

Your code, the build and the app all work in reduced mode. Two things are different.

1. Your browser shows a warning page the first time you open the Editor address. Click **Advanced**, then **Proceed**. You do this once. The warning appears because the lab made its own certificate, and it is expected here.
2. The Editor has no Guide, Build log or Output area, because browsers block them on a certificate they do not trust. A notice in the Editor says so. Use these in their place.

| In full mode | In reduced mode |
|---|---|
| Guide area in the Editor | This manual in Heropa. The **Guide** button also opens it in the Editor as plain text. |
| Build log area in the Editor | The **Build log** address that `./lab-init.sh` prints, in a separate browser tab. The **Build log** button opens it. |
| Output area in the Editor | The **Output** strip along the bottom of the App tab |

Everything else in this manual applies as written. Where a step says to look at the Build log or the Output tab, look at the page from the table.

To get back to full mode, run `./lab-init.sh` again later. When it was refused for being over a limit, the summary gives the time to try again under **To get full mode**. That run asks for a certificate again and puts the lab back in full mode when it gets one. The addresses may change when that happens, so open the ones it prints.

### Download the Code

There is nothing to download on the lab. The code is already there, and the Editor opens on the `codelabs` folder. The Explorer on the left shows what is inside it.

### Project structure

| Module       | Contents                                                                                      |
|:-------------|:----------------------------------------------------------------------------------------------|
| `shared`     | Kotlin Multiplatform module. `commonMain` holds the `App()` composable and the questionnaire. |
| `androidApp` | Android application. `MainActivity` calls `setContent { App() }`.                             |
| `desktopApp` | Desktop (JVM) application. `main()` opens a window that shows `App()`.                        |
| `webApp`     | Web application for Wasm and JS. `main()` shows `App()` in the browser page.                  |
| `iosApp`     | Xcode project for iOS. It shows `App()` through `MainViewController()` in `shared/iosMain`.   |

The platform modules are just entry points. Everything you'll write in this codelab goes into two
files of the `shared` module:

*   `shared/build.gradle.kts`
*   `shared/src/commonMain/kotlin/dev/ohs/fhir/codelab/App.kt`

Each place you'll change is marked with a numbered comment, such as
`// 4 Replace with code from the codelab to display the questionnaire.` The number tells you which
step of the codelab adds the code.

The questionnaire is stored as a
[Compose Multiplatform resource](https://www.jetbrains.com/help/kotlin-multiplatform-dev/compose-multiplatform-resources.html)
at `shared/src/commonMain/composeResources/files/questionnaire.json`, so every platform can read it.

A path such as `shared/build.gradle.kts` tells you which folders to open in the Explorer, from left to right. Click a folder to open it, and click a file to show it in the Code area.

**To open `shared/build.gradle.kts`**

1. In the Explorer, click the `shared` folder to open it.
2. Click the `build.gradle.kts` file that sits inside `shared`.

The project has two files named `build.gradle.kts`. The other one sits lower in the Explorer, outside every folder, and it is the wrong one. Check the line of small text above the code. It must read `shared > build.gradle.kts`.

**To open `shared/src/commonMain/kotlin/dev/ohs/fhir/codelab/App.kt`**

This file is already open when the Editor starts. If you closed it, open `shared`, then `src`, then `commonMain`, then the folder shown as `kotlin/dev/ohs/fhir/codelab`, and click `App.kt`.

### Import the app into Android Studio

There is nothing to import on the lab. The Editor tab takes the place of Android Studio, and the project is already open in it.

### Run the starter app

The starter app is already running on the lab as a web page. Look at the App tab, which shows a purple bar and the words **Hello World!**

As you can see there isn't much here yet, so let's get right into displaying a questionnaire in
your app!

The picture below shows the same app on an Android phone.

![The Hello World app on an Android phone](https://raw.githubusercontent.com/ohs-foundation/kotlin-fhir-data-capture/15d534d67c0acdf9a7e264aee53d1320c91ab5cc/codelabs/images/image3.png)

### Run the app on other platforms

The lab runs the Web (Wasm) version of the app. You do not type any command. The lab builds the app automatically every time you save a file, as the next part explains.

The app is built with Kotlin Multiplatform, so the same code also runs on Android, iOS and Desktop. The [published codelab](https://ohs-foundation.github.io/ohs-docs/tutorials-and-codelabs/data-capture/) shows how to run it on each of them.

### How to run the app

You do the same four things for every step of the codelab.

1. Change a file in the Code area.
2. Save it with Ctrl+S, or Cmd+S on a Mac. Saving starts a build.
3. Watch the Build log under the code. It prints `Building the app` and then, after up to a minute, `BUILD OK`.
4. Switch to the App tab. It has reloaded automatically and shows the new version.

**Builds are automatic.** The lab watches the project files. Every time you save one, it builds the app again and the App tab reloads. There is no Run button to press and no command to type. A change you have not saved is not built. A white dot on a file's tab marks unsaved changes, so while you see it the app does not have that change yet.

If you save again while a build is running, the lab finishes that build and then runs one more, so your latest change is never missed.

### Start a build yourself

You rarely need to, but you can start a build without changing a file. Do this when you are not sure a build ran, when the Build log shows nothing new after a save, or when you want to see the result of the last build again.

| Way | What to do |
|---|---|
| Button | Click **Build now** in the bar at the very bottom of the Editor. It saves every open file first, then builds. |
| Command | Click the **Terminal** tab, type `build` and press Enter. This builds the files as they were last saved. |

Either way, the build appears in the Build log like any other, and ends with `BUILD OK` or `BUILD FAILED`. When nothing changed since the last build, it finishes in a few seconds.

### When a build fails

If the Build log prints `BUILD FAILED`, look just above it for lines that start with `e`. Each one names the file, the line number and the problem. Under them, a **What to do** list explains the common ones in plain words. Fix the file and save again. The App tab keeps showing the last version that worked.

### If you have used Android Studio

This table shows what takes the place of each thing you know.

| In Android Studio | On the lab |
|---|---|
| Open Android Studio | Use the Editor tab |
| Click **Run** in the Android Studio toolbar | Save the file, wait for `BUILD OK`, look at the App tab. **Build now** does the same without a change. |
| Select **Sync Project with Gradle Files** | Save the file. Saving does the sync. |
| Search Logcat | Read the **Output** tab in the Editor, next to the Build log |
| Tap **Submit** | Click **Submit** |
| Android Studio prompts you to add the necessary imports | Paste the import lines yourself, under the numbered comment the step names |

### What this editor does not do for you

The web editor is simpler than Android Studio, which quietly does several jobs as you type. Read this once, because it explains most of the errors people hit.

| Android Studio does this | On the lab |
|---|---|
| Offers to add an import when you paste code | You add each import line yourself |
| Picks between two classes with the same name | You must use the exact import line from the step |
| Underlines mistakes in red as you type | Nothing is underlined. Mistakes show in the Build log after you save. |
| Completes names as you type | No completion. Copy and paste the code. |
| Tidies indentation and removes unused imports | Nothing is tidied. Indentation does not affect the build, and an unused import is harmless. |

**What an import is**

Code refers to things by short names, such as `Questionnaire`. An import line says which library a short name comes from. If the import is missing, the build fails with `Unresolved reference`.

**Where import lines go**

All import lines sit together near the top of `App.kt`, each starting with the word `import`. In the starter code they run from line 18 to line 50. Right under them, the lab has put one numbered comment for each step that needs imports.

```kotlin
import org.jetbrains.compose.resources.stringResource

// 4 Imports for the Questionnaire composable

// 5 Import for the JSON encoder

// 6 Imports for extracting FHIR resources
```

Each step tells you which of these comments to paste its imports under. You never have to hunt for the right spot. The order of import lines does not matter.

**One name, two meanings**

Two different things in this codelab are both called `Questionnaire`.

| Import line | What it is | Used in |
|---|---|---|
| `import dev.ohs.fhir.datacapture.Questionnaire` | The form that appears on screen | Step 4 |
| `import dev.ohs.fhir.model.r4.Questionnaire as FhirQuestionnaire` | The FHIR data behind the form, given a second name so the two do not clash | Step 6 |

If `App.kt` imports `dev.ohs.fhir.model.r4.Questionnaire` without the `as FhirQuestionnaire` ending, the build fails with `No parameter with name 'questionnaireJson' found`. Delete that line and use the two lines above.

**Help when you are stuck**

| Help | What it does |
|---|---|
| **Fix imports** button, at the bottom of the Editor | Adds every import the code in `App.kt` needs and removes the wrong `Questionnaire` import. This is the closest thing to what Android Studio does. |
| `step 3`, `step 4`, `step 5` or `step 6`, typed in the **Terminal** tab | Replaces both files with a working version of the code as it stands at the end of that step |
| `step reset`, typed in the **Terminal** tab | Puts both files back to the starter code |

Your own files are copied to the `backups` folder in your home folder before any of these change them.

## 3. Add Kotlin FHIR Data Capture to the project

### Add the dependency for Kotlin FHIR Data Capture

The Kotlin FHIR Data Capture dependency allows you to integrate the library in your app. Add the
following line below the `// 3 Add the dependency for Kotlin FHIR Data Capture` comment in the
`commonMain` dependencies of the `shared/build.gradle.kts` file:

Do this in the Editor tab.

1. Open `shared/build.gradle.kts`. In the Explorer, click the `shared` folder, then the `build.gradle.kts` file inside it. The line of small text above the code must read `shared > build.gradle.kts`.
2. Find the comment `// 3 Add the dependency for Kotlin FHIR Data Capture`. It is on line 46.
3. Click at the end of that comment line and press Enter to make a new line under it.
4. Paste this one line.

   ```kotlin
   implementation("dev.ohs.fhir:fhir-data-capture:2.0.0-alpha05")
   ```

5. Check your change against the block below, which shows the new line in its place. The `// ...` line stands for the lines that are already in the file. You do not paste this block.

```kotlin
kotlin {
  sourceSets {
    commonMain.dependencies {
      // ...

      // 3 Add the dependency for Kotlin FHIR Data Capture
      implementation("dev.ohs.fhir:fhir-data-capture:2.0.0-alpha05")
    }
  }
}
```

Because the dependency is in `commonMain`, Gradle picks the right artifact for each target
platform. The `androidApp`, `desktopApp` and `webApp` modules get the library through their
dependency on `shared`.

The starter project already includes the [Kotlin FHIR](https://github.com/ohs-foundation/kotlin-fhir)
model and `kotlinx-serialization-json`, which you'll use later in the codelab.

### Sync your project with Gradle files

On the lab, saving the file does the job of the **Sync Project with Gradle Files** button in Android Studio.

1. Save `shared/build.gradle.kts` with Ctrl+S, or Cmd+S on a Mac.
2. Watch the Build log until it prints `BUILD OK`. This build takes two to three minutes, because the whole library is compiled into the app for the first time.

The App tab looks the same after this step, because nothing uses the library yet.

## 4. Display a Questionnaire

In this step, you will add the functionality to the starter app to render a questionnaire with the
`Questionnaire` composable.

Open `shared/src/commonMain/kotlin/dev/ohs/fhir/codelab/App.kt`. The `App()` composable already
reads the questionnaire JSON from the resources into `questionnaireJson`, and creates a coroutine
`scope` that you'll use later in this step:

```kotlin
var questionnaireJson by remember { mutableStateOf<String?>(null) }
LaunchedEffect(Unit) {
  questionnaireJson = Res.readBytes("files/questionnaire.json").decodeToString()
}
val scope = rememberCoroutineScope()
```

Start with the three imports this step needs. The web editor does not add imports for you, so you paste them in yourself, before any other code.

1. Go to `shared/src/commonMain/kotlin/dev/ohs/fhir/codelab/App.kt`. It is the other tab in the Code area.
2. Find the `// 4 Imports for the Questionnaire composable` comment. It is on line 52, just under the import lines. You can also press Ctrl+F and type `// 4 Imports`.
3. Replace that comment with the following code. The first line puts the comment back, so the imports end up right under it.

   ```kotlin
   // 4 Imports for the Questionnaire composable
   import dev.ohs.fhir.datacapture.Questionnaire
   import dev.ohs.fhir.datacapture.QuestionnaireConfig
   import kotlinx.coroutines.launch
   ```

The `// 4 Imports` comment is not in the published codelab. The lab adds it to the starter code to mark the place.

Copy the lines exactly. The first import is the form, and it must come from `datacapture`. A different class, `dev.ohs.fhir.model.r4.Questionnaire`, has the same short name and breaks this step.

### Step 1: Add the Questionnaire composable

The `Questionnaire` composable requires a JSON encoded FHIR Questionnaire for it to render.

You can look at the `questionnaire.json` file if you want, but one of the benefits of Kotlin FHIR
Data Capture is that you don't need to know the structure of the FHIR Questionnaire (or anything
about FHIR) to get it to render it! You'll look more closely at this file later in the codelab.

The three steps below make this change. These notes tell you where each thing they name is.

- The comment `// 4 Replace with code from the codelab to display the questionnaire.` is inside `App()`, near line 83 now that the imports are in. Press Ctrl+F and type `// 4 Replace` to jump to it. Just under it is the line that starts with `Text(text = "Hello World!"`.
- Select both of those lines, delete them and paste the block from step 3 where they were. The new block starts with its own numbered comment, `// 4 Display the questionnaire`.
- The `androidx.compose.ui.Alignment` import is in the import group near the top of the file, on the line `import androidx.compose.ui.Alignment`. Deleting it is optional. An unused import does no harm.

1.  Find the `// 4 Replace with code from the codelab to display the questionnaire.` comment in
    `App()`.
2.  You don't need the `Hello World!` text, so delete the `Text(...)` line below the comment. You
    can also delete the `androidx.compose.ui.Alignment` import, which is no longer used.
3.  Add the following code in its place:

    ```kotlin
    // 4 Display the questionnaire
    questionnaireJson?.let { questionnaireJson ->
      Questionnaire(
        questionnaireJson = questionnaireJson,
        config = QuestionnaireConfig(showCancelButton = false),
        onSubmit = {},
        onCancel = {},
      )
    }
    ```

The questionnaire is shown as soon as `questionnaireJson` has been read. `QuestionnaireConfig` lets
you customize how the questionnaire is displayed. Here it hides the **Cancel** button, so only the
**Submit** button is shown, and `onCancel` can be left empty.

Now run it. Save `App.kt` with Ctrl+S, wait for `BUILD OK` in the Build log, then look at the App tab. It shows the questionnaire as a form.

The picture below shows the same form on an Android phone.

![The questionnaire shown as a form on an Android phone](https://raw.githubusercontent.com/ohs-foundation/kotlin-fhir-data-capture/15d534d67c0acdf9a7e264aee53d1320c91ab5cc/codelabs/images/image2.png)

If the Build log prints `BUILD FAILED`, read the **What to do** list under the errors, or click **Fix imports** at the bottom of the Editor.

Navigate through the questionnaire and try entering some answers. There are a few different answer
widgets used, including text fields and a date picker, which were automatically rendered based on
the underlying type in the original FHIR questionnaire.

*Technically, that's all you need to do to render a questionnaire! Congratulations!*

### Step 2: Handle the Submit button

At the moment nothing happens when you tap **Submit**. The library calls `onSubmit` when the
button is tapped, so replace `onSubmit = {},` with the following code:

1. Find the line `onSubmit = {},`. It is inside the block you pasted in Step 1, four lines under the `// 4 Display the questionnaire` comment. Press Ctrl+F and type `onSubmit` to jump to it. This piece of code does not have its own numbered comment.
2. Replace that one line with these five lines. Leave the `onCancel = {},` line under it in place.

   ```kotlin
   onSubmit = { getQuestionnaireResponse ->
     // getQuestionnaireResponse() validates the answers first. If validation fails, the
     // library shows an error dialog and cancels this coroutine.
     scope.launch { submitQuestionnaire(questionnaireJson, getQuestionnaireResponse()) }
   },
   ```

The new code uses `launch`, which is why you added the `kotlinx.coroutines.launch` import at the start of this step.

`getQuestionnaireResponse` is a suspend function, so it is called from a coroutine launched in
`scope`. It validates the answers before it returns the response. If any answers are invalid, the
library shows a dialog that lists them and cancels the coroutine, so you don't have to handle
validation errors yourself.

The response is passed on to `submitQuestionnaire()`, at the end of `App.kt`, which is still empty.
You'll fill it in over the next two steps.

Your `App()` composable should now look like this:

```kotlin
@Composable
fun App() {
  val darkTheme = isSystemInDarkTheme()
  MaterialTheme(colorScheme = if (darkTheme) darkColorScheme() else lightColorScheme()) {
    Scaffold(topBar = { AppBar(darkTheme) }) { innerPadding ->
      Box(
        modifier = Modifier.fillMaxSize().padding(innerPadding).consumeWindowInsets(innerPadding)
      ) {
        var questionnaireJson by remember { mutableStateOf<String?>(null) }
        LaunchedEffect(Unit) {
          questionnaireJson = Res.readBytes("files/questionnaire.json").decodeToString()
        }
        val scope = rememberCoroutineScope()

        // 4 Display the questionnaire
        questionnaireJson?.let { questionnaireJson ->
          Questionnaire(
            questionnaireJson = questionnaireJson,
            config = QuestionnaireConfig(showCancelButton = false),
            onSubmit = { getQuestionnaireResponse ->
              // getQuestionnaireResponse() validates the answers first. If validation fails, the
              // library shows an error dialog and cancels this coroutine.
              scope.launch { submitQuestionnaire(questionnaireJson, getQuestionnaireResponse()) }
            },
            onCancel = {},
          )
        }
      }
    }
  }
}
```

Save `App.kt` and wait for `BUILD OK`. The form looks the same as before. Clicking **Submit** still shows nothing, because `submitQuestionnaire()` is empty until the next step.

Before you go on, check that the end of the import group in `App.kt` reads like this, and that the file has no line `import dev.ohs.fhir.model.r4.Questionnaire`.

```kotlin
import dev.ohs.fhir.model.r4.QuestionnaireResponse
import org.jetbrains.compose.resources.stringResource

// 4 Imports for the Questionnaire composable
import dev.ohs.fhir.datacapture.Questionnaire
import dev.ohs.fhir.datacapture.QuestionnaireConfig
import kotlinx.coroutines.launch

// 5 Import for the JSON encoder

// 6 Imports for extracting FHIR resources
```

If you would rather jump to a working version, type `step 4` in the **Terminal** tab.

A questionnaire isn't very helpful if you can't see the answers that users input though. Let's go
on to the next step of getting a Questionnaire Response!

## 5. Get a Questionnaire Response

In the previous steps, you rendered a FHIR questionnaire in your app.

In this section, you'll get the answers from the questionnaire as a QuestionnaireResponse.

This step needs one import. In `App.kt`, find the `// 5 Import for the JSON encoder` comment, which sits under the imports you added in step 4. You can also press Ctrl+F and type `// 5 Import`. Replace that comment with the following code.

```kotlin
// 5 Import for the JSON encoder
import kotlinx.serialization.json.Json
```

The `// 5 Import` comment is not in the published codelab. The lab adds it to the starter code to mark the place.

### Step 1: Configure the JSON encoder

Find the `// 5.1 Replace with code from the codelab to configure the JSON encoder.` comment above
`App()` and replace it with the following code:

1. Find the comment `// 5.1 Replace with code from the codelab to configure the JSON encoder.` in `App.kt`. It is a little under the import group and just above the line `@Composable` that comes before `fun App() {`. Press Ctrl+F and type `// 5.1` to jump to it.
2. Select that one comment line and paste this block over it.

   ```kotlin
   // 5.1 Configure the JSON encoder
   private val json = Json {
     explicitNulls = false
     encodeDefaults = false
   }
   ```

The Kotlin FHIR model classes are `kotlinx.serialization` classes, so a `Json` instance can encode
them to JSON and decode them back. `explicitNulls = false` and `encodeDefaults = false` leave out
empty fields, so the output looks like regular FHIR JSON.

### Step 2: Print the questionnaire response

Find the `submitQuestionnaire()` method and replace the
`// 5.2 Replace with code from the codelab to print the questionnaire response.` comment with the
following code:

1. Go to `submitQuestionnaire()`. It is the last part of `App.kt`, at the very bottom of the file. Press Ctrl+F and type `// 5.2` to jump to the comment.
2. Select that one comment line and paste these two lines over it.

   ```kotlin
   // 5.2 Print the questionnaire response
   println("response: ${json.encodeToString(questionnaireResponse)}")
   ```

Leave the `// 6 Replace with code from the codelab` comment under it in place. You replace that one in step 6.

The `questionnaireResponse` passed to `submitQuestionnaire()` is a Kotlin FHIR
`QuestionnaireResponse`, which you can use directly or with other Kotlin FHIR libraries. This
codelab encodes it to a JSON string so that you can view it in the log. The `println` output shows
up in Logcat on Android, the Xcode console on iOS, the terminal on Desktop and the browser console
on Web.

On the lab the output appears in the **Output** tab of the Editor, next to the Build log.

1. Save `App.kt` and wait for `BUILD OK` in the Build log.
2. Switch to the App tab, fill in the form and click **Submit**.
3. Switch back to the Editor tab. The **Output** tab has come to the front and shows an entry named `response`, with the JSON laid out for reading. Each entry has a **Copy** button.

The same output also shows in a strip at the bottom of the App tab.

The same text also goes to the browser console, which opens with F12, if you prefer to read it there.

If you would rather jump to a working version, type `step 5` in the **Terminal** tab.

## 6. Extract FHIR Resources from a QuestionnaireResponse

The questionnaire included with the starter project is for a simple patient registration flow, so
you may ultimately want to use the questionnaire responses to create a
[FHIR Patient resource](https://www.hl7.org/fhir/R4/patient.html) based on them. This conversion
from a questionnaire response to FHIR resources is called
[data extraction](https://hl7.org/fhir/uv/sdc/STU4/extraction.html). We can do this with the
`TemplateExtractionEngine` class of Kotlin FHIR Data Capture.

To perform data extraction, your questionnaire must be authored to include information on how to
perform the data extraction. Luckily, the sample questionnaire is already set up for
[Template-based extraction](https://hl7.org/fhir/uv/sdc/STU4/extraction.html#template-extract).
If you open `questionnaire.json`, you'll find:

*   A `Patient` resource with the ID `patient-template` in the `contained` array. This is the
    template for the resource that gets extracted.
*   An `sdc-questionnaire-templateExtract` extension on the questionnaire, which points to the
    template with `#patient-template`.
*   An `sdc-questionnaire-templateExtractValue` extension on each template field that comes from an
    answer. It holds a FHIRPath expression that picks the answer, for example the family name:

    ```
    %resource.repeat(item).where(linkId='PR-name-family').answer.value.first()
    ```

*   Template fields without an extension, such as the identifier `system`, which are copied to the
    extracted resource as they are.

The questionnaire also has `definition` elements and an `sdc-questionnaire-itemExtractionContext`
extension. These are for
[Definition-based extraction](https://hl7.org/fhir/uv/sdc/STU4/extraction.html#definition-extract),
which the library doesn't support, so they are ignored.

To open `questionnaire.json`, go to the Explorer and open `shared`, then `src`, then `commonMain`, then `composeResources`, then `files`, and click the file. You only read it in this step. Do not change it.

This step needs two imports. In `App.kt`, find the `// 6 Imports for extracting FHIR resources` comment, which sits under the import you added in step 5. You can also press Ctrl+F and type `// 6 Imports`. Replace that comment with the following code.

```kotlin
// 6 Imports for extracting FHIR resources
import dev.ohs.fhir.datacapture.extraction.template.TemplateExtractionEngine
import dev.ohs.fhir.model.r4.Questionnaire as FhirQuestionnaire
```

The `// 6 Imports` comment is not in the published codelab. The lab adds it to the starter code to mark the place.

Copy the last line in full, with its `as FhirQuestionnaire` ending. Without that ending the build fails, because the name `Questionnaire` would then mean two different things.

Find the `submitQuestionnaire()` method and replace the
`// 6 Replace with code from the codelab to extract FHIR resources from the questionnaire response.`
comment with the following code:

1. Go to `submitQuestionnaire()`, at the very bottom of `App.kt`. Press Ctrl+F and type `// 6 Replace` to jump to the comment. It sits under the `// 5.2 Print the questionnaire response` lines you added in step 5.
2. Select that one comment line and paste these four lines over it.

   ```kotlin
   // 6 Extract FHIR resources from the questionnaire response
   val questionnaire = json.decodeFromString<FhirQuestionnaire>(questionnaireJson)
   val bundle = TemplateExtractionEngine.extract(questionnaire, questionnaireResponse)
   println("extraction result: ${json.encodeToString(bundle)}")
   ```

`TemplateExtractionEngine.extract()` requires a Kotlin FHIR Questionnaire, which you can create by
decoding the questionnaire JSON string from earlier, and a QuestionnaireResponse, which we already
got earlier. It returns a transaction
[Bundle](https://www.hl7.org/fhir/R4/bundle.html) containing one or more extracted resources–in
this case a single Patient resource.

Your `submitQuestionnaire()` method should now look like this:

```kotlin
private fun submitQuestionnaire(
  questionnaireJson: String,
  questionnaireResponse: QuestionnaireResponse,
) {
  // 5.2 Print the questionnaire response
  println("response: ${json.encodeToString(questionnaireResponse)}")

  // 6 Extract FHIR resources from the questionnaire response
  val questionnaire = json.decodeFromString<FhirQuestionnaire>(questionnaireJson)
  val bundle = TemplateExtractionEngine.extract(questionnaire, questionnaireResponse)
  println("extraction result: ${json.encodeToString(bundle)}")
}
```

Now run it.

1. Save `App.kt` and wait for `BUILD OK` in the Build log.
2. Switch to the App tab, fill in the form and click **Submit**.
3. Switch back to the Editor tab and look in the **Output** tab for the entry named `extraction result`. The newest entry is on top, with the `response` entry under it.

It is a FHIR Bundle of type `transaction` holding one Patient, with the names you typed.

If you would rather jump to the finished code, type `step 6` in the **Terminal** tab.

## 7. Congratulations!

You have used Kotlin FHIR Data Capture to process FHIR questionnaires and responses in your app:

*   Display a Questionnaire
*   Get a Questionnaire Response
*   Extract FHIR Resources from a QuestionnaireResponse

That is all you need to get it up and running!

### What we've covered

*   How to add Kotlin FHIR Data Capture to your Kotlin Multiplatform app
*   How to use the `Questionnaire` composable and `TemplateExtractionEngine` to work with FHIR
    questionnaires

### Next Steps

*   Run the codelab on the other platforms: Desktop, Web and iOS
*   Customize the look and feel of rendered questionnaires with `QuestionnaireConfig`
*   Read the [conformance](https://github.com/ohs-foundation/kotlin-fhir-data-capture/blob/15d534d67c0acdf9a7e264aee53d1320c91ab5cc/docs/conformance.md) document to see which Questionnaire and SDC
    features the library supports
*   Apply Kotlin FHIR Data Capture in your own app

The other platforms need tools the lab does not have. To try them, follow the [published codelab](https://ohs-foundation.github.io/ohs-docs/tutorials-and-codelabs/data-capture/) on your own computer. Here are some things you can try on the lab.

* In `App.kt`, change `showCancelButton = false` to `true`, save, and see what the form gains
* Open `questionnaire.json`, change the `text` of a question and save
* Leave a field empty and submit, then compare the two entries in the Output tab

## Troubleshooting

| Symptom | Fix |
|---|---|
| You saved a file and the Build log shows no new build | Check the file's tab for a white dot, which means it is not saved yet. Then click **Build now** at the very bottom of the Editor, or type `build` in the **Terminal** tab. |
| The App tab says the app is still building | Wait for `BUILD OK` in the Build log. The page reloads automatically. |
| The App tab did not change after a build | Check that **Auto refresh** is ticked, or click **Refresh**. If it still shows the old version, check that the Build log says `BUILD OK` and not `BUILD FAILED`. |
| `Unresolved reference` in the Build log | An import is missing or misspelled. Click **Fix imports**, or compare your imports with the ones the step gives. |
| `No parameter with name 'questionnaireJson' found` or `No value passed for parameter 'status'` | `App.kt` imports the wrong `Questionnaire`. Delete `import dev.ohs.fhir.model.r4.Questionnaire`, add `import dev.ohs.fhir.datacapture.Questionnaire`, or click **Fix imports**. |
| `Conflicting import` or `Overload resolution ambiguity` on `Questionnaire` | The import in step 6 needs `as FhirQuestionnaire` at the end. |
| `Unresolved reference 'datacapture'` | Step 3 is not done or not saved. Check the line in `shared/build.gradle.kts`. |
| `Unresolved reference 'Alignment'` | The `Hello World!` line is still in the file. Step 4 replaces it. |
| `Unresolved reference 'json'` | The `// 5.1` code is missing. It creates the value that the `// 5.2` and `// 6` code use. |
| `Expecting` or `Syntax error` | A bracket, brace or quote is missing or extra near the line number in the error. |
| The App tab is blank | Your browser is too old for this app. Use a current Chrome, Edge, Firefox or Safari. |
| Nothing appears in the Output tab | Click **Submit** again after the app has reloaded. Step 5 must be built for anything to print. The App tab and the Editor must also be open in the same browser, because the output travels between them through that browser. |
| The Guide, Build log or Output area is gone | Click its name in the bar at the very bottom of the Editor. |
| **Open app** does nothing | The browser blocked the new tab. Allow pop-ups for the lab address, or open the **App** address that `./lab-init.sh` printed. |
| The code is in a state you cannot fix | Type `step reset` in the **Terminal** tab, or `step` with the number of the last step you finished. |
| The Editor asks for the password again | Run `./lab-init.sh --info` in the Heropa terminal. It prints the same password without restarting anything. |
| You came back and the tabs do not load | The VM was stopped. Start it in Heropa, wait a minute and reload the tabs. If they still do not load, run `./lab-init.sh` in its terminal and read **Since the last run** in what it prints. When that says `CHANGED`, open the new addresses. See **Find your addresses again**. |
| The Editor or App address stopped working | The same cause and the same fix as the row above. |
| You lost the addresses or the password | Run `./lab-init.sh --info` in the Heropa terminal. It prints them again. |
| The browser warns that the connection is not private | The lab is in reduced mode. Click **Advanced**, then **Proceed**. See **If the lab starts in reduced mode**. |
| The Editor shows a notice about reduced mode, or has no Guide, Build log or Output area | The lab could not get a trusted certificate. Use the pages listed under **If the lab starts in reduced mode**, and run `./lab-init.sh` again later. |
