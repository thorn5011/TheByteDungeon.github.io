---
layout: newpost
title: "The mysterious case of the dying Pi"
date: 2024-09-05
categories: [tech]
tags: [cowrie,raspberry, mystery]
---

```sh
docker compose up -d
[+] Running 9/10
 ⠸ pihole [⣿⣿⣿⣿⣿⣿⣿⣿⣿] 115.7MB / 115.7MB Pulling 36.4s
   ✔ ca4da1869379 Pull complete
   ✔ c6f190425048 Pull complete
```

And suddenly - *tap tap tap*..again?! Connection lost, the Raspberry is dead. For the eleventh time, why? I do not know...yet. 
There seems to be an issue when the Pi is under some kind of load so my first idea was the power supply. Let's investigate! :mag:


- [Exhibit A - Power supply :electric\_plug:](#exhibit-a---power-supply-electric_plug)
- [Exhibit B - Logs :clipboard:](#exhibit-b---logs-clipboard)
- [Testing out hypothesis :microscope:](#testing-out-hypothesis-microscope)
  - [To business](#to-business)
- [Conclusion](#conclusion)


---

# Exhibit A - Power supply :electric_plug: 

This is a new Pi 5 and I've been running it now for a week or two. I also have a [Sense HAT](https://www.raspberrypi.com/products/sense-hat/) which increase the power drained. But, this setup have been working until a few days ago. Might it be that since I am hosting more and more service, the demand for power have increased? 

- Base Raspberry: 5V, up to 3A. 15W max.
- Sense HAT: 0.2-0.3A 1-1.5W
- No peripherals connected

According to the [docs](https://github.com/raspberrypi/documentation/blob/master/documentation/asciidoc/computers/raspberry-pi/power-supplies.adoc#power-supply-warnings) there is a "power supply warning" when the voltage drops under 4.63V. We will have to dig into the kernel logs.

But when this at first started happening I just replaced the power adapter to make sure the issues was not there.

![mysterious_pi_sony_adapter](/assets/images/blogs/mysterious_pi_sony_adapter.jpg)

---

# Exhibit B - Logs :clipboard: 

A simple way to try to find out what happen is to look at the last logs generated. Since we are running rsyslog we can just run a `tail -f` session and read the last lines when the device drop out. Or scroll back before the reboot to locate the last log before the sinking of the Pi.

I had the `tail` going a few times, without the logs really giving me anything to work with. The only thing in common was that there were some kind of load at the time of shutdown, e.g. docker image pull, (probably) cronjob execution etc.

Going on a `syslog` safari we find one of the [Big Five](https://www.nationalgeographic.com/animals/article/africa-big-five-safaris-lions).. Well, well, well..
```sh
2024-09-01T00:18:55.177855+02:00 r5 kernel: [32620.596760] hwmon hwmon2: Voltage normalised
2024-09-01T00:18:57.197846+02:00 r5 kernel: [32622.612915] hwmon hwmon2: Undervoltage detected!
```
Is this the evidence we've been looking for? Perhaps.. the only issue is that those logs are not something we catch in the logs each shutdown. Could it be that they do not persist through reboot, or that often before they trigger the Pi is killed?  

---

# Testing out hypothesis :microscope: 

I should have done this from the start but since I thought that 5V adapter would be fine I did not go grab the official Pi charger. 

![mysterious_pi_official_adapter](/assets/images/blogs/mysterious_pi_official_adapter.jpg)

Okaaay, 5.1, 9, 12 and 15 volts?! Might it be that 0.1V which is lacking? Reading the [27W USB-C Power Supply product sheet](https://datasheets.raspberrypi.com/power-supply/27w-usb-c-power-supply-product-brief.pdf) I am realizing it is very much the time I get to the bottom **exactly** how watt, voltage and ampere play together. A bit surprising that normal USB charger won't be much use for the Pi, and so that we need a dedicated power supply. I guess that was inevitable as the Pi keeps getting beefier.

## To business

Running the the "new" power adapter we'll test the Pi under a controlled load with `sudo apt install stress -y`.

Load all CPUs:
`stress --cpu 4 --timeout 20`

![mysterious_pi_load_cpus](/assets/images/blogs/mysterious_pi_load_cpus.jpg)

Also the temperature went to 86 degrees, from 73-74 (`watch -n 2 "vcgencmd measure_temp"`).

The Pi survived! :tada:

---

# Conclusion

Something as simple as using the wrong power adapter should have been more simple for me to figure out. Especially since the device died at CPU load. Oh, well, this give me a reason to read up upon some electrical engineering 101. :battery: