VPN
===
Since my university offers me a VPN to connect to their LAN, I wanted to automate
this process as much as possible. Therefore I tweaked the official VPN-config a
little, wrote my own openvpn start/stop wrapper and added all of it to my
/etc/network/interfaces.

Prerequisites
-------------
* Debian (or at least Debian-based) OS
* openvpn installed 
* VPN configured according to [university's guide](https://www.hs-esslingen.de/de/hochschule/service/rechenzentrum/mobile-net-wlan-openvpn/anleitungen-mobile-net/linux-kommandozeile.html)

ToDo
----
If you're interested in my changes, compare your client.conf with my hse.ovpn.
After you're satisfied move your openvpn-config into its own directory to make
sure you can use several VPNs at once.
```
	mkdir /etc/openvpn/hse;
	mv /etc/openvpn/!(hse) /etc/openvpn/hse;
```

You can keep the original client.conf but you won't need it anymore. To reduce
the possibility of conflicts I recommend that you remove it entirely.
```
	rm /etc/openvpn/hse/client.conf
```

You should now create the file up which contains your username and password.
This should be only readable by root since, you know, it contains your username
and password. (It's normally not a good idea to keep things like that lying
around but the whole idea of this is to automate the VPN-stuff)
```
	echo -e 'USER\nPASS' > /etc/openvpn/hse/up;
	chmod 600 /etc/openvpn/hse/up;
```

Link your hse.ovpn into the openvpn-root directory so our VPN script will be able
to find it.
```
	ln -s /etc/openvpn/hse/hse.ovpn /etc/openvpn/hse.ovpn
```

Make sure you change the password everytime you change your university password,
else you won't be able to connect anymore.

Copy the VPN script to /bin and make it executable by root, readable by others.
If you don't want ordinary users to be able to read that file replace 744 by 700.
```
	cp bin/vpn /bin/vpn;
	chmod 744 /bin/vpn;
```

Append the necessary WiFi information to your /etc/network/interfaces
```
	cat etc/network/interfaces >> /etc/network/interfaces;
```

Your WiFi should now connect to the VPN/WEB WiFi at Esslingen University and run
the required VPN tunnel automagically

```
	ifup wlan0=hse
```

You can check this by running your favourite network tests
```
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
