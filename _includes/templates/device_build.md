{% assign device = site.data.devices[page.device] %}

{% if device.migrated_to and device.migrated_to != "" %}
{% include templates/device_migrated_to.md %}
{% endif %}

## Introduction

These instructions will hopefully assist you to start with a stock {{ device.vendor }} {{ device.name }}, unlock the bootloader (if necessary), and then download
the required tools as well as the very latest source code for LineageOS (based on Google’s Android operating system) for your device. Using these, you can build both
a LineageOS installation zip and a LineageOS Recovery image and install them on your device.

It is difficult to say how much experience is necessary to follow these instructions. While this guide is certainly not for the extremely uninitiated,
these steps shouldn’t require a PhD in software development either. Some readers will have no difficulty and breeze through the steps easily.
Others may struggle over the most basic operation. Because people’s experiences, backgrounds, and intuitions differ, it may be a good idea to read through
just to ascertain whether you feel comfortable or are getting over your head.

Remember, you assume all risk of trying this, but you will reap the rewards! It’s pretty satisfying to boot into a fresh operating system you baked at home :).
And once you’re an Android-building ninja, there will be no more need to wait for “nightly” builds from anyone. You will have at your fingertips the skills to
build a full operating system from code and install it to a running device, whenever you want. Where you go from there-- maybe you’ll add a feature, fix a bug, add a translation,
or use what you’ve learned to build a new app or port to a new device-- or maybe you’ll never build again-- it’s all really up to you.


{% include templates/device_build_before_init.md %}


### Initialize the LineageOS source repository

{% if device.maintainers != empty %}
The following branches are officially supported for the {{ device.vendor }} {{ device.name }}:
{% else %}
The following branches can be used to build for the {{ device.vendor }} {{ device.name }}:
{% endif %}

{% for version in device.versions %}
{% if version < 15 %}
* cm-{{ version }}
{% else %}
* lineage-{{ version | precision: 1 }}
{% endif %}
{% endfor %}

{% assign current_branch = device.current_branch %}
{% include templates/device_build_init_sync.md %}

{% include templates/setup_build_env.md %}

### Prepare the device-specific code

```
breakfast {{ device.codename }}
```

This will download your device's [device specific configuration](https://github.com/LineageOS/{{ device.tree }}) and
{%- if device.kernel.repo %}
[kernel](https://github.com/LineageOS/{{ device.kernel.repo }}).
{%- else %}
kernel.
{%- endif %}

{% include alerts/important.html content="Some devices require a vendor directory to be populated before breakfast will succeed. If you receive an error here about vendor
makefiles, jump down to [_Extract proprietary blobs_](#extract-proprietary-blobs). The first portion of breakfast should have succeeded, and after completing you can [rerun
`breakfast`](#prepare-the-device-specific-code)" %}

### Extract proprietary blobs

{% capture extracting_blobs_from_zips %}
This step requires to have a device already running the latest LineageOS, based on the branch you wish to build for. If you don't have access to such device, refer to [Extracting proprietary blobs from installable zip]({{ "extracting_blobs_from_zips.html" | relative_url }}).
{% endcapture %}
{% include alerts/note.html content=extracting_blobs_from_zips %}

Now ensure your {{ device.vendor }} {{ device.name }} is connected to your computer via the USB cable, with ADB and root enabled, and that you are in the
`~/android/lineage/device/{{ device.vendor_short }}/{{ device.codename }}` folder. Then run either the `extract-files.sh` or `extract-files.py` script:

```
./extract-files.sh
```

Or, for the Python script:

```
./extract-files.py
```

The blobs should be pulled into the `~/android/lineage/vendor/{{ device.vendor_short }}` folder. If you see "command not found" errors, `adb` may
need to be placed in `~/bin`.


### Start the build

Time to start building! Now, type:

```
croot
brunch {{device.codename}}
```

The build should begin.

{% if device.current_branch < 15 %}
{% capture flex_workaround %}
In case the build fails with an error like ``Assertion `cnt < (sizeof (_nl_value_type_LC_TIME) / sizeof (_nl_value_type_LC_TIME[0]))' failed.``, type `export LC_ALL=C` and start the build again.
{% endcapture %}
{% include alerts/tip.html content=flex_workaround %}
{% endif %}

{% capture signing_builds %}
Want to learn how to sign your own builds? Take a look at [Signing builds]({{ "signing_builds.html" | relative_url }}).
{% endcapture %}
{% include alerts/tip.html content=signing_builds %}

## Install the build

Assuming the build completed without errors (it will be obvious when it finishes), type the following in the terminal window the build ran in:

```
cd $OUT
```

There you'll find all the files that were created. The two files of more interest are:

1. `{{ device.recovery_partition_name }}.img`, which is the LineageOS recovery image.
2. `lineage-{{ device.current_branch | precision: 1 }}-{{ site.time | date: "%Y%m%d" }}-UNOFFICIAL-{{ device.codename }}.zip`, which is the LineageOS
installer package.

### Success! So... what's next?

You've done it! Welcome to the elite club of self-builders. You've built your operating system from scratch, from the ground up. You are the master/mistress of your domain... and
hopefully you've learned a bit on the way and had some fun too.
