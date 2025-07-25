---
sidebar: home_sidebar
title: Building for Emulator/AVD
permalink: emulator.html
---

## Introduction

In case you don't have an officially supported device, don't want to test changes on your daily driver, or are just someone who wants to test apps with LineageOS-specific features, we've still got you covered.

These instructions will help you build an emulator-compatible version of LineageOS, ready to run on your computer. If you want to use Android Studio/AVD there are also instructions for packing up/installing your
custom build instead of the default AOSP images that Google provides.


{% include templates/device_build_before_init.md %}


### Initialize the LineageOS source repository

The following branches have been tested for building emulator images:

* lineage-16.0
* lineage-17.1
* lineage-18.1
* lineage-19.1
* lineage-20.0
* lineage-21.0
* lineage-22.1
* lineage-22.2

{% include snippets/branches.md %}
{% include templates/device_build_init_sync.md %}

{% include templates/setup_build_env.md %}

### Start the build

Time to start building!

Select the build target by running the following command, where `<target>` is one of the entries in the table below:

```
breakfast <target> eng
```

|                               |              |  Build targets     |  Supported `<arch>`                |
|-------------------------------|--------------|--------------------|------------------------------------|
| **LineageOS 17 and below**    |              |                    |                                    |
|-------------------------------|--------------|--------------------|------------------------------------|
| Phone                         | Emulator/GSI | `<arch>`           | `arm`, `arm64`, `x86` and `x86_64` |
|-------------------------------|--------------|--------------------|------------------------------------|
| **LineageOS 18.1**            |              |                    |                                    |
|-------------------------------|--------------|--------------------|------------------------------------|
| Phone                         | Emulator/GSI | `<arch>`           | `arm`, `arm64`, `x86` and `x86_64` |
| TV                            | Emulator/GSI | `tv_<arch>`        | `arm`, `arm64`, `x86` and `x86_64` |
| Automotive                    | Emulator/GSI | `car_<arch>`       | `arm64` and `x86_64`               |
|-------------------------------|--------------|--------------------|------------------------------------|
| **LineageOS 19 and above**    |              |                    |                                    |
|-------------------------------|--------------|--------------------|------------------------------------|
| Phone                         | Emulator     | `sdk_phone_<arch>` | `x86` and `x86_64`                 |
| Phone                         | GSI          | `gsi_<arch>`       | `arm`, `arm64`, `x86` and `x86_64` |
| TV                            | Emulator     | `sdk_tv_<arch>`    | `arm` and `x86`                    |
| TV                            | GSI          | `gsi_tv_<arch>`    | `arm`, `arm64`, `x86` and `x86_64` |
| Automotive                    | Emulator     | `sdk_car_<arch>`   | `arm64` and `x86_64`               |
| Automotive                    | GSI          | `gsi_car_<arch>`   | `arm64` and `x86_64`               |
|-------------------------------|--------------|--------------------|------------------------------------|
| **LineageOS 21 and above**    |              |                    |                                    |
|-------------------------------|--------------|--------------------|------------------------------------|
| Phone                         | Emulator     | `sdk_phone_<arch>` | `arm64` and `x86_64`               |
| Phone                         | GSI          | `gsi_<arch>`       | `arm`, `arm64`, `x86` and `x86_64` |
| TV                            | Emulator     | `sdk_tv_<arch>`    | `arm`, `x86` and `x86_64`          |
| TV                            | GSI          | `gsi_tv_<arch>`    | `arm`, `arm64`, `x86` and `x86_64` |
| Automotive                    | Emulator     | `sdk_car_<arch>`   | `arm64` and `x86_64`               |
| Automotive                    | GSI          | `gsi_car_<arch>`   | `arm64` and `x86_64`               |
{: .table }


For starting, `x86` or `x86_64` is recommended, as your computer can run it natively using hardware acceleration.

{% include alerts/tip.html content="If you're running an ARM computer such as an Apple Silicon Mac, you should choose `arm64` for supported LineageOS Versions.<br> This will significanly improve the emulator performance compared to using `x86_64` builds via a translation layer such as Rosetta 2." %}

Instead of `eng` one can also target `userdebug`, the latter is used by official AOSP emulator images, but ADB and communication with the emulator will need to be enabled first.

Now, build the image:
```
mka
```

## Running the emulator

Assuming the build completed without errors, type the following in the terminal window the build ran in:

```
emulator
```

The emulator will fire up and you'll see the LineageOS boot animation. After some time, it will finish booting up and be ready to use.


### Success! So... what's next?

You've done it! Welcome to the elite club of self-builders. You've built your operating system from scratch, from the ground up. You are the master/mistress of your domain... and
hopefully you've learned a bit on the way and had some fun too.


## Exporting for use in Android Studio/AVD

In case you want to run the emulator image independently from the system/terminal you built it in, you are able to export the built image into a format that can be used by Android Studio/AVD.
To do that, run the following command in the same terminal that you originally started the build in:

* LineageOS 20 and below
```
mka sdk_addon
```

If you now look into the `out/host/linux-x86/sdk_addon` directory, you will find a ZIP file (ending in `-img.zip`) that contains all the necessary files for running the emulator image externally.

* LineageOS 21 and above
```
mka emu_img_zip
```

If you now look into the `out/target/product/<arch>` directory, you will find a ZIP file `sdk-repo-linux-system-images.zip` that contains all the necessary files for running the emulator image externally.

To deploy the build into your Android Studio installation, move the contained folder (which is named after the architecture that you built for) into a **subfolder** of `/path/to/android/sdk/system-images`.
AOSP uses the following path name by default, but you are free to make up your own as well:

`system-images/android-<sdk version>/<tag>/<arch>` (where `<tag>` is one of `default`/`google_apis`/`google_apis_playstore`)

LineageOS emulator builds will use the tag `lineage` by default (visible as "LineageOS" in the images list).

As long as you **haven't** moved the folder directly into `system-images`, the emulator image should now show up in the of the lists of images when creating a new virtual Android device.
