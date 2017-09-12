# VPN2ShadowsocksR
![Process](/vpn2ssr.jpg)
latest update: 2017.04.23
## Why
  As we know, there isn't an appropriate SSR client on IOS devices~~(current SSR clients such as Wingy are lack of protocols, especially the protocol "auth_aes128_md5" which [@breakwa11](https://github.com/breakwa11) recommended)~~(and now all of those clients are unavailable in some areas). However, VPN with IKEv2 protocol is avliable. Thus an idea came out that we can access SSR server via IKEv2.
## What should you do
  If you want to access SSR on an IOS devices via VPN, you need to:
### First
  You maybe need 2 VPSs[1]. One is used to link to the Internet, and the other is used as a bridge. SSR server should be installed on the former one, and need to turn on a SSR server port(if you don't know how to launch a SSR server, refer this: https://github.com/shadowsocksr/shadowsocksr/tree/manyuser)
  And install follow softwares on the "bridge" VPS:
  * SSR(run as client)
  * Polipo(HTTP Proxy)
  * IPSec(bash "one-key-ikev2.sh". This shell is not written by me, process and illustration can be found in author's blog: https://quericy.me/blog/699)

[1] Use just 1 VPS is OK while it would raise the risk of being detected.
###  Config of Polipo
  On the "bridge", launch SSR client(the command also can be found in SSR Wiki: https://github.com/breakwa11/shadowsocks-rss/wiki) to access your server. Then be sure that your Polipo's config file have these(maybe in /etc/polipo/conf):
  
`allowedClients = 127.0.0.1, 10.31.0.0/24, 10.31.1.0/24, 10.31.2.0/24`

`socksParentProxy = "127.0.0.1:1080"`

`socksProxyType = socks5`

  then,
  
`sudo service polipo restart`

###  Config of IPSec
  If you install IPSec via "one-key-ikev2.sh", DON'T EDIT ITS CONFIG FILES. The IOS device must install "ca.cert.pem" in path ~/my_key. The process is: make a download link of your "ca.cert.pem" by means of email, cloud service, HTTP server, etc. On IOS device, click the link and a message box would guide you to finish the installation.
  Edit your accounts. The config file is in /usr/local/etc/ipsec.secrets. Here is an example:

`: RSA server.pems`

`: PSK "whateverYouLike"`

`: XAUTH "whateverYouLike2"`

`user1 : EAP "psw1"`

`user2 : EAP "psw2"`

After that, launch the IPSec server:

`sudo ipsec restart`

Then:
  
`sudo ipsec statusall`

You would see lots of items. We just need this:

  `Listening IP addresses:`
  
  `<IP1>`
  
  `<IP2>`

  One of these 2 IPs is your VPS's IP in public network, while we need another :)

###  Config on IOS devices
  Add an IKEv2 VPN on your iPhone or iPad. Pay attention to these sections:
  * Remote ID: same as your server IP address.
  * User and password: one of your account in /usr/local/etc/ipsec.secrets you've set before.
  * Proxy: Server=the IP you record before(not the public IP), Port=8123(same as you've set in Polipo config file)
Then, enjoy the safe, free and high speed network :)

### Possible problem
* If you cannot access your VPN, check the iptables, be sure that it's same as what in "one-key-ikev2.sh".
* In IOS9, once you add a new IKEv2 VPN, the username is unchangeable(even though the box can be edited, that's useless). Thus when you need to change the username, you have to add a new IKEv2 VPN. I don't know the situation of IOS10 since I've not updated.
