VPN
===
Since my university offers me a VPN to connect to their LAN, I wanted to automate
this process as much as possible. Therefore I tweaked the official VPN-config a
little, wrote my own OpenVPN start/stop wrapper and added all of it to my
/etc/network/interfaces.

Prerequisites
-------------
* Debian (or at least Debian-based) OS
* OpenVPN installed 
* VPN configured according to [university's guide](https://www.hs-esslingen.de/de/hochschule/service/rechenzentrum/mobile-net-wlan-openvpn/anleitungen-mobile-net/linux-kommandozeile.html)

ToDo
----
If you're interested in my changes, compare your client.conf with my hse.ovpn.
After you're satisfied move your OpenVPN-config into its own directory to make
sure you can use several VPNs at once.
```bash
mkdir /etc/openvpn/hse;
mv /etc/openvpn/!(hse) /etc/openvpn/hse;
```

You can keep the original client.conf but you won't need it anymore. To reduce
the possibility of conflicts I recommend that you remove it entirely.
```bash
rm /etc/openvpn/hse/client.conf;
cp etc/openvpn/hse/hse.ovpn /etc/openvpn/hse/hse.ovpn;
chown 0:0 /etc/openvpn/hse/hse.ovpn;
chmod 644 /etc/openvpn/hse/hse.ovpn;
```

You should now create the file up which contains your username and password.
This should be only readable by root since, you know, it contains your username
and password. (It's normally not a good idea to keep things like that lying
around but the whole idea of this is to automate the VPN-stuff)
```bash
echo -e 'USER\nPASS' > /etc/openvpn/hse/up;
chmod 600 /etc/openvpn/hse/up;
```
Make sure you change the password everytime you change your university password,
else you won't be able to connect anymore.

Link your hse.ovpn into the OpenVPN-root directory so our VPN script will be able
to find it.
```bash
ln -s /etc/openvpn/hse/hse.ovpn /etc/openvpn/hse.ovpn;
```
Create logging ability by adding an openvpn directory to /var/log
```bash
mkdir /var/log/openvpn;
```
Copy the VPN script to /bin and make it executable by root, readable by others.
If you don't want ordinary users to be able to read that file replace 744 by 700.
```bash
cp bin/vpn /bin/vpn;
chmod 744 /bin/vpn;
```
Make sure, your /etc/network/interfaces sources everything from /etc/network/interfaces.d/*
and copy the interface declaration there.
```bash
cp etc/network/interfaces.d/hse /etc/network/interfaces.d/;
```

Your WiFi should now connect to the VPN/WEB WiFi at Esslingen University and run
the required VPN tunnel automagically

```bash
ifup wlan0=hse;
```

You can check this by running your favourite network tests
```bash
ip a show tun0;
ip r show;
traceroute heise.de;
```

Disclaimer
----------
I created this guide to my best knowledge and abilities, if something goes wrong
don't blame me.

I'm grateful for feedback and improvement ideas, so if you find anything lacking
please tell me or throw me a pull request.
