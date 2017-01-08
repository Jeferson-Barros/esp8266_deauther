# esp8266_deauther
Build your own WiFi jammer for under $10 with an ESP8266.

# Introduction

## What it is:

Basically it’s a device which performs a [deauth attack](https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack).
You select the clients you want to disconnect from their network and start the attack. As long as the attack is running, the
selected devices are not able to connect to the network.

## How it works:

The 802.11 WiFi protocol contains a so called [deauthentication frame](https://mrncciew.com/2014/10/11/802-11-mgmt-deauth-disassociation-frames/). It is used to disconnect clients safely from a wireless
network.

For example your smartphone will send such a frame to its connected network when you turn the WiFi off. The router then knows that
you are no longer a part of the network and stops sending you packets.

When you just get out of the range of your WiFi network, the router wouldn’t notice it and would send you data and wait for your
answer. After some time you get a timeout and the router kicks you off the network anyway. But for the sake of resources and to
prevent errors, these packets were invented.

Because they are unencrypted, you just need the mac address of the WiFi router and of the client device which you want to
disconnect from the network. You don’t even have to be in the network or know the password, it’s enough to be in its range.

## What an ESP8266 is:

The [ESP8266](https://de.wikipedia.org/wiki/ESP8266) a very cheap micro controller with build in WiFi. It contains a powerfull 160 MHz processor and you can program it
with the [Arduino IDE](https://www.arduino.cc/en/Main/Software). This makes it perfect for this project.

You can buy these chips for under $2 from China!

## How to protect yourself:

With [802.11w-2009](https://en.wikipedia.org/wiki/IEEE_802.11w-2009) WiFi got an update to encrypt management frames.
So make sure your router is up to date and has management frame protection enabled. But be sure that your client device
supports it too, both ends need to have it enabled!

The only problem is that most devices don’t use it. I tested it with different WiFi networks and client devices now 
and it worked every time. It seems that even newer devices which support frame protection, don’t use it by default.

# Disclaimer

Use it only for testing purposes on your own devices!

Please check the legal regulations in your country before using it. Jamming transmitters are illegal in most countries 
and this device can fall into the same category (even if it’s technically not the same).

My intention with this project is to draw attention to this issue. 
This attack shows how vulnerable the 802.11 WiFi standard is and that it has to be fixed.
**A solution is already there, why don’t we use it?**

# Tutorial

The only thing you will need is a computer and an ESP8266.

I recommend you to buy one of the USB breakout/developer boards.
It doesn’t matter which board you use, as long as it has an ESP8266 on it.

**1** Install [Arduino](https://www.arduino.cc/en/Main/Software) and open it.

**2** Go to `File` > `Preferences`

**3** Add `http://arduino.esp8266.com/stable/package_esp8266com_index.json` to the Additional Boards Manager URLs. (source: https://github.com/esp8266/Arduino)

**4** Go to `Tools` > `Board` > `Boards Manager`

**5** Type in `esp8266`

**6** Select version `2.0.0` and click on `Install`

**7** Go to `File` > `Preferences`again

**8** Open the folder path under `More preferences can be edited directly in the file`

**9** Go to `packages` > `esp8266` > `hardware` > `esp8266` > `2.0.0` > `tools` > `sdk` > `include`

**10** Open `user_interface.h` with a text editor

**11** Scroll down and before `#endif` add following lines:

`typedef void (*freedom_outside_cb_t)(uint8 status);`  
`int wifi_register_send_pkt_freedom_cb(freedom_outside_cb_t cb);`  
`void wifi_unregister_send_pkt_freedom_cb(void);`  
`int wifi_send_pkt_freedom(uint8 *buf, int len, bool sys_seq);`  

**12** Download and open `esp8266_deauther` > `esp8266_deauther.ino` with Arduino

**13** Select your ESP8266 board at `Tools` > `Board` and the right port at `Tools` > `Port`  
If no port shows up you may have to reinstall the drivers.

**14** Upload!

**Your ESP8266 Deauther is now ready!**

Scan for WiFi networks and connect to `pwned`. The password is `deauther`.  
Once connected, you can open up your browser and go to `192.168.4.1`.

Happy hacking :)


# Sources and additional links

deauth attack: https://en.wikipedia.org/wiki/Wi-Fi_deauthentication_attack

deauth frame: https://mrncciew.com/2014/10/11/802-11-mgmt-deauth-disassociation-frames/

ESP8266: 
* https://de.wikipedia.org/wiki/ESP8266
* https://espressif.com/en/products/hardware/esp8266ex/overview

packet injection with ESP8266: 
* http://hackaday.com/2016/01/14/inject-packets-with-an-esp8266/
* http://bbs.espressif.com/viewtopic.php?f=7&t=1357&p=10205&hilit=wifi_pkt_freedom#p10205
* https://github.com/pulkin/esp8266-injection-example

802.11w-2009: https://en.wikipedia.org/wiki/IEEE_802.11w-2009

wifi_send_pkt_freedom function limitations: http://esp32.com/viewtopic.php?f=13&t=586&p=2648&hilit=wifi_send_pkt_freedom#p2648

esp32 esp_wifi_internal function limitations: http://esp32.com/viewtopic.php?f=13&t=586&p=2648&hilit=wifi_send_pkt_freedom#p2648