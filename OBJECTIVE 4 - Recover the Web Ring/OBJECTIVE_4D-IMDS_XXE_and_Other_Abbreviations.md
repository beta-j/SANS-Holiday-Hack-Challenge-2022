# OBJECTIVE 4 - Recover the Web Ring #
## 4D - IMDS, XXE and Other Abbreviations ##

### OBJECTIVE : ###
>The last step in this attack was to use [XXE](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing) to get secret keys from the IMDS service.  What URL did the attacker force the server to fetch?
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4D</summary>
  
>-	AWS uses a specific IP address to access IMDS, and that IP only appears twice in this PCAP.
</details>

#  

## PROCEDURE : ##
Looking through the `victim.pcap` file in Wireshark we can search for the string `secrets` and filter for the conversation with `18.222.86.32` where the secret keys where shared with the attacker.  
We can easily see that the secret keys were shared in response to a HTTP/XML request and by digging into the contents of that request we can see some XML script tricking the server to fetch **`http://169.254.169.254/latest/meta-data/identity-credentials/ec2/security-credentials/ec2-instance`**
