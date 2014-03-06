UPDATE!
Due to the problems people has been having with the error “ERROR: could not insert ’8188eu’: Exec format error.” I’ve compiled and tested the driver for both 2013-02-09-wheezy-raspbian and 2013-09-25-wheezy-raspbian for kernel 3.6.11+.

2013-02-09-wheezy-raspbian kernel 3.6.11+:
http://tech.enekochan.com/wp-content/uploads/2013/rtl8188eu/liwei/2013-02-09-wheezy-raspbian-3-6-11/8188eu.ko
2013-09-25-wheezy-raspbian kernel 3.6.11+:
http://tech.enekochan.com/wp-content/uploads/2013/rtl8188eu/liwei/2013-09-25-wheezy-raspbian-3-6-11/8188eu.ko

wget http://tech.enekochan.com/wp-content/uploads/2013/rtl8188eu/liwei/2013-09-25-wheezy-raspbian-3-6-11/8188eu.ko
sudo install -p -m 644 8188eu.ko /lib/modules/`uname -r`/kernel/drivers/net/wireless
sudo depmod -a
sudo modprobe 8188eu

You’ll probably want your Raspberry PI to connect to the wifi on boot. In that case edit /etc/network/interfaces to configure the SSID and the password of your wifi network:

auto lo

iface lo inet loopback
iface eth0 inet dhcp

iface eth0 inet static
    address 192.168.1.2
    netmask 255.255.255.0
    gateway 192.168.1.1
     

Test it by turning down and up the connection and verifying with ifconfig that wlan0 now has an IP:

sudo ifdown wlan0
sudo ifup wlan0
ifconfig

If you see this messages don’t worry as far as everything works OK (I don’t know why those happend but the connection works anyway):

ioctl[SIOCSIWAP]: Operation not permitted
ioctl[SIOCSIWENCODEEXT]: Invalid argument
ioctl[SIOCSIWENCODEEXT]: Invalid argument

Previously I had been using the Wifi Config (wpa_gui) application to configure my wifi network settings under the desktop environment. This application automatically configures /etc/network/interfaces and /etc/wpa_supplicant/wpa_supplicant.conf files:

auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
       ssid="XXXX"
       psk="XXXXXXXXXXXX"
       proto=RSN
       key_mgmt=WPA-PSK
       pairwise=CCMP
       auth_alg=OPEN
}

But it stoped working with the lastest version of the driver.

If you use this method and want to force wpa_supplicant to reconnect to the wifi just run this:

sudo wpa_supplicant -Dwext -iwlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf

I also had to configure the DNS servers to be able to resolve names from the internet. This is done by editing the /etc/resolv.conf file. Just add your DNS servers (usually your ISP gives you those IPs) or use 8.8.4.4 and 8.8.8.8 (DNS servers from Google):

nameserver 8.8.4.4
nameserver 8.8.8.8

Source: http://blog.pi3g.com/2013/05/tp-link-tl-wn725n-nano-wifi-adapter-v2-0-raspberry-pi-driver/
https://www.zhujunsan.net/index.php/2013/03/make-tp-link-tl-wn725n-v2-work-on-raspbian/
http://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/setting-up-wifi-with-occidentalis