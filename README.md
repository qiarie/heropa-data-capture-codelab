# Kotlin FHIR Data Capture codelab on Heropa

This repository runs the [Kotlin FHIR Data Capture codelab](https://ohs-foundation.github.io/ohs-docs/tutorials-and-codelabs/data-capture/) on a headless Heropa VM. Learners need only a browser. They do not install Android Studio, an emulator, a JDK or anything else.

This guide is for the Heropa administrator who builds and maintains the course. Learners follow `course-manual.md`.

## How it works

The codelab is a Kotlin Multiplatform app. Everything a learner writes goes into shared code, and the starter project already has a web target. The lab builds that web target on the VM and serves it to the learner's browser, so the VM needs no screen.

Each learner gets one VM running two containers.

| Container | What it does |
|---|---|
| `sdc-workspace` | Holds the JDK, the codelab checkout, a warm Gradle cache and code-server, which is VS Code in a browser. A watcher rebuilds the app after every save. |
| `sdc-nginx` | The only published service. It terminates HTTPS and gives each part of the lab a path. |

| Path | What the learner sees |
|---|---|
| `/` | A start page with links to the two below |
| `/ide/` | The editor. It shows the code, the build log and the manual side by side. |
| `/preview/` | The app the learner is building, with a status bar, a refresh button and an output strip around it. This is the learner's App tab. |
| `/app/` | The app with nothing around it |
| `/buildlog/` | A page that follows the build log. The editor embeds this page. |
| `/output/` | A page that shows what the app printed. The editor embeds this page too. |

The learner loop is short. They save a file and wait for `BUILD OK` in the build log under the code. The App tab then reloads by itself.

Learners work in two browser tabs, the Editor and the App. The editor opens in a fixed layout, so nobody has to arrange panes.

| Area | Where | What it shows |
|---|---|---|
| Code | Top centre, about three fifths of the width | `App.kt`, already open |
| Build log | Under the code | Each build, its errors and plain advice for the common ones |
| Output | A tab next to the build log | What the app prints, with the JSON formatted. This is where learners read the QuestionnaireResponse and the extracted Bundle. |
| Terminal | A tab next to the output | An ordinary shell |
| Guide | Right, at full height | `course-manual.md`, rendered |

An earlier version also showed the app in a fourth pane. It left too little room for each pane, so the app now opens in its own tab from the **Open app** button in the status bar.

### Why the web target

| Target | Why it is or is not used |
|---|---|
| Web with Wasm | Used. It runs in the learner's own browser, so the VM needs no display. |
| Android | Not used. It needs an emulator with nested virtualisation and a screen to show it on. |
| Desktop on the JVM | Not used. It opens a window, and the VM has no desktop. |
| iOS | Not used. It needs a Mac. |

Building the web target needs no Android SDK. Gradle configures the Android modules without one and never compiles them.

### What differs from the published codelab

| The codelab says | In this lab |
|---|---|
| Download the code | Already in the workspace |
| Import the app into Android Studio | Open the Editor tab |
| Sync Project with Gradle Files | Save the file. The watcher runs Gradle. |
| Click **Run** | Save the file and wait for `BUILD OK`. The App tab reloads by itself. |
| Search Logcat for `response:` | Read the Output tab in the editor, next to the build log |
| Android Studio prompts you to add an import | Type or paste the import. The manual lists each one, and `fix-imports` adds them. |

The editor colours Kotlin but has no code completion, no import prompts and no error underlining. Three things stand in for them.

| Stand-in | What it does |
|---|---|
| Build hints | After a failed build, the panel prints a **What to do** list that names the missing or wrong import in plain words. `workspace/bin/codelab-hints` holds the rules. |
| `fix-imports` | Adds the imports the code in `App.kt` needs and removes the wrong `Questionnaire` import. A **Fix imports** button in the status bar runs it. |
| `step N` | Replaces both files with a working version of step N. |

The most common mistake is importing `dev.ohs.fhir.model.r4.Questionnaire` in step 4. The form and the FHIR model class share that name, and Android Studio would have offered the choice. The manual has an exercise on this, named "What this editor does not do for you".

## Repository layout

| Path | Purpose |
|---|---|
| `provision-heropa.sh` | Installs Docker on the VM and builds the workspace image |
| `lab-init.sh` | Works out the lab address, gets the certificate, creates the editor password and starts the lab |
| `docker-compose.yml` | The two containers and the volume that holds the learner's work |
| `workspace/Dockerfile` | The workspace image, pinned to one commit of the codelab |
| `workspace/bin/` | The build watcher, the build hints, the `fix-imports` and `step` helpers and the container entry point |
| `workspace/solutions/` | The two codelab files as they stand at the end of steps 3 to 6, and the starter `App.kt` with the numbered import comments added |
| `workspace/ide/` | Editor settings and the small extension that lays out the code, build log and manual |
| `nginx/` | The path routes and the static pages, which are the start page, the app preview, the build log, the output and the waiting page |
| `course-manual.md` | The learner manual, shaped as Heropa Sections and Exercises |

## Step 1 · Get a VM image

Ask the Heropa team for an Ubuntu 24.04 LTS server image in your region, or import your own. Use these settings.

| Setting | Value |
|---|---|
| OS type | Linux |
| Connection type | The terminal connection Heropa offers for Linux servers |
| Instance type | 4 vCPU and 16 GB memory, for example `m5.xlarge` |
| Storage | 30 GB |

A build peaked near 6 GB in a test container limited to 4 CPUs and 8 GB, before the editor was added. 16 GB leaves room for the editor and the browser sessions it serves. 8 GB has not been tested with the full lab.

## Step 2 · Create the template

1. In the Heropa portal open **Templates** and click **Add**.
2. Give it a title such as `Data Capture codelab`, pick the same region as the image and click **Create**.
3. On the **Details** tab click **Add VM** and add the image from Step 1.
4. Open the VM's row action menu and choose **Edit network**.
   1. Tick **Allocate a permanent public IP address**. The lab address is made from this IP, so it must survive an idle stop.
   2. Add inbound rules for TCP 80 and TCP 443 from `0.0.0.0/0`. Let's Encrypt checks over port 80 from addresses it does not publish, so that rule cannot be narrowed.

   Leave outbound traffic allowed. The first build needs it for the images, and step 3 of the codelab is served from the cache but anything a learner adds beyond the codelab is downloaded.

Learners open the lab in tabs of their own browser, not in Heropa interface tabs. A Heropa Website tab takes one fixed URL, and each learner's address is different.

## Step 3 · Prepare the VM

Launch an environment from the template and open the VM's terminal.

1. Put this repository on the VM and run the provisioning script.

   ```bash
   git clone <this repository> ~/sdc-codelab
   cd ~/sdc-codelab
   sudo ./provision-heropa.sh
   ```

   It installs Docker, pulls nginx and certbot, and builds the workspace image. The build clones the codelab, downloads every dependency and compiles the app twice, so allow around ten minutes.

2. Pick up the new group, then start the lab once.

   ```bash
   newgrp docker
   ./lab-init.sh
   ```

   It prints the Editor address, the editor password and the App address.

3. Open the Editor and App addresses in your own browser and walk the learner manual from start to finish.

4. Remove everything that belongs to this one VM, keeping the images.

   ```bash
   ./lab-init.sh --clean
   docker image ls
   ```

   `--clean` stops the lab, deletes the volume with your test edits, and removes `.env` and the certificate. Without it every learner would share one editor password and start from your code. It keeps the `letsencrypt` folder, which is harmless in the template because each learner's VM has a different address and asks for its own certificate.

5. Save the environment as a new template and use that saved template in the course.

### What the learner's first run costs

`./lab-init.sh` on a fresh VM takes about a minute. Most of it is the certificate request and Docker copying the checkout and the Gradle cache into the learner's volume. The App tab then shows the starter app as soon as the first build finishes, which is quick because the image already built it.

## Step 4 · Create the policy

| Setting | Value |
|---|---|
| Access period | 3 hours |
| Limit Active time | On, 2 hours |
| Idle action | Stop the VMs after 30 minutes idle |

A stopped VM keeps its disk and its permanent public IP. Both containers restart with the VM, and the learner's code is still in the volume. Running `./lab-init.sh` again prints the same addresses and password.

## Step 5 · Add the content

`course-manual.md` is written for the Heropa lab and its web editor. The lab instructions are the main text. Anyone who wants to use Android Studio is sent to the published codelab on the website instead.

It is built on the published codelab and keeps all of its text. The passages that only make sense in Android Studio, or on a learner's own computer, are kept but set apart.

| Kind of text | How to spot it |
|---|---|
| Lab instructions and the codelab's own explanations and code | Plain text |
| A passage that applies only to Android Studio | A quoted block that starts with **Android Studio only.** and tells the learner to skip it |

Thirteen blocks are marked that way. One is the example in the introduction. The other twelve are the list of tools, the download, the import into Android Studio, running on a device, the other platforms, the Gradle sync, the three import prompts, the Run button and the two Logcat searches.

The lab text adds three things the original leaves to Android Studio.

* The exact file to open and how to reach it in the Explorer, for learners who have never seen a Gradle project
* Where each import line goes, since the web editor does not offer imports
* A numbered comment for each group of imports, such as `// 4 Imports for the Questionnaire composable`. The published starter has numbered comments for the code but none for the imports. The image build adds three to `App.kt` and commits them, so the starter a learner opens already marks where each step's imports go. Each step then says to replace that comment with a block that starts with the same comment, the way the original does for code.

The manual maps onto Heropa content as one Section, with one Exercise for each second level heading.

| Exercise | Heading |
|---|---|
| 1 | Before you begin |
| 2 | Get set up, which includes starting the lab and the tour of the editor |
| 3 | Add Kotlin FHIR Data Capture to the project |
| 4 | Display a Questionnaire |
| 5 | Get a Questionnaire Response |
| 6 | Extract FHIR Resources from a QuestionnaireResponse |
| 7 | Congratulations |
| 8 | Troubleshooting |

Load it as Custom content by pasting each exercise into the rich text editor, or publish it through GitBook and add it as External content. The three pictures load from GitHub at the pinned commit, so they need no upload.

The text taken from the published codelab keeps its own punctuation and line breaks. Only the text written for the lab follows this repository's prose rule.

## Step 6 · Create and publish the course

Create a course that ties together the template saved in Step 3, the policy from Step 4 and the content from Step 5, then publish it.

## The layout inside the editor

VS Code has no setting that opens files or pages when it starts, so a small extension in `workspace/ide/codelab-guide/` builds the layout. It is packaged into the image at build time and needs no marketplace.

| Area | How it is made |
|---|---|
| Guide | The Markdown preview that ships with the editor, opened on `course-manual.md` |
| Build log | A web view that frames `/buildlog/` |
| Output | A web view that frames `/output/` |
| Terminal | An ordinary terminal, placed in a tab next to the output |

The status bar has five buttons. **Open app** opens `/preview/` in a new browser tab. **Guide**, **Build log** and **Output** reopen an area that was closed. **Fix imports** runs the `fix-imports` helper. The layout comes back after a page reload.

The app runs in one browser tab and the Output tab sits in another, so the output has to travel between them. No server is involved. The preview page listens to the app's console and saves each printed line in the browser's local storage for the lab address. The `/output/` page reads that storage and is told by the browser when it changes. It then asks the editor to bring the Output tab to the front. This works because both pages come from the same address, and it means the two tabs must be open in the same browser.

To change the default sizes, edit `LAYOUT` near the top of `extension/extension.js` and rebuild the image.

The preview page knows when to reload because the build watcher writes its state to a small file, served at `/app-status.json`. The page polls it every second and a half. It can show what the app prints because it frames the app from the same address, which lets it listen to the app's console.

The app, the build log and the status file are served without a password. Only the editor asks for one. The build log holds compiler output and nothing secret.

The file is mounted into the workspace container, not copied into the image. After editing it on the VM, run `docker compose restart workspace` and reload the Editor tab.

The same text can still be loaded as Heropa content. Learners then have it in both places.

## Updating the codelab version

The workspace image is pinned to one commit of the codelab through `CODELAB_REF` in `workspace/Dockerfile`. The files in `workspace/solutions/` were written against that commit.

To move to a newer commit, do these in order.

1. Change `CODELAB_REF` in `workspace/Dockerfile`.
2. Update the solution files to match the new codelab text, including `workspace/solutions/starter/App.kt`, which is the published starter plus the three numbered import comments. The image build copies it over the checkout and commits it.
3. Carry the changes in the new `codelabs/README.md` over to `course-manual.md`, keeping the lab text and the **Android Studio only** blocks. Update the commit in the three picture addresses and the two repository links.
4. Check the line numbers the manual quotes, and the names and imports in `fix-imports` and `codelab-hints`.
5. Rebuild the image, walk the manual again and save a new template.

## Testing without Heropa

The lab runs on any machine with Docker. This starts it on `http://localhost:8080` over plain HTTP.

```bash
docker build -t sdc-codelab-workspace:local workspace
LAB_HOST=localhost LAB_CERT=none LAB_HTTP_PORT=8080 LAB_HTTPS_PORT=8443 ./lab-init.sh
```

Browsers treat `http://localhost` as a secure page, so the editor works in full without a certificate. Do not use `LAB_CERT=self-signed` for this. The editor's web views, the guide pane among them, need a service worker, and browsers refuse to register one on a certificate they do not trust, even after you accept the warning. On Heropa the Let's Encrypt certificate is trusted, so this does not arise.

## Troubleshooting

| Symptom | Fix |
|---|---|
| The guide pane shows `Could not register service worker` | The page is on a certificate the browser does not trust. On Heropa check that the lab got its Let's Encrypt certificate. On your own machine use `LAB_CERT=none`. |
| `certbot failed` | Check the inbound rules for TCP 80 and 443, and that the VM has a public IP. |
| The App tab stays on the waiting page | Read the build with `docker exec sdc-workspace build-log`. |
| A build stops with `returns 127` | `libatomic1` is missing from the image. The Dockerfile installs it, so the image was changed. |
| The editor asks for a password nobody has | Run `./lab-init.sh` again. It prints the password. |
| A learner broke their code beyond repair | In the editor terminal run `step reset`, or on the VM run `./lab-init.sh --reset`. |
| Builds are killed or the VM swaps | The VM has less than 16 GB. Use a larger instance type. |
