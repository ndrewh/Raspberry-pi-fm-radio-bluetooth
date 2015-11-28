Raspberry Pi FM Radio Transmitter
============

Create your own rapsberry pi FM radio radio station (short range)


1. Install [Arch Linux for ARM](http://archlinuxarm.org/platforms/armv6/raspberry-pi)

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
6. Connect your device with bluetoothctl. I had to run `hciconfig hci0 up` before this worked. I also had to say `agent on` in bluetoothctl
7. Install `10-local.rules` at `/etc/udev/rules.d/` to run hciconfig at startup
7. Clone and build https://github.com/ChristopheJacquet/PiFmRds in `/home/alarm/`. Path to executable should be `/home/alarm/PiFmRds/src/pi_fm_rds`
8. Install `pulseaudio.service` in `/etc/systemd/system/` and run 

  ```
  systemd reload-daemon
  systemd enable pulseaudio
  ```
  
9. `Update /usr/share/dbus-1/system.conf` using the included `system.conf`. The modified line is denoted with a comment.
10. Install `radio.service` in `/etc/systemd/system/` and `radioStart.sh` in `/home/alarm/` -- No need to run systemctl enable because we don't want it to start at startup
11. Install `99-input.rules` in `/etc/udev/rules.d/`, install "bluetooth" script in /usr/lib/udev/
