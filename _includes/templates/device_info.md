{% assign device = site.data.devices[page.device] %}

{% if device.migrated_to and device.migrated_to != "" %}
{% include templates/device_migrated_to.md %}
{% endif %}

{% if device.maintainers == empty %}
{% unless device.migrated_to %}
{% include alerts/specific/warning_discontinued_device.html %}
{% endunless %}
{% endif %}
{% if device.is_unlockable == false %}
{% include alerts/specific/important_bootloader_not_unlockable.html %}
{% endif %}

{%- if device.variant %}
## Variants

There are multiple variants of this device. [Make sure you're viewing the right one.]({{ "devices/" | append: device.codename | relative_url }})
{%- endif %}

{% if device.maintainers != empty %}
## Downloads
[Get the builds here](https://updater.oddsolutions.us/devices/{{ device.codename }})
{% endif %}

## Guides

{% include snippets/branches.md %}
{%- assign last_supported_version = device.versions | last %}
{%- assign end = num_branches | minus: 1 %}
{%- for i in (0..end) %}
{%- if branches[i] == last_supported_version %}
{%- assign index = i %}
{%- endif %}
{%- endfor %}
{%- if index > 0 %}
    {%- assign prev_branch_index = index | minus: 3 | min: 0 %}
    {%- assign prev_branch = branches[prev_branch_index] %}
    {%- assign curr_branch = branches[index] %}
{%- else %}
    {%- assign prev_branch = branch_minus_3 %}
    {%- assign curr_branch = current_branch %}
{%- endif %}

- [Installation]({{ device | device_link: "/install" | relative_url }})
- [Build for yourself]({{ device | device_link: "/build" | relative_url }})
{%- if device.firmware_update %}
- [Update to a newer vendor firmware version]({{ device | device_link: "/fw_update" | relative_url }})
{%- endif %}
- [Update to a newer build of the same LineageOS version]({{ device | device_link: "/update" | relative_url }})
{% assign versions_count = device.versions|count_ints -%}
{%- if versions_count > 1 -%}
- [Upgrade to a higher version of LineageOS (e.g. lineage-{{ prev_branch }} -> lineage-{{ curr_branch }})]({{ device | device_link: "/upgrade" | relative_url }})
{%- endif -%}

{% if device.note_title and device.note_title != "" %}
{% include templates/device_info_note.md %}
{% endif %}

{% if device.recovery_boot or device.download_boot %}
## Special boot modes

{% if device.recovery_boot %}
* **Recovery**: {{ device.recovery_boot }}
{%- if device.vendor == "LG" %}
    {% include templates/recovery_boot_lge.md %}
{%- endif %}
{%- endif %}
{%- if device.download_boot %}
* **Bootloader/Fastboot/Download**: {{ device.download_boot }}
{%- endif %}
{%- endif %}

## Known quirks

{% assign quirks = "snet" | split: ',' %}
{% if device.quirks and device.quirks != empty %}
    {% assign quirks = quirks | concat: device.quirks %}
    {% assign quirks = quirks | sort %}
{% endif %}

{%- for quirk in quirks %}
{%- capture url %}/quirks/{{ quirk }}/{% endcapture %}
{%- assign match = site.pages | find: "url": url %}
{%- if match == nil %}
{%- continue %}
{%- endif %}
{%- assign title = match.title | remove: "Quirks - " %}
- [{{ title }}]({{ match.url | relative_url }})
{%- endfor %}
