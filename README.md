
# Setting up and Running Android Emulators on Linux

This document provides a comprehensive guide to setting up and running Android emulators on Linux without using Android Studio. It includes all necessary steps, from downloading the command line tools to creating and running an Android Virtual Device (AVD).

## Prerequisites

Ensure you have `wget` and `unzip` installed. If not, install them using your package manager (e.g., `sudo apt-get install wget unzip` on Debian-based systems).

## Step 1: Download and Extract Command Line Tools

1. **Download the Command Line Tools for Linux**:
   ```sh
   wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip
   ```

2. **Extract the ZIP file**:
   ```sh
   unzip commandlinetools-linux-11076708_latest.zip -d cmdline-tools
   mkdir -p $HOME/Android/Sdk/cmdline-tools
   mv cmdline-tools $HOME/Android/Sdk/cmdline-tools/latest
   ```

## Step 2: Set Up Environment Variables

1. **Add the following lines** to your `~/.bashrc` or `~/.zshrc` file:
   ```sh
   export ANDROID_SDK_ROOT=$HOME/Android/Sdk
   export PATH=$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$PATH
   export PATH=$ANDROID_SDK_ROOT/platform-tools:$PATH
   ```

2. **Source your profile** to apply the changes:
   ```sh
   source ~/.bashrc
   # or
   source ~/.zshrc
   ```

## Step 3: Install SDK Components

1. **Install the Platform Tools**:
   ```sh
   sdkmanager "platform-tools"
   ```

2. **Install the Emulator**:
   ```sh
   sdkmanager "emulator"
   ```

3. **Install a Platform (e.g., Android 30)**:
   ```sh
   sdkmanager "platforms;android-30"
   ```

4. **Install a System Image (e.g., for API level 30 with Google APIs)**:
   ```sh
   sdkmanager "system-images;android-30;google_apis;x86_64"
   ```

## Step 4: Create and Manage AVDs

1. **Create an AVD**:
   ```sh
   avdmanager create avd -n my_avd -k "system-images;android-30;google_apis;x86_64" -d "pixel"
   ```

2. **List Available AVDs**:
   ```sh
   emulator -list-avds
   ```

3. **Start an AVD**:
   ```sh
   emulator -avd my_avd
   ```

## Step 5: Setup Frida and Objection for Bypassing SSL Pinning

### Install Frida and Objection

1. **Install Frida**:
   ```sh
   pip install frida-tools
   ```

2. **Install Objection**:
   ```sh
   pip install objection
   ```

### Setup Frida Server on the Emulator

1. **Download the Frida server**:
   ```sh
   wget https://github.com/frida/frida/releases/download/15.1.17/frida-server-15.1.17-android-x86_64.xz
   ```

2. **Extract the Frida server**:
   ```sh
   unxz frida-server-15.1.17-android-x86_64.xz
   chmod +x frida-server-15.1.17-android-x86_64
   ```

3. **Push the Frida server to the emulator**:
   ```sh
   adb push frida-server-15.1.17-android-x86_64 /data/local/tmp/
   adb shell "chmod 755 /data/local/tmp/frida-server-15.1.17-android-x86_64"
   ```

4. **Run the Frida server**:
   ```sh
   adb shell "/data/local/tmp/frida-server-15.1.17-android-x86_64 &"
   ```

### Bypass SSL Pinning with Objection

1. **Start the application** on the emulator.

2. **List running applications**:
   ```sh
   frida-ps -U
   ```

3. **Inject Objection**:
   ```sh
   objection -g <AppName> explore
   ```

4. **Bypass SSL pinning**:
   ```sh
   android sslpinning disable
   ```

## Full Example Process

1. **Download and extract the command line tools**:
   ```sh
   wget https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip
   unzip commandlinetools-linux-11076708_latest.zip -d cmdline-tools
   mkdir -p $HOME/Android/Sdk/cmdline-tools
   mv cmdline-tools $HOME/Android/Sdk/cmdline-tools/latest
   ```

2. **Set environment variables**:
   Add to `~/.bashrc` or `~/.zshrc`:
   ```sh
   export ANDROID_SDK_ROOT=$HOME/Android/Sdk
   export PATH=$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$PATH
   export PATH=$ANDROID_SDK_ROOT/platform-tools:$PATH
   ```

   Source the profile:
   ```sh
   source ~/.bashrc
   # or
   source ~/.zshrc
   ```

3. **Install SDK components**:
   ```sh
   sdkmanager "platform-tools"
   sdkmanager "emulator"
   sdkmanager "platforms;android-30"
   sdkmanager "system-images;android-30;google_apis;x86_64"
   ```

4. **Create an AVD**:
   ```sh
   avdmanager create avd -n my_avd -k "system-images;android-30;google_apis;x86_64" -d "pixel"
   ```

5. **List available AVDs**:
   ```sh
   emulator -list-avds
   ```

6. **Start the AVD**:
   ```sh
   emulator -avd my_avd
   ```

7. **Install Frida and Objection**:
   ```sh
   pip install frida-tools
   pip install objection
   ```

8. **Download and setup Frida server**:
   ```sh
   wget https://github.com/frida/frida/releases/download/15.1.17/frida-server-15.1.17-android-x86_64.xz
   unxz frida-server-15.1.17-android-x86_64.xz
   chmod +x frida-server-15.1.17-android-x86_64
   adb push frida-server-15.1.17-android-x86_64 /data/local/tmp/
   adb shell "chmod 755 /data/local/tmp/frida-server-15.1.17-android-x86_64"
   adb shell "/data/local/tmp/frida-server-15.1.17-android-x86_64 &"
   ```

9. **Bypass SSL pinning with Objection**:
   ```sh
   objection -g <AppName> explore
   android sslpinning disable
   ```
