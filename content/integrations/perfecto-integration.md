---
title: Perfecto integration
description: How to integrate your workflows with Perfecto using codemagic.yaml
weight: 11
---

**Perfecto** is a web-based Software as a Service (SaaS) platform that allows mobile application developers and QA Engineers to work with services such as advanced automation, monitoring and testing services. It is possible to integrate with Perfecto directly from your **codemagic.yaml**

Signing up with [Perfecto](https://www.perfecto.io/) is required in order to get credentials that are needed during an upload process. 

Usin the following cURL script in a pre-build script(a script that is run after executing build commands in yaml), **.apk**, **.aab** and **.ipa** binaries can be uploaded to the Perfecto platform:

```
curl "https://web.app.perfectomobile.com/repository/api/v1/artifacts" -H "Perfecto-Authorization: $PERFECTO_TOKEN" -H "Content-Type: multipart/form-data" -F "requestPart={\"artifactLocator\":\"PRIVATE:app.aab\",\"artifactType\":\"ANDROID\",\"override\":true}" -F "inputStream=@/path/to/app.aab"
```

**PERFECTO_TOKEN** can found in the Perfecto UI with your account. Environment variables can be added in the Codemagic web app using the ‘Environment variables’ tab. You can then and import your variable groups into your codemagic.yaml. For example, if you named your variable group ‘browserstack_credentials’, you would import it as follows:

```
workflows:
  workflow-name:
    environment:
      groups:
        - browserstack_credentials
```

For further information about using variable groups please click [here](.../variables/environment-variable-groups/).


## Test Automation

In order to automate tests, desired capabitlies can be set inside your custom made test scripts in your project. For example, if your application requires device sensors such as camera or fingerprint reader, then **sensorInstrument** needs to be set:

```
capabilities.setCapability("sensorInstrument", true);
```

With Appium tests **autoInstrument** capability automatically instrument the application and it needs to be set to true:

```
capabilities.setCapability("autoInstrument", true);
```

## Flutter apps integration

In order to set up integration for Flutter specific apps the following steps must be followed:

1. Generate a folder named **PerfectoRun** (can be named differently) in the root directory of your project.

2. Move into that directory in your **codemagic.yaml** after a build script is complete and run **gradle init** and **./gradlew wrapper**. More information about **codemagic.yaml** can be found [here](.../yaml/yaml-getting-started/):

```
- name: Build APK with Flutter
  script: |
    flutter build apk

- name: Moving into PerfectoRun directory and initiate Gradle
  script: |
     cd PerfectoRun \
     gradle init \
     ./gradlew wrapper
```
TIP: In order to avoid manual steps when exevuting **gradle init**, gradle optional flags need to be attached to the command:

```
gradle init --type java-library --dsl kotlin --incubating --test-framework junit-jupiter —project-name=PerfectoRun —package=PerfectoRun
```
3. Create a file called **config.json** and add the following Json content in there:

```
{
      "cloudURL": "web-fra.perfectomobile.com",
      "securityToken": "",
      "devices": [
       
            {
              "platformName" : "Android",
              "platformVersion": "^12.*”,
              “description”:”free”
            },
            {
              "platformName_" : "Android",
              "platformVersion_": "^11.*”,
              “description”:”free”
            }
      ],
      "jobName": "some_job",
      "jobNumber": 1,
      "branch": "some_branch",
      "projectName": "My_Flutter_project",
      "projectVersion": "v1.0",
      "tags": [
        "espresso", "plugin"  ],
      "apkPath": "YOUR_APK_PATH",
      "testApkPath": "YOUR_TEST_APK_PATH",
      "installationDetails" : {"preCleanUp" : "true"},
      "postExecution" : {"uninstall" : "false" },
      "debug": false,
      "failBuildOnFailure": false,
      "takeScreenshotOnTestFailure": true,
      "shard": false,
      "testTimeout" : 60000
    }
```

**"securityToken"** contains your Perfecto Token that can be fetched from your Perfecto account.

In order to generate **testBuildType**, the following command needs to be run in a pre-build script (a script that is run after executing build commands):

```
- name: Build Android Test release
  script: |
      ./gradlew assembleAndroidTest
```

4. In the generated **build.gradle** file inside the PerfectRun directory, the following content must be added:
```
```
5. As a last step, to successfully upload files and enable test automation, the following commands needs to be executed:

```
- name: Upload files to Perfecto and run tests
  script: |
      ./gradlew perfecto-android-inst
```

## Sample projects

A sample project that shows how to configure Perfecto integration for real device testing is available [here]()

A sample project that shows how to configure Perfecto integration for **App Automate**  for Flutter apps is available [here]()