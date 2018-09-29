# captive-portal
This repo is a captive portal page designed for use with OpenWRT and NoDogSplash.

## Install Requirements

- A router that supports OpenWRT/LEDE. I have a [GL-AR750](https://www.amazon.com/GL-iNet-GL-AR750-300Mbps-pre-installed-Included/dp/B07712LKJM). These instructions are written specifically for GL.iNet routers running firmware ≥ 3.003.

- Dropbear enabled on your router so you can SSH in.

- LuCI installed.

- An internet connection NOT via the router you are testing this on.

## Installation

- Connect to your router's network.

- Set up a Guest WLAN and secure it:
  1. Go to Network > Wireless and click "Add" next to the radio you want your Guest Network to broadcast on (2.4G or 5G, does not matter). Edit it, and set the SSID to whatever you would like. Make sure the mode is set to *Access Point*, and create a new Network called `guest`. Everything else can be changed (security etc) without problem. Save and apply.

  2. Go to Network > Interfaces and Edit the `guest` network you just created. Set an IPv4 (and IPv6 if needed) subnet, leave gateway and broadcast blank. Make sure to set up a DHCP server using the button. Save and apply.

  3. Go to the Firewall Settings tab on the top and create a new firewall zone called `guest`. Save and apply.

  4. Go to Network > Firewall. Set Input and Forward to `reject` and set Output to `accept` on the new `guest` firewall zone. Save and apply.

  5. Click Edit next to the `guest` firewall zone. We will be blocking ports / addresses through NoDogSplash, but the LuCI firewall will be used to NAT the guest network to the WAN. Scroll down to Inter-Zone Forwarding and set the destination zone to the WAN. Save and apply.

  *Source*: https://wiki.openwrt.org/doc/recipes/guest-wlan-webinterface

- Set up throttling for the Guest WLAN (optional):
  1. SSH into your router. If you are getting an error make sure you've added a public key to dropbear (or enable password auth for root).

  2. Install `luci-app-sqm`: `opkg update && opkg install luci-app-sqm`

  3. Open LuCI in your browser and go to Network > SQM QoS. Set the Interface name to the guest interface and set download and upload speed as you please. Note that upload / download speeds in LuCI are reversed (so download speed in LuCI is actually upload speed). Save and apply.

- Setup NoDogSplash:
  1. SSH into your router.

  2. Install `nodogsplash`: `opkg update && opkg install nodogsplash`

  3. Copy my configuration file (nodogsplash.conf in this repo) to `/etc/config/nodogsplash`. Alternatively, use the source link below to write your own configuration. My configuration blocks access to the secured LAN (which is 172.16.0.1/16 for me, but by default, is 192.168.8.1/24). It also allows authenticated users to access ports 53, 80, 443, 993, 995, 465, 110, 143. Trusted MACs do not bypass these rules, unlike with the default configuration.

  4. Copy my splash.html and infoskel.html to `/etc/nodogsplash/htdocs/`. Alternatively, write your own using the source link below.

  *Source*: https://openwrt.org/docs/guide-user/services/captive-portal/wireless.hotspot.nodogsplash

- Make connections easier (optional):

  1. If you have writable NFC tags and an Android device, install an app like [NFC Tools](https://play.google.com/store/apps/details?id=com.wakdev.wdnfc&hl=en_US) and write your Wifi network to the NFC tag. This way, users with Android phones and the NFC Tasks app can instantly connect to your guest wifi by just tapping their phone on the tag.

## Additional Resources

[NoDogSplash Docs](http://nodogsplash.rtfd.io)

OpenWRT runs NoDogSplash V1.
