# Technical Guides & Troubleshooting 📖

Step-by-step instructions for bootloader unlocking, rooting, partition backups, and official OTA updates for the Nothing Phone (2a) (Pacman).

---

## General Use & Troubleshooting

### OTA Sideloading & Official System Updates
This guide describes how to update Nothing OS safely while maintaining root, or how to sideload official updates using the built-in offline updater on a locked/unlocked bootloader.

:::note
- Sideloading official incremental or full OTA updates is completely safe as long as they are downloaded directly from an official Nothing source.
- The built-in Nothing OS offline updater only accepts OEM-signed update packages. It verifies the firmware hash before installation and will fail if an incorrect or mismatched OTA zip is used.
- Because of these cryptographic checks, it is impossible to brick your device by sideloading an official OTA package.
:::

#### A. Restoring Stock Partitions (For Rooted Users Only)
*If your device is unrooted with a locked bootloader, skip directly to Section B.*

1. **Verify your current OS Build Number:**
   * Go to `Settings > About phone > Tap the device banner` and note down the exact build number.
2. **Fetch Stock Boot Images:**
   * Download the unmodified `stock-boot.img` or `stock-init_boot.img` matching your current active build from your repository's Releases page.
3. **Flash the Unmodified Stock Images:**
   * Reboot your phone to Fastboot mode:
     ```bash
     adb reboot bootloader
     ```
   * Flash the stock partitions to restore system integrity (only modified partitions need to be flashed):
     ```bash
     fastboot flash boot stock-boot.img
     fastboot flash init_boot stock-init_boot.img
     ```
4. **Reboot to System:**
   * Reboot your device normally. You will temporarily lose root access, which is required to allow the updater to modify the partitions during the update:
     ```bash
     fastboot reboot
     ```

---

#### B. Sideloading the Update (Systemless OTA)
1. **Download the Correct Update Package:**
   * Obtain the official incremental or full OTA `.zip` firmware package for your target Nothing OS version.
2. **Create the OTA Directory:**
   * Create a folder named `ota` in the root of your device's internal storage. The exact path must be:
     ```text
     /sdcard/ota/
     ```
   * Move the downloaded `<firmware_update>.zip` file into this `/sdcard/ota/` folder.
3. **Access the Offline OTA Updater:**
   * Open the stock **Phone dialer app** and dial:
     ```text
     *#*#682#*#*
     ```
   * This will launch the built-in offline updater utility interface (labeled as *NothingOfflineOtaUpdate* or *NOTHING BETA OTA UPDATE*).
4. **Apply the Update:**
   * The updater tool will automatically scan and detect the update package inside the `ota` folder.
   * If it does not show up automatically, use the **Browse** option inside the app to manually select the zip file.
   * Tap **Directly Apply OTA** (or **Update**) to initiate the process.
   * Wait for the verification and installation progress to finish. The phone will reboot automatically into the new OS version.
5. **Re-Root the Updated OS (Optional):**
   * Once your phone successfully boots into the newly updated Nothing OS version, download the newly generated patched images from your GitHub Release page matching the new version.
   * Flash them using the rooting steps in the Advanced section below to restore root access.

---

## Advanced Guides

### Prerequisites & Tools

#### 1. USB Drivers
* **Windows Users:** Install the [Google USB Driver](https://developer.android.com/studio/run/win-usb) to allow ADB and Fastboot interfaces to communicate with your device.

#### 2. SDK Platform Tools (ADB & Fastboot)
* Always use the latest official [Google SDK Platform-Tools](https://developer.android.com/studio/releases/platform-tools) on Windows, macOS, or Linux. Do not use outdated third-party fastboot installers as they often fail to write modern Android 13/14 GKI partition schemes.

---

### Unlocking the Bootloader
Unlocking the bootloader is a mandatory prerequisite before you can flash any patched root partition images or custom recovery zips.

:::warning
Unlocking the bootloader will trigger a factory reset. All personal files, apps, and accounts on the device internal storage will be wiped. Back up your essential data before proceeding.
:::

1. **Enable Developer Options:**
   * Go to `Settings > About phone > Tap Software info > Tap Build number 7 times` until a toast notification appears saying you are a developer.
2. **Enable OEM Unlocking & USB Debugging:**
   * Go to `Settings > System > Developer options`.
   * Turn ON **OEM Unlocking** and **USB Debugging**.
3. **Reboot to Bootloader:**
   * Connect the device to your PC and execute:
     ```bash
     adb reboot bootloader
     ```
4. **Initiate Bootloader Unlock Command:**
   * Execute the following command in terminal/command prompt:
     ```bash
     fastboot flashing unlock
     ```
5. **Confirm on Device:**
   * Use the `Volume` keys on your phone to select the **"Unlock the bootloader"** option, then press the `Power` button to confirm.
   * The device will reboot, wipe all userdata, and show a unlocked bootloader warning logo on boot.

---

### Rooting Guides

#### Method 1: APatch or FolkPatch (Modifying boot.img)
APatch and FolkPatch utilize the KernelPatch framework, which directly modifies the Linux kernel inside the main `boot` partition.

1. **Reboot to Fastboot:**
   * Reboot your phone into **Fastboot Mode** (Power + Volume Down buttons during startup, or via ADB).
2. **Flash the Patched Boot Image:**
   * Execute the command corresponding to your chosen manager:
     * **For APatch:**
       ```bash
       fastboot flash boot APatch-boot.img
       ```
     * **For FolkPatch:**
       ```bash
       fastboot flash boot FolkPatch-boot.img
       ```
3. **Reboot to System:**
   * Reboot your phone back to Nothing OS:
     ```bash
     fastboot reboot
     ```
4. **Install Manager APK:**
   * Download and install the respective stable manager APK from the Releases page.
   * **APatch Setup:** Open the app and enter the defined SuperKey password: `root@123`.
   * **FolkPatch Setup:** Open the app; root privileges are automatically managed using the default `su` interface.

---

#### Method 2: KernelSU, KernelSU-Next, or SukiSU-Ultra (Modifying init_boot.img)
GKI-based kernel roots are loaded as Loadable Kernel Modules (LKM). On MediaTek devices like Nothing Phone (2a), the modules are injected into the ramdisk housed inside the **`init_boot`** partition.

1. **Reboot to Fastboot:**
   * Reboot your phone into **Fastboot Mode**.
2. **Flash the Patched Init_Boot Image:**
   * Execute the command corresponding to your preferred manager package:
     * **For KernelSU (Official):**
       ```bash
       fastboot flash init_boot KernelSU-init_boot.img
       ```
     * **For KernelSU-Next (Normal APK):**
       ```bash
       fastboot flash init_boot KernelSU-Next-Normal-init_boot.img
       ```
     * **For KernelSU-Next (Spoofed APK):**
       ```bash
       fastboot flash init_boot KernelSU-Next-Spoof-init_boot.img
       ```
     * **For SukiSU-Ultra:**
       ```bash
       fastboot flash init_boot SukiSU-Ultra-init_boot.img
       ```
3. **Reboot to System:**
   * Reboot your phone back to Nothing OS:
     ```bash
     fastboot reboot
     ```
4. **Install Manager APK:**
   * Install the matching stable manager APK from the Release assets (Normal or Spoofed depending on the image flashed). Your device is now fully rooted.

---

### Backing Up Essential Partitions
It is highly recommended to backup critical system partitions before executing any system modifications.

1. **Boot into FastbootD Mode:**
   * Enter the user-space fastboot mode by running:
     ```bash
     fastboot reboot fastboot
     ```
2. **Dump Partitions recursively (If custom recovery is installed):**
   * Use ADB commands inside an active custom recovery (like OrangeFox/TWRP) terminal to dump partitions safely:
     ```bash
     dd if=/dev/block/by-name/boot of=/sdcard/boot.img
     dd if=/dev/block/by-name/init_boot of=/sdcard/init_boot.img
     ```

---

### Relocking the Bootloader
To restore your phone to full stock factory settings, pass Play Integrity verification natively, and lock the system partition block.

:::warning
Relocking the bootloader requires 100% untouched stock partitions. Do not execute this command if you have a modified kernel, patched boot/init_boot image, or custom ROM installed, as it will result in a hardbrick.
:::

1. **Flash Stock Partition Backup Images:**
   * Ensure both unmodified images are flashed first in fastboot mode:
     ```bash
     fastboot flash boot stock-boot.img
     fastboot flash init_boot stock-init_boot.img
     ```
2. **Execute Relock Command:**
   * While in bootloader mode, run:
     ```bash
     fastboot flashing lock
     ```
3. **Confirm on Device:**
   * Use the `Volume` buttons to select the **"Lock the bootloader"** option, and press the `Power` button to confirm. The device will reset and boot back to pristine stock Nothing OS.
