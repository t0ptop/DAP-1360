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
![image](https://github.com/IBUILI/vulnerability/blob/main/images/1.png)
