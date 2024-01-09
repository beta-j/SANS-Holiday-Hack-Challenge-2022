# OBJECTIVE 4 - Recover the Web Ring #
## 4A - Naughty IP ##

### OBJECTIVE : ###
>Use [the artifacts](Assets/boriaArtifacts.zip) from Alabaster Snowball to analyze this attack on the Boria mines. Most of the traffic to this site is nice, but one IP address is being naughty! Which is it? Visit Sparkle Redberry in the Tolkien Ring for hints.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 4A</summary>
  
>-  The victim web server is `10.12.42.16`.  Which host is the next top talker?
</details>

#  

## PROCEDURE : ##

Alabaster snowball has given us two files – a `victim.pcap` file and a `weberror.log` file.  A quick look at the log file provides us with time-stamped logs for HTTP requests with the corresponding HTTP response code. We can see a very large number of login attempts (hallmark of a brute-force attack) followed by a large number of 404 error codes in response to `HTTP GET` requests for non-existing directories – which indicates that the attacker was running some kind of forced browsing attack to enumerate directories on the website.

Since all the requests are coming from the same IP address – **`18.222.86.32`** we can be confident that this is the naughty IP address we Are looking for.

