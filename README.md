# mavproxysetup

1. `sudo apt-get update`
2. `sudo apt-get upgrade`
3. https://github.com/aircrack-ng/rtl8812au
4. https://ardupilot.github.io/MAVProxy/html/getting_started/download_and_installation.html
5. Ensure `screen` is installed: `sudo apt-get install screen`
6. Place a script in your home directory (eg `~pi/mavgateway.sh`) to start mavproxy:
```
#! /bin/bash
# optional: provides RTCM injection if ublox m8p is connected to usb
# socat UDP-DATAGRAM:127.0.0.1:13320 file:/dev/ttyACM0
mavproxy.py --master=/dev/serial/by-id/usb-3D_Robotics_PX4_FMU_v2.x_0-if00 --out=udp:192.168.1.xxx:14550 --source-system=199
#mavproxy.py --master=/dev/serial/by-id/usb-3D_Robotics_PX4_FMU_v2.x_0-if00 --out=udpbcast:192.168.1.255:14550 --source-system=199
```
7. Make the script executable:
```
sudo chmod +x mavgateway.sh
```
8. Create a systemd init file in `/etc/init.d/mavrelay` :
```
#! /bin/bash
### BEGIN INIT INFO
# Provides: mavgateway
# Required-Start:    $all
# Required-Stop:
# Default-Start:     2 3 4 5
# Default-Stop:
# Short-Description: Start mavproxy relay at boot
### END INIT INFO

su - pi -c "cd ~pi; screen -L -dmS mavbarn ~pi/mavgateway.sh"
```
9. Edit the permissions for mavgateway and update the system to include the mavgateway daemon upon startup:
```
sudo chmod +x /etc/init.d/mavgateway
sudo chown root:root /etc/init.d/mavgateway
```
10. Enable the service:
```
sudo update-rc.d mavgateway defaults
```
- You can stop the service by `sudo service mavgateway stop` or start it with `sudo service mavgateway start`. On next boot it should start automatically
