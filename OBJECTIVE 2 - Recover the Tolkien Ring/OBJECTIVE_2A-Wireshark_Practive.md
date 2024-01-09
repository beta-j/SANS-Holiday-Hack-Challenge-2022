# OBJECTIVE 2 - Recover the Tolien Ring #
## 2A - Wireshark Practice ##

### OBJECTIVE : ###
>Use the Wireshark Phishing terminal in the Tolkien Ring to solve the mysteries around the [suspicious PCAP](Assets/suspicious.pcap). Get hints for this challenge by typing _hint_ in the upper panel of the terminal.
#  

## HINTS: ##
<details>
  <summary>Hints provided for Objective 2A</summary>
  
>-	[https://unit42.paloaltonetworks.com/using-wireshark-exporting-objects-from-a-pcap/](https://unit42.paloaltonetworks.com/using-wireshark-exporting-objects-from-a-pcap/)
>-	We’re looking for a protocol like FTP, HTTP, SMB, etc.

</details>

#  

## PROCEDURE : ##

>**Q1** There are objects in the PCAP file that can be exported by Wireshark and/or Tshark.  What type of objects can be exported from this PCAP?

**A1:**	Opening the provided `suspicious.pcap` file in WireShark we can filter for `http.request` and we can see three `GET` requests – two for `app.php` (with different lengths) and one for `favicon.ico` these are objects that can be exported and therefore the answer to Q1 is: **HTTP**.

![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/82da83aa-4f59-47c1-875a-14737506cab6)

 
>**Q2:**	What is the file name of the largest file we can export?

**A2:**	By going to *File-> Export Objects-> HTTP* in Wireshark we obtain a list of the exportable files with some more information including their file size. From here we can easily notice that the largest exportable file is `app.php` with a size of **808 kB**.

  ![image](https://github.com/beta-j/SANS-Holiday-Hack-Challenge-2022/assets/60655500/856bb1a2-f55a-4f64-b2e1-5707cf466b76)

>**Q3:**	What packet number starts that `app.php` file?

**A3:**	From the same screen as in Q2 we can see that the packet number for the file in question is **687**.

>**Q4:**	What is the IP of the Apache server?

A4:	This can be quite easily obtained from the filter we applied to answer **Q1**, just by looking at the destination address of the `HTTP GET` request for `app.php` – **192.185.57.242**.

>**Q5:**	What file is saved to the infected host?

**A5:**	This question can be answered by exporting `app.php` and looking at its contents.  In the last couple of lines of the file we can see the following cleartext code: `saveAs(blob1, 'Ref_Sept24-2020.zip');`. So the answer is **`Ref_Sept24-2020.zip`**

>**Q6:**	Attackers used bad TLS certificates in this traffic.  Which countries were they registered to?  Submit the names of the countries in alphabetical order separated by commas (Ex: Norway, South Korea).

**A6:**	By filtering for `tls.handshake.certificate` in Wireshark  we can see a number of certificate exchanges and by looking into the `rdnSequence` of the certificates we can see the country code of the issuing country along with the organisation name and other details.  A number of these certificates appear to have been issued by Microsoft and CyberTrust and appear legit.  But there are also another two suspicious certificates that were issued by “Wemadd Hixchac GmBH” in Israel and another issued by “Hedanpr S.p.a.” in South Sudan.  
So the answer to this question is: **Israel, South Sudan**

>**Q7:**	Is the host infected `(Yes/No)`?

**A7:	Yes**
