# Modding Android: A Plain Guide

By Razon

Written November 2026 for Android 13 and newer. Windows and Linux. Manufacturer policies and tool versions change often, so treat any version number or policy in this guide as a snapshot and check the linked official page before you act.

## Read this first

Modding a phone means changing software that the manufacturer wanted left alone. Most of it is safe if you go slowly. Some of it can leave you with a dead phone. Here is what can actually go wrong.

- **Warranty.** Unlocking the bootloader voids the warranty in many regions. On Samsung it also blows a hardware fuse (Knox) that cannot be reset.
- **Data loss.** Unlocking the bootloader wipes the phone. Everything on internal storage is gone, and there is no way to get it back afterwards.
- **Bricking.** Flashing a file built for a different model, interrupting a write, or relocking a bootloader while the software is modified can leave the phone unable to start. Most of these cases are recoverable. A few are not.
- **Apps that stop working.** Banking apps, Google Wallet, and some games check whether your phone has been tampered with. An unlocked bootloader or root can make them refuse to run.
- **No liability.** This guide is for education. You follow it at your own risk, and neither the author nor any publisher is responsible for what happens to your phone.

Phones differ a lot. Key combinations, partition names, and flashing steps change between brands and even between models of the same brand. Use this guide for the general method, and use your device's own page on XDA Forums or the LineageOS wiki for the exact steps.

**What this guide will not cover.** Bypassing carrier SIM locks, defeating Factory Reset Protection or activation locks on phones that are not yours, changing or spoofing IMEI and serial numbers, and pirating paid apps. Those are either illegal or aimed at stolen devices, and they have nothing to do with modding a phone you own. Changing the software on your own phone is legal in most countries, but laws and carrier contracts vary, and none of this is legal advice.

## How to use this guide

You do not have to read it in order. Pick the description that fits you.

**You want a nicer phone and nothing else.** Read Parts 1 and 3. You never unlock anything, nothing is wiped, and the warranty stays intact.

**You want to keep your current Android but have full control of it.** Read Parts 1 and 2, then Chapters 10 and 12. You unlock the bootloader and root the stock system.

**You want a different operating system.** Read everything through Part 4. This is the path for phones the manufacturer has stopped updating.

**Your phone is stuck right now.** Go straight to Chapters 16 and 17.

Technical terms are explained where they first appear and collected in the glossary at the end. Commands appear in code blocks and must be typed exactly, including capitalisation and spaces. Four labels recur: **Warning** for anything that wipes data or can brick the phone, **Tip** for shortcuts, **Note** for background, and **If it goes wrong** for recovery steps.

---

# Part 1: What you are dealing with

## Chapter 1: What modding means

"Modding" covers three quite different activities. People mix them up constantly, and the confusion leads to a lot of unnecessary risk, so it is worth separating them.

**Level 1: customising without root.** You change how the phone looks and behaves using ordinary apps and one built-in developer tool. A launcher replaces your home screen and app drawer. Icon packs and widgets change the look. ADB, Google's debugging tool, lets you disable apps the manufacturer pre-installed. None of this touches the bootloader, so nothing is wiped and the warranty is unaffected.

**Level 2: rooting.** Android normally runs apps with limited permissions so that a bad app cannot rewrite the system. Root is an administrator account that removes that limit. You keep the operating system the phone came with, but apps you approve can read and change almost anything. That allows system-wide ad blocking, full app-data backups, and modules that alter system behaviour. It requires an unlocked bootloader.

**Level 3: a custom ROM.** A ROM is the complete operating system: the lock screen, settings, notification shade, and the software underneath. Flashing a custom ROM, such as LineageOS or crDroid, writes a different build of Android onto the phone in place of the manufacturer's. It also requires an unlocked bootloader. Its main use is keeping a phone current after the manufacturer stops issuing updates.

|  | No-root customising | Rooting | Custom ROM |
| --- | --- | --- | --- |
| What changes | Home screen, icons, widgets, disabled apps | You gain administrator rights in your current OS | The entire OS is replaced |
| Bootloader unlock | No | Yes, wipes data | Yes, wipes data |
| Warranty | Unaffected | Often voided | Often voided |
| Banking and wallet apps | Work normally | Need workarounds, may fail | Need workarounds, may fail |
| Newer Android version | No | No | Often yes |
| Risk | Very low | Low to moderate | Moderate |

**Tip.** You do not need both. A lot of beginners assume a custom ROM requires root, or that root requires a custom ROM. Neither is true. You can root the stock system, and you can run a custom ROM unrooted. Many people do the second and are happier for it.

A fair amount of what people want from a "mod" is just Level 1. If the real complaint is an ugly home screen or pre-installed junk, Part 3 solves it without any of the risks in Part 4.

## Chapter 2: Should you do it?

Be honest with yourself about what you need the phone for. Then weigh the following.

### What you gain

**Longer life for old hardware.** Manufacturers often stop updates after three to five years, long after the processor stops being the problem. Projects like LineageOS keep supporting phones well beyond that, and some run a current Android release on phones that stopped getting official updates years earlier.

**Fewer pre-installed apps and less telemetry.** A custom ROM or a debloated stock system removes duplicate browsers, shopping apps, and background services. You can go further and run without Google services at all, using GrapheneOS or a ROM with microG.

**Root-only tools.** System-wide ad blocking through the hosts file, audio processing across the whole system, charge limits, and complete backups of app data, including login state and local databases that normal backups skip.

### What it costs you

**Play Integrity.** Google's Play Integrity API is the successor to SafetyNet. Apps ask it whether the phone looks genuine, and it answers at three levels:

- *Basic integrity* passes on most phones, including many with unlocked bootloaders, as long as nothing obvious is wrong.
- *Device integrity* means the device is certified and not obviously modified. Unlocked or rooted phones usually need a workaround module to pass.
- *Strong integrity* depends on hardware-backed proof that the bootloader is locked and the software is unmodified. An unlocked bootloader cannot truthfully pass this, and any method that appears to pass it relies on borrowed credentials that Google revokes from time to time.

In practice: some banking apps and Google Wallet will work after setup, some will break after an update, and some will never work. If your money depends on one phone, keep that phone clean. Section 12 explains what can be done and where it stops.

**Samsung Knox.** On Galaxy phones, unlocking the bootloader sets a hardware fuse called the Knox warranty bit from 0x0 to 0x1. Relocking does not reset it. Samsung Wallet, Samsung Pass, and Secure Folder will not work on a phone with a tripped fuse, and a custom ROM will not bring them back.

**Updates.** Automatic over-the-air (OTA) updates assume an unmodified system. With root or a modified partition they will often fail or overwrite your changes. On a custom ROM you update through that ROM's own mechanism instead, and Xiaomi's HyperOS has had restrictions on official updates for unlocked phones. Plan to update manually.

**Cameras and manufacturer features.** Phone makers tune their camera processing to their own software. On a non-Pixel phone running AOSP-based software, photos can be noticeably worse, and features like fast charging profiles, always-on display extras, or proprietary AI tools may be missing. Look for reports about your exact model before committing.

**Your time.** Setting everything up again after the wipe takes an evening. Every major update on a rooted phone costs another half hour.

**Note.** If you cannot afford a day without your phone, do not start on that phone. Use a spare. A used Pixel or OnePlus that you can afford to break is the best teacher. Do your first unlock and first ROM flash on it.

### A short way to decide

If the phone is your only phone and you rely on banking apps: stay with Part 3. If you like your current Android version and just want control: root the stock system. If the phone is out of support or you dislike the manufacturer's software: choose a custom ROM.

## Chapter 3: How the phone starts, and where things live

Understanding a few partition names will save you a lot of confusion later, because every flashing instruction mentions them.

A phone's storage is split into partitions, each with a job. When one is damaged, the rest usually survive, which is why most software problems are recoverable.

**Bootloader.** The first program that runs when you press power. It checks digital signatures on the next stage, a process called Android Verified Boot (AVB). With a locked bootloader, only software signed by the manufacturer is allowed to start. Unlocking it tells the bootloader to accept other software too. That is the entire meaning of "unlocked."

**boot and init\_boot.** The boot partition holds the kernel, the core of the operating system that talks to the hardware. On phones that launched with Android 13 or later, the small ramdisk of startup files was moved into a separate partition called init\_boot. This matters for rooting. If your phone's factory image contains an init\_boot.img file, that is the file you patch. If it does not, you patch boot.img.

**vendor\_boot.** On newer phones this carries hardware-specific startup pieces, and on some devices it is where a custom recovery is installed.

**recovery.** A separate small system used for factory resets and installing updates. The stock one is limited. A custom recovery, such as TWRP or OrangeFox, or the recovery supplied by a ROM project, lets you install ROMs and manage partitions. On many newer phones the recovery is not a separate partition at all, but part of boot or vendor\_boot, which is why Chapter 11 says to follow your device's page rather than guess.

**super.** A single container that holds system, vendor, and product as resizable logical partitions. They are read-only while Android runs.

- *system* is Android itself and the built-in apps.
- *vendor* contains the drivers for camera, modem, GPU, and so on.
- *product* carries manufacturer extras.

**userdata.** Your apps, photos, and settings. A factory reset wipes this and nothing else.

**efs, modemst1, modemst2, fsg, persist.** Small partitions holding your IMEI, radio calibration, Wi-Fi and Bluetooth addresses, and sensor calibration such as the fingerprint reader. The names vary by chipset. These are specific to your individual phone and cannot be downloaded from anywhere, so back them up (Chapter 6) and never wipe them.

### A/B slots

Most modern phones keep two copies of the system partitions, named with the suffixes \_a and \_b. You run from one while updates install quietly to the other. On reboot the bootloader switches slots. If the new slot fails to start after several tries, it switches back, which is why a failed update on a Pixel rarely bricks it.

For modders, two consequences matter. When you sideload a ROM in recovery, it is written to the slot you are not currently running, and the phone then switches to it. And you can sometimes rescue a phone that will not boot by switching back to the old slot (Chapter 16).

Older phones and many Samsung models are A-only: one set of partitions, no fallback.

**Recap.** The bootloader checks signatures and unlocking it allows custom software. The kernel lives in boot, and on Android 13-era phones the startup ramdisk lives in init\_boot. The OS lives in super. A/B phones keep two copies of the system. The efs and persist partitions are unique to your phone and irreplaceable.

---

# Part 2: Getting ready

## Chapter 4: Choosing a phone

The single biggest factor in how easy modding will be is the brand and the exact variant you own. Some manufacturers publish unlock procedures and firmware. Others make unlocking difficult or impossible.

| Brand | How easy | Unlocking |
| --- | --- | --- |
| Google Pixel | Easiest | Toggle OEM unlocking, then one fastboot command. Phones sold by Verizon and some other US carriers have a permanently locked bootloader. Google's Android Flash Tool makes recovery from mistakes simple, and GrapheneOS can relock safely on Pixels. |
| OnePlus and Nothing | Easy | One fastboot command on international and unlocked models. Some carrier variants need an unlock token. Large ROM community. |
| Motorola | Moderate | Official portal: you run a fastboot command, paste the output into Motorola's site, and receive an unlock code by email. Carrier-locked variants, especially in the US, are not eligible. |
| Xiaomi, POCO, Redmi | Moderate to difficult | Requires a Xiaomi account, the Mi Unlock tool on Windows, and a waiting period. Xiaomi sets limits on new accounts, and Chinese-region units are restricted. Rules change often, so read Xiaomi's current instructions. |
| Samsung | Split | International models can be unlocked in Download Mode. US and Canadian models generally cannot. Unlocking trips Knox permanently. |
| Huawei, Honor, Oppo, Vivo, Asus | Mostly closed | Official unlock programmes have been discontinued or restricted. Stay with Part 3. |

The chip matters too. Qualcomm Snapdragon phones usually have the best ROM and kernel support. MediaTek and Exynos phones are harder to work with and have fewer maintained ROMs.

### Check that your phone can be unlocked before you commit

1. Connect to Wi-Fi and insert the SIM if the phone requires it.
2. Open Settings, then About phone, and tap Build number seven times. On Xiaomi HyperOS, tap OS version instead. On Samsung, it is under Software information. Enter your PIN when asked.
3. Open Developer options. It is under System on stock Android and under Additional settings on some Xiaomi phones.
4. Look for OEM unlocking.

If it can be switched on, the phone supports bootloader unlocking. If it is greyed out, the phone may be carrier-locked, or it may be waiting for a network check-in (try again after a day connected to Wi-Fi). On Samsung phones, the toggle can be hidden for the first seven days after setup. If the option is missing entirely, the bootloader cannot be unlocked.

**Warning: SIM unlocked and bootloader unlocked are different things.** A phone that accepts any carrier's SIM may still have a bootloader that is locked forever. Sellers use the word "unlocked" loosely. Always check the OEM unlocking toggle yourself before buying a used phone for modding.

## Chapter 5: Your computer setup

You need two tools from Google. ADB (Android Debug Bridge) talks to a phone while Android is running or while it is in recovery. Fastboot talks to the bootloader while Android is not running. Both come in one download called SDK Platform-Tools.

**Warning: do not use "minimal ADB and Fastboot" installers.** Older packages bundled with third-party installers are years out of date and do not handle dynamic partitions or init\_boot correctly. Download Platform-Tools from developer.android.com/tools/releases/platform-tools. Check its version number against that page; it is updated regularly.

### Windows

1. Download the Windows ZIP from Google and extract it, for example to C:\\platform-tools.
2. Install the USB driver for your phone. Google's USB driver covers Pixels. Other brands provide their own.
3. Open the folder in File Explorer, click the address bar, type cmd, and press Enter. The Command Prompt opens in that folder.

In PowerShell, commands in the current folder need a prefix, such as .\\adb devices.

### Linux (Ubuntu, Debian, Mint)

Linux needs no drivers, but it needs permission rules so that your user account may talk to the phone without sudo.

```
sudo apt update
sudo apt install android-sdk-platform-tools-common
```

That package supplies the udev rules. Then download Google's Linux ZIP, extract it into your home folder, and either run the tools from that folder with ./adb or add the folder to your PATH. Log out and back in if the phone is still not detected.

### First connection

1. On the phone, turn on USB debugging in Developer options.
2. Connect with a cable that carries data. Many cables bundled with chargers, earbuds, and power banks are charge-only.
3. Run:

```
adb devices
```

A prompt appears on the phone asking whether to trust this computer. Tick Always allow and tap Allow. Run the command again. You should see your serial number followed by the word device.

**If it goes wrong.**

- *It says unauthorized.* You missed the prompt. Unplug, open Developer options, choose Revoke USB debugging authorisations, and reconnect.
- *Nothing appears.* Most often it is a charge-only cable. Change the cable, and set the USB mode on the phone to File transfer. Try a different port, preferably USB 2.0 if the USB-C port is picky.
- *Fastboot says waiting for any device (Windows).* Open Device Manager with the phone in fastboot mode. If Android has a warning triangle, right-click it, choose Update driver, Browse my computer, Let me pick, and select Android Bootloader Interface.

## Chapter 6: Backups

Backups are not an optional step here. The bootloader unlock wipes the phone and destroys the encryption key as part of the wipe, so nothing on the phone can be recovered afterwards by any software.

Work through these four layers before you touch anything.

**1. Two-factor codes.** Do this first. Open your authenticator app and export a backup, or confirm that it syncs to a cloud account you can still reach. Save the recovery codes for your important accounts, such as email and code hosting, somewhere that is not the phone. Losing the authenticator can lock you out of the accounts you need to recover everything else.

**2. Photos, video, documents, music.** Connect in File transfer mode and copy the DCIM, Pictures, Download, Documents, and Music folders to your computer. Or use ADB, which also copies hidden files:

```
mkdir -p ~/android_sdcard_backup
adb pull /sdcard/ ~/android_sdcard_backup/
```

On Windows, create a folder first and substitute its path.

**3. Messages, calls, contacts, chat history.** Run a manual backup in WhatsApp and in Signal (and write down Signal's backup passphrase). Run the system backup in Settings. For SMS and call logs, an app such as SMS Backup & Restore can export a file, which you then copy to the computer.

**4. The firmware.** Download the complete factory firmware for your exact model and exact current build number, and keep it on your computer. You will take your boot or init\_boot image from it for rooting, and it is the path back to normal if something fails (Chapter 17).

**Warning: your IMEI and sensor calibration.** The efs, modemst, fsg, and persist partitions are written at the factory for your individual phone. Once you have a custom recovery or root, back them up as the very first thing you do and copy the backup to your computer and to a second place. Some phones will not accept these partitions being restored from a different device, and no download can recreate them. Backing them up costs a few minutes and has saved many people from a phone with no cellular connection.

**Recap.** Unlocking wipes everything permanently. Export 2FA and chat backups first. Download exact factory firmware in advance. Back up efs and persist as soon as you can.

---

# Part 3: Mods that need no root

Everything in this part works on a locked bootloader. Nothing is wiped, the warranty is untouched, and banking apps behave normally. If you only read one part of this guide, make it this one.

## Chapter 7: Launchers, icons, widgets

The home screen, dock, and app drawer are an ordinary app called a launcher. You can install another one and set it as the default in a couple of minutes.

**Lawnchair** is open source and looks like the Pixel launcher, with extra control over the icon grid, labels, and dock. **Niagara** replaces the grid with a single list down the side of the screen, which suits one-handed use. **Kvaesitso** is an open-source launcher built around search. **Olauncher** is a very plain text-only launcher that makes the phone boring on purpose. Nova was the default recommendation for years, but check its current maintenance status before relying on it.

To switch:

1. Install the launcher and, if you want one, an icon pack such as Lawnicons or Arcticons. F-Droid and the Play Store both carry them.
2. Open Settings, then Apps, then Default apps, then Home app, and choose the new launcher. The path varies by brand; searching Settings for "home app" finds it.
3. Press the home button. In the launcher's own settings, apply the icon pack.

If you later want the old home screen back, choose the stock launcher in the same menu.

**Widgets.** Since Android 12, Material You takes colours from your wallpaper and applies them to system surfaces and supporting apps. For more control, KWGT (Kustom Widget Maker) lets you build or import widgets showing battery temperature, calendar entries, music controls, and similar, with no root.

**Samsung.** Galaxy phones have an official customisation suite called Good Lock, available in the Galaxy Store in supported regions. Its modules change the volume panel, icon shapes, navigation gestures, and lock screen. A good deal of what once required root on Samsung is in there.

## Chapter 8: Developer options and removing pre-installed apps

### Animation speed

In Developer options, under Drawing, you will find Window animation scale, Transition animation scale, and Animator duration scale. Set each to 0.5x, or switch them off. This does not make the processor faster. It shortens the transitions, so the phone feels quicker. That is worth doing, but it is not a performance fix.

### Removing apps you cannot uninstall

Manufacturers put some apps in a read-only partition, so the uninstall button is missing. You can still remove them for your own user account over ADB, without root. The app file stays on the system partition, but it stops running, disappears, and uses no memory or battery.

First list packages matching a name:

```
adb shell pm list packages | grep -i facebook
```

On Windows PowerShell, use `findstr /i facebook` instead of grep.

Then choose one of these. Disabling is the gentlest. Uninstalling for user 0 is more thorough and just as reversible.

```
adb shell pm disable-user --user 0 com.example.package
adb shell pm uninstall -k --user 0 com.example.package
```

To bring an app back:

```
adb shell cmd package install-existing com.example.package
adb shell pm enable com.example.package
```

A factory reset also restores everything you removed this way.

Guessing package names is how people end up with a phone that will not boot. Use a curated list. **Universal Android Debloater Next Generation (UAD-ng)** is a free desktop program for Windows and Linux that connects over ADB and labels packages by how safe they are to remove: Recommended, Advanced, Expert, and Unsafe. Stay in Recommended until you know what a package does.

**Warning.** Do not remove the system UI, the phone app, the keyboard, the only launcher you have installed, the WebView component, or anything with "securitycenter", "setupwizard", or "carrier" in the name unless a trusted source for your exact model says it is safe. Removing the wrong one can cause a black screen or a bootloop, and you may need a factory reset from stock recovery. Remove a few apps, reboot, use the phone for a day, then remove a few more.

## Chapter 9: Shizuku, automation, and safer app sources

**Shizuku.** Android 11 and later include Wireless debugging in Developer options. Shizuku uses it to run a small service on the phone that gives chosen apps access to privileged system functions. That means app freezers, on-phone debloaters, and permission managers, with no computer after the first setup, no root, and no unlocked bootloader.

To set it up:

1. Install Shizuku from the Play Store or GitHub and connect the phone to Wi-Fi.
2. In Developer options, turn on Wireless debugging and open its menu.
3. Choose Pair device with pairing code. A six-digit code appears.
4. In Shizuku, tap Pairing under the wireless debugging section and enter the code in the notification that appears.
5. Return to Shizuku and tap Start.

Shizuku stops when the phone restarts, and it needs Wi-Fi to start again. That is the main limit.

| Tool | What it does |
| --- | --- |
| Canta (needs Shizuku) | On-phone debloater with the same safety ratings as UAD-ng. |
| Hail or Ice Box | Freezes apps so they cannot run in the background until you open them. |
| MacroDroid, Tasker | Automation: "when this happens, do that." Switch settings by place or time, or react to Bluetooth devices. |
| F-Droid | Store for free and open-source apps, built from source. |
| Obtainium | Tracks releases on GitHub and similar sites, so you can keep tools updated straight from the developer. |

**Blocking ads for the whole phone without root.** In Settings, search for Private DNS, choose the hostname option, and enter the hostname of a filtering DNS service, such as dns.adguard-dns.com, or your own NextDNS address. Ads and trackers on blocked domains stop loading in every app. The limit is that it works at the domain level. Ads served from the same domain as the content, as with some video apps, are not affected.

**Recap.** Launchers, icon packs, widgets, ADB, Shizuku, and Private DNS give you most of what casual users want, at no risk to warranty, data, or banking apps. Disable or remove apps with ADB through a vetted list, a few at a time.

---

# Part 4: Unlocking, recovery, root, and ROMs

From here on you are changing what the bootloader will accept. Everything in Part 2 now applies in full: have your backups, the exact firmware, and a charged phone.

## Chapter 10: Unlocking the bootloader

**Warning: this wipes the phone.** Before you run anything:

- Finish all four backup layers from Chapter 6.
- Charge to at least 60 per cent.
- Update to the latest stock firmware first. Several ROMs require it, and updating after unlocking is harder.
- Know the password of the Google account on the phone. After the wipe, Factory Reset Protection can ask for it during setup. If you do not remember it, remove the account in Settings before unlocking, then set it up again afterwards. This is for your own phone only.

### The standard procedure (Pixel, OnePlus, Nothing, and most others)

1. In Developer options, switch on OEM unlocking and USB debugging.
2. Connect to the computer and open a terminal in the platform-tools folder.
3. Reboot into the bootloader:

```
adb reboot bootloader
```

4. Confirm the computer sees the phone:

```
fastboot devices
```

5. Send the unlock command:

```
fastboot flashing unlock
```

Older phones may require `fastboot oem unlock` instead.

6. On the phone, use the volume keys to select the unlock option and the power button to confirm. The phone erases userdata and reboots to the setup screen, or waits in fastboot until you run `fastboot reboot`.

### Brand differences

**Google Pixel.** Exactly the procedure above. Models sold through Verizon and some other carriers have a bootloader that cannot be unlocked, so check the OEM unlocking toggle.

**OnePlus.** The same procedure on international, Indian, and European models. Some carrier variants, such as older T-Mobile units, need an unlock token from OnePlus.

**Motorola.** Run `fastboot oem get_unlock_data`. The output comes in several lines. Join them into one string with no spaces or line breaks, paste it into Motorola's unlock portal, and Motorola emails a unlock code. Then run `fastboot oem unlock CODE`. Retail-unlocked phones qualify. Carrier-locked ones generally do not, and unlocking voids the warranty at the moment you request the code.

**Xiaomi, POCO, Redmi.** You need a Xiaomi account, an application through Xiaomi's official process, the phone bound to the account in Developer options (Mi Unlock status), and the Mi Unlock tool on Windows. Xiaomi has applied waiting periods, account-age rules, and daily quotas, and these have changed several times, so follow Xiaomi's current instructions and the XDA thread for your model rather than any fixed numbers.

**Samsung (international models).** Samsung does not use fastboot. There is a Download Mode, and unlocking goes like this:

1. Switch on OEM unlocking in Developer options. If the option is missing, the phone may be too new to the account; connect to Wi-Fi and wait up to seven days.
2. Power off, hold Volume Up and Volume Down, and plug in a USB cable.
3. At the warning screen, long-press Volume Up to enter the device unlock menu, then confirm.

The phone wipes itself, and the Knox fuse is tripped. US and Canadian Snapdragon models cannot be unlocked at all.

**Note: the warning at every start.** After unlocking, the phone shows a message at power-on saying the bootloader is unlocked and software cannot be verified. It appears on every unlocked phone, lasts a few seconds, and is normal. Do not press anything unless the screen offers a choice you understand.

## Chapter 11: Custom recovery

The stock recovery can factory reset and install the manufacturer's own updates. A custom recovery adds the ability to install ROM packages, back up partitions, and work with files when Android does not start.

**TWRP** has been the standard for a decade, with a touch interface and full backups. It is not supported on every newer phone. **OrangeFox** is TWRP-based and popular on Xiaomi, POCO, and Redmi. **ROM-supplied recoveries** are used by LineageOS and similar projects on many recent phones. Encryption and partition layouts change with each Android release, so these recoveries are built to match the ROM and are the safer choice when the ROM project provides one.

How you install a recovery depends on where the phone keeps it. Your device page will say which:

1. **A dedicated recovery partition.** Flash the image there.
2. **Recovery inside boot.** Some phones have no separate recovery. The instructions will tell you to boot the image once from your computer and then install it from inside it. Never flash a recovery image to the boot partition unless instructions say to, because you overwrite the kernel.
3. **Recovery inside vendor\_boot.** Common on newer phones. The ROM project supplies a vendor\_boot or boot image to flash.

The commands, to be used only when your device instructions call for them:

```
fastboot boot recovery.img
fastboot flash recovery recovery.img
fastboot flash vendor_boot vendor_boot.img
fastboot reboot recovery
```

The first command starts the recovery from the computer without writing anything to the phone, which makes it the safest way to try it.

**Warning: boot into the recovery at once.** If you flash a recovery while still on stock software and let the phone start normally, the stock system can detect the change and put the original recovery back. Go straight into the recovery with `fastboot reboot recovery` or the key combination for your phone, before Android starts.

## Chapter 12: Root

Older methods modified the system partition directly, which broke updates and was easy for apps to detect. Current methods are systemless. They patch the startup image and apply their changes in memory, leaving the system partition as it was.

|  | Magisk | KernelSU | APatch |
| --- | --- | --- | --- |
| How it works | Patches the ramdisk in init\_boot or boot | Root built into the kernel | Patches the kernel image |
| Phones | Almost all unlocked phones | GKI devices (kernel 5.10 and later) or a custom kernel with KernelSU built in | Many ARM64 phones |
| Hiding root | Needs Zygisk with DenyList or a hiding module | Better by design | Better by design |
| Choose it if | You want the largest set of guides and modules | You use a recent phone and care about hiding root | You want kernel-level root on a stock kernel |

Beginners should start with Magisk. Download it only from github.com/topjohnwu/Magisk, and see Chapter 18 for why.

### Rooting with Magisk

You need the factory firmware for exactly the build installed now. If you patch an image from a different build, the phone can lose Wi-Fi, sound, or the camera (Chapter 16).

1. Install the Magisk app on the phone.
2. Extract the boot image from your firmware package. If the package contains **init\_boot.img**, use it. If it does not, use **boot.img**. Which one a phone needs depends on how it launched; the file list in the firmware is the answer.
3. Copy the image to the phone's Download folder.
4. In Magisk, tap Install, choose Select and Patch a File, pick the image, and tap Let's go. Magisk writes a file named magisk\_patched followed by some characters into Download.
5. Copy the patched file to the computer and flash it from fastboot:

```
adb pull /sdcard/Download/magisk_patched-XXXXX.img ./magisk_patched.img
adb reboot bootloader
fastboot flash init_boot magisk_patched.img
fastboot reboot
```

If you patched boot.img, flash to `boot` instead of `init_boot`.

**Samsung.** Samsung has no fastboot. You patch the AP file from the firmware package in the Magisk app and flash the result with Odin in Download Mode.

**Note.** Keep the original unpatched image next to the patched one on your computer. If the phone ever fails to start, flashing the original restores it in seconds.

**After an update.** An OTA update replaces the boot image and removes root. Magisk can install itself to the new slot after an update. Follow the Magisk documentation for the current procedure, and do not restore your phone with a mismatched image.

### Hiding root and passing Play Integrity

In Magisk's settings, switch on Zygisk. Use Enforce DenyList and Configure DenyList to select banking apps, wallets, and any game that refuses to run. If you use Shamiko, leave Enforce DenyList off and keep using the configuration list. Then install an integrity-fix module such as PlayIntegrityFix or a maintained fork, restart, and check the result with a Play Integrity checker app.

Be realistic about what this achieves. Device integrity is often reachable. Strong integrity is not available to an unlocked bootloader honestly, and any module that appears to pass it depends on credentials that Google can revoke. When that happens a bank or wallet that worked last week stops working until a module update arrives. Do not make this phone your only way to pay or sign in to your bank.

**Recap.** Root patches the startup image from the same firmware as the installed build. Keep the stock image. Magisk is the most documented, KernelSU and APatch hide better. Play Integrity is a continuing arrangement, not a one-time fix.

## Chapter 13: Flashing a custom ROM

A custom ROM replaces the manufacturer's software completely. Done correctly, you get a clean, current Android with no pre-installed extras. Done carelessly, you get a bootloop. The difference is nearly always the preparation.

### Choosing one

The Pixel Experience project, which many older videos still recommend, ended active development. Do not follow those tutorials. The ROMs below are the ones people mostly use now, but project status changes, so confirm that your device has a currently maintained build before you start.

| ROM | Character | Google apps |
| --- | --- | --- |
| LineageOS | Close to stock Android, conservative, supports a very large number of devices | Not included |
| crDroid | Built on Lineage, with many extra settings | Usually not included |
| Evolution X | Pixel look with extra options | Included |
| PixelOS | Close to Pixel software | Included |
| GrapheneOS | Security and privacy focused, Pixel phones only | Optional, runs Play services sandboxed |

Take the ROM from the project's official site or the device thread, never from a repost.

### Google apps

Google's apps are proprietary, so open-source ROMs often cannot ship them. You have three routes:

- **A ROM that includes them**, such as Evolution X or PixelOS. Flash it and the Play Store is there.
- **A ROM without them, plus a separate package.** For LineageOS, flash a package such as MindTheGapps for your Android version and architecture before the first boot. Adding them later means a clean install.
- **No Google at all, or microG.** microG is an open-source replacement for part of Google Play services. Many apps that expect Google services run on it, but some will not.

### Before you flash

- Read the device page or thread from top to bottom. Note the required stock firmware version, the recovery to use, and any extra steps. Some ROMs need an updated vendor firmware first, and a few need an extra partition-copy step.
- Check that the codename in the file name matches your phone exactly. Pixel 8 is shiba, Pixel 8 Pro is husky, Poco F5 is marble. A sibling model's ROM is the classic way to get a brick.
- Verify the checksum (Chapter 18).
- Confirm your backups, and that the bootloader is unlocked.

### The procedure (A/B phone, ADB sideload)

1. Boot into the recovery named on the device page.
2. Wipe. In the recovery, choose Factory reset, then Format data. In TWRP or OrangeFox, go to Wipe, then Format Data, and type yes. This is required when moving from stock to a custom ROM, because the old encryption will not work with the new system.
3. Start sideload. In Lineage-style recovery, choose Apply update, then Apply from ADB. In TWRP or OrangeFox, choose Advanced, then ADB Sideload.
4. On the computer:

```
adb sideload name-of-rom.zip
```

The progress may stop at 47 per cent, or at 94 per cent on some recoveries, and then print a message saying the transfer is complete. That is normal. Check the recovery screen for the result.

5. If you are installing Google apps on an A/B phone, the recovery will ask whether to reboot to recovery to install additional packages. Accept. The ROM was written to the other slot, and this switches to it. Sideload the Google apps package the same way.
6. Choose Reboot system. The first start can take several minutes.

If the phone does not start, see Chapter 16. Common causes are skipping Format data and flashing on top of the wrong firmware version.

**Updates.** A ROM usually provides its own update flow. Install updates of the same ROM over your existing data without wiping, which people call a dirty flash. Changing to a different ROM, or a different Android release, normally needs a clean flash with Format data again, so back up first.

## Chapter 14: Kernels and performance

The kernel is the part of the system that decides how fast each processor core runs and when to slow down. A custom kernel replaces it, and a kernel manager app lets you change some of those settings. Most phones need neither, and this is the chapter where bad advice is most common.

**CPU governors.** A governor decides how the clock speed rises and falls. Current phones use schedutil or a vendor scheduler, tuned by the manufacturer. Locking the CPU to a performance governor wastes battery and heats the phone. Locking it to powersave makes the phone sluggish. Leave it alone unless you are testing something specific.

**ZRAM.** Compressed swap held in RAM. Phones with limited memory can benefit from it, and the default is usually fine. Changing it matters mainly on 6 GB phones or less, and you should measure the result rather than assume it helps.

**Charge limits.** Lithium-ion batteries wear faster when they spend long periods at 100 per cent, particularly when warm. Capping charging at about 80 or 85 per cent reduces that wear. Many ROMs and some stock systems now have a built-in limit, and Pixels have one in settings. If yours does not, root tools such as ACC provide one. The gain is real but modest. It does not double battery life.

**Custom kernels.** They can add features, such as built-in KernelSU or different thermal behaviour. They must match your exact ROM and Android version, and a mismatch commonly produces a bootloop or broken Wi-Fi. Use one only from your device's official thread, and keep the original boot image.

### Myths

**RAM boosters.** Android keeps recent apps in memory so they open faster. Closing them forces the phone to reload them and spend more battery. These apps do no good.

**Thermal-disabling modules.** A module that removes thermal limits can raise performance briefly, and it also lets the phone overheat. Heat damages the battery and can harm the hardware. Do not install modules that remove protections.

**build.prop speed tweaks.** Lines such as ro.ril.hsxpa=2 or modified TCP buffer settings come from a decade-old forum culture. Modern Android ignores most of them. They change nothing, and some can cause problems.

**Recap.** The defaults are well tuned. A charge limit and removing background clutter achieve more than any tweak.

## Chapter 15: Root apps and modules

A Magisk module runs scripts as root while the system is still starting. Installing one means trusting its author with the whole phone. Use modules whose source code is public and has an active community behind it.

| Tool | Purpose |
| --- | --- |
| PlayIntegrityFix or a maintained fork | Adjusts what the phone reports so it can pass device integrity (Chapter 12). |
| Neo Backup, Swift Backup | Full backups of apps and their data, restorable after a ROM flash. Neo Backup is open source. |
| AdAway | Ad blocking through the hosts file. Turn on Magisk's Systemless hosts so the system partition stays untouched. |
| App Manager | Inspect apps, trackers, and permissions, freeze or block components. Works with root or Shizuku. |
| JamesDSP | System-wide audio equaliser and effects. |
| LSPosed | Framework letting modules change an app's behaviour, selected app by app. Only use it if you know what you want from it. |

**Warning: one module at a time.** After installing each module, restart and use the phone before adding the next. If you install ten and the phone loops, you cannot tell which one is responsible. Never install two modules that change the same thing, such as two audio mods.

**Recap.** Prefer open-source tools. Add modules singly. Know how to remove them from a bootloop (Chapter 16) before you install the first one.

---

# Part 5: When it goes wrong

## Chapter 16: Bootloops and soft bricks

Most modding failures are recoverable. If the screen turns on, or if the phone still reaches fastboot, Download Mode, or recovery, the bootloader is alive and the software can be replaced.

**Soft brick or bootloop.** The phone shows a logo or boot animation and restarts, or drops back into fastboot or recovery. The bootloader and hardware are fine. Something in boot, super, or a module is wrong. This covers nearly every case.

**Hard brick.** No screen, no vibration, no charging indicator, and no way into fastboot. It usually follows flashing another model's bootloader, or an interrupted write to a low-level partition. Qualcomm phones can sometimes be revived through Emergency Download Mode (USB ID 9008), though many modern phones need a programmer file signed by the manufacturer, so it is often a service-centre job. MediaTek phones have BROM and SP Flash Tool, with similar limits.

### Pick your situation

**A. The loop started right after installing a root module.** You do not need to wipe the phone.

1. While it is looping, connect to the computer and run:

```
adb wait-for-device shell magisk --remove-modules
```

If the phone reaches ADB early enough, Magisk removes all modules and restarts. You may need to try it a few times, running the command first and then connecting.

2. Or try Safe Mode. When the boot animation appears, hold Volume Down until the phone finishes starting in Safe Mode. Magisk disables modules there. Restart normally and remove the module in the Magisk app. On KernelSU, the shortcut is pressing Volume Down several times during the animation.
3. Or flash the original unpatched init\_boot or boot image from fastboot. The phone starts without root. Remove the module, then patch and flash again.

**B. The loop started after flashing a custom ROM.** Go into recovery. Did you run Format data? If not, do it now and sideload the ROM again. Check that the firmware underneath matches what the ROM thread requires. Mismatched vendor firmware is the second most common cause.

**C. Neither the system nor recovery starts, but fastboot works.** On an A/B phone, try the other slot:

```
fastboot --set-active=other
fastboot reboot
```

If that fails, flash the full stock firmware (Chapter 17).

### Specific problems

**"Your device is corrupt" or a red or orange warning at start.** An orange warning is the standard unlocked-bootloader notice. A red one, or a message that the device cannot be trusted, means verification failed for something you flashed. If it lets you continue and the phone works, you have a partition that fails verification. If it will not start, reflash the stock vbmeta image with verification disabled, as your device's instructions direct:

```
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
```

Use the vbmeta from your own stock firmware, and only when needed. This lowers a security check, so undo it by flashing stock when you return to normal.

**Wi-Fi, sound, or camera stopped working after rooting.** Almost always the patched image came from a different build than the firmware that is running. The kernel modules and the kernel must match. Download the firmware for your exact installed build, flash its boot or init\_boot image, and the problem usually disappears.

**Fingerprint sensor fails after flashing.** Check that the persist partition was not erased and that you flashed the right regional firmware. Reflashing the matching stock build often restores calibration. If the factory calibration data itself is lost, only a service centre can rewrite it, which is why Chapter 6 says to back up persist.

**Banking or wallet app stopped working after months.** Google revoked the credentials your integrity module was using. Update the module, or use its action button if it has one, clear data and cache for Google Play services and the app, and restart. If no update exists, the app may stay unavailable for a while.

**Recap.** If it reaches fastboot or recovery, it can be fixed. Remove the module, restore the stock boot image, swap slots, or reflash firmware, in that order of effort.

## Chapter 17: Back to stock, and relocking

Going back to unmodified firmware is the reset button. You need it when you sell the phone, send it for repair, or give up on a stubborn problem.

### Restoring firmware by brand

**Pixel.** The easiest. Open Google's Android Flash Tool in a Chromium-based browser, connect the phone in fastboot mode, and select a build. Or download the factory image from Google, unzip it, boot to fastboot, and run flash-all.bat on Windows or ./flash-all.sh on Linux.

**Xiaomi, POCO, Redmi.** Get the official fastboot ROM for your region, which is a .tgz file and not the recovery .zip. Extract it until you reach the scripts, boot to fastboot, and run flash\_all.bat or flash\_all.sh. The scripts named flash\_all\_lock lock the bootloader as well, so avoid them unless you want that. In Mi Flash, pick Clean all, not Clean all and lock, until you know the phone starts correctly.

**Samsung.** Download the full firmware package for your model and region, which comes as BL, AP, CP, and CSC files, and flash them with Odin on Windows, or Heimdall or Odin4 on Linux. Use CSC, not HOME\_CSC, if you want a clean wipe.

**Motorola.** Use Motorola's Rescue and Smart Assistant tool, or the fastboot firmware packages, following the instructions for your model.

**OnePlus.** Use the manufacturer's tool for your model where one exists, or the full OTA package restored through local update, following the thread for your exact phone.

### Relocking

Relocking a bootloader makes the phone check signatures again. It is only safe when every partition is unmodified and the firmware matches the phone.

**Warning: the way people brick phones.**

- Do not relock while a custom ROM, custom recovery, or root is installed. The bootloader will reject the modified software, and the phone may be unable to start. Because you cannot reach Android to turn on OEM unlocking, you may not be able to unlock it again. The exception is a ROM built to relock with its own signing keys, such as GrapheneOS on Pixels, and only by following its instructions.
- Do not relock a Xiaomi phone running firmware for a different region than the hardware.
- Do not flash firmware older than the version your bootloader's anti-rollback counter expects. Recent Pixel updates have raised this counter on some models, and downgrades will fail or brick. Read the release notes for your build.
- Samsung does not follow this process, and a tripped Knox fuse stays tripped.

### The safe order

1. Flash the latest official firmware for your phone's original region.
2. Let it start fully and reach the setup screen, proving the stock system works.
3. Check in Developer options that OEM unlocking is still on. If something interrupts the relock, you can still unlock again.
4. Run:

```
adb reboot bootloader
fastboot flashing lock
```

5. Confirm on the phone. It wipes once more and starts as a locked stock phone.

**Recap.** Use the manufacturer's tool, leave OEM unlocking on, and relock only when the phone runs unmodified firmware that boots.

## Chapter 18: Staying safe

With an unlocked bootloader and root, the safeguards that protect an ordinary phone are in your hands.

**Verify every download.** A checksum is a fixed-length fingerprint of a file. A single changed byte produces a different one, so it detects both a corrupted download and a tampered one. Official ROM and firmware pages publish the SHA-256 value next to the download link.

Linux:

```
sha256sum name-of-rom.zip
```

Windows PowerShell:

```
Get-FileHash .\name-of-rom.zip -Algorithm SHA256
```

Windows Command Prompt:

```
certutil -hashfile name-of-rom.zip SHA256
```

Compare the whole value with the published one, not only the beginning and end. Any difference means you download again.

**Beware of lookalike sites.** Search results are full of pages pretending to be official. Magisk's only official source is its GitHub repository, github.com/topjohnwu/Magisk, and the project's documentation pages. Names like magiskmanager.com are not related to it. Get ROMs from the project's own site or the device thread you read in full.

**Treat root requests as a trust decision.** When Magisk or KernelSU asks whether to grant root, ask why a flashlight or wallpaper app needs it. If you cannot name a reason, deny it. Audit the list of granted apps from time to time.

**Require authentication for root.** Both Magisk and KernelSU can ask for your fingerprint or PIN before approving a request. Turn that on, so someone using your unlocked phone cannot approve root silently.

**Turn off USB debugging when not in use,** particularly before using public charging points.

**Modules and APKs.** Install modules and apps from sources with public code. Avoid anything promising free paid features, game cheats, or performance miracles, which are common carriers of malware.

**Recap.** Checksum everything, use only official sources, and give root to as few apps as possible.

---

# Reference

## Glossary

| Term | Meaning |
| --- | --- |
| A/B partitions | Two copies of the system partitions. Updates go to the unused one and the phone switches on reboot. |
| ADB | Android Debug Bridge. Google's tool for talking to a phone that is running or in recovery. |
| ADB sideload | Sending an installable ZIP file from a computer to a phone in recovery. |
| AOSP | Android Open Source Project, the open-source base for nearly all Android systems. |
| Anti-rollback | A counter in the bootloader that blocks installing older firmware. |
| AVB | Android Verified Boot. The signature check during startup. |
| Bloatware | Pre-installed apps you did not choose and often cannot remove normally. |
| Boot image | The partition holding the kernel, and on older phones the startup ramdisk. |
| Bootloader | The first program to run. Checks signatures and starts Android or recovery. |
| Bootloop | A phone that keeps restarting without finishing startup. |
| Brick (soft, hard) | A phone that will not start. Soft means fixable by software, hard means no sign of life. |
| Checksum | A fingerprint of a file used to verify that it is intact and authentic. |
| Clean and dirty flash | A clean flash wipes userdata first. A dirty flash installs over existing data. |
| Codename | A device's internal name, which ROMs and recoveries are matched by. |
| Custom kernel | A replacement kernel from a community developer. |
| Custom recovery | A third-party recovery such as TWRP or OrangeFox, or one supplied by a ROM. |
| Custom ROM | A community-built replacement Android operating system. |
| DenyList | Magisk's list of apps from which root is hidden. |
| dm-verity | Block-level integrity checking of read-only partitions. |
| Download Mode | Samsung's flashing mode, used with Odin instead of fastboot. |
| EDL | Qualcomm's low-level Emergency Download Mode. |
| EFS, persist | Partitions storing IMEI, radio and sensor calibration. Unique to each phone. |
| Fastboot | A protocol and bootloader mode used to flash images from a computer. |
| FBE | File-based encryption. Files in userdata are encrypted with keys tied to your lock screen. |
| Flashing | Writing an image or package to the phone's storage. |
| FRP | Factory Reset Protection. After a reset, the phone asks for the Google account last used on it. |
| GApps | Packages containing Google's apps and services for ROMs that do not include them. |
| GKI | Generic Kernel Image. Google's standard kernel arrangement on recent Android versions. |
| init\_boot | Partition holding the generic ramdisk on phones that launched with Android 13 or later. |
| KernelSU | Root solution built into the kernel. |
| Knox | Samsung's security system, including a hardware fuse tripped by unlocking. |
| Magisk | A widely used systemless root and module framework. |
| microG | An open-source replacement for parts of Google Play services. |
| OEM unlocking | The Developer options switch that allows the bootloader to be unlocked. |
| OTA | Over-the-air update. |
| Play Integrity API | Google's service that tells apps whether a device looks genuine and unmodified. |
| Shizuku | An app that gives other apps ADB-level access through wireless debugging. |
| super | The container partition holding system, vendor, and product. |
| Systemless | Root that changes the startup image and memory, not the system partition. |
| vbmeta | Verification metadata for AVB. |
| Zygisk | Magisk's mechanism for running code inside app processes. |

## Command reference

All commands need Android SDK Platform-Tools.

| Command | Mode | Purpose |
| --- | --- | --- |
| adb devices | Android or recovery | List connected devices and authorisation status. |
| adb reboot bootloader | Android | Restart into fastboot. |
| adb reboot recovery | Android | Restart into recovery. |
| adb pull /sdcard/DCIM ./backup/ | Android | Copy files from the phone. |
| adb push file /sdcard/Download/ | Android | Copy a file to the phone. |
| adb install app.apk | Android | Install an APK. |
| adb shell pm disable-user --user 0 PACKAGE | Android | Disable an app for the current user. |
| adb shell cmd package install-existing PACKAGE | Android | Restore a removed system app. |
| adb sideload file.zip | Recovery | Install a ROM or add-on package. |
| adb wait-for-device shell magisk --remove-modules | During boot | Remove all Magisk modules. |
| fastboot devices | Fastboot | Confirm the phone is detected. |
| fastboot getvar all | Fastboot | Print bootloader information. |
| fastboot flashing unlock | Fastboot | Unlock the bootloader. Wipes data. |
| fastboot boot recovery.img | Fastboot | Start an image from the computer without flashing it. |
| fastboot flash init\_boot patched.img | Fastboot | Flash a patched init\_boot. |
| fastboot flash boot patched.img | Fastboot | Flash a patched or stock boot image. |
| fastboot flash recovery recovery.img | Fastboot | Flash a recovery to its own partition. |
| fastboot --set-active=other | Fastboot | Switch active slot on an A/B phone. |
| fastboot flashing lock | Fastboot | Relock. Only on unmodified stock firmware. |
| fastboot reboot | Fastboot | Restart into Android. |

## Checklists

**Before flashing**

- Battery at 60 per cent or more.
- 2FA codes, chats, photos, and contacts backed up to the computer.
- ROM, recovery, and firmware match your exact codename.
- Stock firmware for your current build saved on the computer.
- Checksums verified for every file.
- A tested data cable and a current Platform-Tools.
- Device page or thread read in full.

**After first boot**

- Check Wi-Fi, Bluetooth, calls and texts, mobile data, cameras, audio, and fingerprint.
- If you have root or a custom recovery, back up efs and persist to the computer.
- Turn on authentication for root requests.
- Configure DenyList and check integrity if you use banking apps.
- Turn off USB debugging when you are done.

## Where to look

Start from the official source for each tool rather than a search result: Google's Android developer site for Platform-Tools, ADB documentation, the Android Flash Tool, Pixel factory images, and Play Integrity documentation; the GitHub repositories of Magisk, KernelSU, and APatch for root; the LineageOS, crDroid, TWRP, and OrangeFox sites for ROMs and recoveries; the UAD-ng repository for debloating; F-Droid and Obtainium for open-source apps; and XDA Forums for the thread covering your exact phone. For unlock procedures, use the manufacturer's own pages: OnePlus, Motorola, and Xiaomi each publish one.
