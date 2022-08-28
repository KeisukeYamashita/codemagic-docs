---
title: Capgo integration
description: How to integrate your workflows with Capgo using codemagic.yaml
weight: 7
---

## Getting Started

[Capgo](https://capgo.app/) is an instant updater for Capacitor apps and allows you user to try beta version without the need of TestFlight, or Google beta process. It is possible to integrate the flow with **Codemagic**.

In order to get live updates in your Capgo account via Codemagic, you will need to follow the steps:

1. Sign up with Capgo to get your login token
2. Add the foolowing part in your **capacitor.config.json**
```
"plugins": {
        "CapacitorUpdater": {
            "autoUpdate": true
        }
```
3. Inside your main app source, import the following package:
```
import { CapacitorUpdater } from '@capgo/capacitor-updater'

CapacitorUpdater.notifyAppReady()
```
4. Install **capacitor-updater** by running the following commands in your **codemagic.yaml**
```
    - name: Install npm dependencies for Ionic project
      script: |
        npm install @capgo/capacitor-updater
        npx @capgo/cli login $CAPGO_TOKEN
```
**$CAPGO_TOKEN** is your login token and can be found under your Capgo account. Environment variables can be added in the Codemagic web app using the ‘Environment variables’ tab. You can then and import your variable groups into your codemagic.yaml. For example, if you named your variable group ‘browserstack_credentials’, you would import it as follows:

```
workflows:
  workflow-name:
    environment:
      groups:
        - browserstack_credentials

5. Run **npx cap sync**:
```
    - name: Update dependencies and copy web assets to native project
      script: |
        npx cap sync
```

6. Upload your project to Capgo:
```
    - name: Capgo
      script: |
        npx @capgo/cli add 
        npx @capgo/cli upload
```
When uploading app versions to **Capgo**, executing the following command will submit updates to all user (if production channel is set to public):
```
npx @capgo/cli@latest upload -c production
```

As soon as users start installing app versions on their devices, a device list will be visible in the Capgo UI and then any of them can be pivked in order to let specific groupd of users know about updates shipped with version uploads.

## Sample projects

A sample project that shows how to configure Capgo integration is available [here](https://github.com/codemagic-ci-cd/codemagic-sample-projects/tree/main/integrations/capgo_integration_demo_project)
