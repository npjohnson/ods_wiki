---
sidebar: home_sidebar
title: How to add a new device to the wiki
folder: internal
permalink: addingdevice-howto.html
search: exclude
tags:
 - internal
---

## Requirements for adding a device

{% assign definitions = site.data.schema.definitions %}

The LineageOS wiki provides instructions based on the assumption that your device has got its device tree/kernel
under the [LineageOS GitHub organization](https://github.com/LineageOS), and that it supports building LineageOS {{ definitions.valid_branches.enum[-2] }} or higher. If your device meets
both those requirements, follow the instructions below to get your device set up.

## Adding your device

### Prepare the required files

There are a few files which need to be there to have a device on the wiki.
In order to get them, navigate to `$LINEAGE_SRC/lineage/wiki/` and run:

```
./scripts/generate_device.sh your_device
```

Obviously replace `your_device` with the codename of your device

### Populating the YAML

The sample template has been copied to `$LINEAGE_SRC/lineage/wiki/_data/devices/your_device.yml`.
Update the values to match your device. An explanation of some of the options is below.

Some of the properties allow specifying different values for multiple models being covered in the same file.
In that case the format stays the same as for a single device, but is used in a Key-Value style like this:

```
property:
- Model1: Value
- Model2: Value
```

An example using the `battery` property:
Just one device:

```
battery: {removable: False, capacity: 1000, tech: 'Li-Ion'}
```

vs. two different models:

```
battery:
- Model1: {removable: False, capacity: 1000, tech: 'Li-Ion'}
- Model2: {removable: True, capacity: 2000, tech: 'Li-Po'}
```

The following list will mention Model-Value pairs where applicable.


#### List of properties

{% assign properties = site.data.schema.properties %}

* `architecture`: The CPU architecture of the device, can be one of

  ```
  {{ definitions.valid_architectures.enum | join: ', ' }}
  ```

  If your device has a 64 bit architecture but Android runs on 32 bit, you can use a different format: `{cpu: 'arm64', userspace: 'arm'}`

* `battery`: Use the format `{removable: False, capacity: <number in mAh>, tech: '<tech>'}`. If your battery is removable, use `True` instead.
  For `tech` you can use:

  ```
  {{ definitions.battery_data.properties.tech.enum | join: ', ' }}
  ```

  This property supports Model-Value pairs.

* `bluetooth`: The proper format is either `{spec: '<version>'}` with `version` being the version of the BT protocol supported, or `{spec: '<version>', profiles: '<profiles>'}` when your device
  supports additional profiles. These are the possible values:

  ```
  For the specification:
  {{ properties.bluetooth.properties.spec.enum | join: ', ' }}

  For the optional profiles:
  {{ properties.bluetooth.properties.profiles.items.enum | join: ', ' }}
  ```

* `camera`: One entry for each camera in the format

   ```
   - {flash: '<flash>', info: 'x MP'}
   ```

  with `flash` being one of
  ```
  {{ definitions.camera_data.properties.flash.enum | join: ', ' }}
  ```
  and `info` in the format `x MP` or `x MP (wide)` (wide, ultrawide, depth, etc. can be used), or `x MP (Model1) or x MP (Model2)` if necessary

* `cpu`: The CPU type of the device, can be one of the following list:

  ```
  {{ properties.cpu.enum | join: ", " }}
  ```

* `dimensions`: Use the format `{width: <w>, height: <h>, depth: <d>}` with each being the dimension in [mm]. This property supports Model-Value pairs.
* `download_boot`: Instructions for booting the device into the mode used to install recovery. On most devices, this is fastboot mode.
* `image`: The image located under `images/devices/` to use for this device. Instructions on adding an image <a href="#adding-the-devices-image">can be found below</a>.
* `install_method`: Used to determine the recovery install template to use. Templates can be found in *_includes/templates/recovery_install_*`install_method`*.md* and must be one of:

  ```
  {{ properties.install_method.enum | join: ", " }}
  ```

* `kernel`: Use the format `{repo: <repository name>, version: '<major.minor>'}`.
* `network`: The frequencies and channels for the various network technologies. You can look them up [here](https://www.frequencycheck.com/models/). Keep the non-available technologies empty.
* `peripherals`: A list of peripherals available on the device, can be any of the following list:

  ```
  {{ definitions.valid_peripherals.items.enum | join: ", " }}
  ```

* `recovery_partition_name`: A partition name recovery image is flashed to, can be one of:

  ```
  {{ properties.recovery_partition_name.enum | join: ', ' }}
  ```

* `release`: Allowed formats are `yyyy`, `yyyy-mm` and `yyyy-mm-dd`. This property supports Model-Value pairs.
* `screen`: Use `{size: '<screen size>', resolution: '<1234x567>', technology: ''}` with number for `size`, `1234x567` for `resolution` and one of the following values for `technology`:

  ```
  {{ definitions.screen_technologies.enum | join: ", " }}
  ```

  This property supports Model-Value pairs.

* `tree`: The repo name of the device tree - for example, `android_device_oneplus_bacon`.
* `vendor_short`: The vendor name used for the device tree - for example, `oneplus`.

#### List of optional properties

There are some optional properties which you might not need, but in case you do, they are documented below:

{% include alerts/important.html content="Remove any of these properties in case they don't apply for your device!" %}

* `before_install`: Sometimes some pre-conditions must be met in order to be able to install LineageOS. See *_includes/templates/device_specific/before_install_*`before_install`*.md* for possible values.
  - One common use is to show the precondition for a specific firmware, `needs_specific_android_fw`, which is passed the additional argument `version` to indicate which Android version is required. This `before_install` also accepts a few other optional arguements:
    - `lineage_version` Indicated the LineageOS version this requirement was originally implemented on (and on an upgrade page for these devices, we would show that this condition would be already met when coming from that version) - you only need to pass this arguement the relevant lineage version number
    - `ships_fw`: Set to `true` when the device ships firmware in LineageOS builds (relevant for upgrades), don't set at all if firmware not is not shipped
* `before_lineage_install`: This property can be set to display instructions once recovery is booted and before the actual LineageOS installation. Existing (or to be created) values can be found in *_includes/templates/device_specific/before_lineage_install_*`before_lineage_install_`*.md*
* `before_recovery_install`: This property can be set to specify steps to be completed before installing the recovery image. Values can be found in *_includes/templates/device_specific/before_recovery_install_*`before_recovery_install_`*.md*
* `carrier`: If the device was released for a specific carrier, the name of that carrier.
* `custom_recovery_link`: A custom recovery link in case no official recovery exists for the specific device or it doesn't work properly.
* `custom_recovery_codename`: If an official recovery exists for the device, but it uses a different codename, specify the used one here.
* `custom_unlock_cmd`: Used if the command to unlock your device via fastboot is different than `fastboot oem unlock`.
* `format_on_upgrade`: Used if the device needs to wiped on major LineageOS version due to unfixable device specific issues.
* `is_ab_device`: Used if the device has an A/B partition scheme.
* `is_unlockable`: Set to false if there is no official method to unlock the bootloader. A hint will appear on the device's overview and install page. If this property is not set, it defaults to `True`
* `quirks`: List of known quirks (make sure they aren't violating the [charter](https://github.com/LineageOS/charter/blob/main/device-support-requirements.md) or are exempted!). An array of one or more of the following (if unclear, click the links):

  {%- for item in definitions.valid_quirks.enum %}
  [{{ item }}](/quirks/{{ item }}){% unless forloop.last %}, {% endunless %}
  {%- endfor %}
  {% capture snet %}
  The [Device integrity](/quirks/snet) quirk is automatically shown for every device and can't be listed manually
  {% endcapture %}
  {% include alerts/note.html content=snet %}
* `required_bootloader`: Specify the bootloader versions which are required to install LineageOS. Example:

  ```
  required_bootloader: [Version1, Version2]
  ```
* `sdcard`: Format: `{size_max: '<size>'}` or `{size_max: '<size>', slot: '<slot>'}` with
  - `size` being a string like `32 GB` (checked against a pattern `{{ definitions.sdcard_data.properties.size_max.pattern }}`) and
  - `slot` being one of
    ```
    {{ definitions.sdcard_data.properties.slot.enum | join: ", " }}
    ```

{% include alerts/note.html content="If you need to assign a value to one of the fields which is not allowed by the time you create your change, update the schema validator or contact us to add it" %}

### Adding the device's image

Find a reasonably high-quality image of your device, and add it to `images/devices/<image.png>`. You must ensure the following:

- The filename must match the entry `image` in your YAML file
- The background of the image is transparent
- The image is cropped to the actual content
- Preferably you remove any cast shadow - if that's not possible, make sure it doesn't extend too much beyond the actual device outline
- Optional: If you are already working with the image in an editor, rescale the picture to so the bigger dimension equals 500px (respecting the aspect ratio, keeping the other dimension smaller than that)

{% include alerts/note.html content="It is unlikely that your picture has equal width and height. Please check the image rules above!" %}

Then run

```
./scripts/prepare_image.sh <image.png>
```

This will check the image against the maximum size and generate a smaller version of it in `images/devices/small/<image.png>`.

The smaller picture also needs to be committed!


## Testing it works

Start the wiki on your local Jekyll server, and navigate to [the devices list](http://localhost:4000/devices.html). Your device should be there.
Click on it, and check that the info/install/build pages all seem correct.

Now run the validation:

```
bundle install
ruby ./test/validate.rb
```

If the script doesn't give you an output, all the validated fields have a proper format. Otherwise, read the messages carefully to see which fields have to be corrected.

## Submitting your device

After verifying the device-specific pages, commit and upload your changes to Gerrit, and add the "Wiki Editors" group as reviewers.
