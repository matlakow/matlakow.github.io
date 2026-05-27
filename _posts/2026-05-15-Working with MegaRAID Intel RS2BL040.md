---
layout: post
title:  "Working with MegaRAID / Intel RS2BL040"
author: matlakow
categories: [ linux, raid, cli ]
image: assets/images/intel.png
---

# Creating RAID10, Checking SMART, and Monitoring Rebuilds

Recently I had to configure and inspect a storage setup based on a MegaRAID / Intel RS2BL040 controller.
Below is a quick operational guide with the exact commands I used for:

* listing disks and enclosures,
* creating a RAID10 virtual drive,
* checking RAID health,
* reading SMART data from individual disks,
* reviewing controller events,
* monitoring rebuild progress,
* and configuring SMART monitoring.

If you work with LSI / MegaRAID controllers on Linux, this may save you some time.

---

## Listing Enclosures and Drives

First, I checked what disks were available in the enclosures attached to controller c0`.

<pre class="overflow-visible! px-0!" data-start="727" data-end="801"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0 /e4 /sall show</span><br/><span>./storcli64 /c0 /e11 /sall show</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

This displays all drives connected to enclosure e4` and e11`.

---

## Creating a RAID10 Array

I created a RAID10 virtual drive using disks located in enclosure e11`.

<pre class="overflow-visible! px-0!" data-start="974" data-end="1057"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0 add vd </span><span class="ͼm">type</span><span class="ͼg">=</span><span>r10 </span><span class="ͼm">drives</span><span class="ͼg">=</span><span class="ͼj">11</span><span>:0,11:1,11:3,11:4 </span><span class="ͼm">pdperarray</span><span class="ͼg">=</span><span class="ͼj">2</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

### Explanation

* `type=r10` → RAID10
* `drives=11:x` → disks from enclosure 11
* `pdperarray=2` → two disks per mirrored pair

After creation, you can verify all virtual drives:

<pre class="overflow-visible! px-0!" data-start="1240" data-end="1277"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0/vall show</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

---

## Checking RAID Details

### RAID10 

To inspect the RAID10 virtual drive:

<pre class="overflow-visible! px-0!" data-start="1619" data-end="1658"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0/v1 show all</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

SMART data for the member disks:

<pre class="overflow-visible! px-0!" data-start="1694" data-end="1849"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>smartctl </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-d</span><span> megaraid,12 /dev/sdb</span><br/><span>smartctl </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-d</span><span> megaraid,13 /dev/sdb</span><br/><span>smartctl </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-d</span><span> megaraid,14 /dev/sdb</span><br/><span>smartctl </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-d</span><span> megaraid,15 /dev/sdb</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

One important thing to remember with MegaRAID controllers is that SMART access goes through the controller, not directly to the physical disks. That is why the -d megaraid,N` parameter is required.

---

## Checking Controller Errors and Events

When troubleshooting degraded arrays or failed disks, the controller event log is extremely useful.

<pre class="overflow-visible! px-0!" data-start="2199" data-end="2238"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0 show events</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

This command provides:

* disk failures,
* rebuild history,
* predictive failure warnings,
* controller alerts,
* patrol read events,
* and many other useful diagnostics.

---

## Monitoring Rebuild Progress

If a drive replacement triggers a rebuild, you can monitor the status with:

<pre class="overflow-visible! px-0!" data-start="2526" data-end="2576"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span>./storcli64 /c0/eall/sall show rebuild</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

This shows rebuild percentage and status for all disks connected to the controller.

---

## SMART Monitoring Configuration

For automated monitoring I configured** **`smartd` with MegaRAID support.

Example configuration:

<pre class="overflow-visible! px-0!" data-start="2798" data-end="3184"><div class="relative w-full mt-4 mb-1"><div class=""><div class="relative"><div class="h-full min-h-0 min-w-0"><div class="h-full min-h-0 min-w-0"><div class="border border-token-border-light border-radius-3xl corner-superellipse/1.1 rounded-3xl"><div class="h-full w-full border-radius-3xl bg-token-bg-elevated-secondary corner-superellipse/1.1 overflow-clip rounded-3xl lxnfua_clipPathFallback"><div class="pointer-events-none absolute inset-x-4 top-12 bottom-4"><div class="pointer-events-none sticky z-40 shrink-0 z-1!"><div class="sticky bg-token-border-light"></div></div></div><div class="relative"><div class=""><div class="relative z-0 flex max-w-full"><div id="code-block-viewer" dir="ltr" class="q9tKkq_viewer cm-editor z-10 light:cm-light dark:cm-light flex h-full w-full flex-col items-stretch ͼd ͼr"><div class="cm-scroller"><pre class="cm-content q9tKkq_readonly m-0"><code><span class="ͼe"># MegaRAID / Intel RS2BL040</span><br/><br/><span>DEVICESCAN </span><span class="ͼn">-H</span><span> </span><span class="ͼn">-l</span><span> error </span><span class="ͼn">-l</span><span> selftest </span><span class="ͼn">-f</span><br/><br/><span>/dev/sda </span><span class="ͼn">-d</span><span> megaraid,12 </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-o</span><span> on </span><span class="ͼn">-S</span><span> on </span><span class="ͼn">-s</span><span> (S/../.././02) </span><span class="ͼn">-W</span><span> </span><span class="ͼj">4</span><span>,45,50 </span><span class="ͼn">-m</span><span> root</span><br/><br/><span>/dev/sda </span><span class="ͼn">-d</span><span> megaraid,13 </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-o</span><span> on </span><span class="ͼn">-S</span><span> on </span><span class="ͼn">-s</span><span> (S/../.././03) </span><span class="ͼn">-W</span><span> </span><span class="ͼj">4</span><span>,45,50 </span><span class="ͼn">-m</span><span> root</span><br/><br/><span>/dev/sda </span><span class="ͼn">-d</span><span> megaraid,14 </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-o</span><span> on </span><span class="ͼn">-S</span><span> on </span><span class="ͼn">-s</span><span> (S/../.././04) </span><span class="ͼn">-W</span><span> </span><span class="ͼj">4</span><span>,45,50 </span><span class="ͼn">-m</span><span> root</span><br/><br/><span>/dev/sda </span><span class="ͼn">-d</span><span> megaraid,15 </span><span class="ͼn">-a</span><span> </span><span class="ͼn">-o</span><span> on </span><span class="ͼn">-S</span><span> on </span><span class="ͼn">-s</span><span> (S/../.././05) </span><span class="ͼn">-W</span><span> </span><span class="ͼj">4</span><span>,45,50 </span><span class="ͼn">-m</span><span> root</span></code></pre></div></div></div></div></div></div></div></div></div><div class=""><div class=""></div></div></div></div></div></pre>

### What these options do

* `-H` → health status checks
* `-l error` → monitor SMART error logs
* `-l selftest` → monitor self-test logs
* `-a` → enable all SMART checks
* `-o on` → enable offline tests
* `-S on` → enable attribute autosave
* `-W 4,45,50` → temperature warnings
* `-m root` → send alerts to root
* `-s (...)` → scheduled SMART self-tests

---

## Final Thoughts

MegaRAID controllers are still very common in enterprise and homelab environments, but troubleshooting them can be confusing at first — especially when trying to access SMART information behind the controller.

The combination of:

* `storcli64`
* `smartctl`
* and** **`smartd`

provides a solid toolkit for managing and monitoring RAID arrays on Linux.

If you are running similar hardware, I highly recommend automating SMART checks and periodically reviewing controller event logs before failures become critical.

