# VPN2ShadowsocksR
![Process](/vpn2ssr.jpg)
## Why
  As we know, there isn't an appropriate SSR client on IOS devices(current SSR clients such as Wingy are lack of protocols, especially the protocol "auth_aes128_md5" which [@breakwa11](https://github.com/breakwa11) recommended. However, VPN with IKEv2 protocol is avliable. Thus an idea came out that we can access SSR server via IKEv2.
## What should you do
  If you want to access SSR on an IOS devices via VPN, you need to:
### First
  You maybe need 2 VPSs[1]. One is used to link to the Internet, and the other is used as a bridge. SSR server should be installed on the former one, and need to turn on a SSR server port(if you don't know how to launch a SSR server, refer this: https://github.com/shadowsocksr/shadowsocksr/tree/manyuser)
  And install follow softwares on the "bridge" VPS:
  * SSR(run as client)
  * Polipo(HTTP Proxy)
  * IPSec(bash "one-key-ikev2.sh". This shell is not written by me, process and illustration can be found in author's blog: https://quericy.me/blog/699)
### And then
  On the "bridge", launch SSR client(the command also can be found in SSR Wiki: https://github.com/breakwa11/shadowsocks-rss/wiki) to access your server. Then be sure that your Polipo's config file have these(maybe in /etc/polipo/conf):
  
`allowedClients = 127.0.0.1, 10.31.0.0/24, 10.31.1.0/24, 10.31.2.0/24`

`socksParentProxy = "127.0.0.1:1080"`

`socksProxyType = socks5`

  then,
  
`sudo service polipo restart`

