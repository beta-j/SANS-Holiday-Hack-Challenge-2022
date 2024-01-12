# OBJECTIVE 2 - Recover the Tolkien Ring #
## 2C - Suricata Regatta ##

### OBJECTIVE : ###
>Help detect this kind of malicious activity in the future by writing some Suricata rules.  Work with Dusty Giftwrap in the Tolkien Ring to get some hints.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 2C</summary>
  
>-  [This](https://suricata.readthedocs.io/en/suricata-6.0.0/rules/intro.html) is the official source for Suricata rule creation!

</details>

#  

## PROCEDURE : ##

>**Q1** First, please create a Suricata rule to catch DNS lookups for adv.epostoday.uk.  Whenever there's a match, the alert message (msg) should read Known bad DNS lookup, possible Dridex infection.

**A1** 	
```
alert dns $HOME_NET any -> any any (msg:”Known bad DNS lookup, possible Dridex infection”;dns.query;content:”adv.epostoday.uk”;sid:1;)
```


>**Q2**	STINC thanks you for your work with that DNS record! In this PCAP, it points to 192.185.57.242.  Develop a Suricata rule that alerts whenever the infected IP address 192.185.57.242 communicates with internal systems over HTTP.  When there's a match, the message (msg) should read Investigate suspicious connections, possible Dridex infection

**A2**
```
alert http [192.185.57.242] any -> any any (msg:”Investigate suspicious connections, possible Dridex infection”;sid:2;)
alert http any any -> [192.185.57.242] any (msg:”Investigate suspicious connections, possible Dridex infection”;sid:3;)
```

>**Q3**	We heard that some naughty actors are using TLS certificates with a specific CN.  Develop a Suricata rule to match and alert on an SSL certificate for heardbellith.Icanwepeh.nagoya.  When your rule matches, your message should read Investigate bad certificates, possible Dridex infection  

**A3**
```
alert tls any any -> any any (msg:”Investigate bad certificates, possible Dridex infection”; tls.issuerdn:”CN=heardbellith.Icanwepeh.nagoya”; sid:4;)
```

>**Q4**	OK, one more to rule them all and in the darkness find them.  Let's watch for one line from the JavaScript: let byteCharacters = atob Oh, and that string might be GZip compressed - I hope that's OK! Just in case they try this again, please alert on that HTTP data with message Suspicious JavaScript function, possible Dridex infection

**A4**
```
alert http any any -> any any (msg: “Suspicious JavaScript function, possible Dridex infection“; file_data; content:”let byteCharacters = atob”; sid:5;)
```

That’s it – the Suricata rules were effective in getting rid of the mighty Snowrog!


