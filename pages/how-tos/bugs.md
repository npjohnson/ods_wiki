---
sidebar: home_sidebar
title: How to submit a bug report
folder: how-tos
redirect_from: bugreport-howto.html
permalink: /how-to/bugreport/
tags:
 - how-to
---

## What not to report
  - Bugs in other unofficial builds or anything not downloaded from [our official portal](https://updater.oddsolutions.us/)
  - Missing builds
  - Asking for installation help
  - Asking for device support
  - Feature requests

## Reporting a bug

ODS doesn't accept bugs directly, as our devices are all mostly supported by LineageOS officially. If you hit a bug, go flash the latest official LineageOS build for your device, confirm the bug exists, then report it by following their Wiki guide.

If you happen to discover a rare bug that exists on ODS builds, but not in official LineageOS, or if your device is supported only by ODS and not LineageOS, please report them (with logs, see below) on the relevant XDA thread for your device. If no XDA thread exists, the deivce is WIP and we don't want bug reports.
  {%- capture content %}[Logcats]({{ "how-to/logcat/" | relative_url }}) *must* be attached for all Android bugs, and *must* be captured right after reproducing the issue.{% endcapture %}
  {% include alerts/important.html content=content %}
