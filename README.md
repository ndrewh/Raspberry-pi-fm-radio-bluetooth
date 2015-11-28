Raspberry Pi FM Radio Transmitter
============

Create your own rapsberry pi FM radio radio station (short range) streaming from your bluetooth device (ex. iPhone)

Materials
------

- 70cm (optimally, ~20cm will do) or so plain wire connected to GPIO 4
- Bluetooth dongle (doesn't have to be BTLE, just needs to support audio streaming)

Setup
-------
1. Install [Arch Linux for ARM](http://archlinuxarm.org/platforms/armv6/raspberry-pi).

2. Install pulseaudio, pulseaudio-bluetooth, and sox
  ```
  pacman -S pulseaudio pulseaudio-bluetooth sox
  ```
3. Add the necessary users and groups as explained on the [wiki](http://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/SystemWide/). Note that addgroup and adduser are not the exact same commands in arch linux.
4. You may need to start the bluetooth service manually 
  ```
  systemctl start bluetooth
  systemctl enable bluetooth
  ```
  
5. Edit the `/etc/dbus-1/system.d/bluetooth.conf` file to allow the correct group ("bluetooth"). See included file.
6. Connect your device with bluetoothctl. (See its `help` command if confused). I had to run `hciconfig hci0 up` before this worked. I also had to say `agent on` in bluetoothctl
7. Install `10-local.rules` at `/etc/udev/rules.d/` to run hciconfig at startup
7. Clone and build https://github.com/ChristopheJacquet/PiFmRds in `/home/alarm/`. Path to executable should be `/home/alarm/PiFmRds/src/pi_fm_rds`
8. Install `pulseaudio.service` in `/etc/systemd/system/` and run 

  ```
  systemd reload-daemon
  systemd enable pulseaudio
  ```
  
9. `Update /usr/share/dbus-1/system.conf` using the included `system.conf`. The modified line is denoted with a comment.
10. Install `radio.service` in `/etc/systemd/system/` and `radioStart.sh` in `/home/alarm/` -- No need to run systemctl enable because we don't want it to start at startup. **Modify radioStart.sh with your device's MAC address.**
11. Install `99-input.rules` in `/etc/udev/rules.d/`, install "bluetooth" script in /usr/lib/udev/


Usage
-----

Connect your device via bluetooth. On first attempt, it will error out, but on the second try, it will work. (This could be fixed if I somehow managed to load the `bluetooth-discover` pulseaudio module before connecting but I haven't figured out how to do that). You know its streaming if the speakers on your phone go silent.

By default, this transmits on 89.9. This can be adjusted in radioStart.sh.

Troubleshooting
----

Open an issue and I'll see if maybe I left something out. Also see `journalctl` which acesses most of the logs for pulseaudio, bluez, etc. There is some other stuff logged to `/var/log/bluetooth_dev` and `/var/log/bluetooth_err` by the 'bluetooth' script but thats probably not helpful.

To-do
----

- [ ] Don't require pairing manually at first
- [ ] Auto-connect
- [ ] Don't hard code MAC in radioStart.sh
- [ ] Don't use pulseaudio "system" mode since its use is discouraged
