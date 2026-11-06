# JARVIS-Android.

name: Build JARVIS APK

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Extract JARVIS project
        run: |
          mkdir -p temp
          unzip -q JARVIS_Android_GitHub_Build_Starter.zip -d temp
          cp -a temp/JARVIS-Android/. .

      - name: Setup Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.7'

      - name: Build APK
        run: |
          if [ -f gradlew ]; then
            chmod +x gradlew
            ./gradlew assembleDebug
          else
            gradle assembleDebug
          fi

      - name: Upload JARVIS APK
        uses: actions/upload-artifact@v4
        with:
          name: JARVIS-APK
          path: app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
