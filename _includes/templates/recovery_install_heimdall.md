{%- assign device = site.data.devices[page.device] -%}
{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

{% include snippets/before_recovery_install.md %}

## Preparing for installation

Samsung devices come with a unique boot mode called "Download mode", which is very similar to "Fastboot mode" on some devices with unlocked bootloaders.
Heimdall is a cross-platform, open-source tool for interfacing with Download mode on Samsung devices.
The preferred method of installing a custom recovery is through Download Mode{% unless custom_root_instructions %} -- rooting the stock firmware is neither necessary nor required{% endunless %}.

{% unless device.no_oem_unlock_switch %}
1. Enable Developer Options by pressing the "Build Number" option in the "Settings" app within the "About" menu
 * From within the Developer options menu, enable OEM unlock.
{% endunless %}
2. Download and install the appropriate version of the Heimdall suite for your machine's OS
    * [Windows](https://blob.lineageos.org/downloads/heimdall/Heimdall-Windows-v2.2.2-120625.zip){: .download}: Extract the Heimdall suite zip and take note of the new directory containing `heimdall.exe`. You can verify Heimdall is functioning by opening a Command Prompt or PowerShell in that directory and running `heimdall version`.
    * [Linux](https://blob.lineageos.org/downloads/heimdall/Heimdall-linux-v2.2.2-120625.zip){: .download}: Extract the Heimdall suite zip and take note of the new directory containing `heimdall`. Now copy `heimdall` into a directory in $PATH, a common one on most distros will be /usr/local/bin. For example `cp heimdall /usr/local/bin`. You can verify Heimdall is functioning by opening a Terminal and running `heimdall version`.
    * [macOS](https://blob.lineageos.org/downloads/heimdall/Heimdall-macOS-v2.2.2-120625.dmg){: .download}: Mount the Heimdall suite DMG. Now drag `heimdall` down into the `/usr/local/bin` symlink provided in the DMG. You can verify Heimdall is functioning by opening a Terminal and running `heimdall version`.
    {% include alerts/note.html content="These Heimdall suite distributions were built by LineageOS Developers, as the Heimdall suite executables distributed on the official Heimdall website were outdated and the repo mostly abandoned. Modifications were made to make it build and function on modern OSes." %}
4. Power off the device, and boot it into download mode:
    * {{ device.download_boot }}
    * Now, click the button that the on screen instructions correlate to "Continue", and insert the USB cable into the device.
5. **For Windows users only**: install [UsbDk](https://github.com/daynix/usbdk/releases/latest).
6. On your machine, open a Command Prompt or PowerShell (Windows) window, or Terminal (Linux or macOS) window, and type:
```
heimdall print-pit
```
7. If the device reboots that indicates that Heimdall is working properly. If it does not, please refollow these instructions to verify steps weren't missed, try a different USB cable, and a different USB port.

{% if custom_downgrade_instructions %}
{{ custom_downgrade_instructions }}
{% endif %}

{% if custom_root_instructions %}
{{ custom_root_instructions }}
{% endif %}

{%- capture install_content %}
{%- if device.custom_recovery_link %}
{%- assign is_lineage_recovery = device.custom_lineage_recovery %}
1. Download a custom recovery - you can download one [here]({{ device.custom_recovery_link }}). Simply download the recovery file and rename it to `{{ device.recovery_partition_name }}.img`.
{%- elsif device.uses_twrp %}
1. Download a custom recovery - you can download [TWRP](https://dl.twrp.me/{{ custom_recovery_codename }}). Simply download the latest recovery file, named something like `twrp-x.x.x-x-{{ custom_recovery_codename }}.img` and rename it to `{{ device.recovery_partition_name }}.img`.
    {% include alerts/tip.html content="Ensure you download the `.img` file and not the `.tar` or `.tar.md5` versions." %}
{%- elsif device.maintainers != empty %}
{%- assign is_lineage_recovery = true %}
1. Download [Lineage Recovery](https://updater.oddsolutions.us/devices/{{ custom_recovery_codename }}). Simply download the latest recovery file, named `{{ device.recovery_partition_name }}.img`.
{%- else %}
{%- assign is_lineage_recovery = true %}
1. [Build]({{ device | device_link: "/build" | relative_url }}) a LineageOS installation package. The recovery will be built as part of it!
{%- endif %}
    {% include alerts/important.html content="Other recoveries may not work for installation or updates. We strongly recommend to use the one linked above!" %}
2. Power off the device, and boot it into download mode:
    * {{ device.download_boot }}
    * Now, click the button that the on screen instructions correlate to "Continue", and insert the USB cable into the device.
3. On your machine, open a Command Prompt or PowerShell (Windows) window, or Terminal (Linux or macOS) window, and type:
```
heimdall flash --{{ device.recovery_partition_name | upcase }} {{ device.recovery_partition_name }}.img --no-reboot
```
    {% include alerts/tip.html content="The file may not be named identically to what stands in this command, so adjust accordingly. If the file is wrapped in a zip or tar file, extract the file first, because Heimdall is not going to do it for you." %}
4. A transfer bar will appear on the device showing the recovery image being flashed.
    {% include alerts/note.html content="The device will continue to display `Downloading... Do not turn off target!!` even after the process is complete." %}
5. Manually reboot into recovery, this may require pulling the device's battery out and putting it back in, or if you have a non-removable battery, press the Volume Down + Power buttons for 8~10 seconds until the screen turns black & release the buttons *immediately* when it does, then boot to recovery:
    * {{ device.recovery_boot }}
    {% include alerts/note.html content="Be sure to reboot into recovery immediately after installing the custom recovery. If you don't the stock ROM will overwrite the custom recovery with the stock recovery, and you'll need to flash it again." %}
{%- include snippets/recovery_logo_note.md %}
{%- endcapture %}

{%- if is_lineage_recovery %}
## Installing Lineage Recovery using `heimdall`
{%- else %}
## Installing a custom recovery using `heimdall`
{%- endif %}
{{ install_content }}
