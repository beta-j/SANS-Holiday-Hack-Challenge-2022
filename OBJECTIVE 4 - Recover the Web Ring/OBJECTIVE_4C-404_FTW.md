# OBJECTIVE 4 - Recover the Web Ring #
## 4C - 404 FTW ##

### OBJECTIVE : ###
>The next attack is [forced browsing](https://owasp.org/www-community/attacks/Forced_browsing) where the naughty one is guessing URLs. What's the first successful URL path in this attack?
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4C</summary>
  
>-	With forced browsing, there will be many 404 status codes returned from the web server. Look for 200 codes in that group of 404s. This one can be completed with the PCAP or the log file.
</details>

#  

## PROCEDURE : ##
To find the first successfully enumerated URL path we can look into the `weberror.log` file.  
On the 5th October 2022, starting at 16:47:45 we see a large number of 404 error codes which indicate the start of the forced browsing attack.  Scanning through the list of attempts we can notice a HTTP response of 200 for the directory **`/proc`** at `[05/OCT/2022 16:47:46]`.
