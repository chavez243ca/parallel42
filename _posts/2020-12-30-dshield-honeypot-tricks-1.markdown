---
layout: post
title:  "Dshield Honeypot Tricks 1"
date:   2020-12-30					
categories: hacks blueteam infosec
tags: hacks blueteam infosec raspberry-pi linux
---
ISC SANS has a fantastic little [honeypot][dshield-honeypot] you can throw on a [Raspberry Pi][rasp-pi]. While you can interact with the collected Intel to some degree from the various dashboards on the SANS site, I wanted a bit more detail and immediate access to the logs.

[Cowrie][cowrie-url] json logging is a fantastic feature, providing a rich source of information you can put to various uses.

Below I will show you how I parse the logs and enrich them with geoip to get a better feel for who is attacking and from where.

{% highlight shell %}
cat cowrie.json* | jq -r .src_ip | sort | uniq -c | sort -nr | head -n 100 | xargs -n 1 -I % sh -c 'echo %; geoiplookup %'

#=> parses all cowrie (ssh/telnet) logs pulls out the source ip address and runs those through geoip
{% endhighlight %}

This is performed from the cowrie logs directory which might not be in the most obvious of places: 

{% highlight shell %}
cd /srv/cowrie/var/log/cowrie
{% endhighlight %}

If you have not installed GeoIP, you will need to do so for this all to work as expected:

{% highlight shell %}
sudo apt install geoip-bin
{% endhighlight %}

Sample Output


>435 106.124.137.130 (time seen in logs | ip address)  
>GeoIP Country Edition: CN, China (Country)  
>GeoIP City Edition, Rev 1: CN, 30, Guangdong, Guangzhou, N/A, 23.116699, 113.250000, 0, 0  
>GeoIP ASNum Edition: AS4134 Chinanet (ASN)    




[dshield-honeypot]: https://isc.sans.edu/honeypot.html
[geo-ip]:   https://dev.maxmind.com/geoip/geoip2/geolite2/
[rasp-pi]: https://www.raspberrypi.org/
[cowrie-url]: https://github.com/cowrie/cowrie