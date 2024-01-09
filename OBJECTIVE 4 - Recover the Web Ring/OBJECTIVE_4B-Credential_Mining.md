# OBJECTIVE 4 - Recover the Web Ring #
## 4B - Credential Mining ##

### OBJECTIVE : ###
>The first attack is a [brute force](https://owasp.org/www-community/attacks/Brute_force_attack) login. What's the first username tried?
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4B</summary>
  
>-	The site's login function is at `/login.html`. Maybe start by [searching](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkFindPacketSection.html) for a string.
</details>

#  

## PROCEDURE : ##
From the `weberror.log` file we can see that the attacker successfully enumerated `/login.html` and the proceeded to attempt multiple username and password combinations.  We are now tasked with finding the first username used in this brute-force attack.

To find this we can load the `victim.pcap` file into Wireshark and filter for the `HTTP POST` requests made from the malicious IP address: 
```
ip.addr==18.222.86.32 and http.request.method==POST
```
The first result in the list tells us that the first login attempt made from 18.222.86.32 used the username **`alice`** with the password `Philip`.
