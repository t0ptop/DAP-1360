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
`UPGCGI_CreateDir`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/1.png)

`UPGCGI_Delete`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/2.png)

`UPGCGI_ModifyMac`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/3.png)

`Burpsuite intercept`
![image](https://github.com/tgp-top/DAP-1360/blob/2d09c23efafd8d3199b0a84d5b159f3cdbd26638/%E5%9B%BE%E7%89%87/4.png)

## POC
[POC](https://github.com/tgp-top/DAP-1360/blob/b49ab592d240de04397e9c3389f328cf6a27b6be/dap-1360.py)
  
  
  
